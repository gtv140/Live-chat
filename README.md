<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Infinity | Prime Solutions</title>
    <link href="https://fonts.googleapis.com/css2?family=Syncopate:wght@700&family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --accent: #00f2fe; --primary: #6a11cb; --bg: #050505;
            --glass: rgba(255, 255, 255, 0.08); --border: rgba(255, 255, 255, 0.15);
        }
        /* Light Theme Variables */
        .light-mode { --bg: #f0f2f5; --glass: rgba(0, 0, 0, 0.05); --border: rgba(0, 0, 0, 0.1); --text: #333; }
        
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Poppins', sans-serif; }
        body, html { height: 100%; background: var(--bg); color: white; overflow: hidden; transition: 0.3s; }
        .light-mode { color: #333; }

        /* Auth/Login */
        #auth-screen { position: fixed; inset: 0; z-index: 5000; background: rgba(0,0,0,0.95); display: flex; align-items: center; justify-content: center; backdrop-filter: blur(20px); }
        .auth-card { background: var(--glass); padding: 40px 30px; border-radius: 30px; width: 90%; max-width: 400px; border: 1px solid var(--border); text-align: center; }

        /* Main App */
        .header { height: 70px; background: rgba(255,255,255,0.03); backdrop-filter: blur(15px); display: flex; align-items: center; justify-content: space-between; padding: 0 20px; border-bottom: 1px solid var(--border); z-index: 1000; }
        
        #chat-window { height: calc(100vh - 155px); overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; }

        /* Typing & Status */
        #typing-indicator { font-size: 10px; color: var(--accent); padding-left: 20px; height: 15px; font-style: italic; }

        /* Messages */
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 20px; position: relative; animation: pop 0.3s ease; font-size: 14px; }
        .mine { align-self: flex-end; background: linear-gradient(135deg, var(--primary), #2575fc); color: white; border-bottom-right-radius: 4px; }
        .others { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }

        /* Premium Bottom Bar */
        .bottom-bar { position: fixed; bottom: 0; width: 100%; height: 85px; background: rgba(5,5,5,0.9); display: flex; align-items: center; padding: 0 15px; gap: 10px; border-top: 1px solid var(--border); }
        .input-box { flex: 1; height: 50px; background: var(--glass); border: 1px solid var(--border); border-radius: 25px; padding: 0 20px; color: inherit; outline: none; }
        
        .btn-icon { background: var(--glass); border: 1px solid var(--border); color: white; width: 45px; height: 45px; border-radius: 50%; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 20px; }
        .send-active { background: var(--accent); color: black; border: none; }

        /* Drawer */
        #side-drawer { position: fixed; right: -100%; top: 0; bottom: 0; width: 80%; max-width: 300px; background: rgba(10,10,10,0.98); z-index: 2000; transition: 0.4s; padding: 25px; }
        .drawer-open { right: 0 !important; }

        @keyframes pop { from { transform: scale(0.9); opacity: 0; } to { transform: scale(1); opacity: 1; } }
    </style>
</head>
<body>

    <div id="auth-screen">
        <div class="auth-card">
            <h1 style="font-family:'Syncopate'; color:var(--accent); margin-bottom:10px;">INFINITY</h1>
            <p style="font-size:10px; opacity:0.6; margin-bottom:20px;">PRIME SOLUTIONS SECURE LOGIN</p>
            <input type="text" id="auth-u" placeholder="Username" style="width:100%; padding:15px; border-radius:15px; border:1px solid var(--border); background:rgba(0,0,0,0.3); color:white; margin-bottom:10px;">
            <input type="password" id="auth-p" placeholder="Password" style="width:100%; padding:15px; border-radius:15px; border:1px solid var(--border); background:rgba(0,0,0,0.3); color:white; margin-bottom:20px;">
            <button onclick="login()" style="width:100%; padding:15px; border-radius:15px; background:var(--primary); color:white; border:none; font-weight:bold;">ENTER SYSTEM</button>
        </div>
    </div>

    <div id="side-drawer">
        <h3 style="margin-bottom:20px; color:var(--accent);">SETTINGS</h3>
        <button onclick="toggleTheme()" style="width:100%; padding:12px; margin-bottom:10px; border-radius:10px; background:var(--glass); border:1px solid var(--border); color:white;">🌓 Switch Theme</button>
        <button onclick="logout()" style="width:100%; padding:12px; border-radius:10px; background:rgba(255,71,87,0.2); border:1px solid #ff4757; color:#ff4757;">🚪 Logout System</button>
        <hr style="margin:20px 0; opacity:0.1;">
        <h4 style="font-size:12px; margin-bottom:10px;">ONLINE USERS</h4>
        <div id="online-list"></div>
    </div>

    <div class="header">
        <div style="font-family:'Syncopate'; font-size:12px; color:var(--accent);">💎 PRIME SOLUTIONS</div>
        <div style="display:flex; gap:15px; align-items:center;">
            <span id="chat-tag" style="font-size:10px; opacity:0.6;">Global</span>
            <div onclick="toggleDrawer()" style="cursor:pointer; font-size:20px;">☰</div>
        </div>
    </div>

    <main id="chat-window"></main>
    <div id="typing-indicator"></div>

    <div class="bottom-bar">
        <button class="btn-icon" onclick="document.getElementById('img-file').click()">🖼️</button>
        <input type="file" id="img-file" hidden accept="image/*" onchange="sendImage(this)">
        
        <input type="text" id="msg-input" class="input-box" placeholder="Message..." oninput="isTyping()">
        
        <button class="btn-icon send-active" onclick="sendMsg()">➔</button>
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
        let me = "", target = "global", typingTimer;

        // AUTH & LOGOUT
        window.login = () => {
            const u = document.getElementById('auth-u').value.trim();
            const p = document.getElementById('auth-p').value.trim();
            if(!u || !p) return;
            me = u;
            document.getElementById('auth-screen').style.display = 'none';
            set(ref(db, 'online/' + me), { status: "Online", last: serverTimestamp() });
            onDisconnect(ref(db, 'online/' + me)).remove();
            startApp();
        };

        window.logout = () => {
            remove(ref(db, 'online/' + me));
            location.reload();
        };

        // UI & THEME
        window.toggleDrawer = () => document.getElementById('side-drawer').classList.toggle('drawer-open');
        window.toggleTheme = () => document.body.classList.toggle('light-mode');

        // TYPING SYSTEM
        window.isTyping = () => {
            set(ref(db, 'typing/' + me), true);
            clearTimeout(typingTimer);
            typingTimer = setTimeout(() => remove(ref(db, 'typing/' + me)), 2000);
        };

        onValue(ref(db, 'typing'), (s) => {
            let typers = [];
            s.forEach(u => { if(u.key !== me) typers.push(u.key); });
            document.getElementById('typing-indicator').innerText = typers.length > 0 ? typers.join(', ') + " is typing..." : "";
        });

        // CHAT ENGINE
        window.sendMsg = () => {
            const val = document.getElementById('msg-input').value.trim();
            if(!val) return;
            push(ref(db, 'chats/' + target), { u: me, m: val, ts: serverTimestamp() });
            document.getElementById('msg-input').value = "";
            remove(ref(db, 'typing/' + me));
        };

        window.sendImage = (el) => {
            const r = new FileReader();
            r.onload = (e) => push(ref(db, 'chats/' + target), { u: me, img: e.target.result, ts: serverTimestamp() });
            r.readAsDataURL(el.files[0]);
        };

        function startApp() {
            onChildAdded(ref(db, 'chats/' + target), (s) => {
                const d = s.val();
                const isMe = d.u === me;
                const win = document.getElementById('chat-window');
                win.innerHTML += `
                    <div class="msg ${isMe ? 'mine' : 'others'}">
                        <span style="font-size:10px; font-weight:bold; display:block; margin-bottom:4px; color:var(--accent);">${isMe ? 'YOU' : d.u}</span>
                        ${d.m ? `<div>${d.m}</div>` : ''}
                        ${d.img ? `<img src="${d.img}" style="max-width:100%; border-radius:10px; margin-top:5px;">` : ''}
                    </div>`;
                win.scrollTop = win.scrollHeight;
            });

            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('online-list');
                list.innerHTML = "";
                s.forEach(u => {
                    if(u.key !== me) list.innerHTML += `<div style="padding:10px; margin:5px 0; background:var(--glass); border-radius:10px; font-size:12px;">👤 ${u.key} <span style="color:#4caf50; float:right;">● Online</span></div>`;
                });
            });
        }

        document.getElementById('msg-input').onkeypress = (e) => { if(e.key==='Enter') sendMsg(); };
    </script>
</body>
</html>
