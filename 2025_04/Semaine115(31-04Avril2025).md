**31 Mars**
- [ ] ESL
    - [ ] ESL-307 Afficher les chiffres des unités plus grand que le chiffre des décimales des prix dans les templates
        - [x] prompt 
        ```
        eslTemplateManagement
        ...
        this is my updated function.
        1) is it correct ?
        2) is there a method to isolate the decimals from the rest of the $price$ ?
        ```
        réponse : 
        ```
        for (const obj of template.objects) {
            if (obj.type === 'IText') {
                if (obj.text === '$price$') {
                const priceParts = storeArticle.price.toFixed(2).split('.');
                obj.text = `${priceParts[0]}<sup>${priceParts[1]}</sup>`; // Isolate decimals
                console.log('Price updated in object:', obj.text);
                priceFieldFound = true;
        ```
    - [ ] ESL-314-315 Ajouter les tags d'unité de mesure & de prix avant promo
        - [x] entité store article : 
        ```
        import {Column, Entity, JoinColumn, OneToOne, PrimaryGeneratedColumn, UpdateDateColumn} from 'typeorm';
        import {Esl} from '../../esl/entities/esl.entity';

        @Entity()
        export class StoreArticle {
            @PrimaryGeneratedColumn()
            id:number

            @Column()
            name:string

            @Column({nullable:true, type:'float'})
            price:number

            @Column({nullable:true, type:'float'})
            preDiscountPrice:number

            @Column()
            bar_code:string

            @Column({nullable:true})
            origine: string

            @Column({nullable:true, type:'float'})
            price_per_kilo : number

            @UpdateDateColumn({ type: 'timestamp' })
            updatedAt: Date;

            @Column({ nullable: true, type: 'varchar', length: 10 })
            currency: string; //Varchar 10 to support € $ signs

            @Column({nullable:true})
            measurementUnit: string

            @OneToOne(()=> Esl,(esl)=>esl.store_article,{nullable:true})
            @JoinColumn()
            esl:Esl

        }
        ```
        - [x] store article service backend : 
        ```
        eslTemplateManagement
        ...
        let priceFieldFound = false;
        let currencyFieldFound = false;
        let measurementUnitFieldFound = false;
        // const barcodeFieldFound = false;

            const fabricCanvas = new StaticCanvas(null);
            fabricCanvas.setDimensions({ width: template.width, height: template.height });
            fabricCanvas.backgroundColor = '#FFFFFF';

            for (const obj of template.objects) {
                if (obj.type === 'IText') {
                if (obj.text === '$price$') {
                    obj.text = storeArticle.price.toFixed(2).toString();
                    console.log('Price updated in object:', obj.text);
                    priceFieldFound = true;
                } else if (obj.text === '$currency$') {
                    obj.text = storeArticle.currency.toString();
                    console.log('Currency updated in object:', obj.text);
                } else if (obj.text === '$preDiscountPrice$') {
                    obj.text = storeArticle.preDiscountPrice.toFixed(2); // Ensure two decimal places
                    console.log('Pre-discount price updated in object:', obj.text);
                } else if (obj.text === '$measurementUnit$') {
                    obj.text = storeArticle.measurementUnit.toString();
                    console.log('Measurement unit updated in object:', obj.text);
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
            }

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
            } catc  h (logError) {
            console.error('Failed to log request:', logError);
            }

            return results;
        }
        ```
    - [ ] ESL-312 Reformatage de la date de dernière mise à jour dans la liste des produits. Sous forme "date"
        - [x] import de commonModule dans le component
        - [x] modif du html : 
        ```
        <td class="px-4 py-4 text-sm text-gray-500 whitespace-nowrap">{{ article.updatedAt | date: 'dd-MM-yyyy'}}</td>
        ```
        