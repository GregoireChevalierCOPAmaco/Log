**17 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [ ] Faire démarrer les apps sur un serveur séparé de prod
        - [x] Connexion à l'instance en SSH avec : 
        ```
        ssh -i "predict-beta-clepaire.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
        ```
        Connexion success !
        &&
        ```
        ssh -i "keycloak-beta-clepaire.pem" ubuntu@ec2-3-75-5-27.eu-central-1.compute.amazonaws.com
        ```
- [ ] Lier les apps au serveur keycloak
    - [ ] "Connecter" les applications au serveur keycloak
        - [ ] Refaire le docker-compose
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