**3 Juillet**
- [x] Mise à jour du serveur aws linux : 
```
sudo dnf upgrade --releasever=2023.1.20230628
```
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
- [ ] Redémarrer un keycloak avec dans les --flags le lien vers le nouveau certificat
- [ ] Mise en prod
    - [ ] Démarrer un docker prod du keycloak sur network externe
    - [ ] Lier le keycloak à la db
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [ ] Application au docker compose
    - [ ] Lancer la giga commande docker run du keycloak avec en flag le network externe & le certificat
    - [ ] Reproduire le schéma en prod

- [ ] Entrevue olivier ITS
    - [x] Résolution problème de db atmos prod
    - [ ] Points à aborder : 
        - [ ] Keycloak prod
        - [ ] Apps sur docker network externe lié au keycloak
        - [ ] Certificat Letsencrypt sur Keycloay prod 

        ```
        sudo docker run -d  \
                --name keycloak-prod8 \
                --network=host \
                -e KEYCLOAK_ADMIN=admin \
                -e KEYCLOAK_ADMIN_PASSWORD=$KEYCLOAK_ADMIN_PASSWORD \
                quay.io/keycloak/keycloak:18.0.0 \
                start \
                --auto-build \
                --hostname=$KEYCLOAK_HOSTNAME \
                --proxy=edge \
                --db=postgres \
                --db-url=jdbc:postgres://localhost:5432/postgres \
                --db-username=postgres \
                --db-password=$DB_PASSWORD
        ```

redirection vers localhost 8080

changement du fichier de conf nginx : ajouter
```
set_proxy_header 4 lines voir atmos
```


- démarrer aws container db postgres
- déclarer dans aws export KEYCLOAK_HOSTNAME=testgreg.cop-amaco.digital
- déclarer dans aws export DB_PASSWORD=12062023
- déclarer dans aws export KEYCLOAK_ADMIN_PASSWORD=admin
- démarrer aws container keycloak prod avec la grosse commande
- ouvrir le port 8443 sur amazon pour le serveur
- installer nginx
- installer certbot
- générer certificat
- configurer nginx avec le nom de domaine dans un bloc serveur
- configurer nginx.conf avec location / {
    proxy_pass http://nomducontainerkeycloak:port
}
- configurer nginx.conf avec les 4 lignes de proxy set header


**4 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [x] Créer l'instance ubuntu
        - [x] Création de l'instance ubuntu 20.04/clé paire ed25519/x86/30G stockage/t2micro nommée keycloak-prod-beta
    - [x] Ouvrir les ports
        - [x] Créer un groupe de sécurité
            - nom : keycloak-prod-securitygroup
            - SSH/TCP/22/N'importe ipv4/0.0.0.0/0
            - HTTP/TCP/80/N'importe ipv4/0.0.0.0/0
            - TCPPersonnalisé/TCP/8080/N'importe ipv4/0.0.0.0/0
            - TCPPersonnalisé/TCP/8443/N'importe ipv4/0.0.0.0/0
            - HTTPS/TCP/443/N'importe ipv4/0.0.0.0/0
    - [x] Se connecter à l'instance
    - [ ] Mettre ubuntu à jour
    - [ ] installer les dépendances requises
    - [ ] Installer docker
    - [ ] Démarrer un container postgres avec docker
    - [ ] Installer keycloak
    - [ ] Fair ele docker compose du keycloak de prod
    - [ ] Lier le keycloak à la db
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [ ] Application au docker compose
    - [ ] Générer le certificat 
    - [ ] Renouvellement automatique du certificat via cron
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local
        

**5 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Mettre ubuntu à jour
        - [x] ```sudo apt list --upgradable```
        - [x] ```sudo apt-get upgrade```
    - [x] Installer docker
        - [x] ```sudo apt install docker.io``` puis y
    - [ ] installer les dépendances requises
    - [ ] Démarrer un container postgres avec docker
        - [x] Check si postgres est ibstallée et active :
        ```sudo systemctl status postgresql``` et retour : 
        ```
        Unit postgresql.service could not be found.
        ```
        - [ ] Installation de pg
            ```
            sudo apt-get install postgresql postgresql-server -y
            ```
            mais retour négatif : 
            ```
            Reading package lists... Done
            Building dependency tree
            Reading state information... Done
            E: Unable to locate package postgresql-server
            ```
            - [x] Check de (https://www.cherryservers.com/blog/how-to-install-and-setup-postgresql-server-on-ubuntu-20-04)
            et :
            ```
            apt install postgresql postgresql-contrib
            ```
            puis run de :
            ```
            service postgresql status
            ```
            et retour positif : 
            ```
            service postgresql status
            ● postgresql.service - PostgreSQL RDBMS
                Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; >
                Active: active (exited) since Wed 2023-07-05 07:31:55 UTC; 6min a>
            Main PID: 48652 (code=exited, status=0/SUCCESS)
                Tasks: 0 (limit: 1141)
                Memory: 0B
                CGroup: /system.slice/postgresql.service

            Jul 05 07:31:54 ip-172-31-32-174 systemd[1]: Starting PostgreSQL RDBMS>
            Jul 05 07:31:55 ip-172-31-32-174 systemd[1]: Finished PostgreSQL RDBMS.
            ```
    - [ ] Installer keycloak
    - [ ] Fair ele docker compose du keycloak de prod
    - [ ] Lier le keycloak à la db
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [ ] Application au docker compose
    - [ ] Générer le certificat 
    - [ ] Renouvellement automatique du certificat via cron
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local


**6 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Mettre ubuntu à jour : 
    ```
    0 updates can be applied immediately.

    Enable ESM Apps to receive additional future security updates.
    See https://ubuntu.com/esm or run: sudo pro status


    *** System restart required ***
    ```
    Redémarrage de l'instance via aws
    - [ ] installer les dépendances requises
    - [ ] Démarrer un container postgres avec docker
        - [x] Check si postgres est ibstallée et active :
        ```sudo systemctl status postgresql``` et retour : 
        ```
        Unit postgresql.service could not be found.
        ```
        - [x] Installation de pg
            ```
            sudo apt-get install postgresql postgresql-server -y
            ```
            mais retour négatif : 
            ```
            Reading package lists... Done
            Building dependency tree
            Reading state information... Done
            E: Unable to locate package postgresql-server
            ```
            - [x] Check de (https://www.cherryservers.com/blog/how-to-install-and-setup-postgresql-server-on-ubuntu-20-04)
            et :
            ```
            apt install postgresql postgresql-contrib
            ```
            puis run de :
            ```
            service postgresql status
            ```
            et retour positif : 
            ```
            service postgresql status
            ● postgresql.service - PostgreSQL RDBMS
                Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; >
                Active: active (exited) since Wed 2023-07-05 07:31:55 UTC; 6min a>
            Main PID: 48652 (code=exited, status=0/SUCCESS)
                Tasks: 0 (limit: 1141)
                Memory: 0B
                CGroup: /system.slice/postgresql.service

            Jul 05 07:31:54 ip-172-31-32-174 systemd[1]: Starting PostgreSQL RDBMS>
            Jul 05 07:31:55 ip-172-31-32-174 systemd[1]: Finished PostgreSQL RDBMS.
            ```
    - [x] Vérification que docker est installé & fonctionnel : 
    ```
    sudo docker ps -a
    =====
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    ```
    - [x] Check si le postgresql est actif : 
    ```
    sudo systemctl status postgresql
    ```
    et retour terminal : 
    ```
    ● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
     Active: active (exited) since Thu 2023-07-06 06:40:57 UTC; 25min ago
    Process: 805 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 805 (code=exited, status=0/SUCCESS)

    Jul 06 06:40:57 ip-172-31-32-174 systemd[1]: Starting PostgreSQL RDBMS...
    Jul 06 06:40:57 ip-172-31-32-174 systemd[1]: Finished PostgreSQL RDBMS.
    ```
    - [ ] Faire le docker compose du keycloak de prod
        - [x] Chat gpt : 
        ```
        i have this command to run a production version of keycloak : 

        sudo docker run -d  \
                        --name keycloak-prod8 \
                        --network=host \
                        -e KEYCLOAK_ADMIN=admin \
                        -e KEYCLOAK_ADMIN_PASSWORD=$KEYCLOAK_ADMIN_PASSWORD \
                        quay.io/keycloak/keycloak:18.0.0 \
                        start \
                        --auto-build \
                        --hostname=$KEYCLOAK_HOSTNAME \
                        --proxy=edge \
                        --db=postgres \
                        --db-url=jdbc:postgres://localhost:5432/postgres \
                        --db-username=postgres \
                        --db-password=$DB_PASSWORD

        i want you to make a dockerfile based on it please
        ```
        réponse de gpt : 
        ```
        FROM quay.io/keycloak/keycloak:18.0.0

        ENV KEYCLOAK_ADMIN=admin
        ENV KEYCLOAK_ADMIN_PASSWORD=<your_admin_password>
        ENV KEYCLOAK_HOSTNAME=<your_keycloak_hostname>
        ENV DB_PASSWORD=<your_db_password>

        CMD ["start", "--auto-build", "--hostname=${KEYCLOAK_HOSTNAME}", "--proxy=edge", "--db=postgres", "--db-url=jdbc:postgres://localhost:5432/postgres", "--db-username=postgres", "--db-password=${DB_PASSWORD}"]

        To build the Docker image using this Dockerfile, navigate to the directory containing the Dockerfile and run the following command:

        docker build -t keycloak-prod:1.0 .
        docker run -d --name keycloak-prod8 --network=host -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=<your_admin_password> -e KEYCLOAK_HOSTNAME=<your_keycloak_hostname> -e DB_PASSWORD=<your_db_password> keycloak-prod:1.0
        Adjust the container name, network settings, and environment variables according to your requirements.

        Note: The --network=host flag in the docker run command allows the container to use the host's network stack, eliminating the need to publish individual ports. Ensure that it aligns with your specific networking needs and security considerations.

        Remember to properly secure your environment and sensitive data, such as passwords and connection strings, when deploying Keycloak in a production environment.
        ```
        - [x] ```sudo touch Dockerfile.keycloak``` & ```sudo nano Dockerfile.keycloak```
        - [x] Paste dans le dockerfile :
        ```
        FROM quay.io/keycloak/keycloak:18.0.0

        ENV KEYCLOAK_ADMIN=admin
        ENV KEYCLOAK_ADMIN_PASSWORD=@dminKC-prod!1
        ENV KEYCLOAK_HOSTNAME=$KEYCLOAK_HOSTNAME
        ENV DB_PASSWORD=dbp@06072023-pwpg

        CMD ["start", "--auto-build", "--hostname=${KEYCLOAK_HOSTNAME}", "--proxy=edge", "--db=postgres", "--db-url=jdbc:postgres://localhost:5432/postgres", "--db-username=postgres", "--db-password=${DB_PASSWORD}"]
        ```
        - [x] Redo chat gpt pour docker compose et retour : 
        ```
        version: '3.8'
        services:
        keycloak:
            build:
            context: .
            dockerfile: Dockerfile
            container_name: keycloak-prod8
            network_mode: host
            environment:
            - KEYCLOAK_ADMIN=admin
            - KEYCLOAK_ADMIN_PASSWORD=<your_admin_password>
            - KEYCLOAK_HOSTNAME=<your_keycloak_hostname>
            - DB_PASSWORD=<your_db_password>
        ```
        - [ ] Installer compose 
            - [x] Check de (https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04)
            - [x] ```sudo curl -L "https://github.com/docker/compose/releases/download/2.19.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose``` puis ```sudo apt install docker-compose```
    - [ ] Installer keycloak
    - [ ] Lier le keycloak à la db
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [ ] Application au docker compose
    - [ ] Générer le certificat 
    - [ ] Renouvellement automatique du certificat via cron
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local
- [x] Passage avec Anthony sur ses tests des nouvelles fonctions lambda/kmoboxes
- [x] Review de la PR concernant les ajouts d'Anthonye


**7 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] installer les dépendances requises
    - [ ] Démarrer un container postgres avec docker
        - [x] Check si postgres est ibstallée et active :
        ```sudo systemctl status postgresql``` et retour positif
        - [x] Installation de pg
    - [x] Vérification que docker est installé & fonctionnel : 
    ```
    sudo docker ps -a
    =====
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    ```
    - [x] Installer compose 
        - [x] Check de (https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04)
        - [x] ```sudo curl -L "https://github.com/docker/compose/releases/download/2.19.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose``` puis ```sudo apt install docker-compose```
        mais retour : 
        ```
        $ docker-compose version
        /usr/local/bin/docker-compose: line 1: Not: command not found
        ```
        - [x] Résolution du problème compose not a docker command
            - [x] Check de (https://github.com/docker/compose/issues/8630)
            - [x] Application de :
            ```
            sudo apt-get install docker-compose-plugin
            ```
            mais retour : 
            ```
            E: Unable to locate package docker-compose-plugin
            ```
            - [x] Check de (https://docs.docker.com/compose/install/linux/#install-the-plugin-manually) et 
            ```
            DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
            mkdir -p $DOCKER_CONFIG/cli-plugins
            curl -SL https://github.com/docker/compose/releases/download/v2.19.1/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose                                   
            ```
            puis
            ```
            chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
            ```
            enfin
            ```
            docker compose version
            Docker Compose version v2.19.1
            ```
    - [x] Création du fichier .env avec ```sudo touch .env``` et ```sudo nano .env```
    ```
    KEYCLOAK_ADMIN=admin
    KEYCLOAK_ADMIN_PASSWORD=minKC-prod!1
    KEYCLOAK_HOSTNAME=host-kc1
    DB_PASSWORD=dbp@06072023-pwpg
    ```
    - [x] Création du fichier Dockerfile avec ```sudo touch Dockerfile.keycloak``` et ```sudo nano Dockerfile.keycloak```
    ```
    FROM quay.io/keycloak/keycloak:18.0.0

    ENV KEYCLOAK_ADMIN=admin
    ENV KEYCLOAK_ADMIN_PASSWORD=${KEYCLOAK_ADMIN_PASSWORD}
    ENV KEYCLOAK_HOSTNAME=${KEYCLOAK_HOSTNAME}
    ENV DB_PASSWORD=${DB_PASSWORD}

    CMD ["start", "--auto-build", "--hostname=${KEYCLOAK_HOSTNAME}", "--proxy=edge", "--db=postgres", "--db-url=jdbc:postgres://postgres:5432/postgres", "--db-username=postgres", "--db-password=${DB_PASSWORD}"]
    ```
    - [ ] Faire le docker compose du keycloak de prod
        - [x] Création du fichier .env avec ```sudo touch docker-compose.yml``` et ```sudo nano docker-compose.yml```
        ```
        version: '3.8'
            services:
            postgres:
                image: postgres
                container_name: keycloak-postgres
                restart: unless-stopped
                environment:
                - POSTGRES_USER=postgres
                - POSTGRES_PASSWORD=${DB_PASSWORD}
                volumes:
                - postgres-data:/var/lib/postgresql/data
                ports:
                - 6543:5432

            keycloak:
                build:
                context: .
                dockerfile: Dockerfile
                container_name: keycloak-prod8
                restart: unless-stopped
                depends_on:
                - postgres
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
                volumes:
                - keycloak-data:/opt/jboss/keycloak/standalone/data

            volumes:
            postgres-data:
            keycloak-data:
        ```
        - [x] Lancement de la commande docker compose up -d, retour : 
        ```
        /usr/local/bin/docker-compose: 1: Not: not found
        ````
        ```
        sudo docker compose --env-file .env -f docker-compose.yml up --renew-anon-volumes --always-recreate-deps --build
        ```
    - [ ] Installer keycloak
    - [ ] Lier le keycloak à la db
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [ ] Application au docker compose
    - [ ] Générer le certificat 
    - [ ] Renouvellement automatique du certificat via cron
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [ ] Lier les apps en local à un docker network externe local
- [x] Réunion followup
