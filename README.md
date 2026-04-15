<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro | Enterprise SaaS</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root { --gold: #FFD700; --purple: #8A2BE2; --dark: #05050a; --glass: rgba(255, 255, 255, 0.03); }
        body { margin: 0; background: var(--dark); color: white; font-family: 'Rajdhani', sans-serif; height: 100vh; display: flex; flex-direction: column; }
        .app-wrapper { display: flex; flex: 1; overflow: hidden; }
        
        /* Sidebar Upgrade */
        .sidebar { width: 280px; background: rgba(0,0,0,0.7); border-right: 1px solid var(--purple); display: flex; flex-direction: column; padding: 20px; backdrop-filter: blur(10px); }
        .sidebar h2 { font-family: 'Orbitron'; font-size: 0.8rem; color: var(--gold); margin: 20px 0 10px; border-bottom: 1px solid rgba(255,255,255,0.1); padding-bottom: 5px; }
        .list-item { padding: 10px; cursor: pointer; border-radius: 8px; margin-bottom: 5px; transition: 0.3s; color: #bbb; font-size: 0.9rem; display: flex; align-items: center; }
        .list-item:hover, .list-item.active { background: var(--purple); color: white; }
        .status-indicator { width: 8px; height: 8px; border-radius: 50%; background: #00ff88; margin-right: 10px; box-shadow: 0 0 5px #00ff88; }

        /* Main Chat Area */
        .main-chat { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #121225, #05050a); }
        .chat-header { padding: 15px 25px; border-bottom: 1px solid rgba(138,43,226,0.3); display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.3); }
        #messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 15px; scroll-behavior: smooth; }

        /* Message Styling */
        .msg-wrap { display: flex; flex-direction: column; max-width: 75%; }
        .mine { align-self: flex-end; align-items: flex-end; }
        .others { align-self: flex-start; }
        .bubble { padding: 12px 18px; border-radius: 20px; font-size: 1rem; position: relative; word-wrap: break-word; }
        .mine .bubble { background: linear-gradient(135deg, var(--purple), #4B0082); border-bottom-right-radius: 2px; }
        .others .bubble { background: var(--glass); border: 1px solid rgba(255,255,255,0.1); border-bottom-left-radius: 2px; }
        .sender { font-size: 0.65rem; color: var(--gold); font-weight: bold; margin-bottom: 3px; }
        .time { font-size: 0.6rem; color: #777; margin-top: 4px; }

        /* Input Bar */
        .input-bar { padding: 20px; background: rgba(0,0,0,0.5); display: flex; gap: 10px; align-items: center; }
        input { flex: 1; background: rgba(255,255,255,0.05); border: 1px solid var(--purple); padding: 12px 20px; border-radius: 30px; color: white; outline: none; }
        .send-btn { background: var(--gold); border: none; padding: 12px 25px; border-radius: 30px; font-weight: bold; cursor: pointer; font-family: 'Orbitron'; font-size: 0.7rem; }

        /* Login */
        #login-screen { position: fixed; inset: 0; background: var(--dark); z-index: 1000; display: flex; align-items: center; justify-content: center; }
        .login-box { background: var(--glass); padding: 50px; border-radius: 30px; border: 1px solid var(--purple); text-align: center; }
    </style>
</head>
<body>

    <div id="login-screen">
        <div class="login-box">
            <h1 style="font-family: 'Orbitron'; color: var(--gold);">LIVE CONNECT</h1>
            <input type="text" id="userInput" placeholder="Username" style="width: 100%; display: block; margin-bottom: 10px;">
            <input type="password" id="passInput" placeholder="Password" style="width: 100%; display: block; margin-bottom: 20px;">
            <button class="send-btn" onclick="handleAuth()" style="width: 100%;">ACCESS SYSTEM</button>
        </div>
    </div>

    <div class="app-wrapper">
        <div class="sidebar">
            <h2 style="margin-top:0">CHANNELS</h2>
            <div class="list-item active" onclick="setTarget('global')"># global-hub</div>
            
            <h2>DIRECT MESSAGES</h2>
            <div id="users-list">
                </div>
        </div>

        <div class="main-chat">
            <div class="chat-header">
                <div id="chat-title" style="font-family: 'Orbitron'; color: var(--gold);"># GLOBAL-HUB</div>
                <div style="font-size: 0.8rem; color: #00ff88;">SYSTEM ENCRYPTED</div>
            </div>
            <div id="messages"></div>
            <div class="input-bar">
                <input type="text" id="msgInput" placeholder="Type message or paste image link...">
                <button class="send-btn" onclick="doSend()">SEND</button>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, get, set, serverTimestamp, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
        let chatTarget = "global"; // Can be 'global' or a username

        window.handleAuth = async () => {
            const user = document.getElementById('userInput').value.trim().toLowerCase();
            const pass = document.getElementById('passInput').value.trim();
            if(!user || !pass) return;

            const userRef = ref(db, 'users/' + user);
            const snap = await get(userRef);
            if(snap.exists() && snap.val().password !== pass) return alert("Access Denied!");
            
            await set(userRef, { password: pass, online: true });
            currentUser = user;
            document.getElementById('login-screen').style.display = 'none';
            initApp();
        };

        function initApp() {
            // Load Users List for DMs
            onValue(ref(db, 'users'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(userSnap => {
                    const name = userSnap.key;
                    if(name !== currentUser) {
                        const div = document.createElement('div');
                        div.className = `list-item ${chatTarget === name ? 'active' : ''}`;
                        div.innerHTML = `<span class="status-indicator"></span> ${name}`;
                        div.onclick = () => setTarget(name);
                        list.appendChild(div);
                    }
                });
            });
            listenToMessages();
        }

        window.setTarget = (target) => {
            chatTarget = target;
            document.getElementById('chat-title').innerText = target === 'global' ? '# GLOBAL-HUB' : `@ ${target.toUpperCase()}`;
            document.getElementById('messages').innerHTML = "";
            listenToMessages();
        };

        function listenToMessages() {
            const path = chatTarget === 'global' ? 'messages/global' : 
                         `messages/private/${[currentUser, chatTarget].sort().join('_')}`;
            
            onChildAdded(ref(db, path), (snap) => {
                const d = snap.val();
                render(d);
            });
        }

        function render(d) {
            const isMe = d.user === currentUser;
            const msgBox = document.getElementById('messages');
            const html = `
                <div class="msg-wrap ${isMe ? 'mine' : 'others'}">
                    <span class="sender">${d.user.toUpperCase()}</span>
                    <div class="bubble">${d.text}</div>
                </div>`;
            msgBox.insertAdjacentHTML('beforeend', html);
            msgBox.scrollTop = msgBox.scrollHeight;
        }

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            const path = chatTarget === 'global' ? 'messages/global' : 
                         `messages/private/${[currentUser, chatTarget].sort().join('_')}`;
            
            push(ref(db, path), {
                user: currentUser,
                text: inp.value.trim(),
                time: serverTimestamp()
            });
            inp.value = "";
        };
    </script>
</body>
</html>
