**22 Avril**
- [ ] KP-943 optimiser les services backend
    - [x] KP-944 Optimiser le service backend events
        - [x] Refonte de la méthode returnLastEvents(), passage de :
        ```
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
        ```
        à : 
        ```
        async returnLastEvents(kmoBoxMac: string): Promise<Event[]> {
        const eventPromises = [];
        for (const type in TypeEvent) {
            eventPromises.push(
            Event.findOne({
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
        return Promise.all(eventPromises);
        }
        ```
        ce qui change : 
        Utilisation de Promise.all pour fetch tous les events en parallèle sans utiliser de boucle, ça améliore les perfs globales de la méthode en général et plus spécialement s'il y a beaucoup de types d'events
        - [x] Refonte de la méthode createEvents(), passage de :
        ```
        ```
        à : 
        ```
        ```
        ce qui change : 
        - [x] Refonte de la méthode createEvents(), passage de :
        ```
        ```
        à : 
        ```
        ```
        ce qui change : 