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
        - [x] Troubleshoot des issues avec gpt, #1 : 
        ```
        Check Nginx Access Log:

        Review the Nginx access log (/var/log/nginx/access.log) to see if there are any entries related to the failed API requests. Check the timestamps and request details to identify if the requests from your Angular app are reaching the Nginx server.
        Verify Docker Containers:

        Ensure that both the Angular app and the Nest.js app are running inside the Docker containers and are reachable via their respective ports (3000 for Angular and 3001 for Nest.js).
        Check Angular App Console Logs:

        Inspect the browser console logs of your Angular app again to see if there are any specific error messages related to the failed API requests. The Angular app might provide more information about the cause of the issue.
        Test API Endpoint Separately:

        As mentioned earlier, try accessing the API server directly using tools like Postman or cURL (https://predict-beta.cop-amaco.digital:3001/api) to see if you can get a response from the API server. This will help verify if the API server is accessible and responsive.
        Check Firewall Settings:

        Make sure that there are no firewall rules blocking communication between the Angular app and the Nest.js API server.
        Verify HTTPS Certificates:

        Double-check that the SSL certificates (fullchain.pem and privkey.pem) are in the correct paths and are accessible by Nginx.
        Check Network Connectivity:
        ```
        - [ ] Troubleshoot des issues avec gpt, #2 : 
        ```
        Based on the details provided, it seems that the Nginx configuration and the Docker setup are generally correct. Since the Nginx access log doesn't show any entries related to the failed API requests and accessing the API server directly with Postman returns a timeout error, it indicates that there might be an issue with the connectivity between the Angular app (running on port 3000) and the Nest.js API server (running on port 3001).

        Given the current setup, the following steps can be taken to further troubleshoot and identify the root cause of the issue:

        Check Angular Environment:

        Double-check the environment file for your Angular app (environment.prod.ts or environment.ts, depending on the environment) to ensure that the apiUrl is correctly set to https://predict-beta.cop-amaco.digital:3001/api. Also, ensure that the Angular app is using the correct environment file (environment.prod.ts for production).
        Verify Angular App Deployment:

        Ensure that the Angular app is correctly deployed and running on port 3000 inside the Docker container. You can check the logs of the Angular app container (kmo-predict-front:dev) to see if there are any issues related to its startup or deployment.
        Test Connectivity within Docker Network:

        Since both the Angular app and the Nest.js API server are running inside Docker containers and connected to the app_network, you can try to test the connectivity between them within the Docker network. You can run a temporary container and use curl or wget to see if you can access the API server. For example:
        css
        Copy code
        docker run --rm -it --network app_network alpine sh
        Once inside the container, try to access the API server using curl:
        bash
        Copy code
        curl https://apps:3001/api
        If this command works and returns a response, it indicates that the API server is reachable within the Docker network.
        Check Angular App Console Logs:

        Inspect the browser console logs of your Angular app again to see if there are any specific error messages related to the failed API requests. The Angular app might provide more information about the cause of the issue.
        Check for Possible CORS Issues:

        Ensure that the Nest.js API server's CORS configuration is allowing requests from the Angular app's domain (https://predict-beta.cop-amaco.digital) and port (:3000). You can also try temporarily disabling CORS on the API server to see if it affects the behavior.
        Test Connectivity from the Host Machine:

        Try accessing the Angular app and the Nest.js API server from your host machine's browser and verify if they work as expected. This will help isolate whether the issue is specific to Docker or not.
        Check for Network Firewalls:

        Make sure that there are no network firewalls or security groups blocking communication between the Angular app and the API server.
        ```

**2 Aout**
- [x] Mise à jour du Jira
- [ ] Mise en prod
    - [x] Connexion aux instances en SSH avec : 
    ```
    ssh -i "predict-beta-clepaire-27Juillet.pem" ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com
    ```
    - [x] Retour négatif : 
    ```
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    @    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
    Someone could be eavesdropping on you right now (man-in-the-middle attack)!
    It is also possible that a host key has just been changed.
    The fingerprint for the ED25519 key sent by the remote host is
    SHA256:iAOQHeDkqiWZEJjMHD14aE5fZS4WejkZ3Ae8BpYh5HI.
    Please contact your system administrator.
    Add correct host key in C:\\Users\\gchevalier/.ssh/known_hosts to get rid of this message.
    Offending ECDSA key in C:\\Users\\gchevalier/.ssh/known_hosts:3
    Host key for ec2-52-58-79-10.eu-central-1.compute.amazonaws.com has changed and you have requested strict checking.
    Host key verification failed.
    ```
    - [ ] Suppression du fichier known hosts