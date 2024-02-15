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
        - [x] Créer une fonction qui, onInit, cherche le dernier event trapdoor par checkout et modifie au besoin le maintenanceLevel
            - [ ] Récupérer la gwMac
            - [x] Utiliser la route API en prenant en paramètre la gwMac pour récupérer le dernier event trapdoor associé
            - [x] changer l'état de caisse de la caisse concernée si la date de l'event date de moins de x secondes/minutes
            - [x] OU, créer une route get latest trapdoor event by kmomac, & pour chaque kmo utiliser cette route et ensuite modifier l'état de chaque caisse en conséquence
            - [x] OU get latest trapdoorevent, passer l'id en parametre de getKmoBoxMacByEventId & changer l'état de caisse si datetime < 60 s ?
            - [x] Fix du problème de 404 en modifiant le service du back : 
            ```
            async setMaintenanceLevelToCurative(mac: string): Promise<KmoBox | undefined> {
                try {
                const options: FindOneOptions<KmoBox> = {
                    where: { mac } as any,
                };
            
                const kmoBox = await KmoBox.findOne(options);
            
                if (kmoBox) {
                    kmoBox.maintenanceLevel = 'Curative';
                    await KmoBox.save(kmoBox);
                    return kmoBox;
                }
            
                return undefined;
                } catch (error) {
                throw new HttpException('Failed to set maintenance level to Curative for KmoBox', 500);
                }
            }
            ```
        - [ ] Modification de la couleur en direct
            - [x] Ajout du code de reload
            ```
            this.router.routeReuseStrategy.shouldReuseRoute = () => false;
            const currentUrl = this.router.url;
            this.router.navigateByUrl('/', { skipLocationChange: true }).then(() => {
            this.router.navigate([currentUrl]);
            });
            ```
            - [ ] Résolution du problème de refresh infini
    - [ ] KP 739 Fix utc time
    - [ ] Validation corrélation données sur place au 12
    - [ ] Kp 746 Passer la logique de détection des trapdoor events sur la vue de magasin au lieu de cop
    - [ ] KP 745 Ajouter un code coloré dans la page de magasin pour traduire l'ouverture/fermeture de caisse


**15 février**
- [ ] Poursuite user story KP-705
    - [ ] KP 706 Affichage visuel de caisse en maintenance sur l'interface de store
    - [x] KP 739 Fix utc time
    - [ ] Validation corrélation données sur place au 12
    - [x] Kp 746 Passer la logique de détection des trapdoor events sur la vue de magasin au lieu de cop
    - [ ] KP 745 Ajouter un code coloré dans la page de magasin pour traduire l'ouverture/fermeture de caisse
