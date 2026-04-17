<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Prime Solutions</title>
    <link href="https://fonts.googleapis.com/css2?family=Syncopate:wght@700&family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --accent: #00f2fe; --primary: #6a11cb; --bg: #050505;
            --glass: rgba(255, 255, 255, 0.08); --border: rgba(255, 255, 255, 0.15);
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Poppins', sans-serif; -webkit-tap-highlight-color: transparent; }
        
        body, html { height: 100%; background: var(--bg); color: white; overflow: hidden; }

        /* Animated Background Mesh */
        .bg-animate {
            position: fixed; inset: 0; z-index: -1;
            background: radial-gradient(circle at 50% 50%, #1a1a2e 0%, #050505 100%);
        }
        .bg-animate::after {
            content: ""; position: absolute; top: -50%; left: -50%; width: 200%; height: 200%;
            background: conic-gradient(from 0deg, transparent, var(--primary), transparent);
            animation: rotate 10s linear infinite; opacity: 0.1;
        }
        @keyframes rotate { 100% { transform: rotate(360deg); } }

        /* Auth Screen (Login/Signup) */
        #auth-screen {
            position: fixed; inset: 0; z-index: 5000; background: rgba(0,0,0,0.9);
            display: flex; align-items: center; justify-content: center; backdrop-filter: blur(20px);
        }
        .auth-card {
            background: var(--glass); padding: 40px 30px; border-radius: 30px; width: 90%; max-width: 400px;
            border: 1px solid var(--border); text-align: center; box-shadow: 0 20px 50px rgba(0,0,0,0.5);
        }
        .auth-card h1 { font-family: 'Syncopate', sans-serif; font-size: 22px; margin-bottom: 25px; color: var(--accent); }
        .input-group { position: relative; margin-bottom: 15px; }
        .input-group input {
            width: 100%; padding: 14px; background: rgba(0,0,0,0.3); border: 1px solid var(--border);
            border-radius: 15px; color: white; outline: none; transition: 0.3s;
        }
        .input-group input:focus { border-color: var(--accent); box-shadow: 0 0 15px rgba(0,242,254,0.2); }
        .auth-btn {
            width: 100%; padding: 14px; border-radius: 15px; border: none;
            background: linear-gradient(90deg, var(--primary), var(--secondary));
            color: white; font-weight: 600; cursor: pointer; margin-top: 10px;
        }

        /* App Shell */
        .app-shell { display: flex; flex-direction: column; height: 100vh; position: relative; }

        /* Modern Header */
        .header {
            height: 70px; background: rgba(255,255,255,0.03); backdrop-filter: blur(15px);
            display: flex; align-items: center; justify-content: space-between; padding: 0 20px;
            border-bottom: 1px solid var(--border); z-index: 1000;
        }
        .logo { font-family: 'Syncopate', sans-serif; font-size: 16px; letter-spacing: 2px; color: var(--accent); }

        /* User Online List (Side Drawer) */
        #user-drawer {
            position: fixed; right: -100%; top: 0; bottom: 0; width: 80%; max-width: 300px;
            background: rgba(10, 10, 10, 0.95); backdrop-filter: blur(25px); z-index: 2000;
            transition: 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); padding: 25px; border-left: 1px solid var(--border);
        }
        #user-drawer.open { right: 0; }
        .user-pill {
            padding: 15px; background: var(--glass); border-radius: 18px; margin-bottom: 10px;
            display: flex; justify-content: space-between; align-items: center; border: 1px solid transparent;
        }
        .user-pill:hover { border-color: var(--accent); background: rgba(255,255,255,0.1); }

        /* Chat Window */
        #chat-window {
            flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px;
            scrollbar-width: none; -webkit-overflow-scrolling: touch; padding-bottom: 100px;
        }

        /* Message Bubbles */
        .msg { max-width: 75%; padding: 12px 18px; border-radius: 22px; position: relative; animation: msgPop 0.4s ease forwards; }
        @keyframes msgPop { from { transform: scale(0.8); opacity: 0; } to { transform: scale(1); opacity: 1; } }
        
        .mine { align-self: flex-end; background: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%); border-bottom-right-radius: 4px; box-shadow: 0 5px 15px rgba(106, 17, 203, 0.3); }
        .others { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .sender-tag { font-size: 10px; font-weight: bold; margin-bottom: 5px; color: var(--accent); display: block; }
        .msg img { max-width: 100%; border-radius: 15px; margin-top: 10px; border: 1px solid var(--border); }

        /* Premium Input Bar */
        .bottom-bar {
            position: fixed; bottom: 0; left: 0; right: 0; height: 85px;
            background: rgba(5, 5, 5, 0.8); backdrop-filter: blur(20px);
            display: flex; align-items: center; padding: 0 15px; gap: 12px;
            border-top: 1px solid var(--border); z-index: 1000;
        }
        .input-wrap { flex: 1; position: relative; }
        #msg-input {
            width: 100%; height: 50px; background: var(--glass); border: 1px solid var(--border);
            border-radius: 25px; padding: 0 20px; color: white; outline: none; transition: 0.3s;
        }
        #msg-input:focus { border-color: var(--accent); }
        .send-btn {
            width: 50px; height: 50px; border-radius: 50%; border: none;
            background: var(--accent); color: #000; font-size: 20px; cursor: pointer;
            display: flex; align-items: center; justify-content: center; transition: 0.3s;
        }
        .send-btn:active { transform: scale(0.9); }

        .admin-del { font-size: 10px; color: #ff4757; margin-top: 8px; cursor: pointer; opacity: 0.7; }
    </style>
</head>
<body>

    <div class="bg-animate"></div>

    <div id="auth-screen">
        <div class="auth-card">
            <h1>LIVE CONNECT</h1>
            <p style="font-size: 12px; margin-bottom: 25px; opacity: 0.6;">POWERED BY PRIME SOLUTIONS</p>
            <div class="input-group">
                <input type="text" id="auth-u" placeholder="Enter Username">
            </div>
            <div class="input-group">
                <input type="password" id="auth-p" placeholder="Enter Password">
            </div>
            <button class="auth-btn" onclick="startApp()">CONNECT NOW</button>
        </div>
    </div>

    <div id="user-drawer">
        <div style="display:flex; justify-content:space-between; margin-bottom:30px;">
            <h2 style="font-family: 'Syncopate'; font-size: 14px;">ACTIVE USERS</h2>
            <span onclick="toggleDrawer()" style="cursor:pointer">✕</span>
        </div>
        <div class="user-pill" onclick="switchChat('global')">🌍 Global Hub</div>
        <div id="online-users-list"></div>
    </div>

    <div class="app-shell">
        <header class="header">
            <div class="logo">PRIME SOLUTIONS</div>
            <div style="display:flex; gap:15px; align-items:center;">
                <span id="target-label" style="font-size:11px; background:var(--accent); color:#000; padding:3px 10px; border-radius:20px; font-weight:600;">GLOBAL</span>
                <div onclick="toggleDrawer()" style="cursor:pointer; font-size:22px;">👥</div>
            </div>
        </header>

        <main id="chat-window"></main>

        <div class="bottom-bar">
            <label for="img-file" style="cursor:pointer; font-size:24px;">🖼️</label>
            <input type="file" id="img-file" hidden accept="image/*" onchange="sendImage(this)">
            <div class="input-wrap">
                <input type="text" id="msg-input" placeholder="Type message, sweetie...">
            </div>
            <button class="send-btn" onclick="sendMsg()">➔</button>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp, set, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const config = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
        };

        const app = initializeApp(config);
        const db = getDatabase(app);
        let me = "", chatTarget = "global", isAdmin = false;

        window.startApp = () => {
            const u = document.getElementById('auth-u').value.trim();
            const p = document.getElementById('auth-p').value.trim();
            if(!u || !p) return;
            me = u;
            if(p === "admin786") isAdmin = true;
            document.getElementById('auth-screen').style.display = 'none';
            set(ref(db, 'online_v3/' + me), true);
            onDisconnect(ref(db, 'online_v3/' + me)).remove();
            listenMessages();
        };

        window.toggleDrawer = () => document.getElementById('user-drawer').classList.toggle('open');

        window.switchChat = (t) => {
            chatTarget = t;
            document.getElementById('target-label').innerText = t.toUpperCase();
            document.getElementById('chat-window').innerHTML = "";
            toggleDrawer();
            listenMessages();
        };

        window.sendMsg = () => {
            const inp = document.getElementById('msg-input');
            if(!inp.value.trim()) return;
            const path = chatTarget === 'global' ? 'hub/global' : 'hub/priv/' + getID(me, chatTarget);
            push(ref(db, path), { u: me, m: inp.value, ts: serverTimestamp() });
            inp.value = "";
        };

        window.sendImage = (el) => {
            const reader = new FileReader();
            reader.onload = (e) => {
                const path = chatTarget === 'global' ? 'hub/global' : 'hub/priv/' + getID(me, chatTarget);
                push(ref(db, path), { u: me, img: e.target.result, ts: serverTimestamp() });
            };
            reader.readAsDataURL(el.files[0]);
        };

        window.kill = (id) => {
            if(confirm("Admin Delete?")) {
                const path = chatTarget === 'global' ? 'hub/global/' : 'hub/priv/' + getID(me, chatTarget) + '/';
                remove(ref(db, path + id));
            }
        };

        function listenMessages() {
            const path = chatTarget === 'global' ? 'hub/global' : 'hub/priv/' + getID(me, chatTarget);
            onChildAdded(ref(db, path), (s) => {
                const d = s.val();
                const isMe = d.u === me;
                const win = document.getElementById('chat-window');
                win.innerHTML += `
                    <div class="msg ${isMe ? 'mine' : 'others'}">
                        <span class="sender-tag">${isMe ? 'YOU' : d.u}</span>
                        ${d.m ? `<div>${d.m}</div>` : ''}
                        ${d.img ? `<img src="${d.img}">` : ''}
                        ${(isMe || isAdmin) ? `<div class="admin-del" onclick="kill('${s.key}')">Remove</div>` : ''}
                    </div>
                `;
                win.scrollTop = win.scrollHeight;
            });
        }

        onValue(ref(db, 'online_v3'), (s) => {
            const list = document.getElementById('online-users-list');
            list.innerHTML = "";
            s.forEach(u => {
                if(u.key !== me) list.innerHTML += `
                    <div class="user-pill" onclick="switchChat('${u.key}')">
                        <span>👤 ${u.key}</span>
                        <span style="color:var(--accent); font-size:10px;">●</span>
                    </div>`;
            });
        });

        function getID(a, b) { return a < b ? a + "_" + b : b + "_" + a; }
        document.getElementById('msg-input').onkeypress = (e) => { if(e.key === 'Enter') sendMsg(); };
    </script>
</body>
</html>
