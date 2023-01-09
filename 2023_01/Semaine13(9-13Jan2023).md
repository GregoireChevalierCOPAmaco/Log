**9 Janvier**

- [x] Point rapide avec Frank : objectif prise en main du atmos pour changement image accueil
- [x] Mise entre parenthèses du kmo pour se concentrer sur atmos
- [ ] Reprise du projet atmos
    - [ ] Redémarrage des dockers
        - [x] Positionnement dans le dossier ./atmos/portail-socle-1.0.4
        - [x] Lancement de la commande :
        ```
        docker compose --project-name atmos --env-file .env.dev up -d
        ```
    - [x] Avoir l'application lancée en local fonctionnelle
        - [x] Adresse http://auth.copamaco.local/realms/atmos/console/#/ atteignable
    - [ ] Trouver le code correspondant à l'affichage fron de la page d'accueil
        - [ ] Check de (https://stackoverflow.com/questions/39356300/avoid-keycloak-default-login-page-and-use-project-login-page) pour mieux saisir où et quoi modifier dans le projet
        - [ ] Check de (https://www.baeldung.com/keycloak-custom-login-page) pour la mise en place générale
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
            - [ ] Documentation sur les media queries & le viewport (https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)
            - [x] Check des conditions selon l'orientation avec les media queries (https://stackoverflow.com/questions/16260880/media-query-for-browser-size-where-width-is-less-than-height)
- [x] Appel Johan Zmiri
    - [x] Ré-activation du compte outlook monteur de johan via les active directories par Frank
    - [x] Mail à Johan pour linker le ppt cleemy, outlook & l'informer de la suite.