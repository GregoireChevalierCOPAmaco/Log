- TypeORM est l'ORM le plus 'mature' de l'environnement TypeScript. Il est donc normal que ce soit celui de Nest.
- Nest est *database agnostic* : ``What is the meaning of data agnostic?
First, the technical: In information technology, “agnostic” refers to the ability of one system to work with many systems, rather than being designed for a single system. A system that is “data agnostic” can work with information received from heterogeneous databases, i.e. databases with dissimilar data formats.``
Nest est database agnostic, ce qui permet d'interagir facilement avec des bases de données SQL ou NoSQL. En général, connecter Nest à une base de données se résume à charger le bon pilote de bdd.
- Nest offre la possibilité de travailler avec des librairies d'intégration d'ORM fournies par node telles que MikroORM, Sequelize, Knex, TypeORM et Prisma pour opérer à un plus haut niveau d'abstraction.
- TypeORM Integration : Nest fournit le package ``@nestjs/typeorm``. On indique son utilisation avec l'installation via la commande : 
```
npm install --save @nestjs/typeorm typeorm mysql2
```
 Une fois l'installation complétée, il faut **importer le *TypeOrmModule* dans le document racine *appModule*.** : 
```
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: 'root',
      database: 'test',
      entities: [],
      synchronize: true,
    }),
  ],
})
```
export class AppModule {}
- TypeORM supporte le repository design pattern, ainsi, chaque entity a ison propre repository