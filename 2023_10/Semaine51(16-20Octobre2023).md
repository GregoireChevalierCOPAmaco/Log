**16 Octobre**
- [x] Check des mails
- [ ] Mise à jour Jira
- [ ] Cleanup des lignes de commentaires
    - [x] kp-551 fix le compte des caisses up
    la logique est ok la db n'avait que des events pour une caisse.
    - [x] kp-489 Reprendre la logique pour afficher les caisses fonctionnelles
    - [ ] kp-492 Créer la logique pour calculer le temps d'utilisation d'une caisse
        - [x] GPT : 
        ```
        fourniture des fichiers needed du back
        ...
        what i want : 
        - in my backend : 
        - every two minutes, launch a method that :
            - retrieves all events of today's date
            - for every kmoBox differenciated by mac property, 
                    - for each event emitted that has a property state as down, calculate the time difference between this event and the last event of this kmoBox that has its property state as up
                    - update this kmoBox's uptime property by adding the calculated time difference to the existing number  
        ```
        Je me rends compte que cette logique n'est pas valable : si je reais le calcul toutes les deux minutes ça va fausser le uptime. En fait je dois : 
        - à chaque fois qu'un event qui a en state down est émis
            - récupérer la kmoBox associée
            - récupérer le dernier event de cette kmoBox qui a up en state
            - calculer la différence entre les deux
            - mettre à jour le uptime de cette kmoBox
        reprise de gpt : 
        ```
        what i want : 
        - in my backend : 
        - every time an event that has as a property "state" : down
            - get its "datetime" property
            - retrieve this event's "kmoBoxMac" property
            - find the last event of this kmoBox that has as a property "state" : up
            - calculate the time spent between the two events in seconds and round it to the closest number
            - add this number to the "uptime" property of the kmoBox related to the events
        ```
        - [x] création de branche et checkout
        - [x] Rebuild des containers et relance : ERR_EMPTY_RESPONSE, due au fait que la connection ne peut se faire à la db à cause d'un problème d'enum
        - [x] Destruction containers et rebuild sain de la db 
        - [x] Modif du fichier SQL discord
    - [ ] interdire l'accès aux pages /cop aux users dr & manager 


**17 Octobre**
- [x] Check des mails
- [ ] Mise à jour Jira
- [ ] Reprise kp-492
    - [ ] Résolution des erreurs Type '"xx"' is not assignable to type 'TypeEvent | FindOperator<TypeEvent>'.
    - [x] Modification de l'eventService back : 
    ```
    import { HttpException, Injectable } from '@nestjs/common';
    import { Event } from './entities/event.entity';
    import { CreateEventDto } from './dto/create-event.dto';
    import { TypeEvent } from './enums/type-enum';
    import { getRepository, MoreThanOrEqual, Repository } from 'typeorm';
    import { Interval } from '@nestjs/schedule';
    import { InjectRepository } from '@nestjs/typeorm';
    import { StateEvent } from './enums/state-enum';
    import { KmoBox } from '../kmo-boxes/entities/kmo-box.entity';

    @Injectable()
    export class EventsService {
    constructor(
        @InjectRepository(Event)
        private eventRepository: Repository<Event>,
        // @InjectRepository(KmoBox)
        // private kmoBoxRepository: Repository<KmoBox>
    ){}
    /**
    * Create a new {@link Event}.
    *
    * @param createEvent {@link Event} creation data
    * @returns created {@link Event}
    */
    async createEvent(createEvent: CreateEventDto): Promise<Event> {
        try {
        return await Event.save({ ...createEvent });
        } catch (e) {
        throw new HttpException(e, 500);
        }
    }

    async returnLastEvents(kmoBoxMac: string): Promise<Event[]> {
        const event: Event[] = [];
        for (const type in TypeEvent) {
        event.push(
            await Event.findOne({
            relations: {
                kmoBox: true,
            },
            select: {
                kmoBox: {
                mac: true,
                },
            },
            where: {
                type: TypeEvent[type],
                kmoBox: {
                mac: kmoBoxMac,
                },
            },
            order: {
                datetime: 'DESC',
            },
            }),
        );
        }
        return event;
    }

    @Interval(20000)
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

        const kmoBoxEventsMap = new Map<string, Event>();

        events.forEach((event) => {
            const kmoBoxMac = event.kmoBox.mac;
            if (!kmoBoxEventsMap.has(kmoBoxMac)) {
            kmoBoxEventsMap.set(kmoBoxMac, event);
            } else {
            const existingEvent = kmoBoxEventsMap.get(kmoBoxMac);
            if (event.datetime > existingEvent.datetime) {
                kmoBoxEventsMap.set(kmoBoxMac, event);
            }
            }
        });

        const kmoBoxInfoArray = [];

        for (const [kmoBoxMac, event] of kmoBoxEventsMap.entries()) {
            const timeDelta =
            currentDatetime.getTime() - new Date(event.datetime).getTime();
            const kmoCurrentState = event.state;
            kmoBoxInfoArray.push({ mac: kmoBoxMac, timeDelta, kmoCurrentState });
        }

        return kmoBoxInfoArray;
        } catch (error) {
        console.error(error);
        throw error;
        }
    }


    @Interval(20000)
    async processDownEventsAndUpdateUptime() {
        try {
        const currentDatetime = new Date();
        console.log('currentDatetime : ' + currentDatetime);    
        const twoMinutesAgo = new Date(currentDatetime.getTime() - 10 * 60 * 1000); // Two minutes ago

        // const eventRepository = getRepository(Event);
        console.log(twoMinutesAgo);

        const downEvents = await this.eventRepository
            .createQueryBuilder('event')
            .leftJoinAndSelect('event.kmoBox', 'kmoBox')
            .where('event.state = :state', { state: StateEvent.DOWN })
            // .andWhere('event.datetime >= :twoMinutesAgo', { twoMinutesAgo: twoMinutesAgo.toISOString() })
            .getMany();

        for (const event of downEvents) {
            console.log('downEvents : ' + downEvents);
            
            const kmoBoxMac = event.kmoBox.mac;
            

            const lastUpEvent = await this.eventRepository
            .createQueryBuilder('event')
            .where('event.state = :state', { state: StateEvent.UP })
            .andWhere('event.kmoBox.mac = :kmoBoxMac', { kmoBoxMac })
            .andWhere('event.datetime >= :twoMinutesAgo', { twoMinutesAgo: twoMinutesAgo.toISOString() })
            .orderBy('event.datetime', 'DESC')
            .getOne();

            if (lastUpEvent) {
            console.log('lastUpEvent : ' + lastUpEvent[0]);          
            const timeDiffSeconds = Math.round(
                (new Date(event.datetime).getTime() - new Date(lastUpEvent.datetime).getTime()) / 1000
            );

            // Update the "uptime" property of the KmoBox
            event.kmoBox.uptime += timeDiffSeconds;
            await this.eventRepository.save(event.kmoBox);
            } else {
            // If there is no previous "up" event, set uptime to 0
            event.kmoBox.uptime = 0;
            await this.eventRepository.save(event.kmoBox);
            }
        }
        } catch (error) {
        console.error(error);
        throw error;
        }
    }
    }
    ```
    - [x] Suppression du fichier /back/sql/sql.sql pour éviter les reliquats dans la db
    - [x] Résolution de : 
    ```
     length: 279,
    severity: 'ERROR',
    code: '23502',
    detail: 'Failing row contains (a1aee620-a329-48a8-86a6-51aff2d032ca, null, null, 2023-03-01 16:48:43, null, null, null, null).',
    hint: undefined,
    position: undefined,
    internalPosition: undefined,
    internalQuery: undefined,
    where: undefined,
    schema: 'public',
    table: 'event',
    column: 'type',
    dataType: undefined,
    constraint: undefined,
    file: 'execMain.c',
    line: '1968',
    routine: 'ExecConstraints'
    }
    [Nest] 170  - 10/17/2023, 10:02:40 AM   ERROR [Scheduler] QueryFailedError: null value in column "type" of relation "event" violates not-null constraint 
    ```
    Le problème vient de l'enregistremnt des données en base 
    ```
    // Update the "uptime" property of the KmoBox
    // event.kmoBox.uptime += timeDiffSeconds;
    // await this.eventRepository.save(event.kmoBox);
    ```
    Résolution : importer les repos
    ```
    @InjectRepository(Event)
    private eventRepository: Repository<Event>,
    @InjectRepository(KmoBox)
    private kmoBoxRepository: Repository<KmoBox>,
    ```
    et logique : 
    ```
    @Interval(120000)
    async processDownEventsAndUpdateUptime() {
        try {
        const currentDatetime = new Date();
        console.log('currentDatetime : ' + currentDatetime);
        const twoMinutesAgo = new Date(currentDatetime.getTime() - 2 * 60 * 1000);

        console.log('twoMinutesAgo : ' + twoMinutesAgo);

        const downEvents = await this.eventRepository
            .createQueryBuilder('event')
            .leftJoinAndSelect('event.kmoBox', 'kmoBox')
            .where('event.state = :state', { state: StateEvent.DOWN })
            .andWhere('event.datetime >= :twoMinutesAgo', {
            twoMinutesAgo: twoMinutesAgo.toISOString(),
            })
            .getMany();

        console.log('downEvents : ' + downEvents);
        for (const event of downEvents) {
            console.log(event);
            const kmoBoxMac = event.kmoBox.mac;
            console.log(kmoBoxMac);

            const lastUpEvent = await this.eventRepository
            .createQueryBuilder('event')
            .where('event.state = :state', { state: StateEvent.UP })
            .andWhere('event.kmoBox.mac = :kmoBoxMac', { kmoBoxMac })
            .orderBy('event.datetime', 'DESC')
            .getOne();

            if (lastUpEvent) {
            console.log('lastUpEvent : ' + lastUpEvent);
            const timeDiffSeconds = Math.round(
                (new Date(event.datetime).getTime() -
                new Date(lastUpEvent.datetime).getTime()) /
                1000,
            );

            console.log('timeDiffSeconds : ' + timeDiffSeconds);

            const kmoBox = await this.kmoBoxRepository.findOne({
                where: { mac: kmoBoxMac },
            });

            if (kmoBox) {
                kmoBox.uptime += timeDiffSeconds;
                await this.kmoBoxRepository.save(kmoBox);
                await this.eventRepository.manager.save(kmoBox);
            }
            } else {
            console.log('zéro');
            event.kmoBox.uptime = 0;
            await this.eventRepository.save(event.kmoBox);
            }
        }
        } catch (error) {
        console.error(error);
        throw error;
        }
    }
    ```
    à noter : récupérer la kmo en question pour l'update comme il faut 
    ```
    const kmoBox = await this.kmoBoxRepository.findOne({
    where: { mac: kmoBoxMac },
    });
    ```