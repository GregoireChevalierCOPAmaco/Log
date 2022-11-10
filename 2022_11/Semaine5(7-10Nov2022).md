*** LOG Gregoire Chevalier ***

**7 Novembre**
- [ ] Tests e2e
    - [ ] Premier test : it(/GET login`).
        - [x] Résolution de l'erreur :
        ```
        TypeError: this.userRepository.findOne is not a function
        ```
        -> Mise en commentaire de ```await app.init();``` dans le fichier de test e2e vu que l'application est déjà démarrée à un autre moment.
        - [x] Résolution de l'erreur :
        ```expected 200 "OK", got 404 "Not Found"```   
            - [x] Essai avec :
            ```
            return request(app.getHttpServer())
            .get('https://www.google.com/')
            .expect(200)
            ```
            Renvoie : connect ECONNREFUSED 127.0.0.1:80.  
            .get('localhost') renvoie la même erreur, mais .get('/localhost') & .get('/localhost:4200/#/login') renvoient une 404...
            - [x] Check de (https://stackoverflow.com/questions/66924663/how-to-fix-an-endpoint-test-that-returns-404-status-code-rather-than-200-using-e) & (https://stackoverflow.com/questions/56122778/supertest-not-found-error-testing-express-endpoint/57368925#57368925)
            - [x] Remplacement de la variable "co" écrite en dur dans TypeOrmModule.forRoot() par le modèle défini dans le fichier KMO_WEB\kmo-back\src\app.module.ts, ce qui renvoie un timeout avec l'erreur suivante : [Nest] 9848  - 07/11/2022 09:58:23   ERROR [TypeOrmModule] Unable to connect to the database. Retrying (1)...
            Error: ER_ACCESS_DENIED_ERROR: Access denied for user ''@'172.18.0.1' (using password: NO)
            - [x] Après résolution des problèmes de connexion, on retrouve la 404 de base
        - [x] Mise en commentaire  de la fonction de création de 1er user dans user.service.ts pour faire avancer les tests.  
        !! PENSER À LE DÉCOMMENTER AVANT DE COMMIT !!
        - [x] Les routes (endpoints) doivent aller taper dans le swagger, et non dans l'application front (swagger =/= localhost !!)
        - [x] Résolution de l'erreur : expected 200 "OK", got 500 "Internal Server Error"
            - [x] Check de (https://stackoverflow.com/questions/52840873/getting-500-internal-server-error-while-using-supertest). L'erreur viendrait du faity qu'on est pas authentifié : 
            ```
            [Nest] 27052  - 07/11/2022 11:15:46   ERROR [ExceptionsHandler] Unknown authentication strategy "jwt"
            Error: Unknown authentication strategy "jwt" 
            ```  
            - [x] Import de l'AuthModule qui renvoie l'erreur suivante :
            ```
            Nest can't resolve dependencies of the UsersService (UserRepository, JwtService, ?). Please make sure that the argument MailerService at index [2] is available in the AuthModule context.
            ```
            - [x] Ajout du MailerService et de son porvide: dans les providers[] -> Renvoie une 401
        - [ ] Résolution de l'erreur :  expected 200 "OK", got 401 "Unauthorized"
            - [x] Ajout après la ligne .get(/reports) de : .set('Authorization', `Bearer .... + token), qui renvoie une erreur 500 due à : ERROR [ExceptionsHandler] this.reportService.findReportByGroup is not a function
            - [x] Essai avec un ```.overrideGuard(AuthGuard())```, sans succès
            - [x] Changement de la ligne en supprimant Bearer :
            ```
            .set('Authorization', `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6Ijk5ZmI0MmJhLTY4MGMtNGM1Ny05YTU2LThmNWY3ODU0ZjI2ZiIsImVtYWlsIjoiYWRtaW4uYWRtaW5AYWRtaW4uY29tIiwicm9sZSI6ImFkbWluIiwiZmlyc3RuYW1lIjoiYWRtaW4iLCJsYXN0bmFtZSI6ImFkbWluIiwiaWF0IjoxNjY1NDk2NDUyLCJleHAiOjE2NjU1OTE0NTJ9.lprur-fGrGAUUJ9wxpW0klL2P2wyho1TH_pMf06Y7Ao`)
            ```
            Ceci renvoie une erreur 500 due à :  ERROR [ExceptionsHandler] Cannot read properties of null (reading 'role'). Avec la stack commençant par : 
            ```
             at TechnicianJwtGuard.handleRequest (C:\Users\gchevalier\cleankmo\KMO_WEB\kmo-back\src\authentification\guards\technician-jwt-guard.ts:43:11)
            at C:\Users\gchevalier\cleankmo\KMO_WEB\kmo-back\node_modules\@nestjs\passport\dist\auth.guard.js:49:128
            ```
            - [x] Survol des fichiers auth.guard.ts & technician-jwt-guard.ts : Apparemment, le token est décodé pour trouver Id & Role, et il manquerait role dans le token fourni...
            - [x] Changement de la ligne en remettant Bearer comme dans postman :
            ```
            .set('Authorization', `Bearer eyJhbGciOiJIUzI1NiIsInR...`)
            ```
            Ceci a pour effet de renvoyer l'erreur : TypeError: this.userRepository.findOne is not a function depuis le users.service.ts  
            L'erreur se recoupe avec celle de la création du 1er user. Essayer de rcupérer en réel les données
        - [x] Tentative de récupérer les données en réel avec dbco : se solde par un echec avec l'erreur :
        ```
        AlreadyHasActiveConnectionError: Cannot create a new connection named "default", because connection with such name already exist and it now has an active connection session.
        ```
        - [ ] Résolution de ERROR [ExceptionsHandler] this.reportService.findReportByGroup is not a function depuis le fichier reports.controller
  

**8 Novembre**
- [x] Refacto du test avec de la response en await, pour le même résultat
- [x] Check de (https://medium.com/@exfabrica/nestjs-unit-and-e2e-tests-with-jest-825ba5033c6), (https://www.pluralsight.com/guides/test-asynchronous-code-jest)
- [x] Après modification du fichier reports.controller.ts de :
```
	@Get()
	@UseGuards(TechnicianJwtGuard)
	findByGroup() {
		return this.reportService.findReportByGroup();
	}
```
en : 
```
	@Get()
	@UseGuards(TechnicianJwtGuard)
	findByGroup() {
		async () => {
			await this.reportService.findReportByGroup();
		}
	}
```
On obtient un status code 200, mais toujours avec l'erreur TypeError  this.userRepository.findOne is not a function
- [ ] Application de la méthode trouvée sur (https://github.com/nestjs/typeorm/issues/405) : Rajouter une connection dans le constructor du users.service
    - [ ] Résolution du problème de gestion de dépendance : Nest can't resolve dependencies of the UsersService. Please make sure that the argument Connection is available
        - [x] Check de (https://github.com/nestjs/nest/issues/9416), il semblerait qu'il y ait un import circulaire de TypeOrm/Connection ? dans plusieurs fichiers
        - [x] Documentation sur la résolution d'import circulaire : (https://blog.logrocket.com/avoid-circular-dependencies-nestjs/")
- [ ] Passage sur le reste de l'end-to-end
    - [ ] Documentation de la méthode à suivre
        - [ ] Check de (https://stackoverflow.com/questions/70494827/testing-an-angular-form-with-jest-fill-and-query-form-fields), (https://www.devcurry.com/2020/09/testing-angular-component-using-jest.html), (https://thymikee.github.io/jest-preset-angular/)
        - [ ] Découverte du flow de test jest-angular (https://www.xfive.co/blog/testing-angular-faster-jest/), (https://thymikee.github.io/jest-preset-angular/docs/guides/angular-13+)
- [ ] Tests e2e
    - [ ] Tester le front
        - [ ] Tester le formulaire
            - [ ] Utilisation de Jest-preset-angular
            - [ ] Installation de Jest-preset-angular
            - [ ] Configuration de Jest-preset-angular
            - [x] Check de la verion d'angular : 13.2.6, ok (>13 pour le preset)
        - [ ] Tester la réception des données
        - [ ] Tester les données retournées
        - [ ] Tester l’envoi des données retournées au back
    - [ ] Tester le back
        - [ ] Tester la bonne réception des données
        - [x] Tester la connexion à la db
        - [ ] Tester la bonne forme des données à envoyer
        - [x] Tester le crud des données en base
        - [x] S’assurer de la persistance des données en base
- [ ] Utiliser jira en parallèle du log pour communiquer sur ce qui est fait
- [ ] Penser à donner un justificatif de nouveau domicile à hubert
  

**9 Novembre**
- [ ] Tests e2e
    - [ ] Tester le front
        - [x] Utilisation de Jest-preset-angular
        - [x] Installation de Jest-preset-angular
            - [x] Suivi de doc : (https://thymikee.github.io/jest-preset-angular/docs/getting-started/installation)
            - [x] À la racine du projet (KMO_WEB\kmo-app), installation de toutes les dépendances avec : 
            ```
            npm install -D jest jest-preset-angular @types/jest
            ```
            Retour : 
            ```
            7 vulnerabilities (5 high, 2 critical)
            ```
            - [x] Run de :
            ```
            npm audit fix
            ```
            Retour : 
            ```
            # npm audit report

            minimatch  <3.0.5
            Severity: high
            minimatch ReDoS vulnerability - https://github.com/advisories/GHSA-f8q6-p94x-37v3
            fix available via `npm audit fix --force`
            Will install @angular-devkit/build-angular@13.3.9, which is outside the stated dependency range
            node_modules/minimatch
            @angular-devkit/build-angular  0.8.8 - 13.3.8 || 14.0.0-next.0 - 14.1.0-rc.3
            Depends on vulnerable versions of minimatch
            Depends on vulnerable versions of terser
            node_modules/@angular-devkit/build-angular

            terser  5.0.0 - 5.14.1
            Severity: high
            Terser insecure use of regular expressions before v4.8.1 and v5.14.2 leads to ReDoS - https://github.com/advisories/GHSA-4wf5-vphf-c2xc
            fix available via `npm audit fix --force`
            Will install @angular-devkit/build-angular@13.3.9, which is outside the stated dependency range
            node_modules/terser

            3 high severity vulnerabilities
            ```
            - [x] Run de :
            ```
            npm audit fix --force
            ```
        - [x] Configuration de Jest-preset-angular preset
            - [x] Création du fichier setup-jest.ts à la racine de COP-SAV
            - [x] Remplissage du fichier setup-jest.ts avec :
            ```
            import 'jest-preset-angular/setup-jest';
            ```
            - [x] Création du fichier jest.config.ts à la racine de COP-SAV
            - [x] Remplissage du fichier avec :
            ```
            import type { Config } from 'jest';

            const jestConfig: Config = {
            preset: 'jest-preset-angular',
            setupFilesAfterEnv: ['<rootDir>/setup-jest.ts'],
            globalSetup: 'jest-preset-angular/global-setup',
            };

            export default jestConfig;
            ```
            - [x] Modification du fichier tsconfig.spec.json avec :
            ```
            {
                "extends": "./tsconfig.json",
                "compilerOptions": {
                "outDir": "./out-tsc/spec",
                "module": "CommonJs",
                "types": ["jest"]
                },
                "include": ["src/**/*.spec.ts", "src/**/*.d.ts"]
            }
            ```
            !! Notice pour ES2016+ : !!
            ```
            INFO
            Angular doesn't support native async/await in testing with target higher than ES2016, see https://github.com/angular/components/issues/21632#issuecomment-764975917
            ```
            À l'heure actuelle, la commande ```npm test``` lance le testeur karma
            - [x] Rename du fichier ./src/test.ts en karmaTest.ts, sans succès.
            - [x] Ajout de :
            ```
            "types": ["jest"]
            ```
            dans le fichier tsconfig.ts
            - [x] Dans le fichier angular.json, changement de builder:karma par :
            ```
            "builder": "@angular-devkit/build-angular:jest"
            ```
            ligne 89, et suppression des 11 lignes "options" liées à karma. Retour d'erreur : An unhandled exception occurred: Cannot find builder "@angular-devkit/build-angular:jest" au lancement de ```ng test```
            - [x] Résolution de l'erreur : An unhandled exception occurred: Cannot find builder "@angular-devkit/build-angular:jest"
                - [x] Suivi de (https://medium.com/angular-in-depth/angular-cli-ng-test-with-jest-in-3-minutes-v2-1060ddd7908d)
                - [x] Suppression de karma avec la commande : 
                ```
                npm remove karma karma-chrome-launcher karma-coverage karma-jasmine karma-jasmine-html-reporter
                ```
                - [x] Suppression de karmarelated fichiers avec commande : 
                ```
                rm ./karma.conf.js              
                rm ./src/test.ts  
                ```
                - [x] utilisation de ```npm i @angular-builders/jest --force``` ce qui permet de lancer les tests ave ng test
            - [x] Création et configuration du fichier jest-global-mocks.ts comme précaunisé ici (https://thymikee.github.io/jest-preset-angular/docs/getting-started/installation)
            - [x] Résolution du warning : 
            ```
            warning: unable to locate custom jest configuration file at path "C:\Users\gchevalier\cleankmo\KMO_WEB\COP-SAV\jest.config.js"
            ```
            -> conversion du fichier .ts au format js et suivi du remplissage comme conseillé (https://thymikee.github.io/jest-preset-angular/docs/getting-started/installation)
        - [x] Ignorage des tests auto générés dans la commande
            - [x] Trouver comment ne tester qu'un seul fichier
                - [x] Check de (https://www.cloudhadoop.com/angular-run-single-testfile/) , (https://stackoverflow.com/questions/40683673/how-to-execute-only-one-test-spec-with-angular-cli) & (https://github.com/just-jeb/angular-builders/tree/master/packages/jest#builder-options)
            Il faut préciser le nom donné dans le describe que l'on veut atteindre, exemple avec app.component.spec.ts :
            ```
            ng test --test-name-pattern="AppComponent"
            ```
        - [ ] Tester le formulaire
            - [x] Recherche du fichier concerné : KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\list-store\reports\reports.component.spec.ts
            - [ ] Résolution des tests auto-générés foireux
                - [x] Résolution de l'erreur : 
                ```
                NullInjectorError: R3InjectorError(DynamicTestModule)[StoreService -> HttpClient -> HttpClient]: NullInjectorError: No provider for HttpClient!
                ```
                en ajoutant imports: [HttpClientModule], au TestBed.configureTestingModule({}). -> Retour d'erreur :
                - [x] Résolution de l'erreur : 
                ```
                NullInjectorError: R3InjectorError(DynamicTestModule)[ActivatedRoute -> ActivatedRoute]:
                NullInjectorError: No provider for ActivatedRoute!
                ```
                en ajoutant imports: [RouterTestingModule]Retour d'erreur :
                - [x] Résolution de l'erreur : 
                ```
                NG0302: The pipe 'filter' could not be found in the 'ReportsComponent' component!. Find more at https://angular.io/errors/NG0302
                ```
                - [x] Check de (https://angular.io/errors/NG0302) & (https://www.youtube.com/watch?v=maI2u6Sxk9M)
                - [x] Ajout de FilterStorePipe dans app.module.ts declaration:[] & imports: [] ++ dans le fichier reports.components.spec.ts, declaration:[] & imports: []. Puis, relocate du pipe dans sa déclaration d'import depuis ./src/../pathTo/pipe à ./../../pathTo/pipe & modification du template html ligne 46 de :
                ```
                ngFor="let report of stores?.report | filter: filterString">
                ```
                à :
                ```
                ngFor="let report of stores?.report | filterStore: filterString">
                ```
                -> Retour d'erreur :
                ```
                NullInjectorError: R3InjectorError(DynamicTestModule)[ToastService -> ToastrService -> InjectionToken ToastConfig -> InjectionToken ToastConfig]:           
                NullInjectorError: No provider for InjectionToken ToastConfig!   
                ```          
                - [ ] Résolution de l'erreur NullInjectionError ci dessus
                    - [x] Check de (https://stackoverflow.com/questions/69111109/angular-unit-testing-nullinjectorerror-r3injectorerrordynamictestmodulematsn)
            - [ ] Établissement des tests pertinents dans le contexte
        - [ ] Tester la réception des données
        - [ ] Tester les données retournées
        - [ ] Tester l’envoi des données retournées au back
        

**10 Novembre**
- [ ] Tests e2e
    - [ ] Tester le front      
        - [ ] Tester le formulaire
            - [x] Recherche du fichier concerné : KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\list-store\reports\reports.component.spec.ts
            - [ ] Résolution des tests auto-générés foireux
                - [x] Résolution de l'erreur NullInjectionError ci dessus
                    - [x] Check de (https://www.codegrepper.com/code-examples/typescript/NullInjectorError%3A+R3InjectorError%28DynamicTestModule%29%5BAdminTestCentersComponent+-%3E+ToastrService+-%3E+InjectionToken+ToastConfig+-%3E+InjectionToken+ToastConfig%5D%3A+NullInjectorError%3A+No+provider+for+InjectionToken+ToastConfig%21), à priori il faut rajouter le module toast avec forRoot.
                    - [x] Ajout de ```ToastrModule.forRoot()```dans les imports du beforeEach. Erreur résolue, passage à la suivante
                - [x] Résolution de l'erreur : 
                ```
                NG0303: Can't bind to 'ngModel' since it isn't a known property of 'input'.
                ```
                -> ajout de FormsModule dans les imports du fichier reports.component.spec.ts vu qu'il était déjà présent dans l'app.module.ts
                - [x] Résolution de l'erreur : Function not implemented. du fichier edit-ticket.component.spec.ts en faisant un test it() sur la base du create 
                - [x] Recherche du code qui pose problème : ligne 14 du fichier  KMO_WEB\COP-SAV\src\app\shared\pipes\filter-store.pipe.ts
                - [x] Résolution de l'erreur : Cannot find module 'src/app/core/services/login-service/login.service' from 'src/app/pages/authentification/reset-password/reset-password.component.ts' fichier appelé dans la stack. -> rectification de l'import, passage de ./src/... au chemin réel en dur
                - [ ] Résolution de l'erreur : 
                    ```
                    TypeError: Cannot read properties of null (reading 'length')
                    ```
                    - [x] Check de (https://javascript.plainenglish.io/how-to-choose-between-constructor-and-ngoninit-in-your-angular-apps-f16987627312)
                    - [ ] Décomposition du flow de test du reports.component : 
                        - reports.components.spec.ts
                            - Le test spec attend de la variable (component).toBeTruthy.
                            - La variable component importe le classe ReportComponent
                        - reports.component.ts
                            - À l'initialisation du component via ngOnInit() ligne23, on récupère le champ ['référence'] fourni par le template html,
                            - on refresh la liste des Stores pour qu'elle soit à jour
                            - puis on utilise this.route.queryParams.subscribe(res =>) 
                            - problème : res renvoie un {} vide, du coup ce dont on a besoin par la suite à savoir this.filterString = res['reference']; devient undefined. Pourquoi ?
            - [ ] Établissement des tests pertinents dans le contexte
        - [ ] Tester la réception des données
        - [ ] Tester les données retournées
        - [ ] Tester l’envoi des données retournées au back