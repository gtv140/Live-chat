<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Pro</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', sans-serif; }
        body, html { height: 100%; overflow: hidden; background: #f0f2f5; }

        /* Login Overlay */
        #auth-screen {
            position: fixed; inset: 0; background: #6a11cb; z-index: 2000;
            display: flex; align-items: center; justify-content: center; padding: 20px;
        }
        .auth-card { background: white; padding: 30px; border-radius: 20px; width: 100%; max-width: 350px; text-align: center; box-shadow: 0 10px 25px rgba(0,0,0,0.2); }
        .auth-card h2 { color: #6a11cb; margin-bottom: 20px; }
        .auth-card input { width: 100%; padding: 12px; margin-bottom: 15px; border: 1px solid #ddd; border-radius: 10px; outline: none; }
        .auth-card button { width: 100%; padding: 12px; background: #6a11cb; color: white; border: none; border-radius: 10px; cursor: pointer; font-weight: bold; }

        /* Main App Layout */
        .app-shell { display: flex; flex-direction: column; height: 100vh; position: relative; }

        .header {
            height: 60px; background: #6a11cb; color: white;
            display: flex; align-items: center; justify-content: space-between; padding: 0 15px;
            font-weight: bold; flex-shrink: 0; z-index: 100;
        }
        .online-tag { background: #28a745; font-size: 11px; padding: 3px 8px; border-radius: 10px; }

        #chat-area {
            position: absolute; top: 60px; bottom: 70px; left: 0; right: 0;
            overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 10px;
            background: #e5ddd5; -webkit-overflow-scrolling: touch;
        }

        .input-bar {
            position: absolute; bottom: 0; left: 0; right: 0;
            height: 70px; background: white; border-top: 1px solid #ddd;
            display: flex; align-items: center; padding: 0 10px; z-index: 100;
        }

        #msgInput { flex: 1; padding: 12px 15px; border: 1px solid #ddd; border-radius: 25px; outline: none; font-size: 16px; }
        .send-btn { background: #6a11cb; color: white; border: none; width: 45px; height: 45px; border-radius: 50%; margin-left: 10px; cursor: pointer; }

        /* Message Bubbles */
        .msg { max-width: 80%; padding: 10px; border-radius: 12px; position: relative; font-size: 14px; line-height: 1.4; }
        .name { font-size: 10px; font-weight: bold; display: block; margin-bottom: 2px; color: #6a11cb; }
        .mine { align-self: flex-end; background: #dcf8c6; border-bottom-right-radius: 2px; }
        .others { align-self: flex-start; background: white; border-bottom-left-radius: 2px; }
    </style>
</head>
<body>

    <div id="auth-screen">
        <div class="auth-card">
            <h2>Live Connect</h2>
            <p style="font-size: 13px; color: #666; margin-bottom: 15px;">Enter your name to start chatting</p>
            <input type="text" id="username" placeholder="Your Name...">
            <button onclick="loginUser()">Join Chat</button>
        </div>
    </div>

    <div class="app-shell">
        <div class="header">
            <span>LIVE CONNECT</span>
            <div class="online-tag" id="online-count">Online: 0</div>
        </div>

        <div id="chat-area"></div>

        <div class="input-bar">
            <input type="text" id="msgInput" placeholder="Write something, sweetie...">
            <button class="send-btn" onclick="send()">➔</button>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp, set, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        let currentUserName = "";

        // 1. Login Logic
        window.loginUser = () => {
            const name = document.getElementById('username').value.trim();
            if (!name) return alert("Please enter your name, sweetie!");
            
            currentUserName = name;
            document.getElementById('auth-screen').style.display = 'none';

            // Online Presence
            const onlineRef = ref(db, 'online/' + name);
            set(onlineRef, true);
            onDisconnect(onlineRef).remove();
        };

        // 2. Online Counter
        onValue(ref(db, 'online'), (snapshot) => {
            const count = snapshot.size || 0;
            document.getElementById('online-count').innerText = "Online: " + count;
        });

        // 3. Send Message
        window.send = () => {
            const inp = document.getElementById('msgInput');
            if (!inp.value.trim()) return;
            push(ref(db, 'messages'), {
                user: currentUserName,
                text: inp.value,
                timestamp: serverTimestamp()
            });
            inp.value = "";
        };

        // 4. Receive Messages
        const chatWin = document.getElementById('chat-area');
        onChildAdded(ref(db, 'messages'), (snap) => {
            const d = snap.val();
            const isMe = d.user === currentUserName;
            const div = document.createElement('div');
            div.className = `msg ${isMe ? 'mine' : 'others'}`;
            div.innerHTML = `<span class="name">${isMe ? 'You' : d.user}</span>${d.text}`;
            chatWin.appendChild(div);
            chatWin.scrollTop = chatWin.scrollHeight;
        });

        document.getElementById('msgInput').onkeypress = (e) => { if(e.key==='Enter') send(); };
    </script>
</body>
</html>
