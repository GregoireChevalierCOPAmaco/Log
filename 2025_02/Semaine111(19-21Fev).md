**19 Février**
- [x] Check mails
- [ ] ESL
    - [ ] Poursuite des tickets
        - [ ] ESL-256 Fix la désactivation des ESLs depuis le front
            - [x] Au clic du bouton désactiver sur /management?idEsl=c47....... l'erreur pop :
            ```
            Failed to generate barcode image: Http failure during parsing for http://192.168.1.252:4200/api/esl/barcode/c4700b1e?width=400&height=300
            ```
            ce qui est logique puisque la route /barcode n'existe pas. Le problème vient de l'eslService du frontend
            - [x] Modifs du management component : 
            de
            ```
            onDisableSubmit(event: Event): void {
                event.preventDefault();
                if (this.idEsl && this.width > 0 && this.height > 0) {
                this.eslService.generateBarcodeImage(this.idEsl, this.width, this.height).subscribe({
                    next: (barcodeImage: string) => {
                    const payload: UpdatePictureEslDto = { picture: barcodeImage };
            
                    this.eslService.updateOnEslAndDisable(this.idEsl, payload).subscribe({
                        next: () => alert('ESL updated and disabled successfully!'),
                        error: (err) => alert('Failed to disable ESL: ' + err.message),
                    });
                    },
                    error: (err) => alert('Failed to generate barcode image: ' + err.message),
                });
                } else {
                alert('Invalid ESL parameters. Please check the ID, width, and height.');
                }
            }
            ```
            à
            ```
            onDisableSubmit(event: Event): void {
                event.preventDefault();
                if (this.idEsl && this.width > 0 && this.height > 0) {
                // this.eslService.generateBarcodeImage(this.idEsl, this.width, this.height).subscribe({
                const barcodeImage = this.disableTemplateService.generateImageString(this.idEsl, this.width, this.height);

                    const payload: UpdatePictureEslDto = { picture: barcodeImage };
            
                    this.eslService.updateOnEslAndDisable(this.idEsl, payload).subscribe({
                        next: () => alert('ESL updated and disabled successfully!'),
                        error: (err) => alert('Failed to disable ESL: ' + err.message),
                    });
                } else {
                alert('Invalid ESL parameters. Please check the ID, width, and height.');
                }
            }
            ```
            - [x] Modifs du service de génération de barcode : 
            ```
            generateImageString(id_esl: string, width: number, height: number): string {
                const canvas = document.createElement('canvas');
                canvas.width = width;
                canvas.height = height;
                const ctx = canvas.getContext('2d');
            
                if (!ctx) throw new Error('Failed to generate image');
            
                ctx.fillStyle = 'white';
                ctx.fillRect(0, 0, width, height);
                ctx.fillStyle = 'black';
                ctx.font = '20px Arial';
            
                const text = 'This ESL is disabled';
                const textWidth = ctx.measureText(text).width;
                const textX = (width - textWidth) / 2;
                const textY = (height / 3) - 10;
            
                ctx.fillText(text, textX, textY);
            
                const barcodeCanvas = document.createElement('canvas');
                jsBarcode(barcodeCanvas, id_esl, {
                format: 'CODE128',
                width: 2.5,
                height: height * 0.6, // Adjust barcode height based on main canvas height
                displayValue: true
                });
            
                ctx.drawImage(barcodeCanvas, (width - barcodeCanvas.width) / 2, (height + 30 - barcodeCanvas.height) / 2);
            
                return canvas.toDataURL('image/png'); // Return synchronously
            }
            ```
            - [x] Nouvelle erreur : 
            ```
            PATCH http://192.168.1.252:4200/api/esl/picturedisable/c4700b1e 404 (Not Found)
            ```
            & sur swagger : 
            ```
            Error: Bad Gateway

            Response body
            Download
            {
            "statusCode": 502,
            "message": "Error communicating with ESL service(updateOnEslAndDisable())"
            }
            ```
- [ ] Penser à s'inscrire au salon AWS du 9 avril


**20 Février**
- [x] Check mails
- [ ] ESL
    - [ ] Poursuite des tickets
        - [ ] ESL-256 Fix la désactivation des ESLs depuis le front
            - [x] Nouvelle erreur : 
            ```
            PATCH http://192.168.1.252:4200/api/esl/picturedisable/c4700b1e 404 (Not Found)
            ```
            & sur swagger : 
            ```
            Error: Bad Gateway

            Response body
            Download
            {
            "statusCode": 502,
            "message": "Error communicating with ESL service(updateOnEslAndDisable())"
            }
            ```



**20 Février**
- [x] Check mails
- [ ] ESL
    - [ ] Poursuite des tickets
        - [x] Repasse sur l'application avec Ivann
            - [x] Nouveau ticket : ESL-261 Centrer la barre d'actions d'objet dans l'interface de création de template
            - [x] Nouveau ticket : ESL-262 Assigner le code barre produit au refresh des esl via csv
            - [x] Nouveau ticket : ESL-263 Fix la dissociation du storeArticle de son ESL
            - [x] Nouveau ticket : ESL-264 Ajouter un retour snackbar au clic de faire clignoter une ESL
            - [x] Nouveau ticket : ESL-265 Virer le bouton Editer de la table des store articles
            - [x] Nouveau ticket : ESL-266 Faire un bouton de suppression dans la table des store articles
            - [x] Nouveau ticket : ESL-267 Ajouter un filtre pour trier les ESLs associées
            - [x] Nouveau ticket : ESL-268 Afficher le nom du style des ESLs assoicées
            - [x] Nouveau ticket : ESL-269 Ajouter une interface de changement de style à l'application web
            - [x] Nouveau ticket : ESL-270 Enlever les décimales des labels de charts dans le dashboard
            - [x] Nouveau ticket : ESL-271         Au clic du bouton Sauvegarder de l'édition de template, envoyer un snackbar de confirmation et / ou rediriger vers le menu
            - [x] Nouveau ticket : ESL-272 Fix la barre de recherche de la page storeArticle table
            - [x] Nouveau ticket : ESL-273 Dissocier l'eslId du storeArticle associé en même temps que la dissociation storeArticleID de l'ESL
            - [x] Nouveau ticket : ESL-274 Trouver pourquoi l'esl 15300182 n'est pas GET par findAllPaginate mais est GET par findAll
            - [x] Nouveau ticket : ESL-275 Fix la modification des formes nouvellement ajoutées au template lors de l'édition de template
            - [x] Nouveau ticket : ESL-276 Ajouter un bouton "ajouter champ ..." par champ optionnel lors de la création de template
            - [x] Nouveau ticket : ESL-277 Ajouter un bouton "ajouter champ ..." par champ optionnel lors de l'édition de template
            - [x] Nouveau ticket : ESL-278 Ajouter un retour message erreur/succes à l'application d'un style sur l'app mobile
            - [x] Nouveau ticket : ESL-279 Performer un rafraîchissement de l'application mobile après chaque association/dissociation pour remettre les informations à jour
            - [x] Nouveau ticket : ESL-280 Ajouter un bouton de force refresh dans l'interface de management de l'ESL
            - [x] Nouveau ticket : ESL-281 Application mobile PDA, développer une sulfateuse à style
            - [x] Nouveau ticket : ESL-282 Appliquer un style par défaut à l'initialisation des ESLs
            - [x] Nouveau ticket : ESL-283 Ajouter la concaténation du sigle de devise après le prix dans la création de template
            - [x] Nouveau ticket : ESL-284 Ajouter une sélection de devises dans la création de template
            - [x] Nouveau ticket : ESL-285 Changer le code barre des ESLs depuis le produit vers le ESL Id
            - [x] Nouveau ticket : ESL-286 Changer les labels du tableau ESL table pour avoir des metrics utiles et virer le technique
            - [x] Nouveau ticket : ESL-287 Ajouter un bouton aperçu pour pop-up une prévisualisation de l'ESL dans la ESL table
            - [x] Nouveau ticket : ESL-288 Synthétiser les infos utiles dans le dashboard et mettre en forme façon Delfi
            - [x] Nouveau ticket : ESL-289 Virer la modale des esls non assignées au clic de submit du csv, et la déplacer ailleurs où c'est pertinent