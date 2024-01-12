**8 Javnier**
- [x] Redémarrage atmos beta
    - [ ] KP 517, Fix le problème de la désactivation de store
        - [x] Trouver le code responsable
        Fonction ```deActivateStore(storeId)``` du fichier tab-store component
        - [ ] Récupérer l'objet envoyé / voir si ça colle à ce qu'attend l'endpoint
            - [x] Ajout d'un log sur le paramètre storeId : le storeId récupéré est le bon
            - [x] Ajout d'un log sur le store récupéré : le store récupéré est le bon
        - [x] Le rpoblème venait de l'objet retourné qui différait de l'objet attendu.
        Fix dans le fichier tab store component, methodes deactivate & reactivate store : 
        ```
        deActivateStore(storeId: string) {
            this.storeService.getStoreById(storeId).subscribe({
            next: (store) => {
                    
                    const updatedStore = {
                    id: store.id,
                    address: store.address,
                    city: store.city,
                    brand: store.brand,
                    postalCode: store.postalCode,
                    isStoreOpen: store.isStoreOpen,
                    isStoreActive: false,
                    openingHours: store.openingHours,
                    gatewayMac: store.gateway.mac,
                    };            
        
                    this.storeService.updateStore(storeId, updatedStore).subscribe({
                    next: () => {
                        this.targetStore = updatedStore;
                        this.getStores();
                    },
                    error: (err) => {
                        this.snackBar.open(err.message, 'OK');
                        throw new Error(err);
                    },
                    });
                },
                error: (err) => {
                    this.snackBar.open(err.message, 'OK');
                    throw new Error(err);
                },
                });
        }
        

        reActivateStore(storeId: string) {
            this.storeService.getStoreById(storeId).subscribe({
            next: (store) => {
                
                const updatedStore = {
                id: store.id,
                address: store.address,
                city: store.city,
                brand: store.brand,
                postalCode: store.postalCode,
                isStoreOpen: store.isStoreOpen,
                isStoreActive: true,
                openingHours: store.openingHours,
                gatewayMac: store.gateway.mac,
                }; 
                
                this.storeService.updateStore(storeId, updatedStore).subscribe({
                next: () => {
                    this.targetStore = updatedStore;
                    this.getStores();
                },
                error: (err) => {
                    this.snackBar.open(err.message, 'OK');
                    throw new Error(err);
                },
                });
            },
            error: (err) => {
                this.snackBar.open(err.message, 'OK');
                throw new Error(err);
            },
            });
        }
        ```
- [ ] Passage sur KP-680 fix le lien vers les stores à risque & kmo à risque


**10 janvier**
- [ ] Passage sur KP-680 fix le lien vers les stores à risque & kmo à risque
    - [x] Cassage de crâne avec gpt pour savoir d'où vient le problème
        - [x] la propriété gatewayMac est undefined dans le fronend, ce qui empêche d'avoir l'id du store et donc de rediriger correctement
        - [x] la propriété gatewayMac n'existe pas dans le DTO GET de /kmo-boxes, ajout: 
        ```
        import { Event } from '../../events/entities/event.entity';
        import { Gateway } from '../../gateways/entities/gateway.entity';

        export class GetLambdaKmoBoxDto {
        mac: string;
        checkoutNumber: number;
        datetime: string;
        uptime: number;
        closedTime: number;
        firmwareVersion: number;
        isOutOfOrder: boolean;
        maintenanceLevel: string;
        lastMaintenance: string;
        events: Event[];
        gateway: Gateway;
        gatewayMac: string;
        }
        ```
        - [x] La propriété n'existe pas dans la méthode associée du kmoBoxService du backend, ajout : 
        ```
        async getKmoBoxesWithEvents(): Promise<GetLambdaKmoBoxDto[]> {
            const kmoBoxes = await KmoBox.createQueryBuilder('kmoBox')
            .leftJoinAndSelect('kmoBox.events', 'event')
            .addSelect('kmoBox.gatewayMac')
            .leftJoinAndSelect('kmoBox.gateway', 'gateway')
            .addSelect('gateway.mac')
            .getMany();
            console.log(kmoBoxes);
            

            for (const kmoBox of kmoBoxes) {
            if (kmoBox.lastMaintenance != null) {
                const eventsAfterMaintenance = await Event.createQueryBuilder('event')
                .where('event.kmoBox.mac = :kmoBoxId', { kmoBoxId: kmoBox.mac })
                .andWhere('event.datetime > :lastMaintenance', {
                    lastMaintenance: kmoBox.lastMaintenance,
                })
                .getMany();

                kmoBox.events = eventsAfterMaintenance;
            }
            }
            const lambdaBox: GetLambdaKmoBoxDto[] = kmoBoxes.map((item) => ({
            ...item,
            gatewayMac: item.gateway.mac
            }));
            return lambdaBox;
        }
        ```
        - [x] Clean des nombreux console logs
        - [x] Lint
        - [x] Tests
        - [x] Commit & push
        - [x] PR
        - [x] mise en prod du fix
- [ ] Penser à enlever la console log ```console.log(response.payload);``` de cop store detail
- [ ] Penser à enlever le {{ kmoBox.gatewayMac }} de la modlae kmobox preventive


**11 janvier**
- [ ] Passage sur KP-687 ajouter données de stores désactivés à la modale de /cop
- [ ] Passage sur KP-688 mettre un code couleur aux stores désactivés de /cop


**12 Janvier**
- [ ] Poursuite de KP-687 ajouter données de stores désactivés à la modale de /cop
    - [ ] Créer une route backend qui GET les stores by isActive
- [ ] Poursuite de KP-688 mettre un code couleur aux stores désactivés de /cop
