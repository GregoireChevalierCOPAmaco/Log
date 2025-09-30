**29 Septembre**
- [ ] ESL   
    - [x] Réinstallation du setup   
        - Aménagement espace bureau
        - Mise en réseau routeur-base-pc ESLs
        - Redémarrage pc ESLs
        - clean des dockers
        - ```git pull```sur pc ESLs
    - [ ] ESL-194 Définir la politique de sauvegarde des données et son automatisation
        - [x] Passage sur branche dédiée
        ```
        git checkout develop
        git pull
        git checkout -b feat/ESL194+-savePolicy_deployment
        ```
        - [x] Plan de route 
        ```
        Étape 1 : Préparer les volumes de sauvegarde

        Crée un dossier local sur ton hôte pour centraliser les backups :

        mkdir C:\docker_backups

        Étape 2 : Script de sauvegarde (PowerShell)

        Voici un script backup.ps1 que tu peux placer dans C:\docker_backups :

        # backup.ps1
        # Répertoire où seront stockées les sauvegardes
        $backupDir = "C:\docker_backups"
        $timestamp = Get-Date -Format "yyyyMMdd_HHmmss"

        # Créer le dossier s'il n'existe pas
        if (!(Test-Path -Path $backupDir)) {
            New-Item -ItemType Directory -Path $backupDir | Out-Null
        }

        # Sauvegarde PostgreSQL
        Write-Output "==> Sauvegarde PostgreSQL..."
        docker exec postgres pg_dump -U esl esl > "$backupDir\postgres_$timestamp.sql"

        # Sauvegarde MongoDB
        Write-Output "==> Sauvegarde MongoDB..."
        docker exec esl-mongodb mongodump --username esl --password Esl22102024 --authenticationDatabase admin --db esl --archive > "$backupDir\mongodb_$timestamp.archive"

        # Sauvegarde MQTT (si tu veux juste garder la config / données persistantes du conteneur MQTT, sinon pas nécessaire)
        Write-Output "==> Sauvegarde MQTT broker..."
        docker export mqtt_broker > "$backupDir\mqtt_$timestamp.tar"

        # Garder seulement les 5 derniers fichiers par service
        Write-Output "==> Nettoyage des anciennes sauvegardes..."
        Get-ChildItem "$backupDir\postgres_*.sql" | Sort-Object LastWriteTime -Descending | Select-Object -Skip 5 | Remove-Item
        Get-ChildItem "$backupDir\mongodb_*.archive" | Sort-Object LastWriteTime -Descending | Select-Object -Skip 5 | Remove-Item
        Get-ChildItem "$backupDir\mqtt_*.tar" | Sort-Object LastWriteTime -Descending | Select-Object -Skip 5 | Remove-Item

        Write-Output "==> Sauvegarde terminée à $(Get-Date)"

        Étape 3 : Planifier l’exécution automatique (toutes les 12h)

        Ouvre le Planificateur de tâches Windows.

        Clique sur Créer une tâche.

        Onglet Déclencheurs → Nouveau → Toutes les 12 heures.

        Onglet Actions → Démarrer un programme → mets :

        Programme/script : powershell.exe

        Arguments : -File "C:\docker_backups\backup.ps1"

        Valide.

        Étape 4 : Vérification manuelle

        Tu peux tester le script directement en PowerShell :

        powershell -File C:\docker_backups\backup.ps1


        Tu devrais voir les fichiers postgres_*.sql, mongodb_*.archive, et mqtt_*.tar dans C:\docker_backups.
        ```
        - [x] Restauration, plan de route : 
        ```
        🔹 1. Restaurer PostgreSQL (postgres_xxx.sql)
        Étape 1 : Copie du fichier dans le container

        Si ton container postgres tourne déjà, tu peux envoyer le fichier SQL dedans :

        docker cp C:\docker_backups\postgres_20250929_120000.sql postgres:/tmp/restore.sql

        Étape 2 : Exécuter la restauration

        Ensuite, lance :

        docker exec -i postgres psql -U esl -d esl -f /tmp/restore.sql


        👉 Cela réécrit les données de la base esl à l’état du backup.

        🔹 2. Restaurer MongoDB (mongodb_xxx.archive)
        Étape 1 : Copier le fichier dans le container
        docker cp C:\docker_backups\mongodb_20250929_120000.archive esl-mongodb:/tmp/restore.archive

        Étape 2 : Lancer la restauration
        docker exec -i esl-mongodb mongorestore --username esl --password Esl22102024 --authenticationDatabase admin --db esl --archive=/tmp/restore.archive --drop


        --drop supprime les collections existantes avant d’importer (pour éviter les doublons).

        Tu peux l’enlever si tu veux juste ajouter les données.

        🔹 3. Restaurer MQTT (mqtt_xxx.tar)

        ⚠️ Ici tu as exporté tout le container (pas juste les données). Deux options :

        Option A – Recréer un container à partir du backup
        docker import C:\docker_backups\mqtt_20250929_120000.tar mqtt_broker_restored
        docker run -d --name mqtt_broker_restored -p 1884:1884 mqtt_broker_restored

        Option B – Restaurer uniquement le volume/dossier persistant

        Si ton MQTT utilise un volume (ex : /mosquitto/data), il vaut mieux sauvegarder et restaurer ce volume au lieu d’exporter tout le container. Dans ce cas tu fais un simple :

        docker cp C:\docker_backups\mqtt_data.zip mqtt_broker:/mosquitto/data


        et tu redémarres le container.

        🔹 4. Vérification après restauration

        Postgres : connecte-toi avec Adminer (localhost:8080) et vérifie les tables.

        MongoDB : vérifie avec docker exec -it esl-mongodb mongosh -u esl -p Esl22102024 esl.

        MQTT : relance un client et teste la connexion.
        ```
    - [ ] ESL-441 Ajuster le script de détection des bases pour prendre en compte 192.168.1 en plus du préfixe réseau
        - [x] Refonte du script pour exclure le 192.168.1.1 et ne sélectionner que les bases Equitech : 
        ```
        function Install-Nmap {
            if (-not (Get-Command nmap -ErrorAction SilentlyContinue)) {
                Write-Host "Installation de Nmap..."
                # Telecharger et installer Nmap
                Invoke-WebRequest -Uri "https://nmap.org/dist/nmap-7.92-setup.exe" -OutFile "nmap-installer.exe"
                Start-Process -Wait -FilePath .\nmap-installer.exe -ArgumentList '/S'
                Remove-Item .\nmap-installer.exe
            } else {
                Write-Host "Nmap est deja installe."
            }
        }

        # Function to get the network prefix from ipconfig
        function Get-NetworkPrefix {
            $ipconfigOutput = ipconfig
            $ipv4Pattern = "(?:Adresse IPv4|IPv4 Address)[^:]+:\s*(\d{1,3}\.\d{1,3}\.\d{1,3})\.\d{1,3}"

            $networkPrefix = $null

            # Iterate through each line of the ipconfig output
            $ipconfigOutput | ForEach-Object {
                $line = $_
                if ($line -match $ipv4Pattern) {
                    $networkPrefix = $matches[1]
                    Write-Host "Adresse IP trouvee : $networkPrefix"
                }
            }

            if ($networkPrefix) {
                Write-Host "Prefixe reseau : $networkPrefix"
                return $networkPrefix
            } else {
                throw "Impossible de determiner le prefixe reseau avec ipconfig."
            }
        }

        # Fonction pour extraire IPs d'un resultat nmap -sn (SEULEMENT vendor Equitech)
        function Parse-NmapHosts {
            param(
                [string[]]$ScanOutput
            )

            $ips = @()
            for ($i = 0; $i -lt $ScanOutput.Count; $i++) {
                $line = $ScanOutput[$i]

                # Extraire l'IP de la ligne de rapport
                $ipMatch = [regex]::Match($line, "Nmap scan report for (?:[^\(]+ \()?(\d{1,3}(?:\.\d{1,3}){3})\)?")
                if ($ipMatch.Success) {
                    $ip = $ipMatch.Groups[1].Value

                    # Verifie les lignes suivantes (jusqu'à 5) pour trouver un vendor
                    $vendor = $null
                    for ($j = $i + 1; $j -lt [Math]::Min($i + 6, $ScanOutput.Count); $j++) {
                        $macLine = $ScanOutput[$j]
                        $vendorMatch = [regex]::Match($macLine, "MAC Address:\s*[0-9A-Fa-f:]{17}\s*\((.+?)\)")
                        if ($vendorMatch.Success) {
                            $vendor = $vendorMatch.Groups[1].Value
                            break
                        }
                    }

                    if ($vendor -and $vendor -imatch "Equitech" -and $ip -ne "192.168.1.1") {
                        $ips += $ip
                    }
                }
            }

            return $ips | Sort-Object -Unique
        }



        # Fonction pour scanner le reseau et detecter les bases
        function Scan-Network {
            $outputFile = "bases.txt"
            $allBases = @()

            Write-Host "Premier scan par defaut : nmap -sn 192.168.1.0/24"
            try {
                $defaultScan = & nmap -sn "192.168.1.0/24" 2>&1
            } catch {
                Write-Host "Erreur lors de l'execution de nmap sur 192.168.1.0/24 : $_"
                $defaultScan = @()
            }

            $found = Parse-NmapHosts -ScanOutput $defaultScan

            if ($found.Count -gt 0) {
                Write-Host "Bases trouvees avec le scan par defaut:"
                $found | ForEach-Object { Write-Host $_ }
                $allBases += $found
            } else {
                Write-Host "Aucune base trouvee avec 192.168.1.0/24. Repli sur la logique actuelle..."
                try {
                    $networkPrefix = Get-NetworkPrefix
                }
                catch {
                    Write-Host "Utilisation du prefixe reseau par defaut 192.168.1"
                    $networkPrefix = "192.168.1"
                }

                Write-Host "Execution de nmap -sn $($networkPrefix).0/24"
                try {
                    $scanResults = & nmap -sn "$($networkPrefix).0/24" 2>&1
                } catch {
                    Write-Host "Erreur lors de l'execution de nmap sur $($networkPrefix).0/24 : $_"
                    $scanResults = @()
                }

                $found2 = Parse-NmapHosts -ScanOutput $scanResults

                if ($found2.Count -gt 0) {
                    Write-Host "Bases trouvees avec le scan sur $networkPrefix.0/24:"
                    $found2 | ForEach-Object { Write-Host $_ }
                    $allBases += $found2
                } else {
                    Write-Host "Aucune base trouvee avec la logique actuelle non plus."
                }
            }

            # Sauvegarder les adresses IP dans un fichier (même si vide)
            $allBases = $allBases | Sort-Object -Unique
            $allBases | Out-File -FilePath $outputFile -Force
            Write-Host "Les adresses IP des bases ont ete sauvegardees dans $outputFile (count: $($allBases.Count))"
        }

        # Executer les fonctions
        Install-Nmap
        Scan-Network
        ```
    - [ ] ESL-370 Créer un script pour associer automatiquement les bases au mqtt via gopod