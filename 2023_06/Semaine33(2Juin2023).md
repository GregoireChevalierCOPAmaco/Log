**2 Juin**
- [ ] Poursuite Predict
    - [x] Catchup log
    - [ ] Up du coverage
    - [ ] Back-end
    - [ ] Up du coverage
    - [ ] Back-end
        - [x] Passe modules
            - [x] App-test module : 
            ```
            import { Test, TestingModule } from '@nestjs/testing';
            import { TypeOrmModule } from '@nestjs/typeorm';
            import { ConnectionOptions } from 'typeorm';
            import { AppTestModule } from './app-test.module';
            import { Event } from './app/events/entities/event.entity';
            import { Gateway } from './app/gateways/entities/gateway.entity';
            import { KmoBox } from './app/kmo-boxes/entities/kmo-box.entity';
            import { Store } from './app/stores/entities/store.entity';
            import { HistoryGateway } from './app/history-gateway/entities/history-gateway.entity';

            class MockConfigService {
            get(key: string) {
                switch (key) {
                case 'DB_HOST':
                    return 'localhost';
                case 'DB_PORT':
                    return 5432;
                case 'DB_USERNAME':
                    return 'predict';
                case 'DB_PASSWORD':
                    return 'predict16022023';
                case 'DB_DATABASE':
                    return 'predict';
                default:
                    return undefined;
                }
            }
            }

            describe('AppTestModule', () => {
            let appModule: TestingModule;

            beforeAll(async () => {
                appModule = await Test.createTestingModule({
                imports: [
                    AppTestModule,
                    TypeOrmModule.forRootAsync({
                    useFactory: () => {
                        const dbConfig: ConnectionOptions = {
                        type: 'postgres',
                        host: 'localhost',
                        port: 5432,
                        username: 'predict',
                        password: 'predict16022023',
                        database: 'predict',
                        entities: [Event, Gateway, KmoBox, Store, HistoryGateway],
                        synchronize: true,
                        };
                        return dbConfig;
                    },
                    }),
                ],
                providers: [
                    {
                    provide: 'ConfigService',
                    useClass: MockConfigService,
                    },
                ],
                }).compile();
            });

            afterAll(async () => {
                await appModule.close();
            });

            it('should be defined', () => {
                expect(appModule).toBeDefined();
            });

            it('should provide the ConfigService', () => {
                const configService = appModule.get<MockConfigService>('ConfigService');
                expect(configService).toBeDefined();
            });

            });
            ```
            Test du defined & du config service
            - [x] events module : 
            test si le module est defined
            test si le config service est provided
            - [x] gateways module : 
            test si le module est defined
            test si le service est provided
            test si le gateway est provided
            test si le controller est provided
            - [x] history gateways module : 
            test si le module est defined
            test si le service est provided
            - [x] kmo boxes module : 
            test si le module est defined
            test si le service est provided
            test si le controller est provided
            - [x] mailing module : 
            test si le module est defined
            test si le service est provided
            - [x] statistic-formula module : 
            test si le module est defined
            test si le service est provided
            - [x] stores module : 
            test si le module est defined
            test si le service est provided
            - [x] users module : 
            test si le module est defined
            test si le service est provided
        - [ ] Résolution des erreurs CI
- [ ] Réunion 11h
    - [ ] 
- [ ] Mailing service
    - [ ] Tests sur l'envoi
    - [ ] Tests sur le contenu
    - [ ] Voir à qui un mail doit être envoyé
    - [ ] Voir quand un mail doit être envoyé
- [ ] Voir avec Anthony pour la réalisation d'une V alpha du projet
    - [ ] Prod & déploiement
    - [ ] Keycloak