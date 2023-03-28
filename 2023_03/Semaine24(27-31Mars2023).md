**27 Mars**
- [ ] Poursuite keycloak
    - [x] Edit de la PR, squash & merge
    - [x] Affichage du bouton de link sidebar en fonction du rôle
        - [x] Refacto du code avec tous les rôles autorisés à voir le lien
            - [x] Voir (https://www.geekstrick.com/view-component-based-on-user-role/) pour le stack des roles assignés à la vision d'un component
            - [x] Utilisation de :
            ```
            this.keycloak.isUserInRole('kmo-predict_store-manager')
            ```
            pour ce faire ?
    - [x] Bloquer l'accès la page cop a ceux qui n'ont pas le rôle requis
        - [x] Récupération du rôle dans l’app front
        - [x] Reprise des routes de l'app-routing.component (fait par Théo)
        - [x] Modification de l’authguard (fait par Théo)
        - [x] Restriction de l’accès à la route en fonction du rôle 
        - [x] Appliquer la restriction à toutes les routes nécessaires
        - [x] Push & merge
    - [ ] KP-139 : Suppression d'un magasin
        - [x] Définition & assignation de la tâche sur Jira
        - [x] Création de branche kp139 et checkout
        - [ ] Renseignements sur l’interaction entre code API & keycloak
            - [x] Check de (https://www.keycloak.org/docs-api/15.0/rest-api/index.html) & (https://www.keycloak.org/docs-api/15.0/rest-api/index.html#_users_resource)
        - [ ] Renseignements sur la suppression de store via l’API
            - [x] Check de (https://www.keycloak.org/docs-api/15.0/rest-api/index.html) & (https://www.keycloak.org/docs-api/15.0/rest-api/index.html#_users_resource), chat gpt & (https://angular.io/guide/http)
            - [x] Réponse de GPT sur le sujet :
            ```
            Authenticate with Keycloak: To be able to access the Keycloak API, you need to authenticate with Keycloak first. You can do this by obtaining an access token from Keycloak using your client credentials or a user's credentials.

            Retrieve the user's Keycloak ID: In order to delete a user from Keycloak, you need to know the user's Keycloak ID. You can retrieve this ID by making a request to Keycloak's users API endpoint and filtering the results based on the user's email address or username.

            Delete the user from Keycloak: Once you have the user's Keycloak ID, you can use it to make a DELETE request to the Keycloak users/{id} API endpoint to delete the user.

            Delete the user's data from PostgreSQL: Finally, you can delete the user's data from your PostgreSQL database using an SQL query. You can use the user's email address or any other unique identifier to filter the data and ensure that you are deleting the correct user's data.

            && 

            import { HttpClient } from '@angular/common/http';
            import { Injectable } from '@angular/core';
            import { environment } from '../environments/environment';

            @Injectable()
            export class UserService {

            private keycloakUrl = `${environment.keycloakUrl}/auth`;
            private realm = 'myrealm';
            private clientId = 'myclient';
            private clientSecret = 'mysecret';

            constructor(private http: HttpClient) {}

            async deleteUser(email: string) {
                const token = await this.getAccessToken();
                const user = await this.getUserByEmail(email, token);

                if (user) {
                await this.deleteUserInKeycloak(user.id, token);
                await this.deleteUserInDatabase(email);
                }
            }

            private async getAccessToken(): Promise<string> {
                const tokenUrl = `${this.keycloakUrl}/realms/${this.realm}/protocol/openid-connect/token`;
                const body = new URLSearchParams({
                'grant_type': 'client_credentials',
                'client_id': this.clientId,
                'client_secret': this.clientSecret,
                }).toString();

                const response = await this.http.post(tokenUrl, body, {
                headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
                }).toPromise();

                return response['access_token'];
            }

            private async getUserByEmail(email: string, token: string): Promise<any> {
                const usersUrl = `${this.keycloakUrl}/admin/realms/${this.realm}/users?email=${email}`;
                const headers = { 'Authorization': `Bearer ${token}` };

                const response = await this.http.get(usersUrl, { headers }).toPromise();
                const users = response as Array<any>;
                return users.find(u => u.email === email);
            }

            private async deleteUserInKeycloak(userId: string, token: string) {
                const userUrl = `${this.keycloakUrl}/admin/realms/${this.realm}/users/${userId}`;
                const headers = { 'Authorization': `Bearer ${token}` };

                await this.http.delete(userUrl, { headers }).toPromise();
            }
            private async deleteUserInDatabase(email: string) {
                    // Use HttpClient to make a DELETE request to your API endpoint that deletes the user from the database.
                    const apiUrl = `${environment.apiUrl}/users?email=${email}`;
                    await this.http.delete(apiUrl).toPromise();
                }
            }
            ```
        - [x] Création d’un user keycloak pour le test : jordi recteur
        - [x] Assignation d'un attribut store au user créé : attribution du store : 7d1a9808-ccf1-4136-8c8b-b80020ac0ec4
        - [x] Création d’une page dédiée à la suppression
            - [x] dans le dossier apps/front : ```ng generate component administration```
            - [x] Déplacement dans le dossier ./src/app/page
            - [x] Update de l'import dans le ficiher app.component
            - [x] Création du bouton de lien affiché conditionnellement si rôle = cop_developer
                - [x] Ajout du code suivant dans l'app.component.ts :
                 ```
                if (this.keycloak.isUserInRole('cop_developer')) {
                    this.isAdminUser = true;
                } else {
                    this.isAdminUser = false;
                }
                ```
                - [x] Ajout du code suivant dans l'app.component.html :
                 ```
                <div *ngIf="isAdminUser">
                    <a link mat-list-item routerLink="/administration" class="flex">
                    <mat-icon class="text-white align-middle">bar_chart</mat-icon>
                    <span class="link-label text-white">Administration</span>
                    </a>
                </div>
                ```
                - [x] Ajout du code suivant dans l'app-routing.component.ts :
                ```
                {
                    path: 'administration',
                    canActivate: [AuthGuard],
                    data: {
                    roles: [
                        'cop_super-user',
                        'cop_developer',
                        'kmo-predict_admin',
                        'kmo-predict_regional-direction'
                    ]
                    },
                    component: AdministrationComponent
                }
                ```
        - [ ] Récupération de l'id d'un store
            - [ ] Check de (https://wjw465150.gitbooks.io/keycloak-documentation/content/server_admin/topics/clients/protocol-mappers.html)
        - [ ] Écriture des tests
        - [ ] Retirer l'attribut store au user corespondant au store
        - [ ] Utiliser la route d'API DELETE users:{id}
        - [ ] Restriction de l’accès à la page aux users ayant le rôle pour
        - [ ] Renseignements sur la suppression d’un attribut store à un user via API
        - [ ] Associer la suppression d’un attribut store & la suppression du store en db
- [x] Modification suivi tablette n° 352807082442783 : Localisation DRUDY & SUHR pour installations Grosbliederstroff & Sarralbe


**28 Mars**
- [ ] Poursuite keycloak
    - [ ] KP-139 : Suppression d'un magasin
        - [ ] Renseignements sur la suppression de store via l’API
            - [x] Check de (https://www.keycloak.org/docs-api/15.0/rest-api/index.html) & (https://www.keycloak.org/docs-api/15.0/rest-api/index.html#_users_resource), chat gpt & (https://angular.io/guide/http)
        - [ ] Récupération de l'id d'un store via l'API (front)
            - [x] Check de (https://wjw465150.gitbooks.io/keycloak-documentation/content/server_admin/topics/clients/protocol-mappers.html) & (https://ravthiru.medium.com/keycloak-retrieve-custom-attributes-in-access-token-1a2d5aef0caa)
            - [x] Création d’un mapper store-id-mapper
            - [x] Utilisation de Postman & Keycloak API pour get les users du realm
                - [x] Renseigner l'url : http://localhost:8080/admin/realms/Cop_sass/users
                - [x] Envoyer la requête GET : retour ```"error": "HTTP 401 Unauthorized"```
                - [x] Trouver comment autoriser la connexion
                    - [x] Check de (https://documenter.getpostman.com/view/7294517/SzmfZHnd) & (https://www.youtube.com/watch?v=jjHBJNlBLBU)
                    - [x] Assignation des roles nécessaires à l'user admin 
                        - [x] Users -> admin -> Role mappings -> dropdown "Client Roles" -> realm-management -> Assignation de "realm-admin"
                        - [x] envoi d'une POST à http://localhost:8080/realms/Cop_sass/protocol/openid-connect/token avec les infos de Body/x-www-form-urlencoded renseigné comme suit :
                            - username : admin
                            - password : pw123
                            - grant_type : password
                            - client_ID : KMO_Predict
                - [x]  Renseigner le bearer token dans la requete http://localhost:8080/admin/realms/Cop_sass/users
                - [x]  Envoyer la requête GET : retour probant : 
                ```
                ...
                {
                "id": "bfe25071-688e-45b4-bd72-40391f62f9ce",
                "createdTimestamp": 1679580118277,
                "username": "g.chevalier",
                "enabled": true,
                "totp": false,
                "emailVerified": true,
                "firstName": "Grégoire",
                "lastName": "Chevalier",
                "email": "g.chevalier@cop-amaco.com",
                "attributes": {
                    "storeId": [
                        "9de16383-c4d8-4616-bc4c-a26795f286ae"
                    ]
                },
                ```
            - [ ] Utilisation de Postman & Keycloak API pour get les attributs d'un user
                - [x] Check de (https://docs.nestjs.com/techniques/http-module) & (https://github.com/keycloak/keycloak/tree/main/js/libs/keycloak-admin-client)
            - [ ] Création d’un userService
            - [ ] Création d’une userInterface
        - [ ] Récupération de l'attribut storeID d'un user via l'API
            - [x] Check de (https://docs.nestjs.com/techniques/http-module) & (https://github.com/keycloak/keycloak/tree/main/js/libs/keycloak-admin-client)
        - [ ] Afficher tous les users par ligne et proposer un bouton suppression au bout de la ligne
        - [ ] Filtrer les users par un champ de texte
        - [ ] Lier le bouton suppression à la suppression dans le back
            - [ ] Écriture des tests
            - [ ] Retirer l'attribut store au user corespondant au store
            - [ ] Utiliser la route d'API DELETE users:{id} : NON, ajouter un boolean dans le DTO du store en base et cesser de l'afficher si désactivé
            - [ ] Cacher l'affichage de chaque magasin en cas de boolean désactivé
        - [ ] Restriction de l’accès à la page aux users ayant le rôle (pas nécessaire, la deletion (désactivation) ne sera accesible qu'aux devs cop)
        - [ ] Renseignements sur la suppression d’un attribut store à un user via API
        - [ ] Associer la suppression d’un attribut store & la suppression du store en db
- [x] Export du realm pour théo avec users ```docker cp kmo-keycloak:/tmp/realm-export_28_03_2023.json C:\Users\gchevalier\Desktop\realm_exports```
- [x] Réunion quotidienne : 
    - Changement/Remplacement des gateways/boitiersKMO : créer une interface visuelle pour faire le lien entre les nouvelles données et le code existant
    - Créer un tracking de l'historique des changements de boitier/gateway (coté COP) pour référence et vision d'ensemble
    - voir amazon glacier pour l'archivage des anciennes versions de base
    - prévoir dans les tests e2e les cas d'unauthorized pour les accès aux pages non autorisées.
    - se renseigner sur l'impersonate pour voir ce que voit tel user