*** LOG Gregoire Chevalier ***

**4 Novembre**
- [ ] Tests e2e
    - [ ] Premier test : it(/GET login`).
        - [x] Résolution de l'erreur :
        ```
        TypeError: this.userRepository.findOne is not a function
        ```
        -> Mise en commentaire de ```await app.init();``` dans le fichier de test e2e vu que l'application est déjà démarrée à un autre moment.
        - [ ] Résolution de l'erreur :
        ```
        expected 200 "OK", got 404 "Not Found"
        ```
    - [ ] Tester le front
        - [ ] Tester le formulaire
        - [ ] Tester la réception des données
        - [ ] Tester les données retournées
        - [ ] Tester l’envoi des données retournées au back
    - [ ] Tester le back
        - [ ] Tester la bonne réception des données
        - [ ] Tester la connexion à la db
        - [ ] Tester la bonne forme des données à envoyer
        - [ ] Tester le crud des données en base
        - [ ] S’assurer de la persistance des données en base
- [ ] Utiliser jira en parallèle du log pour communiquer sur ce qui est fait
- [ ] Penser à donner un justificatif de nouveau domicile à hubert