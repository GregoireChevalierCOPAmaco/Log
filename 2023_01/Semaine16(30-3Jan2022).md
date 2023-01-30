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
        - [ ] Survol du projet atmos pour voir comment sont définis les users en dur dans la db
        - [ ] Création de 3 users en dur via commande docker compose
        - [ ] Attribuer des roles / realms différents aux trois users
    - [ ] Implémentation d'une authentification keycloak 
- [ ] Implémentation d'un module d'authentification angularkeycloak
- [ ] Déploiement avec github actions
- [ ] Déploiement avec AWS
- [ ] Aller voir (https://link.medium.com/K4tiifEKYwb) sur conseil de Franck