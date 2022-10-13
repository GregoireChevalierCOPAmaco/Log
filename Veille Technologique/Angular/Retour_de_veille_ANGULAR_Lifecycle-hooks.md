Check de (https://angular.io/guide/lifecycle-hooks)
- Une instance de component angular a un cycle de vie qui commence lorsqu'Angular instancie la classe du component et toutes ses vues enfant.
- Le component est changé dynamiquement quand Angular détecte des changements liés au propriétés relatives aux données, et applique les changements aussi bien à l'instance du component qu'à ses vues associées.
- Le cycle de vie se termine quand Angular détruit l'instance du component et retire son template du DOM
- L'application peut utiliser des méthodes de *lifecycle hook* pour intervenir dans les évènements clés du cycle de vie d'un component ou d'une directive afin d'initialiser une nouvelle instance, initier la détection de changements au moment voulu, répondre aux changements pendant la détection du changement, et nettoyer avant la suppression de l'instance.

Check de (https://angular.io/guide/glossary#lifecycle-hook) pour exemples de méthodes de lifecycle hook :
```
    ngOnChanges	When an input or output binding value changes.
	ngOnInit	After the first ngOnChanges.
	ngDoCheck	Developer's custom change detection.
	ngAfterContentInit	After component content initialized.
	ngAfterContentChecked	After every check of component content.
	ngAfterViewInit	After the views of a component are initialized.
	ngAfterViewChecked	After every check of the views of a component.
	ngOnDestroy	Just before the directive is destroyed.
```