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
            - [ ] Reprise de la doc nest pour la config étape par étape
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

