**12 Février**
- [x] Clean de la db
    - [x] Export de la table events
- [ ] Poursuite user story KP-705
    - [ ] KP 706 Affichage visuel de caisse en maintenance sur l'interface de store
        - [x] Création de branche ```git checkout -b feat/KP-706_change_checkout_card_color_ontrpdoorTriggered_event```
        - [x] Identifier le fichier à modifier : cop.component
        - [ ] Créer une fonction qui, onInit, cherche le dernier event trapdoor par checkout et modifie au besoin le maintenanceLevel
            - [ ] Récupérer la gwMac
            - [ ] Utiliser la route API en prenant en paramètre la gwMac pour récupérer le dernier event trapdoor associé
            - [ ] changer l'état de caisse de la caisse concernée si la date de l'event date de moins de x secondes/minutes
            - [ ] OU, créer une route get latest trapdoor event by kmomac, & pour chaque kmo utiliser cette route et ensuite modifier l'état de chaque caisse en conséquence
            - [ ] OU get latest trapdoorevent, passer l'id en parametre de getKmoBoxMacByEventId & changer l'état de caisse si datetime < 60 s ?
    - [ ] KP 739 Fix utc time
    - [ ] Validation corrélation données sur place au 12


    **13 Février**    
- [ ] Poursuite user story KP-705
    - [ ] KP 706 Affichage visuel de caisse en maintenance sur l'interface de store
        - [ ] Créer une fonction qui, onInit, cherche le dernier event trapdoor par checkout et modifie au besoin le maintenanceLevel
            - [ ] Récupérer la gwMac
            - [ ] Utiliser la route API en prenant en paramètre la gwMac pour récupérer le dernier event trapdoor associé
            - [ ] changer l'état de caisse de la caisse concernée si la date de l'event date de moins de x secondes/minutes
            - [ ] OU, créer une route get latest trapdoor event by kmomac, & pour chaque kmo utiliser cette route et ensuite modifier l'état de chaque caisse en conséquence
            - [ ] OU get latest trapdoorevent, passer l'id en parametre de getKmoBoxMacByEventId & changer l'état de caisse si datetime < 60 s ?
    - [ ] KP 739 Fix utc time
    - [ ] Validation corrélation données sur place au 12

