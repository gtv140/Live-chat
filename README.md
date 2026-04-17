<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Pro | Prime Solutions</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root { --p: #8e2de2; --s: #4a00e0; --bg: #0f0c29; --glass: rgba(255, 255, 255, 0.1); }
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Poppins', sans-serif; }
        
        body { 
            height: 100vh; background: var(--bg); color: white; overflow: hidden;
            background: linear-gradient(135deg, #0f0c29, #302b63, #24243e);
        }

        /* Fixed Header */
        .header {
            position: fixed; top: 0; width: 100%; height: 65px; 
            background: rgba(255,255,255,0.05); backdrop-filter: blur(15px);
            display: flex; align-items: center; justify-content: space-between;
            padding: 0 15px; border-bottom: 1px solid rgba(255,255,255,0.1); z-index: 1000;
        }

        /* Chat Area - Purely Scrollable */
        #chat-box {
            position: absolute; top: 65px; bottom: 85px; width: 100%;
            overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px;
            scroll-behavior: smooth; -webkit-overflow-scrolling: touch;
        }

        /* Input Bar - ALWAYS ON TOP */
        .input-bar {
            position: fixed; bottom: 0; width: 100%; height: 85px;
            background: rgba(15, 12, 41, 0.95); backdrop-filter: blur(20px);
            display: flex; align-items: center; padding: 0 15px; gap: 10px;
            border-top: 1px solid rgba(255,255,255,0.1); z-index: 1000;
        }

        .input-box {
            flex: 1; height: 50px; background: var(--glass); border: 1px solid rgba(255,255,255,0.2);
            border-radius: 25px; padding: 0 20px; color: white; outline: none; font-size: 15px;
        }

        .btn-circle {
            width: 50px; height: 50px; border-radius: 50%; border: none;
            background: linear-gradient(to right, var(--p), var(--s));
            color: white; cursor: pointer; display: flex; align-items: center; justify-content: center;
            box-shadow: 0 4px 15px rgba(142, 45, 226, 0.4);
        }

        /* Bubbles */
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; animation: slideIn 0.3s ease; line-height: 1.5; }
        .mine { align-self: flex-end; background: linear-gradient(135deg, var(--p), var(--s)); border-bottom-right-radius: 5px; }
        .others { align-self: flex-start; background: var(--glass); border-bottom-left-radius: 5px; border: 1px solid rgba(255,255,255,0.1); }
        .u-info { font-size: 10px; font-weight: bold; margin-bottom: 4px; display: block; color: #aaa; }
        .msg img { max-width: 100%; border-radius: 12px; margin-top: 8px; border: 1px solid rgba(255,255,255,0.1); }

        /* Auth & Menu */
        #auth-screen { position: fixed; inset: 0; background: var(--bg); z-index: 2000; display: flex; align-items: center; justify-content: center; }
        .card { background: var(--glass); padding: 30px; border-radius: 25px; width: 90%; max-width: 380px; text-align: center; border: 1px solid var(--glass); }
        #user-menu { position: fixed; inset: 0; background: rgba(0,0,0,0.9); z-index: 1500; display: none; padding: 20px; flex-direction: column; }
        .u-item { padding: 15px; background: var(--glass); margin: 8px 0; border-radius: 15px; display: flex; justify-content: space-between; align-items: center; }

        @keyframes slideIn { from { transform: translateY(10px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
    </style>
</head>
<body>

    <div id="auth-screen">
        <div class="card">
            <h2 style="margin-bottom:20px;">Live Connect</h2>
            <input type="text" id="login-u" placeholder="Username" class="input-box" style="margin-bottom:10px; width:100%;">
            <input type="password" id="login-p" placeholder="Password" class="input-box" style="margin-bottom:20px; width:100%;">
            <button onclick="doLogin()" class="btn-circle" style="width:100%; border-radius:12px;">Login Now</button>
        </div>
    </div>

    <div id="user-menu">
        <h3 style="margin-bottom:20px;">Operators <span onclick="toggleMenu()" style="float:right; cursor:pointer;">✕</span></h3>
        <div class="u-item" onclick="changeChat('global')">🌍 Global Hub (Talk to Everyone)</div>
        <div id="online-users"></div>
    </div>

    <div class="header">
        <div style="font-weight:600;">💎 PRIME SOLUTIONS</div>
        <div style="display:flex; gap:12px; align-items:center;">
            <span id="chat-tag" style="font-size:10px; background:var(--p); padding:2px 8px; border-radius:10px;">Global</span>
            <button onclick="toggleMenu()" style="background:none; border:none; color:white; font-size:22px; cursor:pointer;">👥</button>
        </div>
    </div>

    <div id="chat-box"></div>

    <div class="input-bar">
        <label for="img-file" style="font-size:24px; cursor:pointer;">🖼️</label>
        <input type="file" id="img-file" hidden accept="image/*" onchange="uploadPhoto(this)">
        <input type="text" id="msg-input" class="input-box" placeholder="Write message, sweetie...">
        <button class="btn-circle" onclick="doSend()">➔</button>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp, set, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const config = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
        };

        const app = initializeApp(config);
        const db = getDatabase(app);
        let me = "", activeTarget = "global", isAdmin = false;

        window.doLogin = () => {
            const u = document.getElementById('login-u').value.trim();
            const p = document.getElementById('login-p').value.trim();
            if(!u || !p) return;
            me = u;
            if(p === "admin786") isAdmin = true;
            document.getElementById('auth-screen').style.display = 'none';
            set(ref(db, 'online/' + me), true);
            onDisconnect(ref(db, 'online/' + me)).remove();
            startChat();
        };

        window.toggleMenu = () => {
            const m = document.getElementById('user-menu');
            m.style.display = (m.style.display === 'flex') ? 'none' : 'flex';
        };

        window.changeChat = (t) => {
            activeTarget = t;
            document.getElementById('chat-tag').innerText = t === 'global' ? 'Global' : t;
            document.getElementById('chat-box').innerHTML = "";
            toggleMenu();
            startChat();
        };

        window.doSend = () => {
            const inp = document.getElementById('msg-input');
            if(!inp.value.trim()) return;
            const path = activeTarget === 'global' ? 'main_chat' : 'p_chat/' + getPath(me, activeTarget);
            push(ref(db, path), { user: me, msg: inp.value, time: serverTimestamp() });
            inp.value = "";
        };

        window.uploadPhoto = (el) => {
            const reader = new FileReader();
            reader.onload = (e) => {
                const path = activeTarget === 'global' ? 'main_chat' : 'p_chat/' + getPath(me, activeTarget);
                push(ref(db, path), { user: me, photo: e.target.result, time: serverTimestamp() });
            };
            reader.readAsDataURL(el.files[0]);
        };

        window.delMsg = (key) => {
            if(confirm("Delete this?")) {
                const path = activeTarget === 'global' ? 'main_chat/' : 'p_chat/' + getPath(me, activeTarget) + '/';
                remove(ref(db, path + key));
            }
        };

        function startChat() {
            const path = activeTarget === 'global' ? 'main_chat' : 'p_chat/' + getPath(me, activeTarget);
            onChildAdded(ref(db, path), (s) => {
                const d = s.val();
                const isMe = d.user === me;
                const box = document.getElementById('chat-box');
                box.innerHTML += `
                    <div class="msg ${isMe ? 'mine' : 'others'}">
                        <span class="u-info">${isMe ? 'You' : d.user}</span>
                        ${d.msg ? `<div>${d.msg}</div>` : ''}
                        ${d.photo ? `<img src="${d.photo}">` : ''}
                        ${(isMe || isAdmin) ? `<div onclick="delMsg('${s.key}')" style="font-size:9px; cursor:pointer; opacity:0.6; margin-top:5px;">Delete</div>` : ''}
                    </div>
                `;
                box.scrollTop = box.scrollHeight;
            });
        }

        onValue(ref(db, 'online'), (s) => {
            const list = document.getElementById('online-users');
            list.innerHTML = "";
            s.forEach(u => {
                if(u.key !== me) list.innerHTML += `<div class="u-item" onclick="changeChat('${u.key}')">👤 ${u.key} <span style="font-size:11px; color:#4caf50;">Online</span></div>`;
            });
        });

        function getPath(a, b) { return a < b ? a + "_" + b : b + "_" + a; }
        document.getElementById('msg-input').addEventListener('keypress', (e) => { if(e.key === 'Enter') doSend(); });
    </script>
</body>
</html>
