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
- [ ] Ajout d'un champ closedTime dans l'entité kmoBox  
    - [ ] Connexion à l'instance
    - [ ] Stop des containers app & pg
    - [ ] Remove du container pg
    - [ ] Rebuild des containers
- [ ] Ajuster la logique pour l'update du motorusage
- [ ] Mise à jour Jira
- [ ] MàJ du fichier de tests
ajouter ticket ajout condition (state) pour detection event trappes courant dernière semaine afin d'éviter les détections de timeouts
