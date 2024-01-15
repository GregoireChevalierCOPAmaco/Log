**15 janvier**
- [x] Passage sur KP-688 mettre un code couleur aux stores désactivés de /cop
    - [x] Ajout dans le html de cop store details de :
    ```        
    <tr *matRowDef="let row; columns: displayedColumns;" mat-row [ngClass]="{'inactive-store-row': !row.isStoreActive}"></tr>
    ```
    dans la < table > :
    ```
    ...

        <ng-container matColumnDef="gatewaysState">
          <th *matHeaderCellDef mat-header-cell mat-sort-header>Statut de gateway</th>
          <td *matCellDef="let element" mat-cell routerLink="/cop/{{element.id}}">
            <span class="mr-1 inline-block">{{element.gateway.mac}}</span>
            <span class="align-middle">
              <mat-icon *ngIf="isGatewayDisconnected(element?.gateway.mac)" class="text-red-600">wifi_off</mat-icon>
              <mat-icon *ngIf="!isGatewayDisconnected(element?.gateway.mac)"
                        class="text-green-600">wifi_statusbar_4_bar</mat-icon>
            </span>
          </td>
        </ng-container>


        <tr *matHeaderRowDef="displayedColumns" mat-header-row></tr>
        <tr *matRowDef="let row; columns: displayedColumns;" mat-row [ngClass]="{'inactive-store-row': !row.isStoreActive}"></tr>
    </table>
    ```
- [x] Commit, push & PR
- [ ] Merge
