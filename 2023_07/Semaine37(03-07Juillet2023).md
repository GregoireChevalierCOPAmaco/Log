**29 Juin**
- [x] Mise à jour du jira
- [ ] Check de let's encrypt (https://letsencrypt.org/fr/getting-started/)
- [ ] Récupération d'un certificat letsencrypt
    - [ ] Installation des modules nécessaires
        - [ ] certbot
            - [ ] Update du path de crtificat ssl
            - [ ] Renouvellement automatique du certificat via cron
                - [x] Check si cron est actif
                ```
                sudo systemctl status cron
                ```
                et check du statut :
                ```
                sudo systemctl status crond | grep Active
                ```
            - [ ] Restart nginx & test du reach  
                - [ ] Réussir à accéder au site (3.123.128.118)  
                - [ ] Réussir à accéder au site (http://testgreg.cop-amaco.digital/)
- [ ] Ajout du certificat dans le dossier ./certificates
- [ ] Mise en prod
    - [ ] Démarrer un docker prod du keycloak sur network externe
    - [ ] Lier le keycloak à la db
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [ ] Application au docker compose
    - [ ] Lancer la giga commande docker run du keycloak avec en flag le network externe 
    - [ ] Reproduire le schéma en prod