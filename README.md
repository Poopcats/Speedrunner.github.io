<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Firebase Speedrun Tracker</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 20px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }
        
        .header {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            color: white;
            padding: 30px;
            text-align: center;
            position: relative;
        }
        
        .header h1 {
            margin: 0;
            font-size: 2.5em;
            font-weight: 700;
            text-shadow: 0 2px 4px rgba(0,0,0,0.3);
        }
        
        .connection-status {
            position: absolute;
            top: 20px;
            right: 20px;
            padding: 8px 16px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 600;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        .status-connected {
            background: rgba(40, 167, 69, 0.2);
            color: #155724;
            border: 2px solid rgba(40, 167, 69, 0.3);
        }
        
        .status-disconnected {
            background: rgba(220, 53, 69, 0.2);
            color: #721c24;
            border: 2px solid rgba(220, 53, 69, 0.3);
        }
        
        .status-dot {
            width: 8px;
            height: 8px;
            border-radius: 50%;
            animation: pulse 2s infinite;
        }
        
        .status-connected .status-dot {
            background: #28a745;
        }
        
        .status-disconnected .status-dot {
            background: #dc3545;
        }
        
        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.5; }
            100% { opacity: 1; }
        }
        
        .tabs {
            display: flex;
            background: #f8f9fa;
            border-bottom: 2px solid #dee2e6;
        }
        
        .tab {
            flex: 1;
            padding: 15px 20px;
            background: #e9ecef;
            cursor: pointer;
            border: none;
            font-size: 16px;
            font-weight: 600;
            transition: all 0.3s ease;
            position: relative;
        }
        
        .tab:hover {
            background: #d1ecf1;
            transform: translateY(-2px);
        }
        
        .tab.active {
            background: white;
            color: #007bff;
            box-shadow: 0 -2px 8px rgba(0,123,255,0.2);
        }
        
        .tab.active::after {
            content: '';
            position: absolute;
            bottom: -2px;
            left: 0;
            right: 0;
            height: 3px;
            background: linear-gradient(90deg, #007bff, #00f2fe);
        }
        
        .sheet {
            display: none;
            padding: 30px;
        }
        
        .sheet.active {
            display: block;
            animation: fadeIn 0.5s ease-in;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
            background: white;
            border-radius: 12px;
            overflow: hidden;
            box-shadow: 0 8px 25px rgba(0,0,0,0.1);
        }
        
        th {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 18px 15px;
            text-align: left;
            font-weight: 700;
            font-size: 14px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        
        td {
            padding: 15px;
            border-bottom: 1px solid #f0f0f0;
            transition: all 0.3s ease;
        }
        
        tr:hover td {
            background-color: #f8f9ff;
            transform: scale(1.01);
        }
        
        tr:last-child td {
            border-bottom: none;
        }
        
        .time {
            font-family: 'Courier New', monospace;
            font-weight: bold;
            color: #28a745;
            background: #f0fff4;
            padding: 8px 12px;
            border-radius: 6px;
            display: inline-block;
            min-width: 80px;
            text-align: center;
        }
        
        .record-holder {
            font-weight: 600;
            color: #495057;
            background: #fff3cd;
            padding: 6px 12px;
            border-radius: 20px;
            display: inline-block;
        }
        
        .player-nathan { background: #e3f2fd; color: #1976d2; }
        .player-simon { background: #f3e5f5; color: #7b1fa2; }
        .player-jordan { background: #e8f5e8; color: #388e3c; }
        
        .add-time-section {
            background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
            padding: 25px;
            border-radius: 12px;
            margin-top: 30px;
            border: 2px dashed #dee2e6;
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.05);
        }
        
        .form-group {
            display: flex;
            gap: 15px;
            align-items: center;
            margin-bottom: 15px;
            flex-wrap: wrap;
        }
        
        .form-group input, .form-group select {
            padding: 12px;
            border: 2px solid #dee2e6;
            border-radius: 8px;
            font-size: 14px;
            transition: all 0.3s ease;
            background: white;
        }
        
        .form-group input:focus, .form-group select:focus {
            outline: none;
            border-color: #007bff;
            box-shadow: 0 0 0 3px rgba(0,123,255,0.1);
            transform: translateY(-1px);
        }
        
        .btn {
            background: linear-gradient(135deg, #28a745 0%, #20c997 100%);
            color: white;
            border: none;
            padding: 12px 25px;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }
        
        .btn::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
            transition: left 0.5s;
        }
        
        .btn:hover::before {
            left: 100%;
        }
        
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 15px rgba(40,167,69,0.3);
        }
        
        .btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            transform: none;
        }
        
        .challenge-name {
            font-weight: 600;
            color: #343a40;
        }
        
        .new-record-row {
            animation: highlightNew 3s ease-in-out;
        }
        
        @keyframes highlightNew {
            0% { background: #d4edda; }
            100% { background: transparent; }
        }
        
        .loading {
            text-align: center;
            padding: 40px;
            color: #6c757d;
        }
        
        .loading::after {
            content: '⏳';
            font-size: 2em;
            animation: spin 2s linear infinite;
        }
        
        @keyframes spin {
            from { transform: rotate(0deg); }
            to { transform: rotate(360deg); }
        }
        
        .error-message {
            background: #f8d7da;
            color: #721c24;
            padding: 15px;
            border-radius: 8px;
            margin: 20px 0;
            border: 1px solid #f5c6cb;
        }
        
        .success-message {
            background: #d4edda;
            color: #155724;
            padding: 15px;
            border-radius: 8px;
            margin: 20px 0;
            border: 1px solid #c3e6cb;
            animation: slideIn 0.5s ease-out;
        }
        
        @keyframes slideIn {
            from { transform: translateX(-100%); opacity: 0; }
            to { transform: translateX(0); opacity: 1; }
        }
        
        @media (max-width: 768px) {
            .tabs {
                flex-direction: column;
            }
            
            .form-group {
                flex-direction: column;
                align-items: stretch;
            }
            
            table, th, td {
                font-size: 12px;
            }
            
            .container {
                margin: 10px;
                border-radius: 10px;
            }
            
            .connection-status {
                position: static;
                margin-top: 10px;
                justify-content: center;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🔥 Firebase Speedrun Tracker 🔥</h1>
            <div id="connection-status" class="connection-status status-disconnected">
                <div class="status-dot"></div>
                <span>Connecting...</span>
            </div>
        </div>
        
        <div class="tabs">
            <button class="tab active" onclick="showSheet('best-times')">🏆 Best Times</button>
            <button class="tab" onclick="showSheet('player-stats')">📊 Player Stats</button>
            <button class="tab" onclick="showSheet('all-times')">📝 All Times</button>
        </div>
        
        <!-- Best Times Sheet -->
        <div id="best-times" class="sheet active">
            <h2>🏆 Current Records</h2>
            <div id="best-times-loading" class="loading">Loading records...</div>
            <table id="best-times-table-container" style="display: none;">
                <thead>
                    <tr>
                        <th>Challenge</th>
                        <th>Best Time</th>
                        <th>Record Holder</th>
                        <th>Date Set</th>
                    </tr>
                </thead>
                <tbody id="best-times-table">
                </tbody>
            </table>
        </div>
        
        <!-- Player Stats Sheet -->
        <div id="player-stats" class="sheet">
            <h2>📊 Player Statistics</h2>
            <div id="player-stats-loading" class="loading">Loading stats...</div>
            <table id="player-stats-table-container" style="display: none;">
                <thead>
                    <tr>
                        <th>Player</th>
                        <th>Challenges Completed</th>
                        <th>Average Time</th>
                        <th>Fastest Time</th>
                        <th>Total Attempts</th>
                    </tr>
                </thead>
                <tbody id="player-stats-table">
                </tbody>
            </table>
        </div>
        
        <!-- All Times Sheet -->
        <div id="all-times" class="sheet">
            <h2>📝 Complete Records</h2>
            
            <div class="add-time-section">
                <h3>➕ Add New Time</h3>
                <div id="add-time-messages"></div>
                <div class="form-group">
                    <select id="challenge-select">
                        <option value="">Select Challenge</option>
                        <option value="Bee sting">Bee sting</option>
                        <option value="Obtain Snowball">Obtain Snowball</option>
                        <option value="Lilypad">Lilypad</option>
                        <option value="Sweet Berry">Sweet Berry</option>
                        <option value="Lantern">Lantern</option>
                        <option value="Brown Mushie">Brown Mushie</option>
                        <option value="Dying Lava">Dying Lava</option>
                        <option value="Punch Cat">Punch Cat</option>
                        <option value="Sugar Cane">Sugar Cane</option>
                        <option value="Mining Fatigue">Mining Fatigue</option>
                        <option value="Flint">Flint</option>
                        <option value="Fox Sword">Fox Sword</option>
                        <option value="Cow Killer Pickaxe">Cow Killer Pickaxe</option>
                        <option value="Killing Chicken with a Shovel">Killing Chicken with a Shovel</option>
                        <option value="Make a penis">Make a penis</option>
                        <option value="Ghast POV 30">Ghast POV 30</option>
                        <option value="Taming Parrot">Taming Parrot</option>
                        <option value="Punch Villager">Punch Villager</option>
                        <option value="Win Game">Win Game</option>
                        <option value="Tame a horse">Tame a horse</option>
                        <option value="Shear a sheep">Shear a sheep</option>
                        <option value="Oxeye Daisy into Water">Oxeye Daisy into Water</option>
                        <option value="Enchanted golden Apple">Enchanted golden Apple</option>
                        <option value="Pink Sheep">Pink Sheep</option>
                        <option value="Ride Pig">Ride Pig</option>
                        <option value="Ring a bell">Ring a bell</option>
                    </select>
                    <select id="player-select">
                        <option value="">Select Player</option>
                        <option value="Nathan">Nathan</option>
                        <option value="Simon">Simon</option>
                        <option value="Jordan">Jordan</option>
                    </select>
                    <input type="text" id="time-input" placeholder="Time (e.g., 1:23.456)" />
                    <button class="btn" id="add-time-btn" onclick="addTime()">Add Time</button>
                </div>
            </div>
            
            <div id="all-times-loading" class="loading">Loading all records...</div>
            <table id="all-times-table-container" style="display: none;">
                <thead>
                    <tr>
                        <th>Challenge</th>
                        <th>Player</th>
                        <th>Time</th>
                        <th>Date Added</th>
                    </tr>
                </thead>
                <tbody id="all-times-table">
                </tbody>
            </table>
        </div>
    </div>

    <!-- Firebase SDK -->
    <script type="module">
        // Import Firebase modules
        import { initializeApp } from 'https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js';
        import { getDatabase, ref, push, onValue, serverTimestamp } from 'https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js';

        // ⚠️ REPLACE WITH YOUR FIREBASE CONFIG
        const firebaseConfig = {
  apiKey: "AIzaSyBATBo8rqs1UJMMafNtCBmsCwnMqeEVJig",
  authDomain: "speedruns-485e3.firebaseapp.com",
  databaseURL: "https://speedruns-485e3-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "speedruns-485e3",
  storageBucket: "speedruns-485e3.firebasestorage.app",
  messagingSenderId: "418429963414",
  appId: "1:418429963414:web:076a65eb6344e5733a06b7",
  measurementId: "G-0XZ1HC988J"
};

        // Initialize Firebase
        let app, database;
        let allTimes = [];
        let isConnected = false;

        try {
            app = initializeApp(firebaseConfig);
            database = getDatabase(app);
            setupRealtimeListener();
        } catch (error) {
            console.error('Firebase initialization error:', error);
            showConnectionError();
        }

        function setupRealtimeListener() {
            const timesRef = ref(database, 'speedrun-times');
            
            onValue(timesRef, (snapshot) => {
                updateConnectionStatus(true);
                const data = snapshot.val();
                
                if (data) {
                    allTimes = Object.entries(data).map(([key, value]) => ({
                        id: key,
                        ...value
                    }));
                } else {
                    // Initialize with demo data if empty
                    initializeDemoData();
                }
                
                updateAllTables();
                hideLoadingIndicators();
            }, (error) => {
                console.error('Firebase read error:', error);
                updateConnectionStatus(false);
                showConnectionError();
            });
        }

        function initializeDemoData() {
            const demoData = [
                { challenge: "Bee sting", player: "Simon", time: "0:03.102", seconds: 3.102 },
                { challenge: "Obtain Snowball", player: "Nathan", time: "0:14.752", seconds: 14.752 },
                { challenge: "Lilypad", player: "Jordan", time: "0:01.202", seconds: 1.202 },
                { challenge: "Lilypad", player: "Simon", time: "0:01.202", seconds: 1.202 },
                { challenge: "Sweet Berry", player: "Nathan", time: "0:00.803", seconds: 0.803 },
                { challenge: "Sugar Cane", player: "Jordan", time: "0:00.454", seconds: 0.454 }
            ];

            demoData.forEach(entry => {
                entry.timestamp = Date.now();
                entry.date = new Date().toLocaleDateString();
                const timesRef = ref(database, 'speedrun-times');
                push(timesRef, entry);
            });
        }

        function updateConnectionStatus(connected) {
            isConnected = connected;
            const statusElement = document.getElementById('connection-status');
            
            if (connected) {
                statusElement.className = 'connection-status status-connected';
                statusElement.innerHTML = '<div class="status-dot"></div><span>Connected</span>';
            } else {
                statusElement.className = 'connection-status status-disconnected';
                statusElement.innerHTML = '<div class="status-dot"></div><span>Disconnected</span>';
            }
        }

        function showConnectionError() {
            const errorDiv = document.createElement('div');
            errorDiv.className = 'error-message';
            errorDiv.innerHTML = `
                <strong>Connection Error:</strong> Please check your Firebase configuration. 
                <br><small>Make sure to replace the firebaseConfig with your actual project settings.</small>
            `;
            document.querySelector('.container').insertBefore(errorDiv, document.querySelector('.tabs'));
        }

        function hideLoadingIndicators() {
            document.getElementById('best-times-loading').style.display = 'none';
            document.getElementById('best-times-table-container').style.display = 'table';
            
            document.getElementById('player-stats-loading').style.display = 'none';
            document.getElementById('player-stats-table-container').style.display = 'table';
            
            document.getElementById('all-times-loading').style.display = 'none';
            document.getElementById('all-times-table-container').style.display = 'table';
        }

        function getBestTimes() {
            const bestTimes = {};
            allTimes.forEach(entry => {
                if (!bestTimes[entry.challenge] || entry.seconds < bestTimes[entry.challenge].seconds) {
                    bestTimes[entry.challenge] = entry;
                }
            });
            return Object.values(bestTimes);
        }

        function getPlayerStats() {
            const stats = {};
            allTimes.forEach(entry => {
                if (!stats[entry.player]) {
                    stats[entry.player] = { times: [], fastest: Infinity, totalAttempts: 0 };
                }
                stats[entry.player].times.push(entry.seconds);
                stats[entry.player].totalAttempts++;
                if (entry.seconds < stats[entry.player].fastest) {
                    stats[entry.player].fastest = entry.seconds;
                }
            });
            
            return Object.entries(stats).map(([player, data]) => ({
                player,
                completed: new Set(allTimes.filter(t => t.player === player).map(t => t.challenge)).size,
                average: data.times.reduce((a, b) => a + b, 0) / data.times.length,
                fastest: data.fastest,
                totalAttempts: data.totalAttempts
            }));
        }

        function formatTime(seconds) {
            const minutes = Math.floor(seconds / 60);
            const remainingSeconds = seconds % 60;
            return `${minutes}:${remainingSeconds.toFixed(3).padStart(6, '0')}`;
        }

        function updateAllTables() {
            updateBestTimesTable();
            updatePlayerStatsTable();
            updateAllTimesTable();
        }

        function updateBestTimesTable() {
            const bestTimes = getBestTimes();
            const table = document.getElementById('best-times-table');
            table.innerHTML = bestTimes.map(entry => `
                <tr>
                    <td class="challenge-name">${entry.challenge}</td>
                    <td><span class="time">${entry.time}</span></td>
                    <td><span class="record-holder player-${entry.player.toLowerCase()}">${entry.player}</span></td>
                    <td>${entry.date || 'N/A'}</td>
                </tr>
            `).join('');
        }

        function updatePlayerStatsTable() {
            const playerStats = getPlayerStats();
            const table = document.getElementById('player-stats-table');
            table.innerHTML = playerStats.map(stat => `
                <tr>
                    <td><span class="record-holder player-${stat.player.toLowerCase()}">${stat.player}</span></td>
                    <td><strong>${stat.completed}</strong></td>
                    <td><span class="time">${formatTime(stat.average)}</span></td>
                    <td><span class="time">${formatTime(stat.fastest)}</span></td>
                    <td><strong>${stat.totalAttempts}</strong></td>
                </tr>
            `).join('');
        }

        function updateAllTimesTable() {
            const table = document.getElementById('all-times-table');
            const sortedTimes = [...allTimes].sort((a, b) => (b.timestamp || 0) - (a.timestamp || 0));
            
            table.innerHTML = sortedTimes.map((entry, index) => `
                <tr ${index < 3 ? 'class="new-record-row"' : ''}>
                    <td class="challenge-name">${entry.challenge}</td>
                    <td><span class="record-holder player-${entry.player.toLowerCase()}">${entry.player}</span></td>
                    <td><span class="time">${entry.time}</span></td>
                    <td>${entry.date || 'N/A'}</td>
                </tr>
            `).join('');
        }

        // Global functions for HTML
        window.showSheet = function(sheetName) {
            document.querySelectorAll('.sheet').forEach(sheet => {
                sheet.classList.remove('active');
            });
            
            document.querySelectorAll('.tab').forEach(tab => {
                tab.classList.remove('active');
            });
            
            document.getElementById(sheetName).classList.add('active');
            event.target.classList.add('active');
        };

        window.addTime = async function() {
            if (!isConnected) {
                showMessage('error', 'Not connected to Firebase. Please check your connection.');
                return;
            }

            const challenge = document.getElementById('challenge-select').value;
            const player = document.getElementById('player-select').value;
            const timeInput = document.getElementById('time-input').value;
            
            if (!challenge || !player || !timeInput) {
                showMessage('error', 'Please fill in all fields');
                return;
            }
            
            // Parse time input
            const timeParts = timeInput.split(':');
            let seconds = 0;
            
            if (timeParts.length === 2) {
                const minutes = parseInt(timeParts[0]);
                const secondsPart = parseFloat(timeParts[1]);
                seconds = minutes * 60 + secondsPart;
            } else {
                seconds = parseFloat(timeInput);
            }
            
            if (isNaN(seconds) || seconds <= 0) {
                showMessage('error', 'Please enter a valid time');
                return;
            }

            const newEntry = {
                challenge,
                player,
                time: timeInput.includes(':') ? timeInput : `0:${timeInput.padStart(6, '0')}`,
                seconds,
                date: new Date().toLocaleDateString(),
                timestamp: serverTimestamp()
            };
            
            try {
                document.getElementById('add-time-btn').disabled = true;
                const timesRef = ref(database, 'speedrun-times');
                await push(timesRef, newEntry);
                
                // Clear form
                document.getElementById('challenge-select').value = '';
                document.getElementById('player-select').value = '';
                document.getElementById('time-input').value = '';
                
                showMessage('success', `🎉 Time added successfully! ${player} completed ${challenge} in ${newEntry.time}`);
                
            } catch (error) {
                console.error('Error adding time:', error);
                showMessage('error', 'Failed to add time. Please try again.');
            } finally {
                document.getElementById('add-time-btn').disabled = false;
            }
        };

        function showMessage(type, message) {
            const messagesDiv = document.getElementById('add-time-messages');
            const messageDiv = document.createElement('div');
            messageDiv.className = type === 'success' ? 'success-message' : 'error-message';
            messageDiv.textContent = message;
            
            messagesDiv.innerHTML = '';
            messagesDiv.appendChild(messageDiv);
            
            setTimeout(() => {
                messageDiv.remove();
            }, 5000);
        }
    </script>
</body>
</html>
