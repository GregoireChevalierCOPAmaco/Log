**13 Novembre**
- [x] Check des mails
- [x] Mise à jour Jira
- [ ] Mise en marche du "magasin de tests" en réel
    - [ ] Résolution du bug de non assignation d'une GW à une KMOBOX
- [x] KP-585 : Mettre les GW en HS après 72h sans activité
- [x] gpt : 
```
in the same project, i want to 
- run every 2 minutes a method that would
  - return the property "mac" from every Gateway that has as a property "isBroken" : "false"
  - return the property "mac" from every KmoBox that has as a property "gatewayMac" the "mac" property of the precedently returned Gateways
  - return the latest Event that has as property "kmoBoxMac" for every KmoBox
```
Me paraît pas optimal tout ça
- [x] Petite discussion avec Guillaume et Jacques, il vaut à priori mieux aller chercher tous les events de ces trois derniers jours ↓
- Chercher les latest events de ces trois derniers jours par kmoBoxMac
- Récupérer toutes les mac de KmoBox
- Récupérer toutes les gateways associées 
- Passer ces Gateways en isBroken : false
- Passer toutes les autres Gateways en isBroken : true
```
- run every day a method that would
  - return each unique "kmoBoxMac" property of all events the last three days
  - return every "gatewayMac" property of all KmoBox that have as property "mac" the precedently returned "kmoBoxMac"
  - for every Gateway that has as property "mac" one of the precedently returned "gatewayMac"', set its "isBroken" property to false 
  - for every other Gateway, set its "isBroken" property to true
```
- [x] Ajout d'une méthode dans le service gateways du back : 
```
  async setGatewayStatus(gatewayMac: string, isBroken: boolean) {
    try {
      const gateway = await Gateway.createQueryBuilder('gateway')
        .where('gateway.mac = :mac', { mac: gatewayMac })
        .getOne();

      if (!gateway) {
        throw new HttpException('Gateway not found', 404);
      }

      gateway.isBroken = isBroken;
      await Gateway.save(gateway);
    } catch (e) {
      throw new HttpException(e, 500);
    }
  }
```
- [x] Modifs de l'events.service.ts : 
```
async getUniqueKmoBoxMacsLastThreeDays(): Promise<string[]> {
const threeDaysAgo = new Date();
threeDaysAgo.setDate(threeDaysAgo.getDate() - 3);

const uniqueKmoBoxMacs = await this.eventRepository
    .createQueryBuilder('event')
    .select('DISTINCT event.kmoBox.mac', 'kmoBoxMac')
    .where('event.datetime >= :threeDaysAgo', {
    threeDaysAgo: threeDaysAgo.toISOString(),
    })
    .getRawMany();

return uniqueKmoBoxMacs.map((result) => result.kmoBoxMac);
}

async getGatewayMacsForKmoBox(kmoBoxMac: string): Promise<string[]> {
const gatewayMacs = await this.eventRepository
    .createQueryBuilder('event')
    .distinct(true)
    .select('gateway.mac', 'gatewayMac')
    .innerJoin('event.kmoBox', 'kmoBox')
    .innerJoin('kmoBox.gateway', 'gateway')
    .where('kmoBox.mac = :kmoBoxMac', { kmoBoxMac })
    .getRawMany();

return gatewayMacs.map((result) => result.gatewayMac);
}

@Interval(86400000)
async setGWState() {
try {
    const uniqueKmoBoxMacs = await this.getUniqueKmoBoxMacsLastThreeDays();

    for (const kmoBoxMac of uniqueKmoBoxMacs) {
    const gatewayMacs = await this.getGatewayMacsForKmoBox(kmoBoxMac);

    for (const gatewayMac of gatewayMacs) {
        await this.gatewayService.setGatewayStatus(gatewayMac, false);
    }

    const allGateways = await this.gatewayService.getAllGateway();

    for (const gateway of allGateways) {
        if (!gatewayMacs.includes(gateway.mac)) {
        await this.gatewayService.setGatewayStatus(gateway.mac, true);
        }
    }
    }
} catch (error) {
    console.error('Error in GW state job:', error);
}
}
```
- [x] Lint, test & push
- [x] Ajout de GatewaysServie dans les providers de l'EventsModule pour réousre le problème de dépendances créé


**14 Novembre**
- [x] Check des mails
- [x] Mise à jour Jira
- [x] Mise en marche du "magasin de tests" en réel
- [ ] MàJ du fichier de tests
    - [x] Ligne 38 ; passage à test réussi & update ligne en conséquence
    - [x] Ligne 44 ; update du message d'erreur
    - [x] Ligne 45 ; passage à test réussi & update ligne en conséquence
    - [x] Ligne 46 ; création du ticket KP-597 & update ligne en conséquence
    - [x] Ligne 47 ; création du ticket KP-598 & update ligne en conséquence
    - [x] Ligne 51 ; passage à test réussi & update ligne en conséquence
    - [x] Ligne 55 ; passage à test réussi & update ligne en conséquence
    - [x] Ligne 56 ; passage à test réussi & update ligne en conséquence
    - [x] Ligne 57 ; passage à test réussi & update ligne en conséquence
    - [x] Ligne 58 ; passage à test réussi & update ligne en conséquence
    - [x] Ligne 59 ; passage à test réussi & update ligne en conséquence
    - [x] Ligne 65 ; Création de ligne pour KP-542 & update ligne en conséquence
    - [x] Ligne 66 ; Création de ligne pour KP-551 & update ligne en conséquence
    - [x] Ligne 67 ; Création de ligne pour KP-591 & update ligne en conséquence
    - [x] Ligne 68 ; Création de ligne pour KP-595 & update ligne en conséquence
    - [x] Ligne 69 ; Création de ligne pour KP-596 & update ligne en conséquence
- [ ] Résolution du bug de non assignation d'une GW à une KMOBOX
- [x] Essais multiples sur la kmobox de test réel salle à côté
- [x] Création des tickets KP-597 à KP-601 pour fit needs
- [ ] MàJ du fichier de tests
    - [x] Ligne 70 ; Création de ligne pour KP-601 & update ligne en conséquence
    - [x] Ligne 71 ; Création de ligne pour KP-600 & update ligne en conséquence
- [x] Retour sur develop en local 
- [x] ```git pull origin develop```
- [x] Cleanup de branches locales :
```
git branch -D feat/KP-425_display_dynamicly_/cop_gateways_card
git branch -D feat/KP-425bis_display_dynamicly_/cop_gateways_card
git branch -D feat/KP-466_display_dynamically_checkouts_status_incard
git branch -D fix/KP-424_/administration_sorting_table
git branch -D test/KP-218_catching_every_frontend_test
git branch -D test/KP-176_unit_testing
git branch -D test/KP-216_front_unit_test_authguard
git branch -D refactor/KP-175_clean_administration.component
git branch -D test/KP-177_store_deactivation_e2e_testing
git branch -D feat/KP-467_display_dynamically_critical_stores_incard
```
Retour : 
```
Deleted branch feat/KP-425_display_dynamicly_/cop_gateways_card (was a8a13f2e).
Deleted branch feat/KP-425bis_display_dynamicly_/cop_gateways_card (was 256b1cfe).
Deleted branch feat/KP-466_display_dynamically_checkouts_status_incard (was de3fa34a).
Deleted branch fix/KP-424_/administration_sorting_table (was ff35a098).
Deleted branch refactor/KP-175_clean_administration.component (was a6541e0c).
Deleted branch test/KP-218_catching_every_frontend_test (was 5d859593).
Deleted branch test/KP-216_front_unit_test_authguard (was 8eb35037).
Deleted branch test/KP-177_store_deactivation_e2e_testing (was d198aa83).
Deleted branch test/KP-176_unit_testing (was adc0376d).
Deleted branch feat/KP-467_display_dynamically_critical_stores_incard (was 63e547c2).
```
- [x] Gestion départ tablette Salomon Chaucron


**16 Novembre**
- [x] Check des mails
- [x] Mise à jour Jira
- [x] Mise en marche du "magasin de tests" en réel
- [ ] MàJ du fichier de tests