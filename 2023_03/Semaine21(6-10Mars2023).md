**6 Mars**
- [ ] Passage au keycloak predict
    - [ ] Trouver le moyen d'exporter les users en meme temps que le realm ?
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Setup du repo
        - [ ] Mise en place l'authguard
            - [x] Résolution de l'erreur : 
            ```
            error TS2416: Property 'intercept' in type 'KeycloakBearerInterceptor' is not assignable to the same property in base type 'HttpInterceptor'.
            ```
            - [x] Recherches sur l'erreur : 
                - [x] Check de (https://stackoverflow.com/questions/55760999/property-intercept-in-type-httpintercept-is-not-assignable-to-the-same-prope) & (https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1296)
                - [x] Réponse de GPT sur le sujet :
                ```
                The error message suggests that the intercept property in KeycloakBearerInterceptor is not compatible with the same property in the base HttpInterceptor type. This is likely due to a version incompatibility between the @angular/common/http module and the keycloak-angular library, which provides the KeycloakBearerInterceptor class.

                To fix this issue, you can try upgrading the keycloak-angular library to the latest version or downgrading the @angular/common/http module to a compatible version. Here are a few steps you can take:

                Check the version compatibility of the keycloak-angular library with @angular/common/http module. You can find this information in the keycloak-angular documentation or by checking the peerDependencies field in the package.json file of the library.

                If there is a version incompatibility, try upgrading or downgrading the library or the module to a compatible version. You can do this by updating the version numbers in the dependencies or devDependencies section of the package.json file in your Angular project.

                After updating the dependencies, run npm install to install the new versions of the packages.

                If the issue persists, try clearing the Angular build cache by running npm run clean and then rebuilding the application with ng serve.

                If none of the above steps work, you can try using a different library or implementation for the Keycloak integration in your Angular application.
                ```
            - [x] Adaptation du package.json, & ```npm update --legacy-peer-deps``` , retour console : 
            ```
            added 386 packages, removed 314 packages, changed 137 packages, and audited 2163 packages in 1m

            204 packages are looking for funding
            run `npm fund` for details

            found 0 vulnerabilities
            ```
            - [x] ```ng serve``` compile correctement, mais ne redirige plus vers l'auth keycloak Oo
            - [x] Résolution erreur console browser : 
            ```
            ERROR Error: Uncaught (in promise): NullInjectorError: R3InjectorError(AppModule)[AppAuthGuard -> AppAuthGuard -> AppAuthGuard]: 
            NullInjectorError: No provider for AppAuthGuard!
            ```
            - [x] Ajout de : 
            ```
            AppAuthGuard, {
                provide: KeycloakService,
                useValue: keycloakService
            }
            ```
            dans le providers d'app.module.ts & importscorrespondants
            - [x] Résolution erreur console browser : 
            ```
            ERROR Error: The selector "app-cop-example-dialog" did not match any elements
            ```
            - [x] Check de (https://stackoverflow.com/questions/57523307/angular-material-dialogue-issue-error-the-selector-dialog-result-example-dia)
            - [x] Check de (https://github.com/angular/components/issues/5899)
            - [x] Suppression de :
            ```
            ,CopExampleDialogComponent,COPComponent
            ```
            dans le bootstrap [] de l'app.module.ts et résolution de l'erreur
            - [x] Résolution de l'erreur de non-redirection vers le login keycloak au reach de l'app (8453 ERROR Error: Uncaught (in promise): Error: An error happened during access validation.)
                - [x] Check de (https://www.google.com/search?q=keycloak+angular+core.mjs%3A8453+ERROR+Error%3A+Uncaught+(in+promise)%3A+Error%3A+An+error+happened+during+access+validation.+Details%3ATypeError%3A+Cannot+read+properties+of+undefined+(reading+%27resourceAccess%27)+Error%3A+An+error+happened+during+access+validation.+Details%3ATypeError%3A+Cannot+read+properties+of+undefined+(reading+%27resourceAccess%27)&oq=keycloak+angular+core.mjs%3A8453+ERROR+Error%3A+Uncaught+(in+promise)%3A+Error%3A+An+error+happened+during+access+validation.+Details%3ATypeError%3A+Cannot+read+properties+of+undefined+(reading+%27resourceAccess%27)+Error%3A+An+error+happened+during+access+validation.+Details%3ATypeError%3A+Cannot+read+properties+of+undefined+(reading+%27resourceAccess%27)&aqs=chrome..69i57.4239j0j1&sourceid=chrome&ie=UTF-8) & application : remplacement dans l'app.module.ts de ```bootstrap: [AppComponent]``` par ```entryComponents: [AppComponent]``` 
            - [x] Push et PR
            - [x] Résolution linting :
                - app.module.ts : changement de ngDoBootstrap en DoBootstrap
                - app.authguard.ts : suppression de ```state: RouterStateSnapshot``` et de ses imports
                - app.component.ts : suppression de ```type usersRoles = Array<{id: number, text: string}>```
            - [x] Re-commit, repush et PR
            - [ ] Résolution du fail des tests front : TypeError: Cannot read properties of undefined (reading 'testEnvironmentOptions')
                - [x] Check de (https://stackoverflow.com/questions/72078160/jest-testenvironmentoptions-cannot-be-read)
        - [x] Mise à jour du jira 
        - [ ] Ajouter au fichier app.component.html le bouton de déconnexion
        - [ ] Ajouter au fichier app.component.html les infos user dans la topbar
        - [ ] Faire en sorte que le username = email
- [ ] Lien avec les autres applis en fonction de qui est connecté
    - [ ] Réussir à afficher le rôle de l'user connecté
    - [ ] Afficher un lien vers l'app 4567 en fonction
- [ ] Ajouter un attribut store à chaque user de predict sur keycloak
    - [x] Check de (https://www.keycloak.org/docs/latest/server_admin/index.html#user-attributes)
    - [ ] Voir comment ça a été fait pour atmos
    - [ ] Lier à une autre base ?
    

**7 Mars**
- [ ] Passage au keycloak predict
    - [ ] Trouver le moyen d'exporter les users en meme temps que le realm ?
        - [x] Mail à Olivier
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Setup du repo
        - [x] Mise en place l'authguard
        - [ ] Résolution des problèmes de lint : changement de ```ngDoBootstrap``` en ```DoBootstrap``` renvoie l'erreur fatale suivante :
        ```
        Error: NG0403: The module AppModule was bootstrapped, but it does not declare "@NgModule.bootstrap" components nor a "ngDoBootstrap" method. Please define one of these.
        ```
        Revert du changement pour retrouver l'appli fonctionnelle && 
        ```
        // eslint-disable-next-line 
        ```
        sur la ligne en question
        - [x] test avec ```turbo lint``` , retour console :
        ```
        kmo-predict-front:lint: All files pass linting.
        ```
        - [ ] Résolution des problèmes de versions jest-jsdom pour faire passer les tests
        - [x] Ajouter au fichier app.component.html le bouton de déconnexion
        - [x] Ajouter au fichier app.component.html les infos user dans la topbar
        - [ ] Lien avec les autres applis en fonction de qui est connecté
            - [ ] Réussir à afficher le rôle de l'user connecté
            - [ ] Afficher un lien vers l'app 4567 en fonction
- [ ] Ajouter un attribut store à chaque user de predict sur keycloak
    - [ ] Voir comment ça a été fait pour atmos
    - [ ] Lier à une autre base ?


    

**8 Mars**
- [x] Résolution du problème d'undefined des tests de Théo & démarrage de ses tsts en peer programming
- [ ] Passage au keycloak predict
    - [ ] Exporter les users en meme temps que le realm 
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Setup du repo
        - [x] Résolution des problèmes de versions jest-jsdom pour faire passer les tests
            - [x] Désinstallation de jest & jsdom dans tous les dossiers du repo (root/front/back)
            - [x] Clean install de jest 29.3.1 (root/front/back)
            - [x] Clean install de jest-environment-jsdom 29.3.1 (root/front/back)
            - [x] Update de ts-jest 29.0.0-next.1
            - [x] Les réinstallations suppriment le problème de jsdom mais causent l'erreur en front suivante :
            ```
            jest Component is not resolved: Did you run and wait for 'resolveComponentResources()'?
            ```
        - [ ] Résolution de jest Component is not resolved: Did you run and wait for 'resolveComponentResources()'?
            - [x] Sur suggestion de GPT, essai de : ```const componentFactory = (TestBed.inject(HomeCOPComponent).constructor as any)['ɵcmp'].ɵfac;``` & modifications, sans succès
            - [x] Sur suggestion de GPT, essai de : 
            ```
            const componentRef = componentFactory(null);
            await componentRef.resolveComponentResources(componentRef.hostView); // Wait for resources to be resolved
            await new Promise(resolve => setTimeout(resolve, 100)); // Add a small timeout
            component = componentRef.instance; // Create component instance after resources have been resolved
            fixture = TestBed.createComponent(HomeCOPComponent);
            fixture.detectChanges();
            ```
            mais sans succès
            - [x] Sur suggestion de GPT, essai de : 
            - [x] Sur suggestion de GPT, essai de : 
            ```
              TestBed.overrideComponent(HomeCOPComponent, {
                set: {
                templateUrl: './home-cop.component.html',
                styleUrls: ['./home-cop.component.css']
                }
            });
            ```
            - [x] Sur suggestion de GPT, essai de : ```CUSTOM_ELEMENTS_SCHEMA``` sans succès apparent. problème de config ?
            - [ ] Abandon pour downgrade à jest 29.0.0 ?
                - npm uninstall jest
                - npm uninstall @types/jest
                - npm uninstall ts-jest
                - npm uninstall jest-environment-jsdom
                - [x] npm uninstall front
                - [x] npm uninstall back
                - [x] npm uninstall root
                - [x] Delete des node modules front, back & root
                - [x] npm install jest@29.0.0 & dépendances
                - [x] 29.0.0 ne résout pas le problèmre
            - [ ] Downgrade à jest 28.1.0 
                - [x] Backup de l'application front dans le dossier bureau
                - [ ] Tout casser
                - [ ] Reprendre clean avec jest 28.1.0 et voir à partir de là
        - [ ] Lien avec les autres applis en fonction de qui est connecté
            - [ ] Réussir à afficher le rôle de l'user connecté
            - [ ] Afficher un lien vers l'app 4567 en fonction
- [ ] Ajouter un attribut store à chaque user de predict sur keycloak
    - [ ] Voir comment ça a été fait pour atmos
    - [ ] Lier à une autre base ?
    

**9 Mars**
- [ ] Passage au keycloak predict
    - [ ] Exporter les users en meme temps que le realm 
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Setup du repo
        - [x] Résolution de l'erreur jest Component is not resolved: Did you run and wait for 'resolveComponentResources()'?
            - [x] Copie des package json du github develop, collage, ```npm i``` & ```npm run test``` pour retomber sur le même problème
                - [x] Tout casser
                - [x] Désintallations dans root, front & back :
                ```
                npm uninstall jest-environment-jsdom
                npm uninstall jest
                npm uninstall jest-preset-angular
                npm uninstall @types/jest 
                ```
                - [x] Suppression des dossiers node_modules
                - [x] Réinstallations dans le dossier root :
                ```
                npm i jest@28.1.3
                npm i jest-environment-jsdom@28.1.3
                npm i jest-preset-angular@12.2.3
                npm i
                ```
                - [x] ```git reset --hard```
                - [x] ```npm i```
                - [x] ```npm run test``` : retour à l'erreur :
                ```
                Test suite failed to run                                           TypeError: Cannot read properties of undefined (reading 'testEnvironmentOptions')
                ```
        - [ ] Résolution de l'erreur TypeError: Cannot read properties of undefined (reading 'testEnvironmentOptions')
            - [x] Question à GPT : running jest tests in an angular application, i have this error : 
            Test suite failed to run                                    
            TypeError: Cannot read properties of undefined (reading 'testEnvironmentOptions')                        
            at new JSDOMEnvironment (../../node_modules/jest-environment-jsdom/build/index.js:63:28) 
            how can i solve it ?
            Retour chat : 
            ```
            This error typically occurs when Jest is unable to set up the JSDOM environment for testing. Here are some steps you can try to solve this issue:

            Make sure that you have installed the required dependencies for Jest to work with Angular. You can do this by running the following command:

            scss
            Copy code
            npm install --save-dev jest @types/jest jest-preset-angular @angular-builders/jest
            If you have recently upgraded Jest or any of its dependencies, try running the following command to clear the Jest cache:

            css
            Copy code
            npm run jest -- --clearCache
            If the above steps do not work, try updating the Jest configuration in your package.json file. Add the following lines to your Jest configuration:

            json
            Copy code
            "testEnvironment": "jest-environment-jsdom",
            "testEnvironmentOptions": {
            "resources": "usable"
            }
            This should tell Jest to use the JSDOM environment and allow it to access resources.

            If none of the above steps work, try deleting the node_modules folder and running npm install again.

            Hopefully, one of these steps will solve the issue and allow you to run Jest tests in your Angular application.
            ```
            - [x] Application des suggestions sans effet positif.
            - [x] ```git reset --hard```
            - [x] ```npm i```
    - [x] Downgrade à jest 27.5.1
        - [x] Désintallations dans root, front & back :
        ```
        npm uninstall jest-environment-jsdom
        npm uninstall jest
        npm uninstall jest-config
        npm uninstall ts-jest
        npm uninstall jest-preset-angular
        npm uninstall @types/jest 
        ```
        & cut dans le package.json front de :
        ```
        },
        "jest": {
            "preset": "jest-preset-angular",
            "setupFilesAfterEnv": [
            "<rootDir>/setup-jest.ts"
            ],
            "globalSetup": "jest-preset-angular/global-setup",
            "testEnvironment": "jest-environment-jsdom",
            "testEnvironmentOptions": {
            "resources": "usable"
            }
        ```
        - [x] Suppression des dossiers node_modules
        - [x] ```npm i jest@27.5.1```
        - [x] ```npm i jest-preset-angular@12.2.3``` renvoie l'erreur terminal : 
        ```
        npm ERR! ERESOLVE unable to resolve dependency tree
        npm ERR!
        npm ERR! While resolving: kmo-predict@0.0.0
        npm ERR! Found: jest@27.5.1
        npm ERR! node_modules/jest
        npm ERR!   jest@"^27.5.1" from the root project
        npm ERR!   jest@"^27.0.0" from kmo-predict-back@0.0.1
        ```
        - [x] ```npm uninstall jest``` dans le dossier back
        - [x] 
        ```
        npm i jest-preset-angular --legacy-peer-deps
        ``` 
    - [x] Jest 27 ne résoud pas l'erreur et créée des conflits de dépendances
        - [x] ```git reset --hard```
        - [x] Essai en retirant ```entryComponents: [AppComponent]``` et remettant  ```bootstrap: [AppComponent]``` mais sans effet notable
    - [ ] Reprise du develop clean
        - [x] ```git stash``` & ```git checkout develop```
        - [x] ```git pull origin develop```
        - [x] ```npm i``` , réussite avec retour terminal : 
        ```
        added 36 packages, changed 80 packages, and audited 2085 packages in 27s
        ```
        - [x] les tests passent (turbo test OK, 2 jobs sur 2)
        - [x] Recréation du fichier app.authguard.ts, ```npm i``` (turbo test OK, 2 jobs sur 2), 
        - [x] ```npm i keycloak-angular```, retour console : 
        ```
        npm ERR! code ERESOLVE
        npm ERR! ERESOLVE could not resolve
        npm ERR!
        npm ERR! While resolving: kmo-predict@0.0.0
        npm ERR! Found: @angular/core@15.1.5
        ```
        - [x] ```npm i keycloak-angular --legacy-peer-deps```, retour terminal : added 1 package, removed 10 packages, and audited 2076 packages in 3s
        - [x] ```npm i --legacy-peer-deps```, (turbo test OK, 2 jobs sur 2)
        - [x] Changements dans l'app-routing.module.ts : 
        ```
        { path: 'cop', canActivate: [AppAuthGuard], component: COPComponent },
        { path: 'home', canActivate: [AppAuthGuard], component: HomeCOPComponent },
        {path:'store/:id', canActivate: [AppAuthGuard], component: StoreComponent},
        ```
        & import. run des tests : (turbo test OK, 2 jobs sur 2)
        - [x] Changements dans l'app.component.ts, run des tests :
        ```
        FAIL  src/app/app.component.spec.ts
        ● AppComponent › should create the app

            NullInjectorError: R3InjectorError(DynamicTestModule)[KeycloakService -> KeycloakService]:
            NullInjectorError: No provider for KeycloakService!

        ● AppComponent › should have as title 'kmo-predict'

            NullInjectorError: R3InjectorError(DynamicTestModule)[KeycloakService -> KeycloakService]:
            NullInjectorError: No provider for KeycloakService!

        Test Suites: 1 failed, 4 passed, 5 total
        Tests:       2 failed, 4 passed, 6 total
        ```
        - [x] Résolution des tests qui fail, ajout de : 
        ```
        providers: [
            AppComponent,
            { provide: KeycloakService }
        ]
        ```
        dans le TestBed.configureTestingModule() du beforeEach()
        - [x] Run des tests, 1 seul fail : 
        ```
        ● AppComponent › should render title

        expect(received).toContain(expected) // indexOf

        Matcher error: received value must not be null nor undefined

        Received has value: undefined
        ```
        mise en comms pour plus tard
        - [x] ```ng serve``` et retour terminal : 
        ```
        error TS2307: Cannot find module '@angular/router' or its corresponding type declarations
        ```
        Recherches & ```npm i @angular/router --legacy-peer-deps```. Retour terminal : 
        ```
        Error: export 'ɵisNgModule' (imported as 'ɵisNgModule') was not found in '@angular/core' (possible exports: ......)
        ```
        - [x] Résolution de l'erreur export 'ɵisNgModule'  was not found in '@angular/core'
            - [x] Check de (https://stackoverflow.com/questions/56433781/export-%C9%B5%C9%B5inject-was-not-found-in-angular-core) & (https://github.com/angular/angular/issues/30413)
            - [x] Run de : 
            ```
            npm install -S @angular/material @angular/cdk @angular/animations --legacy-peer-deps
            npm uninstall @angular/core --legacy-peer-deps>=27.0.0
            npm install -S @angular/core --legacy-peer-deps
            ```
        - [x] État actuel : ng serve run sans erreurs, et les tests passent, MAIS reach le port ne fait pas passer par l'auth keycloak
        - [x] Application des modifications de mardi
        - [x] Push et PR
        - [x] Résolution linting :
            - app.module.ts : changement de ngDoBootstrap en DoBootstrap
            - app.authguard.ts : suppression de ```state: RouterStateSnapshot``` et de ses imports
            - app.component.ts : suppression de ```type usersRoles = Array<{id: number, text: string}>```
        - [x] Modif de la ci avec legacy-peer-deps
        - [x] Re-commit, repush et PR
    - [x] Suppression de la branche KP_104
    - [x] Reprise du code pas à pas en vérifiant les tests à chaque étape

**10 Mars**
- [ ] Passage au keycloak predict
    - [x] Suppression de la branche foireuse KP-104 locale
    - [ ] Exporter les users en meme temps que le realm 
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Ajout d'un lien vers 4567 en fonction du role de l'user
        - [ ] Récupération et affichage du role de l'user connecté
        - [x] Check de (https://blog.strongbrew.io/display-a-component-based-on-role/) & (https://angular.io/guide/structural-directives) sans trouver ce que je cherche
        - [x] Ajout dans le html de l'app de : 
        ```
        <div>
          <ng-container *ngIf="userProfile">
              <div *ngIf="userProfile.role">
                <span>{{ userProfile.role }}</span>
              </div>
          </ng-container>
        </div>
        ```
        mais sans succès, le build ne se fait pas puisque la valeur userProfile.role n'existe pas
        - [x] Check du fichier de déclaration de l'objet keycloak.d.ts avec ctrl+clic sur KeycloakService dans l'app.component.ts
        - [x] Ajout à l'app.component.ts de :
        ```
        public userRole: KeycloakRoles | null = null;
        ```
        et ajout de KeycloakRoles dans l'import en début de fichier
        - [x] Check du role de l'user connecté au moyen de :
        ```
        if (this.isLogged) {
            this.userProfile = await this.keycloak.loadUserProfile();
            console.log(this.userProfile);      
            console.log(this.userRole);      
        }
        ```
        qui retourne ```null```
        - [x] Ajout de role
            - [x] Check dans la console keycloak de .../users/user/role-mappings & déroulé du dropdown, sélection du Client Role : KMO_Predict pour constater qu'il n'y a pas de roles dedans. Conclusion : **realm role =/= client role**
            - [x] Création de roles pour le client KMO_Predict
                - [x] Création du role  admin
                - [x] Création du role  base_user
    - [ ] Set d'un attribut store et lien avec users
        - [x] Positionnement en local sur develop et pull origin
        - [ ] Création d'une nouvelle branche
        - [ ] Création d'un attribut store dans le keycloak
        - [ ] Update du code pour autoriser un user à accéder à un seul store