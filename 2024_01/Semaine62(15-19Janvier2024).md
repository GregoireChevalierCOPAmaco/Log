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



**16 janvier**
- [ ] Passage sur KP-665 Rajouter dynamiquement le nombre de caisses d'un magasin au tableau en reprenant la route /GET store/paginate
    - [x] Update requête à gpt : 
    ```
    knowing that a store does not have a numberOfCheckouts property but updateStoreDTO does, and
    knowing that a store has a "gatewayMac" property that corresponds to a Gateway's "mac" property, and that every KmoBox has a "gatewayMac" property, i can know how many KmoBoxes a stores has by filtering all the kmoBoxes that share their "gatwayMac" property with the "gatwayMac" property of the store itself.
    There is no gateway.kmoBoxes.length property, so the way to go would be to iterate on kmoBoxes to see which have a "gatewayMac" property that corresponds to the "gatewayMac" property of the store

    Now what i want is to update the @Get('/paginate/') route for it to  return the number of KmoBoxes a store has when submitted its "id" property, and append it to the object returned as numberOfCheckouts

    can you help me do that ?
    ```
    - [x] Application de la réponse : 
        - [x] store controller : 
        ```
         @Get('/paginate/')
            async getAll(
                @Query('page', new DefaultValuePipe(1), ParseIntPipe) page: number,
                @Query('limit', new DefaultValuePipe(10), ParseIntPipe) limit: number,
                @Query('searchTerm') searchTerm: string,
            ): Promise<Pagination<Store | UpdateStoreDto>> {
            //   return this.storesService.findAllPaginate({ page, limit }, searchTerm);
            // }
                const storesPaginated = await this.storesService.findAllPaginate(
                { page, limit },
                searchTerm,
                );

                const storesWithKmoBoxes: UpdateStoreDto[] = [];

                for (const store of storesPaginated.items) {
                const numberOfCheckouts = await this.storesService.getNumberOfKmoBoxes(store.id);
                const storeWithKmoBoxes: UpdateStoreDto = {
                    ...store,
                    numberOfCheckouts,
                };
                storesWithKmoBoxes.push(storeWithKmoBoxes);
            }

            return { ...storesPaginated, items: storesWithKmoBoxes };
        }
        ```
        - [x] store service : 
        ```
        async getNumberOfKmoBoxes(storeId: string): Promise<number> {
            try {
            const store = await this.storeRepository.findOne({
                where: { id: storeId },
                relations: ['gateway', 'gateway.kmoBoxes'],
            });

            if (!store) {
                throw new HttpException(`Store with ID ${storeId} not found`, 404);
            }

            
            const kmoBoxes = store.gateway.kmoBoxes.filter(
                (kmoBox) => kmoBox.gatewayMac === store.gatewayMac,
            );

            return kmoBoxes.length;
            } catch (e) {
            throw new HttpException(e, 500);
            }
        }
        ```
        - [x] Résolution de l'erreur créée : 
        ```
        Property 'filter' does not exist on type 'KmoBox'.

        for the line  : 
            const kmoBoxes = store.gateway.kmoBoxes.filter(
        in  the getNumberOfKmoBoxes method
        ```
        ajout de  : 
        ```
        async getNumberOfKmoBoxes(storeId: string): Promise<number> {
            try {
            const store = await this.storeRepository.findOne({
                where: { id: storeId },
                relations: ['gateway', 'gateway.kmoBoxes'],
            });

            if (!store) {
                throw new HttpException(`Store with ID ${storeId} not found`, 404);
            }

            const kmoBoxes: KmoBox[] = (store.gateway.kmoBoxes as unknown) as KmoBox[];
            const filteredKmoBoxes = kmoBoxes.filter(
                (kmoBox) => kmoBox.gateway.mac === store.gatewayMac,
            );

            return filteredKmoBoxes.length;
            } catch (e) {
            throw new HttpException(e, 500);
            }
        }
        ``` 
    - [x] Écriture du code sous nouvelle branche
        - [x] ```git checkout -b fix/KP-665_adjust_paginate_route_toGet_numberOfCheckouts```
        - [x] lint
        - [ ] test
        - [ ] push
        - [ ] PR
        - [ ] mise en prod