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
    - [ ] interdire l'accès aux pages /cop aux users dr & manager 