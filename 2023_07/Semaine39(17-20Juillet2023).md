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
                    - [x] Chenagement de la commande à :
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


# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2;
#        listen       [::]:443 ssl http2;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers PROFILE=SYSTEM;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        error_page 404 /404.html;
#        location = /404.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#        location = /50x.html {
#        }
#    }



    server {
    if ($host = predict-beta.cop-amaco.digital) {
        return 301 https://$host$request_uri;
    } # managed by Certbot
    server_name  predict-beta.cop-amaco.digital;
    return 404; # managed by Certbot

    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header        X-Forwarded-Proto $scheme;
    proxy_set_header        X-Forwarded-Host $host;
    proxy_set_header        Host $host;

    location / {
        root /usr/share/nginx/html;
    index index.html index.htm;
    try_files $uri $uri/ /index.html =404;
  }

    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/predict-beta.cop-amaco.digital/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/predict-beta.cop-amaco.digital/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}


    server {
    if ($host = predict-beta.cop-amaco.digital) {
        return 301 https://$host$request_uri;
    } # managed by Certbot




        listen       80;
        listen       [::]:80;
        server_name  predict-beta.cop-amaco.digital;
    return 404; # managed by Certbot


}}