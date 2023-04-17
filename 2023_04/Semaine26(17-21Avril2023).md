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
            - [ ] Tester que la désactivation retourne le bon store
            - [ ] Tester que la désactivation update bien le store
            - [ ] Tester que le store est désactivé en base de données
            - [ ] Tester que l'affichage correspond aux données en base
