<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Live Connect App</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500&family=Inter:wght@400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #6a11cb; --accent: #ffd700; --bg: #f8f9fa;
            --text: #1a1a1a; --card: #ffffff; --nav-bg: #ffffff;
            --border: #eeeeee;
        }

        [data-theme="dark"] {
            --bg: #0b0b0e; --text: #f5f5f5; --card: #16161d;
            --nav-bg: #16161d; --border: #25252b; --primary: #8e2de2;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; outline: none; }
        
        body, html { 
            margin: 0; padding: 0; width: 100%; height: 100%; 
            overflow: hidden; background: var(--bg); color: var(--text);
            font-family: 'Inter', sans-serif;
        }

        /* App Layout Structure */
        .app-shell {
            display: flex; flex-direction: column;
            height: 100vh; width: 100vw;
            position: relative;
        }

        /* Top Bar */
        .app-header {
            height: 60px; background: var(--nav-bg);
            border-bottom: 1px solid var(--border);
            display: flex; align-items: center; padding: 0 15px;
            justify-content: space-between; flex-shrink: 0;
            z-index: 100;
        }

        .logo { font-family: 'Orbitron'; font-size: 1rem; color: var(--primary); letter-spacing: 1px; }

        /* Main Scrollable Content */
        .app-content {
            flex: 1; overflow-y: auto; padding: 15px;
            display: flex; flex-direction: column; gap: 12px;
            padding-bottom: 140px; /* Space for input + bottom nav */
            -webkit-overflow-scrolling: touch;
        }

        /* Chat Bubbles */
        .msg-container { display: flex; flex-direction: column; width: 100%; animation: fadeIn 0.3s ease; }
        .msg-bubble { 
            max-width: 75%; padding: 10px 14px; border-radius: 18px; 
            font-size: 0.95rem; line-height: 1.4; position: relative;
            box-shadow: 0 2px 4px rgba(0,0,0,0.04);
        }
        .mine { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 4px; }
        .others { align-self: flex-start; background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        
        .user-name { font-size: 0.7rem; font-weight: 600; margin-bottom: 3px; color: var(--primary); }
        .del-msg { font-size: 0.65rem; margin-top: 5px; opacity: 0.5; cursor: pointer; text-align: right; display: block; }

        /* Floating Input System */
        .input-container {
            position: fixed; bottom: 70px; left: 0; width: 100%;
            padding: 10px 15px; background: transparent;
            display: flex; gap: 8px; align-items: center;
        }

        .input-bar {
            flex: 1; background: var(--card); border: 1px solid var(--border);
            border-radius: 25px; padding: 10px 18px; display: flex; align-items: center;
            box-shadow: 0 4px 12px rgba(0,0,0,0.08);
        }

        #msgInput { flex: 1; background: none; border: none; color: var(--text); font-size: 0.95rem; }

        .send-btn {
            background: var(--primary); border: none; width: 45px; height: 45px;
            border-radius: 50%; color: white; display: flex; 
            align-items: center; justify-content: center; cursor: pointer;
            box-shadow: 0 4px 10px rgba(106, 17, 203, 0.3);
        }

        /* Bottom Navigation Bar (Real App Style) */
        .bottom-nav {
            position: fixed; bottom: 0; left: 0; width: 100%; height: 65px;
            background: var(--nav-bg); border-top: 1px solid var(--border);
            display: flex; justify-content: space-around; align-items: center;
            z-index: 100;
        }

        .nav-item { 
            display: flex; flex-direction: column; align-items: center; 
            font-size: 0.65rem; color: #888; text-decoration: none; cursor: pointer;
        }
        .nav-item.active { color: var(--primary); }
        .nav-icon { font-size: 1.4rem; margin-bottom: 2px; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* Modal / Settings Page Overlay */
        #settings-page {
            position: fixed; inset: 0; background: var(--bg); z-index: 500;
            display: none; padding: 20px; flex-direction: column;
        }
    </style>
</head>
<body data-theme="light">

    <div class="app-shell">
        <header class="app-header">
            <div class="logo">LIVE CONNECT</div>
            <div onclick="toggleTheme()" style="font-size: 1.2rem; cursor: pointer;">🌓</div>
        </header>

        <main class="app-content" id="messages"></main>

        <div class="input-container">
            <div class="input-bar">
                <label for="imgInput" style="margin-right: 10px; cursor: pointer;">🖼️</label>
                <input type="file" id="imgInput" style="display:none" accept="image/*">
                <input type="text" id="msgInput" placeholder="Message..." autocomplete="off">
            </div>
            <button class="send-btn" onclick="doSend()">➔</button>
        </div>

        <nav class="bottom-nav">
            <div class="nav-item active" onclick="closePages()">
                <span class="nav-icon">💬</span>
                <span>Chats</span>
            </div>
            <div class="nav-item" onclick="openSettings()">
                <span class="nav-icon">🛡️</span>
                <span>Security</span>
            </div>
            <div class="nav-item" onclick="location.reload()">
                <span class="nav-icon">👤</span>
                <span>Logout</span>
            </div>
        </nav>
    </div>

    <div id="settings-page">
        <h2 style="font-family: 'Orbitron'; color: var(--primary);">PRIME SOLUTIONS</h2>
        <p><b>Security Level:</b> Encrypted</p>
        <p><b>Version:</b> 5.0 App Edition</p>
        <hr style="width:100%; border:0; border-top:1px solid var(--border);">
        <button onclick="closePages()" style="margin-top:20px; padding:10px; background:var(--primary); color:white; border:none; border-radius:10px;">Back to Chat</button>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp, remove, onChildRemoved } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
        const myId = "User_" + Math.floor(Math.random()*999);

        // UI Logic
        window.toggleTheme = () => {
            const body = document.body;
            body.setAttribute('data-theme', body.getAttribute('data-theme') === 'light' ? 'dark' : 'light');
        };

        window.openSettings = () => document.getElementById('settings-page').style.display = 'flex';
        window.closePages = () => document.getElementById('settings-page').style.display = 'none';

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            push(ref(db, 'messages/global'), { user: myId, text: inp.value, time: serverTimestamp() });
            inp.value = "";
        };

        window.deleteMsg = (key) => {
            if(confirm("Delete message?")) remove(ref(db, `messages/global/${key}`));
        };

        // Firebase Listeners
        onChildAdded(ref(db, 'messages/global'), (snap) => {
            const d = snap.val();
            const isMe = d.user === myId;
            const mBox = document.getElementById('messages');
            
            const html = `
                <div class="msg-container" id="msg-${snap.key}">
                    <div class="msg-bubble ${isMe ? 'mine' : 'others'}">
                        ${!isMe ? `<div class="user-name">${d.user}</div>` : ''}
                        <div>${d.text}</div>
                        ${isMe ? `<span class="del-msg" onclick="deleteMsg('${snap.key}')">Delete</span>` : ''}
                    </div>
                </div>`;
            mBox.insertAdjacentHTML('beforeend', html);
            mBox.scrollTop = mBox.scrollHeight;
        });

        onChildRemoved(ref(db, 'messages/global'), (snap) => {
            const el = document.getElementById(`msg-${snap.key}`);
            if(el) el.remove();
        });

        document.getElementById('msgInput').addEventListener('keypress', (e) => {
            if(e.key === 'Enter') doSend();
        });

        document.getElementById('imgInput').onchange = (e) => {
            const file = e.target.files[0];
            const reader = new FileReader();
            reader.onload = (ev) => push(ref(db, 'messages/global'), { user: myId, text: `<img src="${ev.target.result}" style="max-width:100%; border-radius:10px;">`, time: serverTimestamp() });
            reader.readAsDataURL(file);
        };
    </script>
</body>
</html>
