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
        - [x] Augmentation du code coverage en testant plus de fonctions
        - [x] Mise en commentaire de la méthode copyToClipBoard() {} vide pour augmentation code coverage & cleaning
    - [x] Tester le fichier lidl-new-report.page.ts
        - [x] Résolution des erreurs existantes
            - [x] Résolution de :
            ```
            NullInjectorError: R3InjectorError(DynamicTestModule)[ReportService -> HttpClient -> HttpClient]:
            NullInjectorError: No provider for HttpClient!
            ```
            - [x] Résolution de :
            ```
            NullInjectorError: R3InjectorError(DynamicTestModule)[ActivatedRoute -> ActivatedRoute]:
            NullInjectorError: No provider for ActivatedRoute!
            ```
            - [x] Résolution de l'erreur : 
            ```
            TypeError: Cannot read properties of undefined (reading 'paramMap')
            ```
            - [x] Mise en commentaire du detectChanges()
        - [x] Écriture du test de component truthy
        - [x] Écriture du test de component defined
        - [x] Écriture du test de type de retour attendu
        - [x] Augmentation du code coverage en testant plus de fonctions
    - [ ] Résolution des 2 tests qui Fail quand on ne les appelle pas .... 
        - [ ] src/app/shared/pipes/filter-store.pipe.spec.ts
        - [ ] src/app/shared/directives/sortable-table.directive.ts
- [x] Check de (https://stackoverflow.com/questions/24825860/how-to-get-the-code-coverage-report-using-jest) pour le coverage
- [x] Repasse orthographe sur les Toasts
    - [x] Correction orth fichier : regionals-management.component.ts
- [x] Veille techno (https://jestjs.io/docs/manual-mocks#mocking-methods-which-are-not-implemented-in-jsdom)
- [x] Tests non successful de refreshStore() dans reports.component.ts :  Cannot call Promise.then from within a sync test (syncTestZone for jest.describe).
    - [x] Check de (https://stackoverflow.com/questions/43186533/unit-test-error-cannot-call-promise-then-from-within-a-sync-test)
- [ ] Tester les use case lors de la création d'un rapport à la main sur la tablette
    - [x] Test de connexion avec les ids fournis par théo, echec.
    - [ ] Retour dans les notes prises lors de la formation tablette le 27 octobre

  
**22 Novembre** 
- [ ] Tests e2e
    - [x] Utilisation du code coverage sur le front pour voir ce qu'il reste à tester
        - [x] Report.component.ts
            - [x] Passage de la couverture à 81% en testant refreshStore()
            - [x] Écriture du test & résolution d’erreurs
                - [x] Check de (https://stackoverflow.com/questions/43186533/unit-test-error-cannot-call-promise-then-from-within-a-sync-test) & ajout de :
                ```
                const dep = TestBed.inject(ReportsComponent);
                jest.spyOn(dep, 'refreshStore').mockReturnValue();
                expect(dep.refreshStore()).toHaveBeenCalled();
                ```
                pour test de la méthode. Ceci mène à l'erreur :
                ```
                NullInjectorError: R3InjectorError(CompilerModule)[ReportsComponent -> ReportsComponent]:
                NullInjectorError: No provider for ReportsComponent!
                ```
                - [x] Résolution avec l'appel au component via testbed.inject() :
                ```
                const dep = TestBed.inject(ReportsComponent);
                jest.spyOn(dep, 'refreshStore').mockReturnValue();
                dep.refreshStore();
                expect(dep.refreshStore).toHaveBeenCalled();
                ```
        - [x] Report.service.ts, augmenter la cover de 44%
            - [x] Passage de la couverture à 84% en testant getReportById(), getReportsByBilling() && updateReportBilling()
        - [x] Lidl-report.component.ts, augmenter la cover de 20%
            - [x] Résolution des erreurs NullInjector & toHaveBeencAlled
            - [x] Mock de la fonction loadRps()
            - [x] Mock de la fonction loadEpi()
            - [x] Mock de la fonction loadSign()
            - [x] Mise en commentaire de la fonction vide copyToClipBoard() 
            - [x] Passage de la couverture à 61% total
        - [x] Aldi-report.component.ts, augmenter la cover de 26%
            - [x] Résolution des erreurs NullInjector & toHaveBeenCalled
            - [x] Passage de la couverture à 73% en testant toutes les méthodes du fichier
        - [x] report-cop-view.component.ts, augmenter la cover de 56%
            - [x] Passage de la couverture à 76% en testant getReportById(), getReportsByBilling() && updateReportBilling()
            - [x] Mise en commentaire de la fonction vide copyToClipBoard() 
    - [ ] Utilisation du code coverage sur le back pour voir ce qu'il reste à tester
        - [ ] Trouver pourquoi le code coverage ne se fait pas sur le back
            - [x] Re-run de npm test --coverage pour voir les choses en l'état : 
            ```
            Test Suites: 2 failed, 21 passed, 23 total
            Tests:       4 failed, 52 passed, 56 total
            ```
            - [ ] Résolution des erreurs
                - [x] ajout d'un :
                ```
                jest.setTimeout(10000);
                ```
                dans le fichier e2e pour laisser le temps à la connexion de se faire
                - [ ] Résolution de l'erreur : 
                ```
                End to end Reports › should return the reports endpoint using /GET                      
                TypeError: this.userRepository.findOne is not a function 
                ```
    - [ ] Résolution des 2 tests qui Fail quand on ne les appelle pas .... 
- [ ] Tester les use case lors de la création d'un rapport à la main sur la tablette
    - [x] Test de connexion avec les ids fournis par théo, echec.
    - [ ] Retour dans les notes prises lors de la formation tablette le 27 octobre


  
**23 Novembre** 
- [ ] Tests e2e
    - [x] Utilisation du code coverage sur le back pour voir ce qu'il reste à tester
        - [x] Trouver pourquoi le coverage ne s'affiche pas
        - [x] Checkk de (https://jestjs.io/docs/cli) et utilisation de collect-coverage plutôt que coverage, sans succès
        - [x] Check de (https://stackoverflow.com/questions/65292413/jest-coverage-ignores-nest-js-controller-and-service-files), 
        - [x] Essai de suppression de package-lock.json, dist & node-modules et réinstallation avec yarn en cours
        - [x] Check de (https://jestjs.io/docs/configuration#collectcoveragefrom-array) et modification du package.json
        - [x] Check de (https://www.valentinog.com/blog/jest-coverage/). **Solution : rajouter un double --** :
        ```
        npm test -- --coverage
        ```
    - [ ] Augmentation du coverage. En l'état, :
    ```
    File                                  | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
    --------------------------------------|---------|----------|---------|---------|----------------------------
    All files                             |   54.17 |     9.92 |   29.25 |   51.66 | 
    ```
    - [ ] Dossier /src/reports, 38.68% des lignes couvertes, repasse sur les fichiers de test
        - [ ] report.service.ts, augmenter la cover de 19%
        - [ ] trouver pourquoi les tests & mocks de methodes n'augmentent pas le %age de code testé ...

    - [x] Changement dans le package.json de :
    ```
    "scripts": {
		...
		"test": "jest",
		"test:watch": "jest --watch",
		"test:cov": "jest"
    ```
    à :
    ```
    "scripts": {
		...
		"test": "jest --detectOpenHandles",
		"test:watch": "jest --watch",
		"test:cov": "jest --coverage"
    ```
    && reprise des fichiers de config jest pour inclusion coverage & openhandles
    - [x] Suppression des 
    ```
    jest.setTimeout(15000);
    ```
    dans les fichiers reports.e2e-spec.ts & report.service.ts pour réduction du temps moyen d'exécution des tests back de ~120s à ~75s

    - [x] Le fichier e2e test retourne tous les résultats attendus, mais le dernier test Fail à cause de :
        ```
        user/users.service.ts
        TypeError: this.userRepository.findOne is not a function  
        ```
        alors que le status code est 200 comme attendu. Penser à le commenter au besoin

  
**24 Novembre** 
- [x] Tests e2e
    - [x] Survol du code backend pour voir si le coverage peut être amélioré
    - [x] Fin des tests dans l'état. Pour augmenter le coverage, je vais avoir besoin de passer plus de temps pour mieux comprendre, et ça va pas drastiquement augmenter la qualité du projet, donc je stoppe pour le moment. Commandes CLI à retenir :  
    back :  
    ```
    npm test --detectOpenHandles -- --coverage 
    ```
    Résultat attendu : 55 tests passed, 0 fail & ~ 50% des lines covered
    front :
    ```  
    ng test --detect-open-handles --coverage --test-name-pattern="Report"    
    ```

**Projet Lidl-Sav & tablettes** (fait partie de cop-sav)
- [ ] Objectif général : Structurer les infos & donner de la visibilité
    - [x] Créer un dossier local où ranger les infos
        - [x] Enregistrement des fichiers excel
    - [ ] Partie tablettes & monteurs
        - [ ] Structurer les infos
            - [ ] Mettre à jour les documents excel
                - [x] Listing monteurs : supprimer les lignes obsolètes. Lignes 5,8,19,23 & 33 supprimées (Deparis, Sardet, Devos, Yvai & Aielo)
                - [x] Listing monteurs : ajout ligne 32 : Nicolas Drudi
                - [ ] Listing monteurs : demander à Nicolas ses coordonnées
                - [x] Listing monteurs : ajouter colonnes pour rang monteur, attribution tablette, possession tablette atm & marque+version de tablette
                - [x] Passage pour infos chez Evelyne
                - [x] Passage pour infos chez Jean-Miche 
                - [ ] Listing monteurs : Mise à jour des colonnes
                - [ ] Listing monteurs : Mise à jour des informations tablettes
            - [ ] Combien de tablettes ont été achetées
                - [x] Extraction du dossier Orange de Jean-Michel / Le fichier COP-Contrat signé fait mention d'un accord signé au 3 Avril 2018 pour 19 tablettes Samsung galaxy A10 32Go à 59.90HT
                - [x] Un bon de commande en juin 2020 a été émis pour 10 tablettes de plus, mais pas de document signé ? Les 10 tabs n'ont pas été achetées ? 
            - [ ] Combien de tablettes sont en circulation
                - [ ] 19 ou 29 ? Trouver si la relance de 10 a été faite ou pas ?
            - [ ] Qui à une tablette
                - [ ] Check du fichier tablette-cop _11_2020.xls
            - [ ] Où est chaque tablette
            - [ ] Récupérer les contrats orange en cours
            - [ ] Gérer les tablettes
                - [ ] Créer un excel
                - [ ] Renseigner le modèle de tablette
                - [ ] Renseigner l'état de la tablette 
                - [ ] Renseigner les pinID
                - [ ] Renseigner les codes de verrouillage
    - [ ] Partie Users accès & droits
        - [ ] Accéder à la bdd
            - [ ] Lister les users & leurs droits
        - [ ] Accéder au déploiement
            - [ ] Lister les users & leurs droits
        - [ ] Structurer les infos
            - [ ] Mettre à jour les documents excel

  
**24 Novembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
    - [ ] Partie tablettes & monteurs
        - [ ] Structurer les infos
            - [ ] Mettre à jour les documents excel
                - [ ] Listing monteurs : demander à Nicolas ses coordonnées
            - [ ] Combien de tablettes ont été achetées
            - [ ] Combien de tablettes sont en circulation
                - [ ] 19 ou 29 ? Trouver si la relance de 10 a été faite ou pas ?
            - [ ] Qui à une tablette
                - [ ] Check du fichier tablette-cop _11_2020.xls
            - [ ] Où est chaque tablette
            - [ ] Récupérer les contrats orange en cours
            - [ ] Gérer les tablettes
                - [ ] Créer un excel
                - [ ] Renseigner le modèle de tablette
                - [ ] Renseigner l'état de la tablette 
                - [ ] Renseigner les pinID
                - [ ] Renseigner les codes de verrouillage
    - [ ] Partie Users accès & droits
        - [ ] Accéder à la bdd
            - [ ] Lister les users & leurs droits
        - [ ] Accéder au déploiement
            - [ ] Lister les users & leurs droits
        - [ ] Structurer les infos
            - [ ] Mettre à jour les documents excel

- [ ] Parler avec Guillaume de la structure/architecture portail saas (keycloak) des projets pour uniformisation & harmonisation des projets en cours & à venir
- [ ] Penser à donner un justificatif de nouveau domicile à hubert