**09 Octobre**
- [x] Check des mails
    - [x] Acknowledge du mail Orange du 04/01/2023 pour les ids Clients Entreprises
- [x] Review de la PR fix(back) : tiny refacto on websocket at channel returnLastEvent -> returnLastEvents #120
- [ ] Mise à jour Jira
    - [x] KP-491 fix tables sorting
        - [x] Rajout des @Viewchild
- [x] Problème identifié : les cards "magasins les plus critiques" & "magasins à risques" n'affichent pas les infos tant qu'elles ne sont pas chargées.
- [x] Réunion et nouveau sprint
- [ ] Passage à KP-492


**10 Octobre**
- [x] Check des mails
- [ ] Mise à jour Jira
    - [ ] Passage à KP-489
        - [ ] Exposition de la situation à GPT pour partir sur la meilleure solution : 
        ```
        I am working on a project called KMO
        This is a program desinged to retrieve machine informations of checkouts in stores.
        Those datas end up in a postgres database i have access to
        The part of KMO i am working on, as a fullstack web developer, is the application.
        The application runs with angular & nestJs.
        I have a working page of the application that displays stores informations as follows : 
        code /cop ici
        
        Below are the different interfaces created  : 
        code interfaces ici

        Below are the different services created  : 
        code services ici

        Below is the /cop page component :
        code ici avec html

        The /cop page displays stores which, when clicked on, redirects to the store's page which displays the store's informations, amongst which ; checkouts. In the application, checkouts are named "KmoBox"
        KmoBoxes are linked to a gateway (via a gatewayMac property) , which is itself linked to a store (via a store property).

        Here are some useful backend entities : 
        entities ici

        Events are sent from kmoBoxes to their associated gateway and end up in the database.
        Events have two properties i am interested in : state  & datetime.
        state, which informs about the kmoBox's state amongst : 
        export enum StateEvent {
        UP = 'up',
        DOWN = 'down',
        HAS_REBOOTED = 'has_rebooted',
        HELP_ALERT = 'help_alert',
        MAINTENANCE_REQUEST = 'maintenance_request',
        THERMAL_SWITCH = 'thermal_switch',
        VALUE = 'value',
        }

        and datetime, which tells the time at which the event got sent.

        What i need : 
        - retrieve a kmo's last event (get from the database the event that has datetime closest to current date) and store the difference of time between its datetime and the current datetime into a variable named timeDelta
        - retrieve from the last event the state and store it into a variable named kmoCurrentState
        
        The thing is events can be sent multiple times per minute so i want to know what is the most optimal way to track kmoBoxes change of state
        ```
        - [x] Question : Est-ce qu'on veut récupérer à chaque intervalle (minute ?) l'état de chaque caisse de chaque de chaque store ? Non, Anthony conseille d'utiliser un worker Nest
        - [ ] Recherches sur les nest workers
        - [x] Création du worker
        ```
        cd .\kmo-predict-back\
        nest generate service worker/worker

        CREATE src/worker/worker.module.ts (83 bytes)
        UPDATE src/app-test.module.ts (1619 bytes)

        nest generate service worker/worker

        CREATE src/worker/worker/worker.service.spec.ts (460 bytes)
        CREATE src/worker/worker/worker.service.ts (90 bytes)
        UPDATE src/worker/worker.module.ts (170 bytes)
        ```
        - [x] Installation de dépendance à la racine : 
        ```
        npm install --save @nestjs/schedule --workspace=kmo-predict-back --legacy-peer-deps
        ```
        - [x] Création du worker.module
        ```
        import { Module } from '@nestjs/common';
        import { ScheduleModule } from '@nestjs/schedule';
        import { WorkerService } from './worker/worker.service';

        @Module({
        imports: [ScheduleModule.forRoot()],
        providers: [WorkerService],
        })
        export class WorkerModule {}
        ```
        - [x] Création du worker.service
        ```
        import { Injectable, Logger } from '@nestjs/common';
        import { Interval } from '@nestjs/schedule';
        import { Event } from 'src/app/events/entities/event.entity';

        @Injectable()
        export class WorkerService {
        private readonly logger = new Logger(WorkerService.name);

        @Interval(120000) // Run every 2 minutes
        async handleInterval() {
            try {
            // Get today's date as a string in the format "YYYY-MM-DD"
            const today = new Date().toISOString().split('T')[0];

            // Find the most recent event for each KmoBox for today
            const latestEvents = await Event.query(`
                SELECT DISTINCT ON (kmoBoxMac) *
                FROM event
                WHERE datetime::date = $1
                ORDER BY kmoBoxMac, datetime DESC
            `, [today]);

            // Process the latest events as needed
            for (const event of latestEvents) {
                // Your processing logic here
                this.logger.log(`Processed event: ${event.id}`);
            }

            this.logger.log(`Processed ${latestEvents.length} events for today.`);
            console.log('worker de rcupération des events du jour lancé à ' + new Date().toISOString());
            
            } catch (error) {
            this.logger.error(`Error: ${error.message}`);
            }
        }
        }
        ```
        - [x] tests du service : 
        - [x] 
        - [ ] Création d'une routine qui cherche les infos
        - [ ] Exploitation des infos en conséquence

now how can i store all the events isolated by the created store into a variable and display it in my front end with angular in the /cop page ?

- dans event
créer les methodes avec typeorm pour récupérer les events
importer dans le module du worker 
injecter le service event


**12 Octobre**
Rather than doing all the heavy lifting front-end, i would rather use a Nest worker to do it. I want you to  :create me a worker that  :
- is named worker
- sorts the Events table to keep only events of today's date
- keeps one event per different kmoBox mac (the most recent event)
- resets itself every two minutes
- use TypeOrm to deal with every database interaction
- [x] Passage de la logique dans l'events service : 
```
@Interval(20000)
  async processEvents() {    
    try {
      const currentDatetime = new Date();
      currentDatetime.setHours(0, 0, 0, 0); 

      // Find all events for today
      const events = await Event.find({
        relations: ['kmoBox'],
        where: {
          datetime: MoreThanOrEqual(currentDatetime.toISOString()),
        },
      });      

      // Create a map to store the most recent event for each KmoBox
      const kmoBoxEventsMap = new Map<string, Event>();

      // Iterate through events and keep the most recent event for each KmoBox
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

      // Extract the time difference and state for each KmoBox
      const kmoBoxInfoArray = [];
      
      for (const [kmoBoxMac, event] of kmoBoxEventsMap.entries()) {
        const timeDelta = currentDatetime.getTime() - new Date(event.datetime).getTime();
        const kmoCurrentState = event.state;
        kmoBoxInfoArray.push({ mac: kmoBoxMac, timeDelta, kmoCurrentState });
        
      }
      console.log(kmoBoxInfoArray);
      return kmoBoxInfoArray;
      // You can now use kmoBoxInfoArray as needed, e.g., save it to a file or send it to another service.
    } catch (error) {
      console.error(error);
      throw error;
    }
  }
```
- [x] Suppression des workers
- [x] Création d'un event.service.ts dans le front :
```
import { HttpClient } from "@angular/common/http";
import { Injectable } from "@angular/core";
import { Observable } from "rxjs";
import { environment } from "../../../environments/environment";

@Injectable({
    providedIn: 'root',
  })
  export class EventService {
    apiUrl = environment.apiUrl + '/events';
  
    constructor(private httpclient: HttpClient) {}
  
    getTodaysEvents(): Observable<any[]> {
        return this.httpclient.get<any[]>(
          this.apiUrl + 
          '/events/todays-events'
          );
      }

}
```

event service : mettre la logique de /cop qui cherche les caisses up dedans


