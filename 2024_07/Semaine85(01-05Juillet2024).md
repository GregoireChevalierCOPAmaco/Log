**1er Juillet**
- [x] Arrivée et brief Loïc
- [ ] Reprise ESLs
    - [ ] ESL-62 Créer un menu de sélection pour les templates
        - [x] Ajout du sélector et modif du service : 
        ```
            createTemplateImage(text1: string, text2: string, text3: string, image: File, templateType: string): Promise<string> {
            return new Promise((resolve, reject) => {
            const canvas = document.createElement('canvas');
            canvas.width = 360;
            canvas.height = 240;
            const ctx = canvas.getContext('2d');
        
            if (ctx) {
                // Set background color to white
                ctx.fillStyle = '#ffffff';
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                const img = new Image();
                img.onload = () => {
                ctx.drawImage(img, 0, 0, 180, 240);

                ctx.fillStyle = '#000000'; // Set text color to black
                ctx.font = '16px Arial'; // Set font size and type

                if (templateType === 'template1') {
                    ctx.fillText(text1, 190, 50);
                    ctx.fillText(text2, 190, 100);
                    ctx.fillText(text3, 190, 150);
                } else if (templateType === 'template2') {
                    ctx.fillText(text1, 10, 50);
                    ctx.fillText(text2, 10, 100);
                    ctx.fillText(text3, 10, 150);
                } else if (templateType === 'template3') {
                    ctx.fillText(text1, 50, 30);
                    ctx.fillText(text2, 50, 90);
                    ctx.fillText(text3, 50, 150);
                }
        
                const url = canvas.toDataURL('image/jpeg');
                resolve(url);
                };
        
                img.onerror = () => {
                reject('Error loading image');
                };

                img.src = URL.createObjectURL(image);
            } else {
                reject('Error getting canvas context');
            }
            });
        }
        ```
        mais erreur : 
        ```
        ✘ [ERROR] NG8002: Can't bind to 'ngModel' since it isn't a known property of 'select'. [plugin angular-compiler]    
        esl_front     |
        esl_front     |     src/app/template-image-creator/template-image-creator.component.html:7:10:
        ```
        - [x] ```git reset --hard```
        - [x] Essai autrement : utilisation de la table template du backend pour 
            - offrir la possibilité à l'user de définir les coordonnées x,y des champs texte et de l'image (et reload au submit)
            - enregistrer ledit template créé en base avec un nom de template
            - sélectionner un template existant en base depuis un selector
    - [ ] Test de la table des esls
        - [ ] Ajout d'esls en base
            - [ ] Connexion via adminer à (localhost:8080)
    - [ ] Résolution du bug de non affichage des esls dans la table
        - [x] Modif du component : 
        ```
        getEsls(page: number): void {
            this.eslService.getEsls(page, this.pageSize).subscribe(
            response => {
                console.log(response);
                this.esls = response;
                this.totalItems = response.length;
                this.cdr.markForCheck();
            },
            error => {
                console.error('Error fetching ESLs:', error);
            }
            );
        }
        ```
        - [x] Modif du service front : 
        ```
        getEsls(page: number, limit: number): Observable<any[]> {
            return this.httpClient.get<any[]>(`${this.route}?page=${page}&limit=${limit}`);
        }  
        ```

**3 Juillet**
- [ ] Suite ESLs
    - [ ] Lien de la creation de template au backend
        - [x] Switch de branche 
        ```
        git checkout -b feat/ESL-68_link_template_creation_toBackend
        ```
        - [x] Description des attentes à gpt : 
        ```
        this is my component : 

        [...] code fourni ici

        what i want is : 
        - change the html form to be with one field for the name of the template to create, one field for an image to load, two fields for the x and y position of the image, and then one text field with two fileds for its x and y coordinates (as for the image) and a button to add another extra field (repeatable)
        - make the template creation logic (createTemplate, not createTemplateImage) frontend and link it to the backend existing logic
        - make possible to create a template on click of a button when submitting a form with an image and x text fields
        - save that template into the backend when created
        - allow the selection of an existing template via dropdown in the component
        - create the template display html and logic and  display the selected template on the page to fill 
        - link the createTemplateImage() function to the eventual selected template
        ```
        - [x] Changement du html : 
        ```
        <div>
            <input type="text" [(ngModel)]="templateName" placeholder="Enter template name">

            <input type="file" (change)="onFileSelected($event)">
            <input type="number" [(ngModel)]="imageX" placeholder="Image X position">
            <input type="number" [(ngModel)]="imageY" placeholder="Image Y position">

            <div *ngFor="let field of textFields; let i = index">
                <input type="text" [(ngModel)]="field.text" placeholder="Enter text {{i + 1}}">
                <input type="number" [(ngModel)]="field.x" placeholder="Text {{i + 1}} X position">
                <input type="number" [(ngModel)]="field.y" placeholder="Text {{i + 1}} Y position">
            </div>

            <button (click)="addTextField()">Add Text Field</button>
            <button (click)="createTemplate()">Create Template</button>
            
            <select [(ngModel)]="selectedTemplate" (change)="onSelectTemplate($event.target.value)">
                <option *ngFor="let template of templates" [value]="template">{{ template.name }}</option>
            </select>

            <button (click)="createTemplateImage()">Create Template Image</button>
            </div>

            <div *ngIf="selectedTemplate">
            <h3>Selected Template: {{ selectedTemplate.name }}</h3>
            <div *ngFor="let data of selectedTemplate.templateDatas">
                <p>Text: {{ data.text }} ({{ data.positionX }}, {{ data.positionY }})</p>
            </div>
        </div>
        ```
        - [x] Changement du component : 
        ```
        import { Component } from '@angular/core';
        import { MatDialog } from '@angular/material/dialog';
        import { TemplateImageService } from '../../services/template-image.service';
        import { TemplateImagePreviewComponent } from '../template-image-preview/template-image-preview.component';
        import { TemplateService } from '../../services/template-service';

        @Component({
        selector: 'app-template-image-creator',
        templateUrl: './template-image-creator.component.html',
        styleUrls: ['./template-image-creator.component.scss']
        })
        export class TemplateImageCreatorComponent {
        templateName: string = '';
        selectedImage: File | null = null;
        imageX: number = 0;
        imageY: number = 0;
        textFields: { text: string, x: number, y: number }[] = [{ text: '', x: 0, y: 0 }];
        templates: any[] = [];
        selectedTemplate: any;
        generatedImageUrl: string = '';

        constructor(
            private templateImageService: TemplateImageService,
            private templateService: TemplateService,
            private dialog: MatDialog
        ) {}

        onFileSelected(event: Event) {
            const input = event.target as HTMLInputElement;
            if (input.files && input.files.length > 0) {
            this.selectedImage = input.files[0];
            }
        }

        addTextField() {
            this.textFields.push({ text: '', x: 0, y: 0 });
        }

        createTemplate() {
            if (this.selectedImage) {
            const templateData = this.textFields.map(field => ({
                text: field.text,
                positionX: field.x,
                positionY: field.y
            }));
            const template = {
                name: this.templateName,
                templateDatas: templateData
            };

            this.templateService.createTemplate(template).subscribe(response => {
                console.log('Template created successfully', response);
                this.loadTemplates();
            }, error => {
                console.error('Error creating template', error);
            });
            } else {
            alert('Please select an image');
            }
        }

        loadTemplates() {
            this.templateService.getTemplates().subscribe(templates => {
            this.templates = templates;
            });
        }

        onSelectTemplate(template: any) {
            this.selectedTemplate = template;
        }

        createTemplateImage() {
            if (this.selectedImage && this.selectedTemplate) {
            const { templateDatas } = this.selectedTemplate;
            const fields = templateDatas.map((data: any) => ({
                text: data.text,
                x: data.positionX,
                y: data.positionY
            }));

            this.templateImageService.createCustomTemplateImage(
                fields[0].text, fields[0].x, fields[0].y,
                fields[1]?.text, fields[1]?.x, fields[1]?.y,
                fields[2]?.text, fields[2]?.x, fields[2]?.y,
                this.selectedImage, this.imageX, this.imageY
            ).then(imageUrl => {
                this.generatedImageUrl = imageUrl;
                this.openPreviewModal(this.generatedImageUrl);
            });
            } else {
            alert('Please select an image and a template');
            }
        }

        private openPreviewModal(imageUrl: string) {
            const dialogRef = this.dialog.open(TemplateImagePreviewComponent, {
            data: { imageUrl },
            width: '500px'
            });

            dialogRef.afterClosed().subscribe(result => {
            if (result) {
                this.templateImageService.saveImage(this.generatedImageUrl);
            }
            });
        }

        ngOnInit() {
            this.loadTemplates();
        }
        }
        ```
        - [x] Création d'un service template dans le front : 
        ```
        import { Injectable } from '@angular/core';
        import { HttpClient } from '@angular/common/http';
        import { Observable } from 'rxjs';

        @Injectable({
        providedIn: 'root'
        })
        export class TemplateService {
        private baseUrl = 'http://your-backend-url/api/Templates';

        constructor(private http: HttpClient) {}

        createTemplate(template: any): Observable<any> {
            return this.http.post(`${this.baseUrl}`, template);
        }

        getTemplates(): Observable<any[]> {
            return this.http.get<any[]>(`${this.baseUrl}`);
        }
        }
        ```
        - [ ] Résolution des erreurs de build angular


i am a developer working on an Electronic Shleves Labeling (ESL) project.
The idea is to allow a user to simply edit ESLs from a wab app filling some fields.
I am using Angular, nest, postgres and MQTT, all dockerized. 
Mqtt and postgres work fine, lets focus on the angular nest part.

ESLs are managed like this : as it is a technology that uses liquid crystals keeping in memory pixels so it does not refresh any other way but by user input, and then saves energy.
To lighten the process and save ESL memory, i create base-64 images that are sent to the ESL.
We call the images sent to the ESL "Templates". Below, the backend entity & its DTO : 


These templates contain TemplateData, which are the content itself of the Template. Below the entity & DTO : 


[...]


here is the TemplateService backend : 

[...]


and here is the TemplateData service backend : 

[...]



now as it stands, the frontend angular is as follows : i have a component supposed to generate Templates using TemplateDatas submitted via html form. Below the files : 

[...]


**4 Juillet**
- [ ] Suite ESLs
  - [ ] Appliquer les directives : 
  ```
  // laisser le component en l'état et y remettre l'ancienne createTemplate
  // et renommer en predefinedTemplate 1, 2 & 3. Faire un menu de sélection
  // et donner la possiblité de faire les 3 templates 360x240
  ```