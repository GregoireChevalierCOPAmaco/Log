**26 Avril**
- [x] Check mails
- [ ] Poursuite Predict
    - [ ] KP-177
    - [ ] Écriture des tests
        - [ ] Tester que l'application angular existe & est définie
        - [ ] Tester que l'user est redirigé vers l'auth keycloak
            - [ ] Réussir à atteindre la bonne page de connexion
                - [x] Entrée en url de : http://localhost:3000/administration et redicrection vers : http://localhost:8080/realms/Cop_sass/protocol/openid-connect/auth?client_id=KMO_Predict&redirect_uri=http%3A%2F%2Flocalhost%3A3000%2Fadministration&state=0c61fe09-7118-4b61-9c62-a63cfa25b8cb&response_mode=fragment&response_type=code&scope=openid&nonce=9a97c059-a767-4b63-8e9a-b2b85351d685
                - [ ] Résolution de la 400 bad request
                    - [X] Check de http://localhost:8080/realms/Cop_sass/protocol/openid-connect/auth... dans postman : retour err 400 avec header : 
                    ```
                    <header class="login-pf-header">
                        <h1 id="kc-page-title"> We are sorry...  </h1>
                    </header>
                        <div id="kc-content">
                        <div id="kc-content-wrapper">
					        <div id="kc-error-message">
                                <p class="instruction">Invalid Request</p>
                    ```
                    - [x] Même résultat avec : http://localhost:8080/realms/Cop_sass/protocol/openid-connect/auth?client_id=KMO_Predict
                    - [x] Essais postman : 
                        - [x] http://localhost:3000/administration  == 404
                        - [x] http://localhost:3000/home  == 404
                        - [x] http://localhost:3000/cop  == 404
                        - [x] http://localhost:3000/  == 200 OK
                    - [x] L'url d'auth est 200 OK en /GET
                - [x] Check de () qui mentionne : 
                ```
                Some web applications "fake" 404 Not Found Errors when requesting an invalid resource. 
                The server returns a standard 200 OK response code, which means the resource loaded as expected, however, the server displayed a custom "404 page.” 
                Such fake errors are typically referred to as soft 404 errors. 
                The provided URL could have been valid in the past, but the server has failed to provide a server-side redirect.
                ```
        - [x] Tester que la page auth keycloak existe et est atteignable : 
        ```
        it('should assert the keycloak auth page exists and is running', async () => {
            
            try {
                const response = await axios.get('http://localhost:8080/realms/Cop_sass/protocol/openid-connect/auth?client_id=KMO_Predict&redirect_uri=http%3A%2F%2Flocalhost%3A3000%2Fadministration&state=5e86e6b7-de2f-4dc4-9f08-96d5894a259c&response_mode=fragment&response_type=code&scope=openid&nonce=3ffa96e7-1d84-48d1-8517-4fe7b4074843');
        
                expect(response.status).toBe(200);
            } catch (error) {
                console.error(error);
            }

        });
        ```
        - [ ] Tester que l'auth keycloak est validée avec les ids "g.chevalier" / "pw123"
        - [ ] Tester que l'user connecté a les bons rôles
        - [ ] Tester que la page existe
        - [ ] Tester que la page est accessible
        - [ ] Tester que le bouton existe
        - [ ] Tester que le bouton appelle la fonction désactivation
        - [ ] Tester que l'appel à la db se fait bien
        - [ ] Tester que la connexion à la db est établie
        - [ ] Tester que la désactivation retourne le bon store
- [x] Mail à Olivier ITS : 
```
Bonjour Olivier, 
 
J’ai une question concernant un point de configuration qui m’est obscur de keycloak ;
Je suis en train de réaliser une série de tests end-to-end et essaye donc d’atteindre en local les routes de mon application protégée par keycloak via mes fichiers de tests et des appels API.
Le fait est que mon app tourne comme attendu (par exemple http://localhost:3000/administration est accessible depuis un browser) mais mon framework de test et Postman me retournent une erreur 404 pour la même route.
Je pensais tomber sur une 302 confirmant l’existence de la page mais cette 404 me fait tourner en rond depuis un moment. Est-ce qu’il y a un paramètre à changer/ajouter dans la configuration du client keycloak pour autoriser les appels externes de type Postman ou dans mon cas, pour les tests e2e ?
```

**27 Avril**
- [x] Réponse Olivier : 
```
Je pense que ton application, est une "Single Page Application".
C'est pourquoi la seule requête qui peut répondre une 200 est http://localhost:3000.
Mettre un proxy devant (type nginx / apache2) permet de réécrire l'URL et te permettra d'avoir un code 200. Cependant, tu ne verras pas de redirection, car c'est le code javascript qui s'en charge.
```
- [ ] Poursuite Predict
    - [ ] KP-177
    - [ ] Écriture des tests
        - [ ] Tester que l'application angular existe & est définie
        - [ ] Tester que l'user est redirigé vers l'auth keycloak
            - [ ] Réussir à atteindre la bonne page de connexion
        - [ ] Tester que l'auth keycloak est validée avec les ids "g.chevalier" / "pw123"
        - [ ] Tester que l'user connecté a les bons rôles
        - [ ] Tester que la page existe
        - [ ] Tester que la page est accessible
        - [ ] Tester que le bouton existe
        - [ ] Tester que le bouton appelle la fonction désactivation
        - [ ] Tester que l'appel à la db se fait bien
        - [ ] Tester que la connexion à la db est établie
        - [ ] Tester que la désactivation retourne le bon store