**19 Juin**
- [ ] Mise en prod
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [x] Doc (https://docs.docker.com/compose/networking/#use-a-pre-existing-network)
        - [x] Suppression de la partie keycloak
        - [x] Ajout à la fin du doc de : 
        ```
        networks:
            app:
            kc-network:
                external:true
        ```
        - [x] Lancement de la commande ```docker compose --env-file .env.dev -f docker-compose2apps.yml up --renew-anon-volumes --always-recreate-deps --build  ``` pour retour fail :
        ```
        networks.kc-network must be a mapping or null
        ```
        Résolution : indentation de networks:
        - [x] Lancement des apps, front fail to compile : 
        ```
        kmo-predict-front:dev: Error: ENOMEM: not enough memory, scandir '/app/apps/kmo-predict-front/src/assets'
        kmo-predict-front:dev:
        kmo-predict-front:dev:
        kmo-predict-front:dev:
        kmo-predict-front:dev: ** Angular Live Development Server is listening on 0.0.0.0:3000, open your browser on http://localhost:3000/ **
        kmo-predict-front:dev:
        kmo-predict-front:dev:
        kmo-predict-front:dev: ✖ Failed to compile.        
        ```
        - [ ] Résolution du fail to compile
        - [ ] Résolution 
        ```
        2023-06-19T12:42:07.474329675Z 2023-06-19 12:42:07,474 ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Failed to start server in (production) mode
        2023-06-19T12:42:07.474626311Z 2023-06-19 12:42:07,474 ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Failed to obtain JDBC connection
        2023-06-19T12:42:07.475876628Z 2023-06-19 12:42:07,475 ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Connection to localhost:5432 refused. Check that the hostname and port are correct and that the postmaster is accepting TCP/IP connections.
        2023-06-19T12:42:07.476230260Z 2023-06-19 12:42:07,476 ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Connection refused (Connection refused)
        2023-06-19T12:42:07.476489608Z 2023-06-19 12:42:07,476 ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) For more details run the same command passing the '--verbose' option. Also you can use '--help' to see the details about the usage of the particular command.
        ```
        il semble que le changement du --network fasse tout casser
        (https://www.mastertheboss.com/keycloak/keycloak-with-docker/) suggère de lancer une image postgres en plus à coté ?
        - [ ] Application au docker compose
    - [ ] Lancer la giga commande docker run du keycloak avec en flag le network externe 
    - [ ] Reproduire le schéma en prod