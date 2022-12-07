**5 Décembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
- [ ] Résolution du problème de mail pro J.Ivanoff
    - [x] Mail envoyé sur adresse perso
- [ ] Démarche obtention contrat 4G Orange 
    - [ ] Garder un oeil sur la gestion d'équipement de test
- [ ] Résolution des erreurs post update docker
    - [x] Processus de rebuild du docker : 
        ```
        docker volume prune    
        ``` 
        Pour suppression volume & clean de l'espace associé
        ```
        docker compose up --build
        ```
        Pour rebuild le docker à jour, à faire aussi souvent qu'on change l'application
    - [ ] Automatisation de la création de 2 stores si la db est vide
        - [x] Retour de sytnhèse de fail de test update crud report : 
        ```
        -> besoin de créer 2 instances de *store* avec id  1 & 2 pour faire passer les tests. Voir pour automatiser si non créés ?
        ```
        - [x] Création de 2 stores aux id 1 & 2 lors du test C du crud report
            - [x] Création de la function createStore
            - [x] Création de 2 stores test
            - [x] Écriture des tests correspondants
            - [x] Résolution de l'erreur : 
            ```
            Cannot find alias for relation at store
            ```
            Check de (https://github.com/typeorm/typeorm/issues/8310)
            - [x] Comportement observé :
                - au 1er docker build up, le reports.service.spec.ts fail car report n'est pas trouvé, dans la timeline, les 2 stores ne sont pas encore en base et donc l'update du report ne se fait pas.
                - au 2e docker build up, le reports.service.spec.ts renvoie Cannot find alias for relation at store, & Jest has detected the following 1 open handle potentially keeping Jest from exiting: ●  TCPWRAP
            - [x] Résolution du TCPWRAP en ajoutant un afterAll() et un close ligne 653 : 
            ```
            afterAll(async () => {
			    db.close();
		    });
            ```
            - [x] Résolution du fail récurrent sur l'update : changement de la propriété à update de store à reference
            - [x] Résolution de l'erreur :
            ```
            TypeORMError: Empty criteria(s) are not allowed for the delete method.
            ```
            la première fois *uniquement* que les tests sont lancés après l'initialisation du docker.  
            Récap de l'erreur : le test ne fail que la première fois après avoir build le docker. En plus, le delete du rapport se fait via son ticket, et le code est correct, donc osef.
    - [ ] Résolution de l'erreur :
    ```
    Nest can't resolve dependencies of the UsersService (UserRepository, JwtService, ?). Please make sure that the argument MailerService at index [2] is available in the UsersModule context.
    ``` 
- [ ] Faire une passe commentaire descriptif utile et clair du code
    - [ ] Supprimer les commentaires de dev inutiles
    - [ ] Checker les coventions jsdoc
        - [ ] Voir (https://jsdoc.app/index.html, https://jsdoc.app/tags-returns.html)
    - [ ] Définir dans un document commun avec Théo & Anthony la manière de commenter 
    - [ ] Commenter le code

  
**6 Décembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
- [x] Résolution des erreurs post update docker
    - [x] e2e
        - [x] copie du fichier et suppression pour reprise des e2e from scratch
        - [x] Reprise de documentation
        - [x] Refonte du fichier
            - [x] Suppression de tout l'intérieur du describe sauf les declarations d'INestApp, de ReportService & de co
            - [x] Suppression de l'import de MailerService dans les imports du fichier users.module.ts (ce qui crée une erreur au build du docker)
            - [x] Réécriture des tests de base : 
            ```
            it('should be defined (app)', () => {
                expect(app).toBeDefined();
            });

            it('should be defined (service)', () => {
                expect(service).toBeDefined();
            });
            
            it(`should refuse connection to endpoint using /GET due to user not being authentified`, async () => {
                return request(app.getHttpServer()).get('/reports').expect(401);
            });
            ```
            - [x] Résolution de l'erreur : 
            ```
            Nest can't resolve dependencies of the StoreRepository (?). Please make sure that the argument Connection at index [0] is available in the TypeOrmModule context.
            ```
            Quand ajout de TypeOrmModule.forRoot(co) aux imports, on retrouve l'erreur  
            ```
            Nest can't resolve dependencies of the UsersService (UserRepository, JwtService, ?). Please make sure that the argument MailerService at index [2] is available in the UsersModule context.
            ```
            - [x] Résolution de l'erreur MailerService : ajout de MailerService dans les *providers* de users.module.ts qui renvoie à l'erreur : 
            - [x] Résolution de l'erreur : 
            ```
            Nest can't resolve dependencies of the MailerService (?, MAILER_TRANSPORT_FACTORY). Please make sure that the argument MAILER_OPTIONS at index [0] is available in the UsersModule context.
            ```
            l'ajout de :
            ```
            MailerService,
                {
                    provide: MAILER_OPTIONS,
                    useValue: {
                        message: {
                            from: process.env.MAIL_USER,
                        },
                        transport: {
                            host: process.env.MAIL_HOST,
                            port: process.env.MAIL_PORT,
                            secure: process.env.MAIL_SECURE,
                            auth: {
                                user: process.env.MAIL_USER,
                                pass: process.env.MAIL_PASS,
                            },
                        },
			    }
            ```
            dans les providers: [] résout le problème et renvoie l'erreur suivante :
            - [x] Résolution de l'erreur : 
            ```
            Nest can't resolve dependencies of the AdminJwtAuthGuard (Reflector, ?). Please make sure that the argument AuthService at index [1] is available in the UsersModule context.
            ```
            fix en ajoutant authservice aux providers de users.module.ts
            - [x] Rebuild du docker pour voir si les modifications ne perturbent pas le build up
                - [x] Ok, mais phases 1/6 && 4/6 substantiellement plus longues (130s)
            - [x] Résolution de l'erreur : 
            ```
            [Nest] 44648  - 06/12/2022 10:41:35   ERROR [ExceptionsHandler] Unknown authentication strategy "jwt"
            Error: Unknown authentication strategy "jwt"
            ```
            check infructueux dans le log perso
                - [x] Check de (https://stackoverflow.com/questions/60405308/nestjs-passport-jwt-unknown-strategy) & (https://github.com/nestjs/nest/issues/1868)*
            Ajout de AuthModule dans les imports du e2e et le test de 401 passe.
            Ajout du code pour tester le comportement de la route /reports. Test fonctionnel mais amène l'erreur suivante : 
            - [x] Résolution de l'erreur : 
            ```
            ReferenceError: You are trying to `import` a file after the Jest environment has been torn down. From reports/reports.service.spec.ts.
            ```
            Mise en commentaire de la ligne 102 du fichier users.service.spec.ts
        - [x] Comportement des tests atm :
            - au 1er build docker, 2 tests FAIL
            - pour tous les autres itérations de tests, 100% de PASS
    - [x] Update du readme avec comportement connu/attendu
- [x] Push, merge develop
- [x] Pull, & installation & lancement des tests sur la machine d'Anthony : effective matched expected.
- [ ] Faire une passe commentaire descriptif utile et clair du code
    - [ ] Supprimer les commentaires de dev inutiles
        - [x] Window-mocks.ts : suppression ligne blanche & rectification commentaire informatif
        - [x] app.Module.ts : suppression du code mort :RouterModule.forRoot([], {enableTracing: true}), && suppression des imports non utilisés
        - [x] report.service.spec.ts : suppression du code mort (12 lignes) && note ultérieure pour ɵisObservable && clean des imports non utilisés
        - [x] authentification.component.ts : suppression  des imports non utilisés
        - [x] regional-management.component.ts : suppression du code mort 
        - [x] report.component.spec.ts : suppression du code mort & consoles log (24 lignes) && clean des imports non utilisés
        - [x] report.component.ts : suppression du code mort (4 lignes)
        - [x] report-cop-view.component.spec.ts : suppression du code mort & consoles log (6 lignes) && clean des imports non utilisés
        - [x] report-cop-view.component.ts : suppression du code mort & consoles log  && clean des imports non utilisés
        - [x] aldi-report.component.ts : suppression du code mort 
        - [x] aldi-report.component.spec.ts : suppression du code mort 
        - [x] lidl-report.component.ts : suppression du code mort 
        - [x] lidl-report.component.spec.ts : suppression du code mort
        - [x] edit-ticket.component.spec.ts : suppression du code mort 
        - [x] filter-store.pipe.ts : suppression du code mort & consoles log  && clean des imports non utilisés
    - [ ] Checker les coventions jsdoc
        - [ ] Voir (https://jsdoc.app/index.html, https://jsdoc.app/tags-returns.html)
    - [ ] Définir dans un document commun avec Théo & Anthony la manière de commenter 
    - [ ] Commenter le code 
- [x] Veille polyfill (https://developer.mozilla.org/en-US/docs/Glossary/Polyfill)


  
**7 Décembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
- [ ] Faire une passe commentaire descriptif utile et clair du code
    - [ ] Commenter le code 
    - [ ] Supprimer les commentaires de dev inutiles
        - [x] jwt.interceptor.ts : suppression du code mort
        - [x] jest-mysql-config.js : suppression du code mort
        - [x] pieces.controller.spec.ts : suppression du code mort
        - [x] reports.controller.spec.ts : suppression de la fonction mock commentée :
        ```
        describe("Report.controller functions mocks", () => {
            it('should call the create() function', async function () {
                // const create = 
                jest.spyOn(controller, 'create').mockReturnValue(Promise<Report>);
                controller.create();
                // const dto = 
                // const result = await controller.create();
                expect(controller.create).toHaveBeenCalled();
            });
        });
        ```
        - [x] reports.e2e-spec.ts : suppression du code mort && console logs
        - [x] reports.module.ts : suppression du code mort 
        - [x] reports.service.spec.ts : suppression du code mock service && suppression des imports non utilisés && des console logs
        ```
        // const spy = jest.spyOn(service, 'create');
        // const rSpy = await service.create({
        // 	reference: "1",
        ...
        ...
        // 	ticket: undefined //targetTicket
        // 	});
        // expect(spy).toBeCalled();
        ```
        - [x] tickets.controller.spec.ts : suppression du code mort && suppression des imports non utilisés 
        - [x] tickets.service.spec.ts : suppression des imports non utilisés 
        - [x] users.service.spec.ts : suppression de console log
        - [x] users.service.ts : suppression du code mort && suppression des imports non utilisés. Gardé en commentaire la throw error not found qui fait fail les tests au démarrage du docker
        - [x] vehicles.controller.spec.ts : suppression du code mort && suppression des imports non utilisés 
        - [x] dbco.spec.ts : suppression du code mort : 
        ```
        // it('should return a positive number', async () => {
        // 	const connection = dbco();
        // 	const count = (await connection).getRepository(Piece).count();
        // 	console.log(count);
        // 	expect(count).toBeGreaterThanOrEqual(1);
        // 	(await dbco()).close();
        // });

        // it('should return a positive number', async () => {
        // 	jest.setTimeout(10000);
        // 	const dbco = async () => {
        // 		const dbOptions = await getConnectionOptions(process.env.NODE_ENV);
        // 		console.log(getConnectionOptions());
        // 		return createConnection({...dbOptions});
        // 	}
        // 	const count = (await dbco()).getRepository(Piece).count();
        // 	console.log(count);
        // 	expect(count).toBeGreaterThanOrEqual(1);
        // 	(await dbco()).close();
        // });
        ```
    - [ ] Checker les coventions jsdoc
        - [ ] Voir (https://jsdoc.app/index.html, https://jsdoc.app/tags-returns.html)
    - [ ] Définir dans un document commun avec Théo & Anthony la manière de commenter 
- [x] Reboot et install màjs windows
- [x] Prise en main du serveur local
    - [x] Accès via l'explorer de fichier avec : H:\02_PROJETS\
    - [x] Stockage des éléments relatifs au suivi des tablettes dans : H:\02_PROJETS\01_KMO\60_SOFTWARE\00_WEB\Suivi tablettes
    - [x] Mettre à jour la tache correspondant à la gestion du parc tablettes sur jira
        - [x] Création des sous-tâches et remplissage du log
- [ ] Prise en main de la tâche KW-293 mailing service
    - [ ] Survol du code existant
    - [ ] Check des variables
    - [ ] Documentation
    - [x] Récupération du fichier smtp.old (enregistré dans Téléchargements)

- [ ] Parler avec Guillaume de la structure/architecture portail saas (keycloak) des projets pour uniformisation & harmonisation des projets en cours & à venir
- [ ] Penser à donner un justificatif de nouveau domicile à hubert