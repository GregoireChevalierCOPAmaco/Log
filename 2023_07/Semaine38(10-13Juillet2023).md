**7 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [x] Installer les dépendances requises
    - [x] Connexion au serveur 07juillet via 
    ```
    ssh -i "keycloak-beta-clepaire.pem" ubuntu@ec2-3-75-5-27.eu-central-1.compute.amazonaws.com
    ```
    - [x] Certifier le keycloak de prod pour https
        - [x] Arrêt des containers certbot et proxy
        ```
        sudo docker stop atmos_proxy
        sudo docker stop keycloakbeta-certbot-1
        ```
        - [x] Générer le certificat 
            - [ ] Essai depuis container dédié
            - [x] Essai depuis serveur de prod en direct avec nginx
                - [x] Installation de snapd
                ```
                sudo snap install core
                sudo snap refresh core
                ```
                - [x] Installation de nginx
                ```
                nginx -v
                Command 'nginx' not found, but can be installed with:
                sudo apt install nginx-core    # version 1.18.0-6ubuntu14.3
                sudo apt install nginx-core    # version 1.18.0-6ubuntu14.3
                ```
                - [x] Reach de (http://keycloak-beta.cop-amaco.digital/)
                - [x] Configuration du nginx.conf
                ```
                sudo nano /etc/nginx/nginx. conf
                ```
                et modifications :
                ```
                server_name keycloak-beta.cop-amaco.digital
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_set_header X-Forwarded-Host $host
                proxy_set_header Host $host;
                location / {
                    proxy_pass http://localhost:8080;
                }
                ```
                - [x] Installation de certbot
                ```
                sudo apt-get remove certbot
                sudo snap install --classic certbot
                sudo ln -s /snap/bin/certbot /usr/bin/certbot
                ```
                - [x] Génération
                ```
                sudo certbot --nginx
                g.chevalier@cop-amaco.com
                Y
                Y
                keycloak-beta.cop-amaco.digital
                ```
                - [x] Check de la config nginx  :
                ```
                sudo nginx -t
                nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
                nginx: configuration file /etc/nginx/nginx.conf test is successful
                ```
                - [x] Modification de la config nginx pour 
                - [ ] Accéder au keycloak en https
    - [ ] Renouvellement automatique du certificat via cron
    - [ ] Faire démarrer les apps sur un serveur séparé de prod
    - [ ] Lier les apps au serveur keycloak
    - [ ] 
    - [ ] 
    - [ ] Checker le DKIM amazon, reapport au mail de Franck
- [ ] Réunion followup

docker compose file as is :
```
version: '3.8'
            services:

            postgres:
                image: postgres:14.5
                container_name: keycloak-postgres
                restart: always
                environment:
                - POSTGRES_USER=postgres
                - POSTGRES_PASSWORD=${DB_PASSWORD}
                volumes:
                - postgres-data:/var/lib/postgresql/data
                ports:
                - 6543:5432

            keycloak:
                image: quay.io/keycloak/keycloak:18.0.0
                container_name: keycloak-beta07Juillet
                restart: always
                depends_on:
                - postgres
                entrypoint: bash -c "entrypoint.sh"
                environment:
                - KEYCLOAK_ADMIN=${KEYCLOAK_ADMIN}
                - KEYCLOAK_ADMIN_PASSWORD=${KEYCLOAK_ADMIN_PASSWORD}
                - KEYCLOAK_HOSTNAME=${KEYCLOAK_HOSTNAME}
                - DB_PASSWORD=${DB_PASSWORD}
                - DB_VENDOR=postgres
                - DB_ADDR=postgres
                - DB_PORT=5432
                - DB_DATABASE=postgres
                - DB_USER=postgres
                - PROXY_ADDRESS_FORWARDING=true
                ports:
                - 8443:8443
                - 8080:8080
                volumes:
                - keycloak-data:/opt/jboss/keycloak/standalone/data
                - entrypoint.sh:/entrypoint.sh:ro

            volumes:
            postgres-data:
            keycloak-data:
```

entrypoint.sh as is :
```
echo "========================================"
echo "=======         BUILDING         ======="
echo "========================================"
/opt/keycloak/bin/kc.sh build
echo "=======     STARTING SERVER      ======="
echo "========================================"
/opt/keycloak/bin/kc.sh start --import-realm --proxy edge --hostname-strict=false
```