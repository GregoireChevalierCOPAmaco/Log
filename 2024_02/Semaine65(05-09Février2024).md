**5 février**
- [ ] Créer une route API pour checker toutes les x secondes les events trapdoor et recharger la page avec les nouvelles infos le cas échéant
    - [x] events controller : 
    ```
    @Get('latest-trapdoor-event/:gatewayMac')
    async getLatestTrapdoorEvent(@Param('gatewayMac') gatewayMac: string) {
        try {
        const event = await this.eventsService.getLatestTrapdoorEvent(gatewayMac);
        console.log(event);
        
        return event;
        } catch (error) {
        console.error('Error fetching latest trapdoor event:', error);
        return { error: 'Failed to fetch latest trapdoor event' };
        }
    }

    @Get('gateway-mac-by-kmobox-mac/:kmoBoxMac')
    async getGatewayMacByKmoBoxMac(@Param('kmoBoxMac') kmoBoxMac: string) {
        try {
        const gatewayMac = await this.eventsService.getGatewayMacByKmoBoxMac(kmoBoxMac);
        console.log(gatewayMac);
        return { gatewayMac };
        
        } catch (error) {
        console.error('Error fetching gatewayMac by KmoBox mac:', error);
        return { error: 'Failed to fetch gatewayMac' };
        }
    }
    ```
    - [x] Ajout des méthodes correspondantes dans le service back : 
    ```
    async getLatestTrapdoorEvent(gatewayMac: string): Promise<Event | null> {
        try {
        const event = await Event.createQueryBuilder('event')
            .where('event.type = :type', { type: TypeEvent.TRAPDOOR_TRIGGERED })
            .andWhere('event.kmoBox.gateway.mac = :gatewayMac', { gatewayMac })
            .orderBy('event.datetime', 'DESC')
            .getOne();

        return event;
        } catch (error) {
        console.error('Error fetching latest trapdoor event:', error);
        throw new HttpException('Failed to fetch latest trapdoor event', 500);
        }
    }

    async getGatewayMacByKmoBoxMac(kmoBoxMac: string): Promise<string | null> {
        try {
        const kmoBox = await this.kmoBoxRepository.findOne({
            where: { mac: kmoBoxMac },
            relations: ['gateway'],
        });

        if (kmoBox && kmoBox.gateway) {
            return kmoBox.gateway.mac;
        }

        return null;
        } catch (error) {
        console.error('Error fetching gatewayMac by KmoBox mac:', error);
        throw new HttpException('Failed to fetch gatewayMac', 500);
        }
    }
    ```
    - [x] Ajout dans le service frontend de : 
    ```
    getLatestTrapdoorEvent(gatewayMac: string): Observable<any> {
        return this.httpclient.get<any>(`${this.apiUrl}/latest-trapdoor-event/${gatewayMac}`);
    }

    getGatewayMacByKmoBoxMac(kmoBoxMac: string): Observable<any> {
        return this.httpclient.get<any>(`${this.apiUrl}/gateway-mac-by-kmobox-mac/${kmoBoxMac}`);
    }
    ```
    - [x] Retour stack back : QueryFailedError: cross-database references are not implemented: event.kmobox.gateway.mac
    ```
    Error fetching latest trapdoor event: QueryFailedError: cross-database references are not implemented: event.kmobox.gateway.mac
        at PostgresQueryRunner.query (/app/src/driver/postgres/PostgresQueryRunner.ts:299:19)
        at processTicksAndRejections (node:internal/process/task_queues:95:5)
        at SelectQueryBuilder.loadRawResults (/app/src/query-builder/SelectQueryBuilder.ts:3724:25)
        at SelectQueryBuilder.executeEntitiesAndRawResults (/app/src/query-builder/SelectQueryBuilder.ts:3487:26)
        at SelectQueryBuilder.getRawAndEntities (/app/src/query-builder/SelectQueryBuilder.ts:1661:29)
        at SelectQueryBuilder.getOne (/app/src/query-builder/SelectQueryBuilder.ts:1688:25)
        at EventsService.getLatestTrapdoorEvent (/app/apps/kmo-predict-back/src/app/events/events.service.ts:609:21)
        at EventsController.getLatestTrapdoorEvent (/app/apps/kmo-predict-back/src/app/events/events.controller.ts:85:21)
        at /app/node_modules/@nestjs/core/router/router-execution-context.js:46:28
        at /app/node_modules/@nestjs/core/router/router-proxy.js:9:17 {
      query: 'SELECT "event"."id" AS "event_id", "event"."type" AS "event_type", "event"."duration" AS "event_duration", "event"."datetime" AS "event_datetime", "event"."state" AS "event_state", "event"."stateValue" AS "event_stateValue", "event"."motor" AS "event_motor", "event"."kmoBoxMac" AS "event_kmoBoxMac" FROM "event" "event" WHERE "event"."type" = $1 AND event.kmoBox.gateway.mac = $2 ORDER BY "event"."datetime" DESC',
      parameters: [ 'trapdoor_triggered', '22:a9:fb:ff:d2:bc' ],
      driverError: error: cross-database references are not implemented: event.kmobox.gateway.mac
          at Parser.parseErrorMessage (/app/node_modules/pg-protocol/src/parser.ts:369:69)
          at Parser.handlePacket (/app/node_modules/pg-protocol/src/parser.ts:188:21)
          at Parser.parse (/app/node_modules/pg-protocol/src/parser.ts:103:30)
          at Socket.<anonymous> (/app/node_modules/pg-protocol/src/index.ts:7:48)
          at Socket.emit (node:events:513:28)
          at addChunk (node:internal/streams/readable:324:12)
          at readableAddChunk (node:internal/streams/readable:297:9)
          at Socket.Readable.push (node:internal/streams/readable:234:10)
          at TCP.onStreamRead (node:internal/stream_base_commons:190:23) {
        length: 143,
        severity: 'ERROR',
        code: '0A000',
        detail: undefined,
        hint: undefined,
        position: '352',
        internalPosition: undefined,
        internalQuery: undefined,
        where: undefined,
        schema: undefined,
        table: undefined,
        column: undefined,
        dataType: undefined,
        constraint: undefined,
        file: 'parse_expr.c',
        line: '804',
        routine: 'transformColumnRef'
      },
      length: 143,
      severity: 'ERROR',
      code: '0A000',
      detail: undefined,
      hint: undefined,
      position: '352',
      internalPosition: undefined,
      internalQuery: undefined,
      where: undefined,
      schema: undefined,
      table: undefined,
      column: undefined,
      dataType: undefined,
      constraint: undefined,
      file: 'parse_expr.c',
      line: '804',
      routine: 'transformColumnRef'
    }
    Error fetching latest trapdoor event: HttpException: Failed to fetch latest trapdoor event
        at EventsService.getLatestTrapdoorEvent (/app/apps/kmo-predict-back/src/app/events/events.service.ts:618:13)
        at processTicksAndRejections (node:internal/process/task_queues:95:5)
        at EventsController.getLatestTrapdoorEvent (/app/apps/kmo-predict-back/src/app/events/events.controller.ts:85:21)
        at /app/node_modules/@nestjs/core/router/router-execution-context.js:46:28
        at /app/node_modules/@nestjs/core/router/router-proxy.js:9:17 {
      response: 'Failed to fetch latest trapdoor event',
      status: 500,
      options: undefined
    }
    ```
    - [x] Résolution en changeant la méthode du service : 
    ```
    async getLatestTrapdoorEvent(gatewayMac: string): Promise<Event | null> {
        console.log(gatewayMac);
        
        try {
        const event = await Event.createQueryBuilder('event')
            .leftJoin('event.kmoBox', 'kmoBox')
            .leftJoin('kmoBox.gateway', 'gateway')
            .where('event.type = :type', { type: TypeEvent.TRAPDOOR_TRIGGERED })
            .andWhere('gateway.mac = :gatewayMac', { gatewayMac })
            .orderBy('event.datetime', 'DESC')
            .getOne();

        return event;
        } catch (error) {
        console.error('Error fetching latest trapdoor event:', error);
        throw new HttpException('Failed to fetch latest trapdoor event', 500);
        }
    }
    ```
    - [x] Ajout de la logique de timer & toaster :
    ```
    private startPeriodicRefresh(): void {
        console.log("startPeriodicRefresh");
        
        this.refreshSubscription = interval(20000).pipe(
        switchMap(() => this.refreshData())
        ).subscribe(() => {
        // Optional: You can add additional logic here if needed after each refresh
        });
    }

    private refreshData(): Observable<any> {
        this.showToaster(); 
    
        return of(null);
    }

    private showToaster() {
        const kmoBoxMac = this.data.mac;

        this.eventsService.getGatewayMacByKmoBoxMac(kmoBoxMac).subscribe({
        next: (response) => {
            if (response && response.gatewayMac) {
            const gwMac = response.gatewayMac;

            this.eventsService.getLatestTrapdoorEvent(gwMac).subscribe({
                next: (latestTrapdoorEvent) => {
                this.checkTrapdoorEventTiming(latestTrapdoorEvent);
                },
                error: (err) => {
                this.snackBar.open(err, 'Ok');
                },
            });
            } else {
            console.error('GatewayMac not found in the response');
            }
        },
        error: (err) => {
            this.snackBar.open(err, 'Ok');
        },
        });
        this.startPeriodicRefresh();
    }


    private checkTrapdoorEventTiming(latestTrapdoorEvent: any) {
        const currentTime = new Date().getTime();
        console.log(currentTime);
        
        const eventTime = new Date(latestTrapdoorEvent.datetime).getTime();
        console.log(eventTime);
        
        const timeDifference = currentTime - eventTime;
        console.log("timediff :" + timeDifference);
        

        if (timeDifference < 30000 && timeDifference > 0) {
        // Event occurred within the last 30 seconds
        this.snackBar.open('Trapdoor error!', 'Close', {
            duration: 5000,
            panelClass: ['red-snackbar'], // Add custom CSS class for styling
        });
        } else {
        // Event occurred more than 30 seconds ago
        this.snackBar.open('Trapdoor ok!', 'Close', {
            duration: 5000,
            panelClass: ['green-snackbar'], // Add custom CSS class for styling
        });
        }
    }
    ```
- [ ] Au retrait de la fiche trappe de la caisse, s'assurer que l'état de la caisse en question change d'état de maintenance. Voir en combien de temps le changement s'opère
- [ ] Au retrait de la fiche trappe de la caisse, faire apparaître un toaster qui informe l'utilisateur que la caisse (numéro & magasin) est passé en maintenance prédictive/curative


**7 Février**
- [ ] Créer une route API pour checker toutes les x secondes les events trapdoor et recharger la page avec les nouvelles infos le cas échéant
    - [x] Régler la multiplicité de l'Occurence de periodic refresh : 
    ```
    // ... (other imports)

    @Component({
    // ... (component metadata)
    })
    export class CopDialogComponent implements OnInit {
    // ... (existing class properties)

    private isRefreshing: boolean = false; // Flag to track if data is being refreshed

    // ... (existing constructor and other methods)

    ngOnDestroy(): void {
        // Unsubscribe from the refresh subscription when the component is destroyed
        if (this.refreshSubscription) {
        this.refreshSubscription.unsubscribe();
        }
    }

    private startPeriodicRefresh(): void {
        console.log("startPeriodicRefresh");
        
        this.refreshSubscription = interval(20000).pipe(
        switchMap(() => this.refreshData())
        ).subscribe(() => {
        // Optional: You can add additional logic here if needed after each refresh
        });
    }

    private refreshData(): Observable<any> {
        if (!this.isRefreshing) {
        this.isRefreshing = true;
        this.showToaster(); 
        }

        return of(null);
    }

    private showToaster() {
        const kmoBoxMac = this.data.mac;

        this.eventsService.getGatewayMacByKmoBoxMac(kmoBoxMac).subscribe({
        next: (response) => {
            if (response && response.gatewayMac) {
            const gwMac = response.gatewayMac;

            this.eventsService.getLatestTrapdoorEvent(gwMac).subscribe({
                next: (latestTrapdoorEvent) => {
                this.checkTrapdoorEventTiming(latestTrapdoorEvent);
                this.isRefreshing = false; // Reset the flag after the code execution
                },
                error: (err) => {
                this.snackBar.open(err, 'Ok');
                this.isRefreshing = false; // Reset the flag in case of an error
                },
            });
            } else {
            console.error('GatewayMac not found in the response');
            this.isRefreshing = false; // Reset the flag in case of an error
            }
        },
        error: (err) => {
            this.snackBar.open(err, 'Ok');
            this.isRefreshing = false; // Reset the flag in case of an error
        },
        });
    }

    // ... (existing methods)
    }
    ```



**8 Février**
- [ ] Créer une route API pour checker toutes les x secondes les events trapdoor et recharger la page avec les nouvelles infos le cas échéant
    - [x] Déplacer la logique et la répétabilité sur la page /cop
    - [x] Chercher le dernier event trapdoor & up, modif de la méthode dans le service events :
        - [x] Créer une route API get latest trapdoor up
        ```
        async getLatestTrapdoorEvent(): Promise<Event | null> {
            try {
            const event = await Event.createQueryBuilder('event')
                .leftJoin('event.kmoBox', 'kmoBox')
                .where('event.type = :type', { type: TypeEvent.TRAPDOOR_TRIGGERED })
                .andWhere('event.state = :state', { state: StateEvent.UP })
                .orderBy('event.datetime', 'DESC')
                .getOne();

            return event;
            } catch (error) {
            console.error('Error fetching latest trapdoor event:', error);
            throw new HttpException('Failed to fetch latest trapdoor event', 500);
            }
        }
        ```
    - [x] Checker la date et l'heure, cop-store details component : 
    ```
    const trapdoorDate = new Date(response.datetime);
          const currentDate = new Date();
          const timeDifference = currentDate.getTime() - trapdoorDate.getTime();

    ```
    - [x] Afficher le toaster si date < 30 secondes
    ```
    const timeDifferenceInSeconds = timeDifference / 1000;
    console.log("Time difference in seconds:", timeDifferenceInSeconds);

    if (timeDifferenceInSeconds < 30)
    ```
    - [x] Afficher le numéro de caisse et le magasin confirmé
        - [x] Créer une route API getEventKmobox events controller : 
        ```
        @Get('kmo-box-mac/:eventId')
        async getKmoBoxMacByEventId(@Param('eventId') eventId: string) {
            try {
            const kmoBoxMac = await this.eventsService.getKmoBoxMacByEventId(eventId);
            return { kmoBoxMac };
            } catch (error) {
            console.error('Error fetching kmoBoxMac by eventId:', error);
            return { error: 'Failed to fetch kmoBoxMac' };
            }
        }
        ```
        events service : 
        ```
        async getKmoBoxMacByEventId(eventId: string): Promise<string | null> {
            try {
            const event = await this.eventRepository
                .createQueryBuilder('event')
                .leftJoinAndSelect('event.kmoBox', 'kmoBox')
                .where('event.id = :eventId', { eventId })
                .getOne();
        
            return event ? event.kmoBox?.mac : null;
            } catch (error) {
            console.error('Error fetching kmoBoxMac by eventId:', error);
            throw new HttpException('Failed to fetch kmoBoxMac', 500);
            }
        }
        ```
        - [x] Créer une route API get gateway by kmo mac controller : 
        ```
        @Get(':mac/gateway-mac')
        async getGatewayMacByKmoBoxMac(@Param('mac') kmoBoxMac: string) {
            try {
            const gatewayMac = await this.kmoBoxesService.getGatewayMacByKmoBoxMac(kmoBoxMac);

            if (!gatewayMac) {
                throw new NotFoundException(`GatewayMac not found for KmoBox with MAC '${kmoBoxMac}'`);
            }

            return { gatewayMac };
            } catch (error) {
            throw new NotFoundException(`Error fetching GatewayMac for KmoBox with MAC '${kmoBoxMac}'`);
            }
        }
        ```
        service : 
        ```
        async getGatewayMacByKmoBoxMac(kmoBoxMac: string): Promise<string | undefined> {
            try {
            const kmoBox = await KmoBox.findOne({ where: { mac: kmoBoxMac }, relations: ['gateway'] });

            if (!kmoBox || !kmoBox.gateway) {
                return undefined;
            }

            return kmoBox.gateway.mac;
            } catch (error) {
            throw new HttpException(`Error fetching GatewayMac for KmoBox with MAC '${kmoBoxMac}'`, 500);
            }
        }
        ```
        - [x] Créer une route API get store by gateway mac controller : 
        ```
        @Get('by-gateway-mac/:gatewayMac')
        async findStoreInfoByGatewayMac(@Param('gatewayMac') gatewayMac: string) {
            try {
            const storeInfo = await this.storesService.findStoreInfoByGatewayMac(gatewayMac);

            if (!storeInfo) {
                throw new NotFoundException(`Store not found for Gateway with MAC '${gatewayMac}'`);
            }

            return storeInfo;
            } catch (error) {
            throw new NotFoundException(`Error fetching Store info for Gateway with MAC '${gatewayMac}'`);
            }
        }
        ```
        service : 
        ```
        async findStoreInfoByGatewayMac(gatewayMac: string): Promise<{ address: string, brand: string, city: string } | undefined> {
            try {
            const store = await this.storeRepository.findOne({ where: { gatewayMac }, select: ['address', 'brand', 'city'] });

            if (!store) {
                return undefined;
            }

            return {
                address: store.address,
                brand: store.brand,
                city: store.city,
            };
            } catch (error) {
            throw new HttpException(`Error fetching Store info for Gateway with MAC '${gatewayMac}'`, 500);
            }
        }
        ```
        - [x] Ajout de fonction dans le service front kmo :
        ```
        getGatewayMacByKmoMac(mac: string){
            return this.httpclient.get<any>(`${this.apiUrl}${mac}/gateway-mac`);
        }
        ```
        - [x] Ajout de fonction dans le service front event :
        ```
        getKmoBoxMacByEventId(eventId: string): Observable<any> {
            return this.httpclient.get<any>(`${this.apiUrl}/kmo-box-mac/${eventId}`)
        }
        ```
        - [x] Ajout de fonction dans le service front store :
        ```
        getStoreInfoByGatewayMac(gatewayMac: string): Observable<any> {
            return this.httpclient.get<any>(`${this.apiUrl}/by-gateway-mac/${gatewayMac}`);
        }
        ```
        - [x] Function dans le component : 
        ```
        ngOnDestroy(): void {
            if (this.refreshSubscription) {
            this.refreshSubscription.unsubscribe();
            }
        }

        private startPeriodicRefresh(): void {
            console.log("startPeriodicRefresh");
            
            this.refreshSubscription = interval(20000).pipe(
            switchMap(() => this.refreshData())
            ).subscribe(() => {
            });
        }

        private refreshData(): Observable<any> {
            if (!this.isRefreshing) {
            this.isRefreshing = true;
            this.showToaster(); 
            }

            return of(null);
        }

        private showToaster() {
            this.eventService.getLatestTrapdoorEvent().subscribe({
            next: async (response) => {
                if(response) {

                const trapdoorDate = new Date(response.datetime);
                const currentDate = new Date();
                const timeDifference = currentDate.getTime() - trapdoorDate.getTime();

                const timeDifferenceInSeconds = timeDifference / 1000;
                console.log("Time difference in seconds:", timeDifferenceInSeconds);

                if (timeDifferenceInSeconds < 30) {
                    const kmoMacObservable = this.eventService.getKmoBoxMacByEventId(response.id);
                    const kmoMac = await kmoMacObservable.toPromise();

                    const gwMacObservable = this.kmoBoxesService.getGatewayMacByKmoMac(kmoMac.kmoBoxMac);
                    const gwMac = await gwMacObservable.toPromise();

                    const storeInfoObservable = this.storeService.getStoreInfoByGatewayMac(gwMac.gatewayMac);
                    const storeInfo = await storeInfoObservable.toPromise();
                    this.snackBar.open(
                    'Trapdoor error! A checkout from the store: ' + 
                    `${storeInfo.brand}` + ' located at ' +
                    ` ${storeInfo.address}` + ' ' +
                    `${storeInfo.city}` + ' has had a trapdoor issue !' ,
                    'Close', {
                    duration: 5000,
                    panelClass: ['red-snackbar'],
                    });
                } else {
                    this.snackBar.open('Trapdoor ok!', 'Close', {
                    duration: 5000,
                    panelClass: ['green-snackbar'],
                    });
                }

                this.isRefreshing = false;

                }
            }
            })
        }
        ```

**9 Février**
- [ ] Passer la caisse en état maintenance curative si trapdoor event détecté
    - [ ] Cleaner la base de prod pour tests au 12
        - [ ] Faire un export, copie de la base de prod
        - [ ] Fix le time code utc 0
    - [ ] Update de la prod, pull et rebuild docker
    - [ ] Créer fonction à mettre dans le ngOninit de la modale de caisse pour : si le dernier event trapdoor est up, pass ela caisse en rouge, sinon non