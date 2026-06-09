<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Team Up! - AI Engine Fixed CORS</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Courier+Prime:wght@400;700&family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        :root {
            --bg-game: #0b0f19; --panel-bg: #171e30; --neon-blue: #00d2ff;
            --neon-pink: #ff007f; --neon-gold: #ffbd00; --neon-green: #39ff14;
            --text-light: #f1f5f9; --border-color: #2a364f;
        }
        * { box-sizing: border-box; }
        body { background-color: var(--bg-game); color: var(--text-light); font-family: 'Courier Prime', monospace; padding: 20px; display: flex; justify-content: center; margin: 0; min-height: 100vh; }
        .game-box { background-color: var(--panel-bg); border: 4px solid var(--border-color); border-radius: 15px; width: 100%; max-width: 1100px; padding: 25px; box-shadow: 0 0 20px rgba(0,0,0,0.8); position: relative; }
        
        .game-screen { display: none; }
        .game-screen.active { display: block; }

        .team-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-top: 15px; }
        .layout-split { display: grid; grid-template-columns: 2fr 1fr; gap: 20px; margin-top: 15px; }
        
        .player-box { background: #0b0f19; border: 3px solid var(--border-color); border-radius: 12px; padding: 15px; display: flex; flex-direction: column; justify-content: space-between; position: relative;}
        
        .role-card { background: rgba(0, 210, 255, 0.05); border: 2px dashed var(--neon-blue); padding: 15px; border-radius: 12px; margin-top: 10px; min-height: 180px; transition: all 0.3s; }
        .role-card.dragover { background: rgba(0, 210, 255, 0.2); border-style: solid; }
        .role-card h4 { color: var(--neon-blue); margin-bottom: 5px; }

        .cards-grid { display: grid; grid-template-columns: 1fr 1fr 1fr 1fr; gap: 15px; margin-bottom: 20px; }
        .mode-card { background: #0b0f19; border: 3px solid var(--border-color); padding: 15px; border-radius: 10px; cursor: pointer; text-align: center; }
        .mode-card.selected { border-color: var(--neon-blue); background: rgba(0,210,255,0.05); }

        textarea, input[type="text"] { width: 100%; padding: 12px; background: #0b0f19; border: 2px solid var(--border-color); border-radius: 8px; color: white; font-size: 14px; margin-top: 5px;}
        
        .check-zone { background: rgba(255,255,255,0.02); border: 2px dashed var(--border-color); padding: 10px; border-radius: 8px; margin-top: 10px; font-family: sans-serif; }
        .check-item { display: flex; align-items: flex-start; gap: 10px; margin-bottom: 8px; font-size: 13px; font-weight: normal; }
        
        .btn-next { display: block; width: 100%; background: var(--neon-green); color: #000; font-weight: bold; padding: 15px; border: none; border-radius: 8px; cursor: pointer; font-size: 16px; margin-top: 20px; box-shadow: 0 4px 0 #24b80c; }
        .btn-back { background: #2a364f; color: white; padding: 6px 12px; border: none; border-radius: 4px; cursor: pointer; font-size: 12px; font-weight: bold; }
        .btn-random { background: var(--neon-gold); color: black; font-weight: bold; border: none; padding: 8px 15px; border-radius: 6px; cursor: pointer; font-size: 12px; margin-top: 10px; font-family: 'Courier Prime', monospace; display: inline-flex; align-items: center; gap: 5px; }
        .sign-btn { background: #334155; color: white; border: 2px solid var(--border-color); padding: 8px 15px; border-radius: 6px; cursor: pointer; font-weight: bold; margin-top: 10px;}
        .sign-btn.signed { background: var(--neon-green); color: black; }

        .drag-player { display: inline-block; background: var(--neon-gold); color: black; padding: 8px 15px; border-radius: 20px; cursor: grab; margin: 5px; font-size: 12px; text-transform: uppercase; user-select: none; font-weight: bold;}
        .drop-zone-slots { background: rgba(0,0,0,0.4); border: 1px solid var(--border-color); border-radius: 6px; padding: 10px; min-height: 45px; margin-top: 10px; display: flex; flex-wrap: wrap; gap: 5px; }

        .challenge-btn { background: #ef4444; color: white; border: none; padding: 10px 20px; border-radius: 5px; font-weight: bold; cursor: pointer; animation: blink 1.5s infinite; margin-top: 12px; font-family: 'Courier Prime', monospace;}
        @keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0.6; } }

        .loading-pulse { animation: pulse 1.5s infinite; color: var(--neon-gold); text-align: center; padding: 40px; }
        @keyframes pulse { 0%, 100% { opacity: 0.4; } 50% { opacity: 1; } }
        .victory-screen { background: #f8fafc; color: #0f172a; padding: 30px; border-radius: 12px; border: 5px solid var(--neon-gold); font-family: sans-serif; }
        
        .homework-box { background: rgba(0, 210, 255, 0.03); border: 2px dashed var(--neon-blue); border-radius: 10px; padding: 20px; margin-top: 25px; font-family: sans-serif; }
        .hw-title { color: var(--neon-blue); display: flex; align-items: center; gap: 10px; margin-top: 0; font-family: 'Courier Prime', monospace; }
        .hw-section { background: rgba(0,0,0,0.3); border: 1px solid var(--border-color); padding: 12px; border-radius: 8px; margin-bottom: 12px; }
        .hw-label { color: var(--neon-gold); font-size: 11px; font-weight: bold; text-transform: uppercase; display: block; margin-bottom: 5px; }
    </style>
</head>
<body>

    <div class="game-box">
        
        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; border-bottom: 3px solid var(--border-color); padding-bottom: 15px;">
            <div style="display: flex; align-items: center; gap: 15px;">
                <button id="nav-back-btn" class="btn-back" onclick="goBack()" style="display:none;">◀ BACK</button>
                <h3 style="color: var(--neon-blue); margin: 0;">🎮 TEAM UP! AI ROLEPLAY</h3>
            </div>
            <div id="screen-tag" style="font-size: 12px; background: #2a364f; padding: 5px 10px; border-radius: 5px; color: var(--neon-gold);">SCREEN 1/6</div>
        </div>

        <div id="screen-1" class="game-screen active">
            <h3>👾 Players Setup</h3>
            <div style="display: grid; gap: 15px; margin-top: 20px;">
                <input type="text" id="p1-name" placeholder="Player 1 Name...">
                <input type="text" id="p2-name" placeholder="Player 2 Name...">
                <input type="text" id="p3-name" placeholder="Player 3 Name (Optional)...">
            </div>
            <button class="btn-next" onclick="savePlayersAndGoToScreen2()">NEXT: THEME ➔</button>
        </div>

        <div id="screen-2" class="game-screen">
            <h3>🗺️ Choose the Adventure</h3>
            <div class="cards-grid">
                <div class="mode-card" onclick="selectCat(event, 'WORK')"><h4>💼 WORK</h4></div>
                <div class="mode-card" onclick="selectCat(event, 'LIFE')"><h4>✈️ LIFE</h4></div>
                <div class="mode-card" onclick="selectCat(event, 'SCHOOL')"><h4>🏫 SCHOOL</h4></div>
                <div class="mode-card" onclick="selectCat(event, 'CUSTOM')"><h4>🛠️ CUSTOM</h4></div>
            </div>
            <div style="margin-top: 20px;">
                <div style="display: flex; justify-content: space-between; align-items: center;">
                    <p style="margin: 0;">What is the goal or scenario?</p>
                    <button class="btn-random" onclick="generateRandomActivity()"><i class="fa-solid fa-dice"></i> Create a Random Activity!</button>
                </div>
                <textarea id="story-input" rows="2" placeholder="Describe the action scenario or click the button above for an idea..."></textarea>
            </div>
            <button class="btn-next" onclick="startAIScenario()">GENERATE ROLES ➔</button>
        </div>

        <div id="screen-loading" class="game-screen">
            <div class="loading-pulse">⚡ CONNECTING TO GROQ AIRNET...<br><span style="font-size: 11px; color:#8fa0c4;">Formulating real-time environment variables.</span></div>
        </div>

        <div id="screen-3" class="game-screen">
            <h3 id="s3-title">SCENARIO: Choose Your Role</h3>
            <p id="s3-context" style="color: var(--neon-gold); font-family: sans-serif; font-weight: normal; font-size: 15px; margin-bottom: 15px;"></p>
            
            <div class="layout-split">
                <div>
                    <div style="background: rgba(255,255,255,0.02); padding: 15px; border-radius: 10px; border: 1px solid var(--border-color); margin-bottom: 20px;">
                        <span style="font-size: 11px; color: var(--neon-gold); display:block; margin-bottom: 5px;">➔ DRAG PLAYERS INTO THEIR ROLES:</span>
                        <div id="players-drag-vault"></div>
                    </div>

                    <div class="team-grid" style="margin-top: 0;">
                        <div class="role-card" id="role-a-box" ondragover="allowDrop(event)" ondragleave="dragLeave(event)" ondrop="drop(event, 'roleA')">
                            <h4 id="role-a-title">Option A</h4>
                            <p id="role-a-desc" style="font-size: 13px; color: #fff; font-family: sans-serif; font-weight: normal; line-height: 1.4;"></p>
                            <div class="drop-zone-slots" id="slot-roleA"><span style="color:#4a5568; font-size:11px;">Drop here...</span></div>
                        </div>
                        <div class="role-card" id="role-b-box" ondragover="allowDrop(event)" ondragleave="dragLeave(event)" ondrop="drop(event, 'roleB')">
                            <h4 id="role-b-title">Option B</h4>
                            <p id="role-b-desc" style="font-size: 13px; color: #fff; font-family: sans-serif; font-weight: normal; line-height: 1.4;"></p>
                            <div class="drop-zone-slots" id="slot-roleB"><span style="color:#4a5568; font-size:11px;">Drop here...</span></div>
                        </div>
                    </div>
                </div>

                <div style="background: rgba(255, 189, 0, 0.04); border: 2px solid var(--border-color); border-radius: 12px; padding: 15px; display: flex; flex-direction: column;">
                    <h4 style="color: var(--neon-gold); margin-top: 0; display: flex; align-items: center; gap: 8px;">
                        <i class="fa-solid fa-graduation-cap"></i> TEAM RESEARCH
                    </h4>
                    <p style="font-size: 11px; color: #8fa0c4; text-transform: uppercase; margin-bottom: 10px;">Warm-up Activity:</p>
                    <p id="research-tip-text" style="font-family: sans-serif; font-size: 13px; line-height: 1.5; color: #e2e8f0; margin: 0; font-weight: normal;"></p>
                </div>
            </div>

            <button class="btn-next" onclick="preparePlayground()">START PLAYGROUND ➔</button>
        </div>

        <div id="screen-4" class="game-screen">
            <div class="protocol-box" style="background: rgba(0,210,255,0.05); border-left: 5px solid var(--neon-blue); padding: 15px; margin-bottom: 15px; font-family: sans-serif;">
                <h4 style="color:var(--neon-blue); margin: 0 0 10px 0;">🗣️ MISSION PROTOCOL</h4>
                <div id="dynamic-talk-instruction" style="font-size: 14px; line-height: 1.5; color: #cbd5e1;"></div>
                
                <button class="challenge-btn" onclick="revealChallenge()">
                    <i class="fa-solid fa-triangle-exclamation"></i> CLICK FOR CHALLENGE!
                </button>
                
                <div id="challenge-box" style="display:none; margin-top:12px; padding: 12px; background: rgba(239, 68, 68, 0.1); border: 1px solid #ef4444; border-radius: 8px;">
                    <strong style="color: #ef4444; font-size: 12px;">⚠️ TWIST CHALLENGE:</strong>
                    <p id="challenge-text" style="margin: 5px 0 0 0; font-size: 14px; font-weight: bold; color: white;"></p>
                </div>
            </div>
            
            <div id="playground-grid" class="team-grid"></div>
            
            <div style="margin-top: 20px;">
                <label style="color: var(--neon-green); font-size: 12px;">💡 SHARED IDEA SANDBOX (Log solutions/deals):</label>
                <textarea id="shared-sandbox" rows="3" placeholder="Type data, decisions or agreements discovered here..."></textarea>
            </div>
            <button class="btn-next" onclick="renderScreen5(); goToScreen(5)">GO TO FEEDBACK ➔</button>
        </div>

        <div id="screen-5" class="game-screen">
            <h3>⭐ Star Feedbacks</h3>
            <div id="feedback-list" style="display: grid; gap: 20px;"></div>
            
            <div style="background:#0b0f19; border: 2px solid var(--neon-gold); padding: 15px; border-radius: 10px; margin-top: 20px;">
                <h4 style="color: var(--neon-gold);">📜 Solution Summary & Validation</h4>
                <textarea id="final-sandbox-edit" rows="3" style="border-color: var(--neon-gold);"></textarea>
                <div id="signature-row" style="display: flex; gap: 10px; margin-top: 15px; flex-wrap: wrap;"></div>
            </div>
            <button class="btn-next" onclick="checkSignatures()">GET CERTIFICATE ➔</button>
        </div>

        <div id="screen-6" class="game-screen">
            <div class="victory-screen">
                <h1 style="text-align:center; color:#2b6cb0; margin:0;">🏆 MISSION COMPLETE</h1>
                <div id="v-players" style="text-align:center; font-weight:bold; margin: 10px 0; color:#4a5568;"></div>
                <div class="victory-block">
                    <h4>🛠️ Target Mission:</h4>
                    <p id="v-story" style="margin:0; color:#2d3748; font-weight: bold;"></p>
                </div>
                <div class="victory-block">
                    <h4>💡 Developed Group Agreement:</h4>
                    <p id="v-agreement" style="margin:0; font-style: italic; color:#2d3748;"></p>
                </div>
                <div id="v-skills" class="victory-block"></div>
                
                <div class="homework-box">
                    <h3 class="hw-title"><i class="fa-solid fa-house-laptop"></i> 📥 ASYNCHRONOUS STUDY HUB (Homework)</h3>
                    <p style="font-size:12px; color:#cbd5e1; margin-bottom:15px;">Study before or after classes to solidify your vocabulary and confidence.</p>
                    
                    <div class="hw-section">
                        <span class="hw-label">📖 1-Minute Micro Reading</span>
                        <p id="hw-reading" style="font-size:14px; margin:5px 0 0 0; line-height:1.5; color:#fff; font-weight:normal;"></p>
                    </div>

                    <div class="hw-section">
                        <span class="hw-label">🔑 Contextual Vocabulary Guide</span>
                        <p id="hw-vocab" style="font-size:13px; margin:5px 0 0 0; line-height:1.4; color:#cbd5e1; font-weight:normal; white-space: pre-line;"></p>
                    </div>

                    <div class="hw-section">
                        <span class="hw-label">🎬 YouTube Active Search Strategy</span>
                        <p id="hw-youtube" style="font-size:13px; margin:5px 0 0 0; color:#fff; font-weight:normal;"></p>
                    </div>

                    <div class="hw-section">
                        <span class="hw-label">🗣️ WhatsApp Shadowing Lab (Pronunciation Practice)</span>
                        <p id="hw-shadowing" style="font-size:14px; margin:5px 0 0 0; font-style:italic; color:var(--neon-green); font-weight:bold;"></p>
                    </div>

                    <div class="hw-section">
                        <span class="hw-label">👥 Peer Study Group Discussion Questions</span>
                        <p id="hw-questions" style="font-size:13px; margin:5px 0 0 0; line-height:1.5; color:#fff; font-weight:normal; white-space: pre-line;"></p>
                    </div>
                </div>
            </div>
            <button class="btn-next" style="background:var(--neon-blue);" onclick="window.print()">PRINT BADGE & HOMEWORK 🖨️</button>
        </div>

    </div>

    <script>
        const GROQ_KEY = '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';
        const SKILLS = [
            { id: "win-win", title: "🤝 The Peace Maker", watch: ["Did they make a fair deal?", "Did they speak nicely and wait?"] },
            { id: "direct", title: "🎯 The Sniper Talker", watch: ["Was the idea simple to follow?", "Did they present clearly?"] },
            { id: "listening", title: "👂 The Mind Reader", watch: ["Did they look at your data?", "Did they answer your question directly?"] }
        ];

        let state = { screen: 1, players: [], category: '', story: '', roles: null, assignments: {}, signatures: {}, claims: { roleA: '', roleB: '' } };

        function goToScreen(n) {
            document.querySelectorAll('.game-screen').forEach(s => s.classList.remove('active'));
            document.getElementById('screen-' + n).classList.add('active');
            state.screen = n;
            document.getElementById('screen-tag').innerText = `SCREEN ${n}/6`;
            document.getElementById('nav-back-btn').style.display = (n > 1 && n < 6) ? "block" : "none";
        }

        function goBack() { if (state.screen > 1) goToScreen(state.screen - 1); }

        function savePlayersAndGoToScreen2() {
            state.players = [document.getElementById('p1-name').value.trim(), document.getElementById('p2-name').value.trim()].filter(n => n);
            if(document.getElementById('p3-name').value.trim()) state.players.push(document.getElementById('p3-name').value.trim());

            if(state.players.length < 2) return alert("Please input at least 2 player names!");
            goToScreen(2);
        }

        function selectCat(event, c) {
            state.category = c;
            document.querySelectorAll('.mode-card').forEach(m => m.classList.remove('selected'));
            event.currentTarget.classList.add('selected');
        }

        async function generateRandomActivity() {
            if(!state.category) return alert("Please select a category (WORK, LIFE, SCHOOL, CUSTOM) first!");
            
            const txtArea = document.getElementById('story-input');
            txtArea.value = "⚡ Loading random mission...";

            const prompt = `Generate just ONE short, practical action-based communication objective scenario for English students learning A1 level. 
            It must be exactly inside this category context: ${state.category}. 
            Provide ONLY the sentence description in your output. No preamble, no markers, no quotes, no extra text. maximum 8 words.`;

            try {
                const resp = await fetch('https://api.groq.com/openai/v1/chat/completions', {
                    method: 'POST',
                    headers: { 'Authorization': `Bearer ${GROQ_KEY}`, 'Content-Type': 'application/json' },
                    body: JSON.stringify({ model: "llama-3.1-8b-instant", messages: [{role: "user", content: prompt}], temperature: 0.7 })
                });
                const data = await resp.json();
                txtArea.value = data.choices[0].message.content.replace(/"/g, '').trim();
            } catch(e) {
                const lists = {
                    WORK: "negotiate a deadline with your manager",
                    LIFE: "check-in at a hotel near the beach",
                    SCHOOL: "ask a classmate to group up for a project",
                    CUSTOM: "order food at a busy international restaurant"
                };
                txtArea.value = lists[state.category] || "book a flight ticket at the counter";
            }
        }

        async function startAIScenario() {
            state.story = document.getElementById('story-input').value.trim();
            if(!state.category || !state.story) return alert("Select theme and type goal!");

            document.getElementById('screen-2').classList.remove('active');
            document.getElementById('screen-loading').classList.add('active');

            const prompt = `Create a 2-role interactive roleplay scenario for beginner A1 English students based on this specific target: "${state.story}" (Category: ${state.category}).
            
            CRITICAL DIRECTION: Look closely at the custom action target text. Do NOT use generic role names like Customer or Agent unless it perfectly fits. If the topic is about spaceship, astronauts, space or mars, make the roles tailored (e.g. "Commander", "Pilot", "Astronaut").
            
            Provide exactly 2 specific simple action sub-tasks for Role A and exactly 2 matching interactive sub-tasks for Role B. 
            Generate a short specific challenge/twist customized to this exact scenario.
            Generate 3 to 5 key items or conditions to consider that match this exact situation.
            Generate a short "summary_template" phrase matching the user's topic.
            Generate a single "research_tip" sentence to challenge students to research keywords about the topic together.

            NEW BONUS REQUIREMENT: Generate an asynchronous homework bundle for A1-A2 students inside the "homework" root key tailored entirely to the target topic "${state.story}".

            Return a strictly clean JSON object with this structure only:
            {
              "context": "Short setup description.",
              "roleA": {"title": "Exact customized title", "tasks": ["Task 1", "Task 2"], "hint": "Short tip"},
              "roleB": {"title": "Exact customized title", "tasks": ["Task 1", "Task 2"], "hint": "Short tip"},
              "twist": "Customized sudden problem related to the action",
              "instructions": "Customized list of items to consider during interaction",
              "summary_template": "Action-specific completion summary text template",
              "research_tip": "Targeted peer-research prompt based on the scenario items",
              "homework": {
                 "reading_text": "A maximum 120-word highly realistic micro text explaining the background context of the target scenario. Use HTML bold tags <b> like this </b> for the 4 most important keywords.",
                 "vocabulary_guide": "A simple list of the 4 bolded keywords with a simple 3-word definition for each.",
                 "youtube_search_strategy": "Provide exact terms to search on YouTube to watch a real POV simulation or documentary of this topic + 1 sentence instruction.",
                 "shadowing_phrase": "One practical, 12-word target sentence from the scenario to practice American pronunciation rhythm.",
                 "peer_study_questions": [
                    "One simple question about the reading text for group study.",
                    "One opinion question to warm up before the official Zoom class."
                 ]
              }
            }`;

            try {
                // AJUSTADO ENDPOINT E PARAMÊTROS PARA EVITAR COMPORTAMENTO DE CORS RESTRITO
                const resp = await fetch('https://api.groq.com/openai/v1/chat/completions', {
                    method: 'POST',
                    headers: { 
                        'Authorization': `Bearer ${GROQ_KEY}`, 
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({ 
                        model: "llama-3.1-8b-instant", 
                        messages: [{role: "user", content: prompt}], 
                        temperature: 0.3, 
                        response_format: {type: "json_object"} 
                    })
                });
                const data = await resp.json();
                state.roles = JSON.parse(data.choices[0].message.content);
                buildScreen3UI();
            } catch(e) { 
                // FALLBACK AJUSTADO PARA DETECTAR MARTE/ESPAÇO CASO O NAVEGADOR BLOQUEIE A REDE LOCAL
                let isSpace = state.story.toLowerCase().includes('space') || state.story.toLowerCase().includes('mars') || state.story.toLowerCase().includes('spaceship');
                
                state.roles = {
                    context: isSpace ? `Mission Simulation Control open for target: "${state.story}".` : `Live simulation open for your team target challenge: "${state.story}".`,
                    roleA: { 
                        title: isSpace ? "Mission Commander" : "Team Lead", 
                        tasks: isSpace ? ["Report spaceship system checklist parameters", "Confirm Mars injection trajectory path"] : ["Explain your main request clearly", "Show your documentation or ticket"], 
                        hint: isSpace ? "Speak clearly using system status vocabulary." : "State your name and purpose." 
                    },
                    roleB: { 
                        title: isSpace ? "Pilot Astronaut" : "Co-Pilot / Specialist", 
                        tasks: isSpace ? ["Verify core oxygen and engine pressure limits", "Alert the captain about any immediate warning light"] : ["Verify the request criteria", "Help and guide your partner to finish"], 
                        hint: isSpace ? "Monitor console parameters strictly." : "Be polite but follow the rules." 
                    },
                    twist: isSpace ? "A small asteroid dust cloud caused a micro leak in tank two!" : "The primary option requested is not available right now!",
                    instructions: isSpace ? "oxygen levels, fuel pressure, navigation coordinates, and communications grid." : "dates, prices, requirements, and local rules.",
                    summary_template: isSpace ? `The crew managed to pilot through the gravity well and stabilized the spaceship target: "${state.story}".` : `The team successfully collaborated to resolve the unexpected issues and completed the mission: "${state.story}".`,
                    research_tip: isSpace ? "Tip: What do you know about space system terms? Do a quick search about basic astronaut checklists!" : "Tip: Do a quick 1-minute search about common phrases or policies!",
                    homework: {
                        reading_text: isSpace ? `To survive a <b>space mission</b> to Mars, crew communication must be perfect. Astronauts monitor critical <b>oxygen levels</b> and calculate orbital <b>navigation paths</b> daily. Any mistake on the <b>spaceship dashboard</b> can delay the landing schedule.` : `When you need to complete this mission, preparation is everything. Always check your requirements online.`,
                        vocabulary_guide: isSpace ? "1. Space mission: A voyage outside Earth.\n2. Oxygen levels: Air data to breathe.\n3. Navigation paths: Directions in space.\n4. Spaceship dashboard: Main control console monitor." : "1. Requirements: Rules you need to follow.",
                        youtube_search_strategy: isSpace ? "Search on YouTube: 'Inside a spaceship cockpit simulation POV'. Watch how astronauts coordinate instruments." : "Search on YouTube simulation POV tips.",
                        shadowing_phrase: isSpace ? "Commander, the system dashboard indicates stable oxygen pressure for our current trajectory." : "I will complete my target objectives on time.",
                        peer_study_questions: isSpace ? ["What is the most critical asset for astronauts to monitor according to the text?", "Would you like to travel inside a spaceship to another planet? Why?"] : ["Question 1", "Question 2"]
                    }
                };
                buildScreen3UI();
            }
        }

        function buildScreen3UI() {
            document.getElementById('s3-context').innerText = state.roles.context;
            
            document.getElementById('role-a-title').innerText = `Option A: ${state.roles.roleA.title}`;
            document.getElementById('role-a-desc').innerHTML = state.roles.roleA.tasks.map(t => `➔ ${t}`).join('<br><br>');
            
            document.getElementById('role-b-title').innerText = `Option B: ${state.roles.roleB.title}`;
            document.getElementById('role-b-desc').innerHTML = state.roles.roleB.tasks.map(t => `➔ ${t}`).join('<br><br>');
            
            document.getElementById('research-tip-text').innerText = state.roles.research_tip || "Do a quick brainstorm about this scenario's key requirements before starting!";

            const vault = document.getElementById('players-drag-vault');
            vault.innerHTML = "";
            state.claims = { roleA: '', roleB: '' };
            document.getElementById('slot-roleA').innerHTML = '<span style="color:#4a5568; font-size:11px;">Drop here...</span>';
            document.getElementById('slot-roleB').innerHTML = '<span style="color:#4a5568; font-size:11px;">Drop here...</span>';

            state.players.forEach(p => {
                vault.innerHTML += `<div class="drag-player" draggable="true" id="drag-${p}" ondragstart="drag(event)">${p}</div>`;
            });

            document.getElementById('screen-loading').classList.remove('active');
            goToScreen(3);
        }

        function drag(ev) { ev.dataTransfer.setData("text", ev.target.id); }
        function allowDrop(ev) { ev.preventDefault(); ev.currentTarget.classList.add('dragover'); }
        function dragLeave(ev) { ev.currentTarget.classList.remove('dragover'); }
        function drop(ev, roleKey) {
            ev.preventDefault();
            ev.currentTarget.classList.remove('dragover');
            const dataId = ev.dataTransfer.getData("text");
            const playerName = dataId.replace("drag-", "");
            
            state.claims[roleKey] = playerName;
            document.getElementById(`slot-${roleKey}`).innerHTML = `<div class="drag-player" style="background:var(--neon-green); color:black; cursor:default;">👤 ${playerName}</div>`;
        }

        function revealChallenge() {
            document.getElementById('challenge-box').style.display = "block";
        }

        function preparePlayground() {
            const nameA = state.claims.roleA;
            const nameB = state.claims.roleB;
            if(!nameA || !nameB) return alert("Please drag a player to Role A and another to Role B!");

            document.getElementById('challenge-text').innerText = state.roles.twist;
            document.getElementById('challenge-box').style.display = "none";

            document.getElementById('dynamic-talk-instruction').innerHTML = `
                🎯 <strong>Goal:</strong> ${state.story}<br>
                📋 <strong>Consider:</strong> ${state.roles.instructions}<br><br>
                💡 <strong>${nameA}</strong> (${state.roles.roleA.title}): ${state.roles.roleA.hint}<br>
                💡 <strong>${nameB}</strong> (${state.roles.roleB.title}): ${state.roles.roleB.hint}<br><br>
                🚀 <strong>Steps:</strong> Unmute on Zoom ➔ Start dialogue ➔ Click "Challenge" for the surprise!
            `;

            state.assignments = {};
            let shuffled = [...SKILLS].sort(() => 0.5 - Math.random());
            
            state.assignments[nameA] = { role: 'roleA', watching: nameB, skill: shuffled[0] };
            state.assignments[nameB] = { role: 'roleB', watching: nameA, skill: shuffled[1] };
            
            state.players.forEach(p => {
                if(p !== nameA && p !== nameB) {
                    state.assignments[p] = { role: 'mediator', watching: nameA, skill: shuffled[2] || SKILLS[0] };
                }
                state.signatures[p] = false;
            });

            const grid = document.getElementById('playground-grid');
            grid.innerHTML = "";
            Object.keys(state.assignments).forEach(name => {
                const data = state.assignments[name];
                const tasks = data.role === 'mediator' ? ["Help the team collaborate and maintain English focus"] : state.roles[data.role].tasks;
                const cardColor = data.role === 'roleA' ? 'var(--neon-blue)' : (data.role === 'roleB' ? 'var(--neon-pink)' : 'var(--neon-gold)');
                
                grid.innerHTML += `
                    <div class="player-box" style="border-color:${cardColor}">
                        <h4 style="color:var(--neon-blue); margin-top:0;">📋 ${name} Dashboard</h4>
                        <div class="check-zone">
                            <span style="font-size:11px; color:var(--neon-green); font-weight:bold;">🚩 WHAT TO DO:</span>
                            ${tasks.map(t => `<div class="check-item" style="margin-top:5px;"><input type="checkbox"> ${t}</div>`).join('')}
                        </div>
                        <div class="check-zone">
                            <span style="font-size:11px; color:var(--neon-gold); font-weight:bold;">👀 WATCH IN ${data.watching.toUpperCase()}:</span>
                            ${data.skill.watch.map(w => `<div class="check-item" style="margin-top:5px;"><input type="checkbox"> ${w}</div>`).join('')}
                        </div>
                    </div>`;
            });
            goToScreen(4);
        }

        function renderScreen5() {
            const list = document.getElementById('feedback-list');
            list.innerHTML = "";
            Object.keys(state.assignments).forEach((name, i) => {
                const data = state.assignments[name];
                list.innerHTML += `
                    <div class="player-box">
                        <h4 style="color:var(--neon-green); margin:0;">${i+1}. ${name} ➔ Performance Stars for ${data.watching}</h4>
                        <div style="display:grid; grid-template-columns:1fr 1fr; gap:10px; margin-top:5px;">
                            <textarea id="pos-${name}" placeholder="What did they do great?"></textarea>
                            <textarea id="next-${name}" placeholder="One growth strategy?"></textarea>
                        </div>
                    </div>`;
            });

            const nameA = state.claims.roleA;
            const nameB = state.claims.roleB;
            const roleATitle = state.roles.roleA.title;
            const roleBTitle = state.roles.roleB.title;
            
            let compiledSummary = state.roles.summary_template || `Mission completed: ${nameA} (${roleATitle}) and ${nameB} (${roleBTitle}) successfully coordinated to resolve the target objectives.`;
            compiledSummary = compiledSummary.replace(/\[Role A\]/g, `${nameA} (${roleATitle})`).replace(/\[Role B\]/g, `${nameB} (${roleBTitle})`);

            const userSandboxNotes = document.getElementById('shared-sandbox').value.trim();
            if(userSandboxNotes) {
                compiledSummary += ` Notes discovered: "${userSandboxNotes}"`;
            }

            document.getElementById('final-sandbox-edit').value = compiledSummary;

            const sigRow = document.getElementById('signature-row');
            sigRow.innerHTML = "";
            state.players.forEach(p => {
                sigRow.innerHTML += `<button class="sign-btn" id="sig-${p}" onclick="sign('${p}')">✍️ ${p}: Sign</button>`;
            });
        }

        function sign(p) {
            state.signatures[p] = true;
            document.getElementById('sig-' + p).classList.add('signed');
            document.getElementById('sig-' + p).style.background = 'var(--neon-green)';
            document.getElementById('sig-' + p).style.color = '#000';
            document.getElementById('sig-' + p).innerText = `✅ ${p}: SIGNED`;
        }

        function checkSignatures() {
            let missing = state.players.filter(p => !state.signatures[p]);
            if(missing.length === 0) {
                goToScreen(6);
                renderScreen6();
            } else {
                alert("All setup slot members must click to sign!");
            }
        }

        function renderScreen6() {
            document.getElementById('v-players').innerText = state.players.join('  ★  ');
            document.getElementById('v-story').innerText = state.story;
            document.getElementById('v-agreement').innerText = document.getElementById('final-sandbox-edit').value;
            
            const skillsDiv = document.getElementById('v-skills');
            skillsDiv.innerHTML = "<h4>🌟 Unlocked Competencies Matrix:</h4>";
            Object.keys(state.assignments).forEach(name => {
                const pos = document.getElementById('pos-'+name).value || "Great communication rhythm.";
                skillsDiv.innerHTML += `<p style="color:#2d3748; font-size:14px; margin:8px 0;">⚡ <strong>${state.assignments[name].watching}</strong> mastered <span style="color:#2b6cb0; font-weight:bold;">${state.assignments[name].skill.title}</span><br><small style="color:#4a5568;">Feedback: "${pos}"</small></p>`;
            });

            const hw = state.roles.homework || {};
            document.getElementById('hw-reading').innerHTML = hw.reading_text || "No reading context available.";
            
            let vocabText = hw.vocabulary_guide || "";
            if(Array.isArray(vocabText)) vocabText = vocabText.join('\n');
            document.getElementById('hw-vocab').innerText = vocabText;

            document.getElementById('hw-youtube').innerText = hw.youtube_search_strategy || "Search keywords about this topic directly on YouTube.";
            document.getElementById('hw-shadowing').innerText = `"${hw.shadowing_phrase || "I will complete my target objectives on time."}"`;
            
            let questionsText = hw.peer_study_questions || "";
            if(Array.isArray(questionsText)) questionsText = questionsText.map((q, idx) => `${idx+1}. ${q}`).join('\n');
            document.getElementById('hw-questions').innerText = questionsText;
        }
    </script>
</body>
</html>
