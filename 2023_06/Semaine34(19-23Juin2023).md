**19 Juin**
- [ ] Mise en prod
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [x] Doc (https://docs.docker.com/compose/networking/#use-a-pre-existing-network)
        - [ ] Application au docker compose
    - [ ] Lancer la giga commande docker run du keycloak avec en flag le network externe 
    - [ ] Reproduire le schéma en prod