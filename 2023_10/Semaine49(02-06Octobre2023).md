**02 Octobre**
- [x] Check des mails
- [ ] Mise à jour Jira
    - [x] Détail de KP-510
    - [ ] Review des tickets à faire 
- [x] KP-452 : Diagnostique et update Jira
- [ ] Poursuite des tests fonctionnels
    - [x] Capture du KP-514 à la modification de store
- [ ] Aide à Jacques pour cibler d'où vient le problème d'events qui ne rentrent pas en base
- [x] Reprise de db pour fix désassignation store modifié
- [x] Reprise de db pour assigner un magasin à chaque kmobox
- [x] Réunion SAV, rebelote
    - Fred Liger & Alex Wandres tablettes ok mises à jour sur site
    - SAV relancé
    - Magasin FR0544
    - Faire liste de magasins récents à intégrer dans la base & actualiser en fonction
    - Tablettes vont revenir pour être mises à jour
    - [x] Envoyer liste tablettes à Bonetto
- [ ] Réunion avancement prédict


**03 Octobre**
- [x] Check des mails
- [ ] Mise à jour Jira
    - [ ] Ajout ticket KP-521 fix reassign gw
        - [x] store.service.ts changement de : 
        ```
        changeGateway(id: string ,mac: string){
            return this.httpclient.get(this.apiUrl +'/reassignGateWay/'+id + `?mac=${mac}`)
        }
        ```
        à
        ```
            changeGateway(id: string ,mac: string, body: any){
            return this.httpclient.patch(this.apiUrl +'/reassignGateWay/'+id + `?mac=${mac}`, body)
        }
        ```
        - [x] change-gateway-assignation.component.ts changement de :
        ```
        reassignGateway() {

            this.storeService.changeGateway(this.data.id, this.gatewayForm.get('mac')?.value).subscribe(() => {
            this.snackBar.open('Réassignation réussie','',{duration: 3000})
            ...
        ```
        à 
        ```
        reassignGateway() {
            const body = {
            mac: this.gatewayForm.get('mac')?.value,
            };

            this.storeService.changeGateway(this.data.id, this.gatewayForm.get('mac')?.value, body).subscribe(() => {
            this.snackBar.open('Réassignation réussie','',{duration: 3000})
            ...
      ```
      - [x] Rebuild des docker et test, erreur 500 due à une violation de contrainte : duplicate key value violates unique constraint "REL_e36699fed6e6a91d65a0e9516b"
      - [ ] Changement d'approche
        - [ ] Modification du fichier store.service.ts (front)
        - [ ] Modification du fichier change-gateway-assignation.component.ts
        - [ ] Modification du fichier change-gateway-assignation.component.html
        - [ ] Modification du fichier stores.service.ts (back)


**04 Octobre**
- [ ] Mise à jour Jira
    - [ ] Ajout ticket KP-521 fix reassign gw
        - [x] Modification du fichier store.service.ts (front)
        ```
        changeGateway(id: string, mac: string) {
            const queryParams = new HttpParams().set('mac', mac);
            return this.httpclient.patch(`${this.apiUrl}/reassignGateWay/${id}`, {}, { params: queryParams });
        }
        ```
        - [x] Modification du fichier stores.service.ts (back)
        ```
        async getGatewayMacForStore(id: string): Promise<string | null> {
            const store = await this.storeRepository.findOne(id);
            return store ? store.gatewayMac : null;
        }

        async getGatewayByMac(mac: string): Promise<Gateway | null> {
            const gateway = await this.gatewayRepository.findOne({ where: { mac } });
            return gateway || null;
        }
        

        async clearStoreGatewayMac(id: string): Promise<void> {
            await this.storeRepository.update(id, { gatewayMac: null });
        }
        

        async setStoreGatewayMac(id: string, mac: string): Promise<void> {
            await this.storeRepository.update(id, { gatewayMac: mac });
        }
        ```
        - [x] Modification du fichier change-gateway-assignation.component.ts
        ```
        reassignGateway() {
            this.storeService.changeGateway(this.data.id, this.gatewayForm.get('mac')?.value).subscribe(() => {
            this.snackBar.open('Réassignation réussie','',{duration: 3000})
            this.historyGatewayService.createHistoryGateway({
                nameOfTheUser: `${this.userProfile.__zone_symbol__value.username}`,
                description: `gateway ajoutée au magasin ${this.data.address + this.data.brand}`,
                gateway: {mac: this.gatewayForm.get('mac')?.value}
                // eslint-disable-next-line @typescript-eslint/no-empty-function
            }).subscribe(() => {

            }, error => {

                throw error
            })
            this.historyGatewayService.createHistoryGateway({
                nameOfTheUser: `${this.userProfile.__zone_symbol__value.username}`,
                description: `gateway retirée du magasin ${this.data.address + this.data.brand}`,
                gateway: {mac: this.data.mac}
                // eslint-disable-next-line @typescript-eslint/no-empty-function
            }).subscribe(() => {},
                error => {
                throw error
                })
            this.closeDialog()
            }, error => {
            console.error(error);
            this.snackBar.open('Echec de la réassignation','',{duration: 3000})
            throw error
            })
        }
        ```
        - [x] Création du fichier store.repository.ts comme suit :
        ```
        import { EntityRepository, Repository } from 'typeorm';
        import { Store } from './entities/store.entity';

        @EntityRepository(Store)
        export class StoreRepository extends Repository<Store> {}
        ```
        - [x] Imports & provide, mais toujours erreur : Nest can't resolve dependencies of the StoresService (StoreRepository, ?). Please make sure that the argument Repository at index [1] is available in the StoresModule context.
        - [ ] ```git reset --hard```
        - [ ] Reprise sur des bases saines