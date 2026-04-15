<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Enterprise Chat</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root { --gold: #FFD700; --purple: #8A2BE2; --dark: #05050a; --glass: rgba(255, 255, 255, 0.03); }
        body { margin: 0; background: var(--dark); color: white; font-family: 'Rajdhani', sans-serif; height: 100vh; display: flex; flex-direction: column; }
        
        /* Layout Structure */
        .app-wrapper { display: flex; flex: 1; overflow: hidden; }
        
        /* Sidebar (Slack Style) */
        .sidebar { width: 260px; background: rgba(0,0,0,0.6); border-right: 1px solid var(--purple); display: flex; flex-direction: column; padding: 20px; }
        .sidebar h2 { font-family: 'Orbitron'; font-size: 0.9rem; color: var(--gold); margin-bottom: 20px; }
        .channel-btn { padding: 10px; cursor: pointer; border-radius: 8px; margin-bottom: 5px; transition: 0.3s; color: #ccc; }
        .channel-btn:hover, .channel-btn.active { background: var(--purple); color: white; }
        
        /* Main Chat Area */
        .main-chat { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1a1a2e, #05050a); }
        .chat-header { padding: 15px 25px; border-bottom: 1px solid rgba(255,255,255,0.1); display: flex; justify-content: space-between; align-items: center; }
        
        #messages { flex: 1; padding: 25px; overflow-y: auto; display: flex; flex-direction: column; gap: 15px; }
        
        /* Message Bubbles */
        .msg-wrap { display: flex; flex-direction: column; max-width: 70%; }
        .mine { align-self: flex-end; align-items: flex-end; }
        .others { align-self: flex-start; }
        
        .bubble { padding: 12px 18px; border-radius: 18px; position: relative; font-size: 1rem; line-height: 1.4; }
        .mine .bubble { background: linear-gradient(135deg, var(--purple), #4B0082); border-bottom-right-radius: 2px; box-shadow: 0 4px 15px rgba(138,43,226,0.2); }
        .others .bubble { background: var(--glass); border: 1px solid rgba(255,255,255,0.1); border-bottom-left-radius: 2px; }
        
        .meta { font-size: 0.65rem; margin-bottom: 4px; color: var(--gold); font-weight: bold; opacity: 0.8; }
        .time { font-size: 0.6rem; color: #888; margin-top: 4px; }

        /* Input Bar */
        .input-container { padding: 20px; background: rgba(0,0,0,0.4); display: flex; gap: 12px; }
        input { flex: 1; background: var(--glass); border: 1px solid var(--purple); padding: 12px 20px; border-radius: 30px; color: white; outline: none; }
        button { background: var(--gold); border: none; padding: 0 25px; border-radius: 30px; font-weight: bold; cursor: pointer; transition: 0.3s; }
        button:hover { transform: scale(1.05); filter: brightness(1.2); }

        /* Login Overlay */
        #login-overlay { position: fixed; inset: 0; background: var(--dark); z-index: 100; display: flex; align-items: center; justify-content: center; }
        .login-card { background: var(--glass); padding: 50px; border-radius: 30px; border: 1px solid var(--purple); text-align: center; backdrop-filter: blur(20px); }
    </style>
</head>
<body>

    <div id="login-overlay">
        <div class="login-card">
            <h1 style="font-family: 'Orbitron'; color: var(--gold); margin-bottom: 30px;">LIVE CONNECT</h1>
            <input type="text" id="userInput" placeholder="Username" style="display: block; width: 100%; margin-bottom: 10px;">
            <input type="password" id="passInput" placeholder="Password" style="display: block; width: 100%; margin-bottom: 20px;">
            <button onclick="handleAuth()" style="width: 100%; height: 45px;">SECURE LOGIN</button>
        </div>
    </div>

    <div class="app-wrapper">
        <div class="sidebar">
            <h2>CHANNELS</h2>
            <div class="channel-btn active" onclick="switchChannel('general')"># general</div>
            <div class="channel-btn" onclick="switchChannel('tech-talk')"># tech-talk</div>
            <div class="channel-btn" onclick="switchChannel('prime-academy')"># prime-academy</div>
            
            <h2 style="margin-top: 40px;">DIRECT MESSAGES</h2>
            <div style="font-size: 0.8rem; color: #666;">No active DM's</div>
        </div>

        <div class="main-chat">
            <div class="chat-header">
                <div id="active-channel-name" style="font-family: 'Orbitron'; font-weight: bold;"># GENERAL</div>
                <div style="color: #00ff88; font-size: 0.8rem;">● Online</div>
            </div>

            <div id="messages"></div>

            <div class="input-container">
                <input type="text" id="msgInput" placeholder="Write something amazing...">
                <button id="sendBtn">SEND</button>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, get, set, serverTimestamp, query, limitToLast, off } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
        let currentChannel = "general";
        let messageListener = null;

        // Auth Logic
        window.handleAuth = async () => {
            const user = document.getElementById('userInput').value.trim().toLowerCase();
            const pass = document.getElementById('passInput').value.trim();
            if(!user || !pass) return alert("Credentials please, sweetie!");

            const userRef = ref(db, 'users/' + user);
            const snapshot = await get(userRef);

            if(snapshot.exists()) {
                if(snapshot.val().password === pass) {
                    login(user);
                } else {
                    alert("Wrong password!");
                }
            } else {
                await set(userRef, { password: pass });
                login(user);
            }
        };

        function login(user) {
            currentUser = user;
            document.getElementById('login-overlay').style.display = 'none';
            loadMessages();
        }

        // Channel Switching
        window.switchChannel = (channel) => {
            currentChannel = channel;
            document.querySelectorAll('.channel-btn').forEach(btn => {
                btn.classList.remove('active');
                if(btn.innerText.includes(channel)) btn.classList.add('active');
            });
            document.getElementById('active-channel-name').innerText = "# " + channel.toUpperCase();
            document.getElementById('messages').innerHTML = ""; // Clear for new channel
            loadMessages();
        };

        function loadMessages() {
            if(messageListener) off(ref(db, `channels/${currentChannel}`));
            
            const chatRef = query(ref(db, `channels/${currentChannel}`), limitToLast(50));
            onChildAdded(chatRef, (snap) => {
                const data = snap.val();
                renderMessage(data);
            });
        }

        function renderMessage(data) {
            const isMe = data.user === currentUser;
            const timeStr = data.time ? new Date(data.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) : "just now";
            
            const msgHtml = `
                <div class="msg-wrap ${isMe ? 'mine' : 'others'}">
                    <span class="meta">${data.user.toUpperCase()}</span>
                    <div class="bubble">${data.text}</div>
                    <span class="time">${timeStr}</span>
                </div>`;
            
            const msgBox = document.getElementById('messages');
            msgBox.insertAdjacentHTML('beforeend', msgHtml);
            msgBox.scrollTop = msgBox.scrollHeight;
        }

        // Sending Logic
        const msgInp = document.getElementById('msgInput');
        function doSend() {
            if(msgInp.value.trim()){
                push(ref(db, `channels/${currentChannel}`), {
                    user: currentUser,
                    text: msgInp.value.trim(),
                    time: serverTimestamp()
                });
                msgInp.value = "";
            }
        }

        document.getElementById('sendBtn').onclick = doSend;
        msgInp.onkeypress = (e) => { if(e.key === 'Enter') doSend(); };

    </script>
</body>
</html>
