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
            - [x] Modification du fichier app-routing.module.ts
        - [x] Suppression de toutes les mentions à la route /home
            - [x] keycloak flow e2e test
            - [x] app.module.ts
            - [x] home component, css, html & home component spec
            - [x] home cop component ts
            - [x] rename home cop en cop-store-details
                - [x] dossier
                - [x] html
                - [x] css
                - [x] test
                - [x] component
                - [x] Remplacement des mentions à home-cop dans les fichiers
        - [x] Delete des fichiers d'essais de docker temporaire
        - [x] Commit, push & PR
- [x] Mise à jour du jira
- [x] Accès au compte office 365
- [x] Accès au fichier excel partagé tests fonctionnels via : https://copcheksoutproduction.sharepoint.com/:x:/r/sites/DIGITAL_TEAM/_layouts/15/Doc.aspx?sourcedoc=%7B33F6BF81-A0DD-43F0-A454-13EA23CD8BA1%7D&file=Tests%20fonctionnels.xlsx&action=default&mobileredirect=true
- [ ] Exhaustification du fichier excel de tests fonctionnels
- [x] Création de branche KP-430 pour tâche Résoudre les problèmes de renvoi de password par mail
- [x] git pull


**24 Aout**
- [ ] Reprise du tableau des tests fonctionnels 
- [ ] KP-430, Fix de l'envoi bugé de mail de recover password
    - [x] Retour en local
    - [x] Essai d'envoi de reset password avec g.chevalier@cop-amaco.com
    Retour négatif console : 
    POST http://localhost:8080/realms/Cop_sass/login-actions/reset-credentials?session_code=5bTuRtR7QD9q1Z8_5hKdMrcCp_tMH3SqrN8tPMg2nzk&execution=0a268b8a-484f-4c43-9ad4-917dc9e74364&client_id=KMO_Predict&tab_id=xq6cqFHWTmc 500 (Internal Server Error)
    Retour négatif log docker : 
    WARN  [org.keycloak.events] (executor-thread-62) type=SEND_RESET_PASSWORD_ERROR, realmId=Cop_sass, clientId=KMO_Predict, userId=bfe25071-688e-45b4-bd72-40391f62f9ce, ipAddress=172.24.0.1, error=email_send_failed, auth_method=openid-connect, auth_type=code, redirect_uri=http://localhost:3000/home, code_id=cbc58c53-4358-4aa9-aa67-a32cfa68abc9, username=g.chevalier@cop-amaco.com, authSessionParentId=cbc58c53-4358-4aa9-aa67-a32cfa68abc9, authSessionTabId=xq6cqFHWTmc
    - [x] Essai avec 123, retour étrange avec message de success et coche verte.
    - [x] Connexion à l'instance keycloak pour check des logs docker, même résultats qu'en local

**25 Aout**
- [ ] Reprise du tableau des tests fonctionnels 
- [ ] KP-424, Réparer le triage des magasins sur /administration
    - [x] Retour en local
    - [x] Création de la branche
    - [x] Switch sur la branche
    - [x] Pull origin pour être à jour 
    - [ ] Comparaison du code entre /cop et /admin sur la table
        - [x] Le code responsable du tri est le ```mat-sort-header```du fichier cop-store-details.component.html
        - [x] Ajouts au component, passage de :
        ```
        import { Component } from '@angular/core';
        import {MatDialog} from '@angular/material/dialog';
        import {AddStoreComponent} from '../../component/administrations/add-store/add-store.component';

        @Component({
        selector: 'app-administration',
        templateUrl: './administration.component.html',
        styleUrls: ['./administration.component.css']
        })
        export class AdministrationComponent{
        constructor(
        public dialog : MatDialog,
        ) {
        }

        openDialog(){
        this.dialog.open(AddStoreComponent,{
        data:{method:'post'}
        })
        }
        }
        ```
        à :
        ```
        import { Component, HostListener, ViewChild } from '@angular/core';
        import { MatDialog } from '@angular/material/dialog';
        import { MatTableDataSource } from '@angular/material/table';
        import { MatPaginator } from '@angular/material/paginator';
        import { MatSort, Sort } from '@angular/material/sort';
        import { StoreService } from '../../core/services/store.service';
        import { StoreInterface } from '../../core/interfaces/store.interface';
        import { WebsocketService } from '../../core/services/websocket.service';
        import { GatewaysLogsInterface } from '../../core/interfaces/gateways-logs.interface';
        import { LiveAnnouncer } from '@angular/cdk/a11y';
        import { MatSnackBar } from '@angular/material/snack-bar';
        import { AddStoreComponent } from 'src/app/component/administrations/add-store/add-store.component';

        @Component({
        selector: 'app-administration',
        templateUrl: './administration.component.html',
        styleUrls: ['./administration.component.css']
        })
        export class AdministrationComponent {
        public gatewaysLogs!: GatewaysLogsInterface;
        public dataSource = new MatTableDataSource<StoreInterface[]>();
        public displayedColumns: string[] = [
            'address',
            'city',
            'brand',
            'postalCode',
            'numberOfCheckouts',
            'gatewaysState',
        ];

        @ViewChild(MatPaginator) paginator!: MatPaginator;

        sort: MatSort | null = new MatSort;
        page!: number;
        pageSize!: number;
        query!: string;
        length!: number;
        pageIndex!: number;

        constructor(
            private snackBar: MatSnackBar,
            private readonly storeService: StoreService,
            private readonly websocketService: WebsocketService,
            public dialog: MatDialog,
            private liveAnnouncer: LiveAnnouncer
        ) { }

        ngOnInit(): void {
            this.getStores();
        }

        getStores() {
            this.storeService.getStores(this.page, this.pageSize,this.query).subscribe({
            next: (data) => {
                this.dataSource.data = data.items;
                this.pageSize = data.meta.itemsPerPage;
                this.length = data.items.length;
            },
            error: (err) => {
                this.snackBar.open(err.message, 'Ok');
            },
            });
        }
        pageChangeEvent(event: any) {
            this.page = event.page;
            this.pageSize = event.pageSize;
            this.getStores();
        }
        @HostListener('keydown.enter',['$event'])

        announceSortChange(sortState: Sort){
            if (sortState.direction) {
            this.liveAnnouncer.announce(`Sorted ${sortState.direction}ending`);
            } else {
            this.liveAnnouncer.announce('Sorting cleared');
            }
        }

        isGatewayDisconnected(mac: string): boolean {
            return this.gatewaysLogs?.disconnectedGateways.some((obj) =>
            obj.mac.includes(mac),
            );
        }

        ngAfterViewInit() {
            this.dataSource.paginator = this.paginator;
            this.dataSource.sort = this.sort

        }

        openDialog(){
        this.dialog.open(AddStoreComponent,{
        data:{method:'post'}
        })
        }

        }
        ```
    - [x] Passage des tests
        - [x] Résolution de Cannot find module 'src/app/component/administrations/add-store/add-store.component' from 'src/app/page/administration/administration.component.ts',
        chengement de l'import à : ```import { AddStoreComponent } from '../../component/administrations/add-store/add-store.component';```
        - [x] Résolution des 4 erreurs de  NullInjectorError: R3InjectorError(DynamicTestModule)[MatSnackBar -> MatSnackBar]:    
      NullInjectorError: No provider for MatSnackBar!
        - [x] Ajout dans les providers
        - [x] Changement de  : 
        ```
        // import {ComponentFixture, TestBed} from '@angular/core/testing';
        // import {AdministrationComponent} from './administration.component';
        // import {MatDialog, MatDialogModule, MatDialogRef} from '@angular/material/dialog';
        // import {NO_ERRORS_SCHEMA} from '@angular/core';
        // import { AddStoreComponent } from '../../component/administrations/add-store/add-store.component';
        // import { MatSnackBar, MatSnackBarModule } from '@angular/material/snack-bar';
        // import { StoreService } from '../../../app/core/services/store.service';
        // import { HttpClientTestingModule } from '@angular/common/http/testing';
        // import { WebsocketService } from '../../../app/core/services/websocket.service';
        // import { SocketIoModule, SocketIoConfig } from 'ngx-socket-io';
        // import { WrappedSocket } from 'ngx-socket-io/src/socket-io.service';

        // describe('AdministrationComponent', () => {
        //   let component: AdministrationComponent;
        //   let fixture: ComponentFixture<AdministrationComponent>;
        //   let matDialogMock: Partial<MatDialog>;
        //   let matSnackBarMock: Partial<MatSnackBar>;

        //   beforeEach(async () => {
        //     matDialogMock = {
        //       open: jest.fn(),
        //     };

        //     matSnackBarMock = {
        //       open: jest.fn(),
        //     };

        //     await TestBed.configureTestingModule({
        //       declarations: [AdministrationComponent],
        //       imports: [
        //         MatDialogModule, 
        //         MatSnackBarModule, 
        //         HttpClientTestingModule,
        //         SocketIoModule.forRoot({ url: 'http://localhost:3000' }),
        //       ],
        //       providers: [
        //         {provide: MatDialog, useValue: matDialogMock}, 
        //         {provide: MatDialogRef, useValue: {}},
        //         {provide: MatSnackBar, useValue: matSnackBarMock}, 
        //         StoreService,
        //         WebsocketService,
        //         WrappedSocket,
        //       ],
        //       schemas: [NO_ERRORS_SCHEMA],
        //     })
        //       .compileComponents();
        //   });

        //   beforeEach(() => {
        //     fixture = TestBed.createComponent(AdministrationComponent);
        //     component = fixture.componentInstance;
        //     fixture.detectChanges();
        //   });

        //   it('should create', () => {
        //     expect(component).toBeTruthy();
        //   });

        //   it('should open dialog when openDialog is called', () => {
        //     component.openDialog();

        //     expect(matDialogMock.open).toHaveBeenCalledWith(AddStoreComponent, {
        //       data: { method: 'post' },
        //     });
        //   });
        
        //   it('should call openDialog when the button is clicked', () => {
        //     const button = fixture.nativeElement.querySelector('button');
        //     component.openDialog = jest.fn(); 
        
        //     button.click();
        
        //     expect(component.openDialog).toHaveBeenCalled();
        //   });
        
        //   it('should render app-tab-store component', () => {
        //     const tabStoreComponent = fixture.nativeElement.querySelector('app-tab-store');
            
        //     expect(tabStoreComponent).toBeTruthy();
        //   });
        
        // });
        ```
        à :
        ```
        import { ComponentFixture, TestBed, waitForAsync } from '@angular/core/testing';
        import { MatDialog, MatDialogModule, MatDialogRef } from '@angular/material/dialog';
        import { MatSnackBar, MatSnackBarModule } from '@angular/material/snack-bar';
        import { MatPaginatorModule } from '@angular/material/paginator';
        import { MatSortModule } from '@angular/material/sort';
        import { MatTableModule } from '@angular/material/table';
        import { NoopAnimationsModule } from '@angular/platform-browser/animations';
        import { AdministrationComponent } from './administration.component';
        import { StoreService } from '../../core/services/store.service';
        import { WebsocketService } from '../../core/services/websocket.service';
        import { AddStoreComponent } from '../../component/administrations/add-store/add-store.component';
        import { LiveAnnouncer } from '@angular/cdk/a11y';
        import { of } from 'rxjs';
        import { Paginated } from 'src/app/core/interfaces/paginated.interface';
        import { StoreInterface } from 'src/app/core/interfaces/store.interface';

        describe('AdministrationComponent', () => {
        let component: AdministrationComponent;
        let fixture: ComponentFixture<AdministrationComponent>;
        let matDialogMock: Partial<MatDialog>;
        let matSnackBarMock: Partial<MatSnackBar>;
        let storeServiceStub: Partial<StoreService>;
        let websocketServiceStub: Partial<WebsocketService>;

        beforeEach(waitForAsync(() => {
            matDialogMock = {
            open: jest.fn(),
            };

            matSnackBarMock = {
            open: jest.fn(),
            };

            storeServiceStub = {
            getStores: jest.fn(() => of({
                items: [],
                meta: {
                itemsPerPage: 10,
                totalItems: 0, 
                itemCount: 0,
                totalPages: 0,
                currentPage: 1,
                }
            } as Paginated<StoreInterface[]>)),
            };

            websocketServiceStub = {};

            TestBed.configureTestingModule({
            declarations: [AdministrationComponent],
            imports: [
                NoopAnimationsModule,
                MatDialogModule,
                MatSnackBarModule,
                MatPaginatorModule,
                MatSortModule,
                MatTableModule,
            ],
            providers: [
                { provide: MatDialog, useValue: matDialogMock },
                { provide: MatDialogRef, useValue: {} },
                { provide: MatSnackBar, useValue: matSnackBarMock },
                { provide: StoreService, useValue: storeServiceStub },
                { provide: WebsocketService, useValue: websocketServiceStub },
                LiveAnnouncer,
            ],
            }).compileComponents();
        }));

        beforeEach(() => {
            fixture = TestBed.createComponent(AdministrationComponent);
            component = fixture.componentInstance;
            fixture.detectChanges();
        });

        it('should create', () => {
            expect(component).toBeTruthy();
        });

        it('should open dialog when openDialog is called', () => {
            component.openDialog();

            expect(matDialogMock.open).toHaveBeenCalledWith(AddStoreComponent, {
            data: { method: 'post' },
            });
        });

        it('should render table', () => {
            const tableElement = fixture.nativeElement.querySelector('table');
            expect(tableElement).toBeTruthy();
        });

        it('should render mat-paginator', () => {
            const paginatorElement = fixture.nativeElement.querySelector('mat-paginator');
            expect(paginatorElement).toBeTruthy();
        });

        it('should call getStores on initialization', () => {
            const getStoresSpy = jest.spyOn(component, 'getStores');
            component.ngOnInit();
            expect(getStoresSpy).toHaveBeenCalled();
        });

        it('should call getStores when pageChangeEvent is triggered', () => {
            const getStoresSpy = jest.spyOn(component, 'getStores');
            const event = { page: 1, pageSize: 10 };
            component.pageChangeEvent(event);
            expect(getStoresSpy).toHaveBeenCalled();
        });

        });
        ```
    - [x] Passage du lint
    - [x] Add, commit, push et création de PR
- [ ] Passage à la KP 429
    - [x] Retour en local
    - [x] Création de la branche
    - [x] Switch sur la branche
    - [x] Pull origin pour être à jour 
    - [x] change-gateway-assignation.component.ts, ajout de :
    ```
    private snackBar: MatSnackBar
    ```
    dans le constructor et 
    changement de la méthode *reassignGateway()* :
    ```
    reassignGateway() {

    this.storeService.changeGateway(this.data.id, this.gatewayForm.get('mac')?.value).subscribe(() => {
      this.historyGatewayService.createHistoryGateway({
        nameOfTheUser: `${this.userProfile.__zone_symbol__value.username}`,
        description: `gateway ajoutée au magasin ${this.data.address + this.data.brand}`,
        gateway: {mac: this.gatewayForm.get('mac')?.value}
        // eslint-disable-next-line @typescript-eslint/no-empty-function
      }).subscribe(
        () => {
          this.snackBar.open('Réassignation de gateway réussie', 'fermer', {
            panelClass: ['snackbarGreen'],
          });
      }, error => {
        this.snackBar.open("Echec de réassignation de la gateway", 'fermer', {
          panelClass: 'snackbarGreen',
        });
        throw error;
      })
    }
    ```
    - [ ] Passage des tests