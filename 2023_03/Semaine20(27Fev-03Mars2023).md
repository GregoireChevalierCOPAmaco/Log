**27 Février**
- [ ] Poursuite démarrage du KMO-Predict
    - [ ] Réunion de catch-up avec Guillaume et Théo sur l'intégration des metadatas des gateways dans la vue cop
    - [x] Initialisations
        - [x] Résolution du passage des tests autogénérés
            - [x] Relance des tests front : tout passe
            - [x] Relance des tests back : tout passe
        - [x] Pull develop et màj du projet en local
            - [ ] Résolution des conflits
            - [x] package.json /front
            - [x] tests.ts /front
            - [x] angular.json
            - [x] package-lock.json /racine
                - [x] Accepted all current
                - [x] Deleted 46 duplicate object key
            - [x] màj des imports foireux (mat au lieu de matlegacy)
            - [x] main.ts /back, supression de l'async de startallmicroservicesasync()
            - [x] push sur github & checks successful
            - [x] test de viabilité du front
                - [x] Front ok avec ng serve dans le dossier front
                - [x] Front ok avec docker-compose dans le dossier racine
        - [x] Merge de la branche KP-90
        - [ ] Delete de la branche KP-90
- [x] Réunion followup
- [x] Intégrer les tests à la CI githubactions
    - [x] Lancement de ```turbo run test``` et retour positif : 
    ```
    Tasks:    2 successful, 2 total
    Cached:    0 cached, 2 total
    Time:    11.285s
    ```
    - [x] Modifications sur le fichier de CI
    - [x] Suppression du --no-verify du package json racine
    - [x] Commit
    - [x] Mise sur le github distant
    - [x] Passage des checks
    - [x] Suppression du --no-verify dans le root package.json
- [ ] Check de (https://www.npmjs.com/package/testcontainers) pour veille techno
- [ ] Réparer le tailwind sur la maquette angular de kmo predict
    - [x] Check de (https://stackoverflow.com/questions/71384038/many-tailwind-css-classes-doesn%C2%B4t-work-on-my-angular-12-project)
- [x] Rouvrir le projet atmos pour remise en tête de keycloak
- [x] Rouvrir le projet githubactions perso pour remise en tête de keycloak
- [ ] Se renseigner sur websocket.io
- [ ] Checker la directive specification de doc de Guillaume sur le discord
- [ ] Checker l'état de l'entité store créée par Théo


**28 Février**
- [x] Journée formation avec Olivier ITS
    - [x] Présentation & résumé des projets en cours et à venir
    - [x] Keycloak : basiques
        - [x] Oublier le passage par l'interface keycloak
        - [x] Passer par l'authguard :
        ```
        keycloakService
        .init({
            config: {
            url: 'http://localhost:8080',  <= retrait du /auth
            realm: 'angular-keycloak-postgresRealm',
            clientId: 'apptest4200',
        ```
        - [x] Pré-création de tables en base de données avec flyway
        - [x] Roles : attributs. Variables - c'est l'authguard qui va, en fonction du token généré avec le role de l'user, permettre l'accès à une app ou non
        - [x] Annuaire Ldap permet d'autoriser un compte windwos/google d'accéder à une app sans avoir besoin de créer un user keycloak (identity providers & user federations)
        - [x] Thèmes customizables selon l'user connecté ?
        - [x] Client scope : tout ce que l'app (client) a le droit de demander pour entre autres permettre l'authentification en fonction du user en question
        - [x] Client settings/ Capability config = définition de si on sort ou pas de l'app/du browser via mobile ou pas
        - [x] Voir keycloak admin api pour donner la possibilité de créer des users depuis les applications
        - [x] Import keycloak en spécifiant le chemin d'accès dans le docker compose /keycloak/volume, comme suit dans le atmos 2 :
        ```
         - ../keycloak/copamaco-realm-export.json:/opt/keycloak/data/import/copamaco-realm-export.json:ro
        ```
        export-import de realm mieux pour les users (pw hashés & permissions, toussa)
    - [x] Keycloak : prise en main atmos
        - [x] Lancement de l'application en dev
            - [x] Atmos2, ```cd deploy``` & ``` docker compose --project-name atmos --env-file .env.dev up -d```
            - [x] Convertir les fichiers ./db/00_init.sh & ./keycloak/entrypoint & tous les fichiers de ./flyway/sql/ en fin de ligne LF pour éviter de mal interpréter les fins de lignes entre linux & windows
            - [x] Édition des fichiers hôte pour ajouter 127.0.0.1 - auth.copamaco.local et expoer le port voulu
            - [x] À savoir : l'image angular doit être construite en prenant en compte l'environnement keycloak cible (si -> prod (défaut) et qu'on cherche à taper sur dev, en local on aura une 403 unauthorized)
            - [x] Lancement front & base à la main et keycloak avec docker
            - [x] Atm, l'application atmos acepte tout user loggé qui possède un attribut magasin
            - [x] entrypoint.sh : on précise l'import du realm dans ce fichier
    - [x] Demi journée non prise à replanifier
- [ ] Reprise du projet perso keycloak
    - [ ] Accéder à chaque appli séparément selon l'user connecté
        - [ ] App4200
            - [ ] Configurer l'auhtguard pour prendre en compte les user roles
        - [ ] App4567
            - [ ] Ajouter l'authguard
                - [x] Suivi de (https://github.com/mauriciovigolo/keycloak-angular/blob/main/README.md)
                - [x] ```npm install keycloak-angular keycloak-js``` dans le dossier ./angulartestapp, retour positif :
                ```
                added 3 packages, removed 1 package, and audited 887 packages in 3s

                87 packages are looking for funding
                run `npm fund` for details

                found 0 vulnerabilities
                ```
                - [x] Modification de l'app.module.ts :
                ```
                import { APP_INITIALIZER,NgModule } from '@angular/core';
                import { BrowserModule } from '@angular/platform-browser';

                import { KeycloakAngularModule, KeycloakService } from 'keycloak-angular';
                import { AppRoutingModule } from './app-routing.module';
                import { AppComponent } from './app.component';

                function initializeKeycloak(keycloak: KeycloakService) {
                return () =>
                    keycloak.init({
                    config: {
                        url: 'http://localhost:8080',
                        realm: 'angular-keycloak-postgresRealm',
                        clientId: 'apptest4567'
                    },
                    initOptions: {
                        onLoad: 'check-sso',
                        silentCheckSsoRedirectUri:
                        window.location.origin + '/assets/silent-check-sso.html'
                    }
                    });
                }

                @NgModule({
                declarations: [
                    AppComponent
                ],
                imports: [
                    BrowserModule,
                    AppRoutingModule,
                    KeycloakAngularModule
                ],
                providers: [
                    {
                    provide: APP_INITIALIZER,
                    useFactory: initializeKeycloak,
                    multi: true,
                    deps: [KeycloakService]
                    }
                ],
                bootstrap: [AppComponent]
                })
                export class AppModule { }
                ```
                - [x] Création d'un fichier ./angulartestapp/assets/silent-check-sso.html rempli comme suit :
                ```
                <html>
                    <body>
                        <script>
                        parent.postMessage(location.href, location.origin);
                        </script>
                    </body>
                </html>
                ```
                - [x] Création d'un fichier ./angulartestapp/src/app/app.authguard.ts rempli comme suit : 
                ```
                import { Injectable } from '@angular/core';
                import {
                ActivatedRouteSnapshot,
                Router,
                RouterStateSnapshot
                } from '@angular/router';
                import { KeycloakAuthGuard, KeycloakService } from 'keycloak-angular';

                @Injectable({
                providedIn: 'root'
                })
                export class AuthGuard extends KeycloakAuthGuard {
                constructor(
                    protected override readonly router: Router,
                    protected readonly keycloak: KeycloakService
                ) {
                    super(router, keycloak);
                }

                public async isAccessAllowed(
                    route: ActivatedRouteSnapshot,
                    state: RouterStateSnapshot
                ) {
                    // Force the user to log in if currently unauthenticated.
                    if (!this.authenticated) {
                        await this.keycloak.login({
                            redirectUri: window.location.origin + state.url
                        });
                        }

                        // Get the roles required from the route.
                        const requiredRoles = route.data['roles'];

                        // Allow the user to proceed if no additional roles are required to access the route.
                        if (!(requiredRoles instanceof Array) || requiredRoles.length === 0) {
                        return true;
                        }

                        // Allow the user to proceed if all the required roles are present.
                        return requiredRoles.every((role) => this.roles.includes(role));
                    }
                }
                ```
            - [ ] Configurer l'auhtguard pour prendre en compte les user roles

    
**1er Mars**
- [ ] Passage au keycloak predict
    - [x] ```git status```, commit de la branche locale authguard 4567
    - [x] Passage sur develop et ```git pull origin develop``` pour être à jour
    - [x] Création d'une story dans  l'epic keycloak KP-5 jira
    - [x] Création d'un repo keycloak
        - [x] Clone en local
        - [x] Récupération de keycloak sur (https://www.keycloak.org/downloads)
        - [x] Check de la version (19.0.2) du keycloak atmos utilisé par ITS
        - [x] Récupération de keycloak v.18.0.0, dernière version avant dépréciation du client adapter pour Node.Js
        - [x] Téléchargement, copie, collage dans le dossier repo local de l'archive
        - [ ] Extraction de l'archive avec ```bin/kc.bat start-dev```, retour console : 
        ```
        bin/kc.bat : Le terme «bin/kc.bat» n'est pas reconnu comme nom d'applet de commande, fonction, fichier de script ou programme exécutable. Vérifiez l'orthographe du nom, ou si un chemin d'accès existe, vérifiez que le chemin d'accès 
        est correct et réessayez.
        ```
        Puis extraction au clic droit, mais pas nécessaire
        - [ ] Reprise du modèle templategithubactions de docker-compose.yml & adaptation pour lancement au plus simple :
        ```
        version: "3.9"
        volumes:
        postgres_data:
            driver: local

        services:

        postgres:
            container_name: postgresdb-container
            image: postgres:11
            volumes:
            - postgres_data:/var/lib/postgresql/data
            environment:
            POSTGRES_DB: keycloak
            POSTGRES_USER: keycloak
            POSTGRES_PASSWORD: pw123
            # volumes:
            #   - type: volume
            #     source: postgresdb-container
            #     target: /var/lib/postgresql/data
            ports:
            - "5432:5432"

        keycloak:
            container_name: keycloak-container
            depends_on:
            - postgres
            image: quay.io/keycloak/keycloak:18.0.0
            command:
            # - start-dev
            - start-dev --spi-login-protocol-openid-connect-legacy-logout-redirect-uri=true
            - -Dkeycloak.migration.strategy=IGNORE_EXISTING
            ports:
            - 8080:8080
            environment:
            DB_VENDOR: POSTGRES
            DB_ADDR: postgres
            DB_DATABASE: keycloak
            DB_USER: keycloak
            DB_SCHEMA: public
            DB_PASSWORD: password
            KEYCLOAK_USER: jojo
            KEYCLOAK_PASSWORD: pw123
            KEYCLOAK_LOGLEVEL: DEBUG
            ROOT_LOGLEVEL: DEBUG
            KEYCLOAK_ADMIN: admin
            KEYCLOAK_ADMIN_PASSWORD: admin
            KEYCLOAK_IMPORT: /tmp/my-realm.json
            # KEYCLOAK_LOGLEVEL: DEBUG
            PROXY_ADDRESS_FORWARDING: 'true'
            KEYCLOAK_FRONTEND_URL: https://proxy/auth
            ```
    - [x] ```docker compose up```, et retour erreur console : 
    ```
    [+] Running 5/5
    - keycloak 
    Pulled
    - 4752687a61a9 Pull complete
    - 0344366a246a Pull complete
    - bf8a68204bbb Pull complete
    - c4db0a36467a Pull complete
    [+] Running 2/2
    - Network kmo_sass_keycloak_default         Created
    - Volume "kmo_sass_keycloak_postgres_data"  Created
    - Container postgresdb-container
    Error response from daemon: Conflict. The container name "/postgresdb-container" is already in use by container "f9d57bece05056fc1f1674d1502af9535de665fde277892197a59d5364156923". You have to remove (or rename) that container to be able to reuse that name.
    ```
    - [x] Changement du nom des containers en postgresdb-sass-container && keycloak-sass-container
    - [ ] Setup du realm
        - [x] Création du realm
            - [x] Cop_sass/realm settings/login : activation de :
                - Edit username
                - Forgot password
                - Remember me
                - Verify email
                - Login with email
        - [x] Création des roles
            - [x] Création du rôle cop superadmin
            - [x] Création du rôle cop developer
            - [x] Création du rôle kmo storeManager
            - [x] Création du rôle kmo regionalDirection
            - [x] Création du rôle kmo kmo-predict_admin
        - [ ] Création groups
            - [x] Création du groupe base_user avec tous les roles
            - [x] Création du groupe keycloak user avec les roles cop & kmo-predict_admin
            - [x] Création du groupe kmo-predict_user avec kmo store manager seulement
            - [x] Création du groupe kmo-predict_DR_user avec le role kmo DR
        - [x] Lier roles et groupes
        - [x] Création des users
            - [x] Création du user g.chevalier avec groupes assignés base_user & keycloak user, et implicitement rôles cop_developer, cop_super_user & les kmo-predict
            - [x] Création du user superadmin avec tous les roles assignés sauf les kmo predict
            - [x] Création du user j.random avec le seul role default-roles-cop_sass
            - [x] Création du user predictuser1 avec le seul groupe kmo-predeict_user et donc le seul role kmo-predict_storeManager
            - [x] Création du user predictDR1 avec le seul groupe kmo-predict_DR et donc le seul role kmo-predict_regionalDirection
        - [x] Création d'un client predict
        - [x] Création d'un client test 4567
        - [x] Lien du client kmo predict au port 4200, lancement ng serve dans autre fenetre ide, accès au port 4200 et page vierge, erreur console : 
        ```
        Refused to frame 'http://localhost:8080/' because an ancestor violates the following Content Security Policy directive: "frame-ancestors 'self'".
        ```
        - [x] Check de (https://stackoverflow.com/questions/68509296/keycloak-js-initialization-failed-because-an-ancestor-violates-the-content-secur)
        - [x] Lancer depuis le bon dossier (app-angular-keycloak) ça aide ....
    - [ ] Export du realm
        - [x] L'export de base ne prend pas en compte les users.
        - [x] Interrogation de GPT sur le sujet :
        ```
        Unfortunately, if the option to export users is not available in the Keycloak administration console, there is no built-in way to export the realm and its users via the console alone.

        However, there are a couple of options to consider:

        Use the Keycloak REST API: You can use the Keycloak REST API to export the realm and its users. The API provides an endpoint to export a realm configuration, including its users. You can use tools like curl or a programming language library to make the API calls.

        Use a third-party tool: There are third-party tools available that can help you export Keycloak realms and users. One such tool is the Keycloak Exporter, which is a command-line tool that can export Keycloak realms and users.

        Note that if you decide to use a third-party tool, make sure to carefully review the tool's documentation and ensure that it is compatible with your Keycloak version. Also, make sure to follow security best practices when exporting and handling sensitive user data.


        g.chevalier@cop-amaco.com
        How can i export a keycloak realm including its users using the Keycloak REST API ?
        To export a Keycloak realm including its users using the Keycloak REST API, you can use the /admin/realms/{realm-name}/partial-export endpoint. Here are the general steps to follow:

        Get an access token: You'll need an access token to authenticate your API requests. You can get an access token using the Keycloak API's /auth/realms/{realm-name}/protocol/openid-connect/token endpoint, passing in your client ID, client secret, and desired scopes.

        Call the partial-export endpoint: Make an HTTP POST request to the /admin/realms/{realm-name}/partial-export endpoint, passing in your access token in the Authorization header, and specifying the desired export format (JSON or ZIP) and optional export options, such as including user data.

        Here's an example cURL command that exports a Keycloak realm named my-realm in JSON format, including user data:

        curl -X POST \
        'https://keycloak.example.com/auth/admin/realms/my-realm/partial-export?export-users=true' \
        -H 'Authorization: Bearer <access_token>' \
        -H 'Content-Type: application/json' \
        -o my-realm-export.json
        In this example, replace <access_token> with your access token, and my-realm-export.json with the desired output file name. You can modify the export options, such as changing the export format, by adjusting the query parameters in the URL.

        Note that the exported user data may contain sensitive information, so make sure to follow security best practices when handling the exported data.
        ```
    - [ ] Trouver le moyen d'exporter les users en meme temps que le realm ?
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Setup du repo
        - [ ] Setup l'app 4567 avec l'adapter
            - [x] Reprise fonctionnelle de l'app 4567
            - [ ] Suivi de : (https://www.npmjs.com/package/keycloak-angular?activeTab=readme)
        - [ ] Mettre en place l'authguard
        - [ ] Gérer les permissions des différents rôles (via angular)
- [x] Pour la suite du projet, le code devra être jsDoc-é. Suivre : (https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html#param-and-returns) && (https://stackoverflow.com/questions/34220564/is-there-a-way-to-generate-jsdoc-comments-in-visual-studio-code)


**2 Mars**
- [ ] Passage au keycloak predict
    - [ ] Trouver le moyen d'exporter les users en meme temps que le realm ?
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Setup du repo
        - [ ] Setup l'app 4567 avec l'adapter
            - [x] Reprise fonctionnelle de l'app 4567
            - [ ] Suivi de : (https://www.npmjs.com/package/keycloak-angular?activeTab=readme) et lecture en entier avant d'attaquer
        - [ ] Mise en place l'authguard
            - [x] Suivi de : 
            ```
            A best practice is to load the JavaScript adapter directly from Keycloak Server as it will automatically be updated when you upgrade the server.
            If you copy the adapter to your web application instead, make sure you upgrade the adapter only after you have upgraded the server.
            ```
            - [x] Installation avec ```npm install keycloak-angular js@18.0.0 --save``` et retour d'erreur :
            ```
            npm ERR! code ETARGET
            npm ERR! notarget No matching version found for js@18.0.0.
            npm ERR! notarget In most cases you or one of your dependencies are requesting
            npm ERR! notarget a package version that doesn't exist.
            ```
            - [x] Installation avec ```npm install keycloak-angular keycloak-js@18.0.0 --save```, retour positif : ```added 3 packages, removed 1 package, and audited 887 packages in 1s```
            - [x] Modification de l'app.module.ts comme suit : 
            ```
            import { APP_INITIALIZER, NgModule } from '@angular/core';
            import { BrowserModule } from '@angular/platform-browser';
            import { KeycloakAngularModule, KeycloakService } from 'keycloak-angular';
            import { AppRoutingModule } from './app-routing.module';
            import { AppComponent } from './app.component';

            function initializeKeycloak(keycloak: KeycloakService) {
            return () =>
                keycloak.init({
                config: {
                    url: 'http://localhost:8080',
                    realm: 'Cop-sass',
                    clientId: 'apptest4567'
                },
                initOptions: {
                    onLoad: 'check-sso',
                    silentCheckSsoRedirectUri:
                    window.location.origin + '/assets/silent-check-sso.html'
                }
                });
            }


            @NgModule({
            declarations: [
                AppComponent
            ],
            imports: [
                BrowserModule,
                AppRoutingModule,
                KeycloakAngularModule
            ],
            providers: [
                {
                provide: APP_INITIALIZER,
                useFactory: initializeKeycloak,
                multi: true,
                deps: [KeycloakService]
                }
            ],
            bootstrap: [AppComponent]
            })
            export class AppModule { }
            ```
            - [x] Création du fichier ./src/app/assets/silent-check-sso.html rempli comme suit :
            ```
            <html>
                <body>
                    <script>
                    parent.postMessage(location.href, location.origin);
                    </script>
                </body>
            </html>
            ```
            Check (https://www.keycloak.org/docs/latest/securing_apps/#_javascript_adapter) pour plus de détail
            - [x] Retour en arrière, reset de l'app.module.ts
            - [x] Création d'un fichier de configuration keycloak
                - [x] Selon GPT, :
                ```
                You can create the Keycloak configuration file anywhere in your Angular project, but a common convention is to create it in the assets folder. This folder is typically used to store static files that are served as-is by the web server, without being processed by Angular's build system.

                To create the configuration file in the assets folder, follow these steps:

                In the root of your Angular project, create a new folder called assets if it doesn't exist already.

                Inside the assets folder, create a new file called keycloak.json.

                Copy and paste your Keycloak configuration settings into the keycloak.json file, like so:

                {
                "realm": "your-realm",
                "url": "https://your-keycloak-server.com/auth",
                "clientId": "your-angular-app-client-id"
                ...
                }
                ```
                - [x] Création du fichier ./src/app/assets/keycloak.json
                - [x] Remplissage comme suit :
                ```
                {
                    "url": "http://localhost:8080",
                    "realm": "Cop_sass",
                    "clientId": "apptest4567"
                }
                ```
                - [x] Création du fichier ./src/app/keycloak/keycloak.service.ts
                - [x] Remplissage comme suit :
                ```
                import Keycloak from 'keycloak-js';
                import { HttpClient } from '@angular/common/http';
                import { Injectable } from '@angular/core';

                @Injectable({
                providedIn: 'root',
                })

                export class KeycloakService{
                    
                    constructor(private http: HttpClient) { }

                    public init(): Promise<any> {
                        return new Promise((resolve, reject) => {
                        this.http.get<any>('assets/keycloak.json').toPromise()
                            .then(config => {
                            const keycloakAuth = new Keycloak(config);
                            keycloakAuth.init({ onLoad: 'login-required' })
                                .then(() => {
                                resolve();
                                })
                                .catch(() => {
                                reject();
                                });
                            });
                        });
                    }
                    
                }
                ```
                , avec retour erreur terminal : 
                ```
                Expected 1 arguments, but got 0. Did you forget to include 'void' in your type argument to 'Promise'?
                ```
                - [x] Erreur résolue en changeant le ```Promise<any> ``` en ```Promise<void>```
                - [x] Modification du fichier app.component.ts comme suit :
                ```
                import { Component, OnInit } from '@angular/core';
                import { KeycloakService } from './keycloak/keycloak.service';

                @Component({
                selector: 'app-root',
                templateUrl: './app.component.html',
                styleUrls: ['./app.component.css']
                })
                export class AppComponent implements OnInit {

                    constructor(private keycloakService: KeycloakService) { }

                    ngOnInit() {
                        this.keycloakService.init();
                    }
                }
                ``` 
            - [ ] Implémentation générique de l'Authguard
            - [ ] Setup de l'HTTP Inerceptor
        - [ ] Gérer les permissions des différents rôles (via angular)


**3 Mars**
- [ ] Passage au keycloak predict
    - [ ] Trouver le moyen d'exporter les users en meme temps que le realm ?
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Setup du repo
        - [x] Setup l'app 4567 avec l'adapter
        - [x] Mise en place l'authguard
            - [x] Résolution de l'erreur console browser : "localhost/:1 Access to XMLHttpRequest at 'http://localhost:8080/realms/Cop_sass/protocol/openid-connect/token' from origin 'http://localhost:4567' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource."
                - [x] Mail à Olivier, réponse : aller voir pour la configuration du web origins dans l'admin console keycloak
                - [x] Check de (https://stackoverflow.com/questions/59018604/keycloak-no-access-control-allow-origin-header-is-present-on-the-requested-r)
            - [x] Résolution de l'erreur console browser : "[ngDoBootstrap] init Keycloak failed undefined"
        - [x] Résultat : eh bah la putaindlameredesémooooorts il a suffit de renseigner le champ Web Origins avec : "*".
        C'est tout.
        - [ ] Setup l'app maquette predict avec l'adapter
            - [x] ```git pull origin develop``` && ```git checkout -b feature/KP-104_link_to_maquette```
            - [x] ```cd apps\kmo-predict-front``` && ```npm install keycloak-angular keycloak-js```, retour console : 
            ```
            added 4 packages, and audited 2073 packages in 4s
            ```
            - [x] Ajout dans l'app.module.ts de :
            ```
            import { KeycloakAngularModule, KeycloakService } from 'keycloak-angular';
            import { RouterModule } from '@angular/router';
            import { AppAuthGuard } from './app.authguard';

            const keycloakService = new KeycloakService();
            ```
            & déclaration dans les imports : []
            & 
            ```
            providers: [AppAuthGuard, {
                provide: KeycloakService,
                useValue: keycloakService
            }]
            ```
            - [x] Modification de l'app.component.ts comme suit : 
            ```
            import { Component, OnInit } from '@angular/core';
            import { KeycloakService } from 'keycloak-angular';
            import { KeycloakProfile } from 'keycloak-js';

            @Component({
            selector: 'app-root',
            templateUrl: './app.component.html',
            styleUrls: ['./app.component.css']
            })
            export class AppComponent implements OnInit {
            
                title = 'app-angular-keycloak';
                
                public isLogged = false;
                public userProfile: KeycloakProfile | null = null;

                constructor(private readonly keycloak: KeycloakService) { }

                public async ngOnInit(): Promise<void>{
                    this.isLogged = await this.keycloak.isLoggedIn();
                    type usersRoles = Array<{id: number, text: string}>

                    if (this.isLogged) {
                    this.userProfile = await this.keycloak.loadUserProfile();
                    }
                }

                public initSession(){
                    this.keycloak.login();
                }

                public closeSession(){
                    this.keycloak.logout();
                }

            }
            ```
            - [ ] Mise en place l'authguard
                - [x] Création du fichier app.authguard.ts
                - [x] Ajout à chaque route du fichier app-routing.module.ts de : 
                ```
                            , 
                canActivate: [AppAuthGuard],
                data: { roles: ['user'] }
                ```
                & déclaration de l'import de l'Authguard
            - [ ] Ajouter au fichier app.component.html les bouton de déconnexion & infos user
            - [ ] Faire en sorte que le username = email
    - [ ] Lien avec les autres applis en fonction de qui est connecté
        - [ ] Réussir à afficher le rôle de l'user connecté
        - [ ] Afficher un lien vers l'app 4567 en fonction