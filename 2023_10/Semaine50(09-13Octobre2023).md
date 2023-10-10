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
        - [ ] Création d'une routine qui cherche les infos
        - [ ] Exploitation des infos en conséquence