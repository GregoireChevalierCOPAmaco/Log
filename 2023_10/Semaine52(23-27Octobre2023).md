**19 Octobre**
- [x] Check des mails
- [ ] Mise à jour Jira
- [ ] Début kp-556
    - [ ] Exploitation des données pour les rendre exploitables
        - [x] Affichage de l'état de la caisse
            - [x] Écriture de la logique : 
            ```
            const kmoBoxMac = this.data.mac;

            this.eventsService.getTodaysEvents().subscribe({
            next: (events) => {
                if (events.length > 0) {
                const kmoBoxEvents = events.filter((event) => event.mac === kmoBoxMac);
        
                const lastEvent = kmoBoxEvents.length > 0 ? kmoBoxEvents[kmoBoxEvents.length - 1] : null;
        
                if (lastEvent) {
                    this.kmoCurrentState = lastEvent.kmoCurrentState;
                } else {
                    this.kmoCurrentState = 'État Inconnu';
                }
                } else {
                this.kmoCurrentState = 'État Inconnu';
                }
            },
            error: (err) => {
                this.snackBar.open(err, 'Ok');
            },
            });
            ```
        - [x] Affichage de la température
            - [x] Création d'un endpoint dans le controller back
            ```
            @Get('latest-cpu-temperature-event')
            async getLatestCpuTemperatureEvent() {
                const event = await this.eventsService.getLatestCpuTemperatureEvent();
                return event;
            }
            ```
            - [x] Création de la logique dans le service back
            ```
            async getLatestCpuTemperatureEvent(): Promise<Event | null> {
                const event = await Event.createQueryBuilder('event')
                .where('event.type = :type', { type: 'cpu_temperature_changed' })
                .orderBy('event.datetime', 'DESC')
                .getOne();

                return event;
            }
            ```


            reunion sprint 
            infos kpi caisse, isoler les fréquences permet de monitorer l'utilisation et l'affluence d'une caisse
            à garder : ir / pédale sur l'affichage
            à compter sans montrer, trappe & thermorupteur

            faire un powerpoint détaillant les vues pour la vue statistique
            recherche - checkbox to pin & summup
            head to head with 2 cards
            faire tache jira