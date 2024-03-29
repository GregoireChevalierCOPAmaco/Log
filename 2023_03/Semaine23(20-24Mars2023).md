**20 Mars**
- [ ] Retour Franck, faire le point
- [ ] Poursuite keycloak
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
        - [x] Check de (https://www.keycloak.org/docs/latest/server_development/#creating-a-theme)
        - [x] Insight de la doc : 
        ```
        Unless you plan to replace every single page you should extend another theme. 
        Most likely you will want to extend the Keycloak theme, but you could also consider extending the base theme if you are significantly changing the look and feel of the pages. 
        The base theme primarily consists of HTML templates and message bundles, while the Keycloak theme primarily contains images and stylesheets
        ```
        - [x] Check de (https://stackoverflow.com/questions/52641379/keycloak-deploy-custom-theme-in-docker) pour intégration du fichier de theme custom
        - [x] Trouver la méthode à suivre pour faire un thème custom : 
            - Copier le fichier de thème du keycloak docker 
            - ```docker cp your_keycloak_container_id:/opt/jboss/keycloak/themes/MyTheme /dossierciblewindows/dossier/. ```
            - Faire les modifs sur le thème voulu en extendant le base ou le keycloak
            - Ajouter des fichiers de style/html le cas échéant
            - docker cp le dossier du nouveau thème et son contenu sur le docker
            - Modifier le fichier ./standalone/configuration/standalone.xml avec  :
            ```
            <theme>
                <staticMaxAge>-1</staticMaxAge>
                <cacheThemes>false</cacheThemes>
                <cacheTemplates>false</cacheTemplates>
                ...
            </theme>
            ```
            - Ajouter le cas échéant dans le /theme/.../ressources/ un dossier css et des fichiers de styles dedans
            - Sélectionner le thème voulu dans la console d'administration, Cop_sass -> Realm Settings -> Themes ↓ et choisir dans les dropdowns
        - [ ] Création d'un thème personnalisé
            - [x] Se rendre dans le docker /opt/keycloak/themes : il n'y a qu'un fichier readme
            - [x] copie du readme sur le bureau pour voir ce qu'il dit, win + R / cmd :
            ```
            docker cp keycloak-sass-container:/opt/keycloak/themes/README.md C:\Users\gchevalier\Desktop
            ```
            - [x] Insight du readme : 
            ```
            While creating custom themes, especially when overriding templates, it may be useful to use the built-in templates as a reference. 
            These can be found within the theme directory of `../lib/lib/main/org.keycloak.keycloak-themes-18.0.0.jar`, which can be opened using any standard ZIP archive tool.
            ```
            - [x] Check du thème de base
                - [x] copie du theme sur le bureau pour voir ce qu'il contient et extraction, win + R / cmd :
                ```
                docker cp keycloak-sass-container:/opt/keycloak/lib/lib/main/org.keycloak.keycloak-themes-18.0.0.jar C:\Users\gchevalier\Desktop
                ```
            - [x] Modifs dans le css du theme extended theme/kmo-predict/admin/ressources/css
            - [ ] Renvoi du theme sur le docker
                - [x] win + R / cmd :
                ```
                docker cp C:\Users\gchevalier\Desktop\theme\kmo-predict keycloak-sass-container:/opt/keycloak/themes/
                ```
            - [ ] Selection du theme 
    - [x] Check des modifs de Théo sur la PR 36  (https://github.com/COP-AMACO/KMO_PREDICT/pull/36/files)
    - [x] Check de (https://medium.com/keycloak/create-a-custom-theme-for-keycloak-8781207be604) pour modifications thème existant
    - [ ] Voir pour la construction du keycloak version prod
    - [ ] Voir avec Frank pour la configuration du mail à keycloak
    - [ ] Voir pour l'addition de stores (attributes) depuis l'application (ajout et lien d'attribut store à la création d'un user ?)
        - [ ] Check de (https://stackoverflow.com/questions/58011950/keycloak-user-registration-how-to-add-the-role)
- [x] La grosse réunion de mise au point des projets en cours + coeur paysan

**21 Mars**
- [ ] Poursuite keycloak
    - [x] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
        - [x] Création d'un thème personnalisé *via docker*
            - [x] Modification du theme avec extend du basic/keycloak
                - [x] Suivi de (https://medium.com/keycloak/create-a-custom-theme-for-keycloak-8781207be604)
                - [x] Recherche du fichier standalone.xml : (https://wiki.archlinux.org/title/Keycloak#:~:text=account%20already%20exists.-,Configuration,xml%20.) le fichier serait dans etc/keyclaok/standalone.xml ? Pas trouvé. Passage à autre chose ; le standalone ne sert qu'à s'éviter un restart du server keycloak pour voir les modifs
                - [x] Redémarrage du docker keycloak, pas la peine, il suffit de sélectionner le theme voulu dans la console
                - [x] Modifs du theme pour changements login 
                    - [x] Ajout d'un nouveau bg
                    - [x] Renvoi du theme sur le docker : 
                    suppression du theme kmo-predict :
                    ```
                    rm -rf kmo-predict/
                    ```
                    mais retour permission denied ....
                    Re push pour voir si ça ecrase l'existant sur le docker de la nouvelle version :`
                    win + R / cmd :
                    ```
                    docker cp C:\Users\gchevalier\Desktop\theme\kmo-predict keycloak-sass-container:/opt/keycloak/themes/
                    ```
                    C'est tout bon
            - [x] Renvoi du theme sur le docker
            - [x] Selection du theme 
        - [ ] Modification du readme keycloak de base pour expliciter la procédure
            - [x] Pull de develop pour être à jour
            - [x] Écriture de doc pour la procédure en .md
    - [ ] Voir pour la construction du keycloak version prod
    - [ ] Voir avec Frank pour la configuration du mail à keycloak
    - [ ] Voir pour l'addition de stores (attributes) depuis l'application (ajout et lien d'attribut store à la création d'un user ?)
        - [ ] Check de (https://stackoverflow.com/questions/58011950/keycloak-user-registration-how-to-add-the-role)
- [x] S'occuper de la carte sim en rab : Carte sim trouvée dans la tablette pétée ancienement celle de A.Wandres et mise dans la flybox : n°SIM : 1041079231059
- [x] Config de l'airbox
- [ ] Aide à la résolution des tests d'Anthony
    - [x] Pull de la branche KP 129
    - [x] Lancement des tests pour voir si ça passe et si la v9.1.4 de nest résout le problème : ça passe pas : 
    ```TypeError: Cannot read properties of undefined (reading 'inspectInstanceWrapper')```
    trouver pourquoi
    - [ ] Résolution de l'erreur : TypeError: Cannot read properties of undefined (reading 'inspectInstanceWrapper')
        - [ ] À essayer : désinstaller jest en global et le réinstaller en global ?
- [ ] Question de créer une page dédiée de l'application qui puisse normaliser l'ajout d'un nouveau magasin
- [ ] Question de créer une page dédiée de l'application qui puisse formater le principe d'utiliser l'API  pour aller taper dans la bdd pour y inscrire les nouveaux magasins
- [ ] Ajouter un log de l'état des caisses au changement d'état : qui a modifié quelle caisse, vers quel état et quand

**22 Mars**
- [ ] Poursuite keycloak
    - [ ] Bloquer l'accès la page cop a ceux qui n'ont pas le rôle requis
        - [x] Précisions apportées à la tâche jira
        - [x] Assignation à la tâche jira
            - [ ] Création de branche kp134 et checkout
            - [ ] Voir avec Théo pour setup du back
            - [ ] Récupération du rôle dans l’app front
            - [ ] Modification de l’authguard
            - [ ] Restriction de l’accès à la route en fonction du rôle 
            - [ ] Affichage du bouton de link en fonction du rôle
- [x] Aide à la résolution des tests d'Anthony
    - [x] Résolution de l'erreur : TypeError: Cannot read properties of undefined (reading 'inspectInstanceWrapper')
        - [x] À essayer : désinstaller jest en global et le réinstaller en global ?
        - [x] ```npm uninstall -g @nest/testing``` à la racine puis |```npm install -g @nest/testing```
- [ ] Question de créer une page dédiée de l'application qui puisse normaliser l'ajout d'un nouveau magasin
    - [ ] Modification du jira en conséquence
- [ ] Question de créer une page dédiée de l'application qui puisse formater le principe d'utiliser l'API  pour aller taper dans la bdd pour y inscrire les nouveaux magasins
    - [ ] Modification du jira en conséquence
- [ ] Ajouter un log de l'état des caisses au changement d'état : qui a modifié quelle caisse, vers quel état et quand
    - [ ] Modification du jira en conséquence

**23 Mars**
- [x] Résolution des problèmes de tests en local
    - [x] Kill du projet
    - [x] Re-clone et ```npm i --legacy-peer-deps```
- [x] Réponse à Evelyne sur les mails monteurs
- [ ] Utilisation du keycloak uniformisé
    - [x] Update de la branche develop avec un dossier documentation
    - [x] Relancer le docker avec ```docker compose --env-file .env.dev -f docker-compose.dev.yml up --renew-anon-volumes --always-recreate-deps --build```
        - [x] Résolution de l'erreur :
        ```
         - keycloak Error              
        Error response from daemon: Head "https://ghcr.io/v2/cop-amaco/kmo-keycloak/manifests/latest": denied: denied
        ```
        - [x] Recréation d'un personnal access token sur github avec les read & write de package
        - [x] Relance de login avec PAT :
        ```
        echo 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 | docker login ghcr.io -u gregoirechevaliercopamaco --password-stdin
        ```
        - [x] Relance de compose :
        ```
        docker compose --env-file .env.dev -f docker-compose.dev.yml up --renew-anon-volumes --always-recreate-deps --build
        ```
        - [x] Erreur au lancement de l'app back dans docker, npm i peer deps, relance & all good
    - [x] Refaire le realm
        - [x] Import du dernier export de realm fourni par theo
        - [x] Création de 3 users
    - [x] Ajouter les données en base locale
        - [x] Lancer l'extension db de vscode
        - [x] Config de la connexion (pw : dbpredict16022023)
        - [x] Lancer la commande : 
        ```
        INSERT INTO public.gateway (mac, datetime, "diskUsage", "temperatureCpu", "freeRam", services)
        VALUES ('01:00:5E:xx:xx:2x', '2023-03-01 16:48:43.000000', 101, 29, 30,
                '{
                "ATMOS": {
                    "active": true,
                    "memory_usage": "46.6M",
                    "mac_declared": "22:a9:fb:ff:d2:bc",
                    "extras": {
                    "operational_rate": 98.66666666666667
                    }
                },
                "RTunnel": {
                    "active": true,
                    "memory_usage": "11.8M"
                }
                }'),
            ('01:00:5E:xx:xx:xx', '2024-03-01 16:48:43.000000', 2323, 29, 30, '{
                "ATMOS": {
                "active": true,
                "memory_usage": "46.6M",
                "mac_declared": "22:a9:fb:ff:d2:bc",
                "extras": {
                    "operational_rate": 98.66666666666667
                }
                },
                "RTunnel": {
                "active": true,
                "memory_usage": "11.8M"
                }
            }'),
            ('22:a9:fb:ff:d2:bc', '2023-02-22 09:45:48.347000', 13, 39.444, 81.52787453215763, '{
                "ATMOS": {
                "active": true,
                "memory_usage": "46.6M",
                "mac_declared": "22:a9:fb:ff:d2:bc",
                "extras": {
                    "operational_rate": 98.66666666666667
                }
                }
            }');

        INSERT INTO public.kmo_box (mac, datetime, uptime, firmware_version,"checkoutNumber", "gatewayMac")
        VALUES ('01:00:5E:xx:xx:xx', '2023-03-01 16:48:43.000000', 10, 1.4,1,
                '22:a9:fb:ff:d2:bc'),
            ('01:00:5E:xx:xx:33', '2023-03-02 13:44:01.000000', 3, 3,2, '22:a9:fb:ff:d2:bc');

        INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand) VALUES ('081439dd-cfda-49e7-85d8-15c6c25c65ca', '7 rue du chenes', 'Nordhouse', '67100', true, 3, null, '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Lidl');
        INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand) VALUES ('9de16383-c4d8-4616-bc4c-a26795f286ae', '7 rue du chenes', 'Nordhouse', '67100', true, 3, '01:00:5E:xx:xx:2x', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Norma');
        INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand) VALUES ('7d1a9808-ccf1-4136-8c8b-b80020ac0ec4', '7 rue du chenes', 'Nordhouse', '67100', true, 3, null, '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Lidl');

        INSERT INTO public.event (id, type, duration, datetime, state, "stateValue", motor, "kmoBoxMac")
        VALUES ('993051ef-1a3e-4781-b60f-1ea7faf7da89', 'pedal', 22.6, '2023-02-22 09:45:48.347000', 'up', null, null,
        '01:00:5E:xx:xx:xx'),
       ('75cb0af5-849d-4ce6-b794-a0585136bd8c', 'motor_flag', null, '2023-02-22 09:45:48.347000', 'value', 0.225, '2',
        '01:00:5E:xx:xx:xx'),
       ('6ee4988c-8e7b-4c10-820c-07b48fd8c574', 'cpu_temperature_changed', null, '2023-02-22 09:45:48.347000',
        'thermal_switch', null, '2', '01:00:5E:xx:xx:xx'),
       ('cbee4323-3f71-4fda-ba96-cc9c122aa5a2', 'power_intensity_changed', null, '2023-02-22 09:45:48.347000',
        'thermal_switch', null, '2', '01:00:5E:xx:xx:xx'),
       ('993051ef-1a3e-4781-b60f-1ea7faf7da83', 'ir', 22.6, '2023-03-02 13:43:30.000000', 'down', null, '1',
        '01:00:5E:xx:xx:xx')
        ```
    - [x] Relance du projet, erreur non critique dans la construction du back : 
    ```
    kmo-predict-back:dev:     this.server.on('connection', (socket) => {              ^
    kmo-predict-back:dev: TypeError: Cannot read properties of null (reading 'on')     
    kmo-predict-back:dev:     at GatewaysGateway.onModuleInit (/app/apps/kmo-predict-back/src/app/gateways/gateways.gateway.ts:30:17)
    ```
    résolution via docker prune image, system & container ?
        - [x] Prune de docker
        - [x] Rajout d'un "?" après server dans le fichier gateways.gateway.ts : 
        ```
        onModuleInit() {
            this.server?.on('connection', (socket) => {
            console.log(socket.id);
            });
        }
        ```
- [x] Rédaction d'une doc pour l'export de realm avec users
- [ ] Poursuite keycloak
    - [ ] Bloquer l'accès la page cop a ceux qui n'ont pas le rôle requis
        - [ ] Récupération du rôle dans l’app front
        - [ ] Modification de l’authguard
        - [ ] Restriction de l’accès à la route en fonction du rôle 
        - [ ] Restructuration des rôles dans les groupes du keycloak
    - [ ] Affichage du bouton de link sidebar en fonction du rôle
        - [ ] Création de branche kp134 et checkout

**24 Mars**
- [ ] Poursuite keycloak
    - [x] Affichage du bouton de link sidebar en fonction du rôle
        - [x] Création de branche kp134 et checkout
        - [x] Refacto du code avec un nommage correct
        - [ ] Refacto du code avec tous les rôles autorisés à voir le lien
            - [x] Voir (https://www.geekstrick.com/view-component-based-on-user-role/) pour le stack des roles assignés à la vision d'un component
            - [ ] Utilisation de :
            ```
            this.keycloak.isUserInRole('kmo-predict_store-manager')
            ```
            pour ce faire ?
        - [x] Refacto du code en adaptant la logique au bouton de lien mon magasin
    - [x] Résoudre le problème d'infos qui ne s'affichent pas sur la topbar selon le role
        - [x] Les rôles imbriqués dans default-roles-cop_sass sont nécessaires au bon fonctionnement, il faut les laisser attribués au user, même le plus basique
    - [ ] Bloquer l'accès la page cop a ceux qui n'ont pas le rôle requis
        - [x] Récupération du rôle dans l’app front
        - [ ] Reprise des routes de l'app-routing.component (fait par Théo)
        - [ ] Modification de l’authguard (fait par Théo)
        - [ ] Restructuration des rôles dans les groupes du keycloak (fait par Théo)
        - [ ] Restriction de l’accès à la route en fonction du rôle 
        - [ ] Appliquer la restriction à toutes les routes nécessaires
        - [ ] Push & merge
- [x] Installation de kmo flasher sur la tablette n° 352807082442783