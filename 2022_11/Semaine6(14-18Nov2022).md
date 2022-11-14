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
                - [x] Modification du template html ligne 46 de :
                ```
                ngFor="let report of stores?.report | filterStore: filterString">
                ```
                à :
                ```
                ngFor="let report of stores?.report | filter: filterString">
                ```
                && modification du @Pipe{name:} du filter-store.pipe.ts de ```filterStore``` à ```filter``` to match, puis rollback à l'initial car faisait crash l'app
                - [x] Réparation des données test en base pour fonctionnement de l'app
                - [x] Résolution du value = null : ajout de  :
                ```
                if (value==null) {
                   return null;
                }
                ``` ligne 15 du fichier KMO_WEB\COP-SAV\src\app\shared\pipes\filter-store.pipe.ts , ce qui a pour effet de faire passer les test de component isTruthy && isDefined
            - [ ] Établissement des tests pertinents dans le contexte
                - [x] Tester la création du component
                - [x] Tester si le component est défini
                - [x] Tester si l'accès à l'url se fait bien
                - [ ] Tester si le component contient bien les champs requis && qu'ils retournent la valuer attendue
                    - [x] Création d'un fichier /src/windows-mock.ts rempli comme suit :
                    ```
                    import 'jest-preset-angular'; 
                    Object.defineProperty(document, 'doctype', {

                    value: '<!DOCTYPE html>'

                    });

                    Object.defineProperty(document.body.style, 'transform', {

                    value: () => {

                        return {

                        enumerable: true,

                        configurable: true

                        };

                    }
                    });
                    ```
                    suivi de (https://www.devcurry.com/2020/09/testing-angular-component-using-jest.html)
                    - [ ] Input
                        - [x] Trouvé ici comment accéder à l'attribut voulu : (https://indepth.dev/tutorials/angular/get-attribute-value)
                        - [ ] Type
                        - [ ] Class
                        - [ ] Placeholder
                        - [ ] ngModel
                    - [ ] Table
                        - [ ] Date de création
                        - [ ] IMS
                        - [ ] Ref clipper
                        - [ ] Monteurs
                        - [ ] 2nd rdv
                        - [ ] Facturé 
                    - [ ] Body
                        - [ ] Date de création
                        - [ ] IMS
                        - [ ] Ref clipper
                        - [ ] Monteurs
                        - [ ] 2nd rdv
                        - [ ] Facturé 
            - [ ] Tester la présence d'un component form
            - [ ] Tester les paramètres attendus du form
            - [ ] Tester si l'évènement appelé à la submission est le bon & si les données pointent vers le bon endroit ?
            - [ ] Tester le format retourné ?
        - [ ] Tester la réception des données
        - [ ] Tester les données retournées
        - [ ] Tester l’envoi des données retournées au back
- [ ] Utiliser jira en parallèle du log pour communiquer sur ce qui est fait
- [ ] Penser à donner un justificatif de nouveau domicile à hubert