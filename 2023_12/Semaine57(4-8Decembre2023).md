**4 Décembre**
- [ ] Résoudre le problème de uptime 
    - [ ] Test fonctionnel de corrélation : Résultat, le uptime ne prend que le temps moteur on. Changer cela 
- [x] Repasse à la branche feat/KP-650_add_closedTime_to_kmo
- [ ] Ajout d'un champ closedTime dans l'entité kmoBox  
    - [ ] Modification de kmo-box
        - [ ] COP dialog component
        - [ ] COP dialog component html
    - [ ] Event service
    - [ ] Event service spec
    - [x] Résolution des bugs 
        - [x] 
        ```
        [Nest] 170  - 12/04/2023, 12:57:38 PM   ERROR [ExceptionHandler] column "closedTime" of relation "kmo_box" contains null values
        predict-apps     | kmo-predict-back:dev: QueryFailedError: column "closedTime" of relation "kmo_box" contains null values
        predict-apps     | kmo-predict-back:dev:     at PostgresQueryRunner.query (/app/src/driver/postgres/PostgresQueryRunner.ts:299:19)
        predict-apps     | kmo-predict-back:dev:     at processTicksAndRejections (node:internal/process/task_queues:95:5)
        predict-apps     | kmo-predict-back:dev:     at PostgresQueryRunner.executeQueries (/app/src/query-runner/BaseQueryRunner.ts:651:13)
        predict-apps     | kmo-predict-back:dev:     at PostgresQueryRunner.addColumn (/app/src/driver/postgres/PostgresQueryRunner.ts:1115:9)
        predict-apps     | kmo-predict-back:dev:     at PostgresQueryRunner.addColumns (/app/src/driver/postgres/PostgresQueryRunner.ts:1129:13)
        predict-apps     | kmo-predict-back:dev:     at RdbmsSchemaBuilder.addNewColumns (/app/src/schema-builder/RdbmsSchemaBuilder.ts:823:13)
        predict-apps     | kmo-predict-back:dev:     at RdbmsSchemaBuilder.executeSchemaSyncOperationsInProperOrder (/app/src/schema-builder/RdbmsSchemaBuilder.ts:226:9)
        predict-apps     | kmo-predict-back:dev:     at RdbmsSchemaBuilder.build (/app/src/schema-builder/RdbmsSchemaBuilder.ts:95:13)
        predict-apps     | kmo-predict-back:dev:     at DataSource.synchronize (/app/src/data-source/DataSource.ts:329:9)
        predict-apps     | kmo-predict-back:dev:     at DataSource.initialize (/app/src/data-source/DataSource.ts:267:43)
        ```
        - [x] Le problème vient du fait qu'on trouve pas le champ closedTime dans la base, car il a été créé dans le back mais le docker pg n'a pas été rebuild derrière
        - [x] Delete de la base locale et rebuild
- [ ] Ajuster la logique pour l'update du motorusage
- [ ] Mise à jour Jira
- [ ] MàJ du fichier de tests
ajouter ticket ajout condition (state) pour detection event trappes courant dernière semaine afin d'éviter les détections de timeouts


**5 Décembre**
- [x] Réalisation whiteboard usecases uptime 
- [ ] Résoudre le problème de uptime 
    - [ ] Test fonctionnel de corrélation : Résultat, le uptime ne prend que le temps moteur on. Changer cela 
    - [ ] Refonte de la logique
        - [x] Initialize lastTreatedEvent :
        ```
        private lastTreatedEventByKmo: { [kmo: string]: { treatedEventId: string; treatedEventDatetime: string } } = {};
        private lastEventByKmo: { [kmo: string]: { eventId: string; eventDatetime: string } } = {};
        ```
        la méthode pour remplir ce dictionnaire est : 
        ```
        // Set values for a key ("kmo")
        this.lastTreatedEventByKmo["kmo"] = {
        mac: "value",
        datetime: "date&heure",
        };
        ```
        - [x] Récupération de la mac de chaque kmoBox qui **a des events qui lui sont liés** : 
        ```
        const uniqueKmoBoxMacs = await this.kmoBoxRepository
            .createQueryBuilder('kmoBox')
            .select('DISTINCT kmoBox.mac', 'kmoBoxMac')
            .innerJoin('kmoBox.events', 'event')
            .getRawMany();
        ```
        - [x] for each uniqueKmoBoxMacWithEvents, aller set le dernier event : 
        ```
        const uniqueKmoBoxMacWithEvents = await this.kmoBoxRepository
            .createQueryBuilder('kmoBox')
            .select('DISTINCT kmoBox.mac', 'kmoBoxMac')
            .innerJoin('kmoBox.events', 'event')
            .getRawMany();

            for (const kmo of uniqueKmoBoxMacWithEvents) {
            const mac = kmo.kmoBoxMac;

            const latestEvent = await this.eventRepository
            .createQueryBuilder('event')
            .where('event.kmoBox.mac = :kmoBoxMac', { kmoBoxMac: mac })
            .orderBy('event.datetime', 'DESC')
            .getOne();

            if (latestEvent) {
                this.lastEventByKmo[mac] = {
                eventId: latestEvent.id,
                eventDatetime: latestEvent.datetime.toString()
                };
            }
        }
        ```
        - [x] Prise en compte du lastTreatedEventByKmo à la fin de la function : 
        ```
        this.lastTreatedEventByKmo[mac] = {
            treatedEventId: this.lastEventByKmo[mac].lastEventId,
            treatedEventDatetime: this.lastEventByKmo[mac].lastEventDatetime,
            treatedEventState: this.lastEventByKmo[mac].lastEventState,
        };
        ```
        - [ ] for each event that event.datetime > lastTreatedEvent.datetime 
            - [ ] return all events where datetime > lastTreatedEvent.datetime 
            - [ ] si allPosteriorEvents.state == !=power_off
                - [ ] get first allPosteriorEvents.datetime
                - [ ] timeDiff = latestEvent.datetime - first allPosteriorEvents.datetime
                - [ ] uptime+= timeDiff
            - [ ] si allPosteriorEvents.state != !=power_off
                - [ ] sort power_off events by ASC
                - [ ] pour chaque powerOffEventsSortedByASC
                    - [ ] timeDiff = lastTreatedEvent.datetime  - powerOffEvent.datetime
                    - [ ] uptime += timeDiff
                    - [ ] powerOffEvent -> lastTreatedEvent
        - [x] twoMinutesAgo = Time - 2 *60
        - [x] if(lastEvent.state != power_off && lastEvent.datetime > twoMinutesAgo) uptime += 120
        ```
        if (latestEvent) {
            const eventDate = new Date(latestEvent.datetime);

            this.lastEventByKmo[mac] = {
            lastEventId: latestEvent.id,
            lastEventDatetime: eventDate.toISOString(),
            lastEventState: latestEvent.state
            };

            if (latestEvent.state !== 'power_off') {
            const kmoBox = await this.kmoBoxRepository.findOne({ where: { mac } });
            kmoBox.uptime += 12; 

            await this.kmoBoxRepository.save(kmoBox);
            }
        }
        ```
- [x] Ajout d'un champ closedTime dans l'entité kmoBox  
    - [x] Connexion à l'instance
    - [x] Stop des containers app & pg
    - [x] Remove du container pg
    - [x] Rebuild des containers
- [ ] Éclater la fonction processEventsAndUpdateUptime en un maximum de petites fonctions à paramètres
- [ ] Ajuster la logique pour l'update du motorusage
- [ ] Mise à jour Jira
- [ ] MàJ du fichier de tests
    - [ ] Ajouter le test : 
        - ouvrir une kmo & faire des events
        - down le serveur pendant que des events sont émis
        - re up le serveur
        - constater que le uptime s'est correctement incrémenté
ajouter ticket ajout condition (state) pour detection event trappes courant dernière semaine afin d'éviter les détections de timeouts


**7 Décembre**
- [ ] Résoudre le problème de uptime 
    - [ ] Test fonctionnel de corrélation : Résultat, le uptime ne prend que le temps moteur on. Changer cela 
    - [ ] Refonte de la logique
        - [ ] Refactorisation du fichier en sortant la logique de la fonction updateuptime pour la mettre dans des méthodes qui seront appelées dans updateuptime
        - [ ] Création de méthode pour détecter les events postérieurs au lastTreatedEvent
        - [ ] Création de méthode pour ajouter au uptime le temps depuis le premier event postérieur au lastTreatedEvent SI PAS d'events power_off
        - [ ] Création de méthode pour ajouter au uptime le temps depuis le premier event postérieur au lastTreatedEvent SI events power_off