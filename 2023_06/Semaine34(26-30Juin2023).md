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
            - [ ] Installation d'nginx
                - [x] Check de (https://medium.com/@mertcal/install-nginx-on-ec2-instance-in-30-seconds-e714d427b01b)
                - [x] ```sudo yum install nginx```
                retour positif : 
                ```
                 Package                       Architecture     Version                            Repository             Size
                ===============================================================================================================
                Installing:
                nginx                         x86_64           1:1.24.0-1.amzn2023.0.1            amazonlinux            32 k
                Installing dependencies:
                generic-logos-httpd           noarch           18.0.0-12.amzn2023.0.3             amazonlinux            19 k
                gperftools-libs               x86_64           2.9.1-1.amzn2023.0.2               amazonlinux           309 k
                libunwind                     x86_64           1.4.0-5.amzn2023.0.2               amazonlinux            66 k
                nginx-core                    x86_64           1:1.24.0-1.amzn2023.0.1            amazonlinux           586 k
                nginx-filesystem              noarch           1:1.24.0-1.amzn2023.0.1            amazonlinux           9.0 k
                nginx-mimetypes               noarch           2.1.49-3.amzn2023.0.3              amazonlinux            21 k

                Transaction Summary
                ===============================================================================================================
                Install  7 Packages

                Total download size: 1.0 M
                Installed size: 3.4 M
                Is this ok [y/N]: y
                Complete
                ```
                - [x] Modification de la config nginx :
                ```sudo vi /etc/nginx/nginx.conf```, set de worker_process à ```auto```
                - [ ] Démarrage : ```sudo systemctl start nginx.service``` et vérification avec  ```sudo systemctl status nginx.service```
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


**27 Juin**
- [ ] Check de let's encrypt (https://letsencrypt.org/fr/getting-started/)
- [ ] Récupération d'un certificat letsencrypt
    - [ ] Installation des modules nécessaires
        - [ ] certbot
            - [ ] Installation de certbot
                - [x] Installation de nginx
                - [ ] Installation de snapd
                - [ ] Installation de certbot
                - [ ] Installation de nginx plugin for certbot
            - [ ] Configuration du virtual host
            - [ ] Request de certificat
            - [ ] Update du path de crtificat ssl
            - [ ] Restart nginx & test du reach  
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
