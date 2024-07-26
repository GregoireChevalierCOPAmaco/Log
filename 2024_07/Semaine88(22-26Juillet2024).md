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

with that said, i want to :
- create a "disableTemplate" component
- add a "disable" button to each line of the esl-table component that redirects to the disableTemplate component, taking as parameters (or in the url) the esl's id_esl, its height & width
- in the disableTemplate" component, i want to have a button that on click, creates an image of the size of the esl's width & height, with a text field that says "This ESL is disabled", and with a barcode corresponding to the barcode of its id_esl, and finally, save that image

disabled template image :
```
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import jsBarcode from 'jsbarcode';

@Component({
  selector: 'app-disable-template',
  templateUrl: './disable-template.component.html',
  styleUrls: ['./disable-template.component.scss']
})
export class DisableTemplateComponent implements OnInit {
  idEsl: string = '';
  width: number = 100;
  height: number = 100;

  constructor(private route: ActivatedRoute) {
    
  }

  ngOnInit(): void {
    this.idEsl = this.route.snapshot.paramMap.get('id_esl')!;
    this.width = Number(this.route.snapshot.paramMap.get('width'));
    this.height = Number(this.route.snapshot.paramMap.get('height'));
  }

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
      ctx.fillText('This ESL is disabled', 10, 50);

      const barcodeCanvas = document.createElement('canvas');
      jsBarcode(barcodeCanvas, this.idEsl, { format: 'CODE128' });

      const barcodeImage = barcodeCanvas.toDataURL();
      const barcode = new Image();
      barcode.src = barcodeImage;
      barcode.onload = () => {
        ctx.drawImage(barcode, 10, 80);

        const finalImage = canvas.toDataURL('image/png');
        this.saveImage(finalImage);
      };
    }
  }

  saveImage(dataUrl: string) {
    const link = document.createElement('a');
    link.href = dataUrl;
    link.download = 'disabled-esl.png';
    link.click();
  }
}
```