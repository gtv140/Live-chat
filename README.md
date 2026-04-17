<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Prime Solutions Elite</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg: #f0f2f5; --sidebar: #ffffff; --text: #1c1e21;
            --primary: #6a11cb; --gold: #b8860b; --border: rgba(0,0,0,0.1);
            --msg-mine: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%);
            --msg-other: #ffffff;
        }

        [data-theme="dark"] {
            --bg: #080810; --sidebar: #0f0f1e; --text: #e4e6eb;
            --primary: #8A2BE2; --gold: #FFD700; --border: rgba(255,255,255,0.1);
            --msg-mine: linear-gradient(135deg, #8A2BE2 0%, #4b0082 100%);
            --msg-other: #242526;
        }

        * { box-sizing: border-box; transition: 0.2s ease; }
        body { margin: 0; background: var(--bg); color: var(--text); font-family: 'Rajdhani', sans-serif; height: 100vh; overflow: hidden; display: flex; flex-direction: column; }
        
        /* App Layout */
        .app-wrapper { display: flex; flex: 1; height: 100%; overflow: hidden; position: relative; }

        .sidebar { 
            width: 280px; background: var(--sidebar); border-right: 1px solid var(--border); 
            display: flex; flex-direction: column; padding: 20px; z-index: 1000;
        }

        .main-container { flex: 1; display: flex; flex-direction: column; height: 100%; position: relative; overflow: hidden; }

        .header { 
            padding: 12px 20px; border-bottom: 1px solid var(--border); 
            display: flex; align-items: center; background: var(--sidebar); gap: 15px; flex-shrink: 0;
        }

        /* Message Area - FIXED & SCROLLABLE */
        #messages { 
            flex: 1; padding: 20px; overflow-y: auto; display: flex; 
            flex-direction: column; gap: 12px; background: transparent;
        }

        .msg-card { max-width: 85%; padding: 12px 16px; border-radius: 18px; box-shadow: 0 2px 5px rgba(0,0,0,0.05); position: relative; }
        .mine { align-self: flex-end; background: var(--msg-mine); color: white; border-bottom-right-radius: 4px; }
        .others { align-self: flex-start; background: var(--msg-other); color: var(--text); border-bottom-left-radius: 4px; border: 1px solid var(--border); }

        .sender-info { font-size: 0.7rem; color: var(--gold); font-weight: bold; margin-bottom: 4px; font-family: 'Orbitron'; display: block; }

        /* Input Bar - ALWAYS AT BOTTOM */
        .input-box { 
            padding: 15px; background: var(--sidebar); display: flex; gap: 10px; 
            align-items: center; border-top: 1px solid var(--border); flex-shrink: 0;
        }

        #msgInput { 
            flex: 1; background: var(--bg); border: 1px solid var(--border); 
            padding: 12px 20px; border-radius: 25px; color: var(--text); outline: none; 
        }

        .action-btn { font-size: 1.4rem; cursor: pointer; color: var(--gold); display: flex; align-items: center; }
        .send-btn { 
            background: var(--gold); border: none; width: 42px; height: 42px; 
            border-radius: 50%; color: white; cursor: pointer; display: flex; 
            align-items: center; justify-content: center; font-size: 1.2rem;
        }

        /* Responsive Fix */
        @media (max-width: 768px) {
            .sidebar { position: absolute; left: -280px; height: 100%; transition: 0.3s; }
            .sidebar.open { left: 0; box-shadow: 10px 0 20px rgba(0,0,0,0.2); }
            .msg-card { max-width: 90%; }
        }

        .page { display: none; height: 100%; width: 100%; }
        .active-page { display: flex; flex-direction: column; }
    </style>
</head>
<body data-theme="light">

    <div id="login-overlay" style="position:fixed; inset:0; background:var(--bg); z-index:9999; display:flex; align-items:center; justify-content:center;">
        <div style="background:var(--sidebar); padding:40px; border-radius:20px; border:1px solid var(--gold); text-align:center; width:90%; max-width:350px;">
            <h1 style="font-family:'Orbitron'; color:var(--primary);">PRIME CONNECT</h1>
            <input type="text" id="userInput" placeholder="Username" style="width:100%; padding:12px; margin: 10px 0; border-radius:10px; border:1px solid var(--border);">
            <button onclick="handleAuth()" style="width:100%; padding:12px; background:var(--gold); color:white; border:none; border-radius:10px; font-weight:bold; cursor:pointer;">START CHATTING</button>
        </div>
    </div>

    <div class="app-wrapper">
        <div class="sidebar" id="sidebar">
            <h2 style="font-family:'Orbitron'; color:var(--primary); font-size:1rem;">PRIME SOLUTIONS</h2>
            <div onclick="showPage('chat-page')" style="cursor:pointer; padding:12px; font-weight:bold;">🌐 GLOBAL HUB</div>
            <div onclick="showPage('about-page')" style="cursor:pointer; padding:12px;">🏢 ABOUT COMPANY</div>
            <div onclick="location.reload()" style="cursor:pointer; padding:12px; color:red; margin-top:auto;">🚪 LOGOUT</div>
        </div>

        <div class="main-container">
            <div class="header">
                <button onclick="document.getElementById('sidebar').classList.toggle('open')" style="background:none; border:1px solid var(--primary); border-radius:5px; padding:5px 10px;">☰</button>
                <div style="font-weight:bold; color:var(--gold); font-family:'Orbitron'; font-size:0.8rem;"># LIVE-CONNECT</div>
                <button onclick="toggleTheme()" style="margin-left:auto; cursor:pointer; background:none; border:1px solid var(--border); padding:5px 10px; border-radius:15px;">🌓 THEME</button>
            </div>

            <div id="chat-page" class="page active-page">
                <div id="messages"></div>
                
                <div class="input-box">
                    <label for="imgInput" class="action-btn">🖼️</label>
                    <input type="file" id="imgInput" style="display:none" accept="image/*">
                    <span class="action-btn" onclick="toggleRecord()" id="voice-btn">🎤</span>
                    <input type="text" id="msgInput" placeholder="Write something, sweetie...">
                    <button onclick="doSend()" class="send-btn">➔</button>
                </div>
            </div>

            <div id="about-page" class="page" style="padding:20px;">
                <h1 style="color:var(--gold)">PRIME SOLUTIONS</h1>
                <p>Advanced Real-time Communication Engine. Built for speed and security.</p>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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

        window.toggleTheme = () => {
            const current = document.body.getAttribute('data-theme');
            document.body.setAttribute('data-theme', current === 'light' ? 'dark' : 'light');
        };

        window.handleAuth = () => {
            const u = document.getElementById('userInput').value.trim();
            if(!u) return;
            currentUser = u;
            document.getElementById('login-overlay').style.display='none';
            initApp();
        };

        function initApp() {
            onChildAdded(ref(db, 'messages/global'), (snap) => {
                const d = snap.val(); const isMe = d.user === currentUser;
                const mBox = document.getElementById('messages');
                
                let content = d.text || "";
                if(d.image) content = `<img src="${d.image}" style="max-width:100%; border-radius:10px;">`;
                
                const html = `<div class="msg-card ${isMe ? 'mine' : 'others'}">
                    ${!isMe ? `<span class="sender-info">${d.user.toUpperCase()}</span>` : ""}
                    ${content}
                    <div style="margin-top:5px; font-size:0.7rem; opacity:0.6;">
                        <span>❤️</span> <span>🔥</span> <span>↩️ Reply</span>
                    </div>
                </div>`;
                mBox.insertAdjacentHTML('beforeend', html);
                mBox.scrollTop = mBox.scrollHeight;
            });
        }

        window.showPage = (id) => {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
            document.getElementById(id).classList.add('active-page');
            if(window.innerWidth < 768) document.getElementById('sidebar').classList.remove('open');
        };

        window.doSend = () => {
            const inp = document.getElementById('msgInput'); if(!inp.value.trim()) return;
            push(ref(db, 'messages/global'), { user: currentUser, text: inp.value, time: serverTimestamp() });
            inp.value = "";
        };

        document.getElementById('imgInput').onchange = (e) => {
            const file = e.target.files[0]; if(!file) return;
            const r = new FileReader(); r.onload = (ev) => push(ref(db, 'messages/global'), { user: currentUser, image: ev.target.result, time: serverTimestamp() });
            r.readAsDataURL(file);
        };
    </script>
</body>
</html>
