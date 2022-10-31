*** LOG Gregoire Chevalier ***

**31 Octobre**
- [x] Cleanup des fichiers de test
    - [x] Suppression des commentaires
    - [x] Suppression des console.log()
    - [x] Run des tests pour s'assurer de leur passage
- [x] Push sur KM-297_testsGreg
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
- [x] Voir comment s'organisent les relations via les methodes de CRUD pour les entités
    - [x] Les relations sont gérées par les décorators @Unique, @ManyToOne, etc ... dans les fichiers entités. **Logiquement, si les services & controllers sont testés, leurs relations sont viables ?**
    - [x] Check de (https://dkzeb.medium.com/unit-testing-in-ts-jest-with-typeorm-entities-ad5de5f95438) & (https://stackoverflow.com/questions/70092175/mocking-relationships-in-typeorm-nestjs)
- [x] Voir comment s'organisent les relations entre les Reports et les entités reliées
    - [x] Check de KMO_WEB\kmo-back\src\reports\entities\report.entity.ts
    - [x] Check de (https://www.valentinog.com/blog/jest-coverage/) sur le code coverage
- [x] Reprise du testing
- [x] Survol des fichiers de test de Reports
- [ ] Appliquer les tests à l'entité Reports
- [ ] Tests fonctionnels
- [ ] Tests e2e
- [ ] Penser à donner un justificatif de nouveau domicile à hubert