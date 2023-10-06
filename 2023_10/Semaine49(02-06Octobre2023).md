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
        - [x] ```git reset --hard```
        - [x] Reprise sur des bases saines
        - [x] Modification du fichier store.service.ts (front) :
        ```
        changeGateway(id: string ,mac: string){
            const headers = new HttpHeaders({
            'Content-Type': 'application/json',
            });

            const body = { gatewayMac: mac };

            return this.httpclient.patch(this.apiUrl + '/reassignGateWay/' + id, body, {
            headers,
            });
        }
        ```
        - [x] Modification du fichier change-gateway-assignation.component.ts : 
        ```
        selectedGatewayMac: string | undefined;
        ...
        reassignGateway() {
            if (this.selectedGatewayMac) {
            this.storeService.changeGateway(this.data.id, this.selectedGatewayMac).subscribe(
                () => {
                this.snackBar.open('Gateway reassigned successfully', 'Close', {
                    panelClass: ['snackbarGreen'],
                });
                this.dialogRef.close();
                },
                (error) => {
                this.snackBar.open('Failed to reassign gateway', 'Close', {
                    panelClass: 'snackbarRed',
                });
                throw error;
                }
            );
            } else {
            // Handle the case where no gateway is selected
            this.snackBar.open('Please select a gateway', 'Close', {
                panelClass: 'snackbarRed',
            });
            }
        }
        ```
        - [x] J'ai toujours le problème de duplicate key
        ```
        ERROR [ExceptionsHandler] duplicate key value violates unique constraint "REL_e36699fed6e6a91d65a0e9516b"
        ```
        - [x] Défonce de la db locale
        - [x] Rebuild de la db locale
        - [x] TLDR : le code est fonctionnel, mais l'application renvoie une 500 avec duplicate unique key parce que la liaison n'est pas faite dans la table gateway avec le storeId
    - [ ] Ticket KP-452 fix duplicate store
        - [x] Ajout de deux
        ```
        console.error(error)
        ```
        pour cibler le problème

**05 Octobre**
- [ ] Mise à jour Jira
    - [x] Ajout de :
    ```
    class="hover:bg-gray-100 hover:cursor-pointer"
    ```
    dans le <li> des html
    - [x] Commit, push et PR pour KP-510 QoL
    - [ ] Ticket KP-517 fix désactivation de store
        - [x] Repasse sur develop et pull origin
        - [x] Crééation de branche KP-517

-> Verifier que il y est app tester d'installer sur la tablette avec un compte google connecter 
-> si pas apptester connecter mail gerance.cop-amamco.com mdp COPAMACO2022**
-> dans les mail chercher le mail qui notifie une nouvelle version de l'app KMO-Assist tu veras il y'aura un lien vers app tester
-> connecter dans apptester le compte gmail gerance.cop-amaco.gmail.com
-> télécharger l'app KMO-ASSIST
-> si jamais les login sont Adresse mail cop mdp COPkmo67 

voir avec jacques pour les infos des caisses

**06 Octobre**
- [ ] Mise à jour Jira
    - [ ] Ticket KP-517 fix désactivation de store