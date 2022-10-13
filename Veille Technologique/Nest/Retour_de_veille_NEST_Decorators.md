- Nest est construit autour d'une feature de langage appelée decorator. Les decorators sont un concept bien connu dans de nombreux langages de programmation populaires, bien que relativement nouveaux dans le monde de JS. 
- Dans le cas précis de JS, un ES2016decorator est une expression qui retourne une fonction et peut prendre une cible, un nom et une propriété en arguments. On l'applique en précédant le decorator d'un @ et en le déclarant en tout premier du fichier à décorer.
- Les decorators peuvent être définis pour une classe, une méthode ou une propriété.
- Nest fournit un ensemble de param decorators utiles qu'il est possible d'utiliser avec les gestionnaires de routes HTTP. Ci-dessous de decorators et les objets qu'ils représentent.
```
@Request(),                 @Req()	req
@Response(),                @Res()	res
@Next()	                    next
@Session()	                req.session
@Param(param?: string)	    req.params / req.params[param]
@Body(param?: string)	    req.body / req.body[param]
@Query(param?: string)	    req.query / req.query[param]
@Headers(param?: string)	req.headers / req.headers[param]
@Ip()	                    req.ip
@HostParam()	            req.hosts
```  
- De plus, il est possible de créer des decorators custom. C'est pratique car dans le monde de node.js, c'est une pratique répandue d'attacher des propriétés à l'objet requête.
- Afin de tendre vers un code lisible et transparent, il est conseillé de créer un decorator @User à part et de le réutiliser au travers des controllers. Un exemple ci-dessous ...
```
user.decorator.tsJS

import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const User = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    return request.user;
  },
);
```
- ... qui sera appelé simplement avec : 
```
@Get()
async findOne(@User() user: UserEntity) {
  console.log(user);
}
```
- Passer des données. Quand le comportement du decorator dépend de certaines conditions, il est possible d'utiliser le paramètre ```data``` afin de passer un argument à la création du decorator. Un cas fréquemment répandu :  extraire  les propriétés d'un objet requête selon sa ```key```. On peut ainsi passer de :
```
{
  "id": 101,
  "firstName": "Alan",
  "lastName": "Turing",
  "email": "alan@email.com",
  "roles": ["admin"]
}
```  
à  
```
@Get()
async findOne(@User('firstName') firstName: string) {
  console.log(`Hello ${firstName}`);
}
```  
si l'on définit un decorator comme suit : 
```
user.decorator.ts

import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const User = createParamDecorator(
  (data: string, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    const user = request.user;

    return data ? user?.[data] : user;
  },
);
```
