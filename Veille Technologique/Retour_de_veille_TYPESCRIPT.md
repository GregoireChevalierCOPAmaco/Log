- TypeScript a une relation inhabituelle avec JavaScript. TypeScript offre toutes les fonctionnalités de JavaScript, avec une couche supplémentaire de fonctionnalités : le système de typage.
- TypeScript connaît le JavaScript et générera les types pour vous la plupart du temps. Par exemple, en créant une variable et en lui assignant une certaine valeur, TypeScript va utiliser cette valeur en tant que type.
``` 
let helloWorld = "Hello World";
        
let helloWorld: string
```  
- En comprenant comment JavaScript fonctionne, TypeScript peut créer un système qui accepte du code JavaScript, avec des types. Le résultat est un système de types qui n’a pas besoin de déclarations explicites de types dans le code. C’est comme ça que TypeScript sait que helloWorld est un string dans l’exemple précédent (TypeScript a inféré le type string de helloWorld).
- Il est possible de définir des types à la volée ...
```
const user = {
  name: "Hayes",
  id: 0,
};
```  
- ...  ou de les expliciter en objets définis : 
``` 
interface User {
  name: string;
  id: number;
}
  
const user: User = {
  name: "Hayes",
  id: 0,
};
```  
- TypeScript va prévenir s'il est fourni un objet qui ne correspond pas à l'interface.
- TypeScript supporte les classes et la programmation orientée objet. On peut utiliser une déclaration d’interface avec une classe :
```
interface User {
  name: string;
  id: number;
}
 
class UserAccount {
  name: string;
  id: number;
 
  constructor(name: string, id: number) {
    this.name = name;
    this.id = id;
  }
}
 
const user: User = new UserAccount("Murphy", 1);
```  
- Il est  utiliser les interfaces pour annoter les types de paramètres et valeurs de retour de fonctions :
```
function getAdminUser(): User {
  //...
}
 
function deleteUser(user: User) {
  // ...
}
```  
- JavaScript fournit déjà un petit ensemble de types primitifs, dont on peut se servir dans une interface : boolean, bigint, null, number, string, symbol, et undefined. TypeScript étend cette liste en y ajoutant any (tout permettre), unknown (s’assurer que quiconque se sert de ce type déclare le type voulu), never (il est impossible d’avoir ce type), et void (une fonction qui retourne undefined ou ne retourne rien). On peut voir deux syntaxes pour créer des types : les Interfaces et les Types. Préférer une interface, mais utiliser un type s'il y a besoin d’une fonctionnalité particulière.
- Type complexe : union ; une union permet de déclarer qu’un type pourrait en être un parmi certains. Par exemple :
```
type MyBool = true | false;
type WindowStates = "open" | "closed" | "minimized";
type LockStates = "locked" | "unlocked";
type PositiveOddNumbersUnderTen = 1 | 3 | 5 | 7 | 9;
```  
- Types Génériques ;ils fournissent des variables aux types. Les tableaux (arrays) seraient un exemple commun. Un tableau sans type générique pourrait tout contenir, alors qu’un tableau avec un type générique restreint son contenu à ce type générique.
```
type StringArray = Array<string>;
type NumberArray = Array<number>;
type ObjectWithNameArray = Array<{ name: string }>;
```
- TypeScript est aussi un langage qui préserve le comportement à l’exécution de JavaScript. Par exemple, la division par 0 produit Infinity au lieu de lancer une erreur. TypeScript, par principe, ne change jamais le comportement de code JS. Cela veut dire que si vous déplacez du code de JavaScript à TypeScript, il est garanti de s’exécuter de la même façon, même si TS pense qu’il comporte des erreurs liées aux types.
- Effacement de types : Grossièrement, une fois que le compilateur de TypeScript a fini de vérifier le code, il efface les types pour laisser le code résultant. Cela signifie qu’à la fin du processus de compilation, le code JS ne conserve aucune information de types. Cela signifie aussi que TypeScript, en se basant sur les types présents dans le code, n’altère jamais le comportement du programme.
- Template string : morceau de string complété par des variable et défini par des backticks ` :
```
`Hello ${person}, today is ${date.toDateString()}!`
```