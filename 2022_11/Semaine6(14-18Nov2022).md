*** LOG Gregoire Chevalier ***
  
**14 Novembre**
- [ ] Tests e2e
    - [ ] Tester le front      
        - [ ] Tester le formulaire
            - [x] Résolution de l'erreur : 
                ```
                TypeError: Cannot read properties of null (reading 'length')
                ```
                - [x] Check de (https://stackoverflow.com/questions/61681239/async-pipe-sends-null-value-to-the-child-component), voir si no value is emitted by the Observable
                - [x] Modification du template html ligne 46 de :
                ```
                ngFor="let report of stores?.report | filterStore: filterString">
                ```
                à :
                ```
                ngFor="let report of stores?.report | filter: filterString">
                ```
                && modification du @Pipe{name:} du filter-store.pipe.ts de ```filterStore``` à ```filter``` to match, puis rollback à l'initial car faisait crash l'app
                - [x] Réparation des données test en base pour fonctionnement de l'app
                - [x] Résolution du value = null : ajout de  :
                ```
                if (value==null) {
                   return null;
                }
                ``` ligne 15 du fichier KMO_WEB\COP-SAV\src\app\shared\pipes\filter-store.pipe.ts , ce qui a pour effet de faire passer les test de component isTruthy && isDefined
            - [ ] Établissement des tests pertinents dans le contexte
                - [x] Tester la création du component
                - [x] Tester si le component est défini
                - [x] Tester si l'accès à l'url se fait bien
                - [ ] Tester si le component contient bien les champs requis && qu'ils retournent la valuer attendue
                    - [x] Création d'un fichier /src/windows-mock.ts rempli comme suit :
                    ```
                    import 'jest-preset-angular'; 
                    Object.defineProperty(document, 'doctype', {

                    value: '<!DOCTYPE html>'

                    });

                    Object.defineProperty(document.body.style, 'transform', {

                    value: () => {

                        return {

                        enumerable: true,

                        configurable: true

                        };

                    }
                    });
                    ```
                    suivi de (https://www.devcurry.com/2020/09/testing-angular-component-using-jest.html)
                    - [x] Input
                        - [x] Trouvé ici comment accéder à l'attribut voulu : (https://indepth.dev/tutorials/angular/get-attribute-value)
                        - [x] Résolution de l'erreur : TypeError: Converting circular structure to JSON
                            - [x] Check de  (https://stackoverflow.com/questions/63895685/unhandledpromiserejectionwarning-typeerror-converting-circular-structure-to-js) et ajout de --detect-opn-handles dans la cli avant la recherche par nom
                        - [x] Type
                        - [x] Class
                        - [x] Placeholder
                        - [ ] ngModel
                    - [x] TableHead
                        - [x] Date de création
                        - [x] IMS
                        - [x] Ref clipper
                        - [x] Monteurs
                        - [x] 2nd rdv
                        - [x] Facturé 
                    - [ ] TableBodyBody
                    - [ ] Dépend du *ngFor qui itère sur le nombre de Reports créés en base : checke de (https://stackoverflow.com/questions/44521254/get-length-of-array-in-ngfor-after-pipes-transformation) & ajout de ```; let l = count``` ligne 46 de KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\list-store\reports\reports.component.html
                        - [ ] Date de création
                        - [ ] IMS
                        - [ ] Ref clipper
                        - [ ] Monteurs
                        - [ ] 2nd rdv
                        - [ ] Facturé 

  
**15 Novembre**
- [x] Prise en main avancée de Jira et consigne du travail en cours/effectué
- [ ] Tests e2e
    - [ ] Tester le front      
        - [ ] Tester le front de l'entité Reports
            - [ ] Tester si le component contient bien les champs 
                - [ ] TableBody
                    - [x] Champ Date de création
                    - [x] Champ IMS
                    - [x] Champ Ref clipper
                    - [x] Champ Monteurs
                    - [x] Champ 2nd rdv
                    - [x] Champ Facturé 
                    - [ ] S'assurer qu'ils retournent la valeur attendue
                        - [x] Check de (https://stackoverflow.com/questions/53544913/angular-6-let-item-of-itens-from-database)
                - [x] TableRow
            - [x] Il faudra revenir sur l'affichage des reports quand je saurai accéder aux données de la base depuis angular. Pour l'instant, je mets ça de côté car pas spécialement primordial
            - [x] Tester le dialogue entre angular et l'api
                - [x] Check de (https://stackoverflow.com/questions/72566904/test-angular-http-service-with-jest)
                - [x] Déclaration de : ```let report: ReportService;``` 
                - [x] Ajout dans le premier beforeEach() de ```report = TestBed.inject(ReportService);``` 
                - [x] Ajout du test  :
                ```
		        type apiUrl = keyof typeof report;
                const linkToReportsInAPI = 'apiUrl' as apiUrl;
                expect(report).toBeDefined();
                expect(report[linkToReportsInAPI]).toBeDefined();
                expect(report).toBeTruthy();
                expect(report[linkToReportsInAPI]).toBeTruthy();
                expect(report[linkToReportsInAPI]).toEqual('http://localhost:3000/reports/');
                ```
                - [ ] Tester le CRUD Angular/API/Db via le ReportService
                    - [x] Résolution des problèmes au lancement du fichier de tests KMO_WEB\COP-SAV\src\app\core\services\report-service\report.service.spec.ts
                        - [x] Résolution de : 
                        ```
                        NullInjectorError: R3InjectorError(DynamicTestModule)[ReportService -> HttpClient -> HttpClient]:
                        NullInjectorError: No provider for HttpClient!
                        ```
                        Ajout dans le beforeEach() de :
                        ```
                        await TestBed.configureTestingModule({
                            imports: [HttpClient],
                            declarations: [],
                            providers: [ { provide: HttpClient, useValue: {} }]
                        });
                        ```
                        Renvoie l'erreur suivante : 
                        - [x] Résolution de :
                        ```
                        Unexpected value 'HttpClient' imported by the module 'DynamicTestModule'. Please add an @NgModule annotation. 
                        ```
                        Check de (https://stackoverflow.com/questions/52116993/jasmin-karma-error-unexpected-value-httpclient-imported-by-the-module-dy) et remplacement de HttpClient par **HttpClientModule**
                    - [x] Test du Read
                        - [x] Documentation à (https://angular.io/guide/testing-services)
                        - [x] Appel de la fonction getById & mock
                        
  
**16 Novembre** 
- [ ] Tests e2e
    - [x] Tester le front      
        - [x] Tester le front de l'entité Reports
            - [x] Tester le CRUD Angular/API/Db via le ReportService
                - [x] Test de l’Update
                    - [x] Appel de la fonction updateReport & mock
                - [x] Test du Create
                    - [x] Appel de la fonction createReport & mock
                - [x] Test du Delete
                    - [x] Appel de la fonction deleteReport & mock
                - [x] S'assurer que l'APIurl visée est atteignable
                - [x] S'assurer que l'APIurl visée est la bonne
            - [ ] Tester l’existence de la page Reports
                - [x] Installation de npm http
                - [x] Résolution des erreurs d'installation (peer-deps)
                - [x] Installation de npm supertest
                - [x] Résolution des erreurs d'installation (audit fix force)
                - [x] Revert des installs qui causent le crash de tous les tests 
                - [x] Résolution des problèmes de configuration créés
                    - [x] npm update --force
                    - [x] Suppression de package-lock.json & node modules
                    - [x] ng update 
                    - [x] npm i
                        - [x] Résolution des erreurs npm ERR! ERESOLVE unable to resolve dependency tree : npm update --legacy-peer-deps
                - [x] Check de (https://www.beyondjava.net/jest-testing-an-angular-service) 
            - [ ] Y a-t-il un intérêt à tester les valeus en réel depuis le front ? Je ne pense pas ... 
            - [x] Documentation (https://angular.io/guide/http)
- [x] Utiliser jira en parallèle du log pour communiquer sur ce qui est fait

  
**17 Novembre** 
- [ ] Tests e2e
    - [ ] Tester l'entité Reports sur l'application
    - [x] Essai d'accès à l'application sur localhost:4200  : erreur Cannot GET. Retour d'erreur console :
    ```
    Error: src/app/pages/authentification/authentification.component.ts:4:34 - error TS7016: Could not find a declaration file for module 'chart.js/helpers'. 'C:/Users/gchevalier/cleankmo/KMO_WEB/COP-SAV/node_modules/chart.js/helpers/helpers.js' implicitly has an 'any' type.

    4 import { resolveObjectKey } from 'chart.js/helpers';
                                    ~~~~~~~~~~~~~~~~~~
    × Failed to compile
    ```
    - [x] Résolution de l'erreur ci-dessus
        - [x] Check de (https://stackoverflow.com/questions/41292559/could-not-find-a-declaration-file-for-module-module-name-path-to-module-nam) 
    - [ ] Préambule aux tests
        - [x] Mise en commentaires de :
        ```
        import { resolveObjectKey } from 'chart.js/helpers';

        ```
    - [x] Restart & update du Docker Desktop
    - [ ] Se connecter en tant que technicien sur l'appli via tablette
        - [x] Aucun feedback à l'erreur d'ids/pw. Normal ?
        - [ ] Trouver les ids & pw nécessaires
    - [x] Prendre en main l'application
        - [x] La raison pour laquelle : 
        ```
        this.filterString = res['reference'];
        ```
        est undefined est que : res correspond à ce qui est renseigné dans le champ de recherche, d'où l'undefined si rien n'est renseigné
        - [x] Passe sur les feedbacks
            -> voir RetourOrthographeEtFeedbackAppli.md
        - [x] Passe sur l'orthographe
            -> voir RetourOrthographeEtFeedbackAppli.md
    - [x] Créer un magasin brand=Aldi
    - [x] Créer un ticket pour ce magasin Aldi
        - [x] Trouver pourquoi le ticket ne se créée pas : ("Le magasin choisi n'existe pas") -> le idstore qui va etre cherché dans la Db est défini comme nom de l'enseigne + id du magasin (new-ticket.component.ts ligne 73), mise en commentaires de : /* this.ticketForm.value.brand + */
    - [ ] Trouver comment créer un report
        - [x] Survol de KMO_WEB\kmo-app\src\app\pages\reports\aldi-new-report\aldi-new-report.page.html
        - [x] Correction des erreurs d'orthographe de KMO_WEB\kmo-app\src\app\pages\reports\aldi-new-report\aldi-new-report.page.html
        - [x] Trouver où est le déclencheur qui mène à l'interface de création de report - > appelé dans openTicket() dans le fichier ticket.page.ts, avec le code suivant :
        ```
         if (data.store.brand === 'Aldi') {
            const modal: HTMLIonModalElement =
                await this.modalController.create({
                component: AldiNewReportPage,
                cssClass: 'fullscreen',
                componentProps: {
                    data,
                }
                });
            modal.onDidDismiss().then(() => {
                this.refreshTicket()
                this.getReportReturnUncompleted()
            });
            await modal.present();
            }
        }
        ```
        && comme attendu, le store.data.brand === 'Aldi', alors pourquoi cela ne trigger pas :
        ```
        create({
          component: AldiNewReportPage
          ...
          })
        ``` 
        ?????
        Pas forcément nécessaire de créer un report depuis l'pp front SI les fichiers lidl-report/lidl-report.component.spec.ts, report-cop-view.component.spec.ts && aldi-report/aldi-report.component.spec.ts sont testés
    - [ ] Tester la présence d'un component form
    - [ ] Tester les paramètres attendus du form
    - [ ] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ?
    - [x] Tester le format retourné -> pas la peine, le validator est sensé le faire
- [x] Utiliser jira en parallèle du log pour communiquer sur ce qui est fait
- [ ] Penser à donner un justificatif de nouveau domicile à hubert

  
**18 Novembre** 
- [ ] Tests e2e
    - [ ] Tester le fichier report-cop-view.component.spec.ts
        - [ ] Résolution des erreurs existantes
            - [x] Résolution de :
            ```
            NullInjectorError: R3InjectorError(DynamicTestModule)[ReportService -> HttpClient -> HttpClient]:
            NullInjectorError: No provider for HttpClient!
            ```
            Ajout dans le beforeEach() de :
            ```
            imports: [HttpClientModule],
			providers: [ { provide: HttpClientModule, useValue: {} }]
            ```
            Qui amène l'erreur suivante: 
            - [x] Résolution de :
            ```
            NullInjectorError: R3InjectorError(DynamicTestModule)[ActivatedRoute -> ActivatedRoute]:
            NullInjectorError: No provider for ActivatedRoute!
            ```
            Ajout dans le provider du beforeEach() de : { provide: ActivatedRoute, useValue: {} } qui amène l'erreur suivante :
            - [x] Résolution de :
            ```
            NullInjectorError: R3InjectorError(DynamicTestModule)[ToastService -> ToastrService -> InjectionToken ToastConfig -> InjectionToken ToastConfig]:
            NullInjectorError: No provider for InjectionToken ToastConfig!
            ```
            Check de (https://stackoverflow.com/questions/59190786/nullinjectorerror-staticinjectorerrordynamictestmoduletoastrservice-injec) et application en ajoutant : ToastrModule.forRoot() dans les *imports* du beforeEach(), ce qui renvoie l'erreur suivante :
            - [x] Résolution de :
            ```
            TypeError: Cannot read properties of undefined (reading 'paramMap')
            ```
            -> La ligne qui cause le problème est : this.route.snapshot.paramMap.get('id'), qui a pour but de récupérer l'id du report. Mocker avec une valeur en dur permet de passer outre et renvoie l'erreur suivante :
            - [x] Résolution de :
            ```
            TypeError: Cannot read properties of undefined (reading 'store')
            ```
            qui provient de la ligne 26 : fixture.detectChanges();
            - [x] Documentation à propos de fixtures.detectChanges() (https://medium.com/@menloinnovations/testing-asynchronous-operations-in-angular-components-45d1ebad3864#:~:text=fixture.,-detectChanges()&text=detectChanges()%20tells%20Angular%20to,first%20time%20it%20is%20called.) & (https://stackoverflow.com/questions/50137734/detectchanges-not-working-within-angular-test)
            - [x] Commenter le detectChanges() fait passer le test mais n'est pas une solution car on ne passe plus alors dans le ngOnInit() du component.
        - [ ] Tester la présence d'un component form
        - [ ] Tester les paramètres attendus du form
        - [ ] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ?
    - [ ] Tester le fichier aldi-new-report.page.ts
        - [ ] Tester la présence d'un component form
        - [ ] Tester les paramètres attendus du form
        - [ ] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ?
    - [ ] Tester le fichier lidl-new-report.page.ts
        - [ ] Tester la présence d'un component form
        - [ ] Tester les paramètres attendus du form
        - [ ] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ?
        