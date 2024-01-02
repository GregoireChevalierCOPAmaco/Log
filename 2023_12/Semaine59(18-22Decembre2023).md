**18 Décembre**
- [ ] Utiliser l'uptime pour Fix la corrélation Durée on-Durée off- Uptime total
    - [x] Reprise de la branche fix/KP-641-642_fix_uptime-related_values
    - [ ] Renommage des kpi existantes
        - uptime en  : Temps de caisse allumée
        - durée on en : temps d’utilisation moteur
        - durée off en : temps de moteur au repos
    - [ ] Ajout de la kpi temps de caisse ouverte (uptime - closedTime)
        - [ ] Ajouter le calcul de closed time dans le service events
            - [ ] Créer une variable bool de classe checkoutOpen
            - [ ] À chaque event open
                - [ ] Passer checkoutOpen à true
                - [ ] Créer logique : 
                    - on event closed
                    - Si checkoutOpen true
                    - get last closed event
                    - calculate timeDiff (datetime closed - datetime open)
                    - Récupérer kmo.uptime
                    - soustraire timeDiff à uptime pour avoir closedTime
                    - remplacer kmo.closedTime par le nouveau closedTime
                    - repasser checkoutOpen à false
    - [ ] lint, test, commit & push
- [ ] Utiliser l'uptime pour Fix la corrélation temps depuis la dernière utilisation
    - [ ] 
    - [ ] 

    !!!! ! OFFTIME != CLOSEDTIME !!!!!!
    rajouter closed time dans la modale 

    Reprendre la documentation de la fonction gestion uptime


**19 Décembre**
- [x] Ajout du calcul front pour le openTime et affichage dans la modale
- [x] Push 
- [ ] Setup en prod
- [ ] Clean des tables event & kmo sur base prod

**21 Décembre**
- [ ] Reprise des tests fonctionnels
    - [ ] Refaire les lignes de Jacques avec lui pour déterminer le processus
    - [x] Flush les données
    - [ ] Ecrire la série de tests fonctionnels depuis zéro
        - Création d'une GW en réel
        - Affichage front lors de la création d'une GW en réel
        - Création d'un store en réel
        - Assignation automatique store - GW
        - Assignation automatique GW - store
        - Affichage front lors de la création d'un store en réel
        - Création d'une deuxième GW en réel
        - Affichage front lors de la création d'une GW en réel
        - Changer la GW associée à un magasin
    - [ ] Réinstaller une GW avec 2 caisses
    - [ ] Reproduire le mode opératoire
- [ ] Créer un objet front modale de modification store
    - [ ] Supprimer l'option de réassignation de GW dans cette modale


**22 Décembre**