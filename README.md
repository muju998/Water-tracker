# Water-tracker
<!-- ... existing code ... -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; }
    </style>
</head>
<body class="bg-blue-50 min-h-screen p-4 md:p-8">

    <div id="app" class="max-w-md mx-auto bg-white rounded-3xl shadow-xl overflow-hidden">
        
        <!-- Setup Screen -->
<!-- ... existing code ... -->
        <!-- Tracker Screen -->
        <div id="tracker-screen" class="hidden">
            <div class="bg-blue-600 p-6 text-white text-center">
                <h1 class="text-2xl font-bold">HydratePro</h1>
                <p class="text-blue-100 text-sm">Keep your body balanced</p>
            </div>

            <div class="p-6">
                <!-- Navigation -->
                <div class="flex border-b mb-6">
                    <button onclick="showTab('tracker')" class="flex-1 py-2 font-bold text-blue-600 border-b-2 border-blue-600">Track</button>
                    <button onclick="showTab('history')" class="flex-1 py-2 font-bold text-gray-400">History</button>
                </div>

                <div id="tab-tracker">
                    <div class="flex justify-between items-center mb-6">
<!-- ... existing code ... -->
                        <div id="badges" class="flex gap-2 flex-wrap">
                            <span id="badge-start" class="px-3 py-1 bg-gray-100 text-gray-400 rounded-full text-xs font-bold transition">🚀 Started</span>
                            <span id="badge-half" class="px-3 py-1 bg-gray-100 text-gray-400 rounded-full text-xs font-bold transition">💧 50%</span>
                            <span id="badge-goal" class="px-3 py-1 bg-gray-100 text-gray-400 rounded-full text-xs font-bold transition">🏆 Goal Reached</span>
                        </div>
                    </div>

                    <div class="border-t pt-6">
                        <h2 class="text-lg font-bold mb-4 text-gray-800">Why Hydrate?</h2>
                        <ul class="space-y-3 text-sm text-gray-600">
                            <li class="flex items-start">✅ <span class="ml-2"><strong>Energy:</strong> Prevents brain fog and fatigue.</span></li>
                            <li class="flex items-start">✅ <span class="ml-2"><strong>Digestion:</strong> Aids in nutrient absorption.</span></li>
                            <li class="flex items-start">✅ <span class="ml-2"><strong>Skin:</strong> Keeps your complexion clear.</span></li>
                            <li class="flex items-start">✅ <span class="ml-2"><strong>Joints:</strong> Maintains lubrication for movement.</span></li>
                        </ul>
                    </div>
                </div>

                <!-- History Tab -->
                <div id="tab-history" class="hidden">
                    <canvas id="historyChart" class="w-full"></canvas>
                </div>
            </div>
        </div>
    </div>

    <script>
        let consumed = parseInt(localStorage.getItem('water_consumed')) || 0;
        let goal = parseInt(localStorage.getItem('water_goal')) || 2000;
        let setupComplete = localStorage.getItem('setup_complete') === 'true';
        let history = JSON.parse(localStorage.getItem('water_history')) || [0,0,0,0,0,0,0];

        window.onload = () => {
            if (setupComplete) {
                document.getElementById('setup-screen').classList.add('hidden');
                document.getElementById('tracker-screen').classList.remove('hidden');
                document.getElementById('goal-display').innerText = `${goal} ml`;
                updateUI();
                initChart();
            }
        };

        function showTab(tab) {
            document.getElementById('tab-tracker').classList.toggle('hidden', tab !== 'tracker');
            document.getElementById('tab-history').classList.toggle('hidden', tab !== 'history');
        }

        let myChart;
        function initChart() {
            const ctx = document.getElementById('historyChart').getContext('2d');
            myChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
                    datasets: [{
                        label: 'Water (ml)',
                        data: history,
                        backgroundColor: '#3b82f6',
                        borderRadius: 8
                    }]
                },
                options: { responsive: true }
            });
        }

        function addWater(amount) {
            consumed += amount;
            history[6] = consumed; // Today's entry
            localStorage.setItem('water_consumed', consumed);
            localStorage.setItem('water_history', JSON.stringify(history));
            updateUI();
            if (myChart) {
                myChart.data.datasets[0].data = history;
                myChart.update();
            }
        }

        function calculateGoal() {
            const gender = document.getElementById('gender').value;
            const weight = parseFloat(document.getElementById('weight').value);
            
            if (!weight || weight < 30) {
                return;
            }

            goal = Math.round(weight * 35);
            localStorage.setItem('water_goal', goal);
            localStorage.setItem('setup_complete', 'true');
            
            document.getElementById('goal-display').innerText = `${goal} ml`;
            document.getElementById('setup-screen').classList.add('hidden');
            document.getElementById('tracker-screen').classList.remove('hidden');
            updateUI();
            initChart();
        }
// ... existing code ...
        function updateUI() {
            const percentage = Math.min((consumed / goal) * 100, 100);
            document.getElementById('consumed').innerText = `${consumed} ml`;
            document.getElementById('progress').style.width = `${percentage}%`;
            
            // Badge styling
            document.getElementById('badge-start').className = consumed > 0 ? "px-3 py-1 bg-blue-500 text-white rounded-full text-xs font-bold transition" : "px-3 py-1 bg-gray-100 text-gray-400 rounded-full text-xs font-bold transition";
            document.getElementById('badge-half').className = percentage >= 50 ? "px-3 py-1 bg-blue-500 text-white rounded-full text-xs font-bold transition" : "px-3 py-1 bg-gray-100 text-gray-400 rounded-full text-xs font-bold transition";
            document.getElementById('badge-goal').className = percentage >= 100 ? "px-3 py-1 bg-green-500 text-white rounded-full text-xs font-bold transition" : "px-3 py-1 bg-gray-100 text-gray-400 rounded-full text-xs font-bold transition";
        }
// ... existing code ...
    </script>
</body>
</html>
