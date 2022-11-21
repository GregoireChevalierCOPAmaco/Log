**18 Novembre** 
- [ ] Tests e2e
    - [ ] Tester le fichier report-cop-view.component.spec.ts
        - [ ] Poursuite de résolution d'erreurs
            - [ ] Trouver pourquoi le store renseigné via l'id en dur est undefined, trouver comment atteindre res :
            ```
            this.reportService.getReportById(this.idReport).subscribe(res => {
			this.report = res;
            ...
            ```
            - [ ] Tester la présence d'un component form
        - [ ] Tester les paramètres attendus du form
        - [ ] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ?
    - [ ] Tester le fichier aldi-new-report.page.ts
        - [ ] Tester la présence d'un component form
        - [ ] Tester les paramètres attendus du form
        - [ ] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ?
    - [ ] Tester le fichier lidl-new-report.page.ts
        - [ ] Tester la présence d'un component form
        - [ ] Tester les paramètres attendus du form
        - [ ] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ?
        