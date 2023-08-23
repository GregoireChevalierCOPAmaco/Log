**21 Aout**
- [x] Check des mails
- [ ] Reprise du tableau des tests fonctionnels 
- [ ] KP-421, Suppression de la page /home
    - [x] Retour en local
        - [x] Suppression de toutes les images, containers & volumes du docker desktop
        - [x] Rebuild d'un projet local en l'état pour voir où ça pète
            - [x] Rebuild docker
            - [x] Reimport du realm cop sass
                - [x] Page not found
                - [x] Modification du client KMO_Predict pour ne plus taper sur /home mais /users
                - [x] Page not found
                - [x] Rechangement de l'app.odule.ts local front realm:'Cop_sass'
                - [x] Page blanche
        - [x] Résolution du problème de page blanche en local
            - [x] Suppression des images & volumes apps du docker desktop
            - [x] Rebuild docker
            - [x] Reach /users et erreur navigateur : Cannot GET /users
            - [x] Relance du front depuis le dossier /apps/front en local et reach ok.
        - [ ] Redirection vers /cop ou /storebyid/iddustore en fonction des privilèges du user connecté
            - [ ] Modification du fichier auth.guard.ts
            Changement de :
            ```
            if (!granted) {
                this.router.navigate(['/']);
            }
            resolve(granted);
            ```
            à
            ```
            if (!granted) {
                if (this.roles.includes('kmo-predict_store-manager')) {
                this.router.navigate(['/store/:id']); 
                } else {
                this.router.navigate(['/cop']);
                }
            }
            resolve(granted);
            ```
            - [ ] Modification du fichier app-routing.module.ts
        - [ ] Suppression de toutes les mentions à la route /home
            - [ ] keycloak flow e2e test
            - [ ] successful auth flow e2e test
            - [ ] unsuccessful auth flow e2e test
            - [ ] app.component.html
            - [ ] app.module.ts
            - [ ] tab store html
            - [ ] home component & home component spec
            - [ ] home cop component ts
            - [ ] realm export

**22 Aout**
- [ ] Reprise du tableau des tests fonctionnels 
- [ ] KP-421, Suppression de la page /home
    - [x] Retour en local
        - [x] Suppression de toutes les images, containers & volumes du docker desktop
        - [x] Rebuild d'un projet local en l'état pour voir où ça pète
            - [x] Rebuild docker
            - [x] Reimport du realm cop sass
        - [ ] Redirection vers /cop ou /storebyid/iddustore en fonction des privilèges du user connecté
            - [ ] Modification du fichier auth.guard.ts
            Changement de :
            ```
            if (!granted) {
                this.router.navigate(['/']);
            }
            resolve(granted);
            ```
            à
            ```
            if (!granted) {
                if (this.roles.includes('kmo-predict_store-manager')) {
                this.router.navigate(['/store/:id']); 
                } else {
                this.router.navigate(['/cop']);
                }
            }
            resolve(granted);
            ```
            - [ ] Modification du fichier app-routing.module.ts
        - [ ] Suppression de toutes les mentions à la route /home
            - [ ] keycloak flow e2e test
            - [x] successful auth flow e2e test
            - [x] unsuccessful auth flow e2e test
            - [x] app.component.html
                - [x] Mise en commentaires du bouton vers home :
                ```
                <!-- <a link mat-list-item routerLink="/home" routerLinkActive="selected">
                    <mat-icon class="text-white align-middle">home</mat-icon>
                    <span class="link-label text-white">Accueil</span>
                </a> -->
                ```
            - [ ] app.module.ts
            - [x] tab store html
                - [x] Changement de :
                ```
                <div class="example-button-row py-8">
                    <a routerLink="/home">
                        <button mat-stroked-button color="primary">Retour à l'accueil</button>
                    </a>
                </div>
                ```
                à 
                ```
                <div class="example-button-row py-8">
                    <a routerLink="/cop">
                        <button mat-stroked-button color="primary">Retour</button>
                    </a>
                </div>
                ```
            - [ ] home component & home component spec
            - [ ] home cop component ts
            - [x] realm export
- [x] Mise à jour du jira


**23 Aout**
- [ ] Reprise du tableau des tests fonctionnels 
- [ ] KP-421, Suppression de la page /home
    - [x] Retour en local
        - [x] Redirection vers /cop ou /storebyid/iddustore en fonction des privilèges du user connecté
            - [ ] Modification du fichier auth.guard.ts
            Changement de :
            ```
            if (!granted) {
                this.router.navigate(['/']);
            }
            resolve(granted);
            ```
            à
            ```
            if (!granted) {
                if (this.roles.includes('kmo-predict_store-manager')) {
                this.router.navigate(['/store/:id']); 
                } else {
                this.router.navigate(['/cop']);
                }
            }
            resolve(granted);
            ```
            - [ ] Modification du fichier app-routing.module.ts
        - [ ] Suppression de toutes les mentions à la route /home
            - [ ] keycloak flow e2e test
            - [ ] app.module.ts
            - [ ] home component & home component spec
            - [ ] home cop component ts
- [x] Mise à jour du jira