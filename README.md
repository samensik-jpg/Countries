# Countries
for Grade 6
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chalkboard World Map: Match the People!</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Caveat:wght@600;700&family=Fredoka:wght@500;600;700&display=swap');

        * {
            box-sizing: border-box;
            user-select: none;
            -webkit-user-select: none;
        }

        body {
            margin: 0;
            padding: 0;
            width: 100vw;
            height: 100vh;
            background: #111;
            font-family: 'Fredoka', cursive, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
        }

        /* Blackboard Outer Frame */
        .board-container {
            width: 96vw;
            height: 94vh;
            background: #233329;
            border: 18px solid #6d4220;
            border-radius: 20px;
            box-shadow: 0 0 0 4px #3d2310, inset 0 0 80px rgba(0, 0, 0, 0.8), 0 15px 35px rgba(0,0,0,0.6);
            position: relative;
            display: flex;
            flex-direction: column;
            padding: 15px;
            overflow: hidden;
        }

        /* Chalkboard Texture Effect */
        .board-container::before {
            content: '';
            position: absolute;
            top: 0; left: 0; right: 0; bottom: 0;
            background: radial-gradient(circle, rgba(255,255,255,0.03) 0%, rgba(0,0,0,0.25) 100%);
            pointer-events: none;
            z-index: 1;
        }

        /* Header Title */
        .header {
            text-align: center;
            color: #fff;
            font-family: 'Caveat', cursive;
            font-size: 2.5rem;
            margin-bottom: 5px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.5), 0 0 8px rgba(255,255,255,0.3);
            z-index: 2;
        }

        /* Game Layout */
        .game-area {
            flex: 1;
            display: flex;
            gap: 15px;
            position: relative;
            z-index: 2;
            height: calc(100% - 60px);
        }

        /* Left Side: Drag Cards Dock */
        .dock {
            width: 240px;
            background: rgba(0, 0, 0, 0.25);
            border: 2px dashed rgba(255, 255, 255, 0.3);
            border-radius: 15px;
            padding: 10px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            overflow-y: auto;
            scrollbar-width: thin;
            scrollbar-color: #557c65 transparent;
        }

        /* Person Draggable Item */
        .person-card {
            width: 100%;
            height: 90px;
            background: #fff;
            border-radius: 12px;
            padding: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: grab;
            box-shadow: 0 4px 8px rgba(0,0,0,0.3);
            transition: transform 0.2s, box-shadow 0.2s, opacity 0.2s;
            position: relative;
        }

        .person-card:hover {
            transform: translateY(-3px) scale(1.02);
            box-shadow: 0 8px 16px rgba(0,0,0,0.4);
        }

        .person-card:active {
            cursor: grabbing;
        }

        .person-card img {
            max-width: 100%;
            max-height: 100%;
            object-fit: contain;
            border-radius: 8px;
            filter: drop-shadow(0px 2px 4px rgba(0,0,0,0.2));
        }

        .person-card.dragging {
            opacity: 0.4;
        }

        .person-card.matched {
            display: none; /* Hide from dock when matched */
        }

        /* Right Side: Map Container */
        .map-wrapper {
            flex: 1;
            position: relative;
            background: rgba(255, 255, 255, 0.92);
            border-radius: 15px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.4);
            overflow: hidden;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .map-wrapper img.world-map-img {
            width: 100%;
            height: 100%;
            object-fit: contain;
            pointer-events: none;
        }

        /* Drop Zones (Positioned absolutely over map percentage) */
        .drop-zone {
            position: absolute;
            width: 85px;
            height: 85px;
            border: 2px dashed rgba(40, 100, 240, 0.6);
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.35);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            transform: translate(-50%, -50%);
            transition: all 0.2s ease;
            cursor: pointer;
        }

        .drop-zone::after {
            content: attr(data-country);
            position: absolute;
            bottom: -22px;
            font-size: 0.75rem;
            font-weight: 700;
            color: #111;
            background: rgba(255, 255, 255, 0.9);
            padding: 2px 8px;
            border-radius: 10px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.2);
            white-space: nowrap;
        }

        .drop-zone.hovered {
            background: rgba(76, 175, 80, 0.5);
            border-color: #2e7d32;
            transform: translate(-50%, -50%) scale(1.15);
        }

        .drop-zone.correct {
            border: 3px solid #2e7d32;
            background: rgba(255, 255, 255, 0.9);
            box-shadow: 0 0 15px #4caf50;
            animation: pulse 0.5s ease;
        }

        .drop-zone.correct img {
            max-width: 80%;
            max-height: 80%;
            object-fit: contain;
            border-radius: 50%;
        }

        .drop-zone.correct::before {
            content: '✓';
            position: absolute;
            top: -8px;
            right: -8px;
            background: #4caf50;
            color: white;
            width: 24px;
            height: 24px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 14px;
            font-weight: bold;
            box-shadow: 0 2px 5px rgba(0,0,0,0.3);
            z-index: 5;
        }

        @keyframes pulse {
            0% { transform: translate(-50%, -50%) scale(1); }
            50% { transform: translate(-50%, -50%) scale(1.3); }
            100% { transform: translate(-50%, -50%) scale(1); }
        }

        /* Victory Popup Overlay */
        .victory-modal {
            position: absolute;
            top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0, 0, 0, 0.85);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 100;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.5s ease;
        }

        .victory-modal.show {
            opacity: 1;
            pointer-events: auto;
        }

        .victory-modal h1 {
            color: #ffeb3b;
            font-size: 3.5rem;
            margin: 0;
            font-family: 'Caveat', cursive;
            text-shadow: 0 0 20px rgba(255,235,59,0.6);
        }

        .victory-modal p {
            color: #fff;
            font-size: 1.5rem;
            margin: 15px 0 25px 0;
        }

        .btn-restart {
            background: #4caf50;
            color: white;
            border: none;
            padding: 12px 30px;
            font-size: 1.2rem;
            font-weight: bold;
            border-radius: 30px;
            cursor: pointer;
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
            transition: background 0.2s, transform 0.2s;
        }

        .btn-restart:hover {
            background: #45a049;
            transform: scale(1.05);
        }

        canvas#confetti {
            position: absolute;
            top: 0; left: 0;
            width: 100%; height: 100%;
            pointer-events: none;
            z-index: 101;
        }
    </style>
</head>
<body>

    <div class="board-container">
        <div class="header">✏️ World Map Challenge: Match the People to Their Home Country! 🌍</div>

        <div class="game-area">
            <!-- Left Panel: Cards Dock -->
            <div class="dock" id="dock"></div>

            <!-- Right Panel: Map Container -->
            <div class="map-wrapper" id="mapWrapper">
                <img src="https://media.istockphoto.com/id/1366741362/zh/%E5%90%91%E9%87%8F/world-map-vector-image-isolated-on-white-background.jpg?s=612x612&w=0&k=20&c=5XUXTNQm2P1hhhZunFDhgjbmPkNYUCXhF5-yabkym9w=" class="world-map-img" alt="World Map">

                <!-- Target Drop Zones (% coordinates based on map image layout) -->
                <div class="drop-zone" data-country="USA" style="top: 36%; left: 21%;"></div>
                <div class="drop-zone" data-country="Spain" style="top: 35%; left: 47.5%;"></div>
                <div class="drop-zone" data-country="UK" style="top: 27%; left: 46%;"></div>
                <div class="drop-zone" data-country="India" style="top: 48%; left: 68%;"></div>
                <div class="drop-zone" data-country="Taiwan" style="top: 47%; left: 83%;"></div>
                <div class="drop-zone" data-country="Japan" style="top: 36%; left: 86.5%;"></div>
                <div class="drop-zone" data-country="Singapore" style="top: 57.5%; left: 76.5%;"></div>
                <div class="drop-zone" data-country="Australia" style="top: 73%; left: 85%;"></div>
            </div>
        </div>

        <!-- Completion Modal -->
        <div class="victory-modal" id="victoryModal">
            <h1>🎉 Spectacular Job! 🎉</h1>
            <p>You matched everyone correctly to their home country!</p>
            <button class="btn-restart" onclick="resetGame()">Play Again</button>
        </div>
        <canvas id="confetti"></canvas>
    </div>

    <script>
        // Data structure for the people and their image sources
        const peopleData = [
            { id: 'Australia', src: 'https://encrypted-tbn0.gstatic.com/images/q=tbn:ANd9GcT-0dckcyzMCcWKbCp2lGKhcQtOz7gWKgVaxdHOXPJfmZk4SKnvbDUhRSc&s=10' },
            { id: 'India', src: 'https://encrypted-tbn0.gstatic.com/images/q=tbn:ANd9GcSSm7MgY6MvSvzBGXFxzIO2yNzjx2pAO1CeJomHCPU6_RTeoCsMtnZystA&s=10' },
            { id: 'Japan', src: 'https://png.pngtree.com/png-clipart/20250124/original/pngtree-cartoon-boy-holding-japan-s-flag-dressed-casually-with-a-proud-png-image_20019920.png' },
            { id: 'Singapore', src: 'https://img.magnific.com/premium-photo/child-holding-singapore-flag-illustration_53876-1206206.jpg?semt=ais_hybrid&w=740&q=80' },
            { id: 'Spain', src: 'https://png.pngtree.com/png-clipart/20250215/original/pngtree-boy-holding-spanish-flag-in-school-uniform-png-image_19171710.png' },
            { id: 'Taiwan', src: 'https://t3.ftcdn.net/jpg/06/00/15/56/360_F_600155646_L4WbJ1kdlgivitdONEOc3RCc9dxyLQeo.jpg' },
            { id: 'UK', src: 'https://thumbs.dreamstime.com/b/proud-to-be-british-girl-little-holding-flag-47012731.jpg' },
            { id: 'USA', src: 'https://png.pngtree.com/png-clipart/20240723/original/pngtree-cute-cartoon-boy-holding-the-flag-of-usa-png-image_15615546.png' }
        ];

        let matchedCount = 0;
        let draggedCard = null;

        // Audio Synthesis (Web Audio API)
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        function playSuccessSound() {
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
            const now = audioCtx.currentTime;
            
            // Major Triad Arpeggio Chime (C5, E5, G5, C6)
            const notes = [523.25, 659.25, 783.99, 1046.50];
            notes.forEach((freq, idx) => {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(freq, now + idx * 0.08);
                
                gain.gain.setValueAtTime(0, now + idx * 0.08);
                gain.gain.linearRampToValueAtTime(0.2, now + idx * 0.08 + 0.02);
                gain.gain.exponentialRampToValueAtTime(0.001, now + idx * 0.08 + 0.35);
                
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                
                osc.start(now + idx * 0.08);
                osc.stop(now + idx * 0.08 + 0.4);
            });
        }

        function playVictoryFanfare() {
            if (audioCtx.state === 'suspended') audioCtx.resume();
            const now = audioCtx.currentTime;
            const fanfare = [
                { f: 523.25, t: 0, d: 0.15 },
                { f: 659.25, t: 0.15, d: 0.15 },
                { f: 783.99, t: 0.30, d: 0.15 },
                { f: 1046.50, t: 0.45, d: 0.6 }
            ];

            fanfare.forEach(note => {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(note.f, now + note.t);
                gain.gain.setValueAtTime(0.25, now + note.t);
                gain.gain.exponentialRampToValueAtTime(0.001, now + note.t + note.d);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now + note.t);
                osc.stop(now + note.t + note.d);
            });
        }

        // Initialize Dock Items
        function initGame() {
            const dock = document.getElementById('dock');
            dock.innerHTML = '';
            matchedCount = 0;
            document.getElementById('victoryModal').classList.remove('show');

            // Reset drop zones
            document.querySelectorAll('.drop-zone').forEach(zone => {
                zone.classList.remove('correct', 'hovered');
                zone.innerHTML = '';
            });

            // Shuffle cards
            const shuffled = [...peopleData].sort(() => Math.random() - 0.5);

            shuffled.forEach(person => {
                const card = document.createElement('div');
                card.classList.add('person-card');
                card.setAttribute('draggable', 'true');
                card.setAttribute('data-country', person.id);

                const img = document.createElement('img');
                img.src = person.src;
                img.alt = person.id;
                card.appendChild(img);

                // Drag Events
                card.addEventListener('dragstart', (e) => {
                    draggedCard = card;
                    card.classList.add('dragging');
                    e.dataTransfer.setData('text/plain', person.id);
                });

                card.addEventListener('dragend', () => {
                    card.classList.remove('dragging');
                    draggedCard = null;
                });

                dock.appendChild(card);
            });
        }

        // Set up Drop Zones
        const dropZones = document.querySelectorAll('.drop-zone');

        dropZones.forEach(zone => {
            zone.addEventListener('dragover', (e) => {
                e.preventDefault();
                if (!zone.classList.contains('correct')) {
                    zone.classList.add('hovered');
                }
            });

            zone.addEventListener('dragleave', () => {
                zone.classList.remove('hovered');
            });

            zone.addEventListener('drop', (e) => {
                e.preventDefault();
                zone.classList.remove('hovered');

                const country = e.dataTransfer.getData('text/plain');
                const targetCountry = zone.getAttribute('data-country');

                if (country === targetCountry && !zone.classList.contains('correct')) {
                    // Correct Match
                    zone.classList.add('correct');
                    
                    // Clone image inside zone
                    if (draggedCard) {
                        const imgClone = draggedCard.querySelector('img').cloneNode(true);
                        zone.appendChild(imgClone);
                        draggedCard.classList.add('matched');
                    }

                    playSuccessSound();
                    matchedCount++;

                    // Check Win Condition
                    if (matchedCount === peopleData.length) {
                        setTimeout(() => {
                            playVictoryFanfare();
                            document.getElementById('victoryModal').classList.add('show');
                            launchConfetti();
                        }, 600);
                    }
                }
            });
        });

        function resetGame() {
            initGame();
        }

        // Simple Canvas Confetti
        function launchConfetti() {
            const canvas = document.getElementById('confetti');
            const ctx = canvas.getContext('2d');
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;

            const particles = [];
            const colors = ['#ffeb3b', '#4caf50', '#2196f3', '#e91e63', '#9c27b0'];

            for (let i = 0; i < 120; i++) {
                particles.push({
                    x: canvas.width / 2,
                    y: canvas.height / 2,
                    vx: (Math.random() - 0.5) * 12,
                    vy: (Math.random() - 0.7) * 12,
                    color: colors[Math.floor(Math.random() * colors.length)],
                    size: Math.random() * 8 + 4,
                    life: 100
                });
            }

            function animate() {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                let active = false;

                particles.forEach(p => {
                    if (p.life > 0) {
                        active = true;
                        p.x += p.vx;
                        p.y += p.vy;
                        p.vy += 0.2; // Gravity
                        p.life--;
                        ctx.fillStyle = p.color;
                        ctx.fillRect(p.x, p.y, p.size, p.size);
                    }
                });

                if (active) requestAnimationFrame(animate);
            }
            animate();
        }

        // Launch on Start
        window.addEventListener('DOMContentLoaded', initGame);
    </script>
</body>
</html>
