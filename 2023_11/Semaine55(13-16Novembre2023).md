**10 Novembre**
- [x] Check des mails
- [x] Mise à jour Jira
- [ ] Mise en marche du "magasin de tests" en réel
    - [ ] Résolution du bug de non assignation d'une GW à une KMOBOX
- [ ] KP-585 : Mettre les GW en HS après 72h sans activité
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