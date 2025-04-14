**14 Avril**
- [ ] ESL
    - [ ] ESL-287 Ajouter un bouton aperçu pour pop-up une prévisualisation de l'ESL dans la ESL table
        - [ ] Refonte de la fonction du service template back


    refaire la fonction getTemplateByEslId du service template back : 
        async getTemplateByEslId(eslId: string): Promise<Template> {
    return this.findByEslId(eslId);
  }
  pour récupérer l'esl, son style, puis trier les templates sur la base du style et des dimensions