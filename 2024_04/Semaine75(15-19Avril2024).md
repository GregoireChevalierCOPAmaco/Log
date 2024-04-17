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
- [ ] KP-942 Optimisation des fonctions du component cop-dialog
- [ ] KP-915 Faire passer le fetch des magasins à risque / à surveiller / critiques dans une routine de fond plutôt qu'au clic de la modale