**22 Avril**
- [ ] KP-943 optimiser les services backend
    - [x] KP-944 Optimiser le service backend events
        - [x] Refonte de la méthode returnLastEvents(), passage de :
        ```
        async returnLastEvents(kmoBoxMac: string): Promise<Event[]> {
            const event: Event[] = [];
            for (const type in TypeEvent) {
            event.push(
                await Event.findOne({
                relations: {
                    kmoBox: true,
                },
                select: {
                    kmoBox: {
                    mac: true,
                    },
                },
                where: {
                    type: TypeEvent[type],
                    kmoBox: {
                    mac: kmoBoxMac,
                    },
                },
                order: {
                    datetime: 'DESC',
                },
                }),
            );
            }
            return event;
        }
        ```
        à : 
        ```
        async returnLastEvents(kmoBoxMac: string): Promise<Event[]> {
        const eventPromises = [];
        for (const type in TypeEvent) {
            eventPromises.push(
            Event.findOne({
                relations: {
                kmoBox: true,
                },
                select: {
                kmoBox: {
                    mac: true,
                },
                },
                where: {
                type: TypeEvent[type],
                kmoBox: {
                    mac: kmoBoxMac,
                },
                },
                order: {
                datetime: 'DESC',
                },
            }),
            );
        }
        return Promise.all(eventPromises);
        }
        ```
        ce qui change : 
        Utilisation de Promise.all pour fetch tous les events en parallèle sans utiliser de boucle, ça améliore les perfs globales de la méthode en général et plus spécialement s'il y a beaucoup de types d'events
        - [x] Refonte de la méthode processMotorEvents (), passage de :
        ```
        async processMotorEvents() {
            try {
            const currentDatetime = new Date();
            const twoMinutesAgo = new Date(currentDatetime.getTime() - 2 * 60 * 1000);

            const motorEvents = await this.eventRepository.find({
                where: {
                type: TypeEvent.MOTOR,
                datetime: MoreThanOrEqual(twoMinutesAgo.toISOString()),
                },
                relations: ['kmoBox'],
            });

            for (const motorEvent of motorEvents) {
                const kmoBoxMac = motorEvent.kmoBox.mac;
                const kmoBox = await this.kmoBoxRepository.findOne({
                where: { mac: kmoBoxMac },
                });

                if (kmoBox) {
                const existingMotorUsage = kmoBox.motorUsage || 0;
                const newMotorUsage = Math.floor(
                    existingMotorUsage + (motorEvent.duration || 0),
                );

                kmoBox.motorUsage = newMotorUsage;
                await this.kmoBoxRepository.save(kmoBox);
                }
            }
            } catch (error) {
            throw error;
            }
        }
        ```
        à : 
        ```
        async processMotorEvents() {
            try {
                const currentDatetime = new Date();
                const twoMinutesAgo = new Date(currentDatetime.getTime() - 2 * 60 * 1000);

                const motorEvents = await this.eventRepository.find({
                where: {
                    type: TypeEvent.MOTOR,
                    datetime: MoreThanOrEqual(twoMinutesAgo.toISOString()),
                },
                relations: ['kmoBox'],
                });

                const kmoBoxMacs = motorEvents.map((motorEvent) => motorEvent.kmoBox.mac);
                const kmoBoxes = await this.kmoBoxRepository.find({
                where: { mac: In(kmoBoxMacs) },
                });

                const kmoBoxMacToKmoBoxMap = new Map(kmoBoxes.map((kmoBox) => [kmoBox.mac, kmoBox]));

                const updatePromises = motorEvents.map(async (motorEvent) => {
                const kmoBoxMac = motorEvent.kmoBox.mac;
                const kmoBox = kmoBoxMacToKmoBoxMap.get(kmoBoxMac);

                if (kmoBox) {
                    const existingMotorUsage = kmoBox.motorUsage || 0;
                    const newMotorUsage = Math.floor(existingMotorUsage + (motorEvent.duration || 0));

                    await this.kmoBoxRepository.increment({ mac: kmoBoxMac }, 'motorUsage', newMotorUsage - existingMotorUsage);
                }
                });

                await Promise.all(updatePromises);
            } catch (error) {
                throw error;
            }
        }
        ```
        ce qui change : 
        Utilisation de Promise.all pour fetch tous les events en parallèle sans utiliser de boucle, ajout de findOneOrFail pour gérer les fails le cas échéant, et utilisation d'inrement() pour update les lignes de la db directement au lieu de charge la table, la modifier et la save. 
        - [x] Refonte de la méthode setGWState(), passage de :
        ```
        @Interval(86400000)
        async setGWState() {
            try {
            const uniqueKmoBoxMacs = await this.getUniqueKmoBoxMacsLastThreeDays();

            for (const kmoBoxMac of uniqueKmoBoxMacs) {
                const gatewayMacs = await this.getGatewayMacsForKmoBox(kmoBoxMac);

                for (const gatewayMac of gatewayMacs) {
                await this.gatewayService.setGatewayStatus(gatewayMac, false);
                }

                const allGateways = await this.gatewayService.getAllGateway();

                for (const gateway of allGateways) {
                if (!gatewayMacs.includes(gateway.mac)) {
                    await this.gatewayService.setGatewayStatus(gateway.mac, true);
                }
                }
            }
            } catch (error) {
            console.error('Error in GW state job:', error);
            }
        }

        async updateClosedTime(kmoBoxMac: string, eventDatetime: string) {
            try {
            const lastClosedDatetime = await this.getPreviousClosedEventDatetime(
                kmoBoxMac,
                eventDatetime,
            );

            if (lastClosedDatetime) {
                const timeDiff = Math.floor(
                (new Date(eventDatetime).getTime() -
                    new Date(lastClosedDatetime).getTime()) /
                    1000,
                );

                if (timeDiff > 0) {
                await this.kmoBoxesService.updateClosedTime(kmoBoxMac, timeDiff);
                }
            }
            } catch (error) {
            console.error('Error updating closedTime:', error);
            }
        }
        ```
        à : 
        ```
        async setGWState() {
            try {
            const uniqueKmoBoxMacs = await this.getUniqueKmoBoxMacsLastThreeDays();
        
            const allKmoBoxMacs = uniqueKmoBoxMacs.join(',');
            const gatewayMacs = await this.eventRepository
                .createQueryBuilder('event')
                .distinct(true)
                .select('gateway.mac', 'gatewayMac')
                .innerJoin('event.kmoBox', 'kmoBox')
                .innerJoin('kmoBox.gateway', 'gateway')
                .where('kmoBox.mac IN (:...kmoBoxMacs)', { kmoBoxMacs: allKmoBoxMacs.split(',') })
                .getRawMany();
        
            const gatewayMacSet = new Set(gatewayMacs.map((result) => result.gatewayMac));
        
            const updatePromises = gatewayMacs.map((gatewayMac) => {
                return this.gatewayService.setGatewayStatus(gatewayMac, false);
            });
        
            await Promise.all(updatePromises);
        
            const allGateways = await this.gatewayService.getAllGateway();
            const allGatewayMacs = new Set(allGateways.map((gateway) => gateway.mac));
        
            const gatewaysToUpdate = Array.from(allGatewayMacs).filter((mac) => !gatewayMacSet.has(mac));
        
            const updatePromises2 = gatewaysToUpdate.map((gatewayMac) => {
                return this.gatewayService.setGatewayStatus(gatewayMac, true);
            });
        
            await Promise.all(updatePromises2);
            } catch (error) {
            console.error('Error in GW state job:', error);
            }
        }
        ```
        ce qui change : 
        Le fetch se fait maintenant sur chaque kmoBox unique au lieu de chercher chaque kmoBox mac, fetch les gateways seulement une fois et stockage dans un Set pour plus d'efficacité. Et utilisation de Promise.all pour fetch tous les events en parallèle sans utiliser de boucle
        - [ ] Rectification des unit tests foireux
- [x] Réunion de mise à jour des objectifs de prédict, partie 1
- [x] Réunion avec Anthony & discussion sur Okta

**24 Avril**
- [ ] KP-943 optimiser les services backend
    - [x] Mise en pause et passage sur : 
- [x] KP-960 Changer le libellé de "Caisses hors-service" en "Boitiers Kmo hors-service"
    - [x] ```git checkout develop``` && ```git pull origin develop``` puis ```git checkout -b fix/KP-950-960_various_copdialog_interface_fixes```
    - [x] Remplacement de Caisse Hors-service par Boîtiers KMO Hors-service dans les fichiers : 
        - cop store details html
        - broken kmo modal html
- [ ] KP-950 Fix le bug qui a désaffiché l'état de caisse dans la modale
    - [x] Baser this.kmoCurrentState sur this.kmoBox.isOpened : 
    ```
    forkJoin([
    this.kmoBoxesService.getKmoBoxById(this.data.mac) as Observable<KmoBoxInterface>,
    ...
    ]).subscribe({
    next: ([
      kmoBox,
    ]) => {
        this.kmoBox = kmoBox;
      switch (kmoBox.isOpened) {
        case 'open':
          this.kmoCurrentState = 'ouvert';
          break;
        case 'closed':
          this.kmoCurrentState = 'fermé';
          break;
        case 'power_off':
          this.kmoCurrentState = 'caisse éteinte';
          break;
        default:
          this.kmoCurrentState = 'pas de données sur cette caisse';
      };
    }
    })
    ...
    ```
    - [x] lint, test, commit & push
- [x] Documentation sur l'optimisation :
    - https://www.brilworks.com/blog/optimize-your-nest-js-app-performance-with-these-practices/
    - https://www.bymoji.com/blog/Scaling_NestJS_APIs_Tips_for_Large_Data_Sets_and_High_Traffic
- [x] Réunion jira longue sa mère
- [ ] Se renseigner sur le temps que prendrait la mise en place de tests d'intégration, à savoir
    - rédaction des étapes de la C.I.
    - création d'outil
    - création de script
    - reproduction des éléments de prod
        - docker
            - db
            - apps
            - websocket
        - injection des données
    - [ ] Demande à mistral : 
    ```
    I am working on a project of store checkout surveilance. 
    The goal is to retrieve machine data from our kmoBoxes via a gateway-websocket system which is functional.
    The application is an angular-nestJs-mysql dockerized and hosted on an aws server.
    I want to set up a series of integration tests using an automated script to replicate and deploy the application and inject an export of the database.

    to further help you, here are a few files : 

    dockerfile : 
    ...


    production docker compose : 
    ...


    .env.prod file : 
    ...


    frontend package.json : 
    ...



    backend package.json 
    ...

    i will later provide the export of database, so you can focus on it later. For now, i  want to set up a series of integration tests using an automated script to replicate and deploy the application and inject an export of the database.
    ```
    - [ ] Réponse : 
    ```
    ```
    

**25 Avril**
- [ ] Mise en prod des fix KP-950/960
    - [x] Connexion à l'instance
    - [x] ```sudo docker stop kmo-predict-front kmo-predict-back```
    - [x] ```git pull origin develop```
    - [x] ```sudo docker remove kmo-predict-front kmo-predict-back```
    - [x] ```sudo  docker compose --env-file .env.prod -f docker-compose-prod.yml up --always-recreate-deps --build```
- [ ] Reprise de KP-943 optimiser les services backend
    - [ ] KP-944 Optimiser le service backend events
- [x] Réunion longue samere, version 2 cette fois sur le sujet de la récupération de l'ERP de nicolas decornet qui s'en va


**26 Avril**
- [ ] Remise en marche de la prod predict
    - [ ] Résolution du problème : 
    ```
    Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
    ```
- [ ] Réunion(s ?) de briefing sur Copaco, l'erm de nicolas decornet
    - [x] Réunion Copaco 26/04, matinée
    Niveau process de prod, demander à Fédé plutôt que Denis, qui est moins objectif
    Besoin de savoir quelques correspondances entre la base mysql et la base hyperfile (hfsql) de clipper
    Centre de contrôle hfsql accessible depuis le bureau à distance (?)
    Fédé reste maître de la base hfsql, et nous au digital on reprend la base mysql
    une sauvegarde automatique de la base de données clipper se fait tous les jours, zippée pour plus de praticité
    En cas de rollback nécessaire, plutôt cibler les fichiers à remplacer que de reload toute la base. (Fichiers .FIC dans le serveur /C/Serveur_HF/BDD_Backup/... avec cop et amaco ,distincte)
    commandes client ou fournisseur, ça reste côté clipper, pas copaco
    Fédérico s'en va en mars/avril prochain.
    Structure du code du projet copaco ; nicolas a fait évoluer sa manière de coder en fonction de ce qu'il a assimilé sur les méthodes (de production ?). Serait idéal d'avoir ses notes et insights là-dessus
    Si stabilisé, charge de travail (maintenance & debug) estimée à 1/2 à 1 journée de travail.
    nicolas a dit faire beaucoup de tests. Il y a des procédures, des cadres de tests ou c'est informel ?
    Pour l'instant, informel. L'équipe va normalement créer un document qui explique les relations et dépendances entre les modules de copaco
    fonctionnalités à développer
    - création des données techniques
    - création de comptes
    - gestion des droits de compte
    - gestion des créations et réassignations de marchandises
    - adaptation à amaco de copaco
    but de copaco est d'etre le reflet des flux physiques
    Andzrei et Fédé seront amenés à utiliser l'outil, ils connaissent un peu et ont déjà vu à quelques reprises l'app.
    ===
    Description des flux
    Travail à l'affaire / travail au catalogue , cop c'est un bordel entre les deux, même si plutôt travail à l'affaire en ce moment.
    **___** Besoin -> Bureau d'études (assemblage et hiérarchie des pièces : nomenclature) logiciel utilisé : créo, versionné avec windchill. Quand c'est validé, ça arrive aux méthodes
    **___** Méthodes -> Traduire les nomenclatures qui viennent du BE et de l'industrialiser, à savoir comment usiner et sur quelles machines. Traduction du plan dans l'entreprise et assignation aux différents pôles de l'atelier, et ajout des gammes (quels moyens matériels). Méthodes = intégrer les étapes réalisées sur le terrain en prenant matière première, temps machine & machine utilisée & composants nécessaires
    **___** Ordonnancement : Traduire nomenclature et gammes côté production. Pour chaque article est décidé l'ordre d'importance (réserver sur stock, soit commander soit lancer prod interne). Sur copaco, l'icone calculette permet d'algorithmer le travail d'ordonnancement actuellement fait par Deins & Fédé
    **___** Achats -> pas développé dans la réunion
    **___** Ordres de fabrication -> en cas d'arborescence, les O.F sont réservés entre eux, exemple, les composants de la pièce petit-enfant et enfant seront réservées/achetées/produites pour la pièce enfant.
    Quand les O.F et les réservations sont générées, il y a une vue "lancement atelier". Chaque chef d'atelier veut lancer les o.f qui concernet son atelier. Dans la vue, un dropdown permet de trier par atelier (ex. tolerie, peinture etc) et affiche tout ce qui concerne l'atelier en question trié par gamme. Plusieurs opérations (découpe, pliage, peinture ... peuvent être assignée à une même o.f). La vue mpontre ce qui est en attente de fabrication. Dans la même vue, à droite est la partie qui est actuellement en production, avec un code coloré pour :
    - 0, vient d'etre généré
    - 1, vient d'etre lancé, le chef d'atelier a déclenché
    - 2, faisable, les étapes précédentes ont été faites (découpe et pliage avant peinture par ex)
    - 3 démarré (assigné par un utilisateur d'atelier), en cours d'exécution
    - 4 clôturé 
    