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
    - [ ]