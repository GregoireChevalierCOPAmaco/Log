**20 Novembre**
- [ ] Check des mails
- [ ] Mise à jour Jira
- [ ] MàJ du fichier de tests
- [x] Mise en marche du "magasin de tests" en réel
    - [ ] fix/KP-596_fix_connection_kmoBox-gatewayMac_on_storeCreate_via_interface et switch dessus
- [x] Créer une série de méthodes dans les fichiers concernés pour solutionner les problèmes d'overwrite à la création de la gateway
    - [x] Changer les routes en PATCH qui prend des arguments optionnels pour :
        - [x] createGateway
        - [x] createKmoBox
        ```
        import {
        BaseEntity,
        BeforeInsert,
        Column,
        Entity,
        JoinColumn,
        OneToMany,
        OneToOne,
        PrimaryColumn,
        } from 'typeorm';
        import { Store } from '../../stores/entities/store.entity';
        import { KmoBox } from '../../kmo-boxes/entities/kmo-box.entity';
        import { HistoryGateway } from '../../history-gateway/entities/history-gateway.entity';

        @Entity()
        export class Gateway extends BaseEntity {
        @PrimaryColumn()
        mac: string;

        @Column({ type: 'timestamp' })
        datetime: string;

        @Column({ type: 'float' })
        diskUsage: number;

        @Column({ type: 'float' })
        temperatureCpu: number;

        @Column({ type: 'float' })
        freeRam: number;

        @Column({ default: true })
        isBroken: boolean;

        @Column({ type: 'json', nullable: true })
        services: JSON;

        @OneToMany(() => KmoBox, (kmoBoxes) => kmoBoxes.gateway)
        kmoBoxes: KmoBox;

        @OneToMany(() => HistoryGateway, (historyGateway) => historyGateway.gateway)
        historyGateway: HistoryGateway[];

        @OneToOne(() => Store)
        @JoinColumn()
        store: Store;

        @BeforeInsert()
        async verifyStoreRelation() {
            const store = await Store.findOne({ where: { id: this.store.id } });
            if (store.gateway.mac != this.mac) {
            this.store.id = null;
            throw new Error('Mauvaise association');
            }
        }
        }
        ```
    - [x] Créer une route GET gateway/mac qui retourne toutes les infos de la GW
- [ ] Appliquer les changements sémantiques à la modale de caisse 
    - [ ] Changer durée on/durée off en motor on/motor off 
    - [ ] Changer temps en usage → Temps d'allumage/Temps caisse allumée/ouverte
    - [ ] Déduire le temps de passage moyen entre caisse ouverte divisé par nombre de clients(passages carte/espèce)


    tests fonctionnels : écrire des scénarios de tests ppour s'assurer que toutes les fonctionnalités de prod sont opérationnelles
    préparer une personne extérieure à utiliser l'app & ses fonctionnalités
    trouver le moyen de rendre utile les informations d'horaires de magasin et de les relier


**21 Novembre**
- [ ] Check des mails
- [ ] Mise à jour Jira
- [ ] MàJ du fichier de tests
- [x] Ajout de la route get gateway/mac au websocket
    - [x] Ajout dans le fichier gateways.gateway.ts : 
    ``` 
    @SubscribeMessage('getGatewayByMac')
    async getGatewayByMac(@MessageBody() mac: string) {
    const gateway = await this.gatewaysService.getGatewayByMac(mac);
    this.server.emit('gatewayDataByMac', {
        gateway,
    });
    }
    ```
    - [x] Lint, test, push & PR
    - [x] Reco à l'instance prod
    - [x] Arrêt des containers front & back, ```git pull origin develop``` & recréation du docker
    - [x] Vérification avec jacko, on reçoit null
    - [x] Changement, ajout de : gateway à la méthode : 
    ```
    @SubscribeMessage('getGatewayByMac')
    async getGatewayByMac(@MessageBody() mac: string) {
        const gateway = await this.gatewaysService.getGatewayByMac(mac);
        this.server.emit('gatewayDataByMac', {
        gateway: gateway,
        });
    }
    ```
    - [x] Reco à l'instance prod
    - [x] Arrêt des containers front & back, ```git pull origin develop``` & recréation du docker
- [x] Mise en marche du "magasin de tests" en réel
    - [ ] fix/KP-596_fix_connection_kmoBox-gatewayMac_on_storeCreate_via_interface et switch dessus 
- [ ] Appliquer les changements sémantiques à la modale de caisse 
    - [ ] Changer durée on/durée off en motor on/motor off 
    - [ ] Changer temps en usage → Temps d'allumage/Temps caisse allumée/ouverte
    - [ ] Déduire le temps de passage moyen entre caisse ouverte divisé par nombre de clients(passages carte/espèce)

passage gateway HS → fonctionnel : codé ou pas ? est ce que ça marche



**23 Novembre**
- [ ] Check des mails
- [ ] Mise à jour Jira
- [ ] MàJ du fichier de tests
- [x] Ajout de la route get gateway/mac au websocket
    - [x] TLDR du problème : le websocket attendait juste un objet string plutôt qu'un JSON contenant le string
    - [x] Résolution : 
        - [x] Création d'un DTO : 
        ```
        import { ApiProperty } from "@nestjs/swagger";

        export class GatewayMacDTO {
            @ApiProperty()
            mac: string
        }
        ```
        - [x] Modification du controller :
        ```
        @Get('/:mac')
        getGatewayByMac(@Param('mac') mac: string): Promise<Gateway | undefined> {
            const macDTO: GatewayMacDTO= { mac };
            return this.gatewaysService.getGatewayByMac(macDTO);
        }
        ``` 
        - [x] Modification du service : 
        ```
        async getGatewayByMac(mac: GatewayMacDTO): Promise<Gateway | undefined> {
            const gateway = await Gateway.createQueryBuilder('gateway')
            .where('gateway.mac = :mac', { mac: mac.mac })
            .leftJoinAndSelect('gateway.kmoBoxes', 'kmoBoxes')
            .getOne();

            return gateway;
        }
        ```
- [ ] Résolution du problème de uptime
    - [x] Réallumage de la GW et de la caisse, envois d'events pour voir
    - [ ] Les events motor viennent 10s en 10s up à la suite de up
    à résoudre, logique à refaire pour le motor up : 
    - à chaque fois qu'un event motor est émis, 
    - si le state = up
    - on ajoute la duration au motorUp
    à résoudre, logique à refaire pour le uptime :
    - à chaque fois qu'un event state = power_off 
    - si c'est le premier power off du jour
    - on prend la datetime
    - on fait la différence avec la datetime du premier event du jour de la caisse
    - et on l'ajoute au uptime
    - [ ] Résoudre le problème de uptime qui diminue avec le temps