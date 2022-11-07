*** LOG Gregoire Chevalier ***

**7 Novembre**
- [ ] Tests e2e
    - [ ] Premier test : it(/GET login`).
        - [x] Résolution de l'erreur :
        ```
        TypeError: this.userRepository.findOne is not a function
        ```
        -> Mise en commentaire de ```await app.init();``` dans le fichier de test e2e vu que l'application est déjà démarrée à un autre moment.
        - [x] Résolution de l'erreur :
        ```expected 200 "OK", got 404 "Not Found"```   
            - [x] Essai avec :
            ```
            return request(app.getHttpServer())
            .get('https://www.google.com/')
            .expect(200)
            ```
            Renvoie : connect ECONNREFUSED 127.0.0.1:80.  
            .get('localhost') renvoie la même erreur, mais .get('/localhost') & .get('/localhost:4200/#/login') renvoient une 404...
            - [x] Check de (https://stackoverflow.com/questions/66924663/how-to-fix-an-endpoint-test-that-returns-404-status-code-rather-than-200-using-e) & (https://stackoverflow.com/questions/56122778/supertest-not-found-error-testing-express-endpoint/57368925#57368925)
            - [x] Remplacement de la variable "co" écrite en dur dans TypeOrmModule.forRoot() par le modèle défini dans le fichier KMO_WEB\kmo-back\src\app.module.ts, ce qui renvoie un timeout avec l'erreur suivante : [Nest] 9848  - 07/11/2022 09:58:23   ERROR [TypeOrmModule] Unable to connect to the database. Retrying (1)...
            Error: ER_ACCESS_DENIED_ERROR: Access denied for user ''@'172.18.0.1' (using password: NO)
            - [x] Après résolution des problèmes de connexion, on retrouve la 404 de base
        - [x] Mise en commentaire  de la fonction de création de 1er user dans user.service.ts pour faire avancer les tests.  
        !! PENSER À LE DÉCOMMENTER AVANT DE COMMIT !!
        - [x] Les routes (endpoints) doivent aller taper dans le swagger, et non dans l'application front (swagger =/= localhost !!)
        - [x] Résolution de l'erreur : expected 200 "OK", got 500 "Internal Server Error"
            - [x] Check de (https://stackoverflow.com/questions/52840873/getting-500-internal-server-error-while-using-supertest). L'erreur viendrait du faity qu'on est pas authentifié : 
            ```
            [Nest] 27052  - 07/11/2022 11:15:46   ERROR [ExceptionsHandler] Unknown authentication strategy "jwt"
            Error: Unknown authentication strategy "jwt" 
            ```  
            - [x] Import de l'AuthModule qui renvoie l'erreur suivante :
            ```
            Nest can't resolve dependencies of the UsersService (UserRepository, JwtService, ?). Please make sure that the argument MailerService at index [2] is available in the AuthModule context.
            ```
            - [x] Ajout du MailerService et de son porvide: dans les providers[] -> Renvoie une 401
        - [ ] Résolution de l'erreur :  expected 200 "OK", got 401 "Unauthorized"
    - [ ] Tester le front
        - [ ] Tester le formulaire
        - [ ] Tester la réception des données
        - [ ] Tester les données retournées
        - [ ] Tester l’envoi des données retournées au back
    - [ ] Tester le back
        - [ ] Tester la bonne réception des données
        - [ ] Tester la connexion à la db
        - [ ] Tester la bonne forme des données à envoyer
        - [ ] Tester le crud des données en base
        - [ ] S’assurer de la persistance des données en base
- [ ] Utiliser jira en parallèle du log pour communiquer sur ce qui est fait
- [ ] Penser à donner un justificatif de nouveau domicile à hubert