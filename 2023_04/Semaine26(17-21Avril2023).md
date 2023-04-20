**17 Avril**
- [ ] Poursuite Predict
    - [ ] KP-177 : test end to end désactivation
        - [x] Création et checkout de branche 177
        - [ ] Créer un enironnement fonctionnel de e2e
            - [x] Installation de ``` npm install --save-dev --legacy-peer-deps @nestjs/testing @nestjs/platform-express supertest```
            - [x] Création d'un fichier tab-store.e2e-spec.ts
            - [x] Ajout des imports :
                ```
                import { Test } from '@nestjs/testing';
                import { INestApplication } from '@nestjs/common';
                import * as request from 'supertest';
                import { AppModule } from '../src/app.module';
                import { StoreService } from '../src/store.service';
                import { TabStoreComponent } from '../src/tab-store.component';
                ```
            - [x] Correction du chemin des imports des components & service angular : 
                ```
                import { StoreService } from '../../kmo-predict-front/src/app/core/services/store.service';
                import { TabStoreComponent } from '../../kmo-predict-front/src/app/component/administrations/tab-store/tab-store.component';
                ```
            - [x] Résolution du format attendu (JSON) des openingHours : 
                ```
                openingHours: JSON.parse(JSON.stringify({
                    mon: ['09:00', '18:00'],
                    tue: ['08:00', '18:00'],
                    wed: ['08:00', '18:00'],
                    thu: ['08:00', '18:00'],
                    fri: ['08:00', '18:00'],
                    sat: ['08:00', '16:00'],
                    sun: ['08:00', '16:00'],
                }))
                ```
            - [x] Run des tests e2e fonctionnel 
        - [ ] Passage des 2 tests e2e de base
            - [ ] Résolution de l'erreur :
            ```
            FAIL  test/app.e2e-spec.ts (13.47 s)
            ● AppController (e2e) › / (GET)
            ```
            - [ ] Résolution de l'erreur :
            ```
            Jest encountered an unexpected token
            [Nest] 25772  - 17/04/2023 12:05:28   ERROR [TypeOrmModule] Unable to connect to the database. Retrying (1)...
            Error: SASL: SCRAM-SERVER-FIRST-MESSAGE: client password must be a string
            ```
        - [ ] Écriture des tests
            - [ ] Tester que la page existe
            - [ ] Tester que le bouton existe
            - [ ] Tester que le bouton appelle la fonction désactivation
            - [ ] Tester que l'appel à la db se fait bien
            - [ ] Tester que la connexion à la db est établie
            - [ ] Tester que la désactivation retourne le bon store
            - [ ] Tester que le store est désactivé en base de données
            - [ ] Tester que la désactivation update bien la variable du store dans le front
            - [ ] Tester que l'affichage correspond aux données en base

**18 Avril**
- [ ] Poursuite Predict
    - [ ] KP-177 : test end to end désactivation
        - [x] Créer un enironnement fonctionnel de e2e
        - [ ] Passage des 2 tests e2e de base
            - [ ] Résolution de l'erreur :
            ```
            FAIL  test/app.e2e-spec.ts (13.47 s)
            ● AppController (e2e) › / (GET)
            ```
            - [ ] Résolution de l'erreur :
            ```
            Jest encountered an unexpected token
            [Nest] 25772  - 17/04/2023 12:05:28   ERROR [TypeOrmModule] Unable to connect to the database. Retrying (1)...
            Error: SASL: SCRAM-SERVER-FIRST-MESSAGE: client password must be a string
            ```
            - [x] Demande a gpt ce qu'il en pense : 
            ```
                I am running a project with the following architecture :
                - a turborepo folder in which there is :
                - an angular project
                - a nestJs project
                There is a .env.dev file in the root turborepo folder as follows :
                ```
                # Databse environment variables
                DB_HOST=postgres
                DB_PORT=5432
                DB_USER=predict
                DB_PASSWORD=predict16022023
                DB_NAME=predict

                # Node environment variables
                NODE_ENV=development

                # Api ports
                NEST_PORT=3000
                ANGULAR_PORT=4200

                # Apps environment variables
                WEB_APP_URL=http://localhost:3000

                # Local dev keycloak
                KC_DEV_PORT=8080
                KC_BASEURL=http://keycloak:8080
                KC_IMG=ghcr.io/cop-amaco/kmo-keycloak:latest
                KC_REALM=Cop_sass
                KC_CLIENT=KMO_Predict
                KC_ADMIN_USERNAME=admin
                KC_ADMIN_PASSWORD=pw123

                My issue is : e2e tests in the nestJs app do not pass since nest seems to be retrieving the user credentials of my windows' session instead of the DB_USER, DB_PASSWORD etc...
                Is there anything you can tell me about this ?
            ```
            Sa réponse :
            ```
            Based on the information provided, it seems like the issue you are facing might be related to the way NestJS is accessing the environment variables.

            When NestJS is started, it will typically load the environment variables from the .env file using a package like dotenv. 
            However, it's possible that the package is not being loaded correctly or the .env file is not being read properly.
            One possible solution is to ensure that the dotenv package is installed and correctly configured in your NestJS project. 
            You can do this by adding the following code at the top of your main.ts file in the NestJS app
            ```
            voir pour dotenv package
                - [x] Installation de dotenv dans le back : déjà présent nativement
                - [x] Configuration de dotenv sans succès
            - [x] Essai d'import forcé du .env.dev dans l'app.module.ts : 
                ```
                @Module({
                imports: [
                    ConfigModule.forRoot({
                    envFilePath: '../../.env.dev'
                    })
                    ...
                ```
                - [x] Sans succès
            - [ ] Reprise du dossier kmo-back en standalone pour voir si le e2e passe
                - [x] Copiecolle du dossier
                - [x] ```npm i --legacy-peer-deps``` & ```npm run test:e2e```, retour :
                    ```
                    FAIL  test/app.e2e-spec.ts
                        ● Test suite failed to run
                                                                                                                                                                                                                                            
                        src/app/stores/stores.service.ts:9:8 - error TS2307: Cannot find module 'nestjs-typeorm-paginate' or its corresponding type declarations.

                        9 } from 'nestjs-typeorm-paginate';
                    ```
                - [x] Installation du package, lancement test et retour de l'erreur :
                ```
                ERROR [TypeOrmModule] Unable to connect to the database. Retrying (4)...
                Error: SASL: SCRAM-SERVER-FIRST-MESSAGE: client password must be a string
                ```
                - [x] Ajout d'un .env à la racine, même problème
                - [x] Modif app.module.ts : 
                ```
                @Module({
                    imports: [
                        ConfigModule,
                        EventsModule,
                        GatewaysModule,
                        KmoBoxesModule,
                        TypeOrmModule.forRoot({
                        type: 'postgres',
                        host: 'localhost',
                        port: 5432,
                        username: 'predict',
                        password: 'predict16022023',
                        database: 'predict',
                        // entities: [Event, Gateway, KmoBox, Store],
                        autoLoadEntities: true,
                        synchronize: process.env.NODE_ENV !== 'production'
                        }),
                        ...
                ```
                nouveau message d'erreur : 
                ERROR [TypeOrmModule] Unable to connect to the database. Retrying (2)...
                TypeORMError: Entity metadata for Gateway#kmoBoxes was not found. Check if you specified a correct entity object and if it's connected in the connection options.
                - [x] Ajout de ligne ```entities: [Event, Gateway, KmoBox, Store]``` avec imports corrects
                - [x] ```npm run test:e2e```, retour :
                ```
                 PASS  test/app.e2e-spec.ts (6.82 s)
                AppController (e2e)
                    √ / (GET) (602 ms)
                                                          
                Test Suites: 1 passed, 1 total                                          
                Tests:       1 passed, 1 total                                         
                Snapshots:   0 total
                Time:        6.949 s
                ```
                - [x] Reproduction dans le dossier dev OK
            - [ ] Plongée dans les options de typeOrmModule.forRoot(options) pour comprendre d'où vient le lézard
            - [ ] Reprise de la doc nest pour la config étape par étape
        - [ ] Résolution de : 
        ```
        FAIL  test/tab-store.e2e-spec.ts                                 
         
        ● Test suite failed to run
            Jest encountered an unexpected token                                 
            Jest failed to parse a file. This happens e.g. when your code or its dependencies use non-standard JavaScript syntax, or when Jest is not configured to support such syntax.                      
            Out of the box Jest supports Babel, which will be used to transform your files into valid JS based on your Babel configuration.                        
            By default "node_modules" folder is ignored by transformers.

            Here's what you can do:
            • If you are trying to use ECMAScript Modules, see https://jestjs.io/docs/ecmascript-modules for how to enable it.
            • If you are trying to use TypeScript, see https://jestjs.io/docs/getting-started#using-typescript
            • To have some of your "node_modules" files transformed, you can specify a custom "transformIgnorePatterns" in your config.
            • If you need a custom transformation specify a "transform" option in your config.
            • If you simply want to mock your non-JS modules (e.g. binary assets) you can stub them out with the "moduleNameMapper" config option.

                | ^
            2 | import { HttpClient } from '@angular/common/http';
            3 | import { environment } from '../../../environments/environment';
        ```
        - [ ] Trouver comment modifier les envs selon le de / test/ prod
            - [ ] Reprise de (https://docs.nestjs.com/techniques/configuration) encore une fois, et (https://docs.nestjs.com/techniques/configuration#custom-env-file-path)
            - [ ] Check de (https://dev.to/pitops/managing-multiple-environments-in-nestjs-71l)
        - [ ] Écriture des tests
            - [ ] Tester que la page existe
            - [ ] Tester que le bouton existe
            - [ ] Tester que le bouton appelle la fonction désactivation
            - [ ] Tester que l'appel à la db se fait bien
            - [ ] Tester que la connexion à la db est établie
            - [ ] Tester que la désactivation retourne le bon store
            - [ ] Tester que le store est désactivé en base de données
            - [ ] Tester que la désactivation update bien la variable du store dans le front
            - [ ] Tester que l'affichage correspond aux données en base

3000 =/= 5432 ? où vont taper les options du typeOrm ? dans la db ou l'api ?



**19 Avril**
- [ ] Poursuite Predict
    - [ ] KP-190
        - [ ] Doit passer par la résolution du problème d'environnement & de la communication e2e/bdd
    - [ ] KP-177
        - [ ] Doit passer par la résolution du problème d'environnement & de la communication e2e/bdd
        - [ ] Demande à gpt de : 
        ```
        i have a running project with 2 applications (one angular, and one netJs) in a root directory turborepo.
        The users and their roles & permissions are managed by a working keycloak instance 
        This project is run in docker containers :  one for the keycloak, one for the angular & nestAPI, & one for the postgres database
        I haveinstalled and configured jest that runs and passes unit tests.
        I have supertest library already installed and working.
        I want to write e2e tests using Jest to ensure the nestJs API works properly. 
        I have an e2e test file as follows :

        ...
        it('/ (GET)', () => {
            return request(app.getHttpServer()).get('/stores').expect(200);
        });

        In this file, the endpoint /stores targeted exists and is working.
        Running `jest app.e2e-spec.ts` fails with in my code editor's console :
        ERROR [TypeOrmModule] Unable to connect to the database. Retrying (1)...
        Error: SASL: SCRAM-SERVER-FIRST-MESSAGE: client password must be a string
        
        my postgres config in docker-compose.yml file is as follows :
        postgres:
            container_name: predict-pg
            image: postgres:15.0-alpine3.16
            restart: on-failure
            environment:
            - POSTGRES_USER=${DB_USER?}
            - POSTGRES_PASSWORD=${DB_PASSWORD?}
            - POSTGRES_DB=${DB_NAME?}
            ports:
            - "${DB_PORT?}:5432"
            networks:
            - app

        and the variables refered as in the ${} parts are defined in the following .env file :
        DB_HOST=postgres
        DB_PORT=5432
        DB_USER=predict
        DB_PASSWORD=predict16022023
        DB_NAME=predict
        ```
        gpt suggère : Verify that the environment variables defined in the docker-compose.yml file are being correctly read by Docker when the containers are launched. You can run docker-compose config to check that the environment variables are being properly set.
            - [x] Lancement de ```docker-compose config``` et retour console : 
            ERROR: 
            Can't find a suitable configuration file in this directory or any parent. Are you in the right directory?
            - [x] Renommage temporaire du fichier compose en enlevant le .env et lancement de ```docker-compose config``` avec retour console :
            ```
            WARNING: The KC_IMG variable is not set. Defaulting to a blank string.  
            ERROR: Missing mandatory value for "environment" option interpolating ['POSTGRES_USER=${DB_USER?}', 'POSTGRES_PASSWORD=${DB_PASSWORD?}', 'POSTGRES_DB=${DB_NAME?}'] in service "postgres": DB_USER 
            ```
            - [x] Changement du docker-compose.yml :
            ```
              postgres:
                container_name: predict-pg
                image: postgres:15.0-alpine3.16
                restart: on-failure
                environment:
                - .env.dev
                ports:
                - "${DB_PORT?}:5432"
                networks:
                - app
            ```
            au lieu de :
            ```
            postgres:
                container_name: predict-pg
                image: postgres:15.0-alpine3.16
                restart: on-failure
                environment:
                - POSTGRES_USER=${DB_USER?}
                - POSTGRES_PASSWORD=${DB_PASSWORD?}
                - POSTGRES_DB=${DB_NAME?}
                ports:
                - "${DB_PORT?}:5432"
                networks:
                - app
            ```
            et brûlage de tout le docker existant, containers, images, volumes
            - [x] Rebuild du docker   
                - [x] ```docker compose --env-file .env.dev -f docker-compose.dev.yml up --renew-anon-volumes --always-recreate-deps --build``` 
                - [x] Le predict-pg fail et rebuild à l'infini
                - [x] rajout dans le .env.dev de :
                ```
                POSTGRES_USER=predict
                POSTGRES_PASSWORD=predict16022023
                POSTGRES_DB=predict
                ```  
            - [x] Relance des tests e2e, sans succès, même erreur.    
            - [x] Changement du docker-compose.yml :
            ```
              postgres:
                container_name: predict-pg
                image: postgres:15.0-alpine3.16
                restart: on-failure
                # env_file:
                #   - .env.dev
                environment:
                - POSTGRES_USER=predict
                - POSTGRES_PASSWORD=predict16022023
                - POSTGRES_DB=predict
                ports:
                - "${DB_PORT?}:5432"
                networks:
                - app
            ```
            au lieu de :
            ```
            postgres:
                container_name: predict-pg
                image: postgres:15.0-alpine3.16
                restart: on-failure
                environment:
                - POSTGRES_USER=${DB_USER?}
                - POSTGRES_PASSWORD=${DB_PASSWORD?}
                - POSTGRES_DB=${DB_NAME?}
                ports:
                - "${DB_PORT?}:5432"
                networks:
                - app
            ```
            et brûlage de tout le docker existant, containers, images, volumes
            - [x] Rebuild du docker   
                - [x] ```docker compose --env-file .env.dev -f docker-compose.dev.yml up --renew-anon-volumes --always-recreate-deps --build``
            - [x] Rebuild du realm
            - [x] Repeuplement de la db
            - [x] Relance des tests e2e, sans succès, même erreur.    
    - [x] Création d'un fichier AppTest.module.ts avec la config db locale :
    ```
    @Module({
    imports: [
        ConfigModule,
        EventsModule,
        GatewaysModule,
        KmoBoxesModule,
        TypeOrmModule.forRoot(
        {
            type: 'postgres',
            host: 'localhost',
            port: 5432,
            username: 'predict',
            password: 'predict16022023',
            database: 'predict',
            entities: [Event, Gateway, KmoBox, Store],
            autoLoadEntities: true,
            synchronize: process.env.NODE_ENV !== 'production'
        }
        ),
        StoresModule,
        StatisticFormulaModule,
        UsersModule,
        MailingModule,
    ],
    providers: [ConfigService],
    })
    export class AppTestModule {}
    ```
    - [x] Update du fichier de test : 
    ```
    beforeEach(async () => {
        const moduleFixture: TestingModule = await Test.createTestingModule({
        imports: [AppTestModule],
        }).compile();

        app = moduleFixture.createNestApplication();
        await app.init();
    });

    afterEach(async () => {
        await app.close();
    });
    ```
    Les tests e2e passent * maintenant
- [ ] Écriture des tests
    - [ ] Tester que la page existe
    - [ ] Tester que le bouton existe
    - [ ] Tester que le bouton appelle la fonction désactivation
    - [ ] Tester que l'appel à la db se fait bien
    - [ ] Tester que la connexion à la db est établie
    - [ ] Tester que la désactivation retourne le bon store
- [ ] Essai de couplage e2e-docker de test
    - [x] Check de (https://medium.com/free-code-camp/how-to-dockerize-your-end-to-end-acceptance-tests-dbb593acb8e0)
    - [ ] Check de (https://node.testcontainers.org/quickstart/)
    - [ ] Check de (https://github.com/leonidas/e2e-ui-testing)
    

**20 Avril**
- [ ] Poursuite Predict
    - [ ] KP-177
        - [x] Doit passer par la résolution du problème d'environnement & de la communication e2e/bdd
    - [ ] Écriture des tests
        - [ ] Tester que l'application angular existe & est définie
        - [x] Tester que l'application angular tourne et est accessible sur le port 3000 :
        ```
        import * as request from 'supertest';
        import { NestFactory } from '@nestjs/core';
        import { AppTestModule } from '../src/app-test.module';

        describe('AppController (e2e)', () => {
        let app;

        beforeAll(async () => {
            app = await NestFactory.create(AppTestModule);
            await app.listen(3002); // Doit démarrer une instance de nest
                                    // Le port 3002 est utilisé pour run
                                    // le test e2e et ne pas entrer en conflit
                                    // avec l'instance réelle sur le 3001
        });

        afterAll(async () => {
            await app.close();
        });

        test('if the angular app is running on port 3000', () => {
                return request('http://localhost:3000')
                .get('/')
                .expect(200)/*
                .expect('Content-Type', /text\/html/)
                .expect('Welcome to my Angular app!')*/;
            });
        });
        ```
        - [x] Tester que l'instance keycloak tourne et est accessible sur le port 8080 :
        ```
          test('if the keycloak instance is running on port 8080', () => {
            return request('http://localhost:8080').get('/').expect(200);
        });
        ```
        - [x] Tester que la redirection vers l'auth keycloak se fait bien :
        ```
        describe('KeycloakAuthentication', () => {
        it('should redirect unauthenticated users to Keycloak login page', async () => {
        // Set up a request to the Angular app
        const response = await request('http://localhost:8080/realms/master/protocol/openid-connect/auth?client_id=security-admin-console&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2Fadmin%2Fmaster%2Fconsole%2F%23%2Frealms%2FCop_sass&state=3055055e-037e-4079-8d15-22a1e2ee2f5f&response_mode=fragment&response_type=code&scope=openid&nonce=509564f3-2b76-4841-8b8f-d00a0aad1bcb&code_challenge=tEmmBYlbp0m743pvMIaHyN5TcXu8IMd9dkqN19FAimw&code_challenge_method=S256').get('/');
    
        // Check that the response is a redirect to the Keycloak login page
        expect(response.status).toEqual(302);
        expect(response.header.location).toMatch("http://localhost:8080/admin/master/console/#/realms/Cop_sass&error=invalid_request&error_description=Invalid+parameter%3A+code+challenge+method+is+not+configured+one&state=3055055e-037e-4079-8d15-22a1e2ee2f5f");
        });
    });
        ```
        - [ ] Tester que la page existe
        - [ ] Tester que le bouton existe
        - [ ] Tester que le bouton appelle la fonction désactivation
        - [ ] Tester que l'appel à la db se fait bien
        - [ ] Tester que la connexion à la db est établie
        - [ ] Tester que la désactivation retourne le bon store
    - [ ] KP-190
        - [x] Doit passer par la résolution du problème d'environnement & de la communication e2e/bdd
        - [ ] Demande à gpt de : 