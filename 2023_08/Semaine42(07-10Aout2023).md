**7 Aout**
- [x] Poursuite des tests fonctionnels
    - [x] Mise à jour du google doc
    - [x] Création de tickets Jira en conséquence

**8 Aout**
- [ ] KP-421, Suppression de la page /home
    - [x] Retour en local
        - [x] Repositionnement sur develop 
            ```
            git stash
            git checkout develop
            git pull origin develop
            ```
        - [x] Création de la branche
        ```
        git checkout -b refactor/KP-421_homepage_suppression
        ```
        - [ ] Suppression de toutes les mentions à la route /home
            - [ ] keycloak flow e2e test
            - [ ] successful auth flow e2e test
            - [ ] unsuccessful auth flow e2e test
            - [x] app-routing module
                - [x] Remplacement de 
                ```
                {path: '**', redirectTo: '/home'}
                ```
                par
                ```
                {path: '**', redirectTo: '/cop'}
                ```
                & suppression de la route : 
                ```
                {
                    path: 'home',
                    canActivate: [AuthGuard],
                    data: {
                    roles: [
                        'cop_super-user',
                        'cop_developer',
                        'kmo-predict_admin',
                        'kmo-predict_regional-direction',
                        'kmo-predict_store-manager',
                    ],
                    },
                    component: HomeComponent,
                },
                ```
            - [ ] app.component.html
            - [ ] app.module.ts
            - [ ] tab store html
            - [ ] home component & home component spec
            - [ ] home cop component ts
            - [ ] realm export
        - [ ] Remplacement de la mention /home par /cop ou /storebyid/iddustore en fonction des privilèges