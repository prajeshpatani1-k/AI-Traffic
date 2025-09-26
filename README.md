<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Smart Traffic Management System - Dashboard</title>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            --primary-color: #1a73e8;
            --secondary-color: #34a853;
            --danger-color: #ea4335;
            --warning-color: #f9ab00;
            --dark-color: #202124;
            --bg-color: #f8f9fa;
            --card-bg: #ffffff;
            --text-color: #343a40;
            --border-color: #e9ecef;
            --shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: 'Roboto', sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            line-height: 1.6;
        }

        header {
            background: linear-gradient(135deg, var(--primary-color), #0056b3);
            color: white;
            padding: 18px 20px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            position: sticky;
            top: 0;
            z-index: 100;
        }

        .header-content {
            display: flex;
            justify-content: space-between;
            align-items: center;
            max-width: 1400px;
            margin: 0 auto;
        }

        .logo-container {
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .logo {
            width: 50px;
            height: 50px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
        }

        .header-text h1 {
            font-size: 1.8rem;
            font-weight: 700;
            margin-bottom: 5px;
        }

        .header-text p {
            font-size: 0.9rem;
            opacity: 0.9;
        }

        .time-display {
            background: rgba(255, 255, 255, 0.15);
            padding: 10px 15px;
            border-radius: 6px;
            font-size: 1.1rem;
            font-weight: 500;
        }

        .dashboard-container {
            display: grid;
            grid-template-columns: 300px 1fr;
            gap: 20px;
            padding: 20px;
            max-width: 1400px;
            margin: 0 auto;
        }

        .sidebar {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .main-content {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            grid-template-rows: auto auto 1fr;
            gap: 20px;
        }

        .card {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            box-shadow: var(--shadow);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
            border-left: 4px solid var(--primary-color);
        }

        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 16px rgba(0, 0, 0, 0.12);
        }

        .card.kpi {
            text-align: center;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 25px 15px;
        }

        .kpi .value {
            font-size: 2.8rem;
            font-weight: 700;
            margin: 10px 0;
            line-height: 1;
        }

        .kpi .label {
            font-size: 0.9rem;
            color: #6c757d;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .kpi.reduction .value { color: var(--secondary-color); }
        .kpi.delay .value { color: var(--danger-color); }
        .kpi.throughput .value { color: var(--primary-color); }
        
        .kpi .change {
            font-size: 1rem;
            font-weight: 600;
            margin-top: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 5px;
        }
        .kpi .positive { color: var(--secondary-color); }
        .kpi .negative { color: var(--danger-color); }

        .full-width-card {
            grid-column: 1 / -1;
            min-height: 400px;
            padding: 0;
            overflow: hidden;
        }

        #live-map-feed {
            height: 100%;
            background-color: #e9ecef;
            border-radius: 12px;
            overflow: hidden;
            position: relative;
        }
        
        .feed-label {
            position: absolute;
            top: 15px;
            left: 15px;
            background: rgba(0, 0, 0, 0.7);
            padding: 8px 15px;
            border-radius: 6px;
            font-size: 0.8rem;
            color: white;
            z-index: 10;
            backdrop-filter: blur(5px);
        }

        .map-container {
            width: 100%;
            height: 100%;
            background: linear-gradient(145deg, #e0e0e0, #f5f5f5);
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
        }

        .traffic-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
        }

        .congestion-marker {
            position: absolute;
            width: 12px;
            height: 12px;
            border-radius: 50%;
            transform: translate(-50%, -50%);
            animation: pulse 2s infinite;
        }

        .congestion-low { background-color: var(--secondary-color); }
        .congestion-medium { background-color: var(--warning-color); }
        .congestion-high { background-color: var(--danger-color); }

        @keyframes pulse {
            0% { transform: translate(-50%, -50%) scale(1); opacity: 1; }
            70% { transform: translate(-50%, -50%) scale(2.5); opacity: 0.3; }
            100% { transform: translate(-50%, -50%) scale(3); opacity: 0; }
        }

        .chart-card {
            grid-column: span 1;
            min-height: 300px;
            display: flex;
            flex-direction: column;
        }
        
        .chart-container {
            flex-grow: 1;
            position: relative;
            margin-top: 10px;
        }
        
        .control-section h3 {
            border-bottom: 2px solid var(--border-color);
            padding-bottom: 12px;
            margin-bottom: 15px;
            color: var(--primary-color);
            font-size: 1.2rem;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        .control-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 0;
            border-bottom: 1px dashed var(--border-color);
        }
        
        .control-item:last-child {
            border-bottom: none;
        }

        .status-badge {
            padding: 6px 12px;
            border-radius: 20px;
            font-weight: 600;
            font-size: 0.75rem;
            letter-spacing: 0.5px;
        }
        .status-badge.ai { background-color: var(--secondary-color); color: white; }
        .status-badge.manual { background-color: var(--danger-color); color: white; }
        .status-badge.alert { background-color: var(--warning-color); color: white; }
        
        .action-button {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 8px 15px;
            border-radius: 6px;
            cursor: pointer;
            transition: all 0.2s;
            font-size: 0.85rem;
            font-weight: 500;
        }
        .action-button:hover {
            background-color: #0d62c9;
            transform: translateY(-1px);
        }
        .action-button.switch {
            background-color: var(--warning-color);
            color: var(--dark-color);
        }
        .action-button.switch:hover {
            background-color: #e09a00;
        }
        .action-button.emergency {
            background-color: var(--danger-color);
        }
        .action-button.emergency:hover {
            background-color: #d33426;
        }

        .alert-item {
            padding: 12px;
            border-radius: 8px;
            margin-bottom: 10px;
            display: flex;
            flex-direction: column;
            gap: 8px;
        }
        
        .alert-congestion {
            background-color: rgba(249, 171, 0, 0.1);
            border-left: 4px solid var(--warning-color);
        }
        
        .alert-incident {
            background-color: rgba(234, 67, 53, 0.1);
            border-left: 4px solid var(--danger-color);
        }

        .alert-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .intersection-status {
            padding: 15px;
            background-color: #f8f9fa;
            border-radius: 8px;
            margin-top: 15px;
            border: 1px solid var(--border-color);
        }
        
        .is-metric {
            margin-bottom: 8px;
            font-size: 0.9rem;
            display: flex;
            justify-content: space-between;
        }
        
        .is-metric span:last-child {
            font-weight: 600;
            color: var(--primary-color);
        }

        select {
            width: 100%;
            padding: 10px;
            border-radius: 6px;
            border: 1px solid var(--border-color);
            background-color: white;
            font-size: 0.9rem;
            margin-bottom: 15px;
        }

        footer {
            text-align: center;
            padding: 20px;
            color: #6c757d;
            font-size: 0.8rem;
            margin-top: 20px;
            border-top: 1px solid var(--border-color);
            max-width: 1400px;
            margin: 0 auto;
        }

        /* Responsive Design */
        @media (max-width: 1200px) {
            .dashboard-container {
                grid-template-columns: 1fr;
            }
            
            .main-content {
                grid-template-columns: repeat(2, 1fr);
            }
            
            .full-width-card {
                grid-column: 1 / -1;
            }
        }

        @media (max-width: 768px) {
            .main-content {
                grid-template-columns: 1fr;
            }
            
            .header-content {
                flex-direction: column;
                gap: 15px;
                text-align: center;
            }
            
            .card.kpi .value {
                font-size: 2.2rem;
            }
        }
    </style>
</head>
<body>

    <header>
        <div class="header-content">
            <div class="logo-container">
                <div class="logo">🚦</div>
                <div class="header-text">
                    <h1>Smart Traffic Management System</h1>
                    <p>AI-Powered Urban Mobility Optimization</p>
                </div>
            </div>
            <div class="time-display" id="current-time">Loading...</div>
        </div>
    </header>

    <div class="dashboard-container">
        <div class="sidebar">
            <div class="card">
                <div class="control-section">
                    <h3>🚦 System Status & Control</h3>
                    <div class="control-item">
                        <span>AI Model Status:</span>
                        <span class="status-badge ai">ACTIVE (RL)</span>
                    </div>
                    <div class="control-item">
                        <span>Active Control Mode:</span>
                        <span class="status-badge ai">AI-ADAPTIVE</span>
                    </div>
                    <div class="control-item">
                        <span>System Health:</span>
                        <span class="status-badge ai">NORMAL</span>
                    </div>
                    <div class="control-item">
                        <span>Override Control:</span>
                        <button class="action-button switch" id="control-mode-btn">Switch to Manual</button>
                    </div>
                </div>
            </div>

            <div class="card">
                <div class="control-section">
                    <h3>⚠️ Real-time Alerts <span id="alert-count">(2)</span></h3>
                    <div class="alert-item alert-congestion">
                        <div class="alert-header">
                            <span class="status-badge alert">CONGESTION ALERT</span>
                            <span class="time-ago">5 min ago</span>
                        </div>
                        <span><strong>KIIT Square:</strong> Queue ≥ 400m (AI Responding)</span>
                    </div>
                    <div class="alert-item alert-incident">
                        <div class="alert-header">
                            <span class="status-badge manual">INCIDENT DETECTED</span>
                            <span class="time-ago">2 min ago</span>
                        </div>
                        <span><strong>Rasulgarh:</strong> Accident (CV Flagged)</span>
                        <button class="action-button emergency">Dispatch Unit</button>
                    </div>
                </div>
            </div>
            
            <div class="card">
                <div class="control-section">
                    <h3>📍 Intersection Details</h3>
                    <select id="junction-select">
                        <option>Select Junction...</option>
                        <option selected value="kiit">KIIT Square (JID: 01)</option>
                        <option value="rasulgarh">Rasulgarh (JID: 02)</option>
                        <option value="master">Master Canteen (JID: 03)</option>
                    </select>
                    <div class="intersection-status">
                        <div class="is-metric"><span>Model State:</span> <span id="model-state">Predicting Congestion</span></div>
                        <div class="is-metric"><span>Current Phase:</span> <span id="current-phase">North-South (45s)</span></div>
                        <div class="is-metric"><span>Queue Length (E-W):</span> <span id="queue-length">380m</span></div>
                        <div class="is-metric"><span>Vehicle Count (Last Min):</span> <span id="vehicle-count">125</span></div>
                        <div class="is-metric"><span>AI Action:</span> <span id="ai-action">Phase Extension (10s)</span></div>
                        <button class="action-button" style="width: 100%; margin-top: 10px;">Force Green Corridor</button>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="main-content">
            <div class="card kpi reduction">
                <div class="label">Commute Time Reduction (Avg)</div>
                <div class="value" id="reduction-value">12.5%</div>
                <div class="change positive">↑ 2.5% vs. Previous Month</div>
            </div>
            
            <div class="card kpi delay">
                <div class="label">Vehicle Wait Time (Network Avg)</div>
                <div class="value" id="wait-time">95s</div>
                <div class="change negative">↓ 15s (13.6% Better)</div>
            </div>
            
            <div class="card kpi throughput">
                <div class="label">Network Throughput (Peak Hour)</div>
                <div class="value" id="throughput">2,100</div>
                <div class="change positive">↑ 150 Vehicles/Hr</div>
            </div>

            <div class="card full-width-card">
                <div id="live-map-feed">
                    <span class="feed-label">REAL-TIME TRAFFIC FLOW MAP</span>
                    <div class="map-container">
                        <div class="traffic-overlay" id="traffic-overlay">
                            <!-- Traffic markers will be added here by JavaScript -->
                        </div>
                        <div style="text-align: center; color: #666; padding: 20px;">
                            <h3>Bhubaneswar Traffic Network</h3>
                            <p>AI-powered congestion visualization</p>
                        </div>
                    </div>
                </div>
            </div>

            <div class="card chart-card">
                <h4>Queue Length Prediction (Next 15 Min)</h4>
                <div class="chart-container">
                    <canvas id="queue-chart"></canvas>
                </div>
            </div>
            
            <div class="card chart-card">
                <h4>Vehicle Classification (Last Hour)</h4>
                <div class="chart-container">
                    <canvas id="vehicle-chart"></canvas>
                </div>
            </div>
            
            <div class="card chart-card">
                <h4>Signal Timing (AI vs Fixed)</h4>
                <div class="chart-container">
                    <canvas id="signal-chart"></canvas>
                </div>
            </div>
        </div>
    </div>

    <footer>
        <p>© 2025 Integrated Command & Control Center. STMS Version 2.0 | Last Updated: <span id="last-updated">Loading...</span></p>
    </footer>

    <script>
        // Initialize real-time clock
        function updateClock() {
            const now = new Date();
            const timeString = now.toLocaleTimeString('en-US', { 
                hour12: true, 
                hour: '2-digit', 
                minute: '2-digit',
                second: '2-digit'
            });
            const dateString = now.toLocaleDateString('en-US', { 
                weekday: 'long', 
                year: 'numeric', 
                month: 'long', 
                day: 'numeric' 
            });
            document.getElementById('current-time').innerHTML = `${dateString}<br>${timeString}`;
            
            // Update last updated time
            document.getElementById('last-updated').textContent = now.toLocaleTimeString();
        }
        
        // Control mode toggle
        document.getElementById('control-mode-btn').addEventListener('click', function() {
            const modeBadge = document.querySelector('.control-item:nth-child(2) .status-badge');
            const button = this;
            
            if (modeBadge.classList.contains('ai')) {
                modeBadge.className = 'status-badge manual';
                modeBadge.textContent = 'MANUAL';
                button.textContent = 'Switch to AI';
                button.style.backgroundColor = '#34a853';
            } else {
                modeBadge.className = 'status-badge ai';
                modeBadge.textContent = 'AI-ADAPTIVE';
                button.textContent = 'Switch to Manual';
                button.style.backgroundColor = '#f9ab00';
            }
        });
        
        // Junction selection handler
        document.getElementById('junction-select').addEventListener('change', function() {
            const junctionData = {
                kiit: {
                    modelState: "Predicting Congestion",
                    currentPhase: "North-South (45s)",
                    queueLength: "380m",
                    vehicleCount: "125",
                    aiAction: "Phase Extension (10s)"
                },
                rasulgarh: {
                    modelState: "Incident Response",
                    currentPhase: "All-Red (Emergency)",
                    queueLength: "210m",
                    vehicleCount: "85",
                    aiAction: "Emergency Clearance"
                },
                master: {
                    modelState: "Normal Operation",
                    currentPhase: "East-West (35s)",
                    queueLength: "150m",
                    vehicleCount: "95",
                    aiAction: "Standard Cycle"
                }
            };
            
            const selectedJunction = this.value;
            if (selectedJunction in junctionData) {
                const data = junctionData[selectedJunction];
                document.getElementById('model-state').textContent = data.modelState;
                document.getElementById('current-phase').textContent = data.currentPhase;
                document.getElementById('queue-length').textContent = data.queueLength;
                document.getElementById('vehicle-count').textContent = data.vehicleCount;
                document.getElementById('ai-action').textContent = data.aiAction;
            }
        });
        
        // Simulate real-time data updates
        function updateKPIs() {
            // Randomize KPI values slightly to simulate real-time updates
            const reductionValue = document.getElementById('reduction-value');
            const waitTime = document.getElementById('wait-time');
            const throughput = document.getElementById('throughput');
            
            // Small random fluctuations
            const reductionChange = (Math.random() - 0.5) * 0.5;
            const waitChange = (Math.random() - 0.5) * 2;
            const throughputChange = (Math.random() - 0.5) * 30;
            
            reductionValue.textContent = (12.5 + reductionChange).toFixed(1) + '%';
            waitTime.textContent = Math.max(80, Math.min(110, 95 + waitChange)) + 's';
            throughput.textContent = Math.round(2100 + throughputChange).toLocaleString();
        }
        
        // Initialize traffic congestion visualization
        function initTrafficVisualization() {
            const overlay = document.getElementById('traffic-overlay');
            
            // Clear existing markers
            overlay.innerHTML = '';
            
            // Add congestion markers at random positions (simulating traffic hotspots)
            const congestionLevels = ['low', 'medium', 'high'];
            const markerCount = 15;
            
            for (let i = 0; i < markerCount; i++) {
                const marker = document.createElement('div');
                const level = congestionLevels[Math.floor(Math.random() * congestionLevels.length)];
                
                marker.className = `congestion-marker congestion-${level}`;
                marker.style.left = `${10 + Math.random() * 80}%`;
                marker.style.top = `${10 + Math.random() * 80}%`;
                marker.style.animationDelay = `${Math.random() * 2}s`;
                
                overlay.appendChild(marker);
            }
        }
        
        // Initialize charts
        function initCharts() {
            // Queue Length Prediction Chart
            const queueCtx = document.getElementById('queue-chart').getContext('2d');
            new Chart(queueCtx, {
                type: 'line',
                data: {
                    labels: ['-10', '-5', 'Now', '+5', '+10', '+15'],
                    datasets: [{
                        label: 'Predicted Queue (m)',
                        data: [300, 350, 380, 420, 390, 360],
                        borderColor: '#ea4335',
                        backgroundColor: 'rgba(234, 67, 53, 0.1)',
                        tension: 0.4,
                        fill: true
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            display: false
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: false,
                            title: {
                                display: true,
                                text: 'Queue Length (m)'
                            }
                        },
                        x: {
                            title: {
                                display: true,
                                text: 'Minutes'
                            }
                        }
                    }
                }
            });
            
            // Vehicle Classification Chart
            const vehicleCtx = document.getElementById('vehicle-chart').getContext('2d');
            new Chart(vehicleCtx, {
                type: 'doughnut',
                data: {
                    labels: ['Two-Wheelers', 'Cars', 'LCV', 'Buses', 'Trucks'],
                    datasets: [{
                        data: [45, 30, 12, 8, 5],
                        backgroundColor: [
                            '#1a73e8',
                            '#34a853',
                            '#f9ab00',
                            '#ea4335',
                            '#9334e6'
                        ]
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'bottom'
                        }
                    }
                }
            });
            
            // Signal Timing Distribution Chart
            const signalCtx = document.getElementById('signal-chart').getContext('2d');
            new Chart(signalCtx, {
                type: 'bar',
                data: {
                    labels: ['Junction 1', 'Junction 2', 'Junction 3', 'Junction 4', 'Junction 5'],
                    datasets: [{
                        label: 'AI Timing (s)',
                        data: [45, 52, 38, 48, 42],
                        backgroundColor: '#1a73e8'
                    }, {
                        label: 'Fixed Timing (s)',
                        data: [60, 60, 60, 60, 60],
                        backgroundColor: '#ea4335'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'Signal Duration (s)'
                            }
                        }
                    }
                }
            });
        }
        
        // Initialize everything when page loads
        window.onload = function() {
            updateClock();
            initTrafficVisualization();
            initCharts();
            
            // Update clock every second
            setInterval(updateClock, 1000);
            
            // Update KPIs every 5 seconds
            setInterval(updateKPIs, 5000);
            
            // Refresh traffic visualization every 10 seconds
            setInterval(initTrafficVisualization, 10000);
        };
    </script>
</body>
</html>
