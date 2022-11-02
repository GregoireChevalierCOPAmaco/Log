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
    - [x] Mock du CRUD
    - [x] Test du Create
    - [ ] Test de l'envoi du mail lors du create
    - [x] Test du Read
    - [ ] Test de l'Update
        - [ ] Résolution des erreurs créées
    - [ ] Test du Delete
        - [ ] Test du  CRUD avec relation à un store, un ticket
- [ ] Tests e2e
- [ ] Penser à donner un justificatif de nouveau domicile à hubert
  
  
**2 Novembre**
- [x] Reprise du testing 
- [ ] Appliquer les tests à l'entité Reports 
    - [ ] Test de l'envoi du mail lors du create
    - [x] Test du Read
    - [x] Test de l'Update
        - [x] Résolution des erreurs créées
            - [x] Résolution de : 
            ```
            No overload matches this call.
            Overload 1 of 4, '(entities: { id?: string; reference?: string; clipperRef?: string; date?: ........... }
        - [x] Console log de report & updateReport. Retour de log :
        ```
        undefined
        at update (reports/reports.service.spec.ts:387:12)

        UpdateResult { generatedMaps: [], raw: [], affected: 2 }
        ```
        - [x] Récupérer le report au lieu d'un undefined
        at update (reports/reports.service.spec.ts:388:12) : 
        changement du paramètre fourni dans le findOne(), passage de new report reference à report reference. Cela retourne le report
- [x] Redémarrage pour résolution des problèmes de push du log
- [x] Passage sur le log sous windows != wsl
- [x] git pull & màj
    - [ ] Test du Delete
    - [ ] Répondre à la question : est-ce normal de pouvoir créer des rerports de même noms ? -> unique sur le champ reference ?
    - [ ] Test du  CRUD avec relation à un store, un ticket
- [ ] Tests e2e
- [ ] Penser à donner un justificatif de nouveau domicile à hubert