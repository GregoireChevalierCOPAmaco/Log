**12 Avril**
- [x] Catch-up mails
- [x] Résolution de conflit mineur PR KP-175 & merge
- [ ] Poursuite keycloak
    - [ ] KP-139 : Refacto suppression & tests
    - [ ] Écriture des tests
        - [x] Test de la désactivation du store
            - [X] Ajout du code : 
            ```
              it('should deactivate a store correctly', () => {
                const storeId = '123';
                const store = { id: storeId, isStoreActive: true };
                jest.spyOn(storeService, 'getStoreById').mockReturnValue(of(store));
                jest.spyOn(storeService, 'updateStore').mockReturnValue(of({}));
                component.deActivateStore(storeId);
                expect(component.targetStore).toEqual({ ...store, isStoreActive: false });
            });
            ```
            - [X] Modification de l'appel à storeService : 
            ```
            import { StoreService } from '../../core/services/store.service'
            ```
            - [X] Ajout de : 
            ```
            this.targetStore = targetStore;
            ```
            dans le next() => {} de la méthode deActivateStore() dans le fichier administration.component.ts
        - [x] Test de la reactivation du store
            - [X] Ajout du code : 
            ```
              it('should reactivate a store correctly', () => {
                const storeId = '234';
                const store = { id: storeId, isStoreActive: false };
                jest.spyOn(storeService, 'getStoreById').mockReturnValue(of(store));
                jest.spyOn(storeService, 'updateStore').mockReturnValue(of({}));
                component.reActivateStore(storeId);
                expect(component.targetStore).toEqual({ ...store, isStoreActive: true });
            });
            ```
            - [X] Ajout de : 
            ```
            this.targetStore = targetStore;
            ```
            dans le next() => {} de la méthode reActivateStore() dans le fichier administration.component.ts
        - [x] Test de la variable isLogged
            - [x] Ajout du code :
            ```
              describe('isLogged', () => {
                beforeEach(() => {
                component.isLogged = true;
                });
            
                test('if the isLogged variable is true after Keycloak authentication', async () => {
                expect(component.keycloak).toBeDefined();
                expect(component.isLogged).toBeTruthy();
                });
                
                describe('when resetting isLogged', () => {
                beforeEach(() => {
                    component.isLogged = false;
                });
                
                test('should be false before Keycloak authentication', async () => {
                    expect(component.isLogged).toBeFalsy();
                });
                });
            });
            ```
        - [x] Tester le getStores
            - [x] Ajout du code : 
            ```
            describe('StoreService', () => {

            it('should retrieve stores', () => {
                const stores: StoreInterface[] = [
                {
                    id: '1',
                    address: '123 Main St',
                    city: 'Anytown',
                    brand: 'Acme',
                    postalCode: '12345',
                    isStoreOpen: true,
                    numberOfCheckouts: 4,
                    isStoreActive: true,
                    openingHours: {
                    mon: ['mon', '09:00-18:00'],
                    tue: [ 'jaja','09:00-18:00'],
                    wed: [ 'jaja','09:00-18:00'],
                    thu: [ 'jaja','09:00-18:00'],
                    fri: [ 'jaja','09:00-18:00'],
                    sat: [ 'jaja','10:00-16:00'],
                    sun: [ 'jaja','10:00-16:00'],
                    },
                    gateway: {
                    mac: '00:11:22:33:44:55',
                    datetime: '2022-04-12T14:30:00Z',
                    diskUsage: 75,
                    temperatureCpu: 60,
                    freeRam: 512,
                    kmoBoxes: [],
                    },
                },
                ];

                const jaja = stores;

                storeService.getStores(1, 10).subscribe((response: any) => {
                expect(response).toEqual(jaja);
                });

                // const req = httpTestingController.expectOne(`${environment.apiUrl}/stores/paginate?page=1&limit=10`);
                // expect(req.request.method).toEqual('GET');
                // req.flush(jaja);   cherche les données en réel
            });
            });
            ```
            - [x] Passer au lint
            - [x] Passer au tests
            - [x] Commit & push
            - [x] PR & résolution de conflits
        - [ ] Modifier le jira créer une user story tests unitaires
        - [ ] Associer un end to end à chaque user sotry/fonctionnalité voulue
        - [ ] tester les dépendances ?!?
    - [ ] Test end to end pour valider la KP-177
        - [ ] app.component.spec.ts
            - [ ] Test de la connexion à keycloak
            voir cycle en v & test de recette
    - [ ] Restriction de l’accès à la page aux users ayant le rôle (pas nécessaire, la deletion (désactivation) ne sera accesible qu'aux devs cop)
    - [x] Rendre les magasins désactivés en grisé et changer le vert par du gris
    - [ ] Lier keycloak au service de mail ses aws et connecter au système de mot de passe oublié

**13 Avril**
- [x] Refacto de détails horaire & typo jaja admin spec test file
- [ ] Poursuite Predict
    - [x] KP-176 : tests
        - [x] Reprise des tests
        - [x] Refacto pour raise une erreur si getbyId() fail :
        ```
        deActivateStore(storeId: string) {
            this.storeService.getStoreById(storeId).subscribe({
            next: (store) => {
                const targetStore: StoreInterface = {
                ...store,
                id: storeId,
                isStoreActive: false,
                };
                this.storeService.updateStore(storeId, targetStore).subscribe({
                next: () => {
                    this.targetStore = targetStore;
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
        - [x] Résolution des problèmes de nullinjectorerror R3 mon cul avec provider {provide : }
    - [ ] KP-177 : test end to end désactivation
        - [x] Check de (https://fr.wikipedia.org/wiki/Cycle_en_V)
        - [ ] Récupération de develop
            - [x] pull
            - [x] Re-run des tests pour être sûr de repartir d'une clean sheet
            - [ ] Vérification que tout marche comme attendu
            - [ ] Résolution des problèmes
                - [x] Tout brûler et reconstruire de zéro
                - [ ] Suppression des containers
                - [ ] Suppression des images
                - [ ] Suppression des volumes
                - [ ] docker compose --env-file .env.dev -f docker-compose.dev.yml up --renew-anon-volumes --always-recreate-deps --build
                - [ ] Réinjecter le dernier realm
                - [ ] Repeupler la database
        - [ ] Création et checkout de branche 177
        - [ ] Tester que la page existe
        - [ ] Tester que le bouton existe
        - [ ] Tester que le bouton appelle la fonction désactivation
        - [ ] Tester que la désactivation retourne le bon store
        - [ ] Tester que la désactivation update bien le store
        - [ ] Tester que le store est désactivé en base de données
        - [ ] Tester que l'affichage correspond aux données en base
