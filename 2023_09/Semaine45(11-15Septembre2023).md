**11 Septembre**
- [ ] Franck m'a dit de considérer la prise d'un stagiaire, et si le besoin est réel, définir le profil et les missions
- [x] Brancher une GW locale pour corrélation données base/prod
- [ ] Check des mails
- [ ] Faire l'affichage des gateways dans la carte de la page /cop (KP-425)
    - [x] Afficher le nombre de gateways non assignées disponibles
        - [x] Résolution du problème : 
        ```
        core.mjs:8405 ERROR Error: NG02200: Cannot find a differ supporting object '[object Object]' of type 'object'. NgFor only supports binding to Iterables, such as Arrays. Did you mean to use the keyvalue pipe? Find more at https://angular.io/errors/NG02200
        ```
        - [x] Changement de la méthode d'ouverture de la modale dans cop-store-details : 
        de
        ```
        openUnassociatedGatewaysModalComponent(unassociatedGWs: any) {
            this.dialog.open(UnassociatedGatewaysModalComponent, {
            data: {unassociatedGWs: this.unassociatedGWs},
            });
            this.fetchAllGateways();
        }
        ```
        à
        ```
            openUnassociatedGatewaysModalComponent(unassociatedGWs: any) {
            this.dialog.open(UnassociatedGatewaysModalComponent, {
            data: this.unassociatedGWs,
            });
            this.fetchAllGateways();
        }
        ```
    - [ ] Afficher les gateways déconnectées ainsi que la dernière date de connexion.
    - [x] Lier les données à l'affichage de la carte
    - [ ] Lint, tests, PR & push
        - [x] Fix des tests dans cop-store-details.component.spec.ts en rajoutant els variables attendues 
        ```
        unassociatedGateways: [],
        unassociatedGatewaysNumber: 3
        ```
        dans les mocks
        - [x] Résolution du problème de test CI :
        ```
        Cannot log after tests are done. Did you forget to wait for something async in your test?
        kmo-predict-front:test:     Attempted to log "Error fetching gateways HttpErrorResponse {
        kmo-predict-front:test:       headers: HttpHeaders {
        kmo-predict-front:test:         normalizedNames: Map(0) {},
        kmo-predict-front:test:         lazyUpdate: null,
        kmo-predict-front:test:         headers: Map(0) {}
        kmo-predict-front:test:       },
        kmo-predict-front:test:       status: 0,
        kmo-predict-front:test:       statusText: 'Unknown Error',
        kmo-predict-front:test:       url: 'http://localhost:3001/gateways',
        kmo-predict-front:test:       ok: false,
        kmo-predict-front:test:       name: 'HttpErrorResponse',
        kmo-predict-front:test:       message: 'Http failure response for http://localhost:3001/gateways: 0 Unknown Error',
        kmo-predict-front:test:       error: ProgressEvent { isTrusted: [Getter] }
        kmo-predict-front:test:     }".
        ```
        - [x] Isolation du problème : le message 
        ```
        A worker process has failed to exit gracefully and has been force exited. This is likely caused by tests leaking due to improper teardown. Try running with --detectOpenHandles to find leaks. Active timers can also cause this, ensure that .unref() was called on them.
        ```
        qui semble être la source du problème provient des modifs liées au fichier de test cop-store-details.component.spec.ts
        - [x] Ajout de UnassociatedGatewaysModalComponent dans les declarations de l'app.module.ts
- [ ] Sécuriser l'API
    - [ ] Chercher de la documentation sur le sujet
        - [ ] Check de (https://medium.com/devops-dudes/secure-nestjs-rest-api-with-keycloak-745ef32a2370)
        - [ ] Check de (https://itnext.io/protecting-your-nestjs-api-with-keycloak-8236e0998233)
- [ ] Reprise du tableau des tests fonctionnels 
    - [ ] Reprise des tests de l'application de /cop  assignées dans la carte de /cop
    - [ ] Ajout de ticket Jira pour chaque issue 
- [ ] Plonger dans le code relatif au websocket


**12 Septembre**
- [ ] Franck m'a dit de considérer la prise d'un stagiaire, et si le besoin est réel, définir le profil et les missions
- [x] Brancher une GW locale pour corrélation données base/prod
- [ ] Check des mails
- [x] Faire l'affichage des gateways dans la carte de la page /cop (KP-425)
    - [x] Afficher le nombre de gateways non assignées disponibles
        - [x] Résolution du problème de test CI :
        ```
        NG0304: 'mat-icon' is not a known element (used in the 'AdministrationComponent' component template):
        ```
        - [x] Ajout de MatIconModule et import dans le fichier de test d'administration
        - [x] Résolution du problème de test CI :
        ```
        NG0304: 'app-tab-store' is not a known element (used in the 'AdministrationComponent' component template):
        ```
        - [x] Ajout de TabStoreComponent dans les déclarations du fichier de test administration
        - [x] Ajout de KeycloakService dans les providers du fichier
        - [x] Ajout de FormsModule et import dans le fichier de test d'administration
        - [x] Résolution du problème de test CI :
        ```
        Cannot log after tests are done. Did you forget to wait for something async in your test?
        ```
        - [x] Reprise d'une branche clean basée sur develop
        - [x] Checkout & modifs pour clean les errors console & warnings
        - [x] Changed test : 
        ```
        // it('should call stateOfGateways method and subscribe to onStateOfGateways', () => {
        //   const websocketService = TestBed.inject(WebsocketService);
        //   const stateOfGatewaysSpy = jest.spyOn(websocketService, 'stateOfGateways');
        //   const onStateOfGatewaysSpy = jest.spyOn(websocketService, 'onStateOfGateways').mockReturnValue(of({}));

        //   component.fetchStatOfGateways();

        //   expect(stateOfGatewaysSpy).toHaveBeenCalled();
        //   expect(onStateOfGatewaysSpy).toHaveBeenCalled();
        // });
        ```
        to : 
        ```
        it('should call stateOfGateways method and subscribe to onStateOfGateways', fakeAsync(() => {
        const websocketService = TestBed.inject(WebsocketService);
        const stateOfGatewaysSpy = jest.spyOn(websocketService, 'stateOfGateways');
        const mockData = { 
            totalGateways: 2,
            connectedGateways: 2,
            disconnectedGateways: [],
            unassociatedGateways: [],
            unassociatedGatewaysNumber: 0
        };
        
        // Mock the onStateOfGateways method to return an observable
        jest.spyOn(websocketService, 'onStateOfGateways').mockReturnValue(of(mockData));
        
        component.fetchStatOfGateways();
        
        // Verify that the stateOfGateways method was called
        expect(stateOfGatewaysSpy).toHaveBeenCalled();
        
        // Ensure that the component's gatewaysLogs property has been updated
        tick();
        
        // Now you can expect that component.gatewaysLogs equals mockData
        expect(component.gatewaysLogs).toEqual(mockData);
        }));
        ```
        - [x] Lint
        - [x] Tests
        - [x] Push & PR
- [ ] Faire l'affichage de l'état des caisses dans la carte de la page /cop (KP-466)
    - [x] Création de branche 466 & switch
    - [x] Retake mineure < br > card gws
    - [ ] Récupération du nombre de caisses Check données utiles
        - [ ] Afficher toutes les caisses et leurs données
            - [x] Création d'une variable dans le fichier cop-details.component : 
            ```
            public kmoBoxData: KmoBoxInterface[] | null = null;
            ```
            - [x] Création d'une methode getAllKmoBoxes dans le service kmo box : 
            ```
            getAllKmoBoxes(){
                return this.httpclient.get<KmoBoxInterface[]>(this.apiUrl);
            }
            ```
            - [x] 
            - [ ] Afficher le nombre de caisses HS
                - [x] Ajout du html dans le fichier : 
                ```
                <mat-card-content>
                    <mat-card-subtitle>Caisses Hors-service</mat-card-subtitle>
                    <mat-card-title>{{ totalBrokenKmoBoxes }} / {{ totalKmoBoxes }}</mat-card-title>
                    <br>
                </div>
                </mat-card-content>
                ```
                - [x] Création des variables totalBrokenKmoBoxes, totalKmoBoxesNumber & totalKmoBoxes
                - [x] Création d'une methode getKmoBoxCount dans le service kmo box : 
                ```
                getAllKmoBoxes(){
                    return this.httpclient.get<KmoBoxInterface[]>(this.apiUrl);
                }
                ```
        - [ ] Afficher le nombre de caisses en fonctionnement
        - [ ] Afficher les caisses en maintenance prev
        - [ ] Afficher les caisses en maintenance urgente
        ```
        fetchBrokenKmoBoxes(){
            this.kmoBoxesService.getAllKmoBoxes().subscribe({
            next: (response: any) => {
                if (response && Array.isArray(response.payload)) {
                this.brokenKmoBoxes = response.payload.filter((kmoBox: any) => kmoBox.isOutOfOrder);
                this.totalBrokenKmoBoxes = response.payload.filter((kmoBox: any) => kmoBox.isOutOfOrder).length;
                this.workingKmoBoxes = response.payload.filter((kmoBox: any) => !kmoBox.isOutOfOrder).length;
                this.curativeKmoBoxes = response.payload.filter(
                    (kmoBox: any) => kmoBox.maintenanceLevel === "Curative"
                );  
                this.curativeKmoBoxesNumber = response.payload.filter(
                    (kmoBox: any) => kmoBox.maintenanceLevel === "Curative"
                ).length;
                this.preventiveKmoBoxes = response.payload.filter(
                    (kmoBox: any) => kmoBox.maintenanceLevel === "Preventive"
                );  
                this.preventiveKmoBoxesNumber = response.payload.filter(
                    (kmoBox: any) => kmoBox.maintenanceLevel === "Preventive"
                ).length;
                } else {
                this.brokenKmoBoxes = [];
                }
            },
            error: (err) => {
                console.error('Error fetching kmoboxes', err);
            },
            });
        }
        ```
        - [ ] Tester unitairement les methodes créées
        
le mailing service marche en dev ?
comment send mail à l'actualisation de statut d'une GW ?
penser à ajouter une propriété "nom" ou autre pour identifier plus facilement les gateways