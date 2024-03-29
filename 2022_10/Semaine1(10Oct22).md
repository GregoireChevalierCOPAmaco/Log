*** LOG Gregoire Chevalier ***

**10 Octobre**
- [x] Prise en main du matériel
    - [x] Changement du mot de pass utilisateur windows
    - [x] Installation du setup Git
        - [x] Installation de gitSK 2.38 64k
        - [x] Création du profil Github
        - [x] Création du repo log
        - [x] Premier push
    - [x] Installation de discord
        - [x] Création du compte discord
    - [x] Installation de Linux
        - [x] Check de (https://learn.microsoft.com/fr-fr/windows/wsl/install)
        - [x] Dans powershell admin, utilisation de la commande 
        ```
        wsl --install -d Ubuntu-20.04
        ```
        - [x] Paramétrage d'Ubuntu
            - [x] Unix username : gregoirechevaliercopamaco
            - [x] Unix pw : procédure habituelle
    - [x] Installation du plugin wsl dans vscode
    - [x] Installation de vscode dans linux WSL (automatique au lancement de wsl via vscode)
    - [x] Vérifier les accès à Github via linux WSL (ssh/?) (https ok)
        - [x] Configuration ubunut-git :  git config --global user.email "you@example.com" / git config --global user.name "Your Name"
    - [x] Installation de Postman
        - [x] Téléchargement
        - [x] Configuration
        - [x] Validation email
    - [x] Intégration au github copamaco
    - [x] Installation de node
        - [x] Check de (https://www.educative.io/answers/how-to-install-nodejs-on-ubuntu)
        - [x] Installation des paquets avec 
        ```
        sudo apt-get install curl
        curl -sL https://deb.nodesource.com/setup_16.17.1 | sudo -E bash -
        sudo apt-get install nodejs
        ```
        - [x] Résolution de l'erreur "E: Unable to locate package npm avec"
        ```
        apt-get update
        sudo apt-get install nodejs
        ```
    - [x] Installation de docker desktop
        - [x] Check de (https://docs.docker.com/desktop/windows/wsl/#:~:text=The%20docker%20CLI%20and%20UI,both%20v1%20or%20v2%20mode.)
        - [x] Téléchargement et installation du Linux kernel update package.
        - [x] Link de docker cli dans ubuntu : use wsl2 engine via docker desktop
    - [x] Installation de flux
- [x] Check de (https://www.docker.com/blog/announcing-docker-sbom-a-step-towards-more-visibility-into-docker-images/) Article docker sur la transparence des images : sbom
- [ ] Création d'un projet template docker pour push ssh
    - [x] Création du repo
    - [x] Création de clé ssh ubuntu
    - [x] Création de clé ssh windows
    - [ ] Début du projet docker
        - [x] Commande : 
        ```
        docker run -d -p 80:80 docker/getting-started
        ```
- [ ] Survol des projets en cours sur le github https://github.com/COP-AMACO
- [x] Veille techno dockerfile
    - [x] Check de (https://www.silicon.fr/31-bonnes-pratiques-securiser-conteneurs-docker-163501.html)

**11 Octobre**
- [x] Check du projet KMO_Web
    - [x] Check du projet avec Anthony
    - [x] Synthèse écrite
- [x] Installation de TypeScript 4.8.4
    - [x] Installation ubuntu wsl ```npm i typescript```
    - [x] Installation windows local
        - [x] Résolution de : "npm should be run outside of the Node.js REPL, in your normal shell." : installation depuis win + R/cmd
        - [x] Redémarrage pour ajout au PATH
- [ ] Immersion dans le projet KMO_Web
    - [x] Check du projet KMO_Web sur Github
        - [x] Check de la branche fix
        - [x] Check de la branche main
            - [x] Prise en compte du readme
        - [x] Check de la branche develop
            - [x] Le dossier .idea concerne les presets de l'ide IJ de jetbrains
        - [x] Pull en local de la branche develop sur ubuntu
            - [x] Le pull chope toutes les branches, utiliser 
            ```
            git checkout <existing_branch>
            ```
        - [x] Installations requises
            - [x] ```npm -i``` dans le dossier COP SAV
            - [ ] Résolution de l'erreur : 
            ```
            npm ERR! code 1
            npm ERR! path \\wsl.localhost\Ubuntu-20.04\home\gregoirechevaliercopamaco\hub\KMO_WEB\COP-SAV\node_modules\@angular\cli
            npm ERR! command failed
            npm ERR! command C:\WINDOWS\system32\cmd.exe /d /s /c C:\Users\GCHEVA~1\AppData\Local\Temp\postinstall-2572bf93.cmd
            npm ERR! '\\wsl.localhost\Ubuntu-20.04\home\gregoirechevaliercopamaco\hub\KMO_WEB\COP-SAV\node_modules\@angular\cli'
            npm ERR! CMD.EXE a ete demarre avec le chemin d'acces comme repertoire en
            npm ERR! cours. Les chemins d'acces UNC ne sont pas prise en charge. Utilisation
            npm ERR! du repertoire Windows par defaut.
            npm ERR! node:internal/modules/cjs/loader:959
            npm ERR!   throw err;
            npm ERR!   ^
            npm ERR!
            npm ERR! Error: Cannot find module 'C:\Windows\bin\postinstall\script.js'
            npm ERR!     at Function.Module._resolveFilename (node:internal/modules/cjs/loader:956:15)
            npm ERR!     at Function.Module._load (node:internal/modules/cjs/loader:804:27)
            npm ERR!     at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:81:12)
            npm ERR!     at node:internal/main/run_main_module:17:47 {
            npm ERR!   code: 'MODULE_NOT_FOUND',
            npm ERR!   requireStack: []
            npm ERR! }
            ```
        - [x] Clone en local de la branche develop sur windows
            - [x] Le pull chope toutes les branches, utiliser 
            ```
            git checkout <existing_branch>
            ```
        - [x] Installations requises
            - [x] ```npm -i``` dans le dossier COP SAV
            - [x] ```npm audit fix``` pour cleaner l'install puis --force
        - [x] Résolution de l'erreur : 
        ```
        ng : Le terme «ng» n'est pas reconnu comme nom d'applet de commande, fonction, fichier de script ou programme exécutable. Vérifiez l'orthographe du nom, ou si un chemin 
        d'accès existe, vérifiez que le chemin d'accès est correct et réessayez.
        ```
        - [x]  Installation de la consile angular sur windows 
        ```
        npm install -g @angular/cli
        ```
- [x] 1er lancement de serveur avec ```ng serve```
    - [x] App fonctionnelle à l'adresse : (http://localhost:4200/#/login)
    - [x] Trouver comment accéder à la base de données
        - [x] Lancer le service db
        - [x] Lancer le dockerfile avec compose
            - [x] Compose est installé (https://docs.docker.com/compose/install/)
            - [x] Positionnement dans le dossier /COP SAV/kmo-back
            - [x] Utilisation de 
            ```
            docker compose up
            ```
            - [x] Docker lancé, pulls effectués
        - [x] Accéder à la base de données
            - [x] Résolution de l'erreur
            ```
            Aborted connection 5 to db: 'unconnected' user: 'unauthenticated' host: '172.18.0.1' (This connection closed normally without authentication)
            ```
            - [x] Connexion à adminer (:8080) avec les ids présents dans le fichier /kmo-back/environements/mysql/local.env (serveur / user / pw / db)
    - [x] Générer un user test
        - [x] Récupération des users avec postman
            - [x] Configuration de la requête avec le token pour autoriser la connexion
                - [x] Trouver le token d'auth
                    - [x] Check de (https://swagger.io/docs/specification/authentication/bearer-authentication/)
                    - [x] Récupération des ids de l'user de base en dur dans /KMO_WEB\kmo-back\src\users\users.service.ts
                    - [x] lancement de la requete dans swagger user/login (id: admin.admin@admin.com pw: Admin123)
                    - [x] Token : 
                    ```
                    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6Ijk5ZmI0MmJhLTY4MGMtNGM1Ny05YTU2LThmNWY3ODU0ZjI2ZiIsImVtYWlsIjoiYWRtaW4uYWRtaW5AYWRtaW4uY29tIiwicm9sZSI6ImFkbWluIiwiZmlyc3RuYW1lIjoiYWRtaW4iLCJsYXN0bmFtZSI6ImFkbWluIiwiaWF0IjoxNjY1NDk2NDUyLCJleHAiOjE2NjU1OTE0NTJ9.lprur-fGrGAUUJ9wxpW0klL2P2wyho1TH_pMf06Y7Ao
                    ```
            - [x] Ajout du token dans postman : Authorization/type : BearerToken
        - [x] Création de la requête postman pour créer un nouvel user : 
        ```
        {
        "firstname": "firstnameTest",
        "lastname": "lastnameTest",
        "password": "pw123",
        "email": "test.test@test.com",
        "role": "technician",
        "phoneNumber": "1"
        }
        ```
        (suppression de la ligne vehicule qui rendait la requete incorrecte)
    - [ ] Recherche des controleurs et des pages dans le projet
    - [x] Découverte de l'application
    - [x] Survol de karma.conf.js
- [ ] Découverte de karma
    - [x] Check de (https://karma-runner.github.io/latest/index.html)
- [x] Check de (https://dev.to/i5han3/git-commit-message-convention-that-you-can-follow-1709)
- [x] Veille techno postman (https://learning.postman.com/docs/designing-and-developing-your-api/creating-an-api/)
- [ ] Se documenter / faire de la veille sur Angular
- [ ] Se documenter / faire de la veille sur Karma
- [ ] Se documenter / faire de la veille sur Jenkins
- [ ] Suivre la procédure suggérée au lancement du docker : 
```
PLEASE REMEMBER TO SET A PASSWORD FOR THE MariaDB root USER !
kmo-mysql    | To do so, start the server, then issue the following command:
kmo-mysql    | -
kmo-mysql    | '/usr/bin/mysql_secure_installation'
```  

**12 Octobre**
- [x] Démarrage des dockers
- [ ] Poursuite du survol du projet kmo web
    - [x] Trouver l'emplacement du testing de l'application
        - [x] Lancer les tests
            - [x] Positionnement dans le dossier ./COP-SAV, et lancement des commandes :
            ```
            ng test
            ng e2e
            ```
            - [ ] Étude des messages d'erreur :
            ```
            Error: src/app/pages/dashboard-ass/list-ticket/modif-ticket/modif-ticket.component.spec.ts:23:23 - error TS2339: Property 'toBeTruthy' does not exist on type 'void'.

            23     expect(component).toBeTruthy();
                                    ~~~~~~~~~~


            Error: src/app/shared/directives/sortable-table.directive.spec.ts:5:23 - error TS2554: Expected 2 arguments, but got 0.        

            5     const directive = new SortableTableDirective();
                                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~

            src/app/shared/directives/sortable-table.directive.ts:47:15
                47   constructor(private renderer: Renderer2, private targetElem: ElementRef) { }
                                ~~~~~~~~~~~~~~~~~~~~~~~~~~~
                An argument for 'renderer' was not provided.


            Error: src/app/shared/pipes/filter-store.pipe.spec.ts:5:18 - error TS2554: Expected 1 arguments, but got 0.

            5     const pipe = new FilterStorePipe();
                            ~~~~~~~~~~~~~~~~~~~~~

            src/app/shared/pipes/filter-store.pipe.ts:13:15
                13   constructor(public toastService:ToastService) {
                                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            An argument for 'toastService' was not provided.
            ```
            Et dans le navigateur généré par karma :
            ```
            Incomplete: No specs found, , randomized with seed 10764
            ```
            - [x] Check de (https://github.com/jasmine/jasmine/blob/4097718b6682f643833f5435b63e4f590f22919f/lib/jasmine-core/jasmine.js#L2908) pour comprendre ce qu'est toBeTruthy()
    - [x] Comprendre le fonctionnement des tests
        - [x] Check du fichier testing.d.ts dans le dossier ./COP-SAV/node-modules/@angular/core/testing
        - [x] Check des fichiers de tests dans les dossiers contenus dans list-tickets 
    - [x] Questions à poser concernant les tests :
        - Nomenclature des noms de fichiers de tests ?
        - Apparemment les fichiers de tests finissent par ```.spec.ts``` et sont liés au fichier du même nom qui finit juste en ```.ts``` ?
        - [x]  
        !!!   
        Les tests ne sont pas encore faits. Les fichiers déjà créés sont générés automatiquement par Angular. Le moteur de test n'est pas encore défini. Si l'instance de test Karma renvoie une page OK avec aucun tests,c 'est parce qu'il n'y a effectivement pas encore de tests  
        !!!
    - [x] Étude précise de la création d'utilisateur via fichier ```user.component.ts``` dans le dossier ./COP-SAV/src/app/pages/cop/user
        - [x] Étude des fichiers html, css et de tests pour le component user
        - [x] Check des fichiers interfaces relatifs aux components : IUSer & IStore, dans le dossier ./COP-SAV/src/app/core/interfaces
    - [x] Examen de la structure générale du projet (voir schema papier)
    - [x] Trouver les modules manquants (@Nestjs/xxx)
    ```
    Cannot find module '@nestjs/testing' or its corresponding type declarations.
    ```  
    - [x] Utilisation des commandes suivantes dans le dossier kmo back
    ```
    npm install @nestjs/common
    npm install @nestjs/core
    npm install @nestjs/testing
    npm audit fix --force
    ```
    - [x] Trouver l'emplacement de l'alias @nest mentionnés dans les fichiers de kmo_back -> dossier /node_modules
    - [x] Lecture de l'introduction à Nestjs
    - [ ] Passer au travers du pipeline de création de ticket
    - [x] Trouver le document / planning : pas de planning ou de trello, mais un jira en développement
    - [x] Trouver les informations sur le ci cd
        - [x] Pas de ci cd
    - [x] Trouver les informations sur le déploiement : actuellement avec amazon lightsail, le but étant de passer sur S3 pour l'image, et EC2 (= vps) pour pouvoir déployer depuis docker
- [x] Décalage des intentions de veille technologique dans un fichier dédié
- [x] Check de ce qu'est un polyfill (https://developer.mozilla.org/en-US/docs/Glossary/Polyfill#:~:text=A%20polyfill%20is%20a%20piece,do%20not%20natively%20support%20it.)
- [x] Check de ce qu'est un lifecycle hook d'Angular (https://angular.io/guide/lifecycle-hooks)
- [x] Check de ce qu'est la programmation fonctionnelle : FP & a programmation fonctionnelle réactive : FRP 
(https://fr.wikipedia.org/wiki/Programmation_fonctionnelle)
- [ ] Grosse lecture (https://www.typescriptlang.org/docs/handbook/intro.html) et (https://www.typescriptlang.org/assets/typescript-handbook.pdf)
- [ ] Définir les contraintes de test
- [ ] Rechercher le framework de test le plus adapté aux problématiques du projet  
  
**13 Octobre**
- [ ] Poursuite de l'intégration au projet kmo web
    - [x] Créer un component test sur Angular
        - [x] Création de la branche testGreg dans le git, et positionnement dessus, puis commit initial.
        ```
        git branch testGreg
        git checkout testGreg
        git add .
        git commit -m "<message>"
        ```  
        - [x] Installation des dépendances nécessaires à la résolution des problèmes affichés dans la console pour kmo-app
        ```
        cd ..
        cd kmo-app
        npm i
        npm i @angular/platform-browser
        npm audit fix --force
        npm i jimp
        ```
        - [x] Commande passée : 
        ```
        ng g component services/TestService
        ```  
        Retour dans le terminal : 
        ```
        CREATE src/app/services/test-service/test-service.component.html (31 bytes)
        CREATE src/app/services/test-service/test-service.component.spec.ts (732 bytes)
        CREATE src/app/services/test-service/test-service.component.ts (291 bytes)
        CREATE src/app/services/test-service/test-service.component.scss (0 bytes)
        ```  
        Le sous dossier, et les fichiers créés n'ont pas la nomenclature voulue.
        - [x] Suppression du component et de ses dépendances via le delete du sous dossier créé. A priori et selon (https://stackoverflow.com/questions/41354755/what-is-the-best-way-to-delete-a-component-with-cli), tout ce qui a besoin d'être supprimé est dans le dossier.
        - [x] Nouvel essai avec ajout de flag --module=app.module : 
        ```
        ng g component services/test --module=app.module
        ```
        Retour terminal : File app.module does not exist.
        - [x] Check de (https://angular.io/guide/styleguide) pour la nomenclature
        - [x] Création d'un dossier TestService dans ./kmo-app/src/app/services en même temps que le service en question :
        ```
        ng generate service services/TestServices/test
        ```
        - [x] Suppression du service créé, dossier créé
        - [x] Recréer le service via ionic
            - [x] Résoudre le problème :
            ``` 
            ionic : Le terme «ionic» n'est pas reconnu comme nom d'applet de commande, fonction, fichier de script ou programme exécutable. Vérifiez 
            l'orthographe du nom, ou si un chemin d'accès existe, vérifiez que le chemin d'accès est correct et réessayez.
            ``` 
            - [x] Intégration de ionic au PATH et redémarrage
            - [x] installation de 
            ```
            npm i @ionic/cli
            npm i -g @ionic/cli
            ```
            Cette dernière commande résolvant le problème
            - [x] Envoi de la commande 
            ```
            ionic generate service services/TestServices/test
            ```  
            Et création du service comme attendu.
        - [x] Recréer le service avec class et controller associés
            - [x] Envoi de la commande 
            ```
            ionic generate service services/TestServices/test
            ```  
            - [x] Compréhension de ce qu'est un controller dans le carde d'angular et ocmment on le créée / l'assigne.
            - [x] Check de (https://docs.angularjs.org/guide/controller)
            - [x] Arrêt des essais sur ionic (kmo-app : ionic) et passage sur essais cop sav (angular)
            - [x] Création d'un component page test sur angular
                - [x] Positionnement dans le dossier : KMO_WEB\COP-SAV\src\app>
                - [x] Commande passée : 
                ```
                ng g component pages/testGreg/testgreg
                ```  
                Retour dans le terminal : 
                ```
                CREATE src/app/pages/testGreg/testgreg/testgreg.component.html (23 bytes)
                CREATE src/app/pages/testGreg/testgreg/testgreg.component.spec.ts (640 bytes)
                CREATE src/app/pages/testGreg/testgreg/testgreg.component.ts (283 bytes)
                CREATE src/app/pages/testGreg/testgreg/testgreg.component.css (0 bytes)
                UPDATE src/app/app.module.ts (6669 bytes)
                ```  
                - [x] Lancement du serveur pour constater le résultat
            - [x] Comprendre pourquoi l'affichage de création de tickets s'affiche au lieu du visuel template de la page.
                - [x] Par défaut, la page /dashboard-ass est affichée lorsqu'une url n'est pas trouvée
                - [x] Trouver le code responsable :
                ```
                {path: '**', component: DashboardAssComponent}
                ```
                Dans le fichier app_routing_modules.ts dans le dossier /src/app/
            - [x] Ajout de la route dans le routing ici : app_routing_modules.ts 
            ```
            import { TestGregComponent } from "./pages/test-greg/test-greg.component";

            const routes: Routes = [
            {path: 'test-greg', component: TestGregComponent}
            ]
            ```
            - [x] Route et page fonctionnelles (https://angular.io/tutorial/toh-pt5)
            - [ ] Ajouter un formulaire simple pour dialogue avec nest
                - [x] Trouver les composants form existants dans le projet
                - [x] Check de (https://angular.io/guide/dynamic-form)
                - [ ] Création d'un form 
                    - [x] Ajout d'import nécessaire au form dans test-greg.component.ts :
                    ```
                    import { Component, HostListener, OnInit } from '@angular/core';
                    import {FormBuilder, FormGroup} from "@angular/forms";
                    ```
                    - [x] Déclaration du constructeur et du form, changement de : 
                    ```
                    export class TestGregComponent implements OnInit {

                    constructor() { }

                    ngOnInit(): void {
                    }

                    }
                    ``` 
                    à : 
                    ```
                      constructor(private fb: FormBuilder) {
                    }

                    testForm!: FormGroup;

                    ngOnInit(): void {
                        this.testForm = this.fb.group({
                        nom: [''],
                        prenom: [''],
                        })
                    }

                    test() {
                        this.testService.test(this.testForm.value.email, this.testForm.value.password)

                    }
                    @HostListener('keydown.enter',['$event'])
                    loginEnventEnter(event:KeyboardEvent){

                        this.testService.test(this.testForm.value.email, this.testForm.value.password)

                    }
                    ```
                    Cela génère l erreur relative à l'absence de testService.
                    - [x] Création du testService en se calquant sur le loginService
                    - [x] Déclaration de l'import du service dans le component :
                    ```
                    import { TestService } from "../../core/services/TestService/test.service";
                    ```
                    - [x] Remplissage du testService
                - [x] Faire une présentation simple avec tailwind
    - [x] Reprise de lecture de doc (https://angular.io/guide/form-validation)
    - [x] Reprise de lecture de doc (https://docs.nestjs.com/first-steps#platform & onwards)
    - [x] Doc sur les decorators de Nest
    - [ ] Doc sur les controllers
- [ ] Poursuite lecture (https://www.typescriptlang.org/assets/typescript-handbook.pdf)
  
**14 Octobre**
- [ ] Poursuite de l'intégration au projet kmo web
    - [x] Angular & Nest interagissent-ils ensemble, et si oui comment ? Est-ce que je peux récupérer les données postées du form angular avec Nest et les faire persister en bdd ? -> Oui : Nest peut récupérer un objet json.
    - [x] S'assurer de la validité de l'objet retourné par le post du form testgreg
        - [x] L'objet retourné est : 
        ```
        {nom: undefined, prenom: undefined}
        nom
        : 
        undefined
        prenom
        : 
        undefined
        ```
        - [x] Trouver pourquoi les paramètres retournés sont undefined
            - [x] Check de (https://stackoverflow.com/questions/47025468/angular-form-input-value-undefined)
            - [x] Check de (https://angular.io/guide/form-validation)
            - [x] Reprise de (https://angular.io/guide/forms-overview)
            - [x] Overview du code du component testgreg
            - [x] Changement de 
            ```
              test() {
                    this.testService.test(this.testForm.value.email, this.testForm.value.password)
                    console.log(this.testForm.value.email)
                }
                @HostListener('keydown.enter',['$event'])
                testEnventEnter(event:KeyboardEvent){

                    this.testService.test(this.testForm.value.email, this.testForm.value.password)

                }
            ```
            aux valeurs définies dans le form à savoir nom & prénom :
            ```
              test() {
                this.testService.test(this.testForm.value.nom, this.testForm.value.prenom)
                console.log(this.testForm.value.nom)
            }
            @HostListener('keydown.enter',['$event'])
            testEnventEnter(event:KeyboardEvent){

                this.testService.test(this.testForm.value.nom, this.testForm.value.prenom)

            }
            ```  
            Boulet, va !
        - [x] Résoudre le problème et faire retourner les valeurs correctes
    - [ ] Reprise de doc sur Nest, controllers
    - [ ] Créer une interaction avec le component test via Nest
        - [x] Check de (https://www.codemag.com/Article/2005051/NestJS-Step-by-Step-Connecting-NestJS-with-Angular-Part-4)
        - [ ] Aller voir le code kmo-back
            - [ ] Si je génère une entity (?) avec nest, est-ce que ça créée aussi la route et les interactions swagger associées ?
            - [x] Lancement de ```npm run start:dev```, échec et retour d'erreur :
            ```
            > kmo@0.0.1 start:dev
            > NODE_ENV=dev nest start --watch

            'NODE_ENV' n’est pas reconnu en tant que commande interne
            ou externe, un programme exécutable ou un fichier de commandes.
            ```
            - [x] Lancement de ```npm run start```, échec et retour d'erreur :
            ```
            > kmo@0.0.1 start
            at Module.require (node:internal/modules/cjs/loader:1028:19)
            at require (node:internal/modules/cjs/helpers:102:18)
            at Object.<anonymous> (C:\Users\gchevalier\KMO_WEB\kmo-back\node_modules\@nestjs\swagger\dist\swagger-explorer.js:8:30)
            at Module._compile (node:internal/modules/cjs/loader:1126:14)
            at Object.Module._extensions..js (node:internal/modules/cjs/loader:1180:10)
            at Module.load (node:internal/modules/cjs/loader:1004:32)
            at Function.Module._load (node:internal/modules/cjs/loader:839:12)
            at Module.require (node:internal/modules/cjs/loader:1028:19)
            ```
            - [x] Check de (https://stackoverflow.com/questions/58437564/npm-run-startdev-cannot-find-module) & (https://stackoverflow.com/questions/57944941/why-nest-js-throw-a-error-when-run-main-js/57964735#57964735)
        - [ ] Tentative de lancement de nest avec 
        ```
        npm run start
        ```  
        - [x] Dans le dossier ./kmo-back, lancement de commande :  
            ```
            npm i
            ```
            Retour d'erreurs : 
            ```
            npm ERR! code ERESOLVE
            npm ERR! ERESOLVE could not resolve
            npm ERR!
            npm ERR! While resolving: @nestjs/core@7.5.5
            npm ERR! Found: @nestjs/common@8.4.7
            ...
            ...
            npm ERR!     node_modules/@nestjs-modules/mailer
            npm ERR!       @nestjs-modules/mailer@"^1.6.1" from the root project
            npm ERR!     4 more (@nestjs/platform-express, @nestjs/swagger, ...)
            ```
            - [x] Check de (https://gist.github.com/caisback/acf7e31561fbb67d8d598f345daeb47e)
            - [x] Lancement des commandes :
            ```
            npm install -g @nestjs/cli npm-check-updates
            nest update --force
            npm-check-updates "/@nestjs*/" -u
            npm install --legacy-peer-deps (pour typeorm qui faisait chier)
            npm-check-updates "/nestjs*/" -u 
            npm run start
            ```
            Démarrage ok mais erreur de co à la db :
            ```
            [Nest] 4580  - 14/10/2022 13:57:50   ERROR [TypeOrmModule] Unable to connect to the database.
            Error: Access denied for user ''@'172.18.0.1' (using password: NO)
            ```
            - [x] Déconnexion d'adminer avec les ids de kmo front
            - [x] Connexion à adminer avec les ids du fichier ./environments/mysql/local.env
            - [x] Utilisation de la commande ```NODE_ENV=local```. Retour d'erreur : 
            ```
            NODE_ENV=local : Le terme «NODE_ENV=local» n'est pas reconnu comme nom d'applet de commande
            ```
            - [x] Check de (https://stackoverflow.com/questions/11928013/node-env-is-not-recognized-as-an-internal-or-external-command-operable-comman)
            - [x] Lancement de ```npm run start:local```
            Démarrage ok mais erreur de co à la db :
            ```
            [Nest] 3896 
             Retrying (1)...
            Error: getaddrinfo ENOTFOUND KMO-mysql
            ```
            **Pas la peine de lancer Nest vu qu'il est lancé (API) depuis le docker**  
            **Nest == kmo-api == API == Swagger :3000**
        - [x] Se documenter sur TypeORM 
            - [x] Check de (https://docs.nestjs.com/techniques/database), (https://typeorm.io/data-source-options) & (https://typeorm.io/entities)
        - [x] Gérer l'interaction form data/api
            - [x] Envoyer les données du formulaires 
            - [x] Création d'un fichier interface ./src/app/core/interfacese/ITestGreg.ts
            - [x] Liens réalisés dans les fichiers correspondants pour matcher l'interface
    - [ ] Faire persister le résultat en bdd
        - [ ] Créer un endpoint dans l'api et le paramétrer pour accepter le retour fourni
- [ ] Voir les interactions avec les services aws
    - [ ] S3
    - [ ] LightSail
    - [ ] EC2
- [ ] Testing du projet
    - [ ] Aller voir (https://ionicframework.com/docs/angular/testing#general-testing-structure)
    - [ ] Définir les contraintes de test
    - [ ] Rechercher le framework de test le plus adapté aux problématiques du projet  
    - [ ] Réaliser des essais de tests

