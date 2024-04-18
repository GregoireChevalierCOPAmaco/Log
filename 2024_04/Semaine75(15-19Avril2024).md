**15 Avril**
- [ ] Reprise de KP-868 Caisse ouverte - Alors qu'elle semble fermée
    - [x] Fourniture à mistral de cop-dialog component, events service front, events service back, events controller : 
    ```
    Hi, i am a developper working in a company that builds supermarket checkouts and retrieve data from their usage.
    Currently, we have several shops with installed checkouts (we usually call them KmoBox ) connected to a network hardway we call Gateway, which is connected to internet and sends datas to a server.
    On this server i run as dockerized applications a posgresql database, a backend using NestJs, a frontend using Angular, and nginx. 
    Using the data sent by checkouts (events) we determine their state, usage, etc ...
    For instance this is the angular component that shows the state of checkouts of a store : 
    ... [insertion cop dialog component]
    currently, the subscription to getTodaysEvents() does not satisfy me ; using the provided events in databse, i know the checkout should be up or down, but it seems only the case for 'État inconnu' is triggered.
    Also, the case 'up' and 'down' both set the kmoCurrentState to 'Caisse ouverte', which is the expected behaviour

    To further help you i will provide in the next message the frontend event service, the backend events service, the backend event controller, and few other needed files
    ```
    - [x] Réponse mistral : 
    ```
    Based on the provided code, the issue might be related to the getTodaysEvents() function in the frontend events service. This function is making a GET request to the '/todays-events' endpoint, but it seems like the backend does not have a corresponding route to handle this request.

    In the backend events controller, you have a getTodaysEvents() function, but it is mapped to the 'todays-events' endpoint, not '/todays-events'. This could be why the frontend is not receiving the expected data.

    To fix this, you can update the getTodaysEvents() function in the frontend events service to make a GET request to the correct endpoint:

    getTodaysEvents(): Observable<any[]> {
    return this.httpclient.get<any[]>(
        this.apiUrl +
        '/todays-events' // remove the leading slash
    );
    }

    Also, in the backend, the getTodaysEvents() function in the events controller is calling processEvents() function from the events service, which returns an array of objects with mac, timeDelta, and kmoCurrentState properties. But the frontend is expecting an array of events.

    To fix this, you can update the getTodaysEvents() function in the backend events controller to return the events directly:


    @Get('todays-events')
    async getTodaysEvents() {
    const events = await this.eventsService.getAllEvents();
    return events;
    }
    And then update the processEvents() function in the events service to filter the events for today:


    async processEvents() {
    try {
        const currentDatetime = new Date();
        currentDatetime.setHours(0, 0, 0, 0);

        const events = await Event.find({
        relations: ['kmoBox'],
        where: {
            datetime: MoreThanOrEqual(currentDatetime.toISOString()),
        },
        });

        // ... rest of the function
    } catch (error) {
        throw error;
    }
    }
    This way, the frontend will receive an array of events for today, and the kmoCurrentState should be set correctly.
    ```
    - [ ] Application des directives suggérées : 
        - [x] Switch sur fix/KP-868-869_state_of_chekouts
        - [ ] Retrait du slash de '/todays-events' dans getTodaysEvents() de l'event service frontend
        - [ ] Update the getTodaysEvents() function in the backend events controller to return the events directly
        - [ ] Update the processEvents() function in the events service to filter the events for today
- [ ] Préparation de la réunion aws


**17 Avril**
- [ ] KP-868 Caisse ouverte - Alors qu'elle semble fermée
    - [x] Utilisation du nouveau champ en base : isOpened
        - [x] Suppression du code avec les events : 
        ```
        ```
        - [x] Ajout du code :
        ```
        ngOnInit(): void {
        ...
        this.kmoBoxesService.getKmoBoxById(this.data.mac).subscribe({
            next: (data) => {
            this.kmoBox = data;
            this.mapIsOpenedToKmoCurrentState();
            },
            error: (err) => {
            this.snackBar.open(err.message, 'Ok');
            },
        });
        ...
        mapIsOpenedToKmoCurrentState(): void {
            switch (this.kmoBox.isOpened) {
            case 'open':
                this.kmoCurrentState = 'Caisse ouverte';
                break;
                case 'closed':
                this.kmoCurrentState = 'Caisse fermée';
                break;
                case 'power_off':
                this.kmoCurrentState = 'Caisse éteinte';
                break;
            default:
                this.kmoCurrentState = 'État inconnu';
            }
        } 
        ```
    - [x] Add, commit, lint, test & push
    - [x] PR
    - [ ] Déploiement en prod
- [x] KP-942 Optimisation des fonctions du component cop-dialog
- [x] KP-915 Faire passer le fetch des magasins à risque / à surveiller / critiques dans une routine de fond plutôt qu'au clic de la modale
    - [x] Reprise de ngOnInit(), passage de  : 
    ```
    ngOnInit(): void {
        this.lastMaintenanceDate = this.data.lastMaintenance

        this.uptimeHours = Math.floor((this.data.uptime) / 3600);
        const remainingMinutes = this.data.uptime % 3600;
        this.uptimeMinutes = Math.floor(remainingMinutes / 60);

        this.motorOffTime = this.data.uptime - this.data.motorUsage;
        this.motorOffHours = Math.floor(this.motorOffTime / 3600)
        const remainingMotorOffMinutes = this.motorOffTime % 3600;
        this.motorOffMinutes = Math.floor(remainingMotorOffMinutes / 60)

        this.motorUsageHours = Math.floor(this.data.motorUsage / 3600)
        const remainingMotorMinutes = this.data.motorUsage % 3600;
        this.motorUsageMinutes = Math.floor(remainingMotorMinutes / 60)

        this.kmoBoxesService.getKmoBoxById(this.data.mac)
        .pipe(takeUntil(this.destroy$))
        .subscribe({
            next: (data) => {
            this.kmoBox = data;
            },
            error: (err) => {
            this.snackBar.open(err.message, 'Ok');
            },
        });

        const kmoBoxMac = this.data.mac;

        this.eventsService.getLatestCpuTemperatureEvent(kmoBoxMac)
            
        .pipe(takeUntil(this.destroy$))
        .subscribe({
            next: (cpuTemperatureEvent) => {
            if (cpuTemperatureEvent) {
                this.cpuTemperature = cpuTemperatureEvent.stateValue;
            }
            },
            error: (err) => {
            this.snackBar.open(err, 'Ok');
            },
        });

        this.eventsService.getLatestMotorCurrentEvent(kmoBoxMac)
        .pipe(takeUntil(this.destroy$))
        .subscribe({
            next: (motorCurrentEvent) => {
            if (motorCurrentEvent) {
                this.motorCurrent = motorCurrentEvent.stateValue;
            }
            },
            error: (err) => {
            this.snackBar.open(err, 'Ok');
            },
        });

        this.eventsService.getTodaysEvents()
        .pipe(takeUntil(this.destroy$))
        .subscribe({
            next: (events) => {
            if (events.length > 0) {
                const kmoBoxEvents = events.filter((event) => event.mac === kmoBoxMac);

                const lastEvent = kmoBoxEvents.length > 0 ? kmoBoxEvents[kmoBoxEvents.length - 1] : null;

                if (lastEvent) {
                switch (lastEvent.kmoCurrentState) {
                    case 'up':
                    this.kmoCurrentState = 'Caisse ouverte' ;
                    break;
                    case 'down':
                    this.kmoCurrentState = 'Caisse ouverte' ;
                    break;
                    case 'closed':
                    this.kmoCurrentState = 'Caisse fermée';
                    break;
                    case 'power_off':
                    this.kmoCurrentState = 'Power off';
                    break;
                    default:
                    this.kmoCurrentState = 'État inconnu';
                    break;
                }
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

        this.eventsService.getLatestOpenedEvent(kmoBoxMac)
        .pipe(takeUntil(this.destroy$))
        .subscribe({
            next: (latestOpenedEvent) => {
            if (latestOpenedEvent) {
                const currentDate = new Date();
                const openedDate = new Date(latestOpenedEvent.datetime);

                if (
                currentDate.getDate() === openedDate.getDate() &&
                currentDate.getMonth() === openedDate.getMonth() &&
                currentDate.getFullYear() === openedDate.getFullYear()
                ) {
                this.lastOpenedDate = "Aujourd'hui";
                } else {
                this.lastOpenedDate = openedDate.toISOString().slice(0, 10);
                }
            } else {
                this.lastOpenedDate = "Cette caisse n'a pas encore été ouverte";
            }
            }
        });

        this.eventsService.getLatestOpenedEvent(kmoBoxMac)
        .pipe(takeUntil(this.destroy$))
        .subscribe({
            next: (latestOpenedEvent) => {
            if (latestOpenedEvent) {
                const currentDate = new Date();

                const openedDate = new Date(latestOpenedEvent.datetime);

                const timeDifference = currentDate.getTime() - openedDate.getTime();

                this.hoursSinceLastUse = Math.floor(timeDifference / (1000 * 60 * 60));
                this.minutesSinceLastUse = Math.floor((timeDifference % (1000 * 60 * 60)) / (1000 * 60));
            }
            }
        });

        this.kmoBoxesService.getClosedTime(this.data.mac)
        .pipe(takeUntil(this.destroy$))
        .subscribe({
            next: (closedTime) => {
            if (closedTime !== undefined && closedTime.closedTime !== undefined) {
                this.offTime = closedTime.closedTime;
                this.offTimeHours = Math.floor(this.offTime / 3600);
                const remainingOffMinutes = this.offTime % 3600;
                this.offTimeMinutes = Math.floor(remainingOffMinutes / 60);

                this.openTime = this.data.uptime - this.offTime;
                this.openTimeHours = Math.floor(this.openTime / 3600);
                const remainingOpenMinutes = this.openTime % 3600;
                this.openTimeMinutes = Math.floor(remainingOpenMinutes / 60);
            }
            },
            error: (err) => {
            this.snackBar.open(err.message, 'Ok');
            },
        });

        this.eventsService.getIREventsInLastHour(kmoBoxMac)
        .pipe(takeUntil(this.destroy$))
        .subscribe({
            next: (events) => {
            this.irEventCount = events.length;
            },
            error: (err) => {
            this.snackBar.open(err, 'Ok');
            },
        });

        this.eventsService.getPedalEventsInLastHour(kmoBoxMac)
        .pipe(takeUntil(this.destroy$))
        .subscribe({
            next: (events) => {
            this.pedalEventCount = events.length;
            },
            error: (err) => {
            this.snackBar.open(err, 'Ok');
            },
        });

        this.eventsService.getTrapdoorEventsInLastWeek(this.data.mac)
        .pipe(takeUntil(this.destroy$))
        .subscribe({
            next: (events) => {
            this.trapdoorTriggeredEventCount = events.length;
            },
            error: (err) => {
            this.snackBar.open(err, 'Ok');
            },
        });
        this.eventsService.getIsCheckoutOpen(this.data.mac)        
        .pipe(takeUntil(this.destroy$))
        .subscribe({
        next: (isOpen) => {
            this.isOpen = isOpen;
        },
        error: (err) => {
            this.snackBar.open(err, 'Ok');
        },
        });
    }
    ```
    à 
    ```
    ngOnInit(): void {
    this.lastMaintenanceDate = this.data.lastMaintenance;

    this.uptimeHours = Math.floor((this.data.uptime) / 3600);
    const remainingMinutes = this.data.uptime % 3600;
    this.uptimeMinutes = Math.floor(remainingMinutes / 60);

    this.motorOffTime = this.data.uptime - this.data.motorUsage;
    this.motorOffHours = Math.floor(this.motorOffTime / 3600);
    const remainingMotorOffMinutes = this.motorOffTime % 3600;
    this.motorOffMinutes = Math.floor(remainingMotorOffMinutes / 60);

    this.motorUsageHours = Math.floor(this.data.motorUsage / 3600);
    const remainingMotorMinutes = this.data.motorUsage % 3600;
    this.motorUsageMinutes = Math.floor(remainingMotorMinutes / 60);

    const kmoBoxMac = this.data.mac;

    forkJoin([
        this.kmoBoxesService.getKmoBoxById(this.data.mac) as Observable<KmoBoxInterface>,
        this.eventsService.getLatestCpuTemperatureEvent(kmoBoxMac) as Observable<any>,
        this.eventsService.getLatestMotorCurrentEvent(kmoBoxMac) as Observable<any>,
        this.eventsService.getTodaysEvents() as Observable<any[]>,
        this.eventsService.getLatestOpenedEvent(kmoBoxMac) as Observable<any>,
        this.kmoBoxesService.getClosedTime(this.data.mac) as Observable<{ closedTime: number }>,
        this.eventsService.getIREventsInLastHour(kmoBoxMac) as Observable<any[]>,
        this.eventsService.getPedalEventsInLastHour(kmoBoxMac) as Observable<any[]>,
        this.eventsService.getTrapdoorEventsInLastWeek(this.data.mac) as Observable<any[]>,
        this.eventsService.getIsCheckoutOpen(this.data.mac) as Observable<boolean>,
    ]).subscribe({
        next: ([
        kmoBox,
        cpuTemperatureEvent,
        motorCurrentEvent,
        todaysEvents,
        latestOpenedEvent,
        closedTime,
        irEvents,
        pedalEvents,
        trapdoorEvents,
        isOpen,
        ]) => {
        this.kmoBox = kmoBox;

        if (cpuTemperatureEvent) {
            this.cpuTemperature = cpuTemperatureEvent.stateValue;
        }

        if (motorCurrentEvent) {
            this.motorCurrent = motorCurrentEvent.stateValue;
        }

        if (latestOpenedEvent) {
            const currentDate = new Date();
            const openedDate = new Date(latestOpenedEvent.datetime);

            if (
            currentDate.getDate() === openedDate.getDate() &&
            currentDate.getMonth() === openedDate.getMonth() &&
            currentDate.getFullYear() === openedDate.getFullYear()
            ) {
            if (todaysEvents) {
                this.lastOpenedDate = "Aujourd'hui";
            }
            } else {
            this.lastOpenedDate = openedDate.toISOString().slice(0, 10);
            }
        }

        if (closedTime) {
            this.offTime = closedTime.closedTime;
            this.offTimeHours = Math.floor(this.offTime / 3600);
            const remainingOffMinutes = this.offTime % 3600;
            this.offTimeMinutes = Math.floor(remainingOffMinutes / 60);

            this.openTime = this.data.uptime - this.offTime;
            this.openTimeHours = Math.floor(this.openTime / 3600);
            const remainingOpenMinutes = this.openTime % 3600;
            this.openTimeMinutes = Math.floor(remainingOpenMinutes / 60);
        }

        if (irEvents) {
            this.irEventCount = irEvents.length;
        }

        if (pedalEvents) {
            this.pedalEventCount = pedalEvents.length;
        }

        if (trapdoorEvents) {
            this.trapdoorTriggeredEventCount = trapdoorEvents.length;
        }

        this.isOpen = isOpen;
        },
        error: (err) => {
        this.snackBar.open(err, 'Ok');
        },
    });
    }
    ```


**18 Avril**
- [ ] KP-943 optimiser les services backend
    - [ ] KP-945 Optimiser le service backend kmoBoxes