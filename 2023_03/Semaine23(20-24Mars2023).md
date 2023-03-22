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
- [ ] Aide à la résolution des tests d'Anthony
    - [ ] Résolution de l'erreur : TypeError: Cannot read properties of undefined (reading 'inspectInstanceWrapper')
        - [ ] À essayer : désinstaller jest en global et le réinstaller en global ?
- [ ] Question de créer une page dédiée de l'application qui puisse normaliser l'ajout d'un nouveau magasin
    - [ ] Modification du jira en conséquence
- [ ] Question de créer une page dédiée de l'application qui puisse formater le principe d'utiliser l'API  pour aller taper dans la bdd pour y inscrire les nouveaux magasins
    - [ ] Modification du jira en conséquence
- [ ] Ajouter un log de l'état des caisses au changement d'état : qui a modifié quelle caisse, vers quel état et quand
    - [ ] Modification du jira en conséquence