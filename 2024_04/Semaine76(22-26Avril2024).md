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
    