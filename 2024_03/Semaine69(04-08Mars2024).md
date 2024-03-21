**04 Mars**
- [x] Checkout develop demo eurocis
    - [ ] Résolution problème events controller "getLatestEvent24hour"
- [ ] Faire le tri des méthodes à garder de la branche develop_demoEuroCIS : 64 files changed. À garder : 
    - [x] fichier : chart event dto
    tout le ficher
    - [ ] fichier : events.service.ts
    lignes 50,54, 408, 412, 420, 424, 432, 436, 444, 448 456, 460, 621,629, 659, 665, 
    ```
    const currentDatetimeUTC = new Date().toISOString();
    ...
    .andWhere('event.datetime < :currentDatetimeUTC', { currentDatetimeUTC })
    ```
    - [ ] fichier : app-routing.module.ts
    tout le fichier
    - [ ] fichier : app.module.ts
    tout le fichier
    - [ ] fichier : chart-event.ts
    tout le fichier
    - [ ] fichier : event.service.ts
    lignes 
    ```
     getNumberOfEventinLastday(StoreId: string) :any {
        return this.httpclient.get(`${this.apiUrl}/latest-event24hour/${StoreId}`)
    }
    ```
    - [ ] fichier : kmo-boxes.service.ts
    lignes 
    ```
    setMaintenanceLevelToCurative(id: string) {
        return this.httpclient.patch(`${this.apiUrl}${id}/set-curative-maintenance`, {});
    }
    ```
    - [ ] fichier : cop-dialog.component.ts
    Refaire les ta=raductions, garder le switch case
    ```
    ```
    - [ ] fichier : /cop.component.spec.ts
    lignes 14 & 98
    ```
    import { MatSnackBar } from '@angular/material/snack-bar';
    ...
    MatSnackBar
    ```
    - [ ] fichier : 
    lignes 
    ```
    ```
    - [ ] fichier : 
    lignes 
    ```
    ```
- [ ] Changement thème keycloak
    - [x] Capture d'écran sur le site cop
    - [ ] Ajout dans le dossier 
    - [ ] Modification du docker compose
    - [ ] Commit
    - [ ] Push
    - [ ] Redéploiement 
    - [ ] Modifs sur l'interface d'administration KC


**05 Mars**
- [ ] merge de local develop eurocis dans local develop
    - [ ] ```git pull origin develop```
    - [ ] checkout
    - [ ] merge
    - [ ] resolve conflicts
    - [ ] push
- résolution de la merde créée par un merge impromptu de demo eurocis dans develop
- [ ] Réunion aws