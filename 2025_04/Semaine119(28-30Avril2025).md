**28 Avril**
- [ ] ESL
    - [ ] ESL-299 Fix les icones de modification et suppression des storeArticles
        - [ ] Se connecter au serveur
        - [ ] ```git pull origin develop``` & restart des containers
    - [ ] ESL-323 Rendre tous les champs avec $ optionnels dans la création de template
    - [ ] ESL-307 Pour l'affichage du prix sur l'ESL, mettre l'unité plus grande que les décimales. Voir photos des ESL hanshow sur Discord #ESL.
        - [ ] Créer une variable priceIntegerFontSize
        - [ ] Séparer l'entier du prix de ses décimales pour le champ $price$
        - [ ] Appliquer le fontsize existant à la string contenant le point et les décimales du $price$
        - [ ] priceIntegerFontSize = fontSize x2
        - [ ] Intégrer la valeur de priceIntegerFontSize au template
        - [ ] Appliquer le priceIntegerFontSize à la string contenant les entiers du $price$
        - [x] Reprise du ESLTemplateManagement dans le store article service : 
        ```
         for (const obj of template.objects) {
        if (obj.type === 'IText') {
          if (obj.text === '$price$') {
            console.log("$price$ détecté avant split pour integers");
            
            // obj.text = storeArticle.price.toFixed(2).toString();
            // console.log('Price updated in object:', obj.text);
            const priceParts = storeArticle.price.toFixed(2).split('.');
            const integerPart = priceParts[0];
            const decimalPart = priceParts[1];

            console.log("integerPart : " + integerPart);
            console.log("decimalPart : " + decimalPart);
            

            const integerText = new fabric.Text(integerPart, {
              left: obj.left,
              top: obj.top,
              fontSize: obj.fontSize * 2, // Double the font size for the integer part
              fontWeight: obj.fontWeight,
              fontFamily: obj.fontFamily,
              fontStyle: obj.fontStyle,
              lineHeight: obj.lineHeight,
              textAlign: obj.textAlign,
              fill: obj.fill,
            });

            const decimalPointText = new fabric.Text('.', {
              left: obj.left + integerText.width! * integerText.scaleX!,
              top: obj.top,
              fontSize: obj.fontSize,
              fontWeight: obj.fontWeight,
              fontFamily: obj.fontFamily,
              fontStyle: obj.fontStyle,
              lineHeight: obj.lineHeight,
              textAlign: obj.textAlign,
              fill: obj.fill,
            });

            const decimalText = new fabric.Text(decimalPart, {
              left: decimalPointText.left! + decimalPointText.width! * decimalPointText.scaleX!,
              top: obj.top,
              fontSize: obj.fontSize,
              fontWeight: obj.fontWeight,
              fontFamily: obj.fontFamily,
              fontStyle: obj.fontStyle,
              lineHeight: obj.lineHeight,
              textAlign: obj.textAlign,
              fill: obj.fill,
            });

            fabricCanvas.add(integerText);
            fabricCanvas.add(decimalPointText);
            fabricCanvas.add(decimalText);

            priceFieldFound = true;
          } else if (obj.text === '$currency$') {
            obj.text = storeArticle.currency.toString();
            console.log('Currency updated in object:', obj.text);
            currencyFieldFound = true;
          } else if (obj.text === '$preDiscountPrice$') {
            obj.text = storeArticle.preDiscountPrice.toFixed(2).toString();
            console.log('Pre-discount price updated in object:', obj.text);
          } else if (obj.text === '$measurementUnit$') {
            obj.text = storeArticle.measurementUnit.toString();
            console.log('Measurement unit updated in object:', obj.text);
            measurementUnitFieldFound = true;
          } else if (obj.text === '$origin$') {
            obj.text = storeArticle.origine.toString();
            console.log('Origin updated in object:', obj.text);
          } else if (obj.text === '$name$') {
            obj.text = storeArticle.name.toString();
            console.log('Name updated in object:', obj.text);
          } else if (obj.text === '$pkg$') {
            obj.text = storeArticle.price_per_kilo.toString();
            console.log('Price per kilo updated in object:', obj.text);
          } 
        } 
        // console.log("hash de référence : ↓");
        
        // console.log(this.generateHash(this.base64Ref));
        
        if (obj.type === 'Image' && this.isImageSame(obj.src,this.generateHash(this.base64Ref))) {
          try {
            const barcodeImageBase64 = await generateBarcode2(storeArticle.bar_code,esl.width,esl.height);

            obj.src = barcodeImageBase64;
            obj.scaleX =1;
            obj.scaleY =1;
            
            // barcodeFieldFound = true;
          } catch (err) {
            console.error('Error adding barcode image to canvas:', err.message);
          }
        }
        ```
    - [ ] ESL-299 Fix les icones de modification et suppression de storeArticles dans la table
    - [ ] ESL-194 Définir la politique de sauvegarde des données
    - [x] CHECKING DE ESL-307 Modification de la taille des entiers du prix d'ESL
        - [x] Connexion à la prod
        - [x] Pull & switch sur la branche 307
        - [x] Redéploiement
        - [x] Réassignation des bases à l'IP de prod : 192.168.1.252
        - [x] Dissociation de toutes les ESLs
        - [ ] Réassociation de, disons, trois ESLs
        - [ ] Création d'un template pour les tailles d'ESLs
        - [ ] Refresh pour voir l'efficacité du fix.
        - [x] Retour logs, erreur : 
        ```
        associateEsl, backend controller 60 :  { code_produit: '7015511751177', eslId: '145031a2', tagId: [ 1 ] }
        $price$ détecté avant split pour integers
        Error in eslTemplateManagement: storeArticle.price.toFixed is not a function
        $price$ détecté avant split pour integers
        Error in eslTemplateManagement: storeArticle.price.toFixed is not a function
        ```
        - [x] Console.logging du storeArticle.price
        - [x] Solution  : 
        ```
        async eslTemplateManagement(storeArticle: StoreArticle) {
          const results = [];
          const requestLogData: CreateRequestLogDto = {
            eslId: '',
            storeArticleId: null,
            success: false,
            code: '',
            message: '',
          };
          requestLogData.storeArticleId = storeArticle.id;

          try {
            const esl = await this.getEslByStoreArticleId(storeArticle.id);      

            if (!esl || !esl.style) {
              console.warn(`No ESL or ESL style associated with Store Article ID ${storeArticle.id}`);
              requestLogData.message = 'No ESL or ESL style associated';
              requestLogData.code = '404';
              await this.requestLogService.create(requestLogData);
              return results;
            }

            // Validation des dimensions et du style
            const { width, height, style } = esl;
            if (!width || !height || !style.name) {
              console.warn(`Invalid ESL dimensions or style for Store Article ID ${storeArticle.id}`);
              requestLogData.message = 'Invalid ESL dimensions or style';
              requestLogData.code = '500';
              requestLogData.success = false;
              requestLogData.storeArticleId = storeArticle.id;
              requestLogData.eslId = esl.id_esl;
              await this.requestLogService.create(requestLogData);
              return results;
            }

            const template = await this.templateService.findOneByProperties(style.name, width, height);
            // console.log("template : " + template);

            if (!template || !Array.isArray(template.objects) || template.objects.length === 0) {
              console.warn(`Template not found or invalid for style ${style.name}`);
              requestLogData.message = `Template not found or invalid for style ${style.name}`;
              requestLogData.code = '404';
              requestLogData.success = false;
              requestLogData.storeArticleId = storeArticle.id;
              requestLogData.eslId = esl.id_esl;
              await this.requestLogService.create(requestLogData);
              return results;
            }

            let priceFieldFound = false;
            let currencyFieldFound = false;
            let measurementUnitFieldFound = false;
            // const barcodeFieldFound = false;

            const fabricCanvas = new StaticCanvas(null);
            fabricCanvas.setDimensions({ width: template.width, height: template.height });
            fabricCanvas.backgroundColor = '#FFFFFF';
            
            const newObjects = [];

            for (const obj of template.objects) {
              if (obj.type === 'IText') {
                const originalFontSize = obj.fontSize || 20;

                if (obj.text === '$price$') {
                  // const priceParts = storeArticle.price.toFixed(2).split('.');
                  const price = Number(storeArticle.price);
                  if (!isNaN(price)) {
                    const priceParts = new Intl.NumberFormat('en-US', {
                      minimumFractionDigits: 2,
                      maximumFractionDigits: 2,
                      useGrouping: false,
                    })
                      .format(price)
                      .split('.');
                  
                  const integerPart = priceParts[0];
                  const decimalPart = priceParts[1];

                  const integerText = {
                    ...obj,
                    text: integerPart,
                    fontSize: originalFontSize * 2,
                  };

                  const dotText = {
                    ...obj,
                    text: '.',
                    left: obj.left + (integerPart.length * (originalFontSize * 2 * 0.6)),
                    fontSize: originalFontSize,
                  };

                  const decimalText = {
                    ...obj,
                    text: decimalPart,
                    left: dotText.left + (originalFontSize * 0.6),
                    fontSize: originalFontSize,
                  };
                  newObjects.push(integerText, dotText, decimalText);
                  priceFieldFound = true;
                }

                } else if (obj.text === '$currency$') {
                  obj.text = storeArticle.currency.toString();
                  newObjects.push(obj);
                  currencyFieldFound = true;

                } else if (obj.text === '$preDiscountPrice$') {
                  // obj.text = storeArticle.preDiscountPrice.toFixed(2).toString();
                  obj.text = new Intl.NumberFormat('en-US', {
                    minimumFractionDigits: 2,
                    maximumFractionDigits: 2,
                    useGrouping: false,
                  }).format(Number(storeArticle.preDiscountPrice));
                  newObjects.push(obj);

                } else if (obj.text === '$measurementUnit$') {
                  obj.text = storeArticle.measurementUnit.toString();
                  newObjects.push(obj);
                  measurementUnitFieldFound = true;

                } else if (obj.text === '$origin$') {
                  obj.text = storeArticle.origine.toString();
                  newObjects.push(obj);

                } else if (obj.text === '$name$') {
                  obj.text = storeArticle.name.toString();
                  newObjects.push(obj);

                } else if (obj.text === '$pkg$') {
                  obj.text = storeArticle.price_per_kilo.toString();
                  newObjects.push(obj);

                } else {
                  newObjects.push(obj); 
                }

              } else if (obj.type === 'Image' && this.isImageSame(obj.src, this.generateHash(this.base64Ref))) {
                try {
                  const barcodeImageBase64 = await generateBarcode2(storeArticle.bar_code, esl.width, esl.height);
                  obj.src = barcodeImageBase64;
                  obj.scaleX = 1;
                  obj.scaleY = 1;
                } catch (err) {
                  console.error('Error adding barcode image to template:', err.message);
                }

                newObjects.push(obj);

              } else {
                newObjects.push(obj); // Non-text, non-barcode image objects
              }
            }

            template.objects = newObjects;


            if (!priceFieldFound) {
              console.warn(`No "$price$" field found in template for ESL ${esl.id_esl}`);
              requestLogData.code = '404';
              requestLogData.success = false;
              requestLogData.storeArticleId = storeArticle.id;
              requestLogData.eslId = esl.id_esl;
              requestLogData.message = `No "$price$" field found in template for ESL ${esl.id_esl}`;
              await this.requestLogService.create(requestLogData);
              return results;
            }

            if (!currencyFieldFound) {
              console.warn(`No "$currency$" field found in template for ESL ${esl.id_esl}`);
              requestLogData.code = '404';
              requestLogData.success = false;
              requestLogData.storeArticleId = storeArticle.id;
              requestLogData.eslId = esl.id_esl;
              requestLogData.message = `No "$currency$" field found in template for ESL ${esl.id_esl}`;
              await this.requestLogService.create(requestLogData);
              return results;
            }

            if (!measurementUnitFieldFound) {
              console.warn(`No "$measurementUnit$" field found in template for ESL ${esl.id_esl}`);
              requestLogData.code = '404';
              requestLogData.success = false;
              requestLogData.storeArticleId = storeArticle.id;
              requestLogData.eslId = esl.id_esl;
              requestLogData.message = `No "$measurementUnit$" field found in template for ESL ${esl.id_esl}`;
              await this.requestLogService.create(requestLogData);
              return results;
            }

            // Génération et envoi de l'image
            await this.generateAndSendImageToEsl(esl.id_esl, template, storeArticle.bar_code);

          } catch (error) {
            console.error('Error in eslTemplateManagement:', error.message);
            requestLogData.message = error.message || 'Failed to process ESL template management';
            requestLogData.code = '500';
            requestLogData.success = false;
            requestLogData.storeArticleId = storeArticle.id;
            await this.requestLogService.create(requestLogData);

          }

          // Création du log
          try {
            await this.requestLogService.create(requestLogData);
          } catch (logError) {
            console.error('Failed to log request:', logError);
          }

        return results;
      }
      ```