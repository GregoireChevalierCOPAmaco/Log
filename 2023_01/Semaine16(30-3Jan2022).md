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
- [ ] Résolution des problèmes liés à la modification  des  :
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
- [ ] Objectif du jour : accéder à l'app angular ou non selon l'user loggé et ses droits
    - [ ] Sécurisation de l'application angular avec keycloak
        - [ ] Finition du module login
        - [ ] Survol du projet atmos pour voir comment sont définis les users en dur dans la db
        - [ ] Lien du code d'auth angular avec keycloak
    - [ ] Création de 3 users en dur via commande docker compose
    - [ ] Attribuer des roles / realms différents aux trois users

