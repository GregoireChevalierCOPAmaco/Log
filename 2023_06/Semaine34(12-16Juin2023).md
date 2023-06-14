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
    - [ ] Lancer le tout combiné