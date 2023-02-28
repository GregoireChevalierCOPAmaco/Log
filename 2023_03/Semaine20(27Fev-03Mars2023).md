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