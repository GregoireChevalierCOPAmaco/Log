**24 Mars**
- [ ] Poursuite keycloak
    - [x] Affichage du bouton de link sidebar en fonction du rôle
        - [x] Refacto du code avec tous les rôles autorisés à voir le lien
            - [x] Voir (https://www.geekstrick.com/view-component-based-on-user-role/) pour le stack des roles assignés à la vision d'un component
            - [x] Utilisation de :
            ```
            this.keycloak.isUserInRole('kmo-predict_store-manager')
            ```
            pour ce faire ?
    - [x] Bloquer l'accès la page cop a ceux qui n'ont pas le rôle requis
        - [x] Récupération du rôle dans l’app front
        - [x] Reprise des routes de l'app-routing.component (fait par Théo)
        - [x] Modification de l’authguard (fait par Théo)
        - [x] Restriction de l’accès à la route en fonction du rôle 
        - [x] Appliquer la restriction à toutes les routes nécessaires
        - [x] Push & merge
    - [ ] KP-139 : Suppression d'un magasin
        - [x] Définition & assignation de la tâche sur Jira
        - [ ] Renseignements sur l’intercation entre code API & keycloak
        - [ ] Renseignements sur la suppression de store via l’API
        - [ ] Création d’une page dédiée à la suppression
        - [ ] Restriction de l’accès à la page aux users ayant le rôle pour
        - [ ] Renseignements sur la suppression d’un attribut store à un user via API
        - [ ] Associer la suppression d’un attribut store & la suppression du store en db