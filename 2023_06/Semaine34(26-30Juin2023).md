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
            - [x] ouverture du port 80 dans les règles de sécurité 
                - instance / onglet sécurité / lien externe vers les groupes de sécurité
                - modifier règle entrante
                - ajout d'une règle
                - TCP perso / 8443 / 0.0.0.0/0
                - reach https://3.123.128.118:8443/admin/master/console/#/realms/master
            - [x] Installation de certbot
                - [x] Installation de nginx
                - [x] Installation de certbot
                - [x] Installation de nginx plugin for certbot
                - [x] Lancement de ```sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot``` pour assurer la faisabilité du certificat sur la machine, retour terminal normal
            - [x] Génération du certificat
                - [x] ```sudo certbot --nginx```
                retour console : 
                ```
                Saving debug log to /var/log/letsencrypt/letsencrypt.log
                Enter email address (used for urgent renewal and security notices)
                (Enter 'c' to cancel): g.chevalier@cop-amaco.com

                - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                Please read the Terms of Service at
                https://letsencrypt.org/documents/LE-SA-v1.3-September-21-2022.pdf. You must
                agree in order to register with the ACME server. Do you agree?
                - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                (Y)es/(N)o: y

                - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                Would you be willing, once your first certificate is successfully issued, to
                share your email address with the Electronic Frontier Foundation, a founding
                partner of the Let's Encrypt project and the non-profit organization that
                develops Certbot? We'd like to send you email about our work encrypting the web,
                EFF news, campaigns, and ways to support digital freedom.
                - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                (Y)es/(N)o: n
                Account registered.
                Please enter the domain name(s) you would like on your certificate (comma and/or
                space separated) (Enter 'c' to cancel): http://3.123.128.118/
                ```
                - [x] Demande à Kévin de créer un sous-domaine OVH appelé testgreg.cop-amaco.digital/ qui tape sur http://3.123.128.118/
                re-run de la commande certbot
                - [x] renseignement du http://testgreg.cop-amaco.digital/ dans l'étape de génération du certificat, et retour terminal : 
                ```
                Requesting a certificate for testgreg.cop-amaco.digital

                Successfully received certificate.
                Certificate is saved at: /etc/letsencrypt/live/testgreg.cop-amaco.digital/fullchain.pem
                Key is saved at:         /etc/letsencrypt/live/testgreg.cop-amaco.digital/privkey.pem
                This certificate expires on 2023-09-27.
                These files will be updated when the certificate renews.

                Deploying certificate
                Could not install certificate

                NEXT STEPS:
                - The certificate was saved, but could not be installed (installer: nginx). After fixing the error shown below, try installing it again by running:
                certbot install --cert-name testgreg.cop-amaco.digital
                - The certificate will need to be renewed before it expires. Certbot can automatically renew the certificate in the background, but you may need to take steps to enable that functionality. See https://certbot.org/renewal-setup for instructions.

                Could not automatically find a matching server block for testgreg.cop-amaco.digital. Set the `server_name` directive to use the Nginx installer.
                Ask for help or search for solutions at https://community.letsencrypt.org. See the logfile /var/log/letsencrypt/letsencrypt.log or re-run Certbot with -v for more details.
                ```
                - [x] Soumission du retour à chat gpt, réponse : 
                ```
                The error message indicates that Certbot could not automatically find a matching server block for your domain in the Nginx configuration. To resolve this issue, you need to manually configure Nginx to recognize your domain and serve the SSL/TLS certificate.

                Here are the steps you can take to fix the problem:

                1. Open the Nginx configuration file for your website. The location of the file can vary depending on your Linux distribution, but common paths include `/etc/nginx/nginx.conf` or `/etc/nginx/sites-available/default`.

                2. Within the configuration file, locate the `server` block that corresponds to the domain `testgreg.cop-amaco.digital` (or create a new `server` block if one doesn't exist).

                3. Inside the `server` block, ensure that the `server_name` directive is set to your domain name. For example:
                ```
                server_name testgreg.cop-amaco.digital;
                ```

                4. Verify that the `location` block for port 80 (HTTP) includes a redirect to HTTPS. If not, add the following lines inside the `server` block:
                ```
                location / {
                    return 301 https://$host$request_uri;
                }
                ```

                5. Save the changes to the Nginx configuration file.

                6. Test the configuration to ensure there are no syntax errors:
                ```
                sudo nginx -t
                ```
                If any errors are reported, review your changes and correct any mistakes.

                7. Once the configuration passes the test, restart Nginx to apply the changes:
                ```
                sudo service nginx restart
                ```

                8. Finally, run the Certbot installation command again to install the certificate:
                ```
                sudo certbot install --cert-name testgreg.cop-amaco.digital
                ```

                This command should install the certificate and configure Nginx to use it.

                If you encounter any issues during this process or need further assistance, don't hesitate to ask for help on the Let's Encrypt community forum or refer to the Certbot documentation for more troubleshooting options.
                ```
            - [ ] Configuration du virtual host
            - [ ] Request de certificat
            ```
            sudo certbot --nginx
            Saving debug log to /var/log/letsencrypt/letsencrypt.log

            Which names would you like to activate HTTPS for?
            We recommend selecting either all domains, or all domains in a VirtualHost/server block.
            - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
            1: testgreg.cop-amaco.digital
            - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
            Select the appropriate numbers separated by commas and/or spaces, or leave input
            blank to select all options shown (Enter 'c' to cancel): 1
            Certificate not yet due for renewal

            You have an existing certificate that has exactly the same domains or certificate name you requested and isn't close to expiry.
            (ref: /etc/letsencrypt/renewal/testgreg.cop-amaco.digital.conf)

            What would you like to do?
            - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
            1: Attempt to reinstall this existing certificate
            2: Renew & replace the certificate (may be subject to CA rate limits)
            - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
            Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 1
            Deploying certificate
            Successfully deployed certificate for testgreg.cop-amaco.digital to /etc/nginx/nginx.conf
            Congratulations! You have successfully enabled HTTPS on https://testgreg.cop-amaco.digital

            - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
            If you like Certbot, please consider supporting our work by:
            * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
            * Donating to EFF:                    https://eff.org/donate-le
            - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
            [ec2-user@ip-172-31-29-175 ~]$ ping https://testgreg.cop-amaco.digital
            ping: https://testgreg.cop-amaco.digital: Name or service not known
            [ec2-user@ip-172-31-29-175 ~]$ sudo service nginx restart
            Redirecting to /bin/systemctl restart nginx.service
            ```
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


**29 Juin**
- [x] Mise à jour du jira
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
                retour terminal : 
                ```
                Unit cron.service could not be found.
                ```
                - [x] Installation de cron
                    - [x] Check de (https://jainsaket-1994.medium.com/installing-crontab-on-amazon-linux-2023-ec2-98cf2708b171)
                    - [x] Installation : 
                    ```
                    sudo yum install cronie -y
                    ```
                    retour terminal : 
                    ```
                    Installing:
                    cronie                      x86_64              1.5.7-1.amzn2023.0.2                amazonlinux              115 k
                    Installing dependencies:
                    cronie-anacron              x86_64              1.5.7-1.amzn2023.0.2                amazonlinux               32 k

                    Transaction Summary
                    ====================================================================================================================
                    Install  2 Packages

                    Total download size: 147 k
                    Installed size: 341 k
                    Downloading Packages:
                    (1/2): cronie-anacron-1.5.7-1.amzn2023.0.2.x86_64.rpm                               378 kB/s |  32 kB     00:00
                    (2/2): cronie-1.5.7-1.amzn2023.0.2.x86_64.rpm
                    Installed:
                    cronie-1.5.7-1.amzn2023.0.2.x86_64                   cronie-anacron-1.5.7-1.amzn2023.0.2.x86_64

                    Complete!
                    ```
                    puis
                    ```
                    sudo systemctl enable crond.service
                    &
                    sudo systemctl start crond.service
                    ```
                    et check du statut :
                    ```
                    sudo systemctl status crond | grep Active
                    ```
            - [ ] Restart nginx & test du reach  
                - [ ] Réussir à accéder au site (3.123.128.118)  
                - [ ] Réussir à accéder au site (http://testgreg.cop-amaco.digital/)
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