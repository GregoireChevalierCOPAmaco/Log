**27 Novembre**
- [ ] Check des mails
- [ ] Mise à jour Jira
- [ ] MàJ du fichier de tests
- [x] Fix des erreurs d'orthographe
    - [x] Création de la branche fix/KP-451_fix_typos
    - [x] Ajout du s à 'Nombre de caisse' dans les fichiers add-store, tab-store & cop-store-details
    - [x] Fix des autres erreurs, voir PR si besoin
    - [x] Commit & push
- [ ] Résolution du problème de uptime
    - [ ] Les events motor viennent 10s en 10s up à la suite de up
    à résoudre, logique à refaire pour le motor up : 
    - à chaque fois qu'un event motor est émis, 
    - si le state = up
    - on ajoute la duration au motorUp
    à résoudre, logique à refaire pour le uptime :
    - à chaque fois qu'un event state = power_off 
    - si c'est le premier power off du jour
    - on prend la datetime
    - on fait la différence avec la datetime du premier event du jour de la caisse
    - et on l'ajoute au uptime
    - [ ] Résoudre le problème de uptime qui diminue avec le temps
    - [ ] Chercher dans les conditions des lignes 147-174 pour trouver pourquoi le offtime est de zéro
    actuellement, ces lignes prennent en compte sans discriminer tous les events up & down, pas que le moteur ou IR Rajouter une condition ?
- [ ] Résolution du problème de motor_overheat
    - [ ] Tests et conclusion : un event motor overheat est émis par le firmware après un timeout pour dire que "tout va bien" avec le state up. Ajouter une condition dans le code pour différencier un event up de down pour motor_overheat


**28 Novembre**
- [ ] Résolution du problème de uptime
    - [x] Réplique des events de prod en dev pour reproduction de uptime négatif
    - [x] Copie des events et modification des champs gatewaymac & datetime
    - [x] Si le dernier event state == down, le uptime est incrémental comme prévu, mais si le dernier event == up, le uptime incrémente négativement
    - [x] Le problème vient du fait que uptime est basé sur des états d'events up & down au lieu de open & closed
    - [ ] Résoudre le problème de uptime qui diminue avec le temps
        - [x] Changer la logique pour chercher les events open & closed
        ```
        .where('event.state = :state', { state: StateEvent.OPEN }
        ```
        ```
        .where('event.state = :state', { state: StateEvent.CLOSED }
        ```
        - [ ] Ajuster la logique pour l'update du motorusage
        - [x] Push, PR, pull sur le serveur de prod & redémarrage des containers
        - [ ] Vérification sur le setup de prod
- [ ] Résolution du problème de motor_overheat
    - [ ] Tests et conclusion : un event motor overheat est émis par le firmware après un timeout pour dire que "tout va bien" avec le state up. Ajouter une condition dans le code pour différencier un event up de down pour motor_overheat


à voir par la suite ; 
si une GW envoie des données de caisse null, faire une routine ?
    non car kmo pas créable si champs null


entité, valeur "défault" mettable si data required et null obtenu à la création de l'entité

596 : renvoyer au websocket les datas GW + KMOs, et dans le back, faire de l'asynchrone pour ne créer la KMO qu'une fois que la GW est bel et bien en base

verifier si possible de remplacer GW mais pas kmos 
vé rifier si possible de remplacer une ou plus kmo mais pas la GW
vérifier les relations


**30 Novembre**
- [x] Résoudre le problème de uptime qui augmente même en power_off
    - [x] Aucun problème dans la logique front qui se contente d'aller fetch en base kmoBox.data.uptime
    - [x] Check du fichier events.service.ts dans le back
    - [x] Ajout de  : 
    ```
    const lastPowerOffEvent = await this.eventRepository
        .createQueryBuilder('event')
        .where('event.state = :state', { state: StateEvent.POWER_OFF })
        .andWhere('event.kmoBox.mac = :kmoBoxMac', { kmoBoxMac })
        .orderBy('event.datetime', 'DESC')
        .getOne();

    if (lastUpEvent && lastPowerOffEvent && lastPowerOffEvent.datetime > lastUpEvent.datetime) {
        continue;
    }
    ```
    - [x] Lint, tests, commit, ```git push``` & PR
    - [x] Mise en prod
    - [ ] Test fonctionnel de corrélation : Résultat, le uptime ne prend que le temps moteur on. Changer cela 
- [ ] Ajout d'un champ closedTime dans l'entité kmoBox
    - [x] Création de la branche feat/KP-650_add_closedTime_to_kmo
    - [ ] Modification de kmo-box
        - [x] Entity
        - [x] Interface
        - [x] Interface spec
        - [x] Service spec
        - [x] DTO create
        - [x] DTO get lambda
        - [x] GW interface spec
        - [x] GW logs interface spec
        - [x] STORE component spec
        - [x] CHECKOUT modal component spec
        - [ ] COP dialog component
        - [x] COP dialog component spec
        - [ ] COP dialog component html
    - [ ] Event service
    - [ ] Event service spec
- [x] Refonte du fichier SQL pour le dev dans discord
- [ ] Ajuster la logique pour l'update du motorusage
- [ ] Résolution du problème de motor_overheat
    - [x] Tests et conclusion : un event motor overheat est émis par le firmware après un timeout pour dire que "tout va bien" avec le state up. Ajouter une condition dans le code pour différencier un event up de down pour motor_overheat
    - [x] Transfert du ticket KP-639 à Anthony pour gestion dans la lambda
- [x] Mise à jour Jira
- [x] MàJ du fichier de tests

**1er Décembre**
- [x] Résoudre le problème de uptime qui augmente même en power_off
    - [ ] Test fonctionnel de corrélation : Résultat, le uptime ne prend que le temps moteur on. Changer cela 
- [ ] Ajout d'un champ closedTime dans l'entité kmoBox
    - [x] Création de la branche feat/KP-650_add_closedTime_to_kmo
    - [ ] Modification de kmo-box
        - [x] Entity
        - [x] Interface
        - [x] Interface spec
        - [x] Service spec
        - [x] DTO create
        - [x] DTO get lambda
        - [x] GW interface spec
        - [x] GW logs interface spec
        - [x] STORE component spec
        - [x] CHECKOUT modal component spec
        - [ ] COP dialog component
        - [x] COP dialog component spec
        - [ ] COP dialog component html
    - [ ] Event service
    - [ ] Event service spec
- [x] Refonte du fichier SQL pour le dev dans discord
- [ ] Ajuster la logique pour l'update du motorusage
- [ ] Résolution du problème de motor_overheat
    - [x] Tests et conclusion : un event motor overheat est émis par le firmware après un timeout pour dire que "tout va bien" avec le state up. Ajouter une condition dans le code pour différencier un event up de down pour motor_overheat
    - [x] Transfert du ticket KP-639 à Anthony pour gestion dans la lambda
- [ ] Mise à jour Jira
- [ ] MàJ du fichier de tests