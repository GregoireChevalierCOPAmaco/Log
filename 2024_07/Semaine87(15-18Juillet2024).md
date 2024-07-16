**15 Juillet**
- [x] Arrivée et mise au point avec Loïc et Franck sur COPACO (sujet de discussion : stocks)
- [x] Reprise ESL avec :
    - [x] ESL-88 Changer la taille de police des templates
        - [x] Modifications au front : 
        - ajout de fontsize? à l'interface du templateData
        - modifications des templates pour y ajouter la fontsize
        - modification de la logique du component et de updateCanvas()
        - [x] Modifications au back : 
        - ajout de fontsize? à l'entity templateData
        - ajout de fontSize aux DTOs relatifs
        - ajustements de la logique dans le service
    - [x] Lint, résolution des tests, commit & push


**16 Juillet**
- [x] Reprise ESL avec :
    - [x] ESL-85 Rendre les champs de l'éditeur de templates réactifs
        - [x] À GPT : 
        ```
        fichiers icic
        [...]

        this is the current state of my app's files. I am pretty happy about it.
        Now i want one more thing : to have the canvas display in real time the text an user would fill in the fields : [(ngModel)]="text1",  [(ngModel)]="text2" & [(ngModel)]="text3"
        So the wanted behaviour would be : select a template using the selector would setup canvas width, height & content datas' position&fontSize but the text inputted in the fileds could change dynamically on the fly
        ```
        - [x] Changement de la fonction updateCanvas() dans le TemplateEditorComponent : 
        ```
        updateCanvas(): void {
            const canvas = this.canvas.nativeElement;
            const ctx = this.ctx;
            ctx?.clearRect(0, 0, canvas.width, canvas.height);

            const selectedTemplate = this.templates[this.selectedTemplateIndex];

            const textData = [
            { text: this.text1, positionX: selectedTemplate.datas[0].positionX, positionY: selectedTemplate.datas[0].positionY, fontSize: selectedTemplate.datas[0].fontSize },
            { text: this.text2, positionX: selectedTemplate.datas[1].positionX, positionY: selectedTemplate.datas[1].positionY, fontSize: selectedTemplate.datas[1].fontSize },
            { text: this.text3, positionX: selectedTemplate.datas[2].positionX, positionY: selectedTemplate.datas[2].positionY, fontSize: selectedTemplate.datas[2].fontSize }
            ];

            textData.forEach((data) => {
            if (data.fontSize) {
                ctx!.font = `${data.fontSize}px Arial`;
            }
            ctx?.fillText(data.text, data.positionX, data.positionY);
            });
        }
        ```
        - [x] Ajustement du test associé : 
        ```
        it('should update canvas with text contents from selected template on updateCanvas', () => {    
            component.selectedTemplateIndex = 1; 
            component.ngOnInit(); 

            component.text1 = templates[component.selectedTemplateIndex].datas[0].content;
            component.text2 = templates[component.selectedTemplateIndex].datas[1].content;
            component.text3 = templates[component.selectedTemplateIndex].datas[2].content;

            const canvas = component.canvas.nativeElement;
            const ctx = canvas.getContext('2d') as CanvasRenderingContext2D;
            jest.spyOn(ctx, 'clearRect');
            jest.spyOn(ctx, 'fillText');

            component.updateCanvas();

            templates[component.selectedTemplateIndex].datas.forEach((data, index) => {
            const text = [component.text1, component.text2, component.text3][index];
            expect(ctx.fillText).toHaveBeenCalledWith(text, data.positionX, data.positionY);
            });
        });
        ```
- [x] Mise à jour Jira
    - [x] Création de tickets : 
    - ESL-89 Reprendre et terminer la branche 83 d'Anthony
    - ESL-90 Écrire et faire passer les tests unitaires sur les nouvelles fonctionnalités
    - ESL-91 Fix l'affichage du contenu dans la page
    - ESL-92 Voir avec Kévin pour mettre en place une charte graphique rapide et efficace
- [ ] ESL-89 Reprendre et terminer la branche 83 d'Anthony
    - [ ] ESL-90 Écrire et faire passer les tests unitaires sur les nouvelles fonctionnalités