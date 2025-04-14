**07 Avril**
- [ ] ESL
    - [ ] ESL- 273 Dissocier l'eslId du storeArticle associé en même temps que la dissociation storeArticleID de l'ESL
        - [ ] Créer la fonction qui set NULL au champ storeArticle.esl
            ```
            async dissociateEsl(id: string){
                try {
                const article = await this.findOneByBarcode(id);
                if (!article) {
                    throw new HttpException('Article not found', 404);
                }
                article.esl = null;
                await this.storeArticleRepository.save(article);

                }catch (error) {
                console.error(error);
                throw new HttpException(error.message || 'Failed to dissociate ESL', 500);
                }
            }
            ```
        - [ ] Appeler la fonction quand on call dissociate(esl) 
            - [x] Création d'une balise html dans le component management : 
            ```
            <div class="associate-container">
            <h2>Dissocier l'ESL de son article </h2>
            <form (submit)="onDissociateSubmit($event)">
                <div>
                <label for="idEsl">ESL ID:</label>
                <input
                    type="text"
                    id="idEsl"
                    [(ngModel)]="idEsl"
                    name="idEsl"
                    readonly
                />
                </div>

                <button type="submit" [disabled]="!idEsl">Dissocier</button>
            </form>
            </div>
            ```
            - [x] Création d'une méthode dans le storearticle service front :
            ```
            dissociateEsl(eslId: string): Observable<void> {
                return this.httpClient.patch<void>(`${this.apiUrl}/dissociate-esl/${eslId}`, {});
            }
            ```
            - [x] Création d'une methode dans le controller storeArticle
            ```
            @Patch('dissociate-esl/:id')
            dissociateEsl(@Param('id') id: string) {
                return this.storeArticlesService.dissociateEsl(id);
            }
            ```
            - [x] Méthode du service : 
            ```
            async dissociateEsl(id: string){
                try {
                const article = await this.findOneByBarcode(id);
                if (!article) {
                    throw new HttpException('Article not found', 404);
                }
                article.esl = null;
                await this.storeArticleRepository.save(article);

                }catch (error) {
                console.error(error);
                throw new HttpException(error.message || 'Failed to dissociate ESL', 500);
                }
            }
            ```
        - [x] création de la fonction dans le component front : 
        ```
         onDissociateSubmit(event: Event): void {
            event.preventDefault();
            let article = this.storeArticleService.getArticleByEslId(this.idEsl);
            if (article != null) {
            this.eslService.dissociateEslWithArticle(this.idEsl);
            this.storeArticleService.dissociateEsl(this.idEsl);
            } else {
            alert('This ESL has no storeArticle associated');
            }
        }
        ```
        - [x] Problème : quand le storeArticle est supprimé, on ne peut plus retrouver l'esl pour la dissocier et à l'inverse, commencer par l'esl empeche de retrouver le store article. Mistral propose d'encapsuler les méthdes su service dans une **transaction**
        ```
        Le principe de transaction dans le contexte du code TypeScript fait généralement référence à la gestion des opérations atomiques, souvent dans le cadre de bases de données ou de systèmes de gestion de données. Une transaction est une séquence d'opérations exécutées comme une seule unité de travail. Les transactions doivent respecter les propriétés ACID (Atomicité, Cohérence, Isolation, Durabilité) pour garantir la fiabilité et la cohérence des données.

        Voici une explication détaillée de ces propriétés et comment elles peuvent être implémentées en TypeScript :
  
        Atomicité : Une transaction doit être atomique, ce qui signifie que toutes les opérations dans la transaction doivent être exécutées avec succès. Si une opération échoue, toutes les opérations doivent être annulées (rollback).

        Cohérence : Une transaction doit amener la base de données d'un état cohérent à un autre état cohérent. Les règles de cohérence doivent être respectées avant et après la transaction.

        Isolation : Les transactions doivent être isolées les unes des autres. Les modifications effectuées par une transaction ne doivent pas être visibles par d'autres transactions jusqu'à ce que la transaction soit validée (commit).

        Durabilité : Une fois qu'une transaction est validée, ses modifications doivent être permanentes, même en cas de panne du système.
        ```
        - [x] Update de dissociateEsl du StoreArticlesService :
        ```
        async dissociateEsl(idEsl: string): Promise<void> {
            const queryRunner = this.storeArticleRepository.manager.connection.createQueryRunner();

            await queryRunner.startTransaction();
            try {
            const article = await this.storeArticleRepository.findOne({ where: { esl: { id: idEsl } }, relations: ['esl'] });
            if (!article) {
                throw new HttpException('Article not found', 404);
            }

            article.esl = null;
            await this.storeArticleRepository.save(article);

            await this.eslService.dissociateEsl(idEsl);

            await queryRunner.commitTransaction();
            } catch (error) {
            await queryRunner.rollbackTransaction();
            throw new HttpException('Failed to dissociate ESL', 500);
            } finally {
            await queryRunner.release();
            }
        }
        ```
        - [x] Ajout de l'EslModule au storeArticle module : 
        ```
        import {forwardRef, Module} from '@nestjs/common';
        import { StoreArticlesService } from './store-articles.service';
        import { StoreArticlesController } from './store-articles.controller';
        import {TypeOrmModule} from '@nestjs/typeorm';
        import {StoreArticle} from './entities/store-article.entity';
        import {Esl} from '../esl/entities/esl.entity';
        import {HttpModule} from '@nestjs/axios';
        import {EslTag} from '../esl-tag/entities/esl-tag.entity';
        import { TemplateModule } from '../Templates/template.module';
        import { StyleModule } from '../style/style.module';
        import { RequestLogModule } from '../request-log/request-log.module';
        import { RequestLog } from 'src/request-log/entities/request-log.entity';
        import { EslModule } from 'src/esl/esl.module';

        @Module({
        imports: [
            HttpModule,
            TypeOrmModule.forFeature([StoreArticle,Esl,EslTag,RequestLog]),
            TemplateModule,
            forwardRef(() => RequestLogModule), // Ajoutez forwardRef ici
            forwardRef(() => StyleModule), // Remplacez StyleService par StyleModule
            forwardRef(() => EslModule)
        ],
        controllers: [StoreArticlesController],
        providers: [StoreArticlesService], // Fournir uniquement les services nécessaires
        exports: [StoreArticlesService], // Exportez le service si d'autres modules en ont besoin
        })
        export class StoreArticlesModule {}
        ```
- [x] Retour salon aws
    - [x] 10h30, stand Temporal (https://temporal.io/)
    Temporal est une solution logicielle d'automatisation de workflows  fiables et évolutifs open source. Il gère les états des workflows, assure la reprise après incident et facilite la coordination des microservices, tout en offrant une visibilité et une traçabilité accrues des processus métiers. Suivi étape par étape du flow, gestion de cas echec, et alerte en cas d'erreur
    L'entreprise a 5 ans, la techno 10. Utilisé par netflix, snapchat et plein d'autres gros entre autres.
    Pricing, 50$ pour 1 M d'appels à l'API
    Interlocuteur : Maël Ropars
    Takima, société de formation en français sur la techno
    - [x] 11h15, amphi Bordeaux, session SaaS
    Bâtir une culture SaaS adaptée au cloud
    Intéressant pour présenter à Pascal et lui communiquer les arguments à avancer face à un client
    Intervenants, Eloi Ancelin (Architecte), Aymeric Thas Pinot (CTO Orisha)
        - Pourquoi un SaaS :
        Simplification de la gestion IT
        Affranchissement du hardware
        Limitation des coûts & des risques de pertes de données
        Facilitation de l'onboarding
        Pricing selon l'usage, petit commerçant = petite facture
        Amélioration et support continus compris dans le prix de l'abonnement
        Développement de fonctionnalités sur demande du client en fonction du besoin
        Agilité niveau charge avec scale up ou down en fonction de la charge
        Permet l'automatisation des upgrades (avec notification mensuelle des changements apportés)
        Réduction du temps d'intervention & efficience opérationnelle
        - Comment SaaSifier son entreprise :
        Adapter l'approche à la stratégie
        Gérer le pricing en fonction du client
        Importance de la communication avec le client pour avoir ses besoins
        Importance de s'adapter à la situation du client
        Importance d'informer les équipes de vente sur le produit à vendre
        Voir avec le client le rapport délai/coût du rappatriement de données en cas de crash, selon besoin
        Proposer un essai gratuit
        Dire au client de nous déléguer le technique
        - Retour d'expérience de passage à une solution saas d'Orisha
        Orisha, éditeur d'ERP dans l'immobilier, la banque, le retail ...
        50k clients, 1900 collaborateurs, 60 produits
        Services aws utilisés : Outbound (mails), RDS SQL (serveur), APPSTREAM
        Méthode utilisée :
            - dupliquer l'application
            - streamer le duplicata d'application saas
            - migrer les clients
            - développer en continu
            - fermer progressivement les instances on premise
    - [x] 12h15 sur un coin de table, avec un utilisateur d'aws dans notre genre de situation aussi
        - Dans le dialogue avec les sales d'aws, demander et gratter des crédits gratuits et des free trials
        - Dire qu'on utilise/envisage le même servie d'un concurrent pour obtenir des rabais
    - [x] 12h50h Cabine essayage connecté
        - Fabricant du hardware : PROTO
        Service arrivant prochainement
        Photo prise, envoyée au cloud, puis renvoyée à la cabine. 
        Ecran tactile
    - [x] 13h15 Stand neo4J
    Bases de données Graph & analyse de données
    BDD Graph != des bases relationnelles, modélise les données sous forme de nœuds (entités) et de relations (arêtes) entre ces nœuds. Permet de représenter et de naviguer facilement dans des réseaux complexes de données interconnectées, comme les réseaux sociaux, les systèmes de recommandation ou les bases de connaissances. Requêtes plus intuitives
    et plus performantes.
    - [x] 14h Schéma pour ask an architect
    - [x] 14h30 Circle CI
    Intégration continue (https://circleci.com/customers/)
    - [x] 14h45 Ask an architect. 

    Cognito serait l'outil approprié pour quasi toutes nos demandes 
    Gère les user pools et les fédérations. Donc ça coche la case utilisateur avec droits/permission. 
    Marche à suivre : 
        - Dédier un compte AWS à l'usage cognito (pour non extraction de passwords)
        - Rendre nos API compatibles OpenID
        - Lier App & API à Cognito
    Workshop à suivre : (https://aws-experience.com/emea/uki/e/b782e/demystifying-saas-application-authentication-on-aws-workshop)
    Aller voir le service Quicksight (dashboard)
    - [x] 15h10 Stand AutomateIT
    FinOPS (financial operations)
    Entreprise de gestion des coûts de services AWS
    Auxilliaire de réduction des coûts à qui on règle la facture plutôt qu'à Amazon. Le principe c'est qu'ils font du minmax des nos instances/services pour les ajuster au maximum à nos besoins tout en payant le moins possible.
    - [x] 15h45 Salle 252, Session Design patterns
    voir (https://pascal-martin.fr/)
    Notion de blast radius de fonctionnalités : combien d'autres fonctionnalités ça impacte si la fonctionnalité casse
    Limiter le blast radius en appliquant le principe S de Solid
    Creuser les concept de composants dégradés & dégradation gracieuse
- [ ] ESL
    - [ ] ESL- 275 Fix la modification des formes nouvellement ajoutées au template lors de l'édition de template
        - [x] Les poignées de redimensionnement sont appelées "controls" en anglais. Il faut d'abord s'assurer que les objets ont les propriétés hasControls et hasBorders définies sur true. Ensuite, vérifier  que les objets ne sont pas verrouillés pour le redimensionnement :
        Les propriétés lockScalingX et lockScalingY doivent être définies sur false pour permettre le redimensionnement
         que les objets ne sont pas verrouillés pour le redimensionnement :
        Les propriétés lockScalingX et lockScalingY doivent être définies sur false pour permettre le redimensionnement
        - [x] Retrait du ```this.sendToBack(square);``` qui causait le probleme
    - [ ] ESL-287 Ajouter un bouton aperçu pour pop-up une prévisualisation de l'ESL dans la ESL table
        - [x] modif de l'HTML : 
        ```
        ...
        <th scope="col" class="px-4 py-3.5 text-sm font-normal text-left text-gray-200">
            prévisualisation
        </th>
        ...
        <td class="px-4 py-4 text-sm text-gray-500 whitespace-nowrap">
            <div class="preview-box" (mouseenter)="showPreview(request.id_esl)" (mouseleave)="hidePreview()">
                <canvas #previewCanvas id="previewCanvas-{{request.id_esl}}" width="200" height="100"></canvas>
            </div>
        </td>
        ```
        - [x] modif du component
        ```
        import {MatSnackBar} from '@angular/material/snack-bar';
        import { TemplateService } from '../../../services/template-service';
        ...
        private templateService: TemplateService
        ...



        refaire la fonction du service template back : 
        async getTemplateByEslId(eslId: string): Promise<Template> {
    return this.findByEslId(eslId);
  }
  pour récupérer l'esl, son style, puis trier les templates sur la base du style et des dimensions