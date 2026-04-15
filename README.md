<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Secure Login</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root { --gold: #FFD700; --purple: #8A2BE2; --dark: #0a0a12; --glass: rgba(255, 255, 255, 0.05); }
        body { margin: 0; background: var(--dark); color: white; font-family: 'Rajdhani', sans-serif; overflow: hidden; }
        nav { background: rgba(0,0,0,0.8); padding: 15px; display: flex; justify-content: center; gap: 25px; border-bottom: 1px solid var(--purple); }
        nav a { color: white; text-decoration: none; font-family: 'Orbitron'; font-size: 0.75rem; cursor: pointer; }
        .page { display: none; height: 85vh; padding: 20px; text-align: center; }
        .active { display: flex; flex-direction: column; align-items: center; justify-content: center; }
        .card { background: var(--glass); padding: 40px; border-radius: 25px; border: 1px solid var(--purple); backdrop-filter: blur(15px); }
        input { padding: 12px 20px; border-radius: 30px; border: 1px solid var(--purple); background: rgba(255,255,255,0.05); color: white; margin-bottom: 10px; width: 280px; text-align: center; outline: none; }
        button { background: linear-gradient(45deg, var(--purple), #4B0082); border: none; color: white; padding: 12px 40px; border-radius: 30px; font-weight: bold; cursor: pointer; font-family: 'Orbitron'; font-size: 0.8rem; margin-top: 10px; }
        #chat-container { width: 100%; max-width: 700px; height: 75vh; background: var(--glass); border-radius: 20px; display: flex; flex-direction: column; border: 1px solid rgba(255,255,255,0.1); }
        #messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; }
        .msg { padding: 10px 18px; border-radius: 20px; max-width: 75%; }
        .mine { align-self: flex-end; background: var(--purple); border-bottom-right-radius: 2px; }
        .others { align-self: flex-start; background: rgba(255,255,255,0.1); border-bottom-left-radius: 2px; }
        .sender { font-size: 0.65rem; color: var(--gold); display: block; font-weight: bold; }
    </style>
</head>
<body>

    <nav>
        <a onclick="showPage('home')">HOME</a>
        <a onclick="showPage('chat')">CHAT ROOM</a>
        <a onclick="showPage('about')">ABOUT</a>
    </nav>

    <div id="home" class="page active">
        <div class="card">
            <h1 style="font-family: 'Orbitron'; color: var(--gold);">LIVE CONNECT</h1>
            <p>Login or Create Account</p>
            <input type="text" id="userInput" placeholder="Username">
            <input type="password" id="passInput" placeholder="Password">
            <br>
            <button onclick="handleAuth()">LOGIN / JOIN</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chat-container">
            <div id="messages"></div>
            <div style="padding: 20px; display: flex; gap: 12px; background: rgba(0,0,0,0.3);">
                <input type="text" id="msgInput" placeholder="Type message..." style="margin:0; flex:1;">
                <button id="sendBtn" style="padding: 0 25px;">SEND</button>
            </div>
        </div>
    </div>

    <div id="about" class="page">
        <div class="card"><h2>Prime Solutions</h2><p>SaaS Chat Architecture</p></div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, get, set, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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

        window.showPage = (id) => {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(id).classList.add('active');
        };

        // Custom Auth System
        window.handleAuth = async () => {
            const user = document.getElementById('userInput').value.trim().toLowerCase();
            const pass = document.getElementById('passInput').value.trim();

            if(!user || !pass) return alert("Fill all fields, sweetie!");

            const userRef = ref(db, 'users/' + user);
            const snapshot = await get(userRef);

            if(snapshot.exists()) {
                if(snapshot.val().password === pass) {
                    currentUser = user;
                    showPage('chat');
                } else {
                    alert("Wrong password, sweetie!");
                }
            } else {
                // Register new user
                await set(userRef, { password: pass });
                currentUser = user;
                alert("Account Created!");
                showPage('chat');
            }
        };

        // Chat Logic
        const chatRef = ref(db, 'global_chat');
        const msgInp = document.getElementById('msgInput');
        const msgBox = document.getElementById('messages');

        document.getElementById('sendBtn').onclick = () => {
            if(msgInp.value.trim()){
                push(chatRef, { user: currentUser, text: msgInp.value, time: serverTimestamp() });
                msgInp.value = "";
            }
        };

        onChildAdded(chatRef, (snap) => {
            const data = snap.val();
            const div = document.createElement('div');
            div.className = `msg ${data.user === currentUser ? 'mine' : 'others'}`;
            div.innerHTML = `<span class="sender">${data.user}</span>${data.text}`;
            msgBox.appendChild(div);
            msgBox.scrollTop = msgBox.scrollHeight;
        });
    </script>
</body>
</html>
