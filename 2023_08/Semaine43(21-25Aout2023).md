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
            if (this.roles.includes('cop_super-user')) {
                this.router.navigate(['/...']);
            } else if (this.roles.includes('kmo-predict_store-manager')) {
                this.router.navigate(['/...']); // You'll need to replace :id with the actual ID
            } else {
                this.router.navigate(['/...']);
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