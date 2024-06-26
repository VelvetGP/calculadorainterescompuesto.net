<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora de Interés Compuesto</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f9;
            color: #333;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            flex-direction: column;
        }
        .container {
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            width: 90%;
            max-width: 1200px;
            display: flex;
            flex-direction: column;
        }
        .title {
            text-align: center;
            margin-bottom: 20px;
        }
        .content {
            display: flex;
            justify-content: space-between;
        }
        .form-section {
            width: 30%;
            padding-right: 20px;
            border-right: 1px solid #ccc;
        }
        .chart-section {
            width: 70%;
            padding-left: 20px;
        }
        .form-section input, .form-section select {
            width: calc(100% - 20px);
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            border: 1px solid #ccc;
        }
        .form-section input[type="number"]::-webkit-outer-spin-button,
        .form-section input[type="number"]::-webkit-inner-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
        .form-section input[type="number"] {
            -moz-appearance: textfield;
        }
        .result {
            margin-top: 20px;
            font-size: 1.2em;
            font-weight: bold;
            color: #007BFF;
            text-align: center;
        }
        .results-section {
            display: flex;
            justify-content: space-around;
            margin-top: 20px;
            text-align: center;
            padding: 0 10%;
        }
        .result-item {
            width: 22%;
        }
        .result-item p {
            margin: 5px 0;
        }
        .result-item i {
            font-size: 2em;
            margin-bottom: 10px;
            color: #007BFF;
        }
        .social {
            margin-top: 20px;
            text-align: center;
        }
        .social a {
            color: #007BFF;
            text-decoration: none;
            display: inline-block;
            margin-top: 10px;
        }
        .social a:hover {
            text-decoration: underline;
        }
        .social i {
            font-size: 2em;
            color: #007BFF;
        }
        .table-section {
            margin-top: 40px;
            overflow-x: auto;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin: 0 auto;
        }
        th, td {
            padding: 10px;
            border: 1px solid #ddd;
            text-align: center;
        }
        th {
            background-color: #f4f4f9;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="title">Calculadora de Interés Compuesto</h1>
        <div class="content">
            <div class="form-section">
                <form id="calcForm">
                    <label for="principal">Depósito inicial</label>
                    <input type="number" id="principal" placeholder="$ 0" required oninput="calculateCompoundInterest()">
                    <label for="annualRate">Tasa de interés anual</label>
                    <input type="number" id="annualRate" placeholder="% 0" step="0.01" required oninput="calculateCompoundInterest()">
                    <label for="years">Años a invertir</label>
                    <input type="number" id="years" placeholder="0" required oninput="calculateCompoundInterest()">
                    <label for="frequency">Frecuencia anual de interés</label>
                    <select id="frequency" required onchange="calculateCompoundInterest()">
                        <option value="1">Anualmente</option>
                        <option value="12">Mensualmente</option>
                        <option value="24">Quincenalmente</option>
                        <option value="52">Semanalmente</option>
                        <option value="365">Diariamente</option>
                    </select>
                    <label for="additionalContribution">Aportaciones adicionales</label>
                    <input type="number" id="additionalContribution" placeholder="$ 0" required oninput="calculateCompoundInterest()">
                </form>
                <div id="result" class="result"></div>
            </div>
            <div class="chart-section">
                <canvas id="interestChart" height="250"></canvas>
            </div>
        </div>
        <div class="results-section">
            <div class="result-item">
                <i class="fas fa-wallet"></i>
                <p>Depósito inicial</p>
                <p id="initialDepositResult">$0.00</p>
            </div>
            <div class="result-item">
                <i class="fas fa-coins"></i>
                <p>Depósitos adicionales acumulados</p>
                <p id="additionalDepositsResult">$0.00</p>
            </div>
            <div class="result-item">
                <i class="fas fa-chart-line"></i>
                <p>Interés acumulado</p>
                <p id="accumulatedInterestResult">$0.00</p>
            </div>
            <div class="result-item">
                <i class="fas fa-dollar-sign"></i>
                <p>Total</p>
                <p id="totalResult">$0.00</p>
            </div>
        </div>
        <div class="table-section">
            <h2>Resultados por año</h2>
            <table id="resultsTable">
                <thead>
                    <tr>
                        <th>Años</th>
                        <th>Depósito inicial</th>
                        <th>Depósitos adicionales acumulados</th>
                        <th>Interés acumulado</th>
                        <th>Total</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- Table rows will be inserted here dynamically -->
                </tbody>
            </table>
        </div>
        <div class="social">
            <p>Sígueme en Instagram:</p>
            <a href="https://www.instagram.com/andrewguzm4n/" target="_blank">
                <i class="fab fa-instagram"></i>
            </a>
        </div>
    </div>
    
    <script>
        const ctx = document.getElementById('interestChart').getContext('2d');
        const interestChart = new Chart(ctx, {
            type: 'bar',
            data: {
                labels: [],
                datasets: [{
                    label: 'Depósito inicial',
                    data: [],
                    backgroundColor: 'rgba(0, 123, 255, 0.6)',
                    borderColor: 'rgba(0, 123, 255, 1)',
                    borderWidth: 1,
                    barPercentage: 1.0,
                    categoryPercentage: 1.0
                },
                {
                    label: 'Depósitos adicionales acumulados',
                    data: [],
                    backgroundColor: 'rgba(40, 167, 69, 0.6)',
                    borderColor: 'rgba(40, 167, 69, 1)',
                    borderWidth: 1,
                    barPercentage: 1.0,
                    categoryPercentage: 1.0
                },
                {
                    label: 'Interés acumulado',
                    data: [],
                    backgroundColor: 'rgba(255, 193, 7, 0.6)',
                    borderColor: 'rgba(255, 193, 7, 1)',
                    borderWidth: 1,
                    barPercentage: 1.0,
                    categoryPercentage: 1.0
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    x: {
                        title: {
                            display: true,
                            text: 'Años'
                        },
                        ticks: {
                            maxRotation: 0,
                            minRotation: 0,
                            autoSkip: true,
                            maxTicksLimit: 10
                        }
                    },
                    y: {
                        title: {
                            display: false
                        },
                        ticks: {
                            callback: function(value) {
                                return value.toLocaleString();
                            }
                        }
                    }
                },
                plugins: {
                    legend: {
                        display: true,
                        position: 'top',
                        labels: {
                            usePointStyle: true
                        }
                    },
                    tooltip: {
                        callbacks: {
                            label: function(context) {
                                return context.dataset.label + ': $' + context.raw.toLocaleString();
                            }
                        }
                    }
                }
            }
        });

        function calculateCompoundInterest() {
            const principal = parseFloat(document.getElementById('principal').value) || 0;
            const annualRate = parseFloat(document.getElementById('annualRate').value) / 100 || 0;
            const years = parseFloat(document.getElementById('years').value) || 0;
            const frequency = parseInt(document.getElementById('frequency').value) || 1;
            const additionalContribution = parseFloat(document.getElementById('additionalContribution').value) || 0;

            const timesCompounded = frequency * years;
            const ratePerPeriod = annualRate / frequency;

            let futureValue = principal * Math.pow(1 + ratePerPeriod, timesCompounded);
            let dataInitial = [];
            let dataAdditional = [];
            let dataInterest = [];
            let labels = [];
            let tableRows = '';

            let accumulatedAdditional = 0;
            let accumulatedInterest = 0;

            for (let i = 1; i <= timesCompounded; i++) {
                futureValue += additionalContribution * Math.pow(1 + ratePerPeriod, timesCompounded - i);
                accumulatedAdditional += additionalContribution;
                accumulatedInterest = futureValue - principal - accumulatedAdditional;

                if (i % frequency === 0) {
                    const year = i / frequency;
                    labels.push(year + ' años');
                    dataInitial.push(principal);
                    dataAdditional.push(accumulatedAdditional);
                    dataInterest.push(accumulatedInterest);
                    tableRows += `
                        <tr>
                            <td>${year}</td>
                            <td>$${principal.toLocaleString()}</td>
                            <td>$${accumulatedAdditional.toLocaleString()}</td>
                            <td>$${accumulatedInterest.toLocaleString()}</td>
                            <td>$${futureValue.toLocaleString()}</td>
                        </tr>
                    `;
                }
            }

            document.getElementById('initialDepositResult').textContent = `$${principal.toLocaleString()}`;
            document.getElementById('additionalDepositsResult').textContent = `$${accumulatedAdditional.toLocaleString()}`;
            document.getElementById('accumulatedInterestResult').textContent = `$${accumulatedInterest.toLocaleString()}`;
            document.getElementById('totalResult').textContent = `$${futureValue.toLocaleString()}`;

            interestChart.data.labels = labels;
            interestChart.data.datasets[0].data = dataInitial;
            interestChart.data.datasets[1].data = dataAdditional;
            interestChart.data.datasets[2].data = dataInterest;
            interestChart.update();

            document.querySelector('#resultsTable tbody').innerHTML = tableRows;
        }

        // Initialize chart with default values
        calculateCompoundInterest();
    </script>
</body>
</html>
