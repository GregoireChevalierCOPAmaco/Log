**30 Janvier**
- [ ] Réflexion sur comment faker le débranchement de la fiche trappe pour l'afficher immédiatement
  - [ ] Faire une route API qui checke les events trapdoor, et y faire appel toutes les 5? secondes et en fonction du retour, recharger la page 
- [ ] KP-709 Créer une route API pour checker toutes les x secondes les events trapdoor et recharger la page avec les nouvelles infos le cas échéant
  - [x] ```git checkout -b feat/KP-709_create_API_route_to_lastTrapdoorEvent_ofKmo```
  - [x] Demande à gpt : 
  ```
  i want to create an API route with a GET endpoint that would return the latest trapdoor event for a specific gateway (with the gateway mac)
  ```
  - [x] Ajout dans l'events.service.ts de : 
  ```
  async getLatestTrapdoorEvent(gatewayMac: string): Promise<Event | null> {
    try {
      const event = await Event.createQueryBuilder('event')
        .where('event.type = :type', { type: TypeEvent.TRAPDOOR_TRIGGERED })
        .andWhere('event.kmoBox.gateway.mac = :gatewayMac', { gatewayMac })
        .orderBy('event.datetime', 'DESC')
        .getOne();

      return event;
    } catch (error) {
      console.error('Error fetching latest trapdoor event:', error);
      throw new HttpException('Failed to fetch latest trapdoor event', 500);
    }
  }
  ```
  - [x] Ajout dans l'events.controller.ts de : 
  ```
  @Get('latest-trapdoor-event/:gatewayMac')
  async getLatestTrapdoorEvent(@Param('gatewayMac') gatewayMac: string) {
    try {
      const event = await this.eventsService.getLatestTrapdoorEvent(gatewayMac);
      return event;
    } catch (error) {
      console.error('Error fetching latest trapdoor event:', error);
      return { error: 'Failed to fetch latest trapdoor event' };
    }
  }
  ```
  - [ ] Reste à obtenir la gwMac de la kmo associée
- [ ] Ajout dans jira du ticket KP-714 : Tests fonctionnels de corrélation caisses-données, partie 2
    - [ ] Nouvelle batch de tests avec la caisse à côté
- [ ] Ajout dans jira du ticket KP-716 La modale de magasin doit afficher la mac de la Gateway associée


**31 Janvier**
- [ ] Passage sur afficher la mac de la Gateway associée d'un magasin sur modale
    - [x] Création et switch sur branche : 
    ```
    git checkout -b fix/KP-716_display_stores_gatewayMacaddress
    ```
    - [x] Isolation du code concerné : le fichier cop.component.html & .ts
    - [x] Ajout de  : 
    ```
    ...
    public gatewayMac: any ;
    ...
    getStore() {
        this.storeService.getStoreById(this.idStore).subscribe({
        next: (data) => {
            this.store = data;
            this.gateway = data.gateway;
            this.isBroken = data.gateway.isBroken
            this.gatewayMac = data.gatewayMac
            
        },
        error: (err) => {
            throw err;
        },
        });
    }
    ```
    dans le ts
    - [x] Ajout de  : 
    ```
    ...
    <div>Macadresse de la gateway : <strong>{{this.gatewayMac}}</strong> </div>
    ```
    dans le html
    - [x] lint
    - [x] test
    - [x] commit
    - [x] push
    - [x] PR
    - [x] merge

surveillance datas gunder
corrélation données caisses, données base
fix affichage mac gw
route last trapdoor event ok avec gwmac mais lourd donc refait
toaster quasi prêt pour deployer quand route ok (!)

clearer les rapports d'erreur si passage à état RAS
cas d'usage à check : je mets en RAS via l'interface alors que la trappe est toujours down

faire une liste de cas d'usage salon à tester fonctionnellement



**2 février**
- [ ] Créer une route API pour checker toutes les x secondes les events trapdoor et recharger la page avec les nouvelles infos le cas échéant
- [ ] Au retrait de la fiche trappe de la caisse, s'assurer que l'état de la caisse en question change d'état de maintenance. Voir en combien de temps le changement s'opère
- [ ] Au retrait de la fiche trappe de la caisse, faire apparaître un toaster qui informe l'utilisateur que la caisse (numéro & magasin) est passé en maintenance prédictive/curative
- [x] Résolution du problème docker local : 
```
Error response from daemon: Ports are not available: exposing port TCP 0.0.0.0:3000 -> 0.0.0.0:0: listen tcp 0.0.0.0:3000: bind: An attempt was made to access a socket in a way forbidden by its access permissions.
```
- [x] Check si le port est en usage : 
```
Get-NetTCPConnection -LocalPort 3000
```
Reéponse : 
```
Get-NetTCPConnection : Aucun objet MSFT_NetTCPConnection avec la propriété « LocalPort » égale à « 3000 » n’a été trouvé. Vérifiez la valeur de la propriété et réessayez.
Au caractère Ligne:1 : 1
+ Get-NetTCPConnection -LocalPort 3000
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (3000:UInt16) [Get-NetTCPConnection], CimJobException
    + FullyQualifiedErrorId : CmdletizationQuery_NotFound_LocalPort,Get-NetTCPConnection
```
Le port n'est pas déjà en usage
- [x] Redémarrer fix le problème