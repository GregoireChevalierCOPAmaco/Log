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