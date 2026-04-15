<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Prime Solutions</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --gold: #FFD700;
            --purple: #8A2BE2;
            --dark: #0a0a12;
            --glass: rgba(255, 255, 255, 0.05);
        }

        body {
            margin: 0;
            background: var(--dark);
            color: white;
            font-family: 'Rajdhani', sans-serif;
            overflow: hidden;
        }

        /* Navigation */
        nav {
            background: rgba(0,0,0,0.5);
            padding: 15px;
            display: flex;
            justify-content: center;
            gap: 20px;
            border-bottom: 1px solid var(--purple);
        }

        nav a {
            color: white;
            text-decoration: none;
            font-family: 'Orbitron';
            font-size: 0.8rem;
            cursor: pointer;
        }

        nav a:hover { color: var(--gold); }

        /* Pages logic */
        .page { display: none; height: 90vh; padding: 20px; text-align: center; }
        .active { display: flex; flex-direction: column; align-items: center; justify-content: center; }

        /* Login/Home Style */
        .card {
            background: var(--glass);
            padding: 40px;
            border-radius: 20px;
            border: 1px solid var(--purple);
            backdrop-filter: blur(10px);
        }

        input {
            padding: 12px;
            border-radius: 25px;
            border: 1px solid var(--purple);
            background: transparent;
            color: white;
            margin-bottom: 15px;
            width: 250px;
            text-align: center;
        }

        button {
            background: var(--gold);
            border: none;
            padding: 10px 30px;
            border-radius: 25px;
            font-weight: bold;
            cursor: pointer;
        }

        /* Chat System */
        #chat-container {
            width: 100%;
            max-width: 600px;
            height: 70vh;
            background: var(--glass);
            border-radius: 15px;
            display: flex;
            flex-direction: column;
            margin-top: 20px;
        }

        #messages {
            flex: 1;
            padding: 15px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .msg {
            padding: 8px 15px;
            border-radius: 15px;
            max-width: 70%;
            text-align: left;
        }

        .mine { align-self: flex-end; background: var(--purple); }
        .others { align-self: flex-start; background: rgba(255,255,255,0.1); }

        .sender { font-size: 0.7rem; color: var(--gold); display: block; }
    </style>
</head>
<body>

    <nav>
        <a onclick="showPage('home')">HOME</a>
        <a onclick="showPage('chat')">LIVE CHAT</a>
        <a onclick="showPage('about')">ABOUT</a>
    </nav>

    <div id="home" class="page active">
        <div class="card">
            <h1 style="font-family: 'Orbitron'; color: var(--gold);">LIVE CONNECT</h1>
            <p>Enter your name to start chatting</p>
            <input type="text" id="usernameInput" placeholder="Your Name...">
            <br>
            <button onclick="joinChat()">JOIN NOW</button>
        </div>
    </div>

    <div id="chat" class="page">
        <h2 id="welcomeText" style="font-family: 'Orbitron';">CHAT ROOM</h2>
        <div id="chat-container">
            <div id="messages"></div>
            <div style="padding: 15px; display: flex; gap: 10px;">
                <input type="text" id="msgInput" placeholder="Write a message..." style="margin:0; flex:1;">
                <button id="sendBtn">SEND</button>
            </div>
        </div>
    </div>

    <div id="about" class="page">
        <div class="card">
            <h2 style="font-family: 'Orbitron'; color: var(--gold);">About Live Connect</h2>
            <p>Developed by <b>Prime Solutions</b></p>
            <p>A Realtime Multi-user SaaS platform for global connectivity.</p>
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
        const chatRef = ref(db, 'multi_user_chat');

        let currentUser = "";

        // Navigation Logic
        window.showPage = (pageId) => {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(pageId).classList.add('active');
        };

        window.joinChat = () => {
            const name = document.getElementById('usernameInput').value.trim();
            if(name) {
                currentUser = name;
                document.getElementById('welcomeText').innerText = `Welcome, ${currentUser}`;
                showPage('chat');
            } else {
                alert("Please enter a name, sweetie!");
            }
        };

        // Chat Logic
        const msgInp = document.getElementById('msgInput');
        const sendBtn = document.getElementById('sendBtn');
        const msgBox = document.getElementById('messages');

        function sendMessage() {
            if(msgInp.value.trim()){
                push(chatRef, {
                    user: currentUser,
                    text: msgInp.value,
                    time: serverTimestamp()
                });
                msgInp.value = "";
            }
        }

        sendBtn.onclick = sendMessage;
        msgInp.onkeypress = (e) => { if(e.key === 'Enter') sendMessage(); };

        onChildAdded(chatRef, (snap) => {
            const data = snap.val();
            const isMe = data.user === currentUser;
            const div = document.createElement('div');
            div.className = `msg ${isMe ? 'mine' : 'others'}`;
            div.innerHTML = `<span class="sender">${data.user}</span>${data.text}`;
            msgBox.appendChild(div);
            msgBox.scrollTop = msgBox.scrollHeight;
        });
    </script>
</body>
</html>
