<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Pro</title>
    <style>
        /* Base Styling - Wahi Jo Aapko Pasand Aya */
        html, body { 
            height: 100%; margin: 0; padding: 0; 
            overflow: hidden; font-family: sans-serif; 
            background: #f0f2f5; 
        }

        /* Login/Auth Overlay */
        #auth-overlay {
            position: fixed; inset: 0; background: #6a11cb;
            display: flex; align-items: center; justify-content: center;
            z-index: 500; padding: 20px;
        }
        .auth-box {
            background: white; padding: 30px; border-radius: 20px;
            width: 100%; max-width: 350px; text-align: center;
            box-shadow: 0 10px 25px rgba(0,0,0,0.2);
        }
        .auth-box h2 { color: #6a11cb; margin-bottom: 20px; font-size: 24px; }
        .auth-box input {
            width: 100%; padding: 12px; margin-bottom: 15px;
            border: 1px solid #ddd; border-radius: 10px; outline: none;
        }
        .auth-box button {
            width: 100%; padding: 12px; background: #6a11cb;
            color: white; border: none; border-radius: 10px;
            cursor: pointer; font-weight: bold; font-size: 16px;
        }

        /* App Layout */
        .app-header {
            position: absolute; top: 0; left: 0; right: 0;
            height: 60px; background: #6a11cb; color: white;
            display: flex; align-items: center; justify-content: space-between;
            padding: 0 15px; font-weight: bold; z-index: 100;
        }
        .online-status { font-size: 12px; background: #28a745; padding: 3px 10px; border-radius: 12px; }

        #chat-window {
            position: absolute; top: 60px; bottom: 70px;
            left: 0; right: 0; overflow-y: auto; padding: 15px;
            display: flex; flex-direction: column; gap: 10px;
            background: #e5ddd5; -webkit-overflow-scrolling: touch;
        }

        .bottom-bar {
            position: absolute; bottom: 0; left: 0; right: 0;
            height: 70px; background: white; border-top: 1px solid #ddd;
            display: flex; align-items: center; padding: 0 10px; z-index: 100;
        }

        #msgInp {
            flex: 1; padding: 12px 15px; border: 1px solid #ccc;
            border-radius: 25px; font-size: 16px; outline: none;
        }

        .btn-send {
            background: #6a11cb; color: white; border: none;
            width: 45px; height: 45px; border-radius: 50%;
            margin-left: 10px; cursor: pointer; display: flex;
            align-items: center; justify-content: center;
        }

        /* Message Bubbles */
        .msg { max-width: 80%; padding: 10px; border-radius: 12px; position: relative; font-size: 14px; }
        .sender-name { font-size: 10px; font-weight: bold; color: #6a11cb; display: block; margin-bottom: 3px; }
        .mine { align-self: flex-end; background: #dcf8c6; border-bottom-right-radius: 2px; }
        .others { align-self: flex-start; background: white; border-bottom-left-radius: 2px; }
    </style>
</head>
<body>

    <div id="auth-overlay">
        <div class="auth-box">
            <h2>Live Connect Login</h2>
            <input type="text" id="user-name" placeholder="Enter your name, sweetie...">
            <button onclick="handleLogin()">Start Chatting</button>
        </div>
    </div>

    <div class="app-header">
        <span>LIVE CONNECT</span>
        <div class="online-status" id="online-counter">Online: 0</div>
    </div>

    <div id="chat-window"></div>

    <div class="bottom-bar">
        <input type="text" id="msgInp" placeholder="Type message...">
        <button class="btn-send" onclick="send()">➔</button>
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

        // 1. LOGIN SYSTEM
        window.handleLogin = () => {
            const name = document.getElementById('user-name').value.trim();
            if (!name) return alert("Please enter your name!");
            
            currentUserName = name;
            document.getElementById('auth-overlay').style.display = 'none';

            // User ko Online mark karein
            const presenceRef = ref(db, 'online_users/' + name);
            set(presenceRef, true);
            onDisconnect(presenceRef).remove(); // App band hone par offline
        };

        // 2. ONLINE USERS COUNTER
        onValue(ref(db, 'online_users'), (snapshot) => {
            const count = snapshot.size || 0;
            document.getElementById('online-counter').innerText = "Online: " + count;
        });

        // 3. SEND MESSAGE
        window.send = () => {
            const el = document.getElementById('msgInp');
            if (!el.value.trim() || !currentUserName) return;
            push(ref(db, 'messages/global'), {
                u: currentUserName,
                t: el.value,
                ts: serverTimestamp()
            });
            el.value = "";
        };

        // 4. DISPLAY MESSAGES
        const win = document.getElementById('chat-window');
        onChildAdded(ref(db, 'messages/global'), (s) => {
            const d = s.val();
            const isMe = d.u === currentUserName;
            const div = document.createElement('div');
            div.className = `msg ${isMe ? 'mine' : 'others'}`;
            div.innerHTML = `<span class="sender-name">${isMe ? 'You' : d.u}</span>${d.t}`;
            win.appendChild(div);
            win.scrollTop = win.scrollHeight;
        });

        document.getElementById('msgInp').onkeypress = (e) => { if(e.key==='Enter') send(); };
    </script>
</body>
</html>
