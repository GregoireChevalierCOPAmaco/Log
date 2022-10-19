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

https://jestjs.io/docs/asynchronous (test de données async)

https://github.com/facebook/jest/issues/8279 (Création d'erreur custom)

https://jestjs.io/docs/tutorial-async#error-handling (gestion d'erreur)

https://jestjs.io/docs/expect#tomatchregexp--string (match string - regex)

https://jestjs.io/fr/docs/api#testfailingname-fn-timeout (tests sensés fail, à vocation de levée d'exception par exemple)

https://jestjs.io/docs/mongodb (test de la co à la db)

=======  
Méthodes  
  
it() et test() sont des alias et ont strictement le même fonctionnement. Ils existent les deux pour permettre plus de lattitudes dans l'écriture en anglais des tests. Par exemple : 
```
describe('yourModule', () => {
  test('if it does this thing', () => {});
  test('if it does the other thing', () => {});
});

&

describe('yourModule', () => {
  it('should do this thing', () => {});
  it('should do the other thing', () => {});
});
```

beforeEach(fn, timeout)  
Exécute la fonction avant chaque test, utile pour réinitialiser un état global. Dans le cas d'appel à une db, permet de retourner à l'état initial de la db avant d'y insérer/récupérer des données via test.  
  
describe(name, fn)  
Crée un bloc qui regroupe plusieurs tests liés. Imbriquer les describe() est possible pour créer une hiérarchie dans le fichier de test.  
  
describe.each(table)(name, fn, timeout)  
Utiliser describe.each permet de dupliquer les mêmes suites de tests avec des données différentes. describe.each permet d'écrire la suite de tests une fois et de lui passer les données.  
  
test.failing(name, fn, timeout)
test.fail est utile lors de l'écriture d'un test destiné à échouer. Ces tests se comporteront de la même manière que les tests normaux. Si le test failing lève n'importe quelle erreur, il passe. S'il n'en lève pas, il échouera.
C'est dans ces tests que l'on est apparemment sensé lever les exceptions
!! Disponible uniquement avec jest-circus !!
