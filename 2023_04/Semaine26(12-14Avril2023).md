**6 Avril**
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
            - [X]Ajout de : 
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
            - [X]Ajout de : 
            ```
            this.targetStore = targetStore;
            ```
            dans le next() => {} de la méthode reActivateStore() dans le fichier administration.component.ts
    - [ ] Restriction de l’accès à la page aux users ayant le rôle (pas nécessaire, la deletion (désactivation) ne sera accesible qu'aux devs cop)
    - [ ] Rendre les magasins désactivés en grisé et changer le vert par du gris
    - [ ] Lier keycloak au service de mail ses aws et connecter au système de mot de passe oublié