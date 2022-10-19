Installation :
npm i --save-dev @nestjs/testing 
  
Run de tous les tests :
cd kmo-back  
npm test
  
Run de tests particuliers :
npm test 'exterior-contacts'  
-> exécution de tous les tests qui matchent la regex dans les '' au sein du projet

**Résolution de l'erreur :** 
```
FAIL  src/exterior-contacts/exterior-contacts.service.spec.ts (5.384 s)
● ExteriorContactsService › should be defined
Nest can't resolve dependencies of the ExteriorContactsService (?). Please make sure that the argument ExteriorContactRepository at index [0] is available in the RootTestModule context.                 
Potential solutions:  
- If ExteriorContactRepository is a provider, is it part of the current RootTestModule?   
- If ExteriorContactRepository is exported from a separate @Module, is that module imported within RootTestModule?
@Module({imports: [ /* the Module containing ExteriorContactRepository */ ]})
```
- [x] Identification du problème : 
    - il faut ajouter les providers au RootTestModule, car Nest n'inclut pas automatiquement les services dans le test.
    - il faut s'assurer que l'argument ExteriorContactRepository at index [0] est accessible dans le contexte RootTestModule.  
    - [x] Passage de : 
```
beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
    providers: [ExteriorContactsService],
}).compile();
```
à :
 ```
beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
    providers: [ExteriorContactsService, 
        { provide: getRepositoryToken(ExteriorContact), useValue: jest.fn()}
    ],
    }).compile();

    service = module.get<ExteriorContactsService>(ExteriorContactsService);
});
```

Documentation : 
https://jestjs.io/docs/api

https://github.com/facebook/jest/issues/8279 (Création d'erreur custom)

https://jestjs.io/docs/tutorial-async#error-handling (gestion d'erreur)