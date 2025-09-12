<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>EFT Rat Run Decider</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
    <style>
        /* --- Basic Setup & Dark Theme --- */
        :root {
            --background-color: #1a1a1a;
            --text-color: #e0e0e0;
            --primary-color: #ffbf00;
            --container-bg: #2c2c2c;
            --border-color: #444;
            --glow-color: rgba(255, 191, 0, 0.8);
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--background-color);
            color: var(--text-color);
            min-height: 100vh;
        }

        .app-container {
            width: 100vw;
            height: 100vh;
            background-color: var(--container-bg);
        }

        .main-layout {
            display: flex;
            height: 100%;
        }


        h1, h2 {
            margin-bottom: 1.5rem;
            color: var(--primary-color);
        }

        /* --- Screen States --- */
        .screen {
            display: none; /* Hidden by default */
        }

        .screen.active {
            display: block;
        }

        /* --- Buttons --- */
        .btn {
            background-color: var(--primary-color);
            color: var(--background-color);
            border: none;
            padding: 1rem 2rem;
            font-size: 1.2rem;
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            transition: transform 0.2s, box-shadow 0.2s;
            margin-top: 1rem;
        }

        .btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 6px 15px rgba(0, 0, 0, 0.4);
        }

        /* --- Decider Screen & Animations --- */
        .decider-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 2rem;
            margin-top: 2rem;
        }

        @media (min-width: 768px) {
            .decider-grid {
                grid-template-columns: 1fr 1fr;
            }
        }

        .decider-list {
            list-style: none;
            padding: 1rem;
            border: 2px solid var(--border-color);
            border-radius: 10px;
            height: 420px; /* Fixed height for consistent layout */
            overflow: hidden;
            position: relative;
        }

        .decider-list-item {
            font-size: 1.5rem;
            padding: 0.5rem;
            border-radius: 5px;
            transition: background-color 0.1s, color 0.1s, box-shadow 0.3s;
        }

        /* Highlight for spinning effect */
        .decider-list-item.highlight {
            background-color: var(--primary-color);
            color: var(--background-color);
        }

        /* Final selection glow effect */
        .decider-list-item.selected {
            color: var(--primary-color);
            box-shadow: 0 0 15px 5px var(--glow-color);
            transform: scale(1.05);
        }

        /* --- Results Screen --- */
        #results-screen h2 {
            margin-bottom: 2rem;
        }
        
        .result-item {
            background-color: #333;
            padding: 1.5rem;
            border-radius: 10px;
            margin-bottom: 1.5rem;
            border-left: 5px solid var(--primary-color);
        }

        .result-item h3 {
            margin-bottom: 0.5rem;
            color: var(--text-color);
            font-size: 1.2rem;
            text-align: left;
        }

        #final-map, #final-kit {
            font-size: 2rem;
            font-weight: bold;
            color: var(--primary-color);
            text-align: left;
        }

        .raid-goal {
            margin-top: 2rem;
            padding: 1rem;
            background-color: #222;
            border-radius: 8px;
        }

        .raid-goal h3 {
            color: var(--primary-color);
            margin-bottom: 0.5rem;
        }

        /* --- Side Navigation Layout --- */
        .side-nav {
            background-color: #222;
            padding: 1rem 0;
            width: 200px;
            flex-shrink: 0; /* Prevent nav from shrinking */
            border-right: 1px solid var(--border-color);
        }

        .content-area {
            padding: 2rem;
            width: 100%;
            overflow-y: auto; /* Add scroll for larger content */
        }

        .tab-link {
            display: block;
            width: 100%;
            background: none;
            border: none;
            color: var(--text-color);
            padding: 1rem 1.5rem;
            font-size: 1.1rem;
            cursor: pointer;
            text-align: left;
            border-left: 4px solid transparent;
            transition: background-color 0.2s, border-color 0.2s;
        }

        .tab-link:hover {
            background-color: #333;
        }

        .tab-link.active {
            color: var(--primary-color);
            border-left-color: var(--primary-color);
            background-color: #2c2c2c;
        }

        .tab-content {
            display: none;
        }

        .tab-content.active {
            display: block;
        }

    </style>
</head>
<body>

    <div class="app-container">
        <div class="main-layout">
            <nav class="side-nav">
                <button class="tab-link active" data-tab="rat-run-decider">Rat Run Decider</button>
                <button class="tab-link" data-tab="quest-helper">Quest Helper</button>
            </nav>
            <main class="content-area">
                <div id="rat-run-decider" class="tab-content active">
                    <!-- Screen 1: Welcome -->
                    <div id="welcome-screen" class="screen active">
                        <h1>EFT Rat Run Decider</h1>
                        <p>Can't decide on your next low-risk raid? Let fate choose for you.</p>
                        <button id="start-btn" class="btn">Start Decider</button>
                    </div>
        
                    <!-- Screen 2: Decider in Progress -->
                    <div id="decider-screen" class="screen">
                        <h2>Deciding your fate...</h2>
                        <div class="decider-grid">
                            <div>
                                <h3>Map</h3>
                                <ul id="map-list" class="decider-list"></ul>
                            </div>
                            <div>
                                <h3>Kit</h3>
                                <ul id="kit-list" class="decider-list"></ul>
                            </div>
                        </div>
                    </div>
        
                    <!-- Screen 3: Results -->
                    <div id="results-screen" class="screen">
                        <h2>Your Next Raid</h2>
                        <div class="decider-grid">
                            <div class="result-item">
                                <h3>MAP:</h3>
                                <p id="final-map"></p>
                            </div>
                            <div class="result-item">
                                <h3>KIT:</h3>
                                <p id="final-kit"></p>
                            </div>
                        </div>
                        <div class="raid-goal">
                            <h3>Your Goal:</h3>
                            <p id="raid-goal-text"></p>
                        </div>
                        <button id="retry-btn" class="btn">Try Again</button>
                    </div>
                </div>
        
                <div id="quest-helper" class="tab-content">
                    <h2>Quest Helper</h2>
                    <p>This feature is coming soon!</p>
                </div>
            </main>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // --- DOM Elements ---
            const welcomeScreen = document.getElementById('welcome-screen');
            const deciderScreen = document.getElementById('decider-screen');
            const resultsScreen = document.getElementById('results-screen');
            const startBtn = document.getElementById('start-btn');
            const retryBtn = document.getElementById('retry-btn');
            const mapList = document.getElementById('map-list');
            const kitList = document.getElementById('kit-list');
            const finalMapEl = document.getElementById('final-map');
            const finalKitEl = document.getElementById('final-kit');
            const raidGoalEl = document.getElementById('raid-goal-text');

            // --- Data ---
            const maps = ['Customs', 'Factory', 'Woods', 'Interchange', 'Shoreline', 'Reserve', 'The Lab', 'Lighthouse', 'Streets of Tarkov'];
            const kits = ['Pistol Run', 'Hatchet Run', 'Shotgun Scavenger', 'Mosina Mania', 'SKS Survivalist'];
            const goals = {
                'Pistol Run': [
                    'Get at least two PMC kills.',
                    'Kill a Scav and extract with their primary weapon and rig.',
                    'Extract with a pistol that isn\'t yours.',
                    'Complete a quest objective.',
                    'Fill your secure container with valuable loot (over 100k Rouble value).'
                ],
                'Hatchet Run': [
                    'Find and extract with one valuable item (e.g., a motor, water filter) from a duffle bag or hidden stash.',
                    'Loot a weapon from a Scav and get one kill with it.',
                    'Reach a high-traffic area (e.g., Dorms on Customs, Resort on Shoreline) and escape without being seen.',
                    'Find and extract with a full set of gear (rig, backpack, armor).',
                    'Extract with at least 50,000 roubles worth of loot.'
                ],
                'Shotgun Scavenger': [
                    'Extract with a full rig of loot found only on Scavs.',
                    'Kill a PMC with a headshot using a shotgun.',
                    'Use only buckshot and aim for the legs. Extract with at least one PMC kill.',
                    'Find and extract with three different types of shotgun shells.',
                    'Extract with a PMC\'s dog tag.'
                ],
                'Mosina Mania': [
                    'Land a headshot on a PMC or a Scav Boss from over 75 meters.',
                    'Get two kills without reloading (using the internal magazine).',
                    'Extract with a Mosin that has at least 2 mods you added in-raid.',
                    'Kill a Scav and take their weapon, using it for the rest of the raid.',
                    'Get a kill using only iron sights.'
                ],
                'SKS Survivalist': [
                    'Extract with at least two items needed for a hideout upgrade.',
                    'Go the entire raid without sprinting unless shot at.',
                    'Only loot items that can be used for crafting.',
                    'Get a kill from over 100 meters away.',
                    'Extract with a full inventory of food and water found in-raid.'
                ]
            };

            // --- Functions ---

            /**
             * Populates the list elements in the DOM.
             */
            function populateLists() {
                mapList.innerHTML = maps.map(map => `<li class="decider-list-item">${map}</li>`).join('');
                kitList.innerHTML = kits.map(kit => `<li class="decider-list-item">${kit}</li>`).join('');
            }

            /**
             * Switches the visible screen.
             * @param {string} screenId - The ID of the screen to make active.
             */
            function showScreen(screenId) {
                document.querySelectorAll('.screen').forEach(screen => {
                    screen.classList.remove('active');
                });
                document.getElementById(screenId).classList.add('active');
            }

            /**
             * Core logic for the spinning and random selection animation.
             * @param {HTMLElement} listElement - The ul element to animate.
             * @param {string[]} dataArray - The array of strings for the list.
             * @param {number} duration - Total duration of the spinning effect.
             * @returns {Promise<string>} A promise that resolves with the selected item.
             */
            function runDecider(listElement, dataArray, duration) {
                return new Promise(resolve => {
                    const items = listElement.getElementsByTagName('li');
                    const intervalDuration = 100; // How fast the highlight cycles
                    let highlightIndex = 0;

                    // Start the "spinning" visual effect
                    const spinInterval = setInterval(() => {
                        // Remove highlight from the previous item
                        items[highlightIndex].classList.remove('highlight');
                        
                        // Move to the next item, looping back to the start
                        highlightIndex = (highlightIndex + 1) % items.length;
                        
                        // Add highlight to the current item
                        items[highlightIndex].classList.add('highlight');
                    }, intervalDuration);

                    // Stop the spinning and select the final item
                    setTimeout(() => {
                        clearInterval(spinInterval);

                        // Pick a random item
                        const randomIndex = Math.floor(Math.random() * dataArray.length);
                        const selectedValue = dataArray[randomIndex];

                        // Clear all highlights
                        for (let item of items) {
                            item.classList.remove('highlight');
                        }

                        // Apply final 'selected' class to the chosen item
                        items[randomIndex].classList.add('selected');
                        
                        resolve(selectedValue);
                    }, duration);
                });
            }

            /**
             * Resets the UI to the initial state.
             */
            function reset() {
                // Remove 'selected' classes
                document.querySelectorAll('.decider-list-item.selected').forEach(el => {
                    el.classList.remove('selected');
                });
                showScreen('welcome-screen');
            }

            // --- Event Listeners ---

            startBtn.addEventListener('click', async () => {
                showScreen('decider-screen');

                // Run both deciders simultaneously
                const [selectedMap, selectedKit] = await Promise.all([
                    runDecider(mapList, maps, 3000), // 3-second spin
                    runDecider(kitList, kits, 3000)
                ]);

                // Short delay to appreciate the selection before showing results
                setTimeout(() => {
                    finalMapEl.textContent = selectedMap;
                    finalKitEl.textContent = selectedKit;
                    
                    // Randomly select a goal from the chosen kit's list
                    const kitGoals = goals[selectedKit];
                    if (kitGoals && kitGoals.length > 0) {
                        const randomGoal = kitGoals[Math.floor(Math.random() * kitGoals.length)];
                        raidGoalEl.textContent = randomGoal;
                    } else {
                        raidGoalEl.textContent = 'Survive and extract!';
                    }

                    showScreen('results-screen');
                }, 1000);
            });

            retryBtn.addEventListener('click', reset);

            // --- Initial Setup ---
            populateLists();
            showScreen('welcome-screen');

            // --- Tab Functionality ---
            const tabs = document.querySelectorAll('.tab-link');
            const tabContents = document.querySelectorAll('.tab-content');

            tabs.forEach(tab => {
                tab.addEventListener('click', () => {
                    const target = document.getElementById(tab.dataset.tab);

                    tabs.forEach(t => t.classList.remove('active'));
                    tab.classList.add('active');

                    tabContents.forEach(content => content.classList.remove('active'));
                    target.classList.add('active');
                });
            });
        });
    </script>

</body>
</html>

