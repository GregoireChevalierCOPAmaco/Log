**27 Novembre**
- [ ] Check des mails
- [ ] Mise à jour Jira
- [ ] MàJ du fichier de tests
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

à voir par la suite ; 
si une GW envoie des données de caisse null, faire une routine ?
    non car kmo pas créable si champs null


entité, valeur "défault" mettable si data required et null obtenu à la création de l'entité

596 : renvoyer au websocket les datas GW + KMOs, et dans le back, faire de l'asynchrone pour ne créer la KMO qu'une fois que la GW est bel et bien en base

verifier si possible de remplacer GW mais pas kmos 
vé rifier si possible de remplacer une ou plus kmo mais pas la GW
vérifier les relations