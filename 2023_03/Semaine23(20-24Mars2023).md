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
                - [x] copie du readme sur le bureau pour voir ce qu'il dit, win + R / cmd :
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
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
        - [ ] Création d'un thème personnalisé
            - [ ] Modification du theme avec extend du basic/keycloak
                
    - [ ] Voir pour la construction du keycloak version prod
    - [ ] Voir avec Frank pour la configuration du mail à keycloak
    - [ ] Voir pour l'addition de stores (attributes) depuis l'application (ajout et lien d'attribut store à la création d'un user ?)
        - [ ] Check de (https://stackoverflow.com/questions/58011950/keycloak-user-registration-how-to-add-the-role)