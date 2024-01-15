**15 janvier**
- [x] Passage sur KP-688 mettre un code couleur aux stores désactivés de /cop
    - [x] Ajout dans le html de cop store details de :
    ```        
    <tr *matRowDef="let row; columns: displayedColumns;" mat-row [ngClass]="{'inactive-store-row': !row.isStoreActive}"></tr>
    ```
    dans la < table > :
    ```
    ...

        <ng-container matColumnDef="gatewaysState">
          <th *matHeaderCellDef mat-header-cell mat-sort-header>Statut de gateway</th>
          <td *matCellDef="let element" mat-cell routerLink="/cop/{{element.id}}">
            <span class="mr-1 inline-block">{{element.gateway.mac}}</span>
            <span class="align-middle">
              <mat-icon *ngIf="isGatewayDisconnected(element?.gateway.mac)" class="text-red-600">wifi_off</mat-icon>
              <mat-icon *ngIf="!isGatewayDisconnected(element?.gateway.mac)"
                        class="text-green-600">wifi_statusbar_4_bar</mat-icon>
            </span>
          </td>
        </ng-container>


        <tr *matHeaderRowDef="displayedColumns" mat-header-row></tr>
        <tr *matRowDef="let row; columns: displayedColumns;" mat-row [ngClass]="{'inactive-store-row': !row.isStoreActive}"></tr>
    </table>
    ```
- [x] Commit, push & PR
- [x] Merge
- [x] Résolution de bug KP 689 : Fix les imports & path des components angular
    - [x] Rename du dossier ... copy qui causait problème
    - [x] Push sur develop
- [x] Mise en prod
    - [x] Connexion au serveur ```ssh -i "Predict-Beta.pem" ubuntu@ec2-3-76-238-223.eu-central-1.compute.amazonaws.com```
    - [x] Arrêt des containers
    - [x] ```git pull develop```
    - [x] ```sudo  docker compose --env-file .env.prod -f docker-compose-prod.yml up --always-recreate-deps --build```
- [ ] Passage sur KP-665 : Rajouter dynamiquement le nombre de caisses d'un magasin au tableau en reprenant la route /GET store/paginate
    - [x] Demande à gpt : 
    ```
    knowing that a store has a "gatewayMac" property that corresponds to a Gateway's "mac" property, 
    and that every KmoBox has a "gatewayMac" property, i can know how many KmoBoxes a stores has by filtering all the kmoBoxes that share their "gatwayMac" 
    property with the "gatwayMac" property of the store itself.

    Now what i want is to create a GET route that would return the number of KmoBoxes a store has 
    when submitted its "id" property, can you help me do that ?
    ```
    - [x] Suggestion de gpt : 
    ```
    @Get('kmoBoxCount/:id')
    async getKmoBoxCount(@Param('id') id: string): Promise<number> {
        try {
        const kmoBoxCount = await this.storesService.getKmoBoxCountByStoreId(id);
        return kmoBoxCount;
        } catch (error) {
        // Handle errors appropriately
        throw new HttpException(error.message, 500);
        }
    }
    ```
    &
    ```
    async getKmoBoxCountByStoreId(storeId: string): Promise<number> {
        try {
        const store = await this.storeRepository.findOne({
            where: { id: storeId },
            relations: ['gateway', 'gateway.kmoBoxes'],
        });

        if (!store) {
            throw new HttpException(`Store with ID ${storeId} not found.`, 404);
        }

        const kmoBoxes = store.gateway.kmoBoxes.filter(
            (kmoBox) => kmoBox.gatewayMac === store.gatewayMac,
        );

        return kmoBoxes.length;
        } catch (error) {
        throw new HttpException(error.message, 500);
        }
    }
    ```
    Risque de surcharge de la page /cop & /administration
