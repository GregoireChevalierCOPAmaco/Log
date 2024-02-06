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


**6 Février**
- [ ] Créer une route API pour checker toutes les x secondes les events trapdoor et recharger la page avec les nouvelles infos le cas échéant
    - [ ] Régler la multiplicité de l'Occurence de periodic refresh
