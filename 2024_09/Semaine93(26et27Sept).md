**26 Septembre**
- [x] Accueil alternant
- [ ] Check mails
- [x] Check Discord0
    - [ ] Veille lib Js canvas suggérée par Jacques
        - [ ] Check de (https://interactjs.io/)
        - [ ] Check de (http://fabricjs.com/super-sub-script)
- [ ] ESL
    - [x] Prise de connaisance du point entre Franck & Anthony de la veille
    - [x] Review de la PR ESL-Paris
    - [ ] Création de tickets Jira correspondant aux nouvelles tâches
    - [x] ESL-138 Update le fichier de tests de store-articles dans le back
    Ajout dans le fichier de tests de : 
    ```
    describe('sendImageAbout_associateESl', () => {
        it('should send an image after associating ESL', async () => {
        const associateDto = {
            code_produit: '123456789',
            eslId: 'esl-123',
        };
    
        jest.spyOn(service, 'sendImageAbout_associateESl').mockResolvedValue(undefined);
    
        expect(await controller.sendImageAbout_associateESl(associateDto)).toBeUndefined();
        });
    
        it('should throw an error if image sending fails', async () => {
        const associateDto = {
            code_produit: '123456789',
            eslId: 'esl-123',
        };
    
        jest.spyOn(service, 'sendImageAbout_associateESl').mockImplementation(async () => {
            throw new Error('Image sending failed');
        });
    
        await expect(controller.sendImageAbout_associateESl(associateDto)).rejects.toThrow();
        });
    });
    ```
    à noter que mockResolvedValue retourne undefined à cause de la promesse du service qui attend une Promise: < void>
    - [x] Commit
    - [x] Push & PR
- [x] Réunion de reprise
    - [ ] Décalée au lendemain
- [x] Évaluation des prospects alternant web


**27 Septembre**
- [ ] ESL
    - [x] ESL-138 Update le fichier de tests de store-articles dans le back
        - [x] Merge
    - [ ] ESL-137 Résoudre le mauvais scaling sur les nouvelles ESLs