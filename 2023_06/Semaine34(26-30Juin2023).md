**26 Juin**
- [ ] Check de let's encrypt (https://letsencrypt.org/fr/getting-started/)
- [ ] Récupération d'un certificat letsencrypt
    - [x] Suivi de (https://mistonline.in/wp/how-to-secure-apache-using-lets-encrypt-on-amazon-linux-2/)
    - [ ] Installation des modules nécessaires
        - [ ] certbot
            ```
            sudo yum install certbot
            ```
            retour console : 
            ```
            Last metadata expiration check: 3:37:47 ago on Fri Jun 23 04:53:43 2023.
            No match for argument: certbot
            Error: Unable to find a match: certbot
            ```
            check de (https://unix.stackexchange.com/questions/744633/how-to-install-certbot-via-snap-on-amazon-linux-2023) pour comprendre
            - [ ] Installation d'nginx
                - [x] Check de (https://medium.com/@mertcal/install-nginx-on-ec2-instance-in-30-seconds-e714d427b01b)
                - [x] ```sudo yum install nginx```
                retour positif : 
                ```
                 Package                       Architecture     Version                            Repository             Size
                ===============================================================================================================
                Installing:
                nginx                         x86_64           1:1.24.0-1.amzn2023.0.1            amazonlinux            32 k
                Installing dependencies:
                generic-logos-httpd           noarch           18.0.0-12.amzn2023.0.3             amazonlinux            19 k
                gperftools-libs               x86_64           2.9.1-1.amzn2023.0.2               amazonlinux           309 k
                libunwind                     x86_64           1.4.0-5.amzn2023.0.2               amazonlinux            66 k
                nginx-core                    x86_64           1:1.24.0-1.amzn2023.0.1            amazonlinux           586 k
                nginx-filesystem              noarch           1:1.24.0-1.amzn2023.0.1            amazonlinux           9.0 k
                nginx-mimetypes               noarch           2.1.49-3.amzn2023.0.3              amazonlinux            21 k

                Transaction Summary
                ===============================================================================================================
                Install  7 Packages

                Total download size: 1.0 M
                Installed size: 3.4 M
                Is this ok [y/N]: y
                Complete
                ```
                - [x] Modification de la config nginx :
                ```sudo vi /etc/nginx/nginx.conf```, set de worker_process à ```auto```
                - [ ] Démarrage : ```sudo systemctl start nginx.service``` et vérification avec  ```sudo systemctl status nginx.service```
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


**27 Juin**
- [x] Mise à jour du jira
- [ ] Check de let's encrypt (https://letsencrypt.org/fr/getting-started/)
- [ ] Récupération d'un certificat letsencrypt
    - [ ] Installation des modules nécessaires
        - [ ] certbot
            - [ ] Installation de certbot
                - [x] Installation de nginx
                - [ ] Installation de snapd
                    - [x] Check de (https://snapcraft.io/docs/installing-snap-on-arch-linux)
                    - [ ] suivi des commandes : 
                    ```
                    git clone https://aur.archlinux.org/snapd.git
                    cd snapd
                    makepkg -si
                    ```
                    retour terminal : 
                    ```
                    Cloning into 'snapd'...
                    remote: Enumerating objects: 677, done.
                    remote: Counting objects: 100% (677/677), done.
                    remote: Compressing objects: 100% (397/397), done.
                    remote: Total 677 (delta 283), reused 666 (delta 275), pack-reused 0
                    Receiving objects: 100% (677/677), 196.66 KiB | 2.69 MiB/s, done.
                    Resolving deltas: 100% (283/283), done.
                    ```
                    &
                    ```
                    makepkg -si command not found
                    ```
                    - [x] Check de (https://www.thegeekdiary.com/makepkg-command-not-found/)
                    - [ ] Installation de snapd via pip (https://certbot.eff.org/instructions?ws=nginx&os=pip) : 
                    ```
                    sudo yum update
                    sudo yum install python3 augeas-libs
                    ```
                    retour console
                    ```
                    Installing:
                    augeas-libs              x86_64              1.13.0-1.amzn2023.0.2              amazonlinux              408 k

                    Transaction Summary
                    ================================================================================================================
                    Install  1 Package

                    Total download size: 408 k
                    Installed size: 1.2 M
                    Is this ok [y/N]: y
                    Downloading Packages:
                    augeas-libs-1.13.0-1.amzn2023.0.2.x86_64
                    ....
                    Complete!
                    ```
                    - [x] Setup python virtual env :
                    ```
                    sudo /opt/certbot/bin/pip install certbot certbot-nginx
                    sudo /opt/certbot/bin/pip install --upgrade pip
                    ```
                    retour terminal : 
                    ```
                    Requirement already satisfied: pip in /opt/certbot/lib/python3.9/site-packages (21.3.1)
                    Collecting pip
                    Downloading pip-23.1.2-py3-none-any.whl (2.1 MB)
                        |████████████████████████████████| 2.1 MB 5.8 MB/s
                    Installing collected packages: pip
                    Attempting uninstall: pip
                        Found existing installation: pip 21.3.1
                        Uninstalling pip-21.3.1:
                        Successfully uninstalled pip-21.3.1
                    Successfully installed pip-23.1.2
                    ```
                - [x] Installation de certbot : 
                ```
                sudo yum remove certbot
                sudo /opt/certbot/bin/pip install certbot certbot-nginx
                ```
                - [ ] Installation de nginx plugin for certbot
            - [ ] Configuration du virtual host
            - [ ] Request de certificat
            - [ ] Update du path de crtificat ssl
            - [ ] Restart nginx & test du reach  
                - [ ] Réussir à accéder au site (3.123.128.118)
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


**28 Juin**
- [x] Mise à jour du jira
- [ ] Check de let's encrypt (https://letsencrypt.org/fr/getting-started/)
- [ ] Récupération d'un certificat letsencrypt
    - [ ] Installation des modules nécessaires
        - [ ] certbot
            - [ ] Installation de certbot
                - [x] Installation de nginx
                - [x] ouverture du port 80 dans les règles de sécurité 
                    - instance / onglet sécurité / lien externe vers les groupes de sécurité
                    - modifier règle entrante
                    - ajout d'une règle
                    - TCP perso / 8443 / 0.0.0.0/0
                    - reach https://3.123.128.118:8443/admin/master/console/#/realms/master
                - [x] Installation de certbot
                - [x] Installation de nginx plugin for certbot
            - [ ] Configuration du virtual host
            - [ ] Request de certificat
            - [ ] Update du path de crtificat ssl
            - [ ] Restart nginx & test du reach  
                - [ ] Réussir à accéder au site (3.123.128.118)
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
