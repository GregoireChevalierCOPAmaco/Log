**11 Décembre**
- [ ] Résoudre le problème de uptime 
    - [x] Test fonctionnel de corrélation 
        - [x] Balance de x events pour tests
        - [x] Les events n'arrivent pas en base, bluetooth pas activé selon jacques
        - [x] Balance de x events pour tests
- [ ] Utiliser l'uptime pour Fix la corrélation Durée on-Durée off- Uptime total
    - [x] Création de la branche ``` git checkout -b fix/KP-641-642_fix_uptime-related_values```
    - [ ] Renommage des kpi existantes
        - uptime en  : Temps de caisse allumée
        - durée on en : temps d’utilisation moteur
        - durée off en : temps de moteur au repos
    - [ ] Ajout de la kpi temps de caisse ouverte (uptime - closedTime)
    - [ ] lint, test, commit & push
- [ ] Utiliser l'uptime pour Fix la corrélation temps depuis la dernière utilisation
    - [ ] 
    - [ ] 
    - [ ] 

**12 Décembre**
Arrêt maladie
**13 Décembre**
Arrêt maladie
**14 Décembre**
Arrêt maladie


**15 Décembre**
- [ ] Utiliser l'uptime pour Fix la corrélation Durée on-Durée off- Uptime total
    - [x] Reprise de la branche fix/KP-641-642_fix_uptime-related_values
    - [ ] Renommage des kpi existantes
        - uptime en  : Temps de caisse allumée
        - durée on en : temps d’utilisation moteur
        - durée off en : temps de moteur au repos
    - [ ] Ajout de la kpi temps de caisse ouverte (uptime - closedTime)
        - [ ] Ajouter le calcul de closed time dans le service events
            - [x] Créer une route dans le controller : 
            ```
            @Patch(':mac/closed-time')
            async updateClosedTime(
                @Param('mac') mac: string,
                @Body('closedTime') closedTimeToAdd: number,
            ) {
                const updatedKmoBox = await this.kmoBoxesService.updateClosedTime(mac, closedTimeToAdd);
                if (!updatedKmoBox) {
                throw new NotFoundException(`KmoBox with MAC '${mac}' not found`);
                }
                return { message: 'Closed time updated successfully', kmoBox: updatedKmoBox };
            }
            ```
            - [x] Créer une méthode dans le service pour update le closedTime d'une kmo : 
            ```
            async updateClosedTime(mac: string, closedTimeToAdd: number): Promise<KmoBox | undefined> {
                try {
                const kmoBox = await KmoBox.findOne({ where: { mac } });

                if (kmoBox) {
                    kmoBox.closedTime = closedTimeToAdd;
                    await KmoBox.save(kmoBox);
                    return kmoBox;
                }

                return undefined;
                } catch (error) {
                throw new HttpException('Failed to update closedTime for KmoBox', 500);
                }
            }
            ```
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