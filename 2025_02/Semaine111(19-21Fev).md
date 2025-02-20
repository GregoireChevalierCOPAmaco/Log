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