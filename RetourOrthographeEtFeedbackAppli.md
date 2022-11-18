**Retour de typos & feedbacks dans l'application**
  
L'élément button [>>] qui ouvre et ferme la sidebar est décollé de la sidebar quand la page n'est pas responsive

- (KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\dashboard-ass.component.html)  
Affichage de la page pas responsive  
Terminée s'écrit Terminé  
<h2>A Traiter</h2> s'écrit -> À traiter
  
- KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\new-ticket\new-ticket.component.ts  
**Pas de retour utilisateur quand il manque un champ / quand un champ est incorrect dans les formulaires de création de Ticket**. Le toast existe mais n'est pas appelé. Pas d'err thrown ?  
Le toast "Le ticket n'a pas pu etre créé", etre s'écrit -> être  
Le toast 'à ete crée', s'écrit -> à été créé'  
Le toast historyService .create(token.lastname, 'Crée', res.id), 'Crée' s'écrit -> 'Créé'.  
À la création du ticket, en cas de mauvais magasin, le toast "Le magasin choisie n'existe pas", il n'y a pas de e à choisie -> choisi  

- KMO_WEB\COP-SAV\src\app\pages\stock-vehicle\add-piece\add-piece.component.ts  
Le toast 'Une pièce a été ajouter' s'écrit -> 'Une pièce a été ajoutée'  
Le toast 'La pièce n'a pas pue être ajouter' s'écrit -> 'La pièce n'a pas pu être ajoutée'  
  
- KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\list-ticket\edit-ticket\edit-ticket.component.ts  
Le toast 'Les tickets on été mis à jours ', pas de s à jours  
  
- KMO_WEB\kmo-back\src\tickets\tickets.service.ts
'<p>Un nouveaux ticket vient de vous être assigné </strong></p>', pas de x à nouveau  
  
- KMO_WEB\COP-SAV\src\app\pages\authentification\setnew-password\setnew-password.component.html  
placeholder="Nouveaux mot de passe" />, pas de x à Nouveau  
  
- KMO_WEB\COP-SAV\src\app\pages\cop\regional-management\regionals-management\regionals-management.component.ts  
'Direction Régional ajouté', s'écrit -> ajoutée  
  
- KMO_WEB\COP-SAV\src\app\pages\cop\store\store.component.ts  
Le toast 'Le magasin été crée', crée s'écrit -> créé  
Le toast 'Le magasin n'a pas été crée', crée s'écrit -> créé  
Le toast 'Le magasin à bien été supprimé', à s'écrit -> a  
  
- KMO_WEB\COP-SAV\src\app\pages\cop\store\edit-store\edit-store.component.ts  
Le toast 'Le Magasin ' +
						this.updateStoreForm.value.idStore +
						' à ete mis à jour', à ete s'écrit -> a été  
Le toast "Le magasin n'a pas pu etre modifié", etre s'écrit -> être  
  
- KMO_WEB\COP-SAV\src\app\pages\cop\user\user.component.ts
Le toast 'utlisateur ajouté' -> ajout de majuscule au U  
Le toast '"L'utilisateur à bien été supprimé' à -> a  
  
- KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\dashboard-ass.component.ts  
Le toast 'Déplacer dans Terminer', Terminer s'écrit -> Terminé   
Le toast 'Le status du ticket à été mis à jour ', status à s'écrit -> statut a  
Le toast "Le status du  ticket n' à pas été mis à jour", retrait des espaces en trop et correction de status en statut, à en a   
  
- KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\list-store\reports\report-cop-view\report-cop-view.component.ts  +  KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\list-store\reports\report-cop-view\aldi-report\aldi-report.component.ts  + KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\list-store\reports\report-cop-view\lidl-report\lidl-report.component.ts  
Le toast "La facturation n'a pas pu etre validé", validé s'écrit validée  
  
- KMO_WEB\COP-SAV\src\app\pages\dashboard-ass\ticket-detail-dialog\ticket-detail-dialog.component.ts  
Les toasts de type "Mis à jour l'ims" ont été corrigés en : Mise à jour de  
Le toast "Le ticket n' a pas été modifié", suppression de l'espace en trop  
Le toast 'Le ticket à bien été supprimé', correction de à en a  
Le toast "Le ticket n'a pas pu être supprimer", correction de supprimer en supprimé  
Le toast 'Le status du ticket à été mis à jour ', correction de status à en statut a  
Le toast "Le status du  ticket n' à pas été mis à jour", correction de status à en statut a & suppression des espaces en trop  
   
- KMO_WEB\COP-SAV\src\app\pages\stock-vehicle\add-vehicle\add-vehicle.component.ts  
Le toast "Succes", correction en Succès
Le toast '"Le véhicule n'a pas pue être ajouter "', correction de pue ajouter en pu ajouté & suppression de l'espace en trop  
  
- KMO_WEB\kmo-app\src\app\guards\TechnicianGuard\technician.guard.ts  
Correction des lignes 26 & 27  
  
- KMO_WEB\kmo-app\src\app\pages\reports\aldi-new-report\aldi-new-report.page.ts & KMO_WEB\kmo-app\src\app\pages\reports\lidl-new-report\lidl-new-report.page.ts  
Correction du message d'alerte "Avant de créer le rapport, verifier que l\'IMS fournit soit identique à la votre. <br> S\'ils ne sont pas identiques préciser le dans la case observation"  
  
- KMO_WEB\kmo-app\src\app\pages\settings\settings.page.ts  
Le toast "Vous utilisé maintenant le véhicule", correction d'utilsé en utilisez  
  
- KMO_WEB\kmo-app\src\app\services\LoginService\login.service.ts  
Correction du toast "Connexion réussite" en réussie
  
