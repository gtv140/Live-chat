<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Final</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body, html { height: 100%; overflow: hidden; font-family: sans-serif; background: #f0f2f5; }

        /* Full Screen Container */
        .app-wrapper { display: flex; flex-direction: column; height: 100vh; }

        /* 1. Header Fix */
        .header { height: 60px; background: #6a11cb; color: white; display: flex; align-items: center; padding: 0 15px; flex-shrink: 0; font-weight: bold; }

        /* 2. Proper Scroll Box (The Main Fix) */
        #chat-box { 
            flex: 1; 
            overflow-y: auto; 
            padding: 15px; 
            display: flex; 
            flex-direction: column; 
            gap: 10px;
            -webkit-overflow-scrolling: touch; 
        }

        .bubble { max-width: 80%; padding: 10px 15px; border-radius: 15px; position: relative; font-size: 14px; }
        .mine { align-self: flex-end; background: #6a11cb; color: white; border-bottom-right-radius: 2px; }
        .others { align-self: flex-start; background: white; border: 1px solid #ddd; border-bottom-left-radius: 2px; }

        /* 3. Input Bar Fix */
        .input-area { height: 70px; background: white; border-top: 1px solid #ddd; display: flex; align-items: center; padding: 0 10px; flex-shrink: 0; }
        #msgInput { flex: 1; padding: 12px; border: 1px solid #ddd; border-radius: 25px; outline: none; margin-right: 8px; }
        .send-btn { background: #6a11cb; color: white; border: none; width: 45px; height: 45px; border-radius: 50%; cursor: pointer; }
    </style>
</head>
<body>

    <div class="app-wrapper">
        <div class="header">LIVE CONNECT</div>

        <div id="chat-box"></div>

        <div class="input-area">
            <input type="text" id="msgInput" placeholder="Write, sweetie...">
            <button class="send-btn" onclick="sendMessage()">➔</button>
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
        const user = "User_" + Math.floor(Math.random()*1000);

        window.sendMessage = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            push(ref(db, 'messages/global'), { user: user, text: inp.value, time: serverTimestamp() });
            inp.value = "";
        };

        const box = document.getElementById('chat-box');
        onChildAdded(ref(db, 'messages/global'), (snap) => {
            const d = snap.val();
            const isMe = d.user === user;
            const html = `<div class="bubble ${isMe ? 'mine' : 'others'}">${d.text}</div>`;
            box.insertAdjacentHTML('beforeend', html);
            box.scrollTop = box.scrollHeight; // Auto-scroll to bottom
        });
    </script>
</body>
</html>
