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

    Reprendre la documentation de la fonction gestion uptime