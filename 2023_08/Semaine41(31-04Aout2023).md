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