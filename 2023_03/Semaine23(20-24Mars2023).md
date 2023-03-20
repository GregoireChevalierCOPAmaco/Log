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
            - Sélectionner le thème voulu dans la console d'administration, Cop_sass -> Realm Settings -> Themes ↓ et choisir dans les dropdowns
    - [ ] Voir pour la construction du keycloak version prod
    - [ ] Voir avec Frank pour la configuration du mail à keycloak
    - [ ] Voir pour l'addition de stores (attributes) depuis l'application (ajout et lien d'attribut store à la création d'un user ?)
        - [ ] Check de (https://stackoverflow.com/questions/58011950/keycloak-user-registration-how-to-add-the-role)
