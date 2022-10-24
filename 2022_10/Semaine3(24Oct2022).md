*** LOG Gregoire Chevalier ***

**24 Octobre**
- [x] Redémarrage et application des màjs windows/docker vscode
- [ ] Testing du projet
    - [ ] Tests unitaires
        - [ ] Résolution de tous les problèmes de ```Nest can't resolve dependencies of...```
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
    - [ ] Installer dans kmo-back (working dir) jest-mysql
        - [ ] Configurer proprement le tout
        - [ ] Accéder à global.db  
        - [ ] Tester la connexion à la base
            - [ ] Résoudre le problème de global.db = undefined.
            ```
            Make sure jest and mysql are installed as well in the project, as they are required as peer dependencies.
            ```
            -> installation de mysql
            -> reinstallation de jest-mysql
        - [ ] Tester la persistance des données
    - [ ] Appliquer les tests à l'entité Reports
    - [ ] Tests fonctionnels
    - [ ] Tests e2e
    - [ ] S'assurer que les données arrivent en base

