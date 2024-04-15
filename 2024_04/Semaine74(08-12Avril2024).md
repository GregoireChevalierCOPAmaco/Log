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


**10 Avril**
- [x] KP-889 Fix l'affichage des magasins à risque dans la modale sur /cop
    - [x] KP-915 Créer une route API pour récupérer les stores qui ont au moins une caisse en maintenance préventive
    - [x] Création d'une route back qui récupère tous les stores qui ont une caisse preventive, store controller : 
    ```
    @Get('with-preventive-kmoboxes')
    async findStoresWithPreventiveKmoBoxes(): Promise<Store[]> {
        return this.storesService.findStoresWithPreventiveKmoBoxes();
    }
    ```
    - [x] Création de la méthode dans le service: 
    ```
    async findStoresWithPreventiveKmoBoxes(): Promise<Store[]> {
        try {
        const stores = await Store.createQueryBuilder('store')
            .innerJoinAndSelect('store.gateway', 'gateway')
            .innerJoinAndSelect(
            'gateway.kmoBoxes',
            'kmoBox',
            'kmoBox.maintenanceLevel = :maintenanceLevel',
            { maintenanceLevel: 'Preventive' },
            )
            .getMany();

        return stores;
        } catch (error) {
        throw new Error(
            `Error finding stores with preventive KmoBoxes: ${error.message}`,
        );
        }
    }
    ```
    - [x] Assignation de la liste à la variable preventiveStores dans cop store details component : 
    ```
    openMediumRiskStoresModalComponent() {
      this.dialog.open(MediumRiskStoresModalComponent, {
        data: this.mediumRiskStores
      });
      this.fetchMediumRiskStores();
    }
    ```
- [x] KP-914 Fix l'affichage des magasins critiques dans la modale sur /cop
    - [x] KP-919 Créer une route API pour récupérer les stores qui ont au moins une caisse en maintenance curative
    - [x] Création d'une route back qui récupère tous les stores qui ont une caisse preventive, store controller : 
    ```
    @Get('with-curative-kmoboxes')
    async findStoresWithCurativeKmoBoxes(): Promise<Store[]> {
        return this.storesService.findStoresWithCurativeKmoBoxes();
    }
    ```
    - [x] Création de la méthode dans le service : 
    ```
    async findStoresWithCurativeKmoBoxes(): Promise<Store[]> {
        try {
        const stores = await Store.createQueryBuilder('store')
            .innerJoinAndSelect('store.gateway', 'gateway')
            .innerJoinAndSelect(
            'gateway.kmoBoxes',
            'kmoBox',
            'kmoBox.maintenanceLevel = :maintenanceLevel',
            { maintenanceLevel: 'Curative' },
            )
            .getMany();

        return stores;
        } catch (error) {
        throw new Error(
            `Error finding stores with preventive KmoBoxes: ${error.message}`,
        );
        }
    }
    ```
    - [x] Assignation de la liste à la variable criticalStores dans cop store details component : 
    ```
    openCriticalStoresModalComponent() {
        this.dialog.open(CriticalStoresModalComponent, {
        data: this.criticalStores
        });
        this.fetchCriticalStores();
    }
    ```
- [x] Réunion Okta concurrent keycloak
- [x] commit, push, PR, et resolve conflits
- [ ] Redémarrage prod
    - [x] Connexion à l'instance
    - [x] Arrêt des containers
    - [x] git pull origin develop
    - [ ] Rebuild des containers et vérification sur le site en prod
- [ ] Mise à jour 


**11 Avril**
- [ ] KP-868 Caisse ouverte - Alors qu'elle semble fermée
- [ ] KP-869 Etat des caisses - nombre caisses fonctionnelles
- [x] KP-921 Optimiser les fonctions de recherche de stores ayant des kmo non-opérationnelles
    - [x] Inscription à Mistral AI
    - [x] Demande : 
    ```
    ... fourniture des fichiers ...
    Pour les méthodes telles que fetchMediumRiskStores(), fetchCriticalStores() ou encore fetchImpactedStores(), et l'assignation de leurs variables respectives, existe-t-il une manière plus efficace/optimale pour effectuer ces méthodes, et si oui réécris les parties concernées du component s'il te plaît
    ```
    - [x] Réponse de Mistral : 
    ```
    Pour optimiser les méthodes fetchMediumRiskStores(), fetchCriticalStores() et fetchImpactedStores(), vous pouvez utiliser forkJoin de RxJS pour effectuer plusieurs requêtes HTTP en parallèle. Cela permettra de réduire le temps d'attente global pour ces requêtes et d'améliorer les performances de votre application.

    Voici comment vous pouvez réécrire les parties concernées du component en utilisant forkJoin :

    Importez forkJoin et Observable en ajoutant ces lignes en haut de votre fichier :

    import { forkJoin, Observable } from 'rxjs';
    Remplacez les méthodes fetchMediumRiskStores(), fetchCriticalStores() et fetchImpactedStores() par la méthode fetchStoreData() suivante :

    fetchStoreData(): void {
    forkJoin({
        mediumRiskStores: this.storeService.fetchMediumRiskStores(),
        criticalStores: this.storeService.fetchCriticalStores(),
        impactedStores: this.storeService.fetchImpactedStores(),
    }).subscribe(
        (results) => {
        this.mediumRiskStores = results.mediumRiskStores;
        this.criticalStores = results.criticalStores;
        this.impactedStores = results.impactedStores;
        },
        (error) => {
        console.error('Error fetching store data', error);
        },
    );
    }
    Dans la méthode ngOnInit(), remplacez les appels aux méthodes fetchMediumRiskStores(), fetchCriticalStores() et fetchImpactedStores() par un appel à fetchStoreData() :

    ngOnInit(): void {
    // ...
    this.fetchStoreData();
    // ...
    }
    Avec cette approche, les trois requêtes seront effectuées en parallèle, et les résultats seront traités une fois que toutes les requêtes auront été résolues. Cela devrait améliorer les performances de votre application, en particulier si les requêtes individuelles prennent du temps à être résolues.
    ```
- [x] Optimiser les fonctions de cop-store details pour éviter les memory leaks on destroy
    - [x] Importer subject et takeuntil
    ```
    import { Subject, takeUntil } from 'rxjs';
    ```
    - [x] Créer un subject qui va "s'activer" onDestroy
    ```
    private readonly destroy$ = new Subject<void>();
    ```
    - [x] Insérer : ```.pipe(takeUntil(this.destroy$))``` dans toutes les requêtes avant ```.subscribe```
- [x] Optimiser les fonctions de cop-dialog pour éviter les memory leaks on destroy (KP-923)
    - [x] Importer subject et takeuntil
    ```
    import { Subject, takeUntil } from 'rxjs';
    ```
    - [x] Créer un subject qui va "s'activer" onDestroy
    ```
    private readonly destroy$ = new Subject<void>();
    ```
    - [x] Insérer : ```.pipe(takeUntil(this.destroy$))``` dans toutes les requêtes avant ```.subscribe```
- [ ] KP-916 Faire passer le fetch des magasins à risque / à surveiller / critiques dans une routine de fond plutôt qu'au clic de la modale
    - [x] Demande à mistral : 
    ```
    in my component, i use methods such as fetchCriticalStores, fetchImpactedStores, fetchMediumRiskStores & fetchAllStores, which i use to populate datas into modal components.

    I wanted to know if there is a way to precalculate it and only access to the data, and if there is a way, if it is more optimal than it is right now
    ```
    - [ ] Réponse de mistral : 
    ```
    Yes, there is a way to precalculate the data and store it in a service or a store (like Redux, NgRx, or Akita) to avoid making repeated API calls. This approach can be more optimal, especially if the data is large and the API calls are frequent.

    Here's a simplified example of how you can do it:

    Precalculate and store data in a service:
    You can create a service, let's call it DataService, where you can precalculate and store your data.


    @Injectable({
    providedIn: 'root'
    })
    export class DataService {
    criticalStores: StoreInterface[] = [];
    impactedStores: StoreInterface[] = [];
    mediumRiskStores: StoreInterface[] = [];
    totalStores: number = 0;

    constructor(private storeService: StoreService) {}

    fetchAndStoreData() {
        this.storeService.fetchCriticalStores().subscribe(criticalStores => {
        this.criticalStores = criticalStores;
        });

        this.storeService.fetchImpactedStores().subscribe(impactedStores => {
        this.impactedStores = impactedStores;
        });

        this.storeService.fetchMediumRiskStores().subscribe(mediumRiskStores => {
        this.mediumRiskStores = mediumRiskStores;
        });

        this.storeService.getAllStores().subscribe(totalStores => {
        this.totalStores = totalStores.length;
        });
    }
    }
    Access stored data from your component:
    Then, in your component, you can inject DataService and access the precalculated data.


    export class HomeCopComponent implements AfterViewInit, OnInit {
    constructor(private dataService: DataService) {}

    ngOnInit(): void {
        this.dataService.fetchAndStoreData();
    }

    get criticalStores() {
        return this.dataService.criticalStores;
    }

    get impactedStores() {
        return this.dataService.impactedStores;
    }

    get mediumRiskStores() {
        return this.dataService.mediumRiskStores;
    }

    get totalStores() {
        return this.dataService.totalStores;
    }
    }
    This way, you're only making the API calls once when the HomeCopComponent is initialized, and then you're accessing the stored data for the rest of the component's lifecycle.

    Please note that this is a simplified example. In a real-world application, you might want to handle loading states, errors, and updates more gracefully. Also, depending on the size and complexity of your application, you might want to use a more sophisticated state management solution, like NgRx or Akita.

    As for whether this approach is more optimal, it depends on the specifics of your application. If you're making the same API calls frequently, then yes, precalculating and storing the data can be more efficient. However, if the data changes frequently on the server side, you might end up with stale data in your application. In that case, you might need to implement a strategy for updating the stored data, like polling or using web sockets.
    ```


**12 Avril**
Récupération