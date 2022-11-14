*** LOG Gregoire Chevalier ***
  
**14 Novembre**
- [ ] Tests e2e
    - [ ] Tester le front      
        - [ ] Tester le formulaire
            - [ ] Résolution de l'erreur : 
                ```
                TypeError: Cannot read properties of null (reading 'length')
                ```
                - [x] Check de (https://stackoverflow.com/questions/61681239/async-pipe-sends-null-value-to-the-child-component), voir si no value is emitted by the Observable
            - [ ] Établissement des tests pertinents dans le contexte
            - [ ] Tester la présence d'un component form
            - [ ] Tester les paramètres attendus du form
            - [ ] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ?
            - [ ] Tester le format retourné ?
        - [ ] Tester la réception des données
        - [ ] Tester les données retournées
        - [ ] Tester l’envoi des données retournées au back
- [ ] Utiliser jira en parallèle du log pour communiquer sur ce qui est fait
- [ ] Penser à donner un justificatif de nouveau domicile à hubert