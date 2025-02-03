**27 Janvier**
- [ ] ESL
    - [ ] ESL-249 Fix la légende des charts du dashboard
        - [x] Modif du html : 
        ```
        <div class="charts-section grid grid-cols-1 sm:grid-cols-2 gap-6">
        <div class="chart-card">
            <h3 class="chart-title">Activité des ESLs Hors-service sur la durée</h3>
            <canvas id="eslBrokenActivityChart"></canvas>
        </div>
        <div class="chart-card">
            <h3 class="chart-title">Activité des ESLs par heure</h3>
            <canvas id="eslHourlyActivityChart"></canvas>
        </div>
        <div class="chart-card">
            <h3 class="chart-title">Activité des ESLs par jour</h3>
            <canvas id="eslDailyActivityChart"></canvas>
        </div>
        <div class="chart-card">
            <h3 class="chart-title">ESLs associées vs non associées</h3>
            <canvas id="eslAssociationChart" class="fixed-size-chart"></canvas>
        </div>
        </div>
        ```
        - [x] Modif du component :
        ```
        initializeBrokenActivityChart() {
            if (this.eslBrokenActivityChart) {
            this.eslBrokenActivityChart.destroy();
            }
            this.eslBrokenActivityChart = new Chart('eslBrokenActivityChart', {
            type: 'line',
            data: {
                labels: this.generateHourLabels(),
                datasets: [
                {
                    label: 'Active ESLs',
                    data: [5, 10, 15, 20, 25, 30],
                    borderColor: 'rgba(75, 192, 192, 1)',
                    backgroundColor: 'rgba(75, 192, 192, 0.2)',
                },
                ],
            },
            options: {
                responsive: true,
                plugins: {
                legend: { display: true },
                },
            },
            });
        }
        ...
        generateHourLabels(): string[] {
            const now = new Date();
            const labels = [];
            for (let i = 23; i >= 0; i--) {
            const hour = (now.getHours() - i + 24) % 24;
            labels.push(`${hour}:00`);
            }
            return labels;
        }
        ```
    - [ ] ESL-250 Brancher la logique d'ESLs broken sur le premier chart
- [x] Configuration du mini pc pour serveur ESLs




        session anthony pw 123soleil

arrêt maladie 28-29 Janvier

**30 Janvier**
- [ ] ESL
    - [ ] ESL-250
    - [ ] ESL-254
    - [ ] ESL-257



Réunion anthony backlog projets
- app mobile bureau connecté - react native/expo
- smartlocker systeme de paiement - nayax
- kmo video
- esl V2 (version cloud & integration portail saas) - angular nest
- portail saas - keycloak

artem : le mettre sur l'app mobile bureau connecté. Implique de poser les bases de l'env dev de l'app mobile avec React Native. Implique d'apprendre react

esl cloud, voir ce que ça implique
- sécuriser l'api
- voir avec jacques pour la communication mqtt
- voir les contraintes appliquées à l'envoi d'images & bdd

- [x] Veille techno sur React native


**31 Janvier****30 Janvier**
- [ ] ESL
    - [x] ESL-250 
        - [x] Actualisation des variables pour populer le diagramme :
        ```
        initializeBrokenActivityChart() {
            if (this.eslBrokenActivityChart) {
            this.eslBrokenActivityChart.destroy();
            }
            
            this.eslService.getEslsBrokenCount().subscribe((count) => {
            this.numberOfBrokenEsls = count;
            this.eslService.getEsls().subscribe((allEsls) => {
                this.numberOfEsl = allEsls.length;
                this.eslBrokenActivityChart = new Chart('eslBrokenActivityChart', {
                type: 'pie',
                data: {
                    labels: ['Broken ESLs', 'Non-Broken ESLs'],
                    datasets: [
                    {
                        data: [this.numberOfBrokenEsls, this.numberOfEsl - this.numberOfBrokenEsls],
                        backgroundColor: ['#F44336', '#4CAF50'],
                    },
                    ],
                },
                options: {
                    responsive: true,
                    plugins: {
                    legend: { display: true },
                    },
                },
                });
            });
            
            });
        }
        ```
    - [x] ESL-254 
        - [x] Ajout d'un snackbar et redirection vers l'accueil après 2s : 
        ```
        handleFileInput(event: Event): void {
            const inputElement = event.target as HTMLInputElement;
            if (inputElement.files && inputElement.files.length > 0) {
            const file = inputElement.files[0];
            if (file) {
                this.parseCSVAndSendToBackend(file);
                this.snackBar.open('The CSV file has been processed', 'Close', {
                duration: 2000,
                });
                setTimeout(() => {
                this.router.navigate(['/']);
                }, 2000);
            }
            }
        }
        ```
    - [x] ESL-257
        - [x] Suppression des liens & icônes dans la sidebar
    - [ ] Redéploiement
        - [x] fix des tests frontend
        - [x] Repush 