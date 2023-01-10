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
        - [ ] Créer une nouvelle branche
        - [ ] Refaire le code local dans ce projet
        - [ ] Push sur le github
        - [ ] Tenter de créer une image docker
    - [ ] Mail à Olivier ITS pour infos sur
        - comment mettre l'app en dev
        - avoir une doc complète, pas juste un readme
        - procédure à suivre pour la création d'image docker