**08 Avril**
- [ ] KP-889 Fix l'affichage des magasins à risque dans la modale sur /cop
    - [ ] Modifictation de la méthode fetchImpactedStores() : 
    ```
    fetchImpactedStores() {
        this.kmoBoxesService.getAllKmoBoxes().subscribe({
        next: (response: KmoBoxInterface[]) => {
            const kmoBoxMacs = response.map(kmoBox => kmoBox.mac);
            console.log(kmoBoxMacs);
            
            const gatewayMacObservables = kmoBoxMacs.map(kmoBoxMac =>
            this.eventService.getGatewayMacByKmoBoxMac(kmoBoxMac).pipe(
                catchError(error => {
                console.error(`Error fetching gateway mac for kmoBoxMac: ${kmoBoxMac}`, error);
                return of(null);
                })
            )
            );

            console.log(gatewayMacObservables);
            

            forkJoin(gatewayMacObservables).subscribe({
            next: (gatewayMacs: any[]) => {
                const filteredGatewayMacs = gatewayMacs.filter(gatewayMac => gatewayMac !== null).flat();
                console.log(filteredGatewayMacs);
                
                const storeObservables = filteredGatewayMacs.map(gatewayMac =>
                this.storeService.getCriticalStoreByGatewayMac(gatewayMac).pipe(
                    catchError(error => {
                    console.error(`Error fetching store for gatewayMac: ${gatewayMac}`, error);
                    return of(null);
                    })
                )
                );
                console.log(storeObservables);
                
                    
                // Subscribe to all storeObservables using forkJoin
                forkJoin(storeObservables).subscribe({
                next: (stores: (StoreInterface | null)[]) => {
                    // Filter out null values and assign to impactedStores
                    this.impactedStores = stores.filter(store => store !== null) as StoreInterface[];
                    console.log(this.impactedStores);
                    
                    // Update the impactedStoresNumber
                    this.impactedStoresNumber = this.impactedStores.length;
                    console.log(this.impactedStoresNumber);
                },
                error: (err) => {
                    console.error('Error fetching stores by gatewayMac', err);
                }
                });
            },
            error: (err) => {
                console.error('Error fetching gatewayMacs for KmoBoxes', err);
            }
            });
        },
        error: (err) => {
            console.error('Error fetching KmoBoxes', err);
        }
        });
    } 
    ```
    v2 de la fonction qui ne fonctionne pas ; impactedStores renvoie un array vide
    - [x] Création à la place d'une route back qui récupère tous les stores qui ont une caisse pas op, store service : 
    ```
    async findStoresWithNonOperationalKmoBoxes(): Promise<Store[]> {
        try {
        const stores = await Store.createQueryBuilder('store')
            .innerJoinAndSelect('store.gateway', 'gateway')
            .innerJoinAndSelect('gateway.kmoBoxes', 'kmoBox', 'kmoBox.maintenanceLevel <> :maintenanceLevel', { maintenanceLevel: 'Operational' })
            .getMany();

        return stores;
        } catch (error) {
        throw new Error(`Error finding stores with non-operational KmoBoxes: ${error.message}`);
        }
    }
    ```
    store controller : 
    ```
    @Get('with-non-operational-kmoboxes')
    async findStoresWithNonOperationalKmoBoxes(): Promise<Store[]> {
        return this.storesService.findStoresWithNonOperationalKmoBoxes();
    }
    ```
    - [x] Ajout au store service front : 
    ```
    fetchImpactedStores(): Observable<StoreInterface[]> {
        return this.httpclient.get<StoreInterface[]>(environment.apiUrl + '/stores/with-non-operational-kmoboxes');
    }
    ```
    - [x] Assignation de la liste à la variable impactedStores dans cop store details component : 
    ```
    fetchImpactedStores() {
        this.storeService.fetchImpactedStores().subscribe({
        next: (impactedStores) => {
            this.impactedStores = impactedStores;        
        },
        error: (err) => {
            console.error('Error fetching impacted stores', err);
        },
        });
    } 
    ```
    - [x] Assignation des impactedStores à la modale