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
- [ ] Recherches sur atteindre les SPAs
    - [x] (https://oliverjam.es/articles/avoid-spa-404#:~:text=This%20is%20because%20your%20server,correctly%20responds%20with%20a%20404.) suggests to point everything towards the one rendered page (http://localhost:3000) and see from there
    - [x] (https://thegray.company/blog/single-page-application-spas-404s-seo) explique que (http://localhost:3000) render la première page de la SPA, à savoir main.ts (?) et que c'est l'application qui build le routing et le reste
    - [x] Réponse GPT : 
    ```
    Single page applications (SPAs) built with frameworks like Angular use client-side routing to manage application state and navigation, which means that instead of loading new pages from the server, they dynamically update the content displayed on the same page based on user interactions.

    When you access an SPA through a web browser, the browser's JavaScript engine handles client-side routing, allowing you to navigate between different views or pages without triggering a full page reload. 
    However, when you call an SPA API endpoint with Postman, you are sending a request directly to the server, bypassing the client-side routing mechanism. 
    This can cause the server to return a 404 error because it doesn't recognize the requested endpoint.

    To resolve this issue, you need to configure your server to serve the SPA's index.html file for all routes, including those that would normally trigger a 404 error. 
    This is known as "fallback routing" and can be implemented using server-side routing rules or by using a service like ngrok to tunnel your local server to a public URL that can be accessed by Postman.

    In Angular, you can enable fallback routing by configuring the RouterModule with the useHash option, which adds a hash fragment to the URL and allows the server to serve the index.html file for all routes:

    javascript
    Copy code
    @NgModule({
    imports: [RouterModule.forRoot(routes, { useHash: true })],
    exports: [RouterModule]
    })
    export class AppRoutingModule { }
    With this configuration, all requests to your server will be redirected to the index.html file, allowing your SPA to handle the routing on the client-side.
    ```
    &&
    ```
    Since your Angular application uses client-side routing, the server is not aware of the client-side routes and will return a 404 error for any route that is not explicitly handled on the server-side. To handle this situation, you need to configure your server to serve the index.html file for all routes and let your Angular application handle the routing on the client-side.

    In your NestJS backend, you can create a fallback route to handle all requests that are not handled by the server and serve the index.html file. You can add the following code to your app.module.ts file:

    typescript
    Copy code
    import { join } from 'path';

    @Module({
    imports: [
        ServeStaticModule.forRoot({
        rootPath: join(__dirname, '..', 'front-end'),
        exclude: ['/api*'],
        }),
        AngularUniversalModule.forRoot({
        bootstrap: AppServerModule,
        viewsPath: join(process.cwd(), 'dist/front-end/browser'),
        }),
        ...
    ],
    controllers: [AppController],
    providers: [AppService],
    })
    export class AppModule {
    configure(consumer: MiddlewareConsumer) {
        consumer.apply((req, res, next) => {
        res.sendFile(join(process.cwd(), 'dist/front-end/browser/index.html'));
        }).forRoutes({
        path: '/*',
        method: RequestMethod.GET
        });
    }
    }
    Here, we use the ServeStaticModule from @nestjs/serve-static to serve the static files for the Angular application from the front-end directory. 
    We exclude the /api* route from being handled by the static file server. 
    Then we use the AngularUniversalModule from @nestjs/ng-universal to serve the Angular application from the dist/front-end/browser directory.

    Finally, we create a fallback route using the configure method of the AppModule to handle all requests that are not handled by the server and serve the index.html file. 
    This will ensure that all client-side routes are handled by the Angular application and prevent 404 errors from being returned by the server.

    After adding this configuration, you should be able to call GET/ http://localhost:3000/administration in Postman and receive a 200 code.
    ```
    - [x] Installation de ```npm install --save @nestjs/serve-static @nestjs/ng-universal @nguniversal/express-engine
 --legacy-peer-deps```
    - [x] Ajout de :
    ```
        ServeStaticModule.forRoot({
      rootPath: join(__dirname, '../..', 'front-end'),
      exclude: ['/api*'],
    }),
    ```
    dans le app.module.ts de nestJs
    - [x] Rebuild du docker avec ```docker compose --env-file .env.dev -f docker-compose.dev.yml up --renew-anon-volumes --always-recreate-deps --build``` et toujours la 404 sur postman
    - [x] Précisions de GPT : 
    ```
    When you navigate to a route in your Angular application, the client-side code in the browser sends an HTTP GET request to the server to retrieve the page content. The server then responds with the HTML, CSS, and JavaScript files required to render the page in the browser.

    When you try to access the same route using Postman, it will send a GET request to the server, but it will not be able to render the page in the same way that the client-side code in the browser can. Instead, Postman will receive the raw HTML, CSS, and JavaScript files that are returned by the server, which may not be what you were expecting.

    If you want to test your Angular routes, you can use a browser like Google Chrome, Firefox or Safari to navigate to the desired route, or you can use a tool like cURL to make HTTP requests from the command line.
    ```En gros : on ne peut pas accéder à angular depuis postman, juste vérifier que l'app tourne sur le port choisi.
- [ ] Poursuite Predict
    - [ ] KP-177
    - [ ] Écriture des tests
        - [x] Tester que l'application angular existe & est définie : on teste si http://localhost:3000/
        - [x] Tester que l'user est redirigé vers l'auth keycloak
            - [ ] Réussir à atteindre la bonne page de connexion
        - [ ] Tester que l'auth keycloak est validée avec les ids "g.chevalier" / "pw123"
        - [ ] Tester que l'user connecté a les bons rôles
        - [ ] Tester que la page existe 
        - [ ] Tester que la page est accessible
            - [x] Essai avec jest & jsdom : 
            ```
            import { JSDOM } from 'jsdom';

            describe('Angular app (e2e)', () => {
            let dom: any;

            beforeAll(async () => {
                // Create a new JSDOM instance with the Angular app URL
                dom = await JSDOM.fromURL('http://localhost:3000');
            });

            afterAll(() => {
                // Clean up the JSDOM instance after all tests have finished
                dom.window.close();
            });

            it('should load the administration page', async () => {
                // Navigate to the administration page and wait for it to load
                await dom.window.location.assign('http://localhost:3000/administration');
                await new Promise(resolve => {
                dom.window.addEventListener('load', resolve);
                });

                // Check if the desired element exists on the page
                const element = dom.window.document.querySelector('app-administration');
                expect(element).not.toBeNull();
                });
            });
            ```
            mais erreur  Error: Not implemented: navigation (except hash changes)
            - [x] Essai avec puppeteer : 
                - [x] Installation de puppeteer ```npm install --save-dev @nestjs/testing puppeteer --legacy-peer-deps```
            ```
            import * as puppeteer from 'puppeteer';

            describe('AppController (e2e)', () => {
            let browser: puppeteer.Browser;
            let page: puppeteer.Page;

            beforeAll(async () => {
                // Launch a headless browser
                browser = await puppeteer.launch();
            });

            beforeEach(async () => {
                // Open a new page in the browser and navigate to your Angular app
                page = await browser.newPage();
                await page.goto('http://localhost:3000');
            });

            afterEach(async () => {
                // Close the current page after each test
                await page.close();
            });

            afterAll(async () => {
                // Close the browser after all tests have finished
                await browser.close();
            });

            it('should load the administration page', async () => {
                // Navigate to the administration page and wait for it to load
                await page.goto('http://localhost:3000/administration');
                await page.waitForSelector('#admin-page');

                // Check if the desired element exists on the page
                const element = await page.$('#admin-page');
                expect(element).not.toBeNull();
                });
            });
            ```
            timeouts
            logs keycloak
            puppeteer keycloak
            puppeteer keycloak form submit
            puppeteer keycloak submit incorrect timeout
                -> séparer en plusieurs fichiers de tests bien nommés
            séparation en fichiers car un seul fichier multitests plus lourd. (?)
        - [ ] Tester que le bouton existe
        - [ ] Tester que le bouton appelle la fonction désactivation
        - [ ] Tester que l'appel à la db se fait bien
        - [ ] Tester que la connexion à la db est établie
        - [ ] Tester que la désactivation retourne le bon store