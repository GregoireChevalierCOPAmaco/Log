**26 Juin**
- [ ] Check de let's encrypt (https://letsencrypt.org/fr/getting-started/)
- [ ] Récupération d'un certificat letsencrypt
    - [x] Suivi de (https://mistonline.in/wp/how-to-secure-apache-using-lets-encrypt-on-amazon-linux-2/)
    - [ ] Installation des modules nécessaires
        - [ ] certbot
            ```
            sudo yum install certbot
            ```
            retour console : 
            ```
            Last metadata expiration check: 3:37:47 ago on Fri Jun 23 04:53:43 2023.
            No match for argument: certbot
            Error: Unable to find a match: certbot
            ```
            check de (https://unix.stackexchange.com/questions/744633/how-to-install-certbot-via-snap-on-amazon-linux-2023) pour comprendre
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