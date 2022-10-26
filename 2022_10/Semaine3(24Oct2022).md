*** LOG Gregoire Chevalier ***

**24 Octobre**
- [x] Redémarrage et application des màjs windows/docker vscode
- [ ] Testing du projet
    - [ ] Tests unitaires
        - [x] Résolution de tous les problèmes de ```Nest can't resolve dependencies of...```
            - [x] Reprsise de doc (https://docs.nestjs.com/faq/common-errors#cannot-resolve-dependency-error), (https://docs.nestjs.com/fundamentals/custom-providers#di-fundamentals), (https://docs.nestjs.com/providers) & (https://docs.nestjs.com/modules)
            - [x] Résolution du problème pour src/pieces/pieces.controller.spec.ts
                - [x] Ajout de AssJwtAuthGuard dans les providers du module pieces.module.ts, *sans changement de message d'erreur*
                - [x] Mise en commentaire des lignes provide/usevalue jest.fn() pour les valeurs Piece, PieceVehicle & Vehicle. Retour au message d'erreur initial : Nest can't resolve dependencies of the PiecesService
                    - [x] changement dans le provide: de getRepoToken en getRepo. Obtention de l'erreur : Type 'Repository< Piece>' is not assignable to type 'InjectionToken'. Pas de changements
                - [x] Retrait du commentaire de provide/usevalue jest.fn() pour Piece. Retour d'erreur : Nest can't resolve dependencies of the PiecesService pour l'argument PieceVehicleRepository at index [1]
                - [x] Mise en commentaires des @imports dnas pieces.module.ts : Pas de changement d'erreur. Conclusion, l'erreur ne vient pas du fichier module
                - [x] Décommentaire des lignes provide/usevalue jest.fn() pour les valeurs Piece, PieceVehicle & Vehicle. Message d'erreur reçu : Nest can't resolve dependencies of the AssJwtAuthGuard (Reflector, authService)
                - [x] Ajout dans le provider[] des services AssJwtAuth, Auth, Jwt, User, Mailer
                - [x] Erreur reçue : Nest can't resolve dependencies of the MailerService (?, MAILER_TRANSPORT_FACTORY). Please make sure that the argument MAILER_OPTIONS,
                - [x] Check de (https://stackoverflow.com/questions/61733060/unit-testing-nestjs-controller-with-injection) et ajout des lignes dans provider : 
                ```
                MailerService,
				{ provide: MAILER_OPTIONS, useValue: jest.fn()},
                ``` 
                - [x] Erreur reçue : Make sure to provide a nodemailer transport configuration object, connection url or a transport plugin instance.
                - [x] Fournir les infos relatives au mail dans le useValue : 
                ```
                MailerService,
				{ provide: MAILER_OPTIONS, useValue: {
					message: {
						from: process.env.MAIL_USER
					},
					transport: {
						host: process.env.MAIL_HOST,
						port: process.env.MAIL_PORT,
						secure: process.env.MAIL_SECURE,
						auth: {
							user: process.env.MAIL_USER,
							pass: process.env.MAIL_PASS
						}
					}},
				},
                ```
            - [x] Résolution du problème pour rc/vehicles/vehicles.controller.spec.ts
                - [x] Ajout des lignes provide: getRepoToken/usevalue jest.fn() pour les valeurs Piece, PieceVehicle & Vehicle, AssJwtAuthService, AuthService, JwtService, UsersService, MailerService, MAILER_OPTIONS
                - [x] Retour d'erreur : 
                ```
                A circular dependency has been detected inside RootTestModule. Please, make sure that each side of a bidirectional relationships are decorated with "forwardRef()".
                ```
            - [x] Résolution du problème de dépendances de VehiclesService (?, PieceRepo, PieceVehicRepo) : Clear de la , en trop, c'était une typo
        - [x] Résolution du problème pour src/stores/stores.controller.spec.ts
            - [x] Ajout dans les providers du getRepoToken(Store), retour d'erreur : Nest can't resolve dependencies of the AdminJwtAuthGuard
            - [x] Ajout dans le provider : [] de : 
            ```
            AdminJwtAuthGuard,
				AssJwtAuthGuard,
				MailerService,
				UsersService,
				AuthService,
				JwtService,
				{ provide: getRepositoryToken(Store), useValue: jest.fn() },
				{ provide: getRepositoryToken(User), useValue: jest.fn() },
				{ provide: MAILER_OPTIONS, useValue: {
					message: {
						from: process.env.MAIL_USER
					},
					transport: {
						host: process.env.MAIL_HOST,
						port: process.env.MAIL_PORT,
						secure: process.env.MAIL_SECURE,
						auth: {
							user: process.env.MAIL_USER,
							pass: process.env.MAIL_PASS
						}
					}},
				}
            ```
            POUR REFERENCE : les valeurs insérées dans le provider [], seront référées au **Bundle provide:**
        - [x] Résolution du problème pour src/tickets/tickets.service.spec.ts
        - [x] Résolution du problème pour src/tickets/tickets.controller.spec.ts
            - [x] Retour d'erreur : Nest can't resolve dependencies of the HttpService (?). Please make sure that the argument AXIOS_INSTANCE_TOKEN
            - [x] Résolution en commentant ```private readonly httpService: HttpService``` dans l'export class de tickets.controller
        - [x] Résolution du problème pour src/tickets/ticket-history/ticket-history.controller.spec.ts
        - [x] Résolution du problème pour src/statistics/statistics.service.spec.ts 
        - [x] Résolution du problème pour src/statistics/statistics.controller.spec.ts 
        - [x] Résolution du problème pour src/regionals-managements/regionals-managements.controller.spec.ts 
        - [x] Résolution du problème pour src/users/users.service.spec.ts
        - [x] Résolution du problème pour src/reports/reports.controller.spec.ts
    - [x] Push et merge sur git
- [x] Réunion followup
    - [ ] Définir les cas de tests qui nécessiteraient de lever des erreurs explicites
    - [ ] Définir le niveau de détail des retours d'erreurs lors des fails
    - [ ] Renommer le kw297 du jira "test unitaires" et assigner des sous-tâches si nécessaire
- [ ] Testing du projet
    - [ ] Installer dans kmo-back (working dir) jest-mysql
        - [x] Installation de jest-mysql
            ```
            Make sure jest and mysql are installed as well in the project, as they are required as peer dependencies.
            ```
            -> installation de mysql
            -> reinstallation de jest-mysql
            Lancement des commandes :
            ```
            npm install jest-mysql --force
            npm audit fix --force
            ```
        - [ ] Configurer proprement le tout
            - [x] Dans package.json, ajout de la ligne : "```preset": "jest-mysql"``` dans la config jest. Lancer les tests retourne maintenant l'erreur : 
            ```
            Jest: Got error running globalSetup - C:\Users\gchevalier\cleankmo\KMO_WEB\kmo-back\node_modules\jest-mysql\setup.js, reason: Unable to find and import testing database config
            ```
            - [x] Création du fichier jest.config.js dans le dossier src rempli comme suit :
            ```
            module.exports = {
                preset: "jest-mysql"
                //any other configuration
            };
            ```
            - [x] Création du fichier jest-mysql-config.js dans le dossier src rempli comme suit :
            ```
            module.exports = {
                databaseOptions: {
                host: "kmo-mysql",
                port: 3306,
                user: "KMO",
                password: "KMO15022022",
                database: "KMO"
                },
                createDatabase: true,
                // dbSchema: "DB_creation.sql",
                truncateDatabase: false
            };
            ```
    - [ ] Tests unitaires
        - [ ] Accéder à global.db  
        - [ ] Tester la connexion à la base
            - [ ] Résoudre le problème de global.db = undefined.
        - [ ] Dégager jest-mysql pour autre chose ? 
        - [ ] Tester la persistance des données
    - [ ] Appliquer les tests à l'entité Reports
    - [ ] Tests fonctionnels
    - [ ] Tests e2e
    - [ ] S'assurer que les données arrivent en base  
  
**25 Octobre**
- [ ] Testing du projet
    - [x] Installer dans kmo-back (working dir) jest-mysql
        - [x] Configurer proprement le tout
            - [x] Déplacement des fichiers jest.config.js & jest-mysql-config.js dans kmo-back depuis src
            - [x] Retour console : 
            ```
            Multiple configurations found:
                * C:/Users/gchevalier/cleankmo/KMO_WEB/kmo-back/jest.config.js
                * `jest` key in C:/Users/gchevalier/cleankmo/KMO_WEB/kmo-back/package.json

            Implicit config resolution does not allow multiple configuration files.
            Either remove unused config files or select one explicitly with `--config`.
            ```
            - [x] Retour d'erreur : 
            ```
            Error: getaddrinfo ENOTFOUND kmo-mysql
            at GetAddrInfoReqWrap.onlookup [as oncomplete] (node:dns:109:26)
            ```
            - [x] Check de (https://jestjs.io/docs/next/configuration) & (https://jestjs.io/docs/next/configuration#preset-string)
            - [x] Suppression de :
            ```
		    "preset": "jest-mysql",
            ```
            du fichier package.json
            - [x] Résolution de l'erreur getaddrinfo ENOTFOUND : remplacement de "kmo-mysql" (nom du conteneur docker) par localhost. Wtf mais ça marche donc bon....
        - [x] Résolution des erreurs : ```TypeError: Class extends value undefined is not a constructor or null``` liées à la configuration de la db
            - [x] Check de (https://jestjs.io/docs/es6-class-mocks#calling-jestmockdocsenjest-objectjestmockmodulename-factory-options-with-the-module-factory-parameter), (https://github.com/kulshekhar/ts-jest/issues/1070)
            - [x] Suppression du fichier jest.config.js, et ajout de la ligne : 
            ```
            "preset": "jest-mysql",
            ``` à jest du package.json . Les tests passent maintenant
    - [x] Tests unitaires
        - [x] Accéder à la db
            - [x] Création d'un fichier ormconfig.json rempli comme suit : 
            ```
            [
                {
                    "type": "mysql",
                    "host": "localhost",
                    "port": 3306,
                    "user": "KMO",
                    "password": "KMO15022022",
                    "database": "KMO"
                }
            ]
            ```
            - [x] Création d'un fichier dbco.ts dans le dossier des .spec rempli comme suit : 
            ```
            import process from "process";
            import { getConnectionOptions, createConnection } from "typeorm"

            export const dbco = async () => {
                const createConnectionOptions = await getConnectionOptions();
                console.log(createConnectionOptions);
                return createConnection({...createConnectionOptions});
            }
            ```
            - [x] Création d'un test d'accès à la db dans pieces.service.spec.ts : 
            ```
            	it('should return a positive number', async () => {
                jest.setTimeout(10000);
                const connection = dbco();
                const count = (await connection).getRepository(Piece).count();
                console.log(count);
                expect(count).toBeGreaterThanOrEqual(1);
                (await dbco()).close();
            });	
            ```
            - [x] Résolution de l'erreur: ER_ACCESS_DENIED_ERROR: Access denied for user ''@'172.18.0.1' (using password: YES) : changement du paramètre user en username dans le ormconfig.json. Retour d'erreur : 
            ```
            RepositoryNotFoundError: No repository for "Piece" was found. Looks like this entity is not registered in current "default" connection?   
            ```  
        - [x] Tester la connexion à la base
            - [x] Résoudre le problème de global.db = undefined.
            - [x] Création des tests de connexion en important dbco.ts comme suit : 
            ```
            describe('Connection to db', () => {
                let dbconnection: Promise<Connection>;
                let db: Connection;

                beforeAll(async () => {
                    dbconnection = dbco();
                    db = (await dbconnection);
                })

                jest.setTimeout(10000);
                it('should be defined', async () => {
                    expect(dbconnection).toBeDefined();
                });	

                it('should return a connexion object', async () => {
                    expect(db).toBeInstanceOf(Connection);
                });	

                afterAll(async () => {
                    (await dbconnection).close();
                })
            });
            ```
        - [x] Dégager jest-mysql pour autre chose ? 
            - [x] Ecriture des tests de connexion avec typeOrm en définissant un ormconfig.json
            - [x] Penser à réécrire le ormconfig.json pour chaque nouvel environnement
            - [x] Résolution d'erreur : 
            ```
            RepositoryNotFoundError: No repository for "Piece" was found. Looks like this entity is not registered in current "default" connection?   
            ```  
            en intégrant la ligne :
            ```
            ,
            "entities": [
            "./src/**/entities/*.ts"
            ]
            ```
            dans le fichier ormconfig.json . 
        - [x] Trouver pourquoi le retour de ```db.getRepository(Piece).count();``` est une  Promise { < pending> }   et non un number -> manquait un await devant . Modification du test pour tester le retour du nombre d'instance du repo : ```expect(typeof value).toBe('number')```
        - [x] Création du fichier de test de ReportsService car non existant atm
    - [x] Màj du jira
  
  
  **26 Octobre**
    - [x] MàJs Windows
    - [ ] Étude de la façon de tester le crud en bdd
        - [x] Check de (https://dev.to/walrusai/testing-database-interactions-with-jest-519n) & (https://deviq.com/design-patterns/repository-pattern)
    - [ ] Tester le crud pour l'entité pieces
        - [ ] Création des tests de mock 
            - [x] Création du mock pour l'Update
    - [ ] S'assurer que les données arrivent en base  
    - [ ] Survol des fichiers de test de Reports
    - [ ] Appliquer les tests à l'entité Reports
    - [ ] Tester la persistance des données
    - [ ] Tests fonctionnels
    - [ ] Tests e2e
