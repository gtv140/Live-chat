<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Prime Solutions 🟢</title>
    
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --primary-gold: #FFD700;
            --prime-purple: #8A2BE2;
            --glass-bg: rgba(255, 255, 255, 0.03);
            --neon-border: rgba(138, 43, 226, 0.3);
        }

        body {
            margin: 0;
            padding: 0;
            background: #0a0a12;
            color: white;
            font-family: 'Rajdhani', sans-serif;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        /* Full Screen Glass Design */
        .live-connect-app {
            width: 95%;
            max-width: 450px;
            height: 90vh;
            background: var(--glass-bg);
            backdrop-filter: blur(20px);
            border: 1px solid var(--neon-border);
            border-radius: 25px;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            box-shadow: 0 0 40px rgba(0, 0, 0, 0.8);
        }

        /* Header */
        .app-header {
            padding: 25px;
            text-align: center;
            background: rgba(0, 0, 0, 0.2);
            border-bottom: 1px solid var(--neon-border);
        }

        .app-header h1 {
            font-family: 'Orbitron', sans-serif;
            font-size: 1.4rem;
            letter-spacing: 2px;
            margin: 0;
            color: var(--primary-gold);
            text-shadow: 0 0 10px rgba(255, 215, 0, 0.3);
        }

        .status-dot {
            display: inline-block;
            width: 8px;
            height: 8px;
            background: #00ff88;
            border-radius: 50%;
            margin-right: 5px;
            box-shadow: 0 0 10px #00ff88;
        }

        /* Chat Area */
        #chatbox {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .msg {
            max-width: 75%;
            padding: 10px 15px;
            border-radius: 18px;
            position: relative;
            font-size: 1.05rem;
            animation: slideIn 0.3s ease;
        }

        .msg.received {
            background: rgba(255, 255, 255, 0.08);
            align-self: flex-start;
            border-bottom-left-radius: 2px;
        }

        .msg.sent {
            background: linear-gradient(135deg, var(--prime-purple), #4B0082);
            align-self: flex-end;
            border-bottom-right-radius: 2px;
            box-shadow: 0 4px 15px rgba(138, 43, 226, 0.4);
        }

        .sender-name {
            font-size: 0.65rem;
            color: var(--primary-gold);
            margin-bottom: 3px;
            display: block;
            font-weight: bold;
        }

        /* Footer Input */
        .app-footer {
            padding: 20px;
            background: rgba(0, 0, 0, 0.3);
            display: flex;
            gap: 10px;
        }

        input {
            flex: 1;
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid var(--neon-border);
            padding: 12px 20px;
            border-radius: 30px;
            color: white;
            outline: none;
            transition: 0.3s;
        }

        input:focus {
            border-color: var(--primary-gold);
        }

        #sendBtn {
            background: var(--primary-gold);
            border: none;
            width: 50px;
            height: 50px;
            border-radius: 50%;
            cursor: pointer;
            font-weight: bold;
            transition: 0.3s;
        }

        #sendBtn:hover {
            transform: scale(1.1);
            background: white;
        }

        @keyframes slideIn {
            from { opacity: 0; transform: translateX(-10px); }
            to { opacity: 1; transform: translateX(0); }
        }
    </style>
</head>
<body>

    <div class="live-connect-app">
        <div class="app-header">
            <h1>LIVE CONNECT</h1>
            <div style="font-size: 0.8rem; margin-top: 5px;">
                <span class="status-dot"></span> SECURE NETWORK ACTIVE
            </div>
        </div>

        <div id="chatbox">
            </div>

        <div class="app-footer">
            <input type="text" id="userInput" placeholder="Message Live Connect...">
            <button id="sendBtn">➤</button>
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
        const chatRef = ref(db, 'live_connect_chat');

        const input = document.querySelector('#userInput');
        const btn = document.querySelector('#sendBtn');
        const box = document.querySelector('#chatbox');

        const myID = "Guest_" + Math.floor(Math.random() * 999);

        function handleSend() {
            if (input.value.trim() !== "") {
                push(chatRef, {
                    user: myID,
                    text: input.value.trim(),
                    created: serverTimestamp()
                });
                input.value = "";
            }
        }

        btn.onclick = handleSend;
        input.onkeypress = (e) => { if(e.key === 'Enter') handleSend(); };

        onChildAdded(chatRef, (snap) => {
            const data = snap.val();
            const isMe = data.user === myID;
            const html = `
                <div class="msg ${isMe ? 'sent' : 'received'}">
                    <span class="sender-name">${data.user}</span>
                    ${data.text}
                </div>`;
            box.insertAdjacentHTML('beforeend', html);
            box.scrollTop = box.scrollHeight;
        });
    </script>
</body>
</html>
