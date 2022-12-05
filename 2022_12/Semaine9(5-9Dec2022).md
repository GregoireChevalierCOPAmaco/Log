**5 Décembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
- [ ] Démarche obtention contrat 4G Orange 
    - [ ] Garder un oeil sur la gestion d'équipement de test
- [ ] Résolution des erreurs post update docker
    - [x] Processus de rebuild du docker : 
        ```
        docker volume prune    
        ``` 
        Pour suppression volume & clean de l'espace associé
        ```
        docker compose up --build
        ```
        Pour rebuild le docker à jour, à faire aussi souvent qu'on change l'application
    - [ ] Résolution de l'erreur :
    ```
    Nest can't resolve dependencies of the UsersService (UserRepository, JwtService, ?). Please make sure that the argument MailerService at index [2] is available in the UsersModule context.
    ``` 
    -> besoin de créer 2 instances de *store* avec id  1 & 2 pour faire passer les tests. Voir pour automatiser si non créés ?
- [ ] Faire une passe commentaire descriptif utile et clair du code
    - [ ] Supprimer les commentaires de dev inutiles
    - [ ] Checker les coventions jsdoc
        - [ ] Voir (https://jsdoc.app/index.html, https://jsdoc.app/tags-returns.html)
    - [ ] Définir dans un document commun avec Théo & Anthony la manière de commenter 
    - [ ] Commenter le code 

- [ ] Parler avec Guillaume de la structure/architecture portail saas (keycloak) des projets pour uniformisation & harmonisation des projets en cours & à venir
- [ ] Penser à donner un justificatif de nouveau domicile à hubert

