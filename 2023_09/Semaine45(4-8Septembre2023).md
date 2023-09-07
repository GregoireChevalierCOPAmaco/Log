**4 Septembre**
- [x] Check des mails
- [ ] Reprise du tableau des tests fonctionnels 
    - [ ] Reprise des tests de l'application
    - [ ] Ajout de ticket Jira pour chaque issue
- [ ] Plonger dans le code relatif au websocket
- [ ] Sécuriser l'API
    - [ ] Chercher de la documentation sur le sujet
        - [x] Lecture de doc sur ce qu'est une API REST (https://www.geeksforgeeks.org/rest-api-introduction/)
        - [ ] Comment sécuriser une api nest (https://medium.com/@chrisbalola/building-a-secure-and-quality-nodejs-rest-api-with-nestjs-34a9aef633e0)
- [x] Aide à Jacques à se connecter au serveur via SSH & à la DB via PGAdmin
- [ ] Discuter avec Anthony de la durée de persistance des events en base.


**5 Septembre**
- [ ] Check des mails
- [ ] Plonger dans le code relatif au websocket
- [ ] Sécuriser l'API
    - [ ] Chercher de la documentation sur le sujet
        - [ ] Check de (https://medium.com/devops-dudes/secure-nestjs-rest-api-with-keycloak-745ef32a2370)
        - [ ] Check de (https://itnext.io/protecting-your-nestjs-api-with-keycloak-8236e0998233)
        - [x] Check de chat gpt, sa réponse : 
        ```
        - Secure API endpoints
            - Modifier l'auth.guard du front
            - Créer un middleware NestJs qui récupère les rôles KC depuis le front
            - Ajouter un check sur les rôles aux controllers back
        - Configure security headers
        ```
    - [ ] Sécurisation des endpoints
        - [x] Modification de l'auth.guard (front), ajout de : 
        ```
        import { HttpHeaders, HttpRequest } from '@angular/common/http';

        async isAccessAllowed(route: ActivatedRouteSnapshot): Promise<boolean> {
        // ... (existing code)

        if (granted) {
            // Add user roles to the HTTP request headers
            const headers = new HttpHeaders({
            'X-User-Roles': userRoles.join(', '), // Send roles as a comma-separated string
            });

            // Update the request headers
            this.keycloakAngular.addTokenToHeader(headers);

            return true;
            }
        }
        ```
        qui a pour but d'envoyer à nest les rôles du user *via* la requête http en l'incluant dedans
        - [x] Check que ça n'ait rien cassé
            - Kill des containers
            - Rebuild
            - Réimport du realm
            - Réimport du sql
            Résultat : 
            ```
            Compiled with problems:X

            ERROR in ./src/styles.scss?ngGlobalStyle (./src/styles.scss.webpack[javascript/auto]!=!../../node_modules/css-loader/dist/cjs.js??ruleSet[1].rules[6].rules[0].oneOf[0].use[1]!../../node_modules/postcss-loader/dist/cjs.js??ruleSet[1].rules[6].rules[0].oneOf[0].use[2]!../../node_modules/resolve-url-loader/index.js??ruleSet[1].rules[6].rules[1].use[0]!../../node_modules/sass-loader/dist/cjs.js??ruleSet[1].rules[6].rules[1].use[1]!./src/styles.scss?ngGlobalStyle)

            Module build failed (from ../../node_modules/postcss-loader/dist/cjs.js):
            Error: ENOMEM: not enough memory, scandir '/app/apps/kmo-predict-front/src'

            ERROR in ./src/styles.scss?ngGlobalStyle
            ```
            Les erreurs n'arrivent que quand la db est vide. Sinon, OK
        - [x] Création du middleware dans nest : 
        ```
        import { Injectable, NestMiddleware } from '@nestjs/common';
        import { Request, Response, NextFunction } from 'express';

        @Injectable()
        export class UserRolesMiddleware implements NestMiddleware {
        use(req: Request, res: Response, next: NextFunction) {
            const userRolesHeader = req.header('X-User-Roles');
            if (userRolesHeader) {
            const userRoles = userRolesHeader.split(',').map(role => role.trim());
            req.userRoles = userRoles;
            }
            next();
        }
        }
        ```
        mais cela suscite une erreur : Property 'userRoles' does not exist on type 'Request<ParamsDictionary, any, any, ParsedQs, Record<string, any>>'.
- [ ] Discuter avec Anthony de la durée de persistance des events en base.
- [ ] Reprise du tableau des tests fonctionnels 
    - [ ] Reprise des tests de l'application
        - [x] Duplication de magasin
        - [x] Réassignation de magasin
        - [x] Affichage des gateways dans la carte de /cop
        - [x] Affichage des caisses dans la carte /cop
        - [x] Changer l'état d'une caisse depuis l'interface /magasin:dialog
        - [x] N'afficher dans les dropdowns que les gateways non assignées ?
        - [x] Afficher les caisses d'un magasin dynamiquement
        - [x] Afficher les informations de caisse d'un magasin dynamiquement
    - [ ] Ajout de ticket Jira pour chaque issue
        - [x] Ajout de KP-457 : N'afficher que les gateways dispon
        - [x] Ajout de KP-458 : Appliquer les changements d'états de caisses
        - [x] Ajout de KP-459 : Affiner l'affichage des caisses dans la carte de la page /cop
        - [x] Ajout de KP-460 : Afficher dynamiquement les informations de caisse dans la boîte de dialogue
        

**6 Septembre**
- [ ] Check des mails
- [ ] Faire l'affichage des gateways dans la carte de la page /cop (KP-425)
    - [ ] Création de branche et switch dessus
    - [ ] Git pull
    - [ ] Trouver où et comment récupérer les données db de gateways
    - [x] Voir avec Guillaume pour savoir comment connecter une gateway
        - [x] Branchement d'une gateway en réseau eth & prise courant
        - [ ] Voir avec Jacques pour les installs
    - [x] Lier les données à l'affichage de la carte
        - [x] Trouver le code relatif à la carte : fichier cop-store-details.component
        - [x] Lier les données
            - [x] Store.service, ajout de :
            ```
            getAllGateways() {
                return this.httpclient.get<GatewayInterface[]>(environment.apiUrl + '/gateways');
            }

            getBrokenGatewaysCount(gateways: GatewayInterface[]) {
                return gateways.filter((gateway) => gateway.isBroken).length;
            }
            ```
            - [x] Cop-store-details.component.ts : 
            ```
            fetchAllGateways() {
                this.storeService.getAllGateways().subscribe({
                next: (gateways) => {
                    this.totalBrokenGateways = this.storeService.getBrokenGatewaysCount(gateways);
                },
                error: (err) => {
                    console.error('Error fetching gateways', err);
                },
                });
            }
            ```
            - [x] Cop-store-details.component.html : 
            ```
            <mat-card-subtitle>Gateways Hors-service</mat-card-subtitle>
            <mat-card-title>{{ totalBrokenGateways }}</mat-card-title>
            <br>
            ```
        - [x] Tests et linting
- [ ] Sécuriser l'API
    - [ ] Chercher de la documentation sur le sujet
        - [ ] Check de (https://medium.com/devops-dudes/secure-nestjs-rest-api-with-keycloak-745ef32a2370)
        - [ ] Check de (https://itnext.io/protecting-your-nestjs-api-with-keycloak-8236e0998233)
- [ ] Reprise du tableau des tests fonctionnels 
    - [ ] Reprise des tests de l'application
        - [x] Affichage du nombre de gateways HS dans la carte de /cop
        - [x] Affichage du nombre de gateways disponibles non assignées dans la carte de /cop
    - [ ] Ajout de ticket Jira pour chaque issue
        - [x] Affichage du nombre de gateways (KP-425)
- [ ] Plonger dans le code relatif au websocket