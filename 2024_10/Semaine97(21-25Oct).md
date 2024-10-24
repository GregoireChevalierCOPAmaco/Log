**21 Octobre**
- [ ] ESL
    - [ ] ESL-147 Mettre à jour les ESLs en fournissant un fichier en entrée
        - [ ] ESL-148 Créer une page pour l'insertion d'un fichier en entrée
            - [x] Prompt à GPT : 
            ```
            Currently, my project is setup as follows : 
            the angular frontend, the nestJs backend, the database, a python mqtt api, a python mqtt broker, all hosted on a server and deployed via docker containers orchestrated by docker compose.
            The app works well, as i am able to detect and interact with the ESLs (detect them, make a LED on the ESL blink, disable them and change the image).
            Right now what i want is to create an angular page that would offer me the possibility to upload a file (csv or excel for example) filled with store-article infos, and make logic to filter informations to change store-articles in database.
            Below are files to help : 
            ```
            avec fichiers fournis
            - [x] Ajout dans le fichier article-list.component.html
            de :
            ```
            <div class="file-upload-section">
                <input type="file" (change)="handleFileInput($event)" accept=".csv">
            </div>
            ```
            - [x] Ajout dans le fichier article-list.component.ts
            de :
            ```
            handleFileInput(event: Event): void {
                const inputElement  = event.target as HTMLInputElement; 
                if (inputElement .files && inputElement .files.length > 0) {
                const file = inputElement .files[0]; 
                if (file) {
                    this.parseCSV(file);
                }
                }
            }
            
            parseCSV(file: File): void {
                Papa.parse(file, {
                header: true,
                skipEmptyLines: true,
                complete: (result) => {
                    this.csvData = result.data;
                    this.uploadCSVData(this.csvData);
                }
                });
            }

            uploadCSVData(data: any[]): void {
                this.storeArticleService.updateArticlesFromCSV(data).subscribe(
                response => {
                    console.log('CSV data processed successfully:', response);
                    this.getArticle(this.currentPage); // Refresh the list
                },
                error => {
                    console.error('Error processing CSV data:', error);
                }
                );
            }
            ```
            - [x] Ajout dans le fichier store article service (front)
            de :
            ```
            updateArticlesFromCSV(data: any[]): Observable<any> {
                return this.httpClient.post(`${this.apiUrl}/upload-csv`, data);
            }
            ```
            - [x] Ajout dans le fichier store article service (back)
            de :
            ```
            async updateArticlesFromCSV(csvData: any[]): Promise<void> {
                try {
                for (const row of csvData) {
                    const { bar_code, name, price, origine } = row;

                    let article = await this.storeArticleRepository.findOne({ where: { bar_code } });

                    if (article) {
                    article.name = name;
                    article.price = price;
                    article.origine = origine;
                    await this.storeArticleRepository.save(article);
                    } else {
                    const newArticle = this.storeArticleRepository.create({
                        bar_code,
                        name,
                        price,
                        origine,
                    });
                    await this.storeArticleRepository.save(newArticle);
                    }
                }
                } catch (error) {
                throw new HttpException('Failed to process CSV data', 500);
                }
            }
            ```
            - [x] Ajout dans le fichier store article controller (back)
            de :
            ```
            @Post('upload-csv')
            async uploadCSV(@Body() csvData: any[]): Promise<void> {
                return this.storeArticlesService.updateArticlesFromCSV(csvData);
            }
            ```
            - [x] Update des fichiers de test
        - [x] ESL-149 Extraire les données du fichier entrée
        - [ ] ESL-150 Créer un bouton pour mettre à jour toutes les ESLs concernées par un changement depuis la page d' soumission de fichier entrée
        - [ ] ESL-151 Créer la logique back pour recharger toutes les ESLs
        - [ ] ESL-152 Créer la logique front pour recharger toutes les ESLs
        - [ ] ESL-156 Filtrer les données du fichier en entrée pour exclure la màj des store-articles pas liés à une ESL
        - [ ] ESL-157 Filtrer les données du fichier en entrée pour pointer à l'utilisateur les ESLs n'étant pas liées à un article


**22 Octobre**
- [ ] ESL
    - [ ] ESL-147 Mettre à jour les ESLs en fournissant un fichier en entrée
        - [x] ESL-158 Créer un fichier csv placeholder et console.log() ses données
        ```
        id,name,price,bar_code,origine,updatedAt,rssiUpdatedAt,eslId
        1,Bananes bio,3.59,1234567890123,France,2024-10-20 12:30:00,2024-10-21 10:00:00,14d0178c
        2,Coca-cola 33 cl,4.58,9876543210987,États-Unis,2024-10-21 11:20:00,2024-10-21 10:00:00,15300198
        3,Pêches plates,2.69,1234598765432,Spain,2024-10-20 09:10:00,2024-10-20 08:00:00,145032eb
        ```
        &
        ```
        parseCSV(file: File): void {
            Papa.parse(file, {
            header: true,
            skipEmptyLines: true,
            complete: (result) => {
                this.csvData = result.data;
                this.uploadCSVData(this.csvData);
                
                console.log('%cCSV Data (Raw Object):', 'color: blue; font-weight: bold;');
                console.log(this.csvData); 
                
                console.log('%cCSV Data (Table Format):', 'color: green; font-weight: bold;');
                console.table(this.csvData); 
            }
            });
        }
        ```
        - [ ] ESL-150 Créer un bouton pour mettre à jour toutes les ESLs concernées par un changement depuis la page d' soumission de fichier entrée
            - [x] Résolution de l'erreur 500 : 
            ```
            article-list.component.ts:87 Error processing CSV data: 
            HttpErrorResponse {headers: _HttpHeaders, status: 500, statusText: 'Internal Server Error', url: 'http://localhost:3000/store-articles/upload-csv', ok: false, …}
            error
            : 
            {statusCode: 500, message: 'Failed to process CSV data'}
            headers
            : 
            _HttpHeaders {normalizedNames: Map(0), lazyUpdate: null, lazyInit: ƒ}
            message
            : 
            "Http failure response for http://localhost:3000/store-articles/upload-csv: 500 Internal Server Error"
            name
            : 
            "HttpErrorResponse"
            ok
            : 
            false
            status
            : 
            500
            statusText
            : 
            "Internal Server Error"
            url
            : 
            "http://localhost:3000/store-articles/upload-csv"
            ```
            - [x] Modification de la méthode update du store articles service backend, ajout de  : 
            ```
            if (updateStoreArticleDto.esl) {
                const esl = await this.Esl.findOne({ where: { id_esl: updateStoreArticleDto.esl.id_esl } });
                if (!esl) {
                    throw new HttpException('ESL not found', 404);
                }
                article.esl = esl;
            }
            ```
            - [x] Modification de la méthode updateArticleFromCSV : 
            ```
            // If the eslId is present, find the ESL and associate it
            if (eslId) {
                const esl = await this.Esl.findOne({ where: { id_esl: eslId } });
                if (!esl) {
                    console.error(`ESL not found for eslId: ${eslId}`);
                } else {
                    article.esl = esl; // Associate ESL with the article
                }
            }
            ```
            - [x] Modification de l'entité store-article :
            ```
            @Column({nullable:true, type:'float'})
            price:number
            ```
            rajout du type float pour que la base de données pour que number soit compris comme float et pas integer
        - [ ] ESL-151 Créer la logique back pour recharger toutes les ESLs
        - [ ] ESL-152 Créer la logique front pour recharger toutes les ESLs
        - [ ] ESL-156 Filtrer les données du fichier en entrée pour exclure la màj des store-articles pas liés à une ESL
        - [ ] ESL-157 Filtrer les données du fichier en entrée pour pointer à l'utilisateur les ESLs n'étant pas liées à un article


**22 Octobre**
- [ ] ESL
    - [ ] ESL-147 Mettre à jour les ESLs en fournissant un fichier en entrée
        - [ ] Changer le store-article entity pour que la colonne esl soit manyToOne ? ouoneTo Many ?
        - [ ] ESL-152 Créer le front pour recharger toutes les ESLs
        - [ ] ESL-151 Créer la logique back pour recharger toutes les ESLs
        - [ ] ESL-159 Gérer le rechargement en masse des ESL