**5 Décembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
- [ ] Résolution du problème de mail pro J.Ivanoff
    - [x] Mail envoyé sur adresse perso
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
    - [ ] Automatisation de la création de 2 stores si la db est vide
        - [x] Retour de sytnhèse de fail de test update crud report : 
        ```
        -> besoin de créer 2 instances de *store* avec id  1 & 2 pour faire passer les tests. Voir pour automatiser si non créés ?
        ```
        - [x] Création de 2 stores aux id 1 & 2 lors du test C du crud report
            - [x] Création de la function createStore
            - [x] Création de 2 stores test
            - [x] Écriture des tests correspondants
            - [x] Résolution de l'erreur : 
            ```
            Cannot find alias for relation at store
            ```
            Check de (https://github.com/typeorm/typeorm/issues/8310)
            - [x] Comportement observé :
                - au 1er docker build up, le reports.service.spec.ts fail car report n'est pas trouvé, dans la timeline, les 2 stores ne sont pas encore en base et donc l'update du report ne se fait pas.
                - au 2e docker build up, le reports.service.spec.ts renvoie Cannot find alias for relation at store, & Jest has detected the following 1 open handle potentially keeping Jest from exiting: ●  TCPWRAP
            - [x] Résolution du TCPWRAP en ajoutant un afterAll() et un close ligne 653 : 
            ```
            afterAll(async () => {
			    db.close();
		    });
            ```
            - [x] Résolution du fail récurrent sur l'update : changement de la propriété à update de store à reference
            - [x] Résolution de l'erreur :
            ```
            TypeORMError: Empty criteria(s) are not allowed for the delete method.
            ```
            la première fois *uniquement* que les tests sont lancés après l'initialisation du docker.  
            Récap de l'erreur : le test ne fail que la première fois après avoir build le docker. En plus, le delete du rapport se fait via son ticket, et le code est correct, donc osef.
    - [ ] Résolution de l'erreur :
    ```
    Nest can't resolve dependencies of the UsersService (UserRepository, JwtService, ?). Please make sure that the argument MailerService at index [2] is available in the UsersModule context.
    ``` 
- [ ] Faire une passe commentaire descriptif utile et clair du code
    - [ ] Supprimer les commentaires de dev inutiles
    - [ ] Checker les coventions jsdoc
        - [ ] Voir (https://jsdoc.app/index.html, https://jsdoc.app/tags-returns.html)
    - [ ] Définir dans un document commun avec Théo & Anthony la manière de commenter 
    - [ ] Commenter le code

  
**6 Décembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
- [ ] Résolution des erreurs post update docker
    - [ ] e2e
        - [x] copie du fichier et suppression pour reprise des e2e from scratch
        - [ ] Reprise de documentation
        - [ ] Refonte du fichier
- [ ] Faire une passe commentaire descriptif utile et clair du code
    - [ ] Supprimer les commentaires de dev inutiles
    - [ ] Checker les coventions jsdoc
        - [ ] Voir (https://jsdoc.app/index.html, https://jsdoc.app/tags-returns.html)
    - [ ] Définir dans un document commun avec Théo & Anthony la manière de commenter 
    - [ ] Commenter le code 

- [ ] Parler avec Guillaume de la structure/architecture portail saas (keycloak) des projets pour uniformisation & harmonisation des projets en cours & à venir
- [ ] Penser à donner un justificatif de nouveau domicile à hubert

