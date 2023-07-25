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
