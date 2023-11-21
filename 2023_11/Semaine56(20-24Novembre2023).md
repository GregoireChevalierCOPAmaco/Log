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
- [ ] Ajout de la route get gateway/mac au websocket
- [x] Mise en marche du "magasin de tests" en réel
    - [ ] fix/KP-596_fix_connection_kmoBox-gatewayMac_on_storeCreate_via_interface et switch dessus
- [ ] Appliquer les changements sémantiques à la modale de caisse 
    - [ ] Changer durée on/durée off en motor on/motor off 
    - [ ] Changer temps en usage → Temps d'allumage/Temps caisse allumée/ouverte
    - [ ] Déduire le temps de passage moyen entre caisse ouverte divisé par nombre de clients(passages carte/espèce)