**9 Janvier**
- [x] Point rapide avec Frank : objectif prise en main du atmos pour changement image accueil
- [x] Mise entre parenthèses du kmo pour se concentrer sur atmos
- [ ] Reprise du projet atmos
    - [x] Redémarrage des dockers
        - [x] Positionnement dans le dossier ./atmos/portail-socle-1.0.4
        - [x] Lancement de la commande :
        ```
        docker compose --project-name atmos --env-file .env.dev up -d
        ```
    - [x] Avoir l'application lancée en local fonctionnelle
        - [x] Adresse http://auth.copamaco.local/realms/atmos/console/#/ atteignable
    - [x] Trouver le code correspondant à l'affichage fron de la page d'accueil : html & css dans le dossier portail-socle-1.0.4\keycloak\themes\atmos-theme\login\resources\
        - [x] Check de (https://stackoverflow.com/questions/39356300/avoid-keycloak-default-login-page-and-use-project-login-page) pour mieux saisir où et quoi modifier dans le projet
        - [x] Check de (https://www.baeldung.com/keycloak-custom-login-page) pour la mise en place générale
        - [x] Les outils de dev du navigateur montrent <div class="background"></div>
            - [x] Les informations de style dans les outils de dev montrent : 
            ```
            .login-content .background {
                background-image: url(../img/background_login.png);
                background-size: cover;
                width: 70%;
            }
            ```
            - [x] Trouver la mention de background dans le projet : atmos\portail-socle-1.0.4\keycloak\themes\atmos-theme\login\login.ftl
            - [x] Trouver où est la ligne de css qui appelle background-image: url(../img/background_login.png) : atmos\portail-socle-1.0.4\keycloak\themes\atmos-theme\login\resources\css\atmos-login.css  à la ligne 70
            - [x] L'image est située là : atmos\portail-socle-1.0.4\keycloak\themes\atmos-theme\login\resources\img\background_login.png . Sa résolution est de 2450 x 2864
            - [x] Documentation sur les media queries & le viewport (https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)
            - [x] Check des conditions selon l'orientation avec les media queries (https://stackoverflow.com/questions/16260880/media-query-for-browser-size-where-width-is-less-than-height)
            - [x] Modifications apportées
                - [x] Création d'un fichier atmos2.css
                - [x] Mise en commentaire de l'appel au fichier initial & appel au nouveau fichier dans le fichier theme.properties ici : portail-socle-1.0.4\keycloak\themes\atmos-theme\login\theme.properties
            - [x] OK pour le placeholder resposive, on attendra d'avoir des visuels définitifs pour la suite.
    - [ ] Trouver comment déployer des modifications sur la beta d'atmos
        - [ ] Poursuite du survol de l'application
        - [x] Trouver la doc fournie par ITS
            - [x] Le readme ne fournit pas toutes les infos dont j'ai besoin
            - [ ] Voir l'accès au EC2 AWS
            - [ ] Essai de création d'image
                - [x] Lancement de la commande : 
                ```
                docker buildx build --platform linux/amd64,linux/arm64 -t registry.prod.its-future.com/296-cop-amaco/projet/portail-client:latest-preprod --push -f Dockerfile.beta .
                ```
                Retour d'erreur :
                ```
                [+] Building 0.0s (0/0)
                ERROR: multiple platforms feature is currently not supported for docker driver. Please switch to a different driver (eg. "docker buildx create --use")
                ```
                - [ ] Résolution en cours
                    - [x] Check de (https://forums.docker.com/t/error-multiple-platforms-feature-is-currently-not-supported-for-docker-driver/124811/10) & 
                    (https://github.com/docker/build-push-action/blob/e27bcee4eb9e7b4bc168418e3364c4482120393a/docs/advanced/cache.md) &
                    (https://docs.docker.com/build/drivers/docker/)
                    - [x] Documentation sur docker driver & concepts connexes (https://docs.docker.com/engine/reference/commandline/buildx_create/#driver)
- [x] Appel Johan Zmiri
    - [x] Ré-activation du compte outlook monteur de johan via les active directories par Frank
    - [x] Mail à Johan pour linker le ppt cleemy, outlook & l'informer de la suite.


**10 Janvier**
- [ ] Reprise du projet atmos
    - [ ] Trouver comment déployer des modifications sur la beta d'atmos
        - [ ] Poursuite du survol de l'application
            - [ ] Essai de création d'image
                - [x] Lancement de la commande : 
                ```
                docker buildx build --platform linux/amd64,linux/arm64 -t registry.prod.its-future.com/296-cop-amaco/projet/portail-client:latest-preprod --push -f Dockerfile.beta .
                ```
                Retour d'erreur :
                ```
                [+] Building 0.0s (0/0)
                ERROR: multiple platforms feature is currently not supported for docker driver. Please switch to a different driver (eg. "docker buildx create --use")
                ```
                - [x] Résolution en cours
                    - [x] Check de (https://developer.broadleafcommerce.com/starter-projects/docker-configuration), rubrique : Understand existing sample Docker build configuration & (https://docs.docker.com/engine/reference/commandline/buildx_create/)
                    - [x] Sur conseil de Guillaume, modification de la commande sans la partie amd : 
                    ```
                    docker buildx build --platform linux/arm64 -t registry.prod.its-future.com/296-cop-amaco/projet/portail-client:latest-prod -t registry.prod.its-future.com/296-cop-amaco/projet/portail-client:1.0.4 --push -f Dockerfile .
                    ```
                    au lieu de :
                    ```
                    docker buildx build --platform **linux/amd64,**linux/arm64 -t registry.prod.its-future.com/296-cop-amaco/projet/portail-client:latest-prod -t registry.prod.its-future.com/296-cop-amaco/projet/portail-client:1.0.4 --push -f Dockerfile .
                    ```
                - [ ] Retour d'erreur :
                ```
                ERROR: failed to solve: rpc error: code = Unknown desc = failed to solve with frontend dockerfile.v0: failed to read dockerfile: open /var/lib/docker/tmp/buildkit-mount3250200511/Dockerfile.beta: no such file or directory
                ```
                & résolution en cours
                - [x] Check de (https://stackoverflow.com/questions/66839443/how-to-enable-disable-buildkit-in-docker) : mettre le 
                ```
                {
                "builder": {
                    "gc": {
                    "defaultKeepStorage": "20GB",
                    "enabled": true
                    }
                },
                "experimental": false,
                "features": {
                    "buildkit": true <<----------------
                }
                ```
                à false pourrait résoudre le problème
                - [ ] Essai de la commande avec le buildkit à false
                - [ ] Installation des émulateurs pour la création d'images
                - [ ] Installation des dépendances 
            - [ ] Essais d'accès au EC2 AWS
    - [ ] Application du code page d'accueil au projet keycloak github
        - [x] Cloner le projet keycloak
        - [ ] Installer les packages
            - [ ] atmos-server
                - [ ] Renvoie une erreur :
                ```
                Error response from daemon: Head "https://ghcr.io/v2/cop-amaco/atmos-server/manifests/latest": unauthorized
                ```
                Résolution en cours
        - [ ] Installer les dépendances
        - [x] Créer une nouvelle branche
        - [x] Refaire le code local dans ce projet
        - [ ] Push sur le github
        - [ ] Tenter de créer une image docker
    - [ ] Mail à Olivier ITS pour infos sur
        - comment mettre l'app en dev
        - avoir une doc complète, pas juste un readme
        - procédure à suivre pour la création d'image docker


**11 Janvier**
- [ ] Reprise du projet atmos
    - [ ] Application du code page d'accueil au projet keycloak github
        - [x] Commit de la nouvelle branche
        - [ ] Push sur le github
            - [x] Retour d'erreur : 
            ```
            ERROR: Write access to repository not granted.
            fatal: Could not read from remote repository.

            Please make sure you have the correct access rights
            ```
            - [ ] En attente que Théo me donne les droits
    - [ ] Trouver comment déployer des modifications sur la beta d'atmos 
        - [ ] Essai de création d'image
                - [x] Retour d'erreur :
                ```
                ERROR: failed to solve: rpc error: code = Unknown desc = failed to solve with frontend dockerfile.v0: failed to read dockerfile: open /var/lib/docker/tmp/buildkit-mount3250200511/Dockerfile.beta: no such file or directory
                ```
                & résolution en cours
                    - [x] La commande cherche le fichier Dockerfile.beta qui est dans le dossier portail-client, lancement de la même commande mais dans le dossier concerné
                    - [x] Création de l'image
                    - [x] Fail dans la procédure au niveau du :
                    ```
                    => ERROR pushing registry.prod.its-future.com/296-cop-a  7.9s
                    ```
                - [ ] Installation des émulateurs pour la création d'images
- [x] Mail à Olivier ITS pour infos sur
    - comment mettre l'app en dev
    - avoir une doc complète, pas juste un readme
    - procédure à suivre pour la création d'image docker
- [x] Reprise du mail apres retour FRANK & envoi
- [x] Retour discussion avec frank : l'objectif est de réussir à déployer sur nos serveurs aws le code ITS. Pour ce faire j'ai besoin de construire des images docker et donc de réaliser une CD adaptée à notre déploiement
- [x] Changement d'approche : décision prise de réaliser une mini cd d'un projet simple pour apprendre à construire, utiliser et déployer des images docker à partir d'un code existant
- [ ] Application :
    - [ ] Créer une base keycloak
    - [ ] Créer un front basique
    - [ ] Créer un back basique
    - [ ] Construire une image docker
    - [ ] Tester son déploiement sur notre aws (architecture = arm)
- [x] Re changement : Après retour mail d'olivier, il est possible d'adapter la ci cd existante. Selon lui il faut juste adapter les commandes CLI (cf portail-socle-1.0.4\README.md lignes 34 - 40)
- [ ] Reprise de doc sur docker buildx build 
    - [x] Check de (https://docs.docker.com/engine/reference/commandline/buildx_build/)
    - [x] Décomposition de la commande pour construire l'image front
        - [x] buildx build
        - [x] --platform (https://docs.docker.com/engine/reference/commandline/buildx_build/#platform)
        - [x] -t  (https://docs.docker.com/engine/reference/commandline/build/#tag-an-image--t)  abrévation du --tag
        - [x] --push
        - [x] -f Dockerfile.beta  le flag -f est suivi du nom du dockerfile, tout simplement.
    - [x] Essai avec une commande épurée : 
    ```
    docker buildx build --platform linux/arm64 -f Dockerfile.beta .
    ```
    Se placer dans le dossier portail-client. La commande passe, le build se fait, mais pour aterrir où ? Je ne le vois pas dans le docker desktop
    - [ ] Trouver où arrive le build. 
        - [x] Essai avec le flag output dans le dossier local :
        ```
        => ERROR exporting to client                                 0.0s
        => => copying files 2.49kB                                   0.0s 
        ------
        > exporting to client:
        ------
        ERROR: failed to solve: error from receiver: failed to symlink local\bin\arch: symlink \bin\busybox loc
        ```
        Windows n'autorise pas d'écrire dans ses fichiers.
        - [ ] Check de (https://stackoverflow.com/questions/41744551/where-does-the-output-for-docker-build-go#:~:text=They%20get%20stored%20as%20a,%2Fvar%2Flib%2Fdocker%20.)


**12 Janvier**
- [ ] Reprise du projet atmos
    - [x] Application du code page d'accueil au projet keycloak github
        - [x] Push sur le github
- [ ] Objectif : Réaliser le déploiement du projet atmos 
    - [x] Récupérer le code keycloak de github
    - [x] Récupérer le package client de github
        - [x] Installation du package trouvé ici (https://github.com/COP-AMACO/kmo-keycloak/pkgs/container/atmos-client)
            - [x] Positionnement dans ./kmo-keycloak & lancement de :
            ```
            docker pull ghcr.io/cop-amaco/atmos-client:latest
            ```
            et retour console : 
            ```
            Error response from daemon: Head "https://ghcr.io/v2/cop-amaco/atmos-client/manifests/latest": unauthorized
            ```
            - [x] Résolution de l'unauthorized
                - [x] Création de personnal accesses sur github avc 1 an de due date : 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
                - [x] Lancement de commande : 
                ```
                echo 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 | docker login ghcr.io -u GregoireChevalierCOPAmaco --password-stdin
                 ```
                 dans le dossier ./kmo-keycloak
                Retour d'erreur : 
                ```
                Error saving credentials: error storing credentials - err: exit status 1, out: `Post "http://ipc/registry/credstore-updated": context deadline exceeded (Client.Timeout exceeded while awaiting headers)`
                ```
                - [x] Recherches sur l'erreur
                    - [x] Essai de ```docker login``` avec retour : 
                    ```
                    Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
                    ```
                    - [x] Création de profil user sur https://hub.docker.com/ :
                    username :GregoireChevalierCOPAmaco
                    mail : g.chevalier@cop-amaco.com
                    pw : procédure habituelle
                    - [x] Essai de ```docker login``` avec retour : 
                    ```
                    Error response from daemon: Get "https://registry-1.docker.io/v2/": unauthorized: incorrect username or password
                    ```
                    - [x] Reboot docker
                    - [x] il s'avère que mon nom d'user docker est passé en lowercase. Username = gregoirechevaliercopamaco
                    - [x] Essai de ```docker login``` avec retour : 
                    ```
                    Error saving credentials: error storing credentials - err: exit status 1, out: `Post "http://ipc/registry/credstore-updated": context deadline exceeded (Client.Timeout exceeded while awaiting headers)`
                    ```
                    - [x] Suivi de (https://www.techrepublic.com/article/resolve-docker-timeout-headers-error/)
                    - [x] Dans le docker hub sur inernet, account settings/security/access token, création d'un access token nommé Default token all accesses. Puis lancement des commandes :
                    ```
                    docker logi|n |-u |greg|irechevaliercopamaco
                    |dckr_pat_SJ|haZHcSDGaZyTVaS1R2GNhY0A0
                    ```
                    - [x] Reboot docker, reboot pc
                - [x] Nouvel essai de commande docker : 
                ```
                echo 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 | docker login ghcr.io -u gregoirechevaliercopamaco --password-stdin
                ```
                retour console : 
                ```
                Error response from daemon: Get "https://ghcr.io/v2/": denied: denied
                ```
                - [x] Nouvel essai de commande docker : 
                ```
                echo "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" | docker login ghcr.io -u "gregoirechevaliercopamaco" --password-stdin
                ```
                sans succès
                - [x] Re-création d'un personal access token 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 & reprise de toute la procédure.
    - [x] Récupérer le package serveur de github
    - [ ] Construire les images docker
        - [x] Docker system prune -a -f pour repartir d'une base saine sans image parasite
        - [x] Suppression de tous les volumes
        - [ ] Reprise de doc sur docker buildx build 
        - [ ] Construction image keycloak 
        - [ ] Construction image client 
        - [ ] Construction image serveur 
    - [ ] Recréer la CI sur github actions au lieu de gitlab
    - [ ] Déploiement sur AWS
- [x] Refactorer le code css keycloak accueil pour une image simple +s logo cop
    - [x] Faire en sorte que l'image soit responsive
    - [x] Faire disparaître l'image au dela d'un certain zoom
    - [x] Ajouter le logo COP sur l'image en vp horizontal et au dessus des forms en vp vertical



**13 Janvier**
- [ ] Reprise du projet atmos
- [ ] Objectif : Réaliser le déploiement du projet atmos 
    - [ ] Renseignement sur la CI CD
        - [x] Check de (https://docs.github.com/fr/actions/learn-github-actions/understanding-github-actions) & (https://docs.github.com/en/actions/quickstart)
        - [x] Création d'un repo test pour les actions
        - [x] Création d'une github action template
        - [ ] Poursuite de recherches sur la CI (https://docs.github.com/fr/actions/automating-builds-and-tests/about-continuous-integration)
        - [ ] Poursuite de recherches sur le lien github actions & docker (https://docs.github.com/en/actions/creating-actions/creating-a-docker-container-action) & (https://docs.github.com/en/actions/creating-actions/dockerfile-support-for-github-actions)
    - [ ] Recréer la CI sur github actions au lieu de gitlab
        - [x] Créer un repo
        - [x] Cloner le repo en local
        - [x] Ajouter le code
        - [ ] Voir si ça marche
            - [x] Le docker-compose renvoie 
            ```
            Error response from daemon: Head "https://ghcr.io/v2/cop-amaco/atmos-client/manifests/latest": denied: denied
            ```
        - [ ] Commit & push
            - [ ] Création de la CI
                - [ ] Décortiquage de la CI gitlab présente
                    - [ ] include
                    - [ ] .global
                    - [ ] .develop
                    - [ ] .preprod
                    - [ ] .production la vision d'ensemble du projet global : avoir un portail d'accès à tous les services & applications. Voir Synthèse de réunion 13Janvier
    - [ ] Déploiement sur AWS
    - [ ] Construire les images docker
        - [ ] Reprise de doc sur docker buildx build 
        - [ ] Construction image keycloak 
        - [ ] Construction image client 
        - [ ] Construction image serveur 


    
    session anthony pw 123soleil