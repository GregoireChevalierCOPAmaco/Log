**27 Février**
- [ ] Poursuite démarrage du KMO-Predict
    - [ ] Réunion de catch-up avec Guillaume et Théo sur l'intégration des metadatas des gateways dans la vue cop
    - [x] Initialisations
        - [x] Résolution du passage des tests autogénérés
            - [x] Relance des tests front : tout passe
            - [x] Relance des tests back : tout passe
        - [x] Pull develop et màj du projet en local
            - [ ] Résolution des conflits
            - [x] package.json /front
            - [x] tests.ts /front
            - [x] angular.json
            - [x] package-lock.json /racine
                - [x] Accepted all current
                - [x] Deleted 46 duplicate object key
            - [x] màj des imports foireux (mat au lieu de matlegacy)
            - [x] main.ts /back, supression de l'async de startallmicroservicesasync()
            - [x] push sur github & checks successful
            - [x] test de viabilité du front
                - [x] Front ok avec ng serve dans le dossier front
                - [x] Front ok avec docker-compose dans le dossier racine
        - [x] Merge de la branche KP-90
        - [ ] Delete de la branche KP-90
- [x] Réunion followup
- [x] Intégrer les tests à la CI githubactions
    - [x] Lancement de ```turbo run test``` et retour positif : 
    ```
    Tasks:    2 successful, 2 total
    Cached:    0 cached, 2 total
    Time:    11.285s
    ```
    - [x] Modifications sur le fichier de CI
    - [x] Suppression du --no-verify du package json racine
    - [x] Commit
    - [x] Mise sur le github distant
    - [x] Passage des checks
    - [x] Suppression du --no-verify dans le root package.json
- [ ] Check de (https://www.npmjs.com/package/testcontainers) pour veille techno
- [ ] Réparer le tailwind sur la maquette angular de kmo predict
    - [x] Check de (https://stackoverflow.com/questions/71384038/many-tailwind-css-classes-doesn%C2%B4t-work-on-my-angular-12-project)
- [x] Rouvrir le projet atmos pour remise en tête de keycloak
- [x] Rouvrir le projet githubactions perso pour remise en tête de keycloak
- [ ] Se renseigner sur websocket.io
- [ ] Checker la directive specification de doc de Guillaume sur le discord
- [ ] Checker l'état de l'entité store créée par Théo