<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Prime Solutions Elite</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@500;700&display=swap" rel="stylesheet">
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

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body, html { margin: 0; padding: 0; width: 100%; height: 100%; overflow: hidden; font-family: 'Rajdhani', sans-serif; background: var(--bg); color: var(--text); }
        
        .app-container { display: flex; width: 100%; height: 100%; position: relative; }

        /* Sidebar Responsive */
        .sidebar { 
            width: 260px; background: var(--sidebar); border-right: 1px solid var(--border); 
            display: flex; flex-direction: column; height: 100%; transition: 0.3s ease;
            position: absolute; left: 0; top: 0; z-index: 2000; transform: translateX(-100%);
        }
        .sidebar.open { transform: translateX(0); box-shadow: 5px 0 15px rgba(0,0,0,0.2); }

        .main-chat { flex: 1; display: flex; flex-direction: column; height: 100%; width: 100%; position: relative; overflow: hidden; }

        .header { 
            height: 60px; background: var(--sidebar); border-bottom: 1px solid var(--border); 
            display: flex; align-items: center; padding: 0 15px; gap: 10px; flex-shrink: 0; z-index: 10;
        }

        /* Message Area - SCROLL FIXED */
        #messages { 
            flex: 1; overflow-y: scroll; padding: 15px; display: flex; 
            flex-direction: column; gap: 12px; background: transparent;
            -webkit-overflow-scrolling: touch; /* Smooth scroll for mobile */
        }

        .msg-wrap { display: flex; flex-direction: column; width: 100%; }
        .msg { max-width: 80%; padding: 10px 14px; border-radius: 15px; position: relative; box-shadow: 0 1px 3px rgba(0,0,0,0.1); }
        .mine { align-self: flex-end; background: var(--msg-mine); color: white; border-bottom-right-radius: 2px; }
        .others { align-self: flex-start; background: var(--msg-other); border-bottom-left-radius: 2px; border: 1px solid var(--border); }
        
        .user-tag { font-family: 'Orbitron'; font-size: 0.65rem; color: var(--gold); font-weight: bold; margin-bottom: 3px; }
        .msg-actions { margin-top: 5px; font-size: 0.75rem; display: flex; gap: 10px; opacity: 0.8; }
        .del-btn { color: #ff4757; cursor: pointer; font-weight: bold; }

        /* Input Bar Fix */
        .input-area { 
            background: var(--sidebar); padding: 10px 15px; border-top: 1px solid var(--border); 
            display: flex; align-items: center; gap: 10px; flex-shrink: 0;
        }

        #msgInput { 
            flex: 1; border: 1px solid var(--border); background: var(--bg); color: var(--text);
            padding: 12px 18px; border-radius: 25px; outline: none; font-size: 1rem;
        }

        .btn-action { font-size: 1.3rem; cursor: pointer; color: var(--gold); border: none; background: none; }
        .send-btn { background: var(--gold); border: none; width: 45px; height: 45px; border-radius: 50%; color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 1.2rem; }

        .overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.4); z-index: 1500; }
        .overlay.active { display: block; }
    </style>
</head>
<body data-theme="light">

    <div class="overlay" id="overlay" onclick="toggleMenu()"></div>

    <div class="app-container">
        <div class="sidebar" id="sidebar">
            <div style="padding: 20px; border-bottom: 1px solid var(--border);">
                <h2 style="font-family:'Orbitron'; color:var(--primary); margin:0; font-size:1rem;">PRIME SOLUTIONS</h2>
            </div>
            <div style="padding: 15px; flex: 1;">
                <div style="padding: 12px; font-weight: bold; color: var(--gold); cursor: pointer;">🌐 GLOBAL HUB</div>
                <div style="padding: 12px; opacity: 0.7; cursor: pointer;">🛡️ SECURITY POLICY</div>
            </div>
            <div style="padding: 15px; border-top: 1px solid var(--border);">
                <button onclick="location.reload()" style="width:100%; padding:10px; border:none; background:#ff4757; color:white; border-radius:8px; cursor:pointer; font-weight:bold;">LOGOUT</button>
            </div>
        </div>

        <div class="main-chat">
            <header class="header">
                <button class="btn-action" onclick="toggleMenu()" style="color:var(--primary)">☰</button>
                <div style="font-family:'Orbitron'; font-size:0.8rem; font-weight:bold; letter-spacing:1px;"># LIVE-CONNECT</div>
                <button onclick="toggleTheme()" style="margin-left:auto; background:none; border:1px solid var(--border); padding:6px 12px; border-radius:20px; font-size:0.7rem; cursor:pointer; font-weight:bold;">🌓 THEME</button>
            </header>

            <div id="messages"></div>

            <div class="input-area">
                <label for="imgInput" class="btn-action">🖼️</label>
                <input type="file" id="imgInput" style="display:none" accept="image/*">
                <button class="btn-action" id="voice-btn">🎤</button>
                <input type="text" id="msgInput" placeholder="Write something, sweetie..." autocomplete="off">
                <button onclick="doSend()" class="send-btn">➔</button>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, onChildRemoved, serverTimestamp, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
        const myUser = "User_" + Math.floor(Math.random()*999);

        window.toggleMenu = () => {
            document.getElementById('sidebar').classList.toggle('open');
            document.getElementById('overlay').classList.toggle('active');
        };

        window.toggleTheme = () => {
            const current = document.body.getAttribute('data-theme');
            document.body.setAttribute('data-theme', current === 'light' ? 'dark' : 'light');
        };

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            const val = inp.value.trim();
            if(!val) return;
            push(ref(db, 'messages/global'), { user: myUser, text: val, time: serverTimestamp() });
            inp.value = "";
        };

        window.deleteMsg = (id) => {
            if(confirm("Delete this message, sweetie?")) {
                remove(ref(db, `messages/global/${id}`));
            }
        };

        // UI rendering
        onChildAdded(ref(db, 'messages/global'), (snap) => {
            const d = snap.val();
            if(!d || !d.text && !d.image) return; // Fix for undefined
            
            const isMe = d.user === myUser;
            const mBox = document.getElementById('messages');
            
            const html = `
                <div class="msg-wrap" id="msg-${snap.key}">
                    <div class="msg ${isMe ? 'mine' : 'others'}">
                        ${!isMe ? `<span class="user-tag">${d.user}</span>` : ''}
                        <div>${d.text || ''} ${d.image ? `<img src="${d.image}" style="max-width:100%; border-radius:8px;">` : ''}</div>
                        <div class="msg-actions">
                            <span onclick="deleteMsg('${snap.key}')" class="del-btn">🗑️ Delete</span>
                            <span>❤️</span>
                        </div>
                    </div>
                </div>`;
            mBox.insertAdjacentHTML('beforeend', html);
            mBox.scrollTop = mBox.scrollHeight;
        });

        onChildRemoved(ref(db, 'messages/global'), (snap) => {
            const el = document.getElementById(`msg-${snap.key}`);
            if(el) el.remove();
        });

        document.getElementById('msgInput').onkeypress = (e) => { if(e.key === 'Enter') doSend(); };
    </script>
</body>
</html>
