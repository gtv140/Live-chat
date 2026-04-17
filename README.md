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

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        
        body, html { 
            margin: 0; padding: 0; width: 100%; height: 100%; 
            overflow: hidden; background: var(--bg); color: var(--text);
            font-family: 'Inter', sans-serif;
        }

        .app-shell { display: flex; flex-direction: column; height: 100vh; width: 100vw; position: relative; }

        .app-header {
            height: 60px; background: var(--nav-bg); border-bottom: 1px solid var(--border);
            display: flex; align-items: center; padding: 0 15px; justify-content: space-between;
            z-index: 1000; flex-shrink: 0;
        }

        .logo { font-family: 'Orbitron'; font-size: 1.1rem; color: var(--primary); font-weight: bold; }

        /* SCROLL AREA FIXED */
        .app-content {
            flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px;
            padding-bottom: 150px; /* Extra space for floating input and nav */
            -webkit-overflow-scrolling: touch;
        }

        .msg-container { display: flex; flex-direction: column; width: 100%; margin-bottom: 5px; }
        .msg-bubble { 
            max-width: 80%; padding: 12px 16px; border-radius: 20px; 
            font-size: 0.95rem; line-height: 1.4; position: relative;
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
        }
        .mine { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 4px; margin-right: 5px; }
        .others { align-self: flex-start; background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; margin-left: 5px; }
        
        .user-name { font-size: 0.75rem; font-weight: 700; margin-bottom: 4px; color: var(--accent); }
        .del-msg { font-size: 0.7rem; margin-top: 6px; opacity: 0.6; cursor: pointer; color: #ff4757; text-align: right; }

        /* FLOATING INPUT BAR FIXED */
        .input-wrapper {
            position: fixed; bottom: 75px; left: 0; width: 100%;
            padding: 0 15px; z-index: 900;
        }

        .input-bar {
            background: var(--card); border: 1px solid var(--border);
            border-radius: 30px; padding: 8px 15px; display: flex; align-items: center;
            box-shadow: 0 5px 20px rgba(0,0,0,0.1); width: 100%;
        }

        #msgInput { flex: 1; background: none; border: none; color: var(--text); padding: 10px; font-size: 1rem; outline: none; }

        .send-btn {
            background: var(--primary); border: none; width: 45px; height: 45px;
            border-radius: 50%; color: white; display: flex; margin-left: 8px;
            align-items: center; justify-content: center; cursor: pointer;
            box-shadow: 0 4px 12px rgba(106, 17, 203, 0.4); flex-shrink: 0;
        }

        /* BOTTOM NAV FIXED */
        .bottom-nav {
            position: fixed; bottom: 0; left: 0; width: 100%; height: 65px;
            background: var(--nav-bg); border-top: 1px solid var(--border);
            display: flex; justify-content: space-around; align-items: center;
            z-index: 1000; padding-bottom: env(safe-area-inset-bottom);
        }

        .nav-item { display: flex; flex-direction: column; align-items: center; font-size: 0.7rem; color: #8e8e93; cursor: pointer; }
        .nav-item.active { color: var(--primary); }
        .nav-icon { font-size: 1.5rem; margin-bottom: 2px; }
    </style>
</head>
<body data-theme="light">

    <div class="app-shell">
        <header class="app-header">
            <div class="logo">LIVE CONNECT</div>
            <div onclick="toggleTheme()" style="font-size: 1.3rem; cursor: pointer;">🌓</div>
        </header>

        <main class="app-content" id="messages"></main>

        <div class="input-wrapper">
            <div style="display: flex; align-items: center; width: 100%;">
                <div class="input-bar">
                    <label for="imgInput" style="cursor: pointer; font-size: 1.2rem; margin-right: 5px;">🖼️</label>
                    <input type="file" id="imgInput" style="display:none" accept="image/*">
                    <input type="text" id="msgInput" placeholder="Message, sweetie..." autocomplete="off">
                </div>
                <button class="send-btn" onclick="doSend()">➔</button>
            </div>
        </div>

        <nav class="bottom-nav">
            <div class="nav-item active">
                <span class="nav-icon">💬</span>
                <span>Chats</span>
            </div>
            <div class="nav-item" onclick="alert('Security Active 🛡️')">
                <span class="nav-icon">🛡️</span>
                <span>Security</span>
            </div>
            <div class="nav-item" onclick="location.reload()">
                <span class="nav-icon">👤</span>
                <span>Logout</span>
            </div>
        </nav>
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
        const myName = "User_" + Math.floor(Math.random()*999);

        window.toggleTheme = () => {
            const current = document.body.getAttribute('data-theme');
            document.body.setAttribute('data-theme', current === 'light' ? 'dark' : 'light');
        };

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            push(ref(db, 'messages/global'), { user: myName, text: inp.value, time: serverTimestamp() });
            inp.value = "";
        };

        window.deleteMsg = (key) => {
            if(confirm("Delete this message?")) remove(ref(db, `messages/global/${key}`));
        };

        onChildAdded(ref(db, 'messages/global'), (snap) => {
            const d = snap.val();
            if(!d.text) return;
            const isMe = d.user === myName;
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
    </script>
</body>
</html>
