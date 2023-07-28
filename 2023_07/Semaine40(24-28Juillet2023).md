**25 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [x] Faire démarrer les apps sur un serveur séparé de prod
- [x] Lier les apps au serveur keycloak
    - [x] "Connecter" les applications au serveur keycloak
    - [x] Dockerisation (assurée par Anthony)
    - [ ] Connecter l'application à l'API
        - [x] Connexion aux instances en SSH avec : 
        ```
        ssh -i "predict-beta-clepaire.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
        ```
        Connexion success !
        &&
        ```
        ssh -i "keycloak-beta-clepaire.pem" ubuntu@ec2-3-75-5-27.eu-central-1.compute.amazonaws.com
        ```
        - [x] Check de la config sur le projet web (https://github.com/COP-AMACO/KMO_WEB/blob/develop/cop-ass-front/nginx.conf#L8)
        - [x] Modifier la config nginx pour taper sur l'api
            - [x] Ajout dans le nginx.conf de  : 
            ```
            location /api {
                proxy_pass "http://predict-beta.cop-amaco.digital/3001";
            }
            ```
            - [x] Vérification de la config avec ```sudo nginx -t```
            - [x] Restart & reload de nginx : 
            ```
            sudo systemctl restart nginx
            sudo systemctl reload nginx
            ```
        - [x] Modifier l'app angular en conséquence pour atteindre l'api qui tourne sur le serveur
            - [x] Ajout du s de https à : 
            ```
            export const environment = {
            production: true,
            apiUrl: 'http://predict-beta.cop-amaco.digital:3001',
            socketUrl: 'http://predict-beta.cop-amaco.digital:3001',
            keycloakUrl: 'https://keycloak-beta.cop-amaco.digital/'
            };
            ```
            - [x] Down des containers
            - [x] Suppression des /dist d'angular
            - [x] Rebuild d'angular
                - [x] ```ng build --configuration production```
            - [x] Relance des containers
    - [ ] Résolution de l'erreur : 
    ```
    net::ERR_SSL_PROTOCOL_ERROR
    ```

idée guillaume : afficher coté client utilisateur un id correspondant à l'erreur
Coté cop, on aurait le code & le nom de l'erreur

**27 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Résolution de l'erreur : 
        ```
        net::ERR_SSL_PROTOCOL_ERROR
        ```
        - [x] Check de (https://stackoverflow.com/questions/60963983/api-requests-returning-errors-trying-to-access-nestjs-backend-from-angular-front) mais pas de rapport apparent avec l'erreur
        - [x] Check de la version TLS dans la config nginx : version optimale déterminée par openssl si pas précisée
        - [x] Check de l'erreur : 
        ```
        main.58b0040de0c36d5f.js:1 
        ERROR yE {headers: Mr, status: 0, statusText: 'Unknown Error', 
        url: 'https://predict-beta.cop-amaco.digital:3001/stores/by-id/9de16383-c4d8-4616-bc4c-a26795f286ae', 
        ok: false, …}
        ```
        L'erreur vient du fait que le store auquel est lié l'user (via l'attribut store de kc) n'existe pas en base
        - [x] Repopulation de la bdd
            - [x] Ouverture de pgadmin
            - [x] Connexion avec les ids du .env.beta
            - [x] Lancement du script épinglé sur discord : 
            ```
            INSERT INTO public.gateway (mac, datetime, "diskUsage", "temperatureCpu", "freeRam", services)
            VALUES ('22:a9:fb:ff:d2:cd', '2023-03-01 16:48:43.000000', 101, 29, 30,
                    '{
                    "ATMOS": {
                        "active": true,
                        "memory_usage": "46.6M",
                        "mac_declared": "22:a9:fb:ff:d2:cd",
                        "extras": {
                        "operational_rate": 98.66666666666667
                        }
                    },
                    "RTunnel": {
                        "active": true,
                        "memory_usage": "11.8M"
                    }
                    }'),
                ('22:a9:fb:ff:d2:bc', '2024-03-01 16:48:43.000000', 2323, 29, 30, '{
                    "ATMOS": {
                    "active": true,
                    "memory_usage": "46.6M",
                    "mac_declared": "22:a9:fb:ff:d2:bc",
                    "extras": {
                        "operational_rate": 98.66666666666667
                    }
                    },
                    "RTunnel": {
                    "active": true,
                    "memory_usage": "11.8M"
                    }
                }'),
                ('22:a9:fb:ff:d2:ef', '2023-02-22 09:45:48.347000', 13, 39.444, 81.52787453215763, '{
                    "ATMOS": {
                    "active": true,
                    "memory_usage": "46.6M",
                    "mac_declared": "22:a9:fb:ff:d2:ef",
                    "extras": {
                        "operational_rate": 98.66666666666667
                    }
                    }
                }');

            INSERT INTO public.kmo_box (mac, datetime, uptime, "firmwareVersion","checkoutNumber", "gatewayMac")
            VALUES ('01:00:5E:xx:xx:xx', '2023-03-01 16:48:43.000000', 10, 1.4,1,
                    '22:a9:fb:ff:d2:bc'),
                ('01:00:5E:xx:xx:33', '2023-03-02 13:44:01.000000', 3, 3,2, '22:a9:fb:ff:d2:bc');

            INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('081439dd-cfda-49e7-85d8-15c6c25c65ca', '7 rue du chêne', 'Nordhouse', '67100', true, 3, '22:a9:fb:ff:d2:cd', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Lidl',true);
            INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('9de16383-c4d8-4616-bc4c-a26795f286ae', '8 rue du chêne', 'Nordhouse', '67100', true, 3, '22:a9:fb:ff:d2:bc', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Norma',true);
            INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('7d1a9808-ccf1-4136-8c8b-b80020ac0ec4', '9 rue du chêne', 'Nordhouse', '67100', true, 3, '22:a9:fb:ff:d2:ef', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Lidl',true);

            INSERT INTO public.event (id, type, duration, datetime, state, "stateValue", motor, "kmoBoxMac")
            VALUES ('993051ef-1a3e-4781-b60f-1ea7faf7da89', 'pedal', 22.6, '2023-02-22 09:45:48.347000', 'up', null, null,
                    '01:00:5E:xx:xx:xx'),
                ('75cb0af5-849d-4ce6-b794-a0585136bd8c', 'motor_flag', null, '2023-02-22 09:45:48.347000', 'value', 0.225, '2',
                    '01:00:5E:xx:xx:xx'),
                ('6ee4988c-8e7b-4c10-820c-07b48fd8c574', 'cpu_temperature_changed', null, '2023-02-22 09:45:48.347000',
                    'thermal_switch', null, '2', '01:00:5E:xx:xx:xx'),
                ('cbee4323-3f71-4fda-ba96-cc9c122aa5a2', 'power_intensity_changed', null, '2023-02-22 09:45:48.347000',
                    'thermal_switch', null, '2', '01:00:5E:xx:xx:xx'),
                ('993051ef-1a3e-4781-b60f-1ea7faf7da83', 'ir', 22.6, '2023-03-02 13:43:30.000000', 'down', null, '1',
                    '01:00:5E:xx:xx:xx')
            ```
        - [x] Essai de reach le backend depuis le serveur avec : 
        ```
        curl -v https://predict-beta.cop-amaco.digital:3001/stores/by-id/9de16383-c4d8-4616-bc4c-a26795f286ae
        ```
        et retour négatif : 
        ```
        *   Trying 52.58.79.10:3001...
        * Connected to predict-beta.cop-amaco.digital (52.58.79.10) port 3001 (#0)
        * ALPN, offering h2
        * ALPN, offering http/1.1
        *  CAfile: /etc/ssl/certs/ca-certificates.crt
        *  CApath: /etc/ssl/certs
        * TLSv1.0 (OUT), TLS header, Certificate Status (22):
        * TLSv1.3 (OUT), TLS handshake, Client hello (1):
        * (5454) (IN), , Unknown (72):
        * error:0A00010B:SSL routines::wrong version number
        * Closing connection 0
        curl: (35) error:0A00010B:SSL routines::wrong version number
        ```
        - [x] Vérification des versions TLS avec ```openssl s_client -connect predict-beta.cop-amaco.digital:443``` et retour : 
        ```
        ...
        Post-Handshake New Session Ticket arrived:
        SSL-Session:
            Protocol  : TLSv1.3
        ...
        ```
        - [x] Check de la config nest
            - [x] Check du fichier app.module.ts
            - [x] Check du fichier main.ts
            - [x] Check du fichier .env.beta : 
            ```
            # Apps environment variables
            WEB_APP_URL=http://predict-beta.cop-amaco.digital:3000
            ```
            on est en http pas en https
        - [x] Modification du fichier env avec le s
        - [x] Down du container apps
        - [x] Prune du container avec : 
        ```
        sudo docker container prune
        WARNING! This will remove all stopped containers.
        Are you sure you want to continue? [y/N] Y
        Deleted Containers:
        4ac7744393179602d41e064c89c2b10295ea05aa8ba2673f0ac885b6cd2307e5
        ```
        - [x] Rebuild du docker compose, pas de changement, toujours la même erreur
    - [ ] Build de l'app nest en https
        - [x] Down du container apps & prune
        - [x] Copie des certificat & clé dans le container nest via Dockerfile
        - [x] Modification du main.ts : 
        ```
        import { NestFactory } from '@nestjs/core';
        import { AppModule } from './app.module';
        import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';
        import { ValidationPipe } from '@nestjs/common';
        import * as fs from 'fs';
        import * as https from 'https';

        async function bootstrap() {
        const app = await NestFactory.create(AppModule, {
            httpsOptions: {
            key: fs.readFileSync('/app/privkey.pem'),
            cert: fs.readFileSync('/app/fullchain.pem'),
            },
        });

        app.enableCors({
            origin: process.env.WEB_APP_URL,
            methods: 'GET,POST, DELETE, PUT, PATCH, OPTIONS',
            allowedHeaders:
            'Accept, Accept-Language, authorization, Content-Language, Content-Type',
        });

        app.useGlobalPipes(
            new ValidationPipe({ whitelist: true, forbidNonWhitelisted: true }),
        );

        const config = new DocumentBuilder()
            .setTitle('Predict')
            .setDescription('Maintenance prédictive')
            .setVersion('0.0')
            .build();

        const document = SwaggerModule.createDocument(app, config);
        SwaggerModule.setup('api', app, document);

        const port = 3001;
        // Use https.createServer instead of app.listen to enable HTTPS.
        await https.createServer(app.getHttpServer(), app.get('httpsOptions')).listen(port);

        console.log(`Application is running on: http://localhost:${port}`);
        await app.startAllMicroservices();
        }

        bootstrap();
        ```
        - [x] Reprise du nginx.conf
        - [ ] Rebuild du container
- [ ] Recommençage de zéro pour cause d'instance pétée
    - [x] Création de l'instance, association à l'ip ellastique, création de la clé paire & suppression du fichier known hosts
        - [x] Connexion aux instances en SSH avec : 
        ```
        ssh -i "predict-beta-clepaire-27Juillet.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
        ```
        Connexion success !
        - [x] Clone du repo github 
            - [x] ```sudo git clone git@github.com:COP-AMACO/KMO_PREDICT.git``` et retour négatif : 
            ```
            Cloning into 'KMO_PREDICT'...
            git@github.com: Permission denied (publickey).
            fatal: Could not read from remote repository.

            Please make sure you have the correct access rights
            and the repository exists.
            ```
            - [x] Génération de clé ssh
                - [x] ```ssh-keygen -t ed25519 -C "g.chevalier@cop-amaco.com"```
                - [x] ```eval "$(ssh-agent -s)"```
                - [x] ```ssh-add ~/.ssh/id_ed25519```
                - [x] Ajout de la clé dans le github (https://github.com/settings/keys)
            - [x] ```git clone git@github.com:COP-AMACO/KMO_PREDICT.git``` note, ne pas mettre sudo devant.
        - [x] Installation de docker
        Check de (https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04) et application :
        ```
        sudo apt update
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
        sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
        sudo apt install docker-ce
        ```
        - [x] Installation de docker compose
        Check de (https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04) et application :
        ```
        sudo curl -L "https://github.com/docker/compose/releases/download/2.20.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        sudo chmod +x /usr/local/bin/docker-compose
        ```
        - [x] Installation de la cli d'angular
            - [x] Installation de node ```sudo apt install npm```
            - [x] ```npm install @angular/cli```
            - [x] Ajout du lien ```export PATH="$PATH:/usr/local/bin"```
            - [x] Update de node : 
            ```
            curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
            nvm install v14.20.0
            ```
        - [x] Installation d'Nginx
        ```
        sudo apt install nginx
        Y
        ```
        - [x] Installation de certbot
        ```
        sudo apt-get remove certbot
        sudo snap install --classic certbot
        sudo ln -s /snap/bin/certbot /usr/bin/certbot
        ```
        - [x] Installation de nginx plugin for certbot ```sudo apt install certbot python3-certbot-nginx```
        - [x] Lancement de ```sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot``` pour assurer la faisabilité du certificat sur la machine, retour terminal normal
        - [x] génération des certificats ssl
            - [x] Ouverture des ports du serveur
                - SSH/TCP/22/N'importe ipv4/0.0.0.0/0
                - HTTP/TCP/80/N'importe ipv4/0.0.0.0/0
                - TCPPersonnalisé/TCP/8080/N'importe ipv4/0.0.0.0/0
                - TCPPersonnalisé/TCP/8443/N'importe ipv4/0.0.0.0/0
                - HTTPS/TCP/443/N'importe ipv4/0.0.0.0/0
                - ICMP-IPv4/ICMP/tous/172.31.39.193/32
            - [x] Redémarrage de l'instance
            - [x] Génération
                ```
                sudo certbot --nginx
                g.chevalier@cop-amaco.com
                Y
                Y
                keycloak-beta.cop-amaco.digital
                ```
        - [x] Modification du fichier de config nginx :
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

                ##
                # SSL Settings
                ##

                ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
                ssl_prefer_server_ciphers on;


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

                location /api {
                    proxy_pass "https://predict-beta.cop-amaco.digital/3001";
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
        - [x] Modification du fichier .env.beta :
        ```
        # Database environment variables
        DB_HOST=postgres
        DB_PORT=5432
        DB_USER=predict
        DB_PASSWORD=predict16022023
        DB_NAME=predict
        POSTGRES_USER=predict
        POSTGRES_PASSWORD=predict16022023
        POSTGRES_DB=predict

        # Node environment variables
        #NODE_ENV=development
        NODE_ENV=beta

        # Apps environment variables
        WEB_APP_URL=https://predict-beta.cop-amaco.digital:3000

        # AWS
        AWS_REGION=eu-central-1
        AWS_ACCESS_KEY_ID=AKIAY5QV4ILLYTYUOK7R
        AWS_SECRET_ACCESS_KEY=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
        ```
        - [x] Modification du Dockerfile.dev :
        ```
        FROM node:18.12.1-alpine3.16

        # Required for turbo post-install
        RUN apk add --no-cache libc6-compat
        RUN apk add --no-cache ffmpeg
        RUN apk update

        WORKDIR /app

        COPY package.json ./
        COPY package-lock.json ./
        COPY turbo.json .

        COPY apps/kmo-predict-front/package.json apps/kmo-predict-front/package.json
        COPY apps/kmo-predict-back/package.json apps/kmo-predict-back/package.json

        COPY packages/eslint-config-custom/package.json packages/eslint-config-custom/package.json
        COPY packages/tsconfig/package.json packages/tsconfig/package.json

        COPY /etc/letsencrypt/live/predict-beta.cop-amaco.digital/fullchain.pem /app/fullchain.pem
        COPY /etc/letsencrypt/live/predict-beta.cop-amaco.digital/privkey.pem /app/privkey.pem

        RUN npm cache clean --force
        RUN npm install turbo --legacy-peer-deps --dev
        RUN npm install --legacy-peer-deps

        COPY . .

        EXPOSE 3000 3001

        CMD [ "npm", "run", "dev" ]
        ```
        - [x] Modification du fichier docker-compose.beta.yml :
        ```
        version: "3.9"

        services:

        apps:
            container_name: predict-apps-beta
            build:
            context: .
            dockerfile: Dockerfile.dev
            ports:
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
        - [x] Modification du fichier environment.prod.ts :
        ```
        export const environment = {
            production: true,
            apiUrl: 'https://predict-beta.cop-amaco.digital:3001',
            socketUrl: 'https://predict-beta.cop-amaco.digital:3001',
            keycloakUrl: 'https://keycloak-beta.cop-amaco.digital/'
        };
        ```
        - [x] Modification du fichier main.ts (back) :
        ```
        import { NestFactory } from '@nestjs/core';
        import { AppModule } from './app.module';
        import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';
        import { ValidationPipe } from '@nestjs/common';
        import * as fs from 'fs';
        import * as https from 'https';

        async function bootstrap() {
        const app = await NestFactory.create(AppModule, {
            httpsOptions: {
            key: fs.readFileSync('/app/privkey.pem'),
            cert: fs.readFileSync('/app/fullchain.pem'),
            },
        });

        app.enableCors({
            origin: process.env.WEB_APP_URL,
            methods: 'GET,POST, DELETE, PUT, PATCH, OPTIONS',
            allowedHeaders:
            'Accept, Accept-Language, authorization, Content-Language, Content-Type',
        });

        app.useGlobalPipes(
            new ValidationPipe({ whitelist: true, forbidNonWhitelisted: true }),
        );

        const config = new DocumentBuilder()
            .setTitle('Predict')
            .setDescription('Maintenance prédictive')
            .setVersion('0.0')
            .build();

        const document = SwaggerModule.createDocument(app, config);
        SwaggerModule.setup('api', app, document);

        const port = 3001;
        // Use https.createServer instead of app.listen to enable HTTPS.
        await https.createServer(app.getHttpServer(), app.get('httpsOptions')).listen(port);

        console.log(`Application is running on port : ${port}`);
        await app.startAllMicroservices();
        }

        bootstrap();
        ```
        - [x] Création des docker networks : 
        ```
        sudo docker network create apps-network
        sudo docker network create  keycloak-network
        ```
        - [ ] Génération du dossier dist d'angular : 
            - [x] Placement dans le dossier /apps/kmo-predict-front
            - [x] ```sudo npm install --legacy-peer-deps```
        ```
        ng build --configuration production
        ```


**28 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Résolution de l'erreur : 
        ```
        net::ERR_SSL_PROTOCOL_ERROR
        ```
    - [ ] Recommençage de zéro pour cause d'instance pétée
        - [ ] Génération du dossier dist d'angular : 
            - [x] Placement dans le dossier /apps/kmo-predict-front
            - [x] ```sudo npm install --legacy-peer-deps```
            - [x] Génération des files :
            ```
            ng build --configuration production
            ```