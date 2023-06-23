**19 Juin**
- [ ] Mise en prod
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [x] Doc (https://docs.docker.com/compose/networking/#use-a-pre-existing-network)
        - [x] Suppression de la partie keycloak
        - [x] Ajout à la fin du doc de : 
        ```
        networks:
            app:
            kc-network:
                external:true
        ```
        - [x] Lancement de la commande ```docker compose --env-file .env.dev -f docker-compose2apps.yml up --renew-anon-volumes --always-recreate-deps --build  ``` pour retour fail :
        ```
        networks.kc-network must be a mapping or null
        ```
        Résolution : indentation de networks:
        - [x] Lancement des apps, front fail to compile : 
        ```
        kmo-predict-front:dev: Error: ENOMEM: not enough memory, scandir '/app/apps/kmo-predict-front/src/assets'
        kmo-predict-front:dev:
        kmo-predict-front:dev:
        kmo-predict-front:dev:
        kmo-predict-front:dev: ** Angular Live Development Server is listening on 0.0.0.0:3000, open your browser on http://localhost:3000/ **
        kmo-predict-front:dev:
        kmo-predict-front:dev:
        kmo-predict-front:dev: ✖ Failed to compile.        
        ```
        - [ ] Résolution du fail to compile
        - [ ] Résolution 
        ```
        2023-06-19T12:42:07.474329675Z 2023-06-19 12:42:07,474 ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Failed to start server in (production) mode
        2023-06-19T12:42:07.474626311Z 2023-06-19 12:42:07,474 ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Failed to obtain JDBC connection
        2023-06-19T12:42:07.475876628Z 2023-06-19 12:42:07,475 ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Connection to localhost:5432 refused. Check that the hostname and port are correct and that the postmaster is accepting TCP/IP connections.
        2023-06-19T12:42:07.476230260Z 2023-06-19 12:42:07,476 ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Connection refused (Connection refused)
        2023-06-19T12:42:07.476489608Z 2023-06-19 12:42:07,476 ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) For more details run the same command passing the '--verbose' option. Also you can use '--help' to see the details about the usage of the particular command.
        ```
        il semble que le changement du --network fasse tout casser
        (https://www.mastertheboss.com/keycloak/keycloak-with-docker/) suggère de lancer une image postgres en plus à coté ?
        - [ ] Application au docker compose
    - [ ] Lancer la giga commande docker run du keycloak avec en flag le network externe 
    - [ ] Reproduire le schéma en prod

**21 Juin**
- [x] Réponse à pascal pour le poste de commercial
- [x] Review de la PR 86 sur la KP 355
    (ajout de la date de dernière maintenance dans la db)
- [x] Demande à gpt : 
```
Running sudo docker network ls in my server instance returns : 
NETWORK ID     NAME         DRIVER    SCOPE
ce2b753c1840   bridge       bridge    local
da2574ab62b6   host         host      local
3c1e2ab16dde   kc-network   bridge    local
967086f09053   none         null      local

what is docker network scope ?
where can i see if a docker network is external or not
```
sa réponse : 
```
The Docker network scope refers to the network's visibility and accessibility. 
There are three network scopes in Docker:

1. `local`: Networks with the local scope are only accessible to containers running on the same Docker host.
These networks are isolated and not visible to containers on other hosts.

2. `bridge`: Networks with the bridge scope are created by default when you start Docker. 
They allow containers to communicate with each other on the same Docker host. 
Bridge networks provide a private network between containers and enable communication via IP addresses.

3. `host`: Networks with the host scope allow containers to directly use the networking stack of the Docker host. 
This means that containers on the host network share the same network namespace, and there is no network isolation between the container and the host. 
Containers on the host network can directly access the host's network interfaces.

To determine whether a Docker network is external or not, you can check the `DRIVER` column in the output of the `docker network ls` command. 
If the driver is set to `bridge`, `overlay`, or any other driver provided by Docker, the network is an internal network created by Docker. 
If the driver is set to `null`, it means that the network is an external network created outside of Docker.

In your case, the `kc-network` network has the `bridge` driver, indicating that it is an internal network created by Docker.
```
- [x] Faire du ménage sur le jira ; clôturer les user stories qui trainent quitte à les renommer pour en recréer d'autres par la suite
    - [x] Déplacement de la KP 191 tests e2e dans la CI hors de l'epic tester l'application
    - [x] Rename de l'epic tester l'application avec (unitaire & coverage) en plus
    - [x] Clôture de l'epic tester l'application
    - [x] Création de la KP-361 
- [ ] Mise en prod
    - [ ] Démarrer un docker prod de la db sur network externe
        - [x] Création du docker compose : 
        ```
        version: '3.5'

            services:
            postgres:
                container_name: postgres_container
                image: postgres
                environment:
                POSTGRES_USER: ${POSTGRES_USER:-postgres}
                POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-changeme}
                PGDATA: /data/postgres
                volumes:
                - postgres:/data/postgres
                ports:
                - "5432:5432"
                networks:
                - pg-network

            networks:
            pg-network:
                driver: bridge

            volumes:
                postgres:
        ```
        - [x] Installation de compose sur l'aws ```sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose```
        - [x] Modification des permissions associées
        ```
        sudo chmod +x /usr/local/bin/docker-compose
        ```
        - [x] ```sudo docker-compose up docker-compose-pg.yml```
        retour fail : 
        ```
        no configuration file provided: not found
        ```
    - [ ] Démarrer un docker prod ddu keycloak sur network externe
    - [ ] Lier le keycloak à la db
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [ ] Application au docker compose
    - [ ] Lancer la giga commande docker run du keycloak avec en flag le network externe 
    - [ ] Reproduire le schéma en prod

    

**22 Juin**
- [x] Check des ports in use : ```sudo lsof -i -P -n | grep LISTEN```
et réponse : 
```
sshd        2030            root    5u  IPv4   16031      0t0  TCP *:22 (LISTEN)
sshd        2030            root    7u  IPv6   16039      0t0  TCP *:22 (LISTEN)
postmaste   4524        postgres    6u  IPv4   28722      0t0  TCP 127.0.0.1:5432 (LISTEN)
container   9044            root   13u  IPv4   76141      0t0  TCP 127.0.0.1:33761 (LISTEN)
java      256165        ec2-user  346u  IPv4 1327568      0t0  TCP 172.31.29.175:33291 (LISTEN)
java      256165        ec2-user  347u  IPv4 1327637      0t0  TCP 127.0.0.1:45407 (LISTEN)
java      256165        ec2-user  348u  IPv4 1327680      0t0  TCP *:8080 (LISTEN)
java      256165        ec2-user  349u  IPv4 1327681      0t0  TCP *:8443 (LISTEN)
docker-pr 351177            root    4u  IPv4 1789175      0t0  TCP *:6543 (LISTEN)
docker-pr 351182            root    4u  IPv6 1789187      0t0  TCP *:6543 (LISTEN)
```
- [ ] Check de let's encrypt (https://letsencrypt.org/fr/getting-started/)
- [ ] Récupération d'un certificat letsencrypt
- [ ] Ajout du certificat dans le dossier ./certificates
- [ ] Mise en prod
    - [x] Démarrer un docker prod de la db sur network externe
    - [ ] Démarrer un docker prod du keycloak sur network externe
    - [ ] Lier le keycloak à la db
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local
    - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [ ] Application au docker compose
    - [ ] Lancer la giga commande docker run du keycloak avec en flag le network externe 
    - [ ] Reproduire le schéma en prod

**23 Juin**
- [ ] Check de let's encrypt (https://letsencrypt.org/fr/getting-started/)
- [ ] Récupération d'un certificat letsencrypt
    - [ ] Installation des modules nécessaires
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