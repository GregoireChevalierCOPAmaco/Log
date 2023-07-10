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
                - [ ] Modifications sur la config nginx 
                    - [x] Ajout du bloc 
                    ```
                    server {
                        server_name  keycloak-beta.cop-amaco.digital;
                        root         /usr/share/nginx/html;
                    proxy_set_header	X-Forwarded-For $proxy_add_x_forwarded_for;
                    proxy_set_header	X-Forwarded-Proto $scheme;
                    proxy_set_header	X-Forwarded-Host $host;
                    proxy_set_header	Host $host;

                        # Load configuration files for the default server block.
                        include /etc/nginx/default.d/*.conf;

                        error_page 404 /404.html;
                        location = /404.html {
                        }

                        error_page 500 502 503 504 /50x.html;
                        location = /50x.html {
                        }

                        location / {
                            proxy_pass http://localhost:8080;
                        }
                        
                        listen [::]:443 ssl ipv6only=on; # managed by Certbot
                        listen 443 ssl; # managed by Certbot
                        ssl_certificate /etc/letsencrypt/live/keycloak-beta.cop-amaco.digital/fullchain.pem; # managed by Certbot
                        ssl_certificate_key /etc/letsencrypt/live/keycloak-beta.cop-amaco.digital/privkey.pem; # managed by Certbot
                        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
                        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

                    }
                    ```
                    - [x] Run de ```nginx -t``` pour vérifier l'état du fichier de conf et retour négatif : 
                    ```
                    nginx: [emerg] "server" directive is not allowed here in /etc/nginx/nginx.conf:63
                    nginx: configuration file /etc/nginx/nginx.conf test failed
                    ```
                    - [x] Déplacement du bloc server dans le bloc http et retour négatif : 
                    ```
                    nginx: [emerg] duplicate listen options for [::]:443 in /etc/nginx/nginx.conf:85
                    ```
                    mise en commentaire et config successful
                    - [ ] Ajout du bloc : 
                    ```
                    server {
                        if ($host = keycloak-beta.cop-amaco.digital) {
                            return 301 https://$host$request_uri;
                        } # managed by Certbot


                            listen       80;
                            listen       [::]:80;
                            server_name  keycloak-beta.cop-amaco.digital;
                        return 404; # managed by Certbot


                    }
                    ```
                    - [x] Refonte finale et fonctionnelle du fichier de conf : 
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
                            server_name  keycloak-beta.cop-amaco.digital;
                            root         /usr/share/nginx/html;
                            proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
                            proxy_set_header        X-Forwarded-Proto $scheme;
                            proxy_set_header        X-Forwarded-Host $host;
                            proxy_set_header        Host $host;

                            # Load configuration files for the default server block.
                            include /etc/nginx/default.d/*.conf;

                            error_page 404 /404.html;
                            location = /404.html {
                            }

                            error_page 500 502 503 504 /50x.html;
                            location = /50x.html {
                            }

                            location / {
                                    proxy_pass http://localhost:8080;
                            }

                        listen [::]:443 ssl ipv6only=on; # managed by Certbot
                        listen 443 ssl; # managed by Certbot
                        ssl_certificate /etc/letsencrypt/live/keycloak-beta.cop-amaco.digital/fullchain.pem; # managed by Certbot
                        ssl_certificate_key /etc/letsencrypt/live/keycloak-beta.cop-amaco.digital/privkey.pem; # managed by Certbot
                        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
                        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

                    }

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
                        if ($host = keycloak-beta.cop-amaco.digital) {
                            return 301 https://$host$request_uri;
                        } # managed by Certbot


                            listen       80;
                            listen       [::]:80;
                            server_name  keycloak-beta.cop-amaco.digital;
                        return 404; # managed by Certbot


                    }}
                    ```
                - [x] Restart de nginx
                ```
                sudo systemctl restart nginx
                ```
                - [x] Accéder au keycloak en https
    - [x] Renouvellement automatique du certificat via cron
        - [x] Recherches
        - [x] Ajout de la commande cron
        ```
        crontab -e
        1
        0 0 1 * * certbot renew "sudo systemctl restart nginx"
        ```
        ce qui équivaut à lancer le script de renouvellement de certificat tous les mois
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