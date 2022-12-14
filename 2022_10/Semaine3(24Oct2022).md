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
        - [x] Création des tests de mock 
            - [x] Création du mock pour le Create
            - [x] Création du mock pour le Read
            - [x] Création du mock pour l'Update
            - [x] Création du mock pour le Delete
            - [x] Déplacement de tous les mocks dans le fichier pieces.service.spec.ts car c'est le service qui interagit avec la db et non le controller.
        - [ ] Création des tests réels
            - [x] Imbrication dans un describe avec beforeAll() qui initialise la co à la db et un afterAll() qui la ferme 
            - [ ] Création du test pour le Create
                - [x] Résolution du problème : AlreadyHasActiveConnectionError: Cannot create a new connection named "default", because connection with such name already exist and it now has an active connection session. -> lancer une connexion dans plusieurs describe au sein du même fichier cause le problème ; tout regrouper sous le même describe.
                - [x] Solution alternative, ajouter le flag ```--runInBand``` pour effectuer les tests non pas en parallèle mais séparément. ! Ralentit grandement l'exécution de la suite de tests
                - [ ] Résolution de l'erreur : ReferenceError: You are trying to `import` a file after the Jest environment has been torn down
                    - [x] Check de (https://stackoverflow.com/questions/50793885/referenceerror-you-are-trying-to-import-a-file-after-the-jest-environment-has)
                    - [x] Ajout de : 
                    ```
		            jest.setTimeout(10000);
                    ```
                    dans les BeforeAll() & AfterAll() pour voir. Ne semble pas résoudre quoi que ce soit
            - [x] Création du test pour le Read
                - [x] Avec la connexion à la db, read le repo Piece et findOne(id: "").toBeTruthy() . Le console log du retour renvoie bien l'objet piece inscrit en base
            - [ ] Création du test pour l'Update
            - [ ] Création du test pour le Delete
    - [x] Tester la persistance des données

  
  **27 Octobre**
- [ ] Tester le crud pour l'entité pieces
    - [ ] Création des tests réels
        - [x] Création du test pour le Create
            - [x] Résolution de l'erreur : ReferenceError: You are trying to `import` a file after the Jest environment has been torn down 
                - [x] Le console log de create(rempli avec infos) renvoie : Promise { < pending> }
                - [x] Ajouter await renvoie : 
                ```
                 Piece {
                    name: 'testpiecename',
                    reference: 'reftest',
                    quantity: 5,
                    id: '36117477-bf6f-4f1a-9d4b-35625ef6a9be'
                }
                ``` 
                - [x] Ajout du await devant le create résoud le problème
        - [ ] Création du test pour l'Update
            - [ ] Écriture du test en cours
                - [ ] Résolution de l'erreur :
                ```
                HttpException: ER_TRUNCATED_WRONG_VALUE: Truncated incorrect DOUBLE value: 'piece1'       
                ```
                - [x] Déplacement du sujet de test de name à id à ref. Ne semble pas changer quoi que ce soit
                - [x] Mise en comm et passage au delete
        - [ ] Création du test pour le Delete
            - [x] Résolution de l'erreur :
            ```                                 
            HttpException: Pool is closed.                                                                                             
                at C:\Users\gchevalier\cleankmo\KMO_WEB\kmo-back\test\dbco.ts:92:19                                                                                                                                  
                at processTicksAndRejections (node:internal/process/task_queues:96:5) {
            response: { message: 'Pool is closed.' },
            status: 400
            }  
            ```
            - [ ] L'erreur vient de la fonction .delete() qui ne supprime pas l'instance de pieces. essayer d'appeler ```Repository< Piece>.delete()``` plutôt que delete() simple ?
    - [x] S'assurer que les données arrivent en base  
- [x] Formation avec Théo sur la maintenance des versions tablettes KMO, & android studio
    - [x] Se positionner dans ./kmo-app et lancer : 
    ```
    npm i
    npx cap add android
    npm i -g cordova-res
    cordova-res android --skip-config --copy
    npm run build --prod
    ionic cap build
    ionic cap sync
    ```
    - [x] Résolution de l'erreur : 
    ```
    [error] Could not find the web assets directory: .\www.
    ```
    en faisant npm run build
    - [x] Android studio
        - [x] Ouverture du projet : C:\Users\gchevalier\cleankmo\KMO_WEB\kmo-app\android
        - [x] Installation d'Atom material icons (settings)
        - [x] Configuration de firebase
            - [x] Dans android studio, (project/android/app) créer le fichier google-services.json et le remplir avec les données présentes dans kmo-app/google-services.json
            - [x] Ajout des lignes ```mavenCentral()``` à : repositories
            - [x] Dans android studio, (project/android/build.gradle) &(project/android/app/build.gradle) ajouter les lignes renseignées dans le readme sur github
            - [x] Dans le fichier android\app\src\main\java\io\cop\kmo\MainActivity.java, ajouter le code renseigné dans le github
            - [x] Dans l'ide android, cliquer sur SyncNow
            - [x] Synchro avec tablette : menu déroulant du haut, pair deviceover wifi / usb selon la methode
                - [x] Activer les options de dev sur le device : spammer reglages/a propos du device/ infos logiciel/ numéro de version
                - [x] Aller dans les options de développement et scanner le qrcode
        - [x] Au build de l'app, résoudre à la main les erreurs dans les fichiers ImagePicker & MultiImageChoserActivity : Virer les déclarations d'import qui posent problème, et se positionner sur le code qui génère les erreurs et appliquer les quick fix suggérer (import & extend)
        - [x] Build successful, rentrer sur la tablette avec mdp 1739
        - [x] Ne pas oublier de cliquer sur syncNow pour apply les changements
        - [x] Config de l'icone de l'app (https://developer.android.com/studio/write/image-asset-studio), clic droit res/new/image asset, renseigner le path et next. Puis sync et run app
    - [x] Installation de l'app-tester
        - [x] Récupérer le fichier app-tester.apk
        - [x] Dans l'explorateur de fichiers, copier le fichier et le coller dans Ce PC\Galaxy Tab A\Tablet\Download
        - [x] Sur la tablette, dans mes fichiers/app-tester.apk installer
    - [x] Ouvrir l'app-tester, puis se connecter avec : Compte gmail monteur : monteur.cop@gmail.com/COPkmo67. D'où on peut choisir la version de l'appli à mettre au monteur
- [ ] Survol des fichiers de test de Reports
- [ ] Appliquer les tests à l'entité Reports
- [ ] Tests fonctionnels
- [ ] Tests e2e
  
  
**28 Octobre**
- [x] Tester le crud pour l'entité pieces
    - [x] Création des tests réels
        - [x] Création du test pour le Delete
            - [x] Résolution de l'erreur :
            ```                                 
            HttpException: Pool is closed.                                                                                             
                at C:\Users\gchevalier\cleankmo\KMO_WEB\kmo-back\test\dbco.ts:92:19                                                                               
                at processTicksAndRejections (node:internal/process/task_queues:96:5) {
            response: { message: 'Pool is closed.' },
            status: 400
            }  
            ```
            - [x] L'erreur vient de la fonction .delete() qui ne supprime pas l'instance de pieces. essayer d'appeler ```Repository< Piece>.delete()``` plutôt que delete() simple ?
                - [x] Import forcé de la fonction delete de kmo-back\node_modules\typeorm\repository\Repository.d.ts avec : 
                ```
                import { Repository } from 'typeorm';
                ```
                - [x] Déclarer this. ... is possibly undefined et créée des erreurs. 
                - [x] Renseignement de CreatePieceDto dans le delete et utilisation du save()
                - [x] La suppression de l'instance en base se fait bien, mais retour d'erreur : ```HttpException: ER_NO_DEFAULT_FOR_FIELD``` dans la console. Check de (https://stackoverflow.com/questions/29068463/er-no-default-for-field-in-mysql-nodejs-rest-request) & (https://stackoverflow.com/questions/68722371/typeorm-evokes-error-in-findone-query-queryfailederror-er-no-default-for-fiel)
                - [x] Résolution : enlever le await du 
                ```
                pieceRepository.save(deletePiece).catch
                ```
                dans la boucle if de delete dans le fichier dcbo permet de se débarrasser de l'erreur no_default_field
            - [x] Le test passe maintenant que j'ai déclaré
            ```
            const deleted = await db.getRepository(Piece).findOne({name: "test2"});
            ```
            *après* l'appel à la fonction delete123() de dcbo.
        - [x] Création du test pour l'Update
            - [x] Reprise de l'écriture du test
                - [x] Résolution de l'erreur :
                ```
                HttpException: ER_TRUNCATED_WRONG_VALUE: Truncated incorrect DOUBLE value: 'piece1'       
                ```
                - [x] Renseignement de CreatePieceDto dans le update et utilisation du save()
                - [x] La requête semble bonne, trouver pourquoi elle ne s'applique pas : manquait ```.execute();``` à la fin de la modification.
                - [x] La modification de l'instance en base se fait bien, mais retour d'erreur : ```HttpException: ER_NO_DEFAULT_FOR_FIELD``` dans la console
                - [x] Mise en commentaire de la gestion d'erreur : 
                ```
                throw new HttpException(
                    {
                        message: err.message,
                    },
                    HttpStatus.BAD_REQUEST
                );
                ```
                pour les methods update() & delete123() de dbco.ts
- [x] Mise à jour du jira
- [ ] Voir comment s'organisent les relations via les methodes de CRUD pour les entités
    - [x] Check du fichier create-piece.dto.ts utilisé dans dbco.ts
        - [x] Check de pieceVehicle.entity.ts pour voir le type de relations utilisées avec les autres entités
- [ ] Voir comment s'organisent les relations entre les Reports et les entités reliées
- [ ] Survol des fichiers de test de Reports
- [ ] Appliquer les tests à l'entité Reports
- [ ] Tests fonctionnels
- [ ] Tests e2e
- [x] Réunion followup