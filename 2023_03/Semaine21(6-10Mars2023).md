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
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Setup du repo
        - [x] Mise en place l'authguard
        - [ ] Résolution des problèmes de versions jest-jsdom pour faire passer les tests
        - [ ] Ajouter au fichier app.component.html le bouton de déconnexion
        - [ ] Ajouter au fichier app.component.html les infos user dans la topbar
        - [ ] Lien avec les autres applis en fonction de qui est connecté
            - [ ] Réussir à afficher le rôle de l'user connecté
            - [ ] Afficher un lien vers l'app 4567 en fonction
- [ ] Ajouter un attribut store à chaque user de predict sur keycloak
    - [ ] Voir comment ça a été fait pour atmos
    - [ ] Lier à une autre base ?