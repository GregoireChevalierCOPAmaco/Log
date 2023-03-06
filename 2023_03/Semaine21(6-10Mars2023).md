**6 Mars**
- [ ] Passage au keycloak predict
    - [ ] Trouver le moyen d'exporter les users en meme temps que le realm ?
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Setup du repo
        - [ ] Mise en place l'authguard
            - [ ] Résolution de l'erreur : 
            ```
            error TS2416: Property 'intercept' in type 'KeycloakBearerInterceptor' is not assignable to the same property in base type 'HttpInterceptor'.
            ```
            - [ ] Recherches sur l'erreur : 
        - [ ] Ajouter au fichier app.component.html les bouton de déconnexion & infos user
        - [ ] Faire en sorte que le username = email
- [ ] Lien avec les autres applis en fonction de qui est connecté
    - [ ] Réussir à afficher le rôle de l'user connecté
    - [ ] Afficher un lien vers l'app 4567 en fonction