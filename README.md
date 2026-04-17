<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Live Connect | Prime Solutions</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500&family=Inter:wght@400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #6a11cb; --bg: #f8f9fa; --text: #1a1a1a; 
            --card: #ffffff; --border: #eeeeee; --nav: #ffffff;
        }

        [data-theme="dark"] {
            --bg: #0b0b0e; --text: #f5f5f5; --card: #16161d;
            --border: #25252b; --nav: #16161d; --primary: #8e2de2;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; outline: none; }
        
        body, html { 
            margin: 0; padding: 0; width: 100%; height: 100%; 
            overflow: hidden; background: var(--bg); color: var(--text);
            font-family: 'Inter', sans-serif;
        }

        /* Full App Wrapper */
        .app-shell {
            display: flex; flex-direction: column;
            height: 100vh; width: 100vw;
            position: relative;
        }

        /* Fix Top Header */
        .app-header {
            height: 60px; background: var(--nav);
            border-bottom: 1px solid var(--border);
            display: flex; align-items: center; padding: 0 15px;
            justify-content: space-between; flex-shrink: 0;
            z-index: 1000;
        }

        .logo { font-family: 'Orbitron'; font-size: 1rem; color: var(--primary); font-weight: bold; }

        /* FIXED MESSAGE BOX - ONLY THIS SCROLLS */
        #messages {
            flex: 1; /* Pure space header aur input ke beech ka cover karega */
            overflow-y: auto; /* Sirf isi box ke andar scroll hoga */
            padding: 15px;
            display: flex; flex-direction: column; gap: 10px;
            background: transparent;
            -webkit-overflow-scrolling: touch;
        }

        .msg-wrap { display: flex; flex-direction: column; width: 100%; }
        .msg-bubble { 
            max-width: 75%; padding: 10px 14px; border-radius: 18px; 
            font-size: 0.92rem; line-height: 1.4; position: relative;
            box-shadow: 0 2px 5px rgba(0,0,0,0.05);
        }
        .mine { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 4px; }
        .others { align-self: flex-start; background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        
        .sender { font-size: 0.7rem; font-weight: bold; margin-bottom: 3px; color: var(--primary); opacity: 0.8; }
        .del-btn { font-size: 0.65rem; color: #ff4757; margin-top: 5px; cursor: pointer; display: block; text-align: right; }

        /* FIXED INPUT CONTAINER AT THE BOTTOM */
        .input-shell {
            background: var(--nav);
            border-top: 1px solid var(--border);
            padding: 10px 15px;
            padding-bottom: calc(10px + env(safe-area-inset-bottom));
            display: flex; flex-direction: column;
            gap: 10px; flex-shrink: 0;
        }

        .input-row { display: flex; align-items: center; gap: 10px; }

        .input-box {
            flex: 1; background: var(--bg); border: 1px solid var(--border);
            border-radius: 25px; padding: 8px 15px; display: flex; align-items: center;
        }

        #msgInput { flex: 1; background: none; border: none; color: var(--text); font-size: 1rem; padding: 5px; }

        .send-btn {
            background: var(--primary); border: none; width: 42px; height: 42px;
            border-radius: 50%; color: white; display: flex; 
            align-items: center; justify-content: center; cursor: pointer;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }

        /* BOTTOM NAVIGATION BAR */
        .bottom-nav {
            height: 60px; background: var(--nav); border-top: 1px solid var(--border);
            display: flex; justify-content: space-around; align-items: center;
            flex-shrink: 0;
        }

        .nav-item { display: flex; flex-direction: column; align-items: center; font-size: 0.65rem; color: #888; }
        .active { color: var(--primary); font-weight: bold; }
        .nav-icon { font-size: 1.4rem; }
    </style>
</head>
<body data-theme="light">

    <div class="app-shell">
        <header class="app-header">
            <div class="logo">LIVE CONNECT</div>
            <div onclick="toggleTheme()" style="cursor:pointer; font-size: 1.2rem;">🌓</div>
        </header>

        <main id="messages"></main>

        <div class="input-shell">
            <div class="input-row">
                <label for="imgInp" style="font-size: 1.3rem; cursor: pointer;">🖼️</label>
                <input type="file" id="imgInp" style="display:none" accept="image/*">
                <div class="input-box">
                    <input type="text" id="msgInput" placeholder="Message, sweetie..." autocomplete="off">
                </div>
                <button class="send-btn" onclick="doSend()">➔</button>
            </div>
        </div>

        <nav class="bottom-nav">
            <div class="nav-item active"><span class="nav-icon">💬</span><span>Chats</span></div>
            <div class="nav-item"><span class="nav-icon">🛡️</span><span>Security</span></div>
            <div class="nav-item" onclick="location.reload()"><span class="nav-icon">👤</span><span>Logout</span></div>
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
            document.body.setAttribute('data-theme', document.body.getAttribute('data-theme') === 'light' ? 'dark' : 'light');
        };

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            push(ref(db, 'messages/global'), { user: myName, text: inp.value, time: serverTimestamp() });
            inp.value = "";
        };

        window.deleteMsg = (id) => { if(confirm("Delete, sweetie?")) remove(ref(db, `messages/global/${id}`)); };

        const mBox = document.getElementById('messages');
        onChildAdded(ref(db, 'messages/global'), (snap) => {
            const d = snap.val();
            const isMe = d.user === myName;
            const html = `
                <div class="msg-wrap" id="msg-${snap.key}">
                    <div class="msg-bubble ${isMe ? 'mine' : 'others'}">
                        ${!isMe ? `<div class="sender">${d.user}</div>` : ''}
                        <div>${d.text}</div>
                        ${isMe ? `<span class="del-btn" onclick="deleteMsg('${snap.key}')">🗑️ Delete</span>` : ''}
                    </div>
                </div>`;
            mBox.insertAdjacentHTML('beforeend', html);
            mBox.scrollTo({ top: mBox.scrollHeight, behavior: 'smooth' }); // Auto scroll fix
        });

        onChildRemoved(ref(db, 'messages/global'), (snap) => {
            const el = document.getElementById(`msg-${snap.key}`);
            if(el) el.remove();
        });

        document.getElementById('msgInput').addEventListener('keypress', (e) => { if(e.key === 'Enter') doSend(); });
    </script>
</body>
</html>
