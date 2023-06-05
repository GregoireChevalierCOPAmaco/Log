**5 Juin**
- [ ] Poursuite Predict
    - [x] Catchup log
    - [ ] Up du coverage
    - [ ] Back-end
        - [ ] Résolution du fail du test de l'app.module.ts
            - [x] Log des options, résultat :
            ```
              console.log
                options: [object Object]
            ```
            avec stringify : 
            ```
            options: {
                "type":"postgres",
                "port":null,
                "entities":[null,null,null,null,null],
                "autoLoadEntities":true,
                "synchronize":true
                }
            ```
            - [ ] Changement de l'app.module.ts pour y inclure le configService
                - [x] Check de (https://docs.nestjs.com/techniques/configuration)
        - [ ] Résolution du fail des tests de modules dans la CI
            - [x] essai : changements dans le fichier orm-config.ts : 
            ```
            // type: 'postgres',
            // host: process.env.DB_HOST,
            // port: parseInt(process.env.DB_PORT, 10),
            // username: process.env.DB_USER,
            // password: process.env.DB_PASSWORD,
            // database: process.env.DB_NAME,
            // entities: [Event, Gateway, KmoBox, Store, HistoryGateway],
            // autoLoadEntities: true,
            // synchronize: process.env.NODE_ENV !== 'production',
            type: 'postgres',
                host: 'localhost',
                port: 5432,
                username: 'predict',
                password: 'predict16022023',
                database: 'predict',
                entities: [Event, Gateway, KmoBox, Store, HistoryGateway],
                autoLoadEntities: true,
                synchronize: process.env.NODE_ENV !== 'production',
            ```
            sans plus de succès.
            - [ ] Essai de mock toutes les interactions avec la db dans les tests de modules.

    constructor() {
  // console.log('options: ' + JSON.stringify(options));
}