**24 Mars**
- [ ] Poursuite keycloak
    - [x] Affichage du bouton de link sidebar en fonction du rôle
        - [ ] Refacto du code avec tous les rôles autorisés à voir le lien
            - [x] Voir (https://www.geekstrick.com/view-component-based-on-user-role/) pour le stack des roles assignés à la vision d'un component
            - [ ] Utilisation de :
            ```
            this.keycloak.isUserInRole('kmo-predict_store-manager')
            ```
            pour ce faire ?
    - [ ] Bloquer l'accès la page cop a ceux qui n'ont pas le rôle requis
        - [x] Récupération du rôle dans l’app front
        - [ ] Reprise des routes de l'app-routing.component (fait par Théo)
        - [ ] Modification de l’authguard (fait par Théo)
        - [ ] Restructuration des rôles dans les groupes du keycloak (fait par Théo)
        - [ ] Restriction de l’accès à la route en fonction du rôle 
        - [ ] Appliquer la restriction à toutes les routes nécessaires
        - [ ] Push & merge