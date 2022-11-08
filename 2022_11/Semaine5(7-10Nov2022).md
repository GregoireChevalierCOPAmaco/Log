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
            - [x] Ajout après la ligne .get(/reports) de : .set('Authorization', `Bearer .... + token), qui renvoie une erreur 500 due à : ERROR [ExceptionsHandler] this.reportService.findReportByGroup is not a function
            - [x] Essai avec un ```.overrideGuard(AuthGuard())```, sans succès
            - [x] Changement de la ligne en supprimant Bearer :
            ```
            .set('Authorization', `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6Ijk5ZmI0MmJhLTY4MGMtNGM1Ny05YTU2LThmNWY3ODU0ZjI2ZiIsImVtYWlsIjoiYWRtaW4uYWRtaW5AYWRtaW4uY29tIiwicm9sZSI6ImFkbWluIiwiZmlyc3RuYW1lIjoiYWRtaW4iLCJsYXN0bmFtZSI6ImFkbWluIiwiaWF0IjoxNjY1NDk2NDUyLCJleHAiOjE2NjU1OTE0NTJ9.lprur-fGrGAUUJ9wxpW0klL2P2wyho1TH_pMf06Y7Ao`)
            ```
            Ceci renvoie une erreur 500 due à :  ERROR [ExceptionsHandler] Cannot read properties of null (reading 'role'). Avec la stack commençant par : 
            ```
             at TechnicianJwtGuard.handleRequest (C:\Users\gchevalier\cleankmo\KMO_WEB\kmo-back\src\authentification\guards\technician-jwt-guard.ts:43:11)
            at C:\Users\gchevalier\cleankmo\KMO_WEB\kmo-back\node_modules\@nestjs\passport\dist\auth.guard.js:49:128
            ```
            - [x] Survol des fichiers auth.guard.ts & technician-jwt-guard.ts : Apparemment, le token est décodé pour trouver Id & Role, et il manquerait role dans le token fourni...
            - [x] Changement de la ligne en remettant Bearer comme dans postman :
            ```
            .set('Authorization', `Bearer eyJhbGciOiJIUzI1NiIsInR...`)
            ```
            Ceci a pour effet de renvoyer l'erreur : TypeError: this.userRepository.findOne is not a function depuis le users.service.ts  
            L'erreur se recoupe avec celle de la création du 1er user. Essayer de rcupérer en réel les données
        - [x] Tentative de récupérer les données en réel avec dbco : se solde par un echec avec l'erreur :
        ```
        AlreadyHasActiveConnectionError: Cannot create a new connection named "default", because connection with such name already exist and it now has an active connection session.
        ```
        - [ ] Résolution de ERROR [ExceptionsHandler] this.reportService.findReportByGroup is not a function depuis le fichier reports.controller
  

**8 Novembre**
- [x] Refacto du test avec de la response en await, pour le même résultat
- [x] Check de (https://medium.com/@exfabrica/nestjs-unit-and-e2e-tests-with-jest-825ba5033c6), (https://www.pluralsight.com/guides/test-asynchronous-code-jest)
- [x] Après modification du fichier reports.controller.ts de :
```
	@Get()
	@UseGuards(TechnicianJwtGuard)
	findByGroup() {
		return this.reportService.findReportByGroup();
	}
```
en : 
```
	@Get()
	@UseGuards(TechnicianJwtGuard)
	findByGroup() {
		async () => {
			await this.reportService.findReportByGroup();
		}
	}
```
On obtient un status code 200, mais toujours avec l'erreur TypeError  this.userRepository.findOne is not a function
- [ ] Application de la méthode trouvée sur (https://github.com/nestjs/typeorm/issues/405) : Rajouter une connection dans le constructor du users.service
    - [ ] Résolution du problème de gestion de dépendance : Nest can't resolve dependencies of the UsersService. Please make sure that the argument Connection is available
- [ ] Passage sur le reste de l'end-to-end
    - [ ] Documentation de la méthode à suivre
- [ ] Tests e2e
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