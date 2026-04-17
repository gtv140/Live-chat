<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Prime Solutions</title>
    <style>
        :root {
            --primary: #6a11cb; --secondary: #2575fc; --bg: #0f0c29;
            --glass: rgba(255, 255, 255, 0.1); --text: #ffffff;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Poppins', sans-serif; }
        body, html { height: 100%; overflow: hidden; background: var(--bg); color: var(--text); }

        /* Premium Background Animation */
        body {
            background: linear-gradient(-45deg, #0f0c29, #302b63, #24243e, #6a11cb);
            background-size: 400% 400%;
            animation: gradient 15s ease infinite;
        }
        @keyframes gradient { 0% {background-position: 0% 50%;} 50% {background-position: 100% 50%;} 100% {background-position: 0% 50%;} }

        /* Auth Screen */
        #auth-screen {
            position: fixed; inset: 0; z-index: 2000; display: flex; align-items: center; justify-content: center;
            backdrop-filter: blur(15px); background: rgba(0,0,0,0.7);
        }
        .auth-card {
            background: var(--glass); padding: 30px; border-radius: 25px; width: 90%; max-width: 380px;
            border: 1px solid rgba(255,255,255,0.2); text-align: center; animation: slideUp 0.5s ease;
        }
        .auth-card input { width: 100%; padding: 12px; margin: 10px 0; border-radius: 12px; border: none; background: rgba(255,255,255,0.9); outline: none; }
        .auth-card button { width: 100%; padding: 12px; border-radius: 12px; border: none; background: var(--primary); color: white; font-weight: bold; cursor: pointer; margin-top: 10px; }

        /* Main Shell */
        .app-shell { position: relative; height: 100vh; display: flex; flex-direction: column; }

        /* Header */
        .header {
            height: 65px; background: rgba(255,255,255,0.05); backdrop-filter: blur(10px);
            display: flex; align-items: center; justify-content: space-between; padding: 0 15px;
            border-bottom: 1px solid rgba(255,255,255,0.1); flex-shrink: 0; z-index: 500;
        }

        /* Fixed Message Window */
        #chat-window {
            position: absolute; top: 65px; bottom: 75px; left: 0; right: 0;
            overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px;
            -webkit-overflow-scrolling: touch;
        }

        /* Bottom Bar */
        .bottom-bar {
            position: absolute; bottom: 0; left: 0; right: 0; height: 75px;
            background: rgba(255,255,255,0.05); backdrop-filter: blur(10px);
            display: flex; align-items: center; padding: 0 15px; gap: 10px; border-top: 1px solid rgba(255,255,255,0.1);
        }

        .input-box { flex: 1; padding: 12px 18px; border-radius: 30px; border: 1px solid rgba(255,255,255,0.2); background: rgba(255,255,255,0.1); color: white; outline: none; }
        .btn-action { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--primary); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; }

        /* Users Sidebar / Modal */
        #user-modal {
            position: fixed; inset: 0; background: rgba(0,0,0,0.9); z-index: 1500; display: none;
            padding: 20px; flex-direction: column; animation: fadeIn 0.3s;
        }
        .user-item { padding: 15px; background: var(--glass); border-radius: 15px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }

        /* Bubbles */
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 20px; animation: pop 0.3s ease; position: relative; font-size: 14px; }
        .mine { align-self: flex-end; background: linear-gradient(135deg, #6a11cb, #2575fc); border-bottom-right-radius: 2px; }
        .others { align-self: flex-start; background: rgba(255,255,255,0.15); border-bottom-left-radius: 2px; }
        .msg-meta { font-size: 10px; opacity: 0.7; margin-bottom: 4px; display: block; }

        @keyframes slideUp { from { transform: translateY(50px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
        @keyframes pop { from { transform: scale(0.9); opacity: 0; } to { transform: scale(1); opacity: 1; } }
    </style>
</head>
<body>

    <div id="auth-screen">
        <div class="auth-card">
            <h2 style="margin-bottom:10px;">Live Connect</h2>
            <p style="font-size:12px; opacity:0.8; margin-bottom:20px;">Premium Access by Prime Solutions</p>
            <input type="text" id="auth-user" placeholder="Username">
            <input type="password" id="auth-pass" placeholder="Password">
            <button onclick="handleAuth()">Enter Live Connect</button>
        </div>
    </div>

    <div id="user-modal">
        <div style="display:flex; justify-content:space-between; margin-bottom:20px;">
            <h3>Online Users</h3>
            <button onclick="closeUsers()" style="background:none; border:none; color:white; font-size:20px;">✕</button>
        </div>
        <div id="online-list"></div>
    </div>

    <div class="app-shell">
        <header class="header">
            <div onclick="showCompany()" style="cursor:pointer">💎 Prime Solutions</div>
            <div style="display:flex; gap:10px;">
                <button onclick="openUsers()" style="background:none; border:none; color:white; cursor:pointer;">👥 Users</button>
                <div id="chat-type-label" style="background:var(--primary); padding:2px 10px; border-radius:10px; font-size:11px;">Global</div>
            </div>
        </header>

        <main id="chat-window"></main>

        <div class="bottom-bar">
            <label for="imgInp" class="btn-action" style="background:rgba(255,255,255,0.2)">📷</label>
            <input type="file" id="imgInp" style="display:none" accept="image/*" onchange="sendImage(this)">
            <input type="text" id="msgInput" class="input-box" placeholder="Write, sweetie...">
            <button class="btn-action" onclick="sendMessage()">➔</button>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp, set, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        let user = "";
        let chatTarget = "global"; // Global or Username
        let isAdmin = false;

        // AUTH & LOGIN
        window.handleAuth = () => {
            const u = document.getElementById('auth-user').value.trim();
            const p = document.getElementById('auth-pass').value.trim();
            if(!u || !p) return alert("Fill all details sweetie!");
            
            user = u;
            if(p === "admin786") isAdmin = true;
            
            document.getElementById('auth-screen').style.display = 'none';
            const presence = ref(db, 'online/' + user);
            set(presence, { status: "online", last: serverTimestamp() });
            onDisconnect(presence).remove();
            
            loadMessages();
        };

        // UI ACTIONS
        window.openUsers = () => document.getElementById('user-modal').style.display = 'flex';
        window.closeUsers = () => document.getElementById('user-modal').style.display = 'none';
        
        window.startPrivate = (target) => {
            chatTarget = target;
            document.getElementById('chat-type-label').innerText = "Private: " + target;
            document.getElementById('chat-window').innerHTML = "";
            closeUsers();
            loadMessages();
        };

        // MESSAGE SYSTEM
        window.sendMessage = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            
            const path = chatTarget === "global" ? 'global_chat' : 'private/' + getPrivateID(user, chatTarget);
            push(ref(db, path), { sender: user, text: inp.value, type: 'text', time: serverTimestamp() });
            inp.value = "";
        };

        window.sendImage = (input) => {
            const file = input.files[0];
            const reader = new FileReader();
            reader.onload = (e) => {
                const path = chatTarget === "global" ? 'global_chat' : 'private/' + getPrivateID(user, chatTarget);
                push(ref(db, path), { sender: user, img: e.target.result, type: 'image', time: serverTimestamp() });
            };
            reader.readAsDataURL(file);
        };

        // ADMIN DELETE
        window.adminDel = (id) => {
            if(!isAdmin) return;
            if(confirm("Admin: Delete this?")) remove(ref(db, 'global_chat/' + id));
        };

        // LOADERS
        function loadMessages() {
            const path = chatTarget === "global" ? 'global_chat' : 'private/' + getPrivateID(user, chatTarget);
            onChildAdded(ref(db, path), (snap) => {
                const d = snap.val();
                const isMe = d.sender === user;
                const win = document.getElementById('chat-window');
                
                const html = `
                    <div class="msg ${isMe ? 'mine' : 'others'}" ondblclick="${isAdmin ? `adminDel('${snap.key}')` : ''}">
                        <span class="msg-meta">${d.sender} ${isAdmin ? '(Double tap to delete)' : ''}</span>
                        ${d.type === 'image' ? `<img src="${d.img}" style="max-width:100%; border-radius:10px;">` : d.text}
                    </div>
                `;
                win.insertAdjacentHTML('beforeend', html);
                win.scrollTop = win.scrollHeight;
            });
        }

        // ONLINE LIST
        onValue(ref(db, 'online'), (snap) => {
            const list = document.getElementById('online-list');
            list.innerHTML = `<div class="user-item" onclick="startPrivate('global')"><span>🌍 Global Chat</span></div>`;
            snap.forEach((u) => {
                if(u.key !== user) {
                    list.innerHTML += `
                        <div class="user-item" onclick="startPrivate('${u.key}')">
                            <span>👤 ${u.key}</span>
                            <button style="background:var(--primary); color:white; border:none; padding:5px 10px; border-radius:5px;">Chat</button>
                        </div>`;
                }
            });
        });

        function getPrivateID(u1, u2) { return u1 < u2 ? u1 + "_" + u2 : u2 + "_" + u1; }
        document.getElementById('msgInput').onkeypress = (e) => { if(e.key==='Enter') sendMessage(); };
    </script>
</body>
</html>
