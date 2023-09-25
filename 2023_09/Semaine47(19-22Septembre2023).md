**19 Septembre**
- [x] KP-467 Statut des magasins
    - [x] Afficher la liste des magasins avec au moins une maintenance urgente
        - [x] Afficher les caisses problématiques et leur statut
            - [x] Modifier le back pour retrieve un magasin et ses infos depuis une mac de gateway
                - [x] Ajout dans le storeService de : 
                ```
                async findStoreByGatewayMac(gatewayMac: string)/*: Promise<Store | null> */{
                    try {
                    return await this.storeRepository.findOne({ where: { gatewayMac } });
                    } catch (e) {
                    throw new HttpException(e, 500);
                    }
                }
                
                async findStoreByGatewayMac2(gatewayMac: string)/*: Promise<Store | null> */{
                    try {
                    return Store.findOne({ where: { gatewayMac } });
                    } catch (e) {
                    throw new HttpException(e, 500);
                    }
                }
                ```
                & dans le storeController de : 
                ```
                @Get('by-gateway-mac')
                findStoreByGatewayMac(@Query('gatewayMac') gatewayMac: string) {
                    return this.storesService.findStoreByGatewayMac(gatewayMac);
                }
                @Get('by-stores-gateway-mac')
                findStoreByGatewayMac2(@Query('gatewayMac') gatewayMac: string) {
                    return this.storesService.findStoreByGatewayMac(gatewayMac);
                }
                ```
                - [x] Rebuild dockers
    - [x] Afficher la liste des magasins avec au moins une maintenance préventive
        - [x] Afficher les caisses problématques et leur statut
penser pour la suite à changer gateway mac par serial number , faire les changements nécessaires dans la DB, le fichier SQL de mise en route, et l'API
- [ ] KP-473, push en prod des mises à jour du code
    - [x] Connection à l'instance
    - [x] Check de l'espace restant : 
    ```
    df -h
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/root        29G   21G  8.2G  72% /
    tmpfs           2.0G     0  2.0G   0% /dev/shm
    tmpfs           785M  1.4M  783M   1% /run
    tmpfs           5.0M     0  5.0M   0% /run/lock
    /dev/xvda15     105M  6.1M   99M   6% /boot/efi
    tmpfs           393M  4.0K  393M   1% /run/user/1000
    ```
    - [x] Check des derniers commits actifs de la prod si besoin de revert
    ```
    git log
    commit bb6fa19845dcfe894ed8535c2449ab0fca80ce65 (HEAD -> KP-437_finish_user, origin/KP-437_finish_user)
    Author: AWilhelmcopamaco <A.wilhelm@cop-amaco.com>
    Date:   Fri Sep 1 10:29:13 2023 +0200

        fix(env.beta) change environement for the new

    commit c107d46102ed9b041654d0e15bed416c4bb0f260
    Author: GregoireChevalierCOPAmaco <115467044+GregoireChevalierCOPAmaco@users.noreply.github.com>
    Date:   Wed Aug 30 10:17:08 2023 +0200

        fix(front): Fixed issues in /admin (KP-424) (#99)

        fix(front): Fixed sorting issues in /admin & refactored component code into tab-store
    ```
    - [x] Arrêt des containers docker
    - [ ] remove du container apps
    - [ ] Clean des images et autres artefacts résiduels du container apps
    - [ ] positionnement dans le dossier KMO_PREDICT
    - [ ] ```git pull origin develop```
    - [ ] Build prod angular
        - [ ] Delete 


**21 Septembre**
- [ ] KP-473, push en prod des mises à jour du code
    - [x] Connexion à l'instance ```ssh -i "Predict-Beta.pem" ubuntu@ec2-3-76-238-223.eu-central-1.compute.amazonaws.com```
    - [x] Arrêt des containers docker
    - [x] remove du container apps
    - [x] remove des images :
    ```
    sudo docker image rm ec19c3e35c8b 3269c366ef4a 5dffa4f57e2b 7ed122296112 70afecd7e09c dc9576ad72f7 e2060bb4b2d3 3edc39dbfdef 0d430108264a  de105afe0794 8a9dd81c3047 7ba9acac558f8d97eb0304c7
    ```
    - [x] remove des volumes :
    ```
    sudo docker volume rm 2be1fea08de9b91ba5396058f27a31fd8ba185d618b6a70b6d687d77fce5d093 
    5ef738fbc9387bf7a9669c69c02c38f840b9828a1119bd7c79257064d37829f3 
    5f165da975d425051f8d822a17a8946b8e0abbb132320096ace7e12f418309ae 
    6a848045da76bab0ec6fd885e71167e93a0f09d5404e8ba3b54ee1c683df18c2 
    6d1693ccf1b385dce398eb84432bc83cba3f32dd5da1eea37f8d7660037335fe
    ...
    ```
    gain de 31% d'espace serveur
    - [x] Clean des images et autres artefacts résiduels du container apps
    - [x] positionnement dans le dossier KMO_PREDICT
    - [x] 
    ```
    git config pull.rebase false
    git pull origin feat/KP-467_display_dynamically_critical_stores_incard
    ```
    - [x] Build prod 
        - [x] 
        ```
        sudo  docker compose --env-file .env.beta -f docker-compose-prod.yml up --build
        ```
- [x] KP-453 Ajouter un rafraîchissement de la page à l'ajout/duplication/modification de magasin de la page /admin
    - [x] Retour en local
    - [x] Création de branche
    ```
    git checkout -b feat/KP-453_autorefresh_pages_onstores_updates
    ```
    - [x] Suggestion gpt : 
    ```
    To refresh the page of the AdministrationComponent every time you submit a form from one of the modals, you can follow these steps:

    Create a service that will act as an event bus to notify the AdministrationComponent when a form is submitted in one of the modals.

    Modify the AddStoreComponent to inject this service and emit an event when a form is submitted.

    Subscribe to the event in the AdministrationComponent and trigger a refresh action.

    Here's a step-by-step guide:

    Step 1: Create an Event Bus Service

    Create a service to act as an event bus. This service will be responsible for emitting events when a form is submitted.

    typescript
    Copy code
    // event-bus.service.ts

    import { Injectable } from '@angular/core';
    import { Subject } from 'rxjs';

    @Injectable({
    providedIn: 'root',
    })
    export class EventBusService {
    private refreshSubject = new Subject<void>();

    refresh$ = this.refreshSubject.asObservable();

    emitRefreshEvent() {
        this.refreshSubject.next();
    }
    }
    Step 2: Modify AddStoreComponent

    Inject the EventBusService into the AddStoreComponent and emit an event when the form is submitted.

    typescript
    Copy code
    // add-store.component.ts

    import { Component, Inject, OnInit } from '@angular/core';
    // ... (other imports)

    // Import the EventBusService
    import { EventBusService } from '../path-to-event-bus.service'; // Update with the correct path

    @Component({
    selector: 'app-add-store',
    templateUrl: './add-store.component.html',
    styleUrls: ['./add-store.component.css'],
    })
    export class AddStoreComponent implements OnInit {
    // ... (other class properties)

    constructor(
        // ...
        private eventBusService: EventBusService // Inject the EventBusService
    ) {}

    ngOnInit(): void {
        // ...
    }

    submitStore() {
        // ... (existing code to submit the form)

        // Emit the refresh event when the form is submitted
        this.eventBusService.emitRefreshEvent();

        // Close the dialog
        this.dialogRef.close();
    }

    // ...
    }
    Step 3: Update AdministrationComponent

    Subscribe to the refresh event in the AdministrationComponent and trigger the refresh action (e.g., call getStores again) when the event is emitted.

    typescript
    Copy code
    // administration.component.ts

    import { Component, HostListener, OnInit, ViewChild } from '@angular/core';
    // ... (other imports)

    // Import the EventBusService
    import { EventBusService } from '../path-to-event-bus.service'; // Update with the correct path

    @Component({
    selector: 'app-administration',
    templateUrl: './administration.component.html',
    styleUrls: ['./administration.component.css'],
    })
    export class AdministrationComponent implements OnInit {
    // ... (other class properties)

    constructor(
        // ... (other injected services),
        private eventBusService: EventBusService // Inject the EventBusService
    ) {}

    ngOnInit(): void {
        this.getStores();

        // Subscribe to the refresh event
        this.eventBusService.refresh$.subscribe(() => {
        // Refresh action (e.g., call getStores again)
        this.getStores();
        });
    }

    // ... (other methods)

    getStores() {
        // ... (existing code to fetch stores)
    }

    // ... (other methods)
    }
    Now, when you submit a form in one of the modals, it will emit a refresh event, and the AdministrationComponent will subscribe to that event and trigger a refresh action, such as calling getStores to refresh the data on the page.
    ```
- [ ] KP-476 Rendre fonctionnel le changement d'état d'une caisse au clic sur la modale
    - [ ] Retour en local
    - [ ] Création de branche
    ```
    git checkout -b fix/KP-476_apply_checkout_status_change_onclick
    ```

    renvoyer le user sur le magasin en question dans la liste des magasin critiques / a risques

    mettre les cards de caisses en couleur selon leur état dans la vue magasin


**21Septembre**
- [x] KP-476 Rendre fonctionnel le changement d'état d'une caisse au clic sur la modale
    - [x] Ajout d'une modif dans la ligne : 
    ```
    (selectionChange)="updateKmoBox()"
    ```
    dans 
    ```
    <mat-form-field appearance="fill">
        <mat-select  [(ngModel)]="data.maintenanceLevel" (selectionChange)="updateKmoBox()">
        <mat-option value="Operationnel">Operationnel</mat-option>
        <mat-option value="R.A.S">R.A.S</mat-option>
        <mat-option value="Curative">Curative</mat-option>
        <mat-option value="Predictive">Predictive</mat-option>
        </mat-select>
    </mat-form-field>
    ```
- [x] Màj Jira des tickets doublon
- [ ] Màj Jira des tickets à faire après le point de la veille
- [x] Mise en place des gateways test dans le bureau avec Jacques
- [x] Test des gateways dans le bureau avec Jacques & modifs sur le timeout de connexion

