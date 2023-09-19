**19 Septembre**
- [x] KP-467 Statut des magasins
    - [x] Afficher la liste des magasins avec au moins une maintenance urgente
        - [x] Afficher les caisses problématiques et leur statut
            - [x] Modifier le back pour retrieve un magasin et ses infos depuis une mac de gateway
                - [x] Ajout dans le storeService de : 
                ```
                async findStoreByGatewayMac(gatewayMac: string)/*: Promise<Store | null> */{
                    try {
                    return await this.storeRepository.findOne({ where: { gatewayMac } });
                    } catch (e) {
                    throw new HttpException(e, 500);
                    }
                }
                
                async findStoreByGatewayMac2(gatewayMac: string)/*: Promise<Store | null> */{
                    try {
                    return Store.findOne({ where: { gatewayMac } });
                    } catch (e) {
                    throw new HttpException(e, 500);
                    }
                }
                ```
                & dans le storeController de : 
                ```
                @Get('by-gateway-mac')
                findStoreByGatewayMac(@Query('gatewayMac') gatewayMac: string) {
                    return this.storesService.findStoreByGatewayMac(gatewayMac);
                }
                @Get('by-stores-gateway-mac')
                findStoreByGatewayMac2(@Query('gatewayMac') gatewayMac: string) {
                    return this.storesService.findStoreByGatewayMac(gatewayMac);
                }
                ```
                - [x] Rebuild dockers
    - [x] Afficher la liste des magasins avec au moins une maintenance préventive
        - [x] Afficher les caisses problématques et leur statut
penser pour la suite à changer gateway mac par serial number , faire les changements nécessaires dans la DB, le fichier SQL de mise en route, et l'API
- [ ] KP-473, push en prod des mises à jour du code