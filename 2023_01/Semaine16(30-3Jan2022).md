**30 Janvier**
- [ ] Objectif du jour : réaliser un lancement opérationnel de docker/keycloak/postgres/angular depuis un docker-compose
- [ ] Accéder à l'application angular depuis un protail keycloak
    - [x] Démarrage du container keycloak en local
    - [ ] Accéder aux outils d'administration
        - [ ] Résolution du problème d'identifiants incorrects/user not found
            - [x] Check de (https://keycloak.discourse.group/t/keycloak-17-impossible-to-create-initial-admin-user/14474) & (https://keycloak.discourse.group/t/keycloak-v18-unable-to-get-first-admin-user-trough-environement-variables/15352)
            - [x] Suppression du container docker & son image
            - [x] Dans le fichier docker-compose, changement de :
            ```
            environment:
                KEYCLOAK_USER: admin
                KEYCLOAK_PASSWORD: admin
            ```
            en : 
            ```
            environment:
                KC_ADMIN: admin
                KC_ADMIN_PASSWORD: admin
            ```
            - [x] Rebuild de l'image avec ```docker build -t templategithubactionskeycloak -f ./docker/Dockerfile.keycloak .```
        - [x] Relance de docker compose up, sans effet.
        - [x] Check de (https://keycloak.discourse.group/t/docker-compose-with-keycloak-and-postgres/8964/2) et adaptation du docker-compose :
        ```
        version: "3.9"
            volumes:
            postgres_data:
                driver: local

            services:

            postgres:
                container_name: postgresdb-container
                # image: templategithubactionspostgresql
                image: postgres:11
                volumes:
                - postgres_data:/var/lib/postgresql/data
                environment:
                POSTGRES_DB: keycloak
                POSTGRES_USER: keycloak
                POSTGRES_PASSWORD: pw123
                ports:
                - "5432:5432"

            keycloak:
                container_name: keycloak-container
                depends_on:
                - postgres
                image: quay.io/keycloak/keycloak:latest
                # image: templategithubactionskeycloak
                command:
                - start-dev
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
                PROXY_ADDRESS_FORWARDING: 'true'
                KEYCLOAK_FRONTEND_URL: https://proxy/auth

            angular:
                container_name: angular-container
                image: templategithubactionsangular
                build:
                context: app-angular-keycloak
                command: sh -c "cd app-angular-keycloak && npm link @angular/cli && ng serve --host 0.0.0.0
                ports: 
                - 4200:4200
        ```
        - [x] Relance de docker compose up : les logs montrent ```INFO  [org.keycloak.services] (main) KC-SERVICES0009: Added user 'admin' to realm 'master'``` et le keycloak admin est fonctionnel & atteignable.
        Moralité : On a juste besoin de dockeriser l'app angular, postgres & keycloak sont des images templates de base.
    - [ ] Mise en commun de la base de données avec keycloak
        - [ ] Survol du log pour voir comment avoir accès à l'app angular depuis keycloak
            - [x] Création d'un user via l'admin keycloak:
            g.chevalier
            pw123
            - [x] Création d'un user via l'admin keycloak:
            t.test
            pw123
            - [x] Connection à http://localhost:8080/realms/angular-keycloak-postgresRealm/account/#/ avec les ids : accès au profil OK
            - [x] Essai d'accès au projet angular depuis https://www.keycloak.org/app/ avec les ids :
            keycloak url : http://localhost:8080
            realm : angular-keycloak-postgresRealm
            client : angular-keycloak-postgresRealm
            Puis sign in : 
                ```
                We are sorry...
                Invalid parameter: redirect_uri
                « Back to Application
                ```
            - [x] Reprise des champs avec les bonnes infos :
            keycloak url : http://localhost:8080
            realm : angular-keycloak-testRealm
            client : app-angular-keycloak,
            même résultat mais le lien << Back to applicaiton mène bien à l'app angular 4200
        - [ ] Sécurisation de l'application angular avec keycloak
            - [x] Check de (https://medium.com/keycloak/secure-angular-app-with-keycloak-63ec934e5093)
            - [ ] Suivi des instructions 
                - [x] Création d'une branche sur le projet git TemplateGitHubActions : ```git checkout -b testKeycloakJs```
                - [x] Installation de keycloak-js :
                ```
                cd .\app-angular-keycloak\
                npm i keycloak-js --save
                ```
                - [x] Ajout dans le tsconfig.ts de :
                ```
                "allowSyntheticDefaultImports": true,
                ```
                à la rubrique compilerOptions
                Trop d'erreurs, pas assez d'angles de résolution.
               - [x] Check de (https://plainenglish.io/blog/secure-an-angular-single-page-application-with-keycloak-cdbe5026881e) 
            - [ ] Suivi des instructions 
                - [x] Création d'un fichier keycloak.config.ts & remplissage comme suit :
                ```
                import { KeycloakConfig } from "keycloak-js";

                const keycloakConfig: KeycloakConfig = {
                    url: 'http://localhost:4200/',
                    realm: 'angular-keycloak-postgresRealm',
                    clientId: 'app-angular-keycloak-postgres'
                };

                export default keycloakConfig;
                ```
                - [x] Création d'un module login avec ```ng generate module login```
                - [x] Création d'un fichier  keycloak-initializer.ts rempli comme suit :
                ```
                import { KeycloakOptions, KeycloakService } from "keycloak-angular";

                export function initializer(keycloak: KeycloakService): () => Promise<boolean> {
                        const options: KeycloakOptions = {
                            // config: :
                            loadUserProfileAtStartUp:true,
                            initOptions: {
                                onLoad: 'check-sso',
                                checkLoginIframe: false
                            },
                            bearerExcludedUrls: []
                        };

                        return () => keycloak.init(options);
                }
                ```
                - [x] Création d'un service login avec ```ng generate service login/service/Login``` et remplissage comme suit :
                ```
                import { Injectable } from '@angular/core';
                import { KeycloakService } from 'keycloak-angular';
                import { KeycloakProfile, KeycloakTokenParsed } from 'keycloak-js';

                @Injectable({
                providedIn: 'root'
                })
                export class LoginService {

                constructor(private keycloakService: KeycloakService) {}

                public getLoggedUser(): KeycloakTokenParsed | undefined {
                    try {
                    const userDetails: KeycloakTokenParsed | undefined = this.keycloakService.getKeycloakInstance()
                        .idTokenParsed;
                    return userDetails;
                    } catch (e) {
                    console.error("Exception", e);
                    return undefined;
                    }
                }

                public isLoggedIn() : Promise<boolean> {
                    return this.keycloakService.isLoggedIn();
                }

                public loadUserProfile() : Promise<KeycloakProfile> {
                    return this.keycloakService.loadUserProfile();
                }

                public login() : void {
                    this.keycloakService.login();
                }

                public logout() : void {
                    this.keycloakService.logout(window.location.origin);
                }

                public redirectToProfile(): void {
                    this.keycloakService.getKeycloakInstance().accountManagement();
                }

                public getRoles(): string[] {
                    return this.keycloakService.getUserRoles();
                }

                }
                ```
                - [x] Création d'un fichier  login.guard.ts rempli comme suit :
                ```
                import { Injectable } from '@angular/core';
                import { ActivatedRouteSnapshot, RouterStateSnapshot, Router, UrlTree } from '@angular/router';
                import { KeycloakAuthGuard, KeycloakService } from 'keycloak-angular';

                @Injectable()
                export class LoginGuard extends KeycloakAuthGuard {

                constructor(protected override router: Router, protected override keycloakAngular: KeycloakService) {
                    super(router, keycloakAngular);
                }


                public async isAccessAllowed(route: ActivatedRouteSnapshot,state: RouterStateSnapshot): Promise<boolean | UrlTree> {

                    // Force the user to log in if currently unauthenticated.
                    if (!this.authenticated) {
                    await this.keycloakAngular.login({
                        redirectUri: window.location.origin + state.url,
                    });
                    }

                    // Get the roles required from the route.
                    const requiredRoles = route.data['roles'];

                    // Allow the user to to proceed if no additional roles are required to access the route.
                    if (!(requiredRoles instanceof Array) || requiredRoles.length === 0) {
                    return true;
                    }

                    // Allow the user to proceed if all the required roles are present.
                    return requiredRoles.every((role) => this.roles.includes(role));
                }

                }
                ```
                - [x] Complétion du fichier login.module.ts comme suit :
                ```
                import { NgModule, APP_INITIALIZER } from '@angular/core';
                import { KeycloakService, KeycloakAngularModule } from 'keycloak-angular';
                import { LoginGuard } from './login.guard';
                import { initializer } from './keycloak-initializer';
                import { LoginService } from './service/login.service';
                import { CommonModule } from '@angular/common';


                @NgModule({
                declarations: [],
                imports: [
                    KeycloakAngularModule,
                    CommonModule
                ],
                providers: [
                    {
                        provide: APP_INITIALIZER,
                        useFactory: initializer,
                        multi: true,
                        deps: [KeycloakService]
                    },
                    LoginGuard,
                    LoginService
                ]
                })
                export class LoginModule { }
                ```
                - [x] Ajout du module de login dans l'app.module.ts
                - [ ] Configuration du module de routing 
        - [ ] Survol du projet atmos pour voir comment sont définis les users en dur dans la db
        - [ ] Création de 3 users en dur via commande docker compose
        - [ ] Attribuer des roles / realms différents aux trois users
    - [ ] Implémentation d'une authentification keycloak 
- [ ] Implémentation d'un module d'authentification angularkeycloak
- [ ] Déploiement avec github actions
- [ ] Déploiement avec AWS
- [ ] Aller voir (https://link.medium.com/K4tiifEKYwb) sur conseil de Franck


**31 Janvier**
- [x] Rangement pour retour Théo & Anthony
- [x] Accéder à l'application angular depuis un protail keycloak
    - [x] Accéder aux outils d'administration
        - [x] Résolution du problème d'identifiants incorrects/user not found
- [ ] Objectif du jour : accéder à l'app angular ou non selon l'user loggé et ses droits
    - [ ] Sécurisation de l'application angular avec keycloak
        - [x] Finition du module login
            - [x] Reprise de (https://plainenglish.io/blog/secure-an-angular-single-page-application-with-keycloak-cdbe5026881e)
                - [x] Création d'un *landing module* avec ```ng generate module landing```
                - [x] Création du routing pour le landing avec ```ng generate module landing/landing-routing --flat --module=landing```
                - [x] Création d'un *landing component* avec ```ng generate module landing```
                - [x] Répétition des trois commandes précédentes pour un module admin
                - [x] Répétition des trois commandes précédentes pour un module user
                - [x] Configuration du module de routing :
                ```
                import { NgModule } from '@angular/core';
                import { RouterModule, Routes } from '@angular/router';
                import { LoginComponent } from './login/login.component';
                import { LoginGuard } from './login/login.guard';

                const routes: Routes = [
                // { path: '', redirectTo: 'login', pathMatch: 'full' },
                // { path: 'login', component: LoginComponent },
                {
                    path: '',
                    redirectTo: 'landing',
                    pathMatch: 'full'
                },
                {
                    path: 'landing',
                    loadChildren: () => import('./landing/landing.module').then(m => m.LandingModule),
                },
                {
                    path: 'admin',
                    loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule),
                    canActivate: [LoginGuard],
                    data: { roles: ["admin", "user"] }
                },
                {
                    path: 'user',
                    loadChildren: () => import('./user/user.module').then(m => m.UserModule),
                    canActivate: [LoginGuard],
                    data: { roles: ["user"] }
                }
                ];

                @NgModule({
                imports: [RouterModule.forRoot(routes)],
                exports: [RouterModule]
                })
                export class AppRoutingModule { }
                ```
                - [x] Lancement du projet avec ```ng serve```
                - [x] Non successful : stash de la branche et revert
        - [x] Reprise de doc (https://www.keycloak.org/getting-started/getting-started-docker#_secure_your_first_app)
        - [x] Check de (https://stackoverflow.com/questions/54305880/how-can-i-restrict-client-access-to-only-one-group-of-users-in-keycloak)
    - [ ] Implémentation d'une authentification keycloak 
        - [x] Conclusion : pour associer les roles des utilisateurs & l'accès aux applications à keycloak, il faut passer par du code
    - [ ] Survol du projet atmos pour voir comment sont définis les users en dur dans la db
    - [ ] Création de 3 users en dur via commande docker compose
    - [ ] Attribuer des roles / realms différents aux trois users
- [ ] Implémentation d'un module d'authentification angularkeycloak
- [ ] Déploiement avec github actions
- [ ] Déploiement avec AWS


**1er Février**
- [x] Commit des changements relatifs au service de mailing sur kmo-web
- [x] Push & pull request sur le github
- [x] Résolution des conflits
- [x] Résolution des problèmes liés à la modification  des  :
```
TypeOrmModule.forRoot({
			type: 'mariadb',
			host: 'kmo-mysql', //process.env.MYSQL_HOST,
			port: 3306,
			username: 'KMO',//process.env.MYSQL_USER, mettre les valeurs en dur ?
			password: 'KMO15022022', //process.env.MYSQL_PASSWORD,
			database: 'KMO', //process.env.MYSQL_DATABASE,
			entities: [Checkout, User, Store, Data],
			autoLoadEntities: true,
			synchronize: false,
		})
```
.
    - [x] Problème : 
    ```
    QueryFailedError: ER_NO_SUCH_TABLE: Table 'KMO.user' doesn't exist
    ```
    - [x] Résolution :  dans le app.module.ts du back, changement de ```synchronize: false``` à ```synchronize: true,```
    - [x] Résolution du problème de [Object object] dans les mails : changement de ```ticket``` à ```ticket.reference```les cas échéants dans amazon-ses.service.ts
    - [x] Nouveau commit et push après cleanup demandé par review de théo
- [x] Réunion kmo équipe digitale
- [ ] Inventaire des tablettes fonctionnelles dans le stock bureau
    - [ ] Installation de l'appli sur les tablettes
        - [x] Récupération du code wifi guest : @Gu3St99!@
        - [x] Insstallation de AppTester OK
        - [ ] Installation de kmo : reste à faire
- [ ] Objectif du jour : accéder à l'app angular ou non selon l'user loggé et ses droits
    - [ ] Git clone d'un projet keycloak vierge ? Pour travailler en local
    - [ ] Sécurisation de l'application angular avec keycloak
        - [ ] Finition du module login
        - [ ] Survol du projet atmos pour voir comment sont définis les users en dur dans la db
        - [ ] Survol du projet atmos pour voir comment marche le lien angular/keycloak pour l'auth
            - [x] Check de (https://github.com/COP-AMACO/atmos/blob/main/atmos-client/src/app/business/util/auth-guard.service.ts)
            - [ ] Documentation sur angular/router && keycloak-angular
                - [x] Check de (https://angular.io/api/router#primary-entry-point-exports)
                - [x] Check de (https://www.npmjs.com/package/keycloak-angular)
        - [ ] Lien du code d'auth angular avec keycloak
    - [ ] Création de 3 users en dur via commande docker compose
    - [ ] Attribuer des roles / realms différents aux trois users


**2 Février**
- [x] Prse de connaissance du mail de pascal, je suis "pilote" de la gestion des tablettes & sim et je "m'engage à fournir des chiffres justes" .......
- [ ] Objectif du jour : accéder à l'app angular ou non selon l'user loggé et ses droits
    - [ ] Git clone d'un projet keycloak vierge ? Pour travailler en local
    - [ ] Trouver le moyen de rediriger vers l'app angular dans le keycloak
    - [ ] Sécurisation de l'application angular avec keycloak
        - [ ] Survol du projet atmos pour voir comment marche le lien angular/keycloak pour l'auth
            - [x] Poursuite de documentation sur angular/router && keycloak-angular
                - [x] Check de (https://angular.io/api/router#primary-entry-point-exports)
                - [x] Check de (https://www.npmjs.com/package/keycloak-angular)
        - [x] Check de la version angular/node dans le projet *templategithubactions* : 
        ```
        ng version

        Angular CLI: 15.1.1
        Node: 18.12.1
        Package Manager: npm 9.2.0
        OS: win32 x64

        Angular: 15.1.0
        ```
        - [x] Versions adaptées de ```npm install keycloak-angular keycloak-js``` : 
        ```
        Angular | 	keycloak-js	|keycloak-angular	Support
        15.x	|       18 - 20	        |   13.x.x	    New Features / Bugs
        ```
        - [ ] Suivi de : (https://www.npmjs.com/package/keycloak-angular#setup)
            - [x] Ajout au fichier app.module.ts de :
            ```
            import { KeycloakAngularModule, KeycloakService } from 'keycloak-angular';
            function initializeKeycloak(keycloak: KeycloakService){
            return () =>
                keycloak.init({
                config: {
                    url: 'http://localhost:8080',
                    realm: 'your-realm',
                    clientId: 'your-client-id'
                },
                initOptions: {
                    onLoad: 'check-sso',
                    silentCheckSsoRedirectUri:
                    window.location.origin + '/assets/silent-check-sso.html'
                }
                });
            }

            @NgModule({
            declarations: [AppComponent],
            imports: [AppRoutingModule, BrowserModule, KeycloakAngularModule],
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
            ```
            - [x] Documentation à propos du silent check-sso :
            ```
            In the example we have set up Keycloak to use a silent check-sso. With this feature enabled, your browser will not do a full redirect to the Keycloak server and back to your application, instead this action will be performed in a hidden iframe, so your application resources only need to be loaded and parsed once by the browser when the app is initialized and not again after the redirect back from Keycloak to your app. 
            To ensure that Keycloak can communicate through the iframe you will have to serve a static HTML asset from your application at the location provided in silentCheckSsoRedirectUri.
            ```
            - [x] Création d'un fichier silent-check-sso.html dans le dossier /assets rempli comme suit :
            ```
            <html>
                <body>
                    <script>
                    parent.postMessage(location.href, location.origin);
                    </script>
                </body>
                </html>
            ```
            - [x] Sur conseil de la doc, check de (https://www.keycloak.org/docs/latest/securing_apps/#_javascript_adapter)
            - [x] Suppression et délétion des images keycloak & angular
            - [x] Rebuild de l'image keycloak : ```docker build -t templategithubactionskeycloak -f ./docker/Dockerfile.keycloak .```
            - [x] Rebuild de l'image angular : ```docker build -t templategithubactionsangular -f ./docker/Dockerfile.angular .```
            - [x] Relance de ```docker compose up```
        - [x] Check de (https://www.youtube.com/watch?v=aykr98e7PlM)
        - [ ] Finition du module login
        - [ ] Survol du projet atmos pour voir comment sont définis les users en dur dans la db
        - [ ] Lien du code d'auth angular avec keycloak
    - [ ] Création de 3 users en dur via commande docker compose
    - [ ] Attribuer des roles / realms différents aux trois users
- [ ] Renseignement sur la possibilité de faire un keycloak redirigeant vers x applications
    - [ ] Recherches générales
    - [x] Poursuite de doc
        - [x] Check de (https://www.keycloak.org/docs/latest/server_admin/index.html#using-the-admin-console) & (https://www.keycloak.org/docs/latest/server_admin/index.html#configuring-realms) & (https://www.keycloak.org/server/outgoinghttp) & (https://www.keycloak.org/docs/latest/securing_apps/#_javascript_adapter)
    - [x] Premier contact avec le concept de *multi-tenancy*
        - [x] Check de (https://medium.com/swlh/using-keycloak-for-multi-tenancy-with-one-realm-7be81583ed7b)

