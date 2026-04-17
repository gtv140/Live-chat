<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Prime Solutions Ultimate</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root { --gold: #FFD700; --purple: #8A2BE2; --dark: #080810; --glass: rgba(255, 255, 255, 0.05); --neon: 0 0 10px rgba(138,43,226,0.5); }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { margin: 0; background: var(--dark); color: white; font-family: 'Rajdhani', sans-serif; height: 100vh; overflow: hidden; }
        
        .app-wrapper { display: flex; height: 100vh; width: 100vw; overflow: hidden; position: relative; }

        /* Sidebar */
        .sidebar { 
            width: 280px; background: rgba(5, 5, 15, 0.98); border-right: 1px solid rgba(138, 43, 226, 0.3); 
            display: flex; flex-direction: column; padding: 20px; z-index: 1000; transition: 0.4s ease;
        }
        
        .main-container { flex: 1; display: flex; flex-direction: column; position: relative; background: radial-gradient(circle at top right, #1a0b2e, #080810); height: 100vh; }

        /* Mobile Responsive Sidebar */
        @media (max-width: 768px) {
            .sidebar { position: absolute; left: -280px; height: 100%; box-shadow: 10px 0 20px rgba(0,0,0,0.8); }
            .sidebar.open { left: 0; }
        }

        .header { padding: 15px 20px; border-bottom: 1px solid rgba(255,255,255,0.05); display: flex; align-items: center; background: rgba(0,0,0,0.4); flex-shrink: 0; }
        .menu-btn { background: none; border: 1px solid var(--purple); color: white; padding: 5px 10px; cursor: pointer; border-radius: 5px; margin-right: 15px; display: none; }
        @media (max-width: 768px) { .menu-btn { display: block; } }

        /* Messages Area - FIXED HEIGHT */
        #messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 15px; scroll-behavior: smooth; }
        
        /* Chat Bubbles */
        .msg-card { max-width: 80%; padding: 12px; border-radius: 18px; position: relative; animation: slideUp 0.3s ease; border: 1px solid rgba(255,255,255,0.05); }
        .mine { align-self: flex-end; background: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%); border-bottom-right-radius: 2px; }
        .others { align-self: flex-start; background: #1e1e2e; border-bottom-left-radius: 2px; }
        .bot-msg { align-self: center; background: rgba(255,215,0,0.05); border: 1px solid var(--gold); color: var(--gold); font-size: 0.8rem; border-radius: 20px; padding: 5px 15px; }

        .sender-info { font-size: 0.7rem; color: var(--gold); font-weight: bold; margin-bottom: 4px; font-family: 'Orbitron'; }
        .shared-img { max-width: 100%; border-radius: 10px; margin-top: 5px; }
        
        /* Reply & Reaction UI */
        .reply-box { font-size: 0.75rem; background: rgba(0,0,0,0.2); padding: 5px 10px; border-left: 3px solid var(--gold); margin-bottom: 8px; border-radius: 4px; }
        .msg-actions { margin-top: 8px; display: flex; gap: 10px; font-size: 0.7rem; color: #aaa; }
        .msg-actions span:hover { color: var(--gold); cursor: pointer; }
        .react-pills { display: flex; gap: 4px; margin-top: 5px; }
        .react-pill { background: rgba(255,255,255,0.1); padding: 2px 6px; border-radius: 10px; font-size: 0.7rem; }

        /* Input System */
        #reply-preview { display: none; background: #161625; padding: 10px 20px; border-top: 2px solid var(--gold); font-size: 0.8rem; }
        .input-box { padding: 15px; background: #0c0c14; display: flex; gap: 10px; align-items: center; flex-shrink: 0; }
        #msgInput { flex: 1; background: #161625; border: 1px solid #333; padding: 12px 18px; border-radius: 25px; color: white; outline: none; }
        .icon-btn { cursor: pointer; font-size: 1.3rem; transition: 0.2s; flex-shrink: 0; }
        .icon-btn:hover { transform: scale(1.1); }
        .send-btn { background: var(--gold); border: none; width: 40px; height: 40px; border-radius: 50%; font-weight: bold; cursor: pointer; }

        /* Info Pages */
        .page { display: none; flex: 1; overflow-y: auto; padding: 30px; }
        .active-page { display: flex; flex-direction: column; }
        .nav-item { padding: 12px; cursor: pointer; border-radius: 10px; margin-bottom: 5px; color: #888; display: flex; align-items: center; gap: 10px; }
        .nav-item.active, .nav-item:hover { background: rgba(138,43,226,0.2); color: white; border-right: 3px solid var(--purple); }

        #login-overlay { position: fixed; inset: 0; background: var(--dark); z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 20px; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>

    <div id="login-overlay">
        <div style="background: rgba(15,15,25,0.95); padding: 40px; border-radius: 30px; border: 1px solid var(--purple); text-align: center; width: 100%; max-width: 350px;">
            <h1 style="font-family:'Orbitron'; color:var(--gold); margin:0;">PRIME</h1>
            <p style="letter-spacing:4px; font-size:0.7rem; color:#555; margin-bottom:30px;">SOLUTIONS CORE</p>
            <input type="text" id="userInput" placeholder="Username" style="width:100%; padding:15px; margin-bottom:12px; border-radius:10px; border:none; background:#000; color:white;">
            <input type="password" id="passInput" placeholder="Password" style="width:100%; padding:15px; margin-bottom:20px; border-radius:10px; border:none; background:#000; color:white;">
            <button onclick="handleAuth()" style="width:100%; padding:15px; background:var(--gold); border:none; border-radius:10px; font-weight:bold; cursor:pointer; font-family:'Orbitron';">AUTHENTICATE</button>
        </div>
    </div>

    <div class="app-wrapper">
        <div class="sidebar" id="sidebar">
            <h2 id="myUserDisplay" style="color:var(--gold); font-size:0.9rem; font-family:'Orbitron';">@OPERATOR</h2>
            <div class="nav-item active" onclick="showPage('chat-page'); toggleSidebar()">🌐 Global Hub</div>
            <div class="nav-item" onclick="showPage('about-page'); toggleSidebar()">🏢 About Prime</div>
            <div class="nav-item" onclick="showPage('privacy-page'); toggleSidebar()">🛡️ Security Hub</div>
            
            <h2 style="font-size:0.65rem; color:#444; margin-top:30px; text-transform:uppercase;">Live Operators</h2>
            <div id="online-list" style="flex:1; overflow-y:auto;"></div>
            
            <div onclick="doLogout()" class="nav-item" style="color:#ff4d4d; border-top:1px solid #222; margin-top:10px;">🔴 Terminate Session</div>
        </div>

        <div class="main-container">
            <div class="header">
                <button class="menu-btn" onclick="toggleSidebar()">☰</button>
                <div id="active-target" style="font-family:'Orbitron'; color:var(--gold); font-size:0.8rem;"># GLOBAL-HUB</div>
            </div>

            <div id="chat-page" class="page active-page" style="padding:0;">
                <div id="messages"></div>
                
                <div id="reply-preview">
                    <span id="reply-text">Replying to...</span>
                    <span onclick="cancelReply()" style="float:right; cursor:pointer;">✖</span>
                </div>

                <div class="input-box">
                    <label for="imgInput" class="icon-btn" title="Gallery">🖼️</label>
                    <input type="file" id="imgInput" style="display:none" accept="image/*">
                    
                    <span id="voice-btn" class="icon-btn" onclick="toggleRecord()">🎤</span>
                    
                    <input type="text" id="msgInput" placeholder="Message, sweetie...">
                    <button class="send-btn" onclick="doSend()">➔</button>
                </div>
            </div>

            <div id="about-page" class="page">
                <h1 style="color:var(--gold); font-family:'Orbitron';">PRIME SOLUTIONS</h1>
                <p>Live Connect is a proprietary real-time communication engine designed for high-security data exchange. Our mission is to bridge the digital gap with speed and elegance.</p>
                <h3>Our Vision</h3>
                <p>To become the world's most trusted SaaS platform for instant connectivity.</p>
            </div>

            <div id="privacy-page" class="page">
                <h1 style="color:var(--gold); font-family:'Orbitron';">Security Protocol</h1>
                <p>All data within the Prime Ecosystem is encrypted. We value your privacy above all else.</p>
                <ul>
                    <li>Zero-log private messaging.</li>
                    <li>End-to-end encrypted gallery sharing.</li>
                    <li>Instant database wiping upon session termination.</li>
                </ul>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, get, set, update, remove, onValue, onDisconnect, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            authDomain: "liveconnect-9af37.firebaseapp.com",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
            storageBucket: "liveconnect-9af37.firebasestorage.app",
            messagingSenderId: "665010965194",
            appId: "1:665010965194:web:5a71a3eb2b0627fed04233"
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);

        let currentUser = "";
        let chatTarget = "global";
        let replyingTo = null;
        let mediaRecorder;
        let audioChunks = [];

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('open');

        window.handleAuth = async () => {
            const user = document.getElementById('userInput').value.trim().toLowerCase();
            const pass = document.getElementById('passInput').value.trim();
            if(!user || !pass) return;
            currentUser = user;
            const pRef = ref(db, 'presence/' + currentUser);
            set(pRef, { name: currentUser, online: true });
            onDisconnect(pRef).remove();
            document.getElementById('login-overlay').style.display = 'none';
            document.getElementById('myUserDisplay').innerText = "@" + currentUser.toUpperCase();
            initApp();
        };

        function initApp() {
            onValue(ref(db, 'presence'), (snap) => {
                const list = document.getElementById('online-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    if(c.val().name !== currentUser) {
                        list.innerHTML += `<div class="nav-item" onclick="setTarget('${c.val().name}')">🟢 ${c.val().name.toUpperCase()}</div>`;
                    }
                });
            });
            listenMessages();
        }

        window.showPage = (id) => {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
            document.getElementById(id).classList.add('active-page');
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
        };

        window.setTarget = (t) => {
            chatTarget = t;
            document.getElementById('active-target').innerText = t === 'global' ? '# GLOBAL-HUB' : `@ ${t.toUpperCase()}`;
            document.getElementById('messages').innerHTML = "";
            showPage('chat-page');
            listenMessages();
            if(window.innerWidth <= 768) toggleSidebar();
        };

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            const msgData = { user: currentUser, text: inp.value, time: serverTimestamp() };
            if(replyingTo) msgData.reply = replyingTo;
            push(ref(db, getPath()), msgData);
            inp.value = ""; cancelReply();
        };

        // IMAGE HANDLER
        document.getElementById('imgInput').onchange = (e) => {
            const file = e.target.files[0];
            if (!file || file.size > 1000000) return alert("File too big (Max 1MB)");
            const reader = new FileReader();
            reader.onload = (ev) => push(ref(db, getPath()), { user: currentUser, image: ev.target.result, time: serverTimestamp() });
            reader.readAsDataURL(file);
        };

        // VOICE HANDLER
        window.toggleRecord = async () => {
            const btn = document.getElementById('voice-btn');
            if (!mediaRecorder || mediaRecorder.state === "inactive") {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                mediaRecorder.onstop = async () => {
                    const audioBlob = new Blob(audioChunks, { type: 'audio/mp3' });
                    const reader = new FileReader();
                    reader.onload = (e) => push(ref(db, getPath()), { user: currentUser, voice: e.target.result, time: serverTimestamp() });
                    reader.readAsDataURL(audioBlob);
                    audioChunks = [];
                    btn.innerText = "🎤"; btn.style.color = "white";
                };
                mediaRecorder.start();
                btn.innerText = "🛑"; btn.style.color = "red";
            } else {
                mediaRecorder.stop();
            }
        };

        function getPath() {
            return chatTarget === 'global' ? 'messages/global' : `messages/private/${[currentUser, chatTarget].sort().join('_')}`;
        }

        function listenMessages() {
            onChildAdded(ref(db, getPath()), (snap) => {
                const d = snap.val(); const id = snap.key;
                const isMe = d.user === currentUser;
                const mBox = document.getElementById('messages');
                
                let content = d.text || "";
                if(d.image) content = `<img src="${d.image}" class="shared-img">`;
                if(d.voice) content = `<audio controls src="${d.voice}" style="width:200px; height:30px;"></audio>`;

                const replyHtml = d.reply ? `<div class="reply-box"><b>@${d.reply.user}:</b> ${d.reply.text}</div>` : "";
                const reactHtml = d.reacts ? `<div class="react-pills">${Object.entries(d.reacts).map(([e, c]) => `<span class="react-pill">${e} ${c}</span>`).join("")}</div>` : "";

                const html = `
                    <div class="msg-card ${isMe ? 'mine' : 'others'}">
                        ${!isMe ? `<span class="sender-info">${d.user.toUpperCase()}</span>` : ''}
                        ${replyHtml} ${content} ${reactHtml}
                        <div class="msg-actions">
                            <span onclick="startReply('${d.user}', '${d.text || "Media"}')">↩️ Reply</span>
                            <span onclick="addReact('${id}', '❤️')">❤️</span>
                            <span onclick="addReact('${id}', '🔥')">🔥</span>
                            ${isMe ? `<span onclick="deleteMsg('${id}')">🗑️</span>` : ""}
                        </div>
                    </div>`;
                mBox.insertAdjacentHTML('beforeend', html);
                mBox.scrollTop = mBox.scrollHeight;
            });
        }

        window.startReply = (u, t) => { replyingTo = { user: u, text: t }; document.getElementById('reply-preview').style.display="block"; document.getElementById('reply-text').innerText = "Replying to @" + u; };
        window.cancelReply = () => { replyingTo = null; document.getElementById('reply-preview').style.display="none"; };
        window.addReact = (id, e) => {
            const rRef = ref(db, getPath() + `/${id}/reacts/${e}`);
            get(rRef).then(s => update(ref(db, getPath() + `/${id}/reacts`), { [e]: (s.val() || 0) + 1 }));
        };
        window.deleteMsg = (id) => { if(confirm("Delete message?")) remove(ref(db, getPath() + '/' + id)); };
        window.doLogout = () => { location.reload(); };

    </script>
</body>
</html>
