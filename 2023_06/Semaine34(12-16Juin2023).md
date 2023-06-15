**13 Juin**
- [ ] Mise en prod
    - [x] Connexion sur AWS & run de l'instance test i-0ff387a4c39282058
    - [x] Login à l'instance avec :
    ```
    ssh -i "Keycloak.pem" ec2-user@ec2-3-70-202-53.eu-central-1.compute.amazonaws.com
    ```
    - [x] Check de l'espace disque restant dans l'instance : ```df -h``` , retour console : 
    ```
    [ec2-user@ip-172-31-29-175 ~]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        4.0M     0  4.0M   0% /dev
    tmpfs           475M     0  475M   0% /dev/shm
    tmpfs           190M  2.8M  188M   2% /run
    /dev/xvda1      8.0G  4.6G  3.4G  58% /
    tmpfs           475M     0  475M   0% /tmp
    tmpfs            95M     0   95M   0% /run/user/1000
    ```
    - [x] Finir l'install des projets sur le serveur avec npm i 
        - [x] Front 
        - [x] Back
        - [x] Racine
    - [ ] Configurer Keycloak pour la prod
        - [x] Vérifier les prérequis
            - [x] postgresql
                - [x] ```sudo systemctl status postgresql``` retourne : 
                ```
                Unit postgresql.service could not be found.
                ```
                - [ ] Installation de pg
                ```
                sudo yum install postgresql postgresql-server -y
                ```
                mais retour négatif : 
                ```
                No match for argument: postgresql
                No match for argument: postgresql-server
                ```
                poursuite des recherches
                    - [x] Check de (https://devopscube.com/install-configure-postgresql-amazon-linux/), puis (https://stackoverflow.com/questions/76082320/alternative-of-amazon-linux-extras-in-amazon-linux-2023) et application : 
                    ```
                    sudo yum update -y
                    sudo dnf install postgresql15
                    y
                    sudo yum install -y postgresql-server
                    ```
                    retour
                    ```
                    not found
                    ```
                    puis : 
                    ```
                    postgresql15-server
                    y
                    Downloading Packages:
                    (1/2): libicu-67.1-7.amzn2023.0.3.x86_64.
                    37 MB/s | 9.6 MB     00:00
                    (2/2): postgresql15-server-15.0-1.amzn2023.0.2.x86_64.rpm                                   18 MB/s | 6.0 MB     00:00
                    -------------------------------------------------------------------------
                    Total                                                                                       39 MB/s |  16 MB     00:00
                    Running transaction check
                    Transaction check succeeded.
                    Running transaction test
                    Transaction test succeeded.
                    Running transaction
                    Preparing        :                                                                                                   1/1
                    Installing       : libicu-67.1-7.amzn2023.0.3.x86_64                                ...
                    Complete!
                    ```
                    - [x] Suite avec :
                    ```sudo service postgresql-15.service start``` et retour ```Failed to start postgresql-15.service: Unit postgresql-15.service not found.```
                    - [x] ```sudo postgresql-setup initdb``` 
                    retour console : 
                    ```
                    WARNING: using obsoleted argument syntax, try --help
                    WARNING: arguments transformed to: postgresql-setup --initdb --unit postgresql
                    * Initializing database in '/var/lib/pgsql/data'
                    * Initialized, logs are in /var/lib/pgsql/initdb_postgresql.log
                    ```
                    - [x] ```sudo systemctl start postgresql```
                    - [x] ```sudo systemctl enable postgresql``` et retour ```Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /usr/lib/systemd/system/postgresql.service```
        - [x] Reprise de (https://www.adaltas.com/en/2023/03/14/ec2-deploy-keycloak/) au point 3.2
            - [x] ```sudo systemctl status postgresql```:
            ```
            ● postgresql.service - PostgreSQL database server
                Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; preset: disabled)
                Active: active (running) since Mon 2023-06-12 07:50:43 UTC; 8min ago
            Main PID: 6437 (postmaster)
                Tasks: 7 (limit: 1114)
                Memory: 19.2M
                    CPU: 160ms
                CGroup: /system.slice/postgresql.service
                        ├─6437 /usr/bin/postmaster -D /var/lib/pgsql/data
                        ├─6438 "postgres: logger "
                        ├─6439 "postgres: checkpointer "
                        ├─6440 "postgres: background writer "
                        ├─6442 "postgres: walwriter "
                        ├─6443 "postgres: autovacuum launcher "
                        └─6444 "postgres: logical replication launcher "
            ```
            - [x] Set du pw postgresql : 
            ```
            sudo su - postgres
            psql -c "ALTER USER postgres WITH PASSWORD '12062023';"
            exit
            ```
            retour console : 
            ```
            ALTER ROLE
            ```
            - [x] Postgresql & server OK
            - [ ] Suite du tuto
    - [ ] Lancer le tout combiné

**14 Juin**
- [ ] Mise en prod
    - [x] Connexion sur AWS & run de l'instance test 
    - [x] Check de l'espace disque restant dans l'instance : ```df -h``` , retour console : 
    - [ ] Configurer Keycloak pour la prod
        - [x] Reprise de (https://www.adaltas.com/en/2023/03/14/ec2-deploy-keycloak/) au point 3.2
        - [ ] Suite du tuto
            - [x] Modification du fichier pg_hba.conf
                - [x] À la racine de l'instance, ``` sudo nano /var/lib/pgsql/data/pg_hba.conf```
                - [x] Changement de :
                ```
                host    all             all             127.0.0.1/32            ident
                ```
                en 
                ```
                # host    all             all             127.0.0.1/32            ident
                host    all             postgres             127.0.0.1/32            md5
                ```
                - [x] Redémarrage de postgres : 
                ```
                sudo systemctl restart postgresql
                ```
            - [x] Création du dossier à certificats : 
            ```
            mkdir certificates
            cd certificates
            ```
            - [ ] Création du certificat SSL self-signed : 
            ```
            openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout key.pem -out    cert.pem
            ```
            retour console : 
            ```
            ...+.........+.+.........+......+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
            -----
            You are about to be asked to enter information that will be incorporated
            into your certificate request.
            What you are about to enter is what is called a Distinguished Name or a DN.
            There are quite a few fields but you can leave some blank
            For some fields there will be a default value,
            If you enter '.', the field will be left blank.
            ```
            prompt infos : 
            ```
            Country Name (2 letter code) [XX]:FR
            State or Province Name (full name) []:
            Locality Name (eg, city) [Default City]:Nordhouse
            Organization Name (eg, company) [Default Company Ltd]:COP
            Organizational Unit Name (eg, section) []:
            Common Name (eg, your name or your server's hostname) []:predict-test-greg
            Email Address []:g.chevalier@cop-amaco.com
            ```
            - [x] Déplacement du dossier certificates : 
            ```
            mv -i -v certificates predict-prod/KMO_PREDICT
            ```
            - [x] Setup du pw & hostname : 
            ```
            export KEYCLOAK_ADMIN_PASSWORD=admin
            export DB_PASSWORD=12062023
            export KEYCLOAK_HOSTNAME=3.123.128.118:8443 (ip publique)
            ```
            - [ ] Lancement de la commande : 
            ```
            sudo docker run -d  \
                --name keycloak-prodtest-greg \
                -v /home/ec2-user/certificates:/certificates \
                --network=host \
                -e KEYCLOAK_ADMIN=admin \
                -e KEYCLOAK_ADMIN_PASSWORD=$KEYCLOAK_ADMIN_PASSWORD \
                quay.io/keycloak/keycloak:18.0.0 \
                start \
                --features=token-exchange \
                --https-certificate-file=/certificates/cert.pem \
                --https-certificate-key-file=/certificates/key.pem \
                --hostname=$KEYCLOAK_HOSTNAME \
                --proxy=edge \
                --db=postgres \
                --db-url=jdbc:postgres://localhost:5432/postgres \
                --db-username=postgres \
                --db-password=$DB_PASSWORD
            ```
            - [ ] Lancement du container : 
            ```
            sudo docker start keycloak-prodtest-greg
            ```
            mais  le container exit après 1s
                - [x] Trouver la raison de l'exit
                ```
                sudo docker logs -t keycloak-prodtest-greg
                2023-06-14T10:13:07.342783643Z Unknown option: '--features'
                ```
                - [x] Nouvel essai sans le --features 
                ```
                sudo docker run -d  \
                --name keycloak-ptg-nofeatures \
                -v /home/ec2-user/certificates:/certificates \
                --network=host \
                -e KEYCLOAK_ADMIN=admin \
                -e KEYCLOAK_ADMIN_PASSWORD=$KEYCLOAK_ADMIN_PASSWORD \
                quay.io/keycloak/keycloak:18.0.0 \
                start \
                --https-certificate-file=/certificates/cert.pem \
                --https-certificate-key-file=/certificates/key.pem \
                --hostname=$KEYCLOAK_HOSTNAME \
                --proxy=edge \
                --db=postgres \
                --db-url=jdbc:postgres://localhost:5432/postgres \
                --db-username=postgres \
                --db-password=$DB_PASSWORD
                ```
                - [x] nouvel arret :
                ```
                sudo docker logs -t keycloak-ptg-nofeatures
                2023-06-14T11:03:20.556699006Z Unknown option: '--db'
                ```
                - [x] Nouvel essai sans le --db 
                ```
                sudo docker run -d  \
                --name keycloak-ptg-nodb \
                -v /home/ec2-user/certificates:/certificates \
                --network=host \
                -e KEYCLOAK_ADMIN=admin \
                -e KEYCLOAK_ADMIN_PASSWORD=$KEYCLOAK_ADMIN_PASSWORD \
                quay.io/keycloak/keycloak:18.0.0 \
                start \
                --https-certificate-file=/certificates/cert.pem \
                --https-certificate-key-file=/certificates/key.pem \
                --hostname=$KEYCLOAK_HOSTNAME \
                --proxy=edge \
                --db-url=jdbc:postgres://localhost:5432/postgres \
                --db-username=postgres \
                --db-password=$DB_PASSWORD
                ```
                nouvel arrêt : 
                ```
                sudo docker logs -t keycloak-ptg-nodb
                2023-06-14T11:49:56.095751601Z ERROR: Failed to start server in (production) mode
                2023-06-14T11:49:56.096065537Z ERROR: Strict hostname resolution configured but no hostname was set
                ```
                - [x] Re run des commandes host, dbp & kcp, logs :
                ```
                INFO  [org.keycloak.quarkus.runtime.hostname.DefaultHostnameProvider] (main) Hostname settings: FrontEnd: 3.123.128.118:8443, Strict HTTPS: true, Path: <request>, Strict BackChannel: false, Admin: <request>, Port: -1, Proxied: true
                WARN  [io.agroal.pool] (agroal-11) Datasource '<default>': No suitable driver found for jdbc:postgres://localhost:5432/postgres
                WARN  [org.hibernate.engine.jdbc.env.internal.JdbcEnvironmentInitiator] (JPA Startup Thread: keycloak-default) HHH000342: Could not obtain connection to query metadata: java.sql.SQLException: No suitable driver found for jdbc:postgres://localhost:5432/postgres
                aSource.getJdbcConnection(JdbcDataSource.java:191)
                aSource.getXAConnection(JdbcDataSource.java:352)
                ctionFactory.createConnection(ConnectionFactory.java:216)
                ctionPool$CreateConnectionTask.call(ConnectionPool.java:513)
                ctionPool$CreateConnectionTask.call(ConnectionPool.java:494)
                concurrent.FutureTask.run(FutureTask.java:264)
                PriorityScheduledExecutor.beforeExecute(PriorityScheduledExecutor.java:75)
                concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1126)
                concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
                Thread.run(Thread.java:829)
                WARN  [io.agroal.pool] (agroal-11) Datasource '<default>': No suitable driver found for jdbc:postgres://localhost:5432/postgres
                WARN  [org.infinispan.PERSISTENCE] (keycloak-cache-init) ISPN000554: jboss-marshalling is deprecated and planned for removal
                WARN  [org.infinispan.CONFIG] (keycloak-cache-init) ISPN000569: Unable to persist Infinispan internal caches as no global state enabled
                INFO  [org.infinispan.CONTAINER] (keycloak-cache-init) ISPN000556: Starting user marshaller 'org.infinispan.jboss.marshalling.core.JBossUserMarshaller'
                INFO  [org.infinispan.CONTAINER] (keycloak-cache-init) ISPN000128: Infinispan version: Infinispan 'Triskaidekaphobia' 13.0.8.Final
                INFO  [org.infinispan.CLUSTER] (keycloak-cache-init) ISPN000078: Starting JGroups channel `ISPN`
                INFO  [org.infinispan.CLUSTER] (keycloak-cache-init) ISPN000088: Unable to use any JGroups configuration mechanisms provided in properties {}. Using default JGroups configuration!
                WARN  [org.jgroups.protocols.UDP] (keycloak-cache-init) JGRP000015: the send buffer of socket MulticastSocket was set to 1.00MB, but the OS only allocated 212.99KB
                WARN  [org.jgroups.protocols.UDP] (keycloak-cache-init) JGRP000015: the receive buffer of socket MulticastSocket was set to 20.00MB, but the OS only allocated 212.99KB
                WARN  [org.jgroups.protocols.UDP] (keycloak-cache-init) JGRP000015: the send buffer of socket MulticastSocket was set to 1.00MB, but the OS only allocated 212.99KB
                WARN  [org.jgroups.protocols.UDP] (keycloak-cache-init) JGRP000015: the receive buffer of socket MulticastSocket was set to 25.00MB, but the OS only allocated 212.99KB
                INFO  [org.jgroups.protocols.pbcast.GMS] (keycloak-cache-init) ip-172-31-29-175-53584: no members discovered after 2011 ms: creating cluster as coordinator
                INFO  [org.infinispan.CLUSTER] (keycloak-cache-init) ISPN000094: Received new cluster view for channel ISPN: [ip-172-31-29-175-53584|0] (1) [ip-172-31-29-175-53584]
                INFO  [org.infinispan.CLUSTER] (keycloak-cache-init) ISPN000079: Channel `ISPN` local address is `ip-172-31-29-175-53584`, physical addresses are `[172.31.29.175:41756]`
                INFO  [org.infinispan.CLUSTER] (main) ISPN000080: Disconnecting JGroups channel `ISPN`
                ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Failed to start server in (production) mode
                ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Failed to obtain JDBC connection
                ERROR [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: No suitable driver found for jdbc:postgres://localhost:5432/postgres
                ```
                raison : il faut --db
                - [x] Essai en ajoutant --auto-build : 
                ```
                sudo docker run -d  \
                --name keycloak-ptg-db \
                -v /home/ec2-user/certificates:/certificates \
                --network=host \
                -e KEYCLOAK_ADMIN=admin \
                -e KEYCLOAK_ADMIN_PASSWORD=$KEYCLOAK_ADMIN_PASSWORD \
                quay.io/keycloak/keycloak:18.0.0 \
                start \
                --auto-build \
                --https-certificate-file=/certificates/cert.pem \
                --https-certificate-key-file=/certificates/key.pem \
                --hostname=$KEYCLOAK_HOSTNAME \
                --proxy=edge \
                --db=postgres \
                --db-url=jdbc:postgres://localhost:5432/postgres \
                --db-username=postgres \
                --db-password=$DB_PASSWORD
                ```
                retour logs : 
                ```
                Changes detected in configuration. Updating the server image.
                Updating the configuration and installing your custom providers, if any. Please wait.
                ```
                vu sur (https://github.com/keycloak/keycloak/issues/10722)
                - [x] Ajout sur l'instance du groupe de sécurité pour ouvrir le port 8443 
                    - modifier règle entrante
                    - ajout d'une règle
                    - TCP perso / 8443 / 0.0.0.0/0
                    - reach https://3.123.128.118:8443/admin/master/console/#/realms/master
    - [ ] Lancer le tout combiné
        - [ ] Créer un docker network externe
        - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
        - [ ] Lancer la giga commande docker run du keycloak avec en flag le network externe

**15 Juin**
- [ ] Mise en prod
    - [ ] Faire démarrer les apps avec le keycloak de prod
        - [x] Connexion sur AWS & run de l'instance test 
        - [ ] Lier les apps en local à un docker network externe local
            - [ ] Créer un docker network externe
            - [ ] Modifier le docker-compose du projet en y intégrant le network externe dans la section keycloak
            - [ ] Lancer la giga commande docker run du keycloak avec en flag le network externe 
        - [ ] Reproduire le schéma en prod
