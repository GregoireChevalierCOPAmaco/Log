**23 Octobre**
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

**24 Octobre**
- [x] Check des mails
- [ ] Mise à jour Jira
- [ ] Début kp-556
    - [ ] Exploitation des données pour les rendre exploitables
        - [x] Affichage du temps depuis le dernier allumage de la caisse
            - [x] Création de route pour find évents de fermeture & ouverture de caisse 
            ```
              @Get('latest-opened-event/:kmoBoxMac')
            async getLastOpenedEvent(@Param('kmoBoxMac') kmoBoxMac: string) {
                const event = await this.eventsService.getLastOpenedEvent(kmoBoxMac);
                return event;
            }

            @Get('latest-closed-event/:kmoBoxMac')
            async getLastClosedEvent(@Param('kmoBoxMac') kmoBoxMac: string) {
                const event = await this.eventsService.getLastClosedEvent(kmoBoxMac);
                return event;
            }
            ```
            - [x] Création de logique back : 
            ```
              async getLastOpenedEvent(kmoBoxMac: string): Promise<Event | null> {
                const event = await Event.createQueryBuilder('event')
                .where('event.state = :state', { state: 'open' })
                .andWhere('event.kmoBox.mac = :kmoBoxMac', { kmoBoxMac })
                .orderBy('event.datetime', 'DESC')
                .getOne();

                return event;
            }

            async getLastClosedEvent(kmoBoxMac: string): Promise<Event | null> {
                const event = await Event.createQueryBuilder('event')
                .where('event.state = :state', { state: 'closed' })
                .andWhere('event.kmoBox.mac = :kmoBoxMac', { kmoBoxMac })
                .orderBy('event.datetime', 'DESC')
                .getOne();

                return event;
            }
            ```
            - [x] Création de logique front : 
            ```
            this.eventsService.getLatestClosedEvent(kmoBoxMac).subscribe({
                next: (latestClosedEvent) => {
                    if (latestClosedEvent && latestClosedEvent.state === 'closed') {
                    this.eventsService.getLatestOpenedEvent(kmoBoxMac).subscribe({
                        next: (latestOpenedEvent) => {
                        if (latestOpenedEvent && latestOpenedEvent.state === 'open') {
                            const timeDiff =
                            new Date().getTime() -
                            new Date(latestClosedEvent.datetime).getTime();
                            this.offTime = Math.floor(timeDiff / 1000);
                            
                            this.offTimeHours = Math.floor((this.offTime) / 3600);
                            const remainingOffTimeMinutes = this.offTime % 3600;
                            this.offTimeMinutes = Math.floor(remainingOffTimeMinutes / 60);
                            
                        } else {
                            this.offTime = 0;
                        }
                        },
                        error: (err) => {
                        this.snackBar.open(err, 'Ok');
                        },
                    });
                    } else {
                    this.offTime = 0;
                    }
                },
                error: (err) => {
                    this.snackBar.open(err, 'Ok');
                },
            });
            ```
        - [x] Lint & tests
        - [x] Commit
    - [ ] Kp-573 powerpoint /statistiques


**25 Octobre**
- [x] Check des mails
- [ ] Mise à jour Jira
- [ ] Suite kp-556
    - [x] pull develop et résoudre les conflits
    - [x] Résolution de invalid input value for enum event_type_enum: "IR"
    bug fantôme, redémarrage du docker et ok
    - [x] Résolution du problème de ci, suppression des motorUsage en trop dans les mocks des tests
    - [x] kp-574
    - [ ] KP-575
- [x] Kp-573 powerpoint /statistiques
- [x] Réunion rôles keycloak
    - [x] Création du rôle user_cop
    - [x] Export du realm


**26 Octobre**
- [x] Check des mails
- [ ] Mise à jour Jira
- [ ] Suite kp-556
    - [ ] KP-570 moyennes IR & Pédale
        - [x] Création des routes 
        ```
          @Get('ir-events-in-last-hour/:kmoBoxMac')
        async getIREventsInLastHour(@Param('kmoBoxMac') kmoBoxMac: string) {
            const irEvents = await this.eventsService.getIREventsInLastHour(kmoBoxMac);
            return irEvents;
        }

        @Get('pedal-events-in-last-hour/:kmoBoxMac')
        async getPedalEventsInLastHour(@Param('kmoBoxMac') kmoBoxMac: string) {
            const pedalEvents = await this.eventsService.getPedalEventsInLastHour(kmoBoxMac);
            return pedalEvents;
        }
        ```
        - [x] Création dela logique back :
        ```
          async getIREventsInLastHour(kmoBoxMac: string): Promise<Event[]> {
            const oneHourAgo = new Date();
            oneHourAgo.setHours(oneHourAgo.getHours() - 1);

            const irEvents = await this.eventRepository.find({
            where: {
                type: TypeEvent.IR,
                kmoBox: {
                mac: kmoBoxMac,
                },
                datetime: MoreThanOrEqual(oneHourAgo.toISOString()),
            },
            });

            return irEvents;
        }

        async getPedalEventsInLastHour(kmoBoxMac: string): Promise<Event[]> {
            const oneHourAgo = new Date();
            oneHourAgo.setHours(oneHourAgo.getHours() - 1);

            const pedalEvents = await this.eventRepository.find({
            where: {
                type: TypeEvent.PEDAL,
                kmoBox: {
                mac: kmoBoxMac,
                },
                datetime: MoreThanOrEqual(oneHourAgo.toISOString()),
            },
            });

            return pedalEvents;
        }
        ```
        - [x] Ajout logique front : 
        ```
        getIREventsInLastHour(kmoBoxMac: string): Observable<any[]> {
            return this.httpclient.get<any[]>(`${this.apiUrl}/ir-events-in-last-hour/${kmoBoxMac}`);
        }

        getPedalEventsInLastHour(kmoBoxMac: string): Observable<any[]> {
            return this.httpclient.get<any[]>(`${this.apiUrl}/pedal-events-in-last-hour/${kmoBoxMac}`);
        }
        ```
        &
        ```
        this.eventsService.getIREventsInLastHour(kmoBoxMac).subscribe({
        next: (events) => {        
            this.irEventCount = events.length;
        },
        error: (err) => {
            this.snackBar.open(err, 'Ok');
        },
        });

        this.eventsService.getPedalEventsInLastHour(kmoBoxMac).subscribe({
        next: (events) => {        
            this.pedalEventCount = events.length;
        },
        error: (err) => {
            this.snackBar.open(err, 'Ok');
        },
        });
        ```

**27 Octobre**
- [x] Check des mails
- [ ] Mise à jour Jira
- [ ] Réunion exploitation des données
/events
- motor overheat : exploitée par la lambda, pas par l'app X
- trapdoor : exploitée par la lambda, pas par l'app relever et afficher par semaine ->  KP-588
- kmo flag : ? pas exploité, pas relevé X
- motor : pas exploité, mais possibilité : motor = écrit en base ou nb de moteurs détectés ? NtH, voir plus tard
/gw
- datetime : correspond à quoi ?    naissance de la gw, 1ere co à internet, pas à exploiter dans l'application
- disk usage : renvoyé ? Exploitable mais voir sous quelle forme (warning, un message d'état, etc..) visibilité sur /cop/iddumagasin en dessous du message existant                         : V2, NtH
- freeRam : même constat que diskUsage   : V2, NtH
- temperature CPU : température de quoi ? Affichée dans card /cop/magasin
/historygateway
- pas exploitable sur MVP ?
/kmobox

ajouter champ version hardware/software pour gw & kmo
