**10 Juillet**
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


**11 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Faire démarrer les apps sur un serveur séparé de prod
        - [x] Utilisation du serveur déjà existant (Predict-Beta / i-08c43697e819e6501)
        - [x] Connexion avec ```ssh -i "Predict-Beta.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com``` et retour négatif : 
        ```
        ED25519 key fingerprint is SHA256:5QwsamG38bWUUMeLJ5cOk1fcCikqzq9A0qqQHt2uomA.
        This key is not known by any other names
        Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
        Warning: Permanently added 'ec2-52-58-79-10.eu-central-1.compute.amazonaws.com' (ED25519) to the list of known hosts.
        ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com: Permission denied (publickey).
        ```
        Je n'ai pas la clé paire
            - [x] Check de (https://linux.how2shout.com/add-a-new-key-pair-to-your-exisitng-aws-ec2-instances/)
            - [x] Création d'une clé paire nommée predict-beta-clepaire.pem
            - [x] Positionnement dans le dossier via invite de commande et ```ssh-keygen -y -f "predict-beta-clepaire.pem"```
            - [x] Connexion à l'instance via l'interface web
            - [x] ```sudo nano .ssh/authorized_keys```
            - [x] Ajout du résultat de la commande ssh-keygen -f -y au fichier
            - [x] Redémarrage de l'instance
            - [x] Connexion à l'instance en SSH avec : 
            ```
            ssh -i "predict-beta-clepaire.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
            ```
            Connexion success ! 
        - [x] Découverte de l'instance
        - [ ] Lancer les apps avec un docker compose
            - [x] ```docker compose --env-file .env.dev -f docker-compose.dev.yml up --renew-anon-volumes --always-recreate-deps --build``` sur le serveur beta des apps
            - [ ] Reach l'application angular de beta
    - [ ] Lier les apps au serveur keycloak
        - [x] Demande à gpt comment faire, sa suggestion : 
            - S'assurer de la connectivité et de la connectabailité des deux instances
            - Utiliser keycloak-js adapter
            - Modifier l'app.module.ts
            - Modifier l'authguard
            - Configurer le KeycloakService d'angular
            - Configurer les client settings dans la console d'administration du keycloak de prod
        - [ ] "Connecter" les applications au serveur keycloak
            - [ ] S'assurer de la connectivité et de la connectabailité des deux instances
                - [x] GPT à ce sujet : 
                ```
                Certainly! To ensure that both EC2 instances have proper network connectivity, you can follow these steps:

                1. Identify the security groups associated with your EC2 instances. 
                You can find this information in the AWS Management Console by navigating to the EC2 service, selecting the instance, and viewing the "Security groups" section.

                2. Ensure that both instances are associated with security groups that allow the necessary network traffic to pass between them. 
                By default, instances are assigned a default security group, which allows all inbound traffic from within the same security group. 
                You can modify the rules of the default security group or create a new security group specifically for your use case.

                3. Create a new security group (if needed) and configure the inbound rules to allow the necessary communication between the instances. 
                For example, if your Keycloak container listens on port 8443 and your Angular container communicates with Keycloak over that port, you'll need to allow inbound traffic on port 8443 from the IP address or security group associated with your Angular instance.

                To add a new inbound rule to a security group:
                - Go to the AWS Management Console and navigate to the EC2 service.
                - Select "Security Groups" from the sidebar.
                - Choose the appropriate security group associated with one of your instances.
                - Click the "Edit inbound rules" button.
                - Add a new rule with the desired port and source IP or security group.

                Repeat these steps for the security group associated with the other instance, ensuring that the necessary ports and sources are allowed.

                4. Verify the network connectivity by attempting to ping one EC2 instance from the other using their private IP addresses. 
                For example, on EC2 instance 2, you can run the following command:

                ping <private-ip-of-EC2-instance-1>

                If the ping is successful, it means the instances have proper network connectivity.

                By configuring the security groups of both EC2 instances to allow the necessary network traffic, you ensure that the containers within the instances can communicate with each other over the specified ports.
                ```
                - [ ] Assurer la connexion entre les 2 instances
                - [x] Check de (https://repost.aws/questions/QUR3jtTMoyQlSE-aSmdX0-aQ/aws-internal-communication-between-2-ec2-servers)
                - [x] Élément de réponse ici : (https://www.edureka.co/community/51996/how-to-connect-an-ec2-linux-instance-another-linux-instance)
                - [x] Ping de l'instance kc à l'instance apps : 
                ```
                ping 172.31.5.119
                PING 172.31.5.119 (172.31.5.119) 56(84) bytes of data.
                64 bytes from 172.31.5.119: icmp_seq=1 ttl=64 time=1.13 ms
                64 bytes from 172.31.5.119: icmp_seq=2 ttl=64 time=0.762 ms
                64 bytes from 172.31.5.119: icmp_seq=3 ttl=64 time=0.795 ms
                64 bytes from 172.31.5.119: icmp_seq=4 ttl=64 time=0.723 ms
                64 bytes from 172.31.5.119: icmp_seq=5 ttl=64 time=0.726 ms
                ```
                mais ping de l'instance apps à l' instance kc : 
                ```
                ping 172.31.39.193
                PING 172.31.39.193 (172.31.39.193) 56(84) bytes of data.
                ^C
                --- 172.31.39.193 ping statistics ---
                15 packets transmitted, 0 received, 100% packet loss, time 14314ms
                ```
                - [ ]
            - [ ] Recréer le realm keycloak sur la prod
                - [ ] Créer le fichier realm export sur le serveur prod keycloak
            - [ ] Refaire le docker-compose
                - [ ] Ne plus lancer de container keycloak
                - [ ] Pointer vers le serveur keycloak prod à la place
                - [ ] Ne plus lancer de script entrypoint.sh (déjà lancé dans le serveur keycloak)
            - [ ] Sécuriser la connexion à angular
                - [ ] Installer nginx
                - [ ] Configurer nginx
                - [ ] Ouvrir les ports sur amazon
                - [ ] Générer le certificat
                - [ ] Reach l'application angular

**12 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Faire démarrer les apps sur un serveur séparé de prod
        - [x] Connexion à l'instance en SSH avec : 
        ```
        ssh -i "predict-beta-clepaire.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
        ```
        Connexion success !
        - [ ] Reach l'application angular de beta
- [ ] Lier les apps au serveur keycloak
    - [x] Feuille de route : 
        - S'assurer de la connectivité et de la connectabailité des deux instances
        - Utiliser keycloak-js adapter
        - Modifier l'app.module.ts
        - Modifier l'authguard
        - Configurer le KeycloakService d'angular
        - Configurer les client settings dans la console d'administration du keycloak de prod
    - [x] S'assurer de la connectivité et de la connectabailité des deux instances
        - [x] Modifier les règles de sécurité entrantes / sortantes des instances
            - [x] Instance KC
                - [x] Modification des règles de sécurité
                    - [x] Autorisation de tout le traffic entrant de 172.31.5.119
                        - [x] Modification des règles entrantes/ ICMP Personnalisé-IPv4 /Tous / Personnalisé / 172.31.5.119/32
                    - [x] Autorisation de tout le traffic sortant de 172.31.5.119
                        - [x] Modification des règles sortantes/ ICMP Personnalisé-IPv4 /Tous / Personnalisé / 172.31.5.119/32
            - [x] Instance APPS
                - [x] Modification des règles de sécurité
                    - [x] Autorisation de tout le traffic entrant de 172.31.39.193
                        - [x] Modification des règles entrantes/ ICMP Personnalisé-IPv4 /Tous / Personnalisé / 172.31.39.193/32
                    - [x] Autorisation de tout le traffic sortant de 172.31.39.193
                        - [x] Modification des règles sortantes/ ICMP Personnalisé-IPv4 /Tous / Personnalisé / 172.31.39.193/32
        - [x] Vérification au ping :
        ```
        --- 172.31.39.193 ping statistics ---
        6 packets transmitted, 6 received, 0% packet loss, time 5091m
        &&
        --- 172.31.5.119 ping statistics ---
        4 packets transmitted, 4 received, 0% packet loss, time 3080ms
        ```
    - [ ] "Connecter" les applications au serveur keycloak
        - [ ] Recréer le realm keycloak sur la prod
            - [ ] Créer le fichier realm export sur le serveur prod keycloak
        - [ ] Refaire le docker-compose
            - [ ] Ne plus lancer de container keycloak
            - [ ] Pointer vers le serveur keycloak prod à la place
            - [ ] Ne plus lancer de script entrypoint.sh (déjà lancé dans le serveur keycloak)
        - [ ] Sécuriser la connexion à angular
            - [ ] Installer nginx
            - [ ] Configurer nginx
            - [ ] Ouvrir les ports sur amazon
            - [ ] Générer le certificat
            - [ ] Reach l'application angular


**13 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Faire démarrer les apps sur un serveur séparé de prod
        - [x] Connexion à l'instance en SSH avec : 
        ```
        ssh -i "predict-beta-clepaire.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
        ```
        Connexion success !
- [ ] Lier les apps au serveur keycloak
    - [ ] "Connecter" les applications au serveur keycloak
        - [ ] Refaire le docker-compose
            - [x] Arrêt des containers déjà en run avec ```sudo docker stop predict-apps predict-pgadmin kmo-keycloak predict-pg```
            - [x] Placement dans le dossier KMO-PREDICT
            - [x] Création du fichier docker-compose.beta.yml
            - [ ] Pointer vers le serveur keycloak prod à la place
            - [x] Ne plus lancer de script entrypoint.sh (déjà lancé dans le serveur keycloak)
            - [x] Création des docker networks app_network & keycloak_network sur l'instance du keycloak :
            ```
            sudo docker network create app_network
            sudo docker network create keycloak_network
            ```
            - [x] Refonte du .env : 
            ```
            # Database environment variables
            DB_HOST=postgres
            DB_PORT=6543
            DB_USER=postgres
            DB_PASSWORD=dbp@06072023-pwpg
            DB_NAME=predict
            POSTGRES_USER=predict
            POSTGRES_PASSWORD=dbp@06072023-pwpg
            POSTGRES_DB=predict

            # Node environment variables
            #NODE_ENV=development
            NODE_ENV=beta

            # Api ports
            NEST_PORT=3000
            ANGULAR_PORT=4200

            # Apps environment variables
            WEB_APP_URL=http://localhost:3000

            # AWS
            AWS_REGION=eu-central-1
            AWS_ACCESS_KEY_ID=AKIAY5QV4ILLYTYUOK7R
            AWS_SECRET_ACCESS_KEY=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
            ```
            - [x] Docker-compose.beta.yml as is :
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
                depends_on:
                - postgres
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

            keycloak:
                container_name: kmo-keycloak
                restart: always
                depends_on:
                - postgres
                image: ${KC_IMG}
                ports:
                - "8443:8443"
                networks:
                app:
                    ipv4_address: 172.31.39.193  # Instance 1 IP address

            postgres:
                container_name: predict-pg
                image: postgres:15.0-alpine3.16
                restart: on-failure
                environment:
                - POSTGRES_USER=${DB_USER?}
                - POSTGRES_PASSWORD=${DB_PASSWORD?}
                - POSTGRES_DB=${DB_NAME?}
                ports:
                - "${DB_PORT?}:5432"
                networks:
                app:
                    ipv4_address: 172.31.39.193  # Instance 1 IP address

            pgadmin:
                container_name: predict-pgadmin
                image: dpage/pgadmin4
                restart: always
                environment:
                PGADMIN_DEFAULT_EMAIL: admin@admin.com
                PGADMIN_DEFAULT_PASSWORD: root
                ports:
                - "5050:80"
                networks:
                app:

            networks:
            app:
                external:
                name: app_network
            keycloak:
                external:
                name: keycloak_network
            ```
        - [x] Lancement du docker-compose.beta.yml avec :
        ```
        sudo docker compose --env-file .env.beta -f docker-compose.beta.yml up --renew-anon-volumes --always-recreate-deps --build
        ```
        et retour : 
        ```
        WARN[0000] The "KC_IMG" variable is not set. Defaulting to a blank string.
        WARN[0000] network keycloak: network.external.name is deprecated. Please set network.name with external: true
        WARN[0000] network app: network.external.name is deprecated. Please set network.name with external: true
        service "keycloak" has neither an image nor a build context specified: invalid compose project
        ```
        - [x] Résolution des warnings, changement de :
        ```
        networks:
        app:
            external:
            name: app_network
        keycloak:
            external:
            name: keycloak_network    
        ```
        en : 
        ```
        networks:
        app:
            name: app_network
            external: true
        keycloak:
            name: keycloak_network
            external: true
        ```
        - [x] Suppression de "KC_IMG" du docker compose
        - [x] Ne plus lancer de container keycloak, update as is :
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
                depends_on:
                - postgres
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

            pgadmin:
                container_name: predict-pgadmin
                image: dpage/pgadmin4
                restart: always
                environment:
                PGADMIN_DEFAULT_EMAIL: admin@admin.com
                PGADMIN_DEFAULT_PASSWORD: root
                ports:
                - "5050:80"
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
        et relance de la commande ```sudo docker compose --env-file .env.beta -f docker-compose.beta.yml up --renew-anon-volumes --always-recreate-deps --build```
        retour négatif : 
        ```
        network app_network declared as external, but could not be found
        ```
        - [x] Création des networks aussi sur l'instance des apps : 
        ```
        sudo docker network create app_network
        sudo docker network create keycloak_network
        ```
        - [ ] Résolution des erreurs : 
            - [ ] Connexion à la db :
            ```
            ERROR [TypeOrmModule] Unable to connect to the database. Retrying (1)...
            Error: connect ETIMEDOUT 172.31.39.193:6543
            at TCPConnectWrap.afterConnect [as oncomplete] (node:net:1300:16)
            ```
        - [ ] Reach l'application angular de beta
            - [x] Ouvrir le port 3000 sur aws
        - [ ] Recréer le realm keycloak sur la prod
            - [x] Créer le fichier realm export sur le serveur prod keycloak
        - [ ] Configurer le client predict dans le realm
        - [ ] Configurer angular pour la redirection au keycloak de prod
        - [ ] Sécuriser la connexion à angular
            - [ ] Installer nginx
            - [ ] Configurer nginx
            - [ ] Ouvrir les ports sur amazon
            - [ ] Générer le certificat
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
        - [ ] Setup les apps pour la prod

