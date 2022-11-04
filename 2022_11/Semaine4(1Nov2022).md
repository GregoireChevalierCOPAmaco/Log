*** LOG Gregoire Chevalier ***

**31 Octobre**
- [x] Cleanup des fichiers de test
    - [x] Suppression des commentaires
    - [x] Suppression des console.log()
    - [x] Run des tests pour s'assurer de leur passage
- [x] Push sur KM-297_testsGreg
    - [x] Check que je suis sur la bonne branche en local
    - [x] Commit 
        - [x] Résolution d'erreur de code liées au linting
    - [x] Push
    - [x] Merge
        - [x] Pour note : l'env a été changé pour l'url de prod pour les tablettes :
        ```
        export const environment = {
            production: true,
           - apiUrl: 'http://3.126.39.182:3000/',
           + apiUrl: 'http://18.197.225.248:3000/',
        };
        ```
- [x] Voir comment s'organisent les relations via les methodes de CRUD pour les entités
    - [x] Les relations sont gérées par les décorators @Unique, @ManyToOne, etc ... dans les fichiers entités. **Logiquement, si les services & controllers sont testés, leurs relations sont viables ?**
    - [x] Check de (https://dkzeb.medium.com/unit-testing-in-ts-jest-with-typeorm-entities-ad5de5f95438) & (https://stackoverflow.com/questions/70092175/mocking-relationships-in-typeorm-nestjs)
- [x] Voir comment s'organisent les relations entre les Reports et les entités reliées
    - [x] Check de KMO_WEB\kmo-back\src\reports\entities\report.entity.ts
    - [x] Check de (https://www.valentinog.com/blog/jest-coverage/) sur le code coverage
- [x] Reprise du testing
- [x] Survol des fichiers de test de Reports
- [ ] Appliquer les tests à l'entité Reports
    - [x] Mock du CRUD
    - [x] Test du Create
    - [ ] Test de l'envoi du mail lors du create
    - [x] Test du Read
    - [ ] Test de l'Update
        - [ ] Résolution des erreurs créées
    - [ ] Test du Delete
        - [ ] Test du  CRUD avec relation à un store, un ticket
- [ ] Tests e2e
- [ ] Penser à donner un justificatif de nouveau domicile à hubert
  
  
**2 Novembre**
- [x] Reprise du testing 
- [ ] Appliquer les tests à l'entité Reports 
    - [x] Test du Read
    - [x] Test de l'Update
        - [x] Résolution des erreurs créées
            - [x] Résolution de : 
            ```
            No overload matches this call.
            Overload 1 of 4, '(entities: { id?: string; reference?: string; clipperRef?: string; date?: ........... }
        - [x] Console log de report & updateReport. Retour de log :
        ```
        undefined
        at update (reports/reports.service.spec.ts:387:12)

        UpdateResult { generatedMaps: [], raw: [], affected: 2 }
        ```
        - [x] Récupérer le report au lieu d'un undefined
        at update (reports/reports.service.spec.ts:388:12) : 
        changement du paramètre fourni dans le findOne(), passage de new report reference à report reference. Cela retourne le report
- [x] Redémarrage pour résolution des problèmes de push du log
- [x] Passage sur le log sous windows != wsl
- [x] git pull & màj
    - [x] Test du Delete
    - [ ] Répondre à la question : est-ce normal de pouvoir créer des reports de même noms ? -> unique sur le champ reference ?
    - [ ] Test de l'envoi du mail lors du create
        - [x] Check de (https://www.npmjs.com/package/jest-smtp) & (https://github.com/tglink/jest-email-reporter)
        - [x] Survol des fonctions relevant de l'envoi de mail 
        - [x] Check des fichiers relevant de mailer.service.ts
        - [x] En gros : si l'objet createReportDto == true dans la requête de création, on passe dans la boucle d'envoi de mail :
        ```
        const store = await this.StoreRepository.findOne(createReportDto.store);
			this.mailerService
				.sendMail({
					to: 'g.chevalier@cop-amaco.com',
					from: 'copdevbot@gmail.com',
					subject:
						'Le rapport ' +
						createReportDto.reference +
						" à besoin d'un second rendez-vous",
					html:
						'<h1>Bonjour</h1>\n' +
						'<p>Le rapport </strong>' +
						createReportDto.reference +
                        .....
						createReportDto.observationProblem,
				})
				.then((success) => {
					console.log(success);
				})
				.catch((err) => {
					console.log(err);
				});
        ```
        - [x] Résolution de l'erreur : 
            ```
            TypeError: Cannot read properties of undefined (reading 'sendMail')
            ```
            - [x] Check de (https://stackoverflow.com/questions/70737323/jest-error-typeerror-cannot-read-properties-of-undefined-reading-send), (https://salesforce.stackexchange.com/questions/372425/uncaught-in-promise-typeerror-cannot-read-properties-of-undefined-reading-b) & (https://github.com/nest-modules/mailer/blob/master/lib/mailer.service.spec.ts)
            - [x] Uilisation des tests en snapshot comme renseigné dans (https://github.com/nest-modules/mailer/blob/master/lib/mailer.service.spec.ts) 
            - [x] Check de (https://jestjs.io/docs/snapshot-testing) pour référence
        - [x] Mail service mock-testé
        - [ ] Tester en réel le mailing ? Pas sûr que ce soit nécessaire...
    - [ ] Test du  CRUD avec relation à un store, un ticket
        - [x] Création d'un store
        - [x] Création d'un ticket
        - [x] Création des liens report-ticket-store
            - [x] Le lien se fait entre report & store mais pas entre report & ticket... -> insérer :
            ```
            @ManyToOne(() => Ticket, (ticket) => ticket.id, { onDelete: 'CASCADE' })
	        ticket: Ticket;
            ```
            au create-report.dto.ts ??? ou voir s'il est logique ou non d'assigner le ticket à la création du report.... ?
            - [x] Insertion du ticket comme many to one dans le CreateReportDTO
        - [x] Test du Create
            - [x] Lien avec store
            - [x] Lien avec ticket
        - [x] Test du Read
            - [x] Lien avec store
            - [x] Lien avec ticket
        - [ ] Test de l'Update
        - [x] Test du Delete
            - [x] Lien avec store
            - [x] Lien avec ticket
            - [x] Fonctionnel même si peu de sens à delete en itérant sur le ticket associé
    - [ ] Essais d'envoi de mail à la création du report
        - [ ] Résolution de l'erreur : 
        ```
        Cannot query across one-to-many for property checkout
        ```
        - [x] Check de (https://stackoverflow.com/questions/69471840/cannot-query-across-one-to-many-for-property-nestjs-and-typeorm)
        - [x] Console log du target store pour voir si c'est lui qui fout la merde. En effet c'est de là que vient la propriété checkout :
        ```
        Store {
        id: '1',
        idStore: '42',
        brand: 'MonMagasin',
        address: "12 rue de l'industrie des cornemuses",
        zipCode: 67123,
        city: 'Villageheim',
        phoneNumber: '0123456789',
        checkout: [],
        report: [
          Report {
            id: '1efa01f9-035d-4e07-bf79-62562e991dba',
            reference: 'report test magasin ticket 1',
        ```
        -> solution : changer le parametre du findOne de :
        ```
        .findOne(createReportDto.store);
        ```
        à
        ```
        .findOne({id: "2"});
        ```
        - [x] Résolution de l'erreur consécutive :
        TypeError: Cannot read properties of undefined (reading 'mailerService')  -> dûe au this ? -> solution : remplacer le this par un new Mailer() auquel on assigne les options et transport factory
        - [ ] Résolution de l'erreur consécutive :
        ```
        Type '"MAILER_OPTIONS"' has no properties in common with type 'MailerOptions'.
        ```
        - [x] Check des fichiers KMO_WEB\kmo-back\node_modules\@nestjs-modules\mailer\dist\mailer.service.d.ts & KMO_WEB\kmo-back\node_modules\@nestjs-modules\mailer\dist\interfaces\mailer-options.interface.d.ts
- [ ] Tests e2e
- [ ] Penser à donner un justificatif de nouveau domicile à hubert
  
  
**3 Novembre**
- [x] Répondre à la question : est-ce normal de pouvoir créer des reports de même noms ? -> unique sur le champ reference ? : pas la peine car les reports sont liés au ticket associé et sont différenciés par l'id
- [x] Appliquer les tests à l'entité Reports 
    - [x] Test du  CRUD avec relation à un store, un ticket
        - [x] Test de l'Update
            - [x] Update d'un ticket à un autre
                - [x] Résolution des problèmes liés au non update des paramètres de la fonction async update()
                - [x] Tests ok
            - [x] Update d'un store à un autre
- [x] Exercice alarme incendie : Important de noter qu'il doit être réalisé de bout en bout pour satisfaire les exigences des commanditaires tels que decathlon etc...
- [ ] Test de l'envoi du mail lors du create
    - [ ] Essais d'envoi de mail à la création du report
        - [ ] Résolution de l'erreur consécutive :
        TypeError: Cannot read properties of undefined (reading 'mailerService')  -> dûe au this ? à voir
        - [x] Check de (https://notiz.dev/blog/send-emails-with-nestjs) & (https://nest-modules.github.io/mailer/docs/mailer#configuration)
        - [x] Check des variables d'environnement
        - [x] déclaration du service ```let service: MailerService;``` *dans* le describe concerné pour faire appel à la fonction sendMail() 
        - [x] Mise en comms et mise de coté du test de mail réel
- [ ] Tests e2e
    - [x] Reprise de documentation : (https://docs.nestjs.com/fundamentals/testing#end-to-end-testing)
    - [x] Check de (https://github.com/visionmedia/supertest), module supertest utilisé nativement dans les e2e nest pour simuler les requetes http
    - [x] Check de (https://docs.nestjs.com/fundamentals/testing#overriding-globally-registered-enhancers) pour contournement du/des guard
    - [x] Création du fichier reports.e2e.spec.ts et remplissage du beforeAll dans le describe()
    - [x] Accès au dossier COP-SAV et npm install
    - [x] ng serve et accès au front
    - [ ] Premier test : it(/GET login)
        - [ ] Résolution des erreurs de gestion de dépendance  
            - [x] erreur : 
            ```
            Nest can't resolve dependencies of the StoreRepository (?). Please make sure that the argument Connection at index [0] is available in the TypeOrmModule context.
            ``` 
            -> Déclarer Connection dans un provider: 
            renvoie l'erreur suivante :
            ```
            TypeError: Cannot read properties of undefined (reading 'name')
            ```
            Voir lien avec KMO_WEB\kmo-back\node_modules\typeorm\connection\Connection.d.ts
  

**4 Novembre**
- [ ] Tests e2e
    - [ ] Premier test : it(/GET login`)  
        - [x] Résolution des erreurs de gestion de dépendance  
            - [x] Déclaration de Connection dans un provider
            - [x] Reprise de doc (https://docs.nestjs.com/fundamentals/custom-providers) et check de (https://stackoverflow.com/questions/69000993/nestjs-overrideprovider-vs-provider-in-unit-testing) & (https://firxworx.com/blog/coding/nestjs-integration-and-e2e-tests-with-typeorm-postgres-and-jwt/)
            - [ ] Faut-il renseigner tous les paramètres de l'objet Connction déclaré en provider ou ai-je ommis quelque chose dans la configuration ?
            - [x] Survol du fichier App.module.ts
            - [x] Renseignement de la connexion en dur dans l'import : 
            ```
            TypeOrmModule.forRoot(
                {
                    "type": "mysql",
                    "host": "localhost",
                    "port": 3306,
                    "username": "KMO",
                    "password": "KMO15022022",
                    "database": "KMO"
                }
            )
            ```  
            - [x] Résolution des problèmes de dépendances plus familiers  
            - [ ] Trouver pourquoi renseigner AuhtService dans les providers ne résout pas :   
            ```
            Nest can't resolve dependencies of the TechnicianJwtGuard (Reflector, ?). Please make sure that the argument AuthService at index [1] is available in the ReportsModule context
            ```  
            - [x] Ajout dans le report.modules.ts des services nécessaires à la résolution des problemes de dépendances ; UserService, AuthService,
            JwtService, {getRepoToken(User)}, MailerService et conf asssociée
- [x] Réunion de fin de semaine
- [x] Reprise de la construction de test unitaire
    - [x] Check de (https://stackoverflow.com/questions/45989574/repositorynotfounderror-typeorm) & (https://github.com/typeorm/typeorm/issues/3017)
        - [x] Voir si la structure du test e2e est correcte ?
            - [x] Lecture de doc : (https://www.synbioz.com/blog/tech/tests-e2e-avec-jest-et-puppeteer), (https://blog.logrocket.com/end-end-testing-nestjs-typeorm/#what-end-to-end-tests), & (https://medium.com/@exfabrica/nestjs-unit-and-e2e-tests-with-jest-825ba5033c6)
        - [x] Comme indiqué dans la doc, ```Keep your e2e test files inside the test directory. The testing files should have a .e2e-spec suffix.```
        - [x] Essai de run du test natif App.e2e-spec.ts : No tests found, exiting with code 1
            - [x] Check de (https://stackoverflow.com/questions/54493998/do-not-pass-e2e-tests-in-framework-nestjs), ()
            - [x] Trouver pourquoi jest ne lance pas les tests e2e-spec.ts : -> Résolution en incluant :
            ```
            "testRegex": [
			".*\\.spec\\.ts$",
			".e2e-spec.ts$"  <---------
		    ]
            ```
            dans la config jest du package.json
        - [x] Nouvelle erreur :  
        ```
        RepositoryNotFoundError: No repository for "Report" was found. Looks like this entity is not registered in current "default" connection?
        ```  
         -> Résolution en incluant :
        ```
        "entities": ["./src/**/entities/*.ts"]
        ```
        dans la variable co en dur. Le test est maintenant exécuté et renvoie :
        ```
        expected 200 "OK", got 404 "Not Found"
        ```
        - [ ] Ré-écriture du test/des variables/methodes pour accéder à la connexion au kmo
- [ ] Tests e2e
    - [ ] Tester le front
        - [ ] Tester le formulaire
        - [ ] Tester la réception des données
        - [ ] Tester les données retournées
        - [ ] Tester l’envoi des données retournées au back
    - [ ] Tester le back
        - [ ] Tester la bonne réception des données
        - [ ] Tester la connexion à la db
        - [ ] Tester la bonne forme des données à envoyer
        - [ ] Tester le crud des données en base
        - [ ] S’assurer de la persistance des données en base
- [ ] Utiliser jira en parallèle du log pour communiquer sur ce qui est fait
- [ ] Penser à donner un justificatif de nouveau domicile à hubert