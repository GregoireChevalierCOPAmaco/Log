**6 Février**
- [x] Mise à jour du fichier excel de suivi d'heures
- [x] Consultation de l'échange de mails avec ITS
- [x] Prise de connaissance des modifs opérées par Anthony sur le service de mails dans le SAV
- [x] Prise de connaissance du schéma d'utilisation des mails SAV de frank sur le serveur Discord 
- [x] Prise de connaissance de l'évolution des tâches sur Jira (https://cop-amaco.atlassian.net/jira/software/c/projects/KW/issues)
- [ ] Check d'un tuto youtube sur keycloak (https://www.youtube.com/watch?v=zyqWpFUPTnE&ab_channel=Tekgainers)
- [x] Redémarrage des containers docker
- [ ] Objectif du jour : accéder à l'app angular ou non selon l'user loggé et ses droits
    - [ ] Réussir à accéder à l'application
        - [x] Check de (https://www.comakeit.com/blog/quick-guide-using-keycloak-identity-access-management/)
        - [x] Check de (https://www.youtube.com/watch?v=GHy2KFjlhpg) :
            - Identity Access Management =
                - Auth du user
                - Droits, accès et privilèges du user
                - Gestion de par qui et comment les informations peuvent être modifiées
                - Administration, gestion des infos users, des sessions, etc ...
            - Identity Management a 4 fonctions principales :
                - Pure Identity Function, qui ne prend pas en compte le CRUD d'users
                - User Access Function (log-on), qui utilise une 'smart card' et ses données associées pour connecter l'user à un service ou une vue
                - Service Function, qui fournit un système de services personnalisé selon l'utilisateur ou le device concerné
                - Identity Federation, qui permet d'authentifier un user sans connaître son mot de passe sur le principe de la fédération d'identités
            - Schema de requête basique d'une app sous keycloak :
            User request to app -> Redirection vers l'auth keycloak -> Redirection vers la page de login -> Entrée des informations de login -> Submit des infos de login au keycloak -> Validation des informations de login par le keycloak -> Redirection vers l'application -> Display de la page requestée -> Request de token de l'application au keycloak -> Validation du token par keycloak -> Retour du token à l'application
            - Keycloak utilise le SSO, Single Sign On, pour permettre à un user d'accéder à de multiples applications avec un seul compte user sans avoir à constamment log in & out des différentes applications (grâce au token)
            - Le SSO se base sur Lightweight Directory Access Protocol (LDAP) & bases de données LDAP supportées par directory servers
            - Le SSO peut être utilisé avec les cookies dans le cadre de réseau IP, mais seulement si les différentes apps partagent le même domaine DNS parent
        - [x] Consigne des infos dans un doc de retour de veille
        - [x] Assignation de rôle à un user :
            - Créer un Realm Role qui englobe tous les rôles du realm
            - Créer un rôle 
            - Aller dans users/userX/Role_mapping/Assign_role/
            - Cocher la case du role à assigner
        - [x] Accéder au portail user de keycloak :
            - Créer un client, renseigner l'ID et le Name
            - Renseigner la valid URL, post logout url & web origins avec * pour taper sur la page de connexion de l'user
        - [ ] Rediriger le user vers l'application angular
            - [x] Check de (https://www.youtube.com/watch?v=0cufUQS59as)
            - [x] Reprise de doc : (https://www.keycloak.org/docs/latest/securing_apps/#_javascript_adapter)
                - [x] Toggle Client Authorization sur Off dans Clients/clientX/Settings , partie Capability config
                - [x] Configuration de Valid Redirect URIs and Web Origins
                    - [x] Check de (https://stackoverflow.com/questions/45352880/keycloak-invalid-parameter-redirect-uri) & (https://levelup.gitconnected.com/keycloak-in-angular-application-980260b4b196)
            - [x] Suppression du container angular et rebuild, puis re compose up
- [x] Call ITS pour setup une ou plusieurs journées de formation keycloak & réunion derrière
- [ ] Essais de modifs sur le fichier authguard.service.ts pour voir si je peux accéder à l'application client keycloak
    - [ ] Recherches sur le sujet
        - [x] Check de (https://github.com/mauriciovigolo/keycloak-angular) & du fichier (https://github.com/mauriciovigolo/keycloak-angular/blob/main/projects/keycloak-angular/src/lib/core/services/keycloak-auth-guard.ts) pour référence.
        - [ ] Application du code (https://github.com/mauriciovigolo/keycloak-angular#authguard) au projet template


**7 Février**
- [x] Installation application SAV sur les tablettes
    - [x] Aller dans l'App Tester
    - [x] Création d'un compte google, pas de tel à asocier
    - [x] Connecter la tablette au compte google : cop.monteur@gmail.com /COPkmo67  X  pas l'appareil racine pour vérifier la connexion
    - [x] Connecter la tablette au compte google : gerance.cop@gmail.com /COPAmaco2022**
    - [x] Connecter l'AppTester au compte google : gerance.cop@gmail.com /COPAmaco2022**
    - [x] Mise à jour de KMO SAV sur chaque tablette valide :
        - [x] IMEI 359839768760843 : à jour, tablette bureau & lien au compte google gerance.cop
        - [x] IMEI 359839768837088 : màj d'AppTester, lien au compte google gerance.cop , KMO SAV & OS Android
        - [x] IMEI 352807082442783 : màj d'AppTester, lien au compte google gerance.cop & KMO SAV
        - [x] IMEI 359839768802520 : màj de KMO SAV & lien au compte google gerance.cop
        - [x] IMEI 352807082442874 : màj de KMO SAV & lien au compte google gerance.cop
    - [x] Mise à jour du fichier excel de suivi de tablettes sur le serveur
- [ ] Objectif du jour : accéder à l'app angular ou non selon l'user loggé et ses droits
    - [ ] Réussir à accéder à l'application
    - [ ] Essais sur un template de fichier authguard.service.ts pour voir si je peux accéder à l'application client keycloak
    - [ ] Recherches sur le sujet
        - [x] Check de (https://github.com/mauriciovigolo/keycloak-angular) & du fichier (https://github.com/mauriciovigolo/keycloak-angular/blob/main/projects/keycloak-angular/src/lib/core/services/keycloak-auth-guard.ts) pour référence.
        - [ ] Application du code (https://github.com/mauriciovigolo/keycloak-angular#authguard) au projet template
        - [x] Check de (https://medium.com/@damilareaadedoyin/authentication-in-angular-using-keycloak-aff8e98dd094)
        - [x] Application du code (https://medium.com/@damilareaadedoyin/authentication-in-angular-using-keycloak-aff8e98dd094) au projet template
            - [x] Reprise de l'app.module.ts :
            ```
            imports {...}

            const keycloakService = new KeycloakService();

            @NgModule({
            declarations: [
                AppComponent,
                XyzComponent,
                FirstpageComponent
            ],
            imports: [
                KeycloakAngularModule,
                BrowserModule,
                AppRoutingModule,
                RouterModule
            ],
            providers: [AppAuthGuard, {
                provide: KeycloakService,
                useValue: keycloakService
            }],
            entryComponents: [AppComponent],
            })
            export class AppModule { 
                ngDoBootstrap(app) {
                    keycloakService
                    .init({
                        config: {
                        url: 'http://localhost:8080/auth',
                        realm: 'keycloak-angular',
                        clientId: 'angularproject',
                        },
                        initOptions: {
                        onLoad: 'login-required',
                        checkLoginIframe: false,
                        },
                        enableBearerInterceptor: true,
                        bearerExcludedUrls: [],
                    })
                    .then(() => {
                        app.bootstrap(AppComponent);
                    })
                    .catch((error) =>
                        console.error('[ngDoBootstrap] init Keycloak failed', error)
                    );
                }
            }
            ```
            - [x] Création du fichier ./src/app/app.authguard.ts
            - [x] Remplissage comme suit :
            ```
            import { Injectable } from '@angular/core';
            import {
            Router,
            ActivatedRouteSnapshot,
            RouterStateSnapshot,
            } from '@angular/router';
            import { KeycloakService, KeycloakAuthGuard } from 'keycloak-angular';

            @Injectable()
            export class AppAuthGuard extends KeycloakAuthGuard {
            constructor(
                protected override router: Router,
                protected override keycloakAngular: KeycloakService
            ) {
                super(router, keycloakAngular);
            }

            isAccessAllowed(
                route: ActivatedRouteSnapshot,
                state: RouterStateSnapshot
            ): Promise<boolean> {
                    return new Promise((resolve, reject) => {
                    let permission;
                    if (!this.authenticated) {
                        this.keycloakAngular.login().catch((e) => console.error(e));
                        return reject(false);
                    }

                    const requiredRoles: string[] = route.data['roles'];
                    if (!requiredRoles || requiredRoles.length === 0) {
                        permission = true;
                    } else {
                        if (!this.roles || this.roles.length === 0) {
                        permission = false
                        }
                        if (requiredRoles.every((role) => this.roles.indexOf(role) > -1))
                        {
                            permission=true;
                        } else {
                            permission=false;
                        };
                    }
                    if(!permission){
                        this.router.navigate(['/']);
                    }
                    resolve(permission)
                    });
                }
            }
            ```
            - [x] Check de (https://www.npmjs.com/package/keycloak-angular#authguard)
            - [ ] Ajout de l'authGuard aux routes, modification de app-routing.module.ts comme suit : 
            ```
            imports { ... } from ...

            const routes: Routes = [
                { 
                    path: 'login', 
                    canActivate: [AppAuthGuard],
                    component: LoginComponent,
                    data: { roles: ['user'] } 
                },
                { 
                    path: '',
                    component: LoginComponent 
                }
                ];

                @NgModule({
                imports: [RouterModule.forRoot(routes)],
                providers: [AppAuthGuard],
                exports: [RouterModule]
            })
            export class AppRoutingModule { }
            ```
        - [x] Suppression, rebuild et redémarrage des containers docker
- [x] Commit du projet de test
- [x] Prochain objetcif : refaire l'app angular au plus basique pour tests
    - [x] Positionnement dans le dossier racine et ```ng new angulartestapp```
- [x] Installation application KMO Flasher sur la tablette gonflée
    - [x] Aller dans l'App Tester
    - [x] Création d'un compte google, pas de tel à asocier
    - [x] Connecter l'AppTester au compte google : gerance.cop@gmail.com /COPAmaco2022**
    - [x] IMEI 359839768760843 : kmo flasher installé, tablette envoyée à l'atelier
- [x] Modification du fichier excel de suivi en conséquence
- [x] Réunion de setup du jira KM Prédictive
- [ ] Objectif : accéder à l'app angular ou non selon l'user loggé et ses droits
    - [ ] Réussir à accéder à l'application
    - [ ] Créer une dummy app sur un port 4567
    - [ ] Créer une dummy app sur un port 5678
    - [ ] Créer une dummy app sur un port 6789
    - [ ] Créer 5 users 
    - [ ] Créer 4 droits dans le realm
    - [ ] Assigner les droits aux users
    - [ ] Intercepter les connexions aux dummy apps avec keycloak
    - [ ] Autoriser l'accès aux différentes apps selon le niveau de droits de chaque user 


**8 Février**
- [ ] Réunion de définition des tâches pour le jira KM Prédictive
- [ ] Création d'une 2e dummy app
    - [x] Modification du docker-compose.yml comme suit :
    ```
    postgres:
    ...
    keycloak: 
    ...
    angular:
        container_name: angular-container
        image: templategithubactionsangular
        build:
        context: app-angular-keycloak
        # dockerfile: docker/Dockerfile.angular
        command: sh -c "cd app-angular-keycloak && npm link @angular/cli && ng serve --port 4200 --host 0.0.0.0"
        ports: 
        - 4200:4200
    angular2:
        container_name: angular-container2
        image: templategithubactionsangular2
        build:
        context: angulartestapp
        command: sh -c "cd angulartestapp && npm link @angular/cli && ng serve --port 4567 --host 0.0.0.0"
        ports: 
        - 4567:4567
    ```
    - [x] Création d'un 2e dockerfile Dockerfile.angular2 rempli comme suit :
    ```
    # syntax=docker/dockerfile:1

    FROM node:18-alpine

    # WORKDIR /app
    WORKDIR /angulartestapp/src/app
    # WORKDIR /

    ENV PATH /angulartestapp/node_modules/.bin:$PATH

    COPY ./angulartestapp/package.json ./
    COPY ./angulartestapp/package-lock.json ./
    RUN npm install

    COPY . .
    ```
    - [x] Suppression des images angular docker existantes 
    - [x] Build de l'image avec ```docker build -t templategithubactionsangular -f ./docker/Dockerfile.angular .```
    - [x] Build de l'image avec ```docker build -t templategithubactionsangular2 -f ./docker/Dockerfile.angular2 .```
    - [x] Relance du docker compose up avec les modifs : on arrive toujours au message d'erreur Invalid parameter: redirect_uri
    - [x] Check de (https://github.com/mauriciovigolo/keycloak-angular/issues/396)
    - [x] Application du workaround : dans le docker-compose.yml, modification de :
    ```
    keycloak:
    container_name: keycloak-container
    ...
    command:
      - start-dev
    ```
    en 
    ```  
    keycloak:
    container_name: keycloak-container
    ...
    command:
      - start --spi-login-protocol-openid-connect-legacy-logout-redirect-uri=true
    ```
    - [x] Relance du docker compose up, retour d'erreur console keycloak :
    ```
    ERROR: Unexpected error when starting the server in (production) mode
    ERROR: Failed to start quarkus
    ERROR: Strict hostname resolution configured but no hostname setting provided
    ```
    - [x] Reprise de la commande start avec start-dev et les flags, puis relance du docker compose up, setup des infos sur https://www.keycloak.org/app/ , sign in et retour d'erreur : Page not found && plus autorisé à accéder au realm créé
    - [x] Revert de la commande sans le flag et relance docker compose up : Realm supprimé par le rebuild du docker keycloak
    - [x] Reprise de la commande start avec start-dev et les flags
    - [x] Recréation du realm
    - [ ] Recréation des clients
    - [ ] Recréation des users
- [ ] Objectif : accéder à l'app angular ou non selon l'user loggé et ses droits
    - [ ] Réussir à accéder à l'application
    - [ ] Créer une dummy app sur un port 4567
    - [ ] Créer une dummy app sur un port 5678
    - [ ] Créer une dummy app sur un port 6789
    - [ ] Créer 5 users 
    - [ ] Créer 4 droits dans le realm
    - [ ] Assigner les droits aux users
    - [ ] Intercepter les connexions aux dummy apps avec keycloak
    - [ ] Autoriser l'accès aux différentes apps selon le niveau de droits de chaque user 


**9 Février**
- [x] Prise de connaissance des mails
- [x] Code review du service de mail ses avec Anthony
- [ ] Refonte du setup keycloak admin
    - [x] Recréation des clients
    - [x] Recréation des roles
        - [x] Création d'un role admin
        - [x] Création d'un role baseRole
        - [x] Création d'un role canReach4567
    - [x] Recréation des groupes
        - [x] Création d'un group base user
        - [x] Création d'un group super user
    - [x] Recréation des users
        - [x] Création de l'user g.chevalier
            - [x] Assignation au groupe super_user
            - [x] Assignation aux roles baseRole & canReach4567
            - [x] Assignation d'un mdp pw123
        - [x] Création de l'user t.test
            - [x] Assignation au groupe base_user
            - [x] Assignation au role baseRole
            - [x] Assignation d'un mdp pw123
        - [x] Création de l'user admin
            - [x] Assignation au role admin
            - [x] Assignation au role uma_authorization
            - [x] Assignation au role offline_access
            - [x] Assignation d'un mdp admin
    - [x] Export du realm
        - [x] Check de (https://www.opcito.com/blogs/how-to-import/export-realm-in-keycloak) & (http://www.mastertheboss.com/keycloak/how-to-export-and-import-realms-in-keycloak/)
        - [x] Dans le realm/RealmSettings/General , dérouler le menu en haut à droite flaggé |Action ↓| , et faire un "partial export"
        - [x] Récupération du json dans les téléchargements
        - [ ] Restriction de l'accès aux apps selon le role
            - [x] Check de (https://www.keycloak.org/docs/latest/server_admin/#con-user-registration_server_administration_guide) & (https://www.keycloak.org/docs/latest/server_admin/#assigning-permissions-using-roles-and-groups)
            - [ ] Application de la doc
- [x] Échange de tablettes pour Ales Wandres
    - [x] Retour tab ecran cassé n° 359839768834986
    - [x] Sortie tab n° 359839768802520
    - [x] Mise à jour du fichier de suivi des tablettes
- [ ] Réunion KMO Predict
    - [x] Établissement des spécifications données utilisateur à afficher par magasin :
        - Total time
        - On Time
        - Off Time
        - Clients/heure
        - Première utilisation
        - Dernière maintenance
        - État de maintenance
        - Commande
    - [x] Type de données remontables :
        - Température CPU
        - Bool trappe
        - Bool moteur
        - Bool IR
        - Bool pédale
        - État caisse
        - Assistance
        - Courant monteur
        - Reboot
        - Durée ON
        - Durée OFF
        - Temps de passage en caisse
        - Taux d'occupation
- [ ] Démarrer le projet Atmos ITS pour aller voir comment est configuré le keycloak
- [ ] Objectif : accéder à l'app angular ou non selon l'user loggé et ses droits
    - [ ] Réussir à accéder à l'application
    - [x] Créer une dummy app sur un port 4567
    - [ ] Créer une dummy app sur un port 5678
    - [ ] Créer une dummy app sur un port 6789
    - [ ] Créer 5 users 
    - [ ] Créer 4 droits dans le realm
    - [ ] Assigner les droits aux users
    - [ ] Intercepter les connexions aux dummy apps avec keycloak
    - [ ] Autoriser l'accès aux différentes apps selon le niveau de droits de chaque user 


**10 Février**
- [ ] Démarrer le projet Atmos ITS pour aller voir comment est configuré le keycloak
    - [x] Suppression du repo vide kmo-deploy sur le github de la boite
    - [x] Réouverture du projet C:\Users\gchevalier\Desktop\atmos>
    - [x] Tentative d'accès à http://auth.copamaco.local/admin/atmos/console/#/ , sans succès.
    - [x] Lancement des containers
        - [x] Positionnement dans le dossier ./portail-socle-1.0.4/deploy
        - [x] Lancement de  ```docker compose --project-name atmos up -d```, retour console :
        ```
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ENV_SUFFIX\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"FQDN\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"AUTH_FQDN\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"AUTH_FQDN\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"FQDN\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"KC_DB_PASSWORD\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_DB_USERNAME\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_DB_PASSWORD\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"KC_DB_USERNAME\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_DB_USERNAME\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_DB_PASSWORD\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ENV_SUFFIX\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"KC_DB_USERNAME\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"KC_DB_PASSWORD\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"KC_LOG_LEVEL\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_BACK_KEY\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"KEYCLOAK_ADMIN\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"KEYCLOAK_ADMIN_PASSWORD\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"AUTH_FQDN\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_DB_USERNAME\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_DB_PASSWORD\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_S3_BUCKET\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_S3_REGION\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"SCHEME\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"AUTH_FQDN\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_BACK_KEY\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_AWS_ACCESS_KEY_ID\" variable is not set. Defaulting to a blank string."
        time="2023-02-10T11:33:28+01:00" level=warning msg="The \"ATMOS_AWS_SECRET_ACCESS_KEY\" variable is not set. Defaulting to a blank string."
        services.proxy.networks.default.aliases array items[0,1] must be unique
        ```
        - [x] Recherche dans les logs de la méthode à suivre pour lancer atmos
        - [x] Dans le dossier :
        ```
        atmos\portail-socle-1.0.4\deploy>
        ```
        lancement de commande : 
        ```
        docker compose --project-name atmos --env-file .env.dev up -d

        ```
    - [x] Accéder à la console admin keycloak de l'atmos fourni par ITS
        - [x] Lancement vers http://auth.copamaco.local/admin/atmos/console, et redirection automatique vers : http://auth.copamaco.local/realms/atmos/login-actions/authenticate?execution=1735c886-d5fd-4126-9d3f-291d200b913f&client_id=security-admin-console&tab_id=GKcvhNIWNPg
        - [x] Connexion à l'interface d'adminn du keycloak : déception on n'a accès qu'aux menus users et groups, et encore pas avec toutes les infos
    - [x] Tests d'ajout d'user sur le realm master en local
        [x] Ajout d'un user pour le realm master :
        - t.test
        - pw123
    - [ ] Assignation de roles & permissions pour restreindre son accès aux fonctionnalités keycloak comme pour la console d'admin atmos ITS
        - [x] Check de (https://www.youtube.com/w  atch?v=zyqWpFUPTnE) pour référence
        - [x] Check de (https://www.keycloak.org/docs-api/13.0/rest-api/index.html#_overview) pour documentaiton
- [ ] Objectif : accéder à l'app angular ou non selon l'user loggé et ses droits
    - [ ] Réussir à accéder à l'application
    - [x] Créer une dummy app sur un port 4567
    - [ ] Créer une dummy app sur un port 5678
    - [ ] Créer une dummy app sur un port 6789
    - [ ] Créer 5 users 
    - [ ] Créer 4 droits dans le realm
    - [ ] Assigner les droits aux users
    - [ ] Intercepter les connexions aux dummy apps avec keycloak
    - [ ] Autoriser l'accès aux différentes apps selon le niveau de droits de chaque user 
- [x] Démarrage KMO Predict
    - [x] Réunion
    - [x] Prise en main du jira
    - [ ] Début du maquettage