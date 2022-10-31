*** LOG Gregoire Chevalier ***

**31 Octobre**
- [x] Cleanup des fichiers de test
    - [x] Suppression des commentaires
    - [x] Suppression des console.log()
    - [x] Run des tests pour s'assurer de leur passage
- [ ] Push sur KM-297_testsGreg
    - [x] Check que je suis sur la bonne branche en local
    - [x] Commit 
        - [x] Résolution d'erreur de code liées au linting
    - [x] Push
    - [x] Merge
        - [x] Pour note : l'env a été changé pour l'url de prod pour les tablettes :
        ```
        export const environment = {
            production: true,
           - apiUrl: 'http://3.126.39.182:3000/',
           + apiUrl: 'http://18.197.225.248:3000/',
        };
        ```
- [ ] Reprise du testing
- [ ] Voir comment s'organisent les relations via les methodes de CRUD pour les entités
- [ ] Voir comment s'organisent les relations entre les Reports et les entités reliées
- [ ] Survol des fichiers de test de Reports
- [ ] Appliquer les tests à l'entité Reports
- [ ] Tests fonctionnels
- [ ] Tests e2e