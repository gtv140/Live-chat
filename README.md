<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Prime Solutions</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        /* Theme Variables */
        :root {
            --bg: #f4f7f6; --sidebar: #ffffff; --text: #333; --card: #ffffff;
            --primary: #6a11cb; --accent: #2575fc; --gold: #d4af37;
            --msg-mine: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%);
            --msg-other: #e9ecef; --input-bg: #ffffff;
        }

        [data-theme="dark"] {
            --bg: #080810; --sidebar: #0f0f1e; --text: #eee; --card: #161625;
            --primary: #8A2BE2; --accent: #2575fc; --gold: #FFD700;
            --msg-mine: linear-gradient(135deg, #8A2BE2 0%, #4b0082 100%);
            --msg-other: #1e1e2e; --input-bg: #0c0c14;
        }

        * { box-sizing: border-box; transition: background 0.3s, color 0.3s; }
        body { margin: 0; background: var(--bg); color: var(--text); font-family: 'Rajdhani', sans-serif; height: 100vh; overflow: hidden; }
        
        .app-wrapper { display: flex; height: 100vh; }

        /* Sidebar */
        .sidebar { 
            width: 280px; background: var(--sidebar); border-right: 1px solid rgba(0,0,0,0.1); 
            display: flex; flex-direction: column; padding: 20px; z-index: 1000; transition: 0.4s;
        }
        [data-theme="dark"] .sidebar { border-right: 1px solid rgba(255,255,255,0.05); }

        .main-container { flex: 1; display: flex; flex-direction: column; height: 100vh; position: relative; }

        .header { 
            padding: 15px 20px; border-bottom: 1px solid rgba(0,0,0,0.05); 
            display: flex; align-items: center; background: var(--sidebar);
        }

        /* Theme Toggle Button */
        .theme-btn { 
            margin-left: auto; background: var(--primary); color: white; border: none; 
            padding: 8px 15px; border-radius: 20px; cursor: pointer; font-family: 'Orbitron'; font-size: 0.7rem;
        }

        #messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; }

        /* Chat Bubbles Fix */
        .msg-card { max-width: 75%; padding: 12px 16px; border-radius: 18px; position: relative; font-size: 0.95rem; }
        .mine { align-self: flex-end; background: var(--msg-mine); color: white; border-bottom-right-radius: 4px; }
        .others { align-self: flex-start; background: var(--msg-other); color: var(--text); border-bottom-left-radius: 4px; }

        .sender-info { font-size: 0.7rem; color: var(--gold); font-weight: bold; margin-bottom: 4px; display: block; }

        /* Input Bar */
        .input-box { padding: 15px; background: var(--sidebar); display: flex; gap: 10px; align-items: center; border-top: 1px solid rgba(0,0,0,0.05); }
        #msgInput { 
            flex: 1; background: var(--bg); border: 1px solid rgba(0,0,0,0.1); 
            padding: 12px 20px; border-radius: 25px; color: var(--text); outline: none; 
        }
        [data-theme="dark"] #msgInput { border-color: #333; }

        .send-btn { 
            background: var(--gold); border: none; width: 45px; height: 45px; 
            border-radius: 50%; cursor: pointer; font-size: 1.2rem; display: flex; align-items: center; justify-content: center;
        }

        @media (max-width: 768px) {
            .sidebar { position: absolute; left: -280px; height: 100%; box-shadow: 5px 0 15px rgba(0,0,0,0.1); }
            .sidebar.open { left: 0; }
            .msg-card { max-width: 85%; }
        }

        .nav-item { padding: 12px; cursor: pointer; border-radius: 10px; margin-bottom: 5px; color: var(--text); opacity: 0.7; }
        .nav-item.active { background: var(--primary); color: white; opacity: 1; }
    </style>
</head>
<body data-theme="light">

    <div class="app-wrapper">
        <div class="sidebar" id="sidebar">
            <h2 style="font-family:'Orbitron'; color:var(--primary); font-size:1.2rem;">PRIME</h2>
            <div class="nav-item active">🌐 Global Hub</div>
            <div class="nav-item" onclick="location.reload()">🔄 Logout</div>
            <div id="online-list" style="margin-top:20px;"></div>
        </div>

        <div class="main-container">
            <div class="header">
                <button onclick="document.getElementById('sidebar').classList.toggle('open')" style="background:none; border:1px solid var(--primary); padding:5px 10px; margin-right:10px; border-radius:5px;">☰</button>
                <div style="font-family:'Orbitron'; font-size:0.8rem; color:var(--gold);"># LIVE-CONNECT</div>
                <button class="theme-btn" onclick="toggleTheme()">🌓 THEME</button>
            </div>

            <div id="messages"></div>

            <div class="input-box">
                <input type="text" id="msgInput" placeholder="Write something, sweetie...">
                <button class="send-btn" onclick="doSend()">➔</button>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        // Firebase Config (Puraane wala hi use ho raha hai)
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
        const user = "User_" + Math.floor(Math.random() * 1000);

        window.toggleTheme = () => {
            const body = document.body;
            const current = body.getAttribute('data-theme');
            body.setAttribute('data-theme', current === 'light' ? 'dark' : 'light');
        };

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            push(ref(db, 'messages/global'), { user: user, text: inp.value, time: serverTimestamp() });
            inp.value = "";
        };

        onChildAdded(ref(db, 'messages/global'), (snap) => {
            const d = snap.val();
            const isMe = d.user === user;
            const mBox = document.getElementById('messages');
            const html = `
                <div class="msg-card ${isMe ? 'mine' : 'others'}">
                    ${!isMe ? `<span class="sender-info">${d.user}</span>` : ''}
                    ${d.text}
                </div>`;
            mBox.insertAdjacentHTML('beforeend', html);
            mBox.scrollTop = mBox.scrollHeight;
        });
    </script>
</body>
</html>
