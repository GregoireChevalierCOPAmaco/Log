**21 Novembre** 
- [ ] Tests e2e
    - [x] Manipulation de l'application en local pour comprendre les interactions vues en Db
    - [x] Tester le fichier report-cop-view.component.spec.ts
        - [x] Poursuite de résolution d'erreurs
            - [x] On ne passe pas dans la boucle .subscribe(res...)
            - [ ] Trouver pourquoi le store associé au report renseigné via l'id en dur est undefined, trouver comment atteindre res :
            ```
            this.reportService.getReportById(this.idReport).subscribe(res => {
			this.report = res;
            ...
            ```
            - [x] Mise en commentaire du detectChanges() pour progression. Voir (https://codecraft.tv/courses/angular/unit-testing/change-detection/) le moment venu
            - [x] résolution de l'erreur : 
            ```
            TypeError: Cannot read properties of undefined (reading 'paramMap')
            ```
            Check de (https://stackoverflow.com/questions/53479797/angular-unit-testing-mock-parammap-get), et ajout de :
            ```
            snapshot: { paramMap: {get:(id:string)=>{id:"1"}}}
            ```
            dans le provide: ActivatedRoute de providers.
        - [x] Écriture du test de component defined
        - [x] Écriture du test de type de retour attendu
        <!-- - [ ] Écriture du test de connexion à l'API -->
        - [x] Tester la présence d'un component form -> pas à faire sur le front, mais éventuellement à tester sur l'app java
        - [x] Tester les paramètres attendus du form -> pas à faire sur le front, mais éventuellement à tester sur l'app java
        - [x] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ? -> pas à faire sur le front, mais éventuellement à tester sur l'app java
        -> résonnement applicable aux duex autres fichiers à tester ci-dessous ↓
    - [x] Tester le fichier aldi-new-report.page.ts
        - [x] Résolution des erreurs existantes
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
            Ajout dans le provider du beforeEach() de : { provide: ActivatedRoute, useValue: {} }, && ToastrModule.forRoot() dans les *imports* du beforeEach()
            Qui amène à l'erreur suivante: 
            - [x] Résolution de l'erreur : 
            ```
            TypeError: Cannot read properties of undefined (reading 'paramMap')
            ```
            Ajout de :
            ```
            snapshot: { paramMap: {get:(id:string)=>{id:"1"}}}
            ```
            dans le provide: ActivatedRoute de providers.
            - [x] Mise en commentaire du detectChanges()
        - [x] Écriture du test de component truthy
        - [x] Écriture du test de component defined
        - [x] Écriture du test de type de retour attendu  
    - [ ] Tester le fichier lidl-new-report.page.ts
        - [ ] Écriture du test de component truthy
        - [ ] Écriture du test de component defined
        - [ ] Écriture du test de type de retour attendu
        - [ ] Écriture du test de connexion à l'API
    - [ ] Tester les use case lors de la création d'un rapport à la main sur la tablette
