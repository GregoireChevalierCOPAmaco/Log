**17 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Faire démarrer les apps sur un serveur séparé de prod
        - [x] Connexion à l'instance en SSH avec : 
        ```
        ssh -i "predict-beta-clepaire.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
        ```
        Connexion success !
        &&
        ```
        ssh -i "keycloak-beta-clepaire.pem" ubuntu@ec2-3-75-5-27.eu-central-1.compute.amazonaws.com
        ```
- [ ] Lier les apps au serveur keycloak
    - [ ] "Connecter" les applications au serveur keycloak
        - [ ] Front
            - [ ] Modifier le lien au keycloak dans l'app.module.ts
                - [x] Sur le serveur de prod des apps, positionnement dans le dossier KMO_PREDICT/apps/kmo-predict-front/src$
                - [x] Modifier les fichiers environment.ts & environment.prod.ts, changement de :
                ```
                export const environment = {
                production: false,
                apiUrl: 'http://localhost:3001',
                socketUrl: 'http://localhost:3001',
                keycloakUrl: 'http://localhost:8080',
                };
                ```
                à  :
                ```
                export const environment = {
                production: true,
                apiUrl: 'http://predict-beta.cop-amaco.digital:3001',
                socketUrl: 'http://predict-beta.cop-amaco.digital:3001',
                keycloakUrl: 'https://keycloak-beta.cop-amaco.digital/'
                };
                ```
                - [ ] Modifier le fichier .env pour correspondre à la config actuelle
                - [ ] Rebuild le docker
                    - [x] Modif du docker compose beta : 
                    ```
                    version: "3.9"

                        services:

                        apps:
                            container_name: predict-apps-beta
                            build:
                            context: .
                            dockerfile: Dockerfile.dev
                            ports:
                            - "3000:3000"
                            - "3001:3001"
                            env_file:
                            - .env.beta
                            volumes:
                            - ./apps:/app/apps
                            - /app/node_modules
                            - /app/apps/kmo-predict-back/node_modules
                            - /app/apps/kmo-predict-back/dist
                            - /app/apps/kmo-predict-front/node_modules
                            - /app/apps/kmo-predict-front/.angular
                            - /app/apps/kmo-predict-front/dist
                            - /app/packages/eslint-config-custom/node_modules
                            - /app/packages/tsconfig/node_modules
                            networks:
                            - app
                            - keycloak

                        postgres:
                            container_name: predict-pg
                            image: postgres:15.0-alpine3.16
                            restart: on-failure
                            environment:
                            - POSTGRES_USER=${DB_USER?}
                            - POSTGRES_PASSWORD=${DB_PASSWORD?}
                            - POSTGRES_DB=${DB_NAME?}
                            ports:
                            - "5432:5432"
                            networks:
                            app:

                        networks:
                        app:
                            name: app_network
                            external: true
                        keycloak:
                            name: keycloak_network
                            external: true
                    ```
                    - [x] Changement de la commande à :
                    ```
                    sudo docker compose --env-file .env.beta -f docker-compose.beta.yml up --renew-anon-volumes --always-recreate-deps --build
                    ```
        - [x] Résolution des erreurs : 
            - [x] Connexion à la db :
            ```
            ERROR [TypeOrmModule] Unable to connect to the database. Retrying (1)...
            Error: connect ETIMEDOUT 172.31.39.193:6543
            at TCPConnectWrap.afterConnect [as oncomplete] (node:net:1300:16)
            ```
            - [x] Ouvrir le port 3000 sur aws
        - [x] Recréer le realm keycloak sur la prod
            - [x] Créer le fichier realm export sur le serveur prod keycloak
        - [x] Configurer le client predict dans le realm
        - [x] Sécuriser la connexion à angular
            - [x] Installer nginx : 
            ```
            sudo apt install nginx
            Y
            ```
            - [x] Générer le certificat
                - [x] Installation de certbot
                ```
                sudo apt-get remove certbot
                sudo snap install --classic certbot
                sudo ln -s /snap/bin/certbot /usr/bin/certbot
                ```
            - [x] Configurer nginx
                - [x] Ajout de :
                ```
                server {
                listen 80;

                location / {
                    root /usr/share/nginx/html;
                    index index.html index.htm;
                    try_files $uri $uri/ /index.html =404;
                }
                }
                ```
                et au chargement de la page, erreur : 
                predict-beta.cop-amaco.digital vous a redirigé à de trop nombreuses reprises.
                ERR_TOO_MANY_REDIRECTS
                - [x] Résolution de l'erreur too many redirects : 
                ```
                user www-data;
                worker_processes auto;
                error_log /var/log/nginx/error.log notice;
                pid /run/nginx.pid;

                # Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
                include /usr/share/nginx/modules/*.conf;

                events {
                    worker_connections 1024;
                }

                http {
                    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                                    '$status $body_bytes_sent "$http_referer" '
                                    '"$http_user_agent" "$http_x_forwarded_for"';

                    access_log  /var/log/nginx/access.log  main;

                    sendfile            on;
                    tcp_nopush          on;
                    keepalive_timeout   65;
                    types_hash_max_size 4096;

                    include             /etc/nginx/mime.types;
                    default_type        application/octet-stream;

                    # Load modular configuration files from the /etc/nginx/conf.d directory.
                    # See http://nginx.org/en/docs/ngx_core_module.html#include
                    # for more information.
                    include /etc/nginx/conf.d/*.conf;

                    server {
                        listen 80;
                        listen [::]:80;
                        server_name predict-beta.cop-amaco.digital;

                        location / {
                            return 301 https://$host$request_uri;
                        }
                    }

                    server {
                        listen 443 ssl;
                        listen [::]:443 ssl;
                        server_name predict-beta.cop-amaco.digital;

                        ssl_certificate /etc/letsencrypt/live/predict-beta.cop-amaco.digital/fullchain.pem; # managed by Certbot
                        ssl_certificate_key /etc/letsencrypt/live/predict-beta.cop-amaco.digital/privkey.pem; # managed by Certbot
                        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
                        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

                        location / {
                            root /usr/share/nginx/html;
                            index index.html index.htm;
                            try_files $uri $uri/ /index.html =404;
                        }
                    }
                }
                ```
            - [x] Ouvrir les ports sur amazon
            - [ ] Reach l'application angular
        - [x] Retour de warnings apps  : 
        ```
        Warning: This is a simple server for use in testing or debugging Angular applications
        locally. 
        It hasn't been reviewed for security issues.

        Binding this server to an open connection can result in compromising your application or
        computer. 
        Using a different host than the one passed to the "--host" flag might result in websocket connection issues. 
        You might need to use "--disable-host-check" if that's the case.
        ```
        - [ ] Reach l'application angular de beta
            - [ ] Modifier la config nginx
            - [ ] Modifier le docker compose
            - [ ] Modifier le Dockerfile
            - [ ] Rebuild les containers
        - [ ] Configurer angular pour la redirection au keycloak de prod
        - [ ] Setup les apps pour la prod


**18 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Faire démarrer les apps sur un serveur séparé de prod
        - [x] Connexion à l'instance en SSH avec : 
        ```
        ssh -i "predict-beta-clepaire.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
        ```
        Connexion success !
        &&
        ```
        ssh -i "keycloak-beta-clepaire.pem" ubuntu@ec2-3-75-5-27.eu-central-1.compute.amazonaws.com
        ```
- [ ] Lier les apps au serveur keycloak
    - [ ] "Connecter" les applications au serveur keycloak
        - [ ] Front
            - [x] Modifier le lien au keycloak dans l'app.module.ts
        - [ ] Reach l'application angular de beta
            - [x] Modifier la config nginx
            - [ ] Modifier le docker compose
                - [ ] Résolution du problème : 
                ```
                Error response from daemon: failed to create shim task: 
                OCI runtime create failed: runc create failed: 
                unable to start container process: 
                error during container init: error mounting "/etc/nginx/nginx.conf" to rootfs at "/etc/nginx/nginx.conf": 
                mount /etc/nginx/nginx.conf:/etc/nginx/nginx.conf (via /proc/self/fd/6), 
                flags: 0x5000: not a directory: unknown: 
                Are you trying to mount a directory onto a file (or vice-versa)? 
                Check if the specified host path exists and is the expected type
                ```
                - [x] Arret des containers avec ```sudo docker stop nginx predict-apps-beta predict-pg``` et prune des volumes ``` sudo docker volume prune -f```
                - [x] Ajout de ./ avant le chemin vers la source des fichiers dans le dockerfile pour partir du chemin relatif
                - [x] Rebuild des containers, sans succès.
                - [x] Copie des fichiers dans le dossier source du docker compose
                    - [x] Copie du fichier de conf nginx
                    - [x] Impossible de déplacer les fichiers ssl & clé pem
                    - [x] Rebuild des containers, le container nginx exit car il ne trouve pas les fichiers de certificats : 
                    ```
                    2023/07/18 07:46:23 [emerg] 1#1: open() "/etc/letsencrypt/options-ssl-nginx.conf" failed (2: No such file or directory) in /etc/nginx/nginx.conf:41
                    nginx: [emerg] open() "/etc/letsencrypt/options-ssl-nginx.conf" failed (2: No such file or directory) in /etc/nginx/nginx.conf:41
                    ```
                    - [x] Copie des fichiers
                - [ ] Résolution de l'erreur du container nginx : 
                ```
                2023/07/18 08:51:14 [emerg] 1#1: host not found in upstream "apps" in /etc/nginx/nginx.conf:45
                nginx: [emerg] host not found in upstream "apps" in /etc/nginx/nginx.conf:45
                ubuntu@ip-172-31-5-119:~/KMO_PREDICT$ cat nginx/nginx.conf
                ```
                - [x] Changement dans le fichier nginx.conf de :
                ```
                proxy_pass http://apps:3000;
                ```
                à 
                ```
                proxy_pass http://predict-apps-beta:3000;
                ```
                et retour même erreur :
                ```
                host not found in upstream "predict-apps-beta"  
                ```
                - [x] check de la connectivité entre les containers via le network "app_network" avec 
                ```
                docker network inspect app_network
                ```
                et retour : 
                ```
                ... 
                "Containers": {
                        "937b74f071805cfaca5db59b6c4d3dee43503ac909d1ecd7e061c663d1f4f743": {
                            "Name": "predict-apps-beta",
                            "EndpointID": "cfe5339e5fcc76faadab4a940b58c2f53d7f8951edbcb079cd58310a17d0ca63",
                            "MacAddress": "02:42:ac:16:00:03",
                            "IPv4Address": "172.22.0.3/16",
                            "IPv6Address": ""
                        },
                        "c1efb6a639fff97da100885c782658afb79126015254891472aebaabaf080a8a": {
                            "Name": "predict-pg",
                            "EndpointID": "67933d89badf5c90466f6a145ad0ebc13f928cfea48153c70a14bc297baed66b",
                            "MacAddress": "02:42:ac:16:00:02",
                            "IPv4Address": "172.22.0.2/16",
                            "IPv6Address": ""
                        }
                    },
                ```        
                ce qui montre que le container nginx n'y est pas connecté.        
                - [x] Modif du dockerfile : 
                ```
                version: "3.9"

                services:
                nginx:
                    container_name: nginx
                    image: nginx
                    volumes:
                    - ./nginx/nginx.conf:/etc/nginx/nginx.conf
                    - ./nginx/options-ssl-nginx.conf:/etc/letsencrypt/options-ssl-nginx.conf
                    - ./nginx/ssl-dhparams.pem:/etc/letsencrypt/ssl-dhparams.pem
                    - ./nginx/fullchain.pem:/etc/letsencrypt/live/predict-beta.cop-amaco.digital/fullchain.pem:ro
                    - ./nginx/privkey.pem:/etc/letsencrypt/live/predict-beta.com-amaco.digital/privkey.pem:ro
                    ports:
                    - "80:80"
                    - "443:443"
                    depends_on:
                    - apps
                    networks:
                    - app

                apps:
                    container_name: predict-apps-beta
                    build:
                    context: .
                    dockerfile: Dockerfile.dev
                    ports:
                    - "3000:3000"
                    - "3001:3001"
                    env_file:
                    - .env.beta
                    volumes:
                    - ./apps:/app/apps
                    - /app/node_modules
                    - /app/apps/kmo-predict-back/node_modules
                    - /app/apps/kmo-predict-back/dist
                    - /app/apps/kmo-predict-front/node_modules
                    - /app/apps/kmo-predict-front/.angular
                    - /app/apps/kmo-predict-front/dist
                    - /app/packages/eslint-config-custom/node_modules
                    - /app/packages/tsconfig/node_modules
                    networks:
                    - app

                postgres:
                    container_name: predict-pg
                    image: postgres:15.0-alpine3.16
                    restart: on-failure
                    environment:
                    - POSTGRES_USER=${DB_USER?}
                    - POSTGRES_PASSWORD=${DB_PASSWORD?}
                    - POSTGRES_DB=${DB_NAME?}
                    ports:
                    - "5432:5432"
                    networks:
                    app:

                networks:
                app:
                    name: app_network
                ```
                le container demarre maintenant mais avec l'erreur :
                - [x] Résolution de l'erreur :
                ```
                cannot load certificate key "/etc/letsencrypt/live/predict-beta.cop-amaco.digital/privkey.pem": BIO_new_file() failed (SSL: error:80000002:system library::No such file or directory:calling fopen(/etc/letsencrypt/live/predict-beta.cop-amaco.digital/privkey.pem, r) error:10000080:BIO routines::no such file)
                nginx: [emerg] cannot load certificate key "/etc/letsencrypt/live/predict-beta.cop-amaco.digital/privkey.pem": BIO_new_file() failed (SSL: error:80000002:system library::No such file or directory:calling fopen(/etc/letsencrypt/live/predict-beta.cop-amaco.digital/privkey.pem, r) error:10000080:BIO routines::no such file)
                ```
                on a peut etre pas les droit d'accès au folder live du ubuntu dockerisé ?
                changement du folder de destination dans dockerfile :
                ```
                ...
                services:
                    nginx:
                        container_name: nginx
                        image: nginx
                        volumes:
                        - ./nginx/nginx.conf:/etc/nginx/nginx.conf
                        - ./nginx/options-ssl-nginx.conf:/etc/letsencrypt/options-ssl-nginx.conf
                        - ./nginx/ssl-dhparams.pem:/etc/letsencrypt/ssl-dhparams.pem
                        - ./nginx/fullchain.pem:/etc/nginx/ssl/fullchain.pem
                        - ./nginx/privkey.pem:/etc/nginx/ssl/privkey.pem
                ```
                & dans la config nginx : 
                ```
                    server {
                listen 443 ssl;
                listen [::]:443 ssl;
                server_name predict-beta.cop-amaco.digital;

                #        ssl_certificate /etc/letsencrypt/live/predict-beta.cop-amaco.digital/fullchain.pem; # managed by Certbot
                #       ssl_certificate_key /etc/letsencrypt/live/predict-beta.cop-amaco.digital/privkey.pem; # managed by Certbot
                ssl_certificate /etc/nginx/ssl/fullchain.pem;
                ssl_certificate_key /etc/nginx/ssl/privkey.pem;
                ```
                puis relance de ``` sudo docker compose --env-file .env.beta -f docker-compose.beta.yml up --renew-anon-volumes --always-recreate-deps --build```
                retour au message invalid host header
                - [x] Check de (https://github.com/angular/angular-cli/issues/6349), il semblerait que ```ng serve```soit indissociable d'un host 0.0.0.0 qui est invalide pour nous ici.
                - [x] Modification du package.json du front pour inclure 
                ```
                 "scripts": {
                    "ng": "ng",
                    "start": "ng serve",
                    "dev": "ng build --watch",
                ```
                Problème de invalid host résolu ! On atterrit maintenant sur une 502
                - [ ] Résolution de la 502 bad gateway
                mon avis : c'est l'imbrication d'nginx dans docker qui fout la merde.
                Je vais essayer de revenir au point où j'avais l'invalid host header avec nginx en-dehors du docker, sur le serveur. 
                De là, lancer l'app angular avec build en  modifiant le package json du front.
                Il est possible que le certificat ait été prévu pour le nginx du serveur et que les avoir déplacés dans le docker ait foutu le zbeul.
                Une fois le setup revenu à l'invalid host header, regénérer les certificats
                Puis tout relancer et un chien ici
            - [ ] Reprise
                - [ ] Retirer la partie nginx du docker compose
                - [ ] Relancer les containers docker
                - [ ] Retaper la config nginx
                - [ ] Résoudre la 502
            - [ ] Modifier le Dockerfile
            - [ ] Rebuild les containers
        - [ ] Configurer angular pour la redirection au keycloak de prod
        - [ ] Setup les apps pour la prod



**19 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Faire démarrer les apps sur un serveur séparé de prod
        - [x] Connexion à l'instance en SSH avec : 
        ```
        ssh -i "predict-beta-clepaire.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
        ```
        Connexion success !
        &&
        ```
        ssh -i "keycloak-beta-clepaire.pem" ubuntu@ec2-3-75-5-27.eu-central-1.compute.amazonaws.com
        ```
- [ ] Lier les apps au serveur keycloak
    - [ ] "Connecter" les applications au serveur keycloak
        - [ ] Reach l'application angular de beta
            - [ ] Résolution de la 502 bad gateway
                - [x] Retirer la partie nginx du docker compose
                - [x] Relancer les containers docker
                - [x] Retaper la config nginx
                - [x] Demande de conseil à gpt, ses suggestions : 
                - Check Docker Container Logs:
                - Ensure Angular Application is Running:
                - Check Security Groups (Verify that the security group associated with your EC2 instance allows incoming traffic on port 3000 (the port where your Angular application is running). Additionally, ensure that the outbound rules of the security group are appropriately configured to allow the instance to communicate with external services like Keycloak.)
                - Verify Keycloak Configuration:
                - Check Nginx Error Logs:
                - Check SELinux (Security-Enhanced Linux) (If you're running SELinux on your server, it might be blocking Nginx from communicating with the Docker container. You can check SELinux status with the following command: ```sestatus``` & ```sudo setenforce 0```)
                - Test Connectivity: use ```curl http://localhost:3000``` from within the ec2
            - [ ] Modifier le Dockerfile
            - [ ] Rebuild les containers
        - [ ] Configurer angular pour la redirection au keycloak de prod
        - [ ] Setup les apps pour la prod

        si la config nginx ne marche toujours pas, demain : commencer un projet angular keycloak hypersimple


**20 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Faire démarrer les apps sur un serveur séparé de prod
        - [x] Connexion à l'instance en SSH avec : 
        ```
        ssh -i "predict-beta-clepaire.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
        ```
        Connexion success !
        &&
        ```
        ssh -i "keycloak-beta-clepaire.pem" ubuntu@ec2-3-75-5-27.eu-central-1.compute.amazonaws.com
        ```
- [ ] Lier les apps au serveur keycloak
    - [ ] "Connecter" les applications au serveur keycloak
        - [x] Extinction des containers de beta & changement config nginx
        - [x] Essai avec l'app predict angular depuis le serveur
            - [x] Installation des dépendances sur le serveur beta
                - [x] ```sudo apt install npm```
                - [x] ```npm install @angular/cli```
            - [x] Echec, run ng ou npm ouvre l'éditeur de texte MG, ou micro
            - [x] Désinstallation de mg++ ```sudo apt-get remove mg++```
            et clean :
            ```
            sudo apt-get purge mg++
            sudo apt-get autoremove
            sudo apt remove mg ng-common
            ```
            - [x] ```npm install -g @angular/cli```
            - [x] Ajout du lien ```export PATH="$PATH:/usr/local/bin"```
            - [x] Update de node : 
            ```
            curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
            nvm install v14.20.0
            ```
            - [x] Modification des permissions :
            ```
            chmod -R 755 .
            chmod -R 777 .angular
            chmod -R 777 node_modules
            ```
            - [x] Installation de kc-angular : 
            ```
            npm install keycloak-angular
            npm install keycloak-js
            ```
            - [x] ```ng build```
            - [x] Modification du nginx conf : 
            ```
            user www-data;
            worker_processes auto;
            error_log /var/log/nginx/error.log notice;
            pid /run/nginx.pid;

            # Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
            include /usr/share/nginx/modules/*.conf;

            events {
                worker_connections 1024;
            }

            http {
                log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                                '$status $body_bytes_sent "$http_referer" '
                                '"$http_user_agent" "$http_x_forwarded_for"';

            access_log  /var/log/nginx/access.log  main;

            sendfile            on;
            tcp_nopush          on;
            keepalive_timeout   65;
            types_hash_max_size 4096;

            include             /etc/nginx/mime.types;
            default_type        application/octet-stream;

            # Load modular configuration files from the /etc/nginx/conf.d directory.
            # See http://nginx.org/en/docs/ngx_core_module.html#include
            # for more information.
            include /etc/nginx/conf.d/*.conf;

            server {
                listen 443 ssl;
                listen [::]:443 ssl;
                server_name predict-beta.cop-amaco.digital;

                ssl_certificate /etc/letsencrypt/live/predict-beta.cop-amaco.digital/fullchain.pem; # managed by Certbot
                ssl_certificate_key /etc/letsencrypt/live/predict-beta.cop-amaco.digital/privkey.pem; # managed by Certbot
                include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
                ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

                location / {
                    root /home/ubuntu/KMO_PREDICT/apps/kmo-predict-front/dist/kmo-predict;
                    try_files $uri $uri/ /index.html =404;
                }

                location @angular {
                    # Proxy pass to the Angular development server running on localhost:3000.
                    proxy_pass http://localhost:3000;

                    # Proxy headers configuration...
                    proxy_set_header Host $host;
                    proxy_set_header X-Real-IP $remote_addr;
                    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                    proxy_set_header X-Forwarded-Proto $scheme;
                    proxy_set_header X-Forwarded-Host $host;
                }
            }
            }
            ```
            - [x] Listing des droits ```ls -l /home/ubuntu/KMO_PREDICT/apps/kmo-predict-front/dist/kmo-predict```
            - [x] Changement des doits pour le repo dist où sont buildés les fichiers de l'app angular : 
            ```
            sudo chown -R www-data:www-data /home/ubuntu/KMO_PREDICT/apps/kmo-predict-front/dist/kmo-predict
            sudo chmod -R o+rx /home/ubuntu/KMO_PREDICT/apps/kmo-predict-front/dist/kmo-predict
            ```
            - [x] Ajout à l'utilisateur ubuntu des droits à tous les dossiers de la chaîne depuis l'endroit du nginx à l'endroit des fichiers dist d'angular : 
            ```
            sudo gpasswd -a www-data ubuntu
            sudo chmod g+x /ubuntu
            sudo chmod g+x /home/ubuntu
            sudo chmod g+x /home/ubuntu/KMO_PREDICT/
            sudo chmod g+x /home/ubuntu/KMO_PREDICT/apps/
            sudo chmod g+x /home/ubuntu/KMO_PREDICT/apps/kmo-predict-front/
            sudo chmod g+x /home/ubuntu/KMO_PREDICT/apps/kmo-predict-front/dist/
            sudo chmod g+x /home/ubuntu/KMO_PREDICT/apps/kmo-predict-front/dist/kmo-predict/
            ```
            - [x] Restart & reload de nginx : 
            ```
            sudo systemctl restart nginx
            sudo systemctl reload nginx
            ```
        - [x] Vérifier la possibilité de connexion à KC
            - [x] Reach https://predict-beta.cop-amaco.digital/ redirige vers keycloak mais vers la page keycloak we are sorry page not found
        - [ ] Reach l'application angular de beta
            - [ ] Résolution de la 502 bad gateway