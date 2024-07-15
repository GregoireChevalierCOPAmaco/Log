**08 Juillet**
- [x] Mise au point avec Anthony sur le travail qu'il a fait sur ma branche vendredi
- [x] Ajustement Jira
- [ ] Réunion avec Kévin et Anthony pour l'UI des ESLs
- [ ] Jira 79 & 80


// laisser le component en l'état et y remettre l'ancienne createTemplate
// et renommer en predefinedTemplate 1, 2 & 3. Faire un menu de sélection
// et donner la possiblité de faire les 3 templates 360x240

//  interface principale : liste ESLs
// sur chaque ligne, ajouter un bouton "créer un template" en bout de ligne qui prend en paramètres
// width & height + id_esl (pour further envoi de l'image à ladite ESL) et qui redirige vers
// la page/component correspondant aux templates de la bonne résolution
//  créer un component d'ensemble qui affiche tous les templates relatifs à la résolution et masque les autres



**10 Juillet**
- [x] Sauvegarde du fichier supprimé par PR 18 : template creator : 
```
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import { FormBuilder, FormGroup, ReactiveFormsModule, Validators } from '@angular/forms';
import { TemplateImageService } from '../../services/template-image.service';

@Component({
  selector: 'app-template-creator',
  templateUrl: './template-creator.component.html',
  styleUrls: ['./template-creator.component.scss'],
  imports: [ReactiveFormsModule],
  standalone: true
})
export class TemplateCreatorComponent implements OnInit {
  templateForm: FormGroup;
  id_esl!: string;
  width!: number;
  height!: number;

  constructor(
    private route: ActivatedRoute,
    private fb: FormBuilder,
    private templateImageService: TemplateImageService
  ) {
    this.templateForm = this.fb.group({
      text1: ['', Validators.required],
      text1X: [0, Validators.required],
      text1Y: [0, Validators.required],
      image1: [null],
      image1X: [0, Validators.required],
      image1Y: [0, Validators.required]
    });
  }

  ngOnInit(): void {
    this.route.queryParams.subscribe(params => {
      this.id_esl = params['id_esl'];
      this.width = +params['width'];
      this.height = +params['height'];
    });
  }

  onImageChange(event: any): void {
    const file = event.target.files[0];
    this.templateForm.patchValue({ image1: file });
  }

  // submitTemplate(): void {
  //   const fields = [
  //     { text: this.templateForm.value.text1, image: null, x: this.templateForm.value.text1X, y: this.templateForm.value.text1Y },
  //     { text: null, image: this.templateForm.value.image1, x: this.templateForm.value.image1X, y: this.templateForm.value.image1Y }
  //   ];

  //   this.templateImageService.createCustomTemplateImage(fields, this.id_esl, this.width, this.height)
  //     .then(imageUrl => {
  //       this.templateImageService.saveImage(imageUrl);
  //       // Save le template dans le backend ici
  //       this.templateImageService.saveTemplate({ id_esl: this.id_esl, imageUrl }).subscribe();
  //     })
  //     .catch(error => {
  //       console.error('Error creating template image:', error);
  //     });
  // }
  // submitTemplate(): void {
  //   const fields = [
  //     { text: this.templateForm.value.text1, image: this.templateForm.value.image1, x: this.templateForm.value.text1X, y: this.templateForm.value.text1Y },
  //     { text: null, image: this.templateForm.value.image1, x: this.templateForm.value.image1X, y: this.templateForm.value.image1Y }
  //   ];
  //
  //   if (this.id_esl && this.width && this.height) {
  //     this.templateImageService.createCustomTemplateImage(fields, this.id_esl, this.width, this.height)
  //       .then(imageUrl => {
  //         this.templateImageService.saveImage(imageUrl);
  //         // Save le template en back ici
  //         this.templateImageService.saveTemplate({ id_esl: this.id_esl, imageUrl }).subscribe();
  //       })
  //       .catch(error => {
  //         console.error('Error creating template image:', error);
  //       });
  //   } else {
  //     console.error('ID ESL, width, or height is not defined.');
  //   }
  // }

}
```
- [x] Clean de la branche 68 et ajustement des tests


**11 Juillet**
- [ ] Créer un 2nd template
    - [x] Création et switch de branche ```git checkout -b feat/ESL-54_create2nd_template ``` 
    