*** LOG Gregoire Chevalier ***

**24 Octobre**
- [ ] Testing du projet
    - [ ] Tests unitaires
        - [ ] Résolution de tous les problèmes de ```Nest can't resolve dependencies of...```
            - [ ] Résolution du problème pour rc/vehicles/vehicles.controller.spec.ts
                - [ ] Résolution du problème de dépendances de VehiclesService (?, PieceRepo, PieceVehicRepo)
                - [ ] Résolution du problème pour src/statistics/statistics.controller.spec.ts 
                - [ ] Résolution du problème pour src/users/users.service.spec.ts
                - [ ] Résolution du problème pour src/tickets/ticket-history/ticket-history.controller.spec.ts
                - [ ] Résolution du problème pour src/tickets/tickets.service.spec.ts
                - [ ] Résolution du problème pour src/pieces/pieces.controller.spec.ts
                - [ ] Résolution du problème pour src/stores/stores.controller.spec.ts
                - [ ] Résolution du problème pour src/reports/reports.controller.spec.ts
                - [ ] Résolution du problème pour src/tickets/tickets.controller.spec.ts
                - [ ] Résolution du problème pour src/regionals-managements/regionals-managements.controller.spec.ts 
    - [ ] Installer dans kmo-back (working dir) jest-mysql
        - [ ] Configurer proprement le tout
        - [ ] Accéder à global.db  
        - [ ] Tester la connexion à la base
            - [ ] Résoudre le problème de global.db = undefined.
            ```
            Make sure jest and mysql are installed as well in the project, as they are required as peer dependencies.
            ```
            -> installation de mysql
            -> reinstallation de jest-mysql
        - [ ] Tester la persistance des données
    - [ ] Appliquer les tests à l'entité Reports
    - [ ] Tests fonctionnels
    - [ ] Tests e2e
    - [ ] S'assurer que les données arrivent en base

