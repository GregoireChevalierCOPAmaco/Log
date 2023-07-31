**31 Juillet**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [x] Connexion aux instances en SSH avec : 
    ```
    ssh -i "predict-beta-clepaire-27Juillet.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
    ```
    - [ ] Résolution de l'erreur : 
        ```
        net::ERR_CONNECTION_TIMED_OUT
        ```
        - [x] Rebuilld des dockers avec  : 
        ```
        sudo docker compose --env-file .env.beta -f docker-compose.beta.yml up --build
        ```
        - [x] Repopulation de la db
            - Ouverture de pgAdmin
            - Connexion à la db une fois le container pg lancé
            - pw pr....023
            - psoitionnement dans Query tool
            - past du fichier sql
            - vérification de la présence des données dans l'outil view data
            - positionnement dans predict/schemas/public/tables/ ...
        - [x] Changement de la WEB_APP_URL dans le .env.beta de : 
        ```
        WEB_APP_URL=https://predict-beta.cop-amaco.digital:3000
        ```
        à 
        ```
        WEB_APP_URL=https://predict-beta.cop-amaco.digital:80
        ```
        - [x] Ajout à la config nginx des lignes :
        ```
        proxy_set_header Host $host;
        proxy_set_header Origin $http_origin;
        proxy_set_header Access-Control-Request-Method $request_method;
        proxy_set_header Access-Control-Request-Headers $http_access_control_request_headers;
        ```
        à location /api
        - [x] Redmarrage d'nginx
        ```
        sudo systemctl restart nginx
        sudo systemctl reload nginx
        ```
        - [x] Recréation des containers, mais pas de changement, toujours la même erreur

