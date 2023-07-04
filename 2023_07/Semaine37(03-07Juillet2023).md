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
    - [ ] Créer l'instance ubuntu
    - [ ] Ouvrir les ports
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