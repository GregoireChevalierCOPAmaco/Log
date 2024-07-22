**22 Juillet**
- [ ] Reprise ESLs
    - [x] Fix par Anthony de l'html buggé dans le main
    - [ ] À la recherche du fix de l'enregistrement de l'image
        - [x] Le problème n'a pas l'air de venir d'updateCanvas() dans le TemplateEditorComponent
        - [ ] Recherche sur les services
    - [ ] ESL-86 Restreindre le choix des templates prédéfinis en fonction de la taille de l'esl choisie
        - [x] Création de nouvelle branche : 
        ```git checkout -b feat/ESL-86_display_templates_depending_onWidhtHeight```
        - [x] Demande à GPT : 
        ```
        // component
        // html 
        // templates
        [...]
        currently the url contains width and height parameters ; as with this example : 
        http://192.168.1.252:4200/template-editor?id_esl=c4f00d7e&width=360&height=240
        , so i want to use this to only display in the selector templates that widht & height correspond to the ones in url
        ```
        - [x] Ajout dans le component de :
        ```
          filterTemplates(): void {
            this.filteredTemplates = this.templates.filter(template => template.width === this.width && template.height === this.height);
            this.selectedTemplateIndex = 0;
            this.updateCanvas();
        }
        ```
        - [x] Modif dans le html de :
        ```
        <select [(ngModel)]="selectedTemplateIndex" (change)="updateCanvas()">
        <option *ngFor="let template of templates; let i = index" [value]="i">{{ template.name }}</option>
        </select>
        ```
        à : 
        ```
        <select [(ngModel)]="selectedTemplateIndex" (change)="updateCanvas()">
        <option *ngFor="let template of filteredTemplates; let i = index" [value]="i">{{ template.name }}</option>
        </select>
        ```
        - [x] Rebuild
        - [x] Tests unitaires
        ```
        it('should filter templates based on width and height', () => {
            component.ngOnInit();
            component.filterTemplates();
            expect(component.filteredTemplates.every(template => template.width === 360 && template.height === 240)).toBe(true);
            expect(component.selectedTemplateIndex).toBe(0);
        });

        it('should update canvas after filtering templates', () => {
            component.ngOnInit();
            const updateCanvasSpy = jest.spyOn(component, 'updateCanvas');
            component.filterTemplates();
            expect(updateCanvasSpy).toHaveBeenCalled();
        });
        ```
        - [x] Linting
        - [x] push & PR