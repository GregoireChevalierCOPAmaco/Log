**21 Mars**
- [x] Connexion à predict test,  ```git pull origin develop``` et rebuild docker pour valider KP-839 : ECHEC, avec erreur console : 
```
ERROR Error: NG0901
```
- [ ] Résolution ERROR : NG0901
NG0901 occurs when i try to iterate over an object rather than an expected array
Changement .html avec getDataAsArray() : 
```
<h1 mat-dialog-title>Gateways en ligne</h1>
<div mat-dialog-content>
  <ul>
    <ng-container *ngFor="let value of getDataAsArray()">
      <li *ngIf="getStoreInfo(value.mac) | async as storeInfo">
        <a [routerLink]="['/cop', getStoreId(value.mac)]">
          {{ value.mac }} - {{ storeInfo.brand }}, {{ storeInfo.address }}, {{ storeInfo.city }}
        </a>
      </li>
      <li *ngIf="!(getStoreInfo(value.mac) | async)">
        <a [routerLink]="['/cop', getStoreId(value.mac)]">
          {{ value.mac }} - No store assigned to this gateway
        </a>
      </li>
    </ng-container>
  </ul>
</div>
<div mat-dialog-actions>
  <button mat-button mat-dialog-close>Fermer</button>
</div>
```
changements sur le .ts : 
```
import { Component, Inject } from '@angular/core';
import { MAT_DIALOG_DATA } from '@angular/material/dialog';
import { StoreService } from '../../../../app/core/services/store.service';
import { Observable, forkJoin, of } from 'rxjs';
import { catchError } from 'rxjs/operators';

@Component({
  selector: 'app-online-gateways-modal',
  templateUrl: './online-gateways-modal.component.html',
})
export class OnlineGatewaysModalComponent {
  constructor(
    @Inject(MAT_DIALOG_DATA) public data: any,
    private storeService: StoreService
  ) {}

  getStoreId(mac: string): string {
    let storeId = '';

    this.storeService.getStoreIdByGatewayMac(mac).subscribe(
      (response) => {
        if (response && response.storeId) {
          storeId = response.storeId;
        }
      },
      (error) => {
        console.error('Error fetching store ID:', error);
      }
    );

    return storeId;
  }

  getStoreInfo(mac: string): Observable<any> {
    return this.storeService.getStoreInfoByGatewayMac(mac);
  }

  getDataAsArray(): any[] {
    return Object.values(this.data);
  }
}
```
- [x] Résolution du crash de l'app du aux calss incessants à getStoreInfo() dans disconnectedGatewaysModal, passage de :
```
import {Component, Inject} from '@angular/core';
import {MAT_DIALOG_DATA} from '@angular/material/dialog';
import { Observable, catchError, of } from 'rxjs';
import { StoreService } from '../../../../app/core/services/store.service';

@Component({
  selector: 'app-gateways-disconnected-modal',
  templateUrl: './gateways-disconnected-modal.component.html',
})
export class GatewaysDisconnectedModalComponent {
  constructor(
    @Inject(MAT_DIALOG_DATA) public data: any,
    private storeService: StoreService
    ) {
  }
  
  getStoreInfo(mac: string): Observable<any> {
    console.log("passage dans getstoreinfo");
    
    return this.storeService.getStoreInfoByGatewayMac(mac).pipe(
      catchError(error => {
        console.error('Error fetching store info:', error);
        return of(null);
      })
    );
  }
}
```
à 
```
import { Component, Inject } from '@angular/core';
import { MAT_DIALOG_DATA } from '@angular/material/dialog';
import { Observable, of } from 'rxjs';
import { catchError } from 'rxjs/operators';
import { StoreService } from '../../../../app/core/services/store.service';

@Component({
  selector: 'app-gateways-disconnected-modal',
  templateUrl: './gateways-disconnected-modal.component.html',
})
export class GatewaysDisconnectedModalComponent {
  storeInfoMap: Map<string, Observable<any>> = new Map<string, Observable<any>>();

  constructor(
    @Inject(MAT_DIALOG_DATA) public data: any,
    private storeService: StoreService
  ) {
    this.initializeStoreInfoMap();
  }

  initializeStoreInfoMap(): void {
    this.data.forEach((value: any) => {
      if (!this.storeInfoMap.has(value.mac)) {
        this.storeInfoMap.set(value.mac, this.storeService.getStoreInfoByGatewayMac(value.mac).pipe(
          catchError(error => {
            console.error('Error fetching store info:', error);
            return of(null);
          })
        ));
      }
    });
  }

  getStoreInfo(mac: string): Observable<any> {
    return this.storeInfoMap.get(mac) || of(null);
  }
}
```
- [x] Push, PR, connexion à la prod, rebuild des containers et test pour voir si ça fonctionne
- [x] Review avec Franck & Anthony des diagrammes IT & d'architecture pour doc AWS
- [x] Remplissage du doc AWS


**22 Mars**
- [ ] Résolution de KP-886 Fix la redirection vers le store concerné depuis le lien dans les modales sur la page /cop
    - [x] Débug au console log : 
    ```
    redirectToStorePage(gatewayMac: string) 
    console.log("redirectToStorePage");
    console.log(gatewayMac);
    console.log(this.curativeKmoBoxes);
    ```
    retourne : 
    ```
    redirectToStorePage
    curative-kmoboxes-modal.component.ts:25 undefined
    curative-kmoboxes-modal.component.ts:26 
    (2) [{…}, {…}]
    0
    : 
    {mac: '01:00:5E:xx:xx:xx', checkoutNumber: 1, datetime: '2023-03-01T16:48:43.000Z', uptime: 32674114, closedTime: 7580, …}
    1
    : 
    {mac: '01:00:5E:xx:xx:35', checkoutNumber: 2, datetime: '2023-07-02T15:44:01.000Z', uptime: 103, closedTime: 5, …}
    length
    : 
    2
    [[Prototype]]
    : 
    Array(0)
    ```
    - [ ] Changement du fichier curative-checkouts.component.ts de : 
    ```
    import {Component, Inject} from '@angular/core';
    import {MAT_DIALOG_DATA, MatDialogRef} from '@angular/material/dialog';
    import {Router} from '@angular/router';
    import {StoreService} from '../../../../app/core/services/store.service';

    @Component({
    selector: 'app-curative-kmoboxes-modal',
    templateUrl: './curative-kmoboxes-modal.component.html',
    })
    export class CurativeKmoBoxesModalComponent {
    matchingStore: any;

    storeId = '';

    constructor(
        @Inject(MAT_DIALOG_DATA) public curativeKmoBoxes: any[],
        private router: Router,
        private dialogRef: MatDialogRef<CurativeKmoBoxesModalComponent>,
        private storeService: StoreService,
    ) {
    }

    redirectToStorePage(gatewayMac: string) {
        console.log("redirectToStorePage");
        console.log(gatewayMac);
        console.log(this.curativeKmoBoxes);
        
        
        this.matchingStore = this.curativeKmoBoxes.find(
        (kmoBox) => kmoBox.gatewayMac === gatewayMac
        );

        if (this.matchingStore) {
        this.storeService.getCriticalStoreByGatewayMac(this.matchingStore.gatewayMac).subscribe({
            next: (data) => {
            this.storeId = data.id;
            this.dialogRef.close();
            this.router.navigate(['/cop', this.storeId]);
            },
        });
        }
    }
    }
    ```
    à
    ```
    import { Component, Inject } from '@angular/core';
    import { MAT_DIALOG_DATA, MatDialogRef } from '@angular/material/dialog';
    import { Router } from '@angular/router';
    import { StoreService } from '../../../../app/core/services/store.service';
    import { KmoBoxInterface } from '../../../core/interfaces/kmo-box.interface';
    import { KmoBoxesService } from '../../../../app/core/services/kmo-boxes.service';

    @Component({
    selector: 'app-curative-kmoboxes-modal',
    templateUrl: './curative-kmoboxes-modal.component.html',
    })
    export class CurativeKmoBoxesModalComponent {
    matchingStore: any;
    storeId = '';

    constructor(
        @Inject(MAT_DIALOG_DATA) public curativeKmoBoxes: KmoBoxInterface[],
        private router: Router,
        private dialogRef: MatDialogRef<CurativeKmoBoxesModalComponent>,
        private storeService: StoreService,
        private kmoBoxesService: KmoBoxesService // Inject the KmoBoxesService
    ) {}

    redirectToStorePage(kmoBox: KmoBoxInterface) {
        console.log(kmoBox);
        
        if (kmoBox) {
        this.kmoBoxesService.getGatewayMacByKmoMac(kmoBox.mac).subscribe({
            next: (data) => {          
            const gatewayMac = data.gatewayMac;
            if (gatewayMac) {
                this.storeService.getCriticalStoreByGatewayMac(gatewayMac).subscribe({
                next: (storeData) => {
                    
                    this.storeId = storeData.id;
                    this.dialogRef.close();
                    this.router.navigate(['/cop', this.storeId]);
                },
                error: (error) => {
                    console.error('Error fetching store data:', error);
                }
                });
            } else {
                console.error('Gateway Mac not found');
            }
            },
            error: (error) => {
            console.error('Error fetching gateway mac:', error);
        });
        }
    }
    }
    ```
    - [ ] Changement du fichier curative-checkouts.component.html de : 
    ```
    <h1 mat-dialog-title>Caisses en Maintenance Curative</h1>
    <div mat-dialog-content>
    <ul>
        <li (click)="redirectToStorePage(kmoBox.gatewayMac)" *ngFor="let kmoBox of curativeKmoBoxes"
            class="hover:bg-gray-100 hover:cursor-pointer">
        <a>
            {{ kmoBox.mac }}
        </a>
        </li>
    </ul>
    </div>
    <div mat-dialog-actions>
    <button mat-button mat-dialog-close>Fermer</button>
    </div>
    ```
    à
    ```
    <h1 mat-dialog-title>Caisses en Maintenance Curative</h1>
    <div mat-dialog-content>
    <ul>
        <li (click)="redirectToStorePage(kmoBox)" *ngFor="let kmoBox of curativeKmoBoxes"
            class="hover:bg-gray-100 hover:cursor-pointer">
        <a>
            {{ kmoBox.mac }}
        </a>
        </li>
    </ul>
    </div>
    <div mat-dialog-actions>
    <button mat-button mat-dialog-close>Fermer</button>
    </div>
    ```