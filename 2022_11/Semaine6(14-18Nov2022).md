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
                    - [ ] Résolution des problèmes au lancement du fichier de tests KMO_WEB\COP-SAV\src\app\core\services\report-service\report.service.spec.ts
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
                    - [ ] Test du Read
                    - [ ] Test de l’Update
                    - [ ] Test du Create
                    - [ ] Teste du Delete
        - [ ] Tester l'entité Reports sur l'application
            - [ ] Tester la présence d'un component form
            - [ ] Tester les paramètres attendus du form
            - [ ] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ?
            - [ ] Tester le format retourné ?
        - [ ] Tester la réception des données
        - [ ] Tester les données retournées
        - [ ] Tester l’envoi des données retournées au back
- [ ] Utiliser jira en parallèle du log pour communiquer sur ce qui est fait
- [ ] Penser à donner un justificatif de nouveau domicile à hubert