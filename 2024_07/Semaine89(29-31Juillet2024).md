**29 Juillet**
- [ ] ESL-99 Créer un template "disable" pour chaque ESL avec le code barre de l'esl en question
    - [x] ESL-109 Gérer la disposition du code barre et texte en fonction du format d'ESL:
    ```
    generateImage() {
        const canvas = document.createElement('canvas');
        canvas.width = this.width;
        canvas.height = this.height;
        const ctx = canvas.getContext('2d');
    
        if (ctx) {
        ctx.fillStyle = 'white';
        ctx.fillRect(0, 0, this.width, this.height);
        ctx.fillStyle = 'black';
        ctx.font = '20px Arial';
    
        const text = 'This ESL is disabled';
        const textWidth = ctx.measureText(text).width;
        const textX = (this.width - textWidth) / 2;
        const textY = (this.height / 3) - 10; 
    
        ctx.fillText(text, textX, textY);
    
        const barcodeCanvas = document.createElement('canvas');
        jsBarcode(barcodeCanvas, this.idEsl, { format: 'CODE128' });
    
        const barcodeImage = barcodeCanvas.toDataURL();
        const barcode = new Image();
        barcode.src = barcodeImage;
        barcode.onload = () => {
            
            const maxBarcodeHeight = this.height / 2;
            const scaleFactor = Math.min(1, maxBarcodeHeight / barcodeCanvas.height);
            const barcodeWidth = barcodeCanvas.width * scaleFactor;
            const barcodeHeight = barcodeCanvas.height * scaleFactor;
            const barcodeX = (this.width - barcodeWidth) / 2;
            const barcodeY = (this.height + 30 - barcodeHeight) / 2; 
    
            ctx.drawImage(barcode, barcodeX, barcodeY, barcodeWidth, barcodeHeight);
    
            const finalImage = canvas.toDataURL('image/png');
            this.saveImage(finalImage);
        };
        }
    }
    ```
    - [ ] ESL-110 Changer la logique de génération de template disable depuis la page attitrée vers un simple bouton disable depuis la table ESL :
    ```
    what i want is : 
    - on click of the "Disable" button of the esl-table, redirect to template-editor with the information of height & width in the url (as is for edit button)
    - have a button "disable in the template-editor component that is linked to the logic of the DisableTemplateComponent
    - on click of said Disable button, use generateImage function, and then send the image generated to the ESL, using Base64imageConverterService's functions
    ```

**30 Juillet**
- [x] Photoshop ; fonds pour les ESLs

**31 Juillet, 1er & 2 Aout**
Arrêt 