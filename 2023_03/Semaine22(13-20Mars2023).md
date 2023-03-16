**13 Mars**
- [ ] Passage au keycloak predict
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Exporter les users en meme temps que le realm 
        - [x] Reprise du mail d'Olivier sur le sujet
        - [x] Réouverture du projet atmos pour référence
        - [x] Suivi de la doc concernant l'export des users
            - [x] Récupération de la commande : 
            ```
            docker exec -it atmos_keycloak /opt/keycloak/bin/kc.sh export --users skip --realm atmos --file /tmp/realm.json
            docker cp atmos_keycloak:/tmp/realm.json .
            ```
            - [x] Check de la doc https://www.keycloak.org/server/importExport
            - [ ] Modification de la commande 
                - [x] Essai dans le terminal de : 
                ```
                export --file testexport.json --users same_file --users-per-file 10
                ```
                & retour console : ```no such file or directory```
                - [x] Rechercehs google & ls -a dans le container docker
                - [x] Essai de commande : 
                ```
                docker exec -it keycloak-sass-container /opt/keycloak/bin/kc.sh export --file testexport.json --users same_file --users-per-file 10
                ```
                dans le terminal du container docker, retour terminal : ```sh: docker: command not found```
                - [x] Essai de commande : 
                ```
                docker exec -it keycloak-sass-container /opt/keycloak/bin/kc.sh export --file testexport.json --users same_file --users-per-file 10
                ```
                dans le terminal du projet vscode kmo_sass_keycloak, et retour terminal : 
                ```
                [org.keycloak.quarkus.runtime.hostname.DefaultHostnameProvider] (main) Hostname settings: FrontEnd: <request>, Strict HTTPS: false, Path: <request>, Strict BackChannel: false, Admin: <request>, Port: -1, Proxied: false
                [org.infinispan.PERSISTENCE] (keycloak-cache-init) ISPN000554: jboss-marshalling is deprecated and planned for removal
                [org.infinispan.CONFIG] (keycloak-cache-init) ISPN000569: Unable to persist Infinispan internal caches as no global state enabled
                [org.infinispan.CONTAINER] (keycloak-cache-init) ISPN000556: Starting user marshaller 'org.infinispan.jboss.marshalling.core.JBossUserMarshaller'
                [org.infinispan.CONTAINER] (keycloak-cache-init) ISPN000128: Infinispan version: Infinispan 'Triskaidekaphobia' 13.0.8.Final
                [org.infinispan.CLUSTER] (keycloak-cache-init) ISPN000078: Starting JGroups channel `ISPN`
                [org.infinispan.CLUSTER] (keycloak-cache-init) ISPN000088: Unable to use any JGroups configuration mechanisms provided in properties {}. Using default JGroups configuration!
                [org.jgroups.protocols.UDP] (keycloak-cache-init) JGRP000015: the send buffer of socket MulticastSocket was set to 1.00MB, but the OS only allocated 212.99KB
                [org.jgroups.protocols.UDP] (keycloak-cache-init) JGRP000015: the receive buffer of socket MulticastSocket was set to 20.00MB, but the OS only allocated 212.99KB
                [org.jgroups.protocols.UDP] (keycloak-cache-init) JGRP000015: the send buffer of socket MulticastSocket was set to 1.00MB, but the OS only allocated 212.99KB
                [org.jgroups.protocols.UDP] (keycloak-cache-init) JGRP000015: the receive buffer of socket MulticastSocket was set to 25.00MB, but the OS only allocated 212.99KB
                [org.infinispan.CLUSTER] (keycloak-cache-init) ISPN000094: Received new cluster view for channel ISPN: [a24d61dd74c7-9059|3] (2) [a24d61dd74c7-9059, a24d61dd74c7-2940]
                [org.infinispan.CLUSTER] (keycloak-cache-init) ISPN000079: Channel `ISPN` local address is `a24d61dd74c7-2940`, physical addresses are `[172.27.0.3:34744]`
                [org.keycloak.connections.infinispan.DefaultInfinispanConnectionProviderFactory] (main) Node name: a24d61dd74c7-2940, Site name: null
                [org.keycloak.services] (main) KC-SERVICES0033: Full model export requested
                [org.keycloak.exportimport.singlefile.SingleFileExportProvider] (main) Exporting model into file /testexport.json
                [io.agroal.pool] (main) Datasource '<default>': JDBC resources leaked: 1 ResultSet(s) and 1 Statement(s)
                [org.infinispan.CLUSTER] (main) ISPN000080: Disconnecting JGroups channel `ISPN`
                [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Failed to start server in (import_export) mode
                [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: Error during export/import: testexport.json (Permission denied)
                [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) ERROR: testexport.json (Permission denied)
                [org.keycloak.quarkus.runtime.cli.ExecutionExceptionHandler] (main) For more details run the same command passing the '--verbose' option. Also you can use '--help' to see the details about the usage of the particular command.
                ```
                - [x] Check de (https://github.com/keycloak/keycloak/discussions/10815)
                - [x] Lancer la commande ```/opt/keycloak/bin/kc.sh export --file testexport.json --users same_file --users-per-file 10``` directement dans le docker renvoie le même résultat
                - [x] Essai de : ```QUARKUS_HTTP_HOST_ENABLED=false /opt/keycloak/bin/kc.sh export --file /tmp/testexport.json --users same_file --users-per-file 10```
                - [x] Le fichier est maintenant exporté dans le dossier /tmp
            - [x] Check de (https://keepgrowing.in/tools/keycloak-in-docker-5-how-to-export-a-realm-with-users-and-secrets/)
        - [x] Trouver comment déplacer le fichier /tmp/realmexport.json depuis /tmp/ dans le docker vers Windows pour le récupérer
            - [x] Check de (https://stackoverflow.com/questions/22049212/docker-copying-files-from-docker-container-to-host) & (https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/How-to-copy-files-from-a-Docker-container-to-a-host-machine)
            - [x] Création d'un dossier realm_exports sur le bureau
            - [x] win+R -> cmd -> entrée -> ```docker cp keycloak-sass-container:/tmp/realm-export_13_03_2023.json C:\Users\gchevalier\Desktop\realm_exports```
        - [ ] Suivre (https://www.keycloak.org/server/containers#_importing_a_realm_on_startup) pour la suite
    - [ ] Assigner un attribut store aux users
        - [ ] Suivre (https://www.keycloak.org/docs/latest/server_admin/#proc-configuring-user-attributes_server_administration_guide)
        - [ ] Création d'une nouvelle branche
        - [ ] Création d'un attribut store dans le keycloak
    - [ ] Ajouter un affichage conditionnel en fonction de l'attribut user
- [ ] Mise en relation des applications dans le keycloak

**14 Mars**
- [ ] Passage au keycloak predict
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [x] Exporter les users en meme temps que le realm 
    - [x] Essai de création de realm et import total avec users pour voir
        - [x] Comparatif entre les json
            - [x] Le realm exporté est le master. Ce qui est nécessaire si on veut récupérer les users, mais du coup à l'import via la console d'administration ça créée un conflit vu que le realm master est déjà existant.
            - [ ] Est-il possible de n'exporter que les users et leurs relations ?
            - [ ] Est-il possible d'importer des users dans un realm deja existant ?
            - [ ] Et sinon comment faire pour importer le realm master à l'initialisation du keycloak ?
                - [x] Check de (https://keycloak.discourse.group/t/keycloak-17-docker-container-how-to-export-import-realm-import-must-be-done-on-container-startup/13619/21?page=2)
                - [x] Création d'une branche ?
                    - [x] Non
                - [x] Création & assignation d'une tâche jira
                - [x] Se baser sur --import-realm (cf. docker-compose.yml du projet socle atmos)
                - [x] Création d'un nouveau dossier/projet KMO SASS V2
                    - [x] écriture du docker compose : 
                    ```
                    version: "3.9"
                    volumes:
                    postgres_data:
                        driver: local

                    services:

                    postgres:
                        container_name: postgresdb-sass-containerV2
                        image: postgres:11
                        volumes:
                        - postgres_data:/var/lib/postgresql/data
                        environment:
                        POSTGRES_DB: keycloak
                        POSTGRES_USER: keycloak
                        POSTGRES_PASSWORD: pw123
                        ports:
                        - "5432:5432"

                    keycloak:
                        container_name: keycloak-sass-containerV2
                        depends_on:
                        - postgres
                        image: quay.io/keycloak/keycloak:18.0.0
                        command:
                        - docker cp ./realm-export_13_03_2023.json keycloak-sass-containerV2:/opt/keycloak/data/import
                        - start-dev --import-realm
                        - -Dkeycloak.migration.strategy=IGNORE_EXISTING
                        ports:
                        - 8080:8080
                        environment:
                        DB_VENDOR: POSTGRES
                        DB_ADDR: postgres
                        DB_DATABASE: keycloak
                        DB_USER: keycloak
                        DB_SCHEMA: public
                        DB_PASSWORD: password
                        KEYCLOAK_USER: jojo
                        KEYCLOAK_PASSWORD: pw123
                        KEYCLOAK_LOGLEVEL: DEBUG
                        ROOT_LOGLEVEL: DEBUG
                        KEYCLOAK_ADMIN: admin
                        KEYCLOAK_ADMIN_PASSWORD: admin
                        KEYCLOAK_IMPORT: /tmp/realm-export_13_03_2023.json
                        PROXY_ADDRESS_FORWARDING: 'true'
                    ```
                - [x] Lancement du compose pour voir le résultat : erraur dans le terminal ```Unknown option: 'docker'```, la ligne :
                ```
                command:
                        - docker cp ./realm-export_13_03_2023.json keycloak-sass-containerV2:/opt/keycloak/data/import
                ```
                - [x] Check de (https://stackoverflow.com/questions/54796791/importing-keycloak-configuration-files-while-using-docker-compose) & application des changements :
                    - suppression de la ligne de commande irrelevante
                    - ajout dans  de :
                    ```
                    volumes:
                    - ./imports:/opt/jboss/keycloak/imports
                    command: 
                    - "-b 0.0.0.0 -Dkeycloak.import=/opt/jboss/keycloak/imports/realm-export_13_03_2023.json"
                    ```
                - [x] insight de la doc : 
                ```
                To re-create realms you should explicitly run the import command prior to starting the server.

                Importing the master realm is not supported because as it is a very sensitive operation.
                ```
                Il va falloir trouver comment exporter un realm spécifique, le doc dit :
                ```
                Exporting a specific realm
                If you do not specify a specific realm to export, all realms are exported. To export a single realm, you can use the --realm option as follows:

                bin/kc.[sh|bat] export [--dir|--file] <path> --realm my-realm
                ```
                - [x] Lancement la commande ```QUARKUS_HTTP_HOST_ENABLED=false /opt/keycloak/bin/kc.sh export --file realm-export_14_03_2023.json --users same_file --users-per-file 10 --realm Cop_sass``` depuis le dossier tmp
                - [ ] Mise en place d'un script sh pour importer le realm avec users (Optionnel)
                    - [ ] Appel du script dans le docker-compose.yml
    - [ ] Assigner un attribut store aux users
        - [ ] Suivre (https://www.keycloak.org/docs/latest/server_admin/#proc-configuring-user-attributes_server_administration_guide)
        - [ ] Création d'une nouvelle branche
        - [ ] Création d'un attribut store dans le keycloak
    - [ ] Ajouter un affichage conditionnel en fonction de l'attribut user
- [ ] Mise en relation des applications dans le keycloak

**15 Mars**
- [x] Review du code socket.io dans les fichiers gateways du back & ngrok
    - [x] Check de (https://ngrok.com/)
- [ ] Passage au keycloak predict
    - [ ] Automatiser l'import du realm au lancement de keycloak dans le docker compose
        - [x] Check de (https://stackoverflow.com/questions/39176561/copying-files-to-a-container-with-docker-compose)
        - [x] Reprise du docker-compose comme suit :
        ```
        volumes:
        - ./imports:/opt/jboss/keycloak/imports
        command:
        - /opt/keycloak/bin/kc.sh import --file realm-export_14_03_2023.json
        - start-dev
        ```
        - [x] Le fichier de realm json est bien ajouté au docker dans opt/jboss/keycloak/imports, mais la commande d'import renvoie : ```Unknown option: '--profile'```
        - [x] Résolution de l'erreur d'import de realm  : ```Unknown option: '--profile'```
            - [x] Essai avece la commande : 
            ```
            - start-dev --import-realm --file /opt/jboss/keycloak/imports/realm-export_14_03_2023.json
            ```
            et retour terminal : Instead of manually specifying the files to import, just copy them to the 'data/import' directory.
            - [x] Reprise du docker-compose comme suit :
            ```
            volumes:
            - ./imports:/opt/keycloak/data/imports
            command:
            - start-dev --import-realm
            ```
            - [x] Erreur résolue, le docker se build, mais le realm n'apparaît pas sur la console d'administration
            Voir avec olivier its ?
        - [ ] Recherches sur keycloak angular use attributes with users
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
        - [x] Check de (https://www.keycloak.org/server/hostname)
        - [ ] Check de (https://keycloak.discourse.group/t/stuck-understanding-frontendurl-baseurl-and-configuring-registration-pages/4638)
    - [ ] Keycloak realm : lier le realm au serveur de mail
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [x] Résolution des erreurs de tests d'Anthony
    - [ ] Voir pour la construction du keycloak version prod
    - [ ] Voir pour l'addition de stores depuis l'application (ajout et lien d'attribut store à la création d'un user ?)

**16 Mars**
- [x] Check du protocole gateways écrit par Guillaume pour le predict (file:///C:/Users/gchevalier/Downloads/Specification_G2W.pdf)
- [x] Voir avec Anthony pour le mock des components à tester
    - [x] Utilisation du spyOn
    - [x] Utilisation du spyOn sur une variable, check de (https://stackoverflow.com/questions/56706667/how-to-mock-variables-inside-a-function-in-jest)
- [ ] Passage au keycloak predict
    - [x] Keycloak realm : se renseigner sur les timeout de session clients 
        - [x] Check de (https://wjw465150.gitbooks.io/keycloak-documentation/content/server_admin/topics/sessions/timeouts.html)
        - [x] Application su rle keycloak ici : http://localhost:8080/admin/master/console/#/realms/Cop_sass/token-settings
    - [ ] Keycloak realm : se renseigner sur le Fronted URL
        - [x] Check de (https://www.keycloak.org/server/hostname#_frontend)
        - [x] Check de (https://keycloak.discourse.group/t/stuck-understanding-frontendurl-baseurl-and-configuring-registration-pages/4638)
        - [ ] Modifier la console admin en conséquence
    - [ ] Keycloak realm : lier le realm au serveur de mail
        - [x] Check du fichier ppt tuto mail monteurs
        - [x] Check de la doc adaptée à la version 18 de keycloak in use : (https://www.keycloak.org/docs/18.0/server_admin/)
        - [x] Aller dans le keycloak sur Realm Settings -> Email
        - [ ] Configuration
            - [x] Remplissage des champs avec : 
                - mailhost.cop-amaco.com
                - 25
                - Cop Keycloak admin
                - g.chevalier@cop-amaco.com
                - Cop Keycloak admin
                - g.chevalier@cop-amaco.com
                - X
                - OFF
                - OFF
                - OFF
            Retour : error, no email set for this user
            - [x] Ajout d'un email au compte admin de keycloak (v Admin -> Manage Account -> Personal info)
            - [x] Renvoi du formulaire rempli à l'identique,
            Retour : error! Failed to send email
            - [ ] Résolution de l'erreur
    - [ ] Keycloak realm : se renseigner sur l'utilisation des thèmes pour le realm
    - [ ] Voir pour la construction du keycloak version prod
        - [x] Check de (https://www.keycloak.org/server/containers)
        - [x] Check de (https://dev.to/gjrdiesel/setup-a-production-grade-keycloak-instance-on-docker-19dk)
        - [x] Check de (https://siweheee.medium.com/keycloak-a-real-scenario-from-development-to-production-ce57800e3ba9)
    - [ ] Voir pour l'addition de stores depuis l'application (ajout et lien d'attribut store à la création d'un user ?)
