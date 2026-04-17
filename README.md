<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Pro</title>
    <style>
        :root { --p: #6a11cb; --s: #2575fc; --bg: #0f0c29; --g: rgba(255,255,255,0.1); }
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', sans-serif; }
        body { height: 100vh; background: var(--bg); color: white; overflow: hidden; }

        /* Animated Background */
        body::before {
            content: ""; position: fixed; inset: 0;
            background: linear-gradient(45deg, #0f0c29, #302b63, #6a11cb);
            background-size: 400% 400%; animation: gradient 10s infinite alternate; z-index: -1;
        }
        @keyframes gradient { 0% {background-position: 0% 50%;} 100% {background-position: 100% 50%;} }

        /* Auth */
        #auth { position: fixed; inset: 0; z-index: 1000; background: rgba(0,0,0,0.9); display: flex; align-items: center; justify-content: center; backdrop-filter: blur(10px); }
        .box { background: var(--g); padding: 30px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.2); width: 85%; max-width: 350px; text-align: center; }
        input { width: 100%; padding: 12px; margin: 10px 0; border-radius: 10px; border: none; outline: none; }
        button { width: 100%; padding: 12px; background: var(--p); color: white; border: none; border-radius: 10px; cursor: pointer; font-weight: bold; }

        /* Sidebar/Users */
        #user-menu { position: fixed; inset: 0; background: rgba(0,0,0,0.95); z-index: 900; display: none; padding: 20px; flex-direction: column; }
        .u-card { padding: 15px; background: var(--g); margin: 5px 0; border-radius: 12px; display: flex; justify-content: space-between; align-items: center; cursor: pointer; }

        /* Main UI */
        .header { height: 60px; display: flex; align-items: center; justify-content: space-between; padding: 0 15px; background: rgba(255,255,255,0.05); border-bottom: 1px solid var(--g); }
        #chat-box { position: absolute; top: 60px; bottom: 80px; left: 0; right: 0; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 10px; }
        .bar { position: absolute; bottom: 0; left: 0; right: 0; height: 80px; display: flex; align-items: center; padding: 0 10px; gap: 8px; background: rgba(0,0,0,0.5); border-top: 1px solid var(--g); }

        /* Bubbles */
        .msg { max-width: 80%; padding: 10px 15px; border-radius: 18px; position: relative; font-size: 14px; line-height: 1.4; animation: pop 0.3s ease; }
        .mine { align-self: flex-end; background: linear-gradient(to right, var(--p), var(--s)); border-bottom-right-radius: 2px; }
        .others { align-self: flex-start; background: var(--g); border-bottom-left-radius: 2px; }
        .u-name { font-size: 10px; font-weight: bold; margin-bottom: 3px; display: block; color: #ffcc00; }
        .msg img { max-width: 100%; border-radius: 10px; margin-top: 5px; }
        .del-btn { color: #ff4d4d; font-size: 10px; cursor: pointer; margin-top: 5px; display: block; }

        @keyframes pop { from { transform: scale(0.8); opacity: 0; } to { transform: scale(1); opacity: 1; } }
    </style>
</head>
<body>

    <div id="auth">
        <div class="box">
            <h2>Live Connect</h2>
            <input type="text" id="u-name" placeholder="Username">
            <input type="password" id="u-pass" placeholder="Password">
            <button onclick="login()">Enter Premium Chat</button>
        </div>
    </div>

    <div id="user-menu">
        <h3 style="margin-bottom:20px;">Online Operators <span onclick="toggleMenu()" style="float:right">✕</span></h3>
        <div class="u-card" onclick="setChat('global')">🌍 Global Hub (All Users)</div>
        <div id="online-list"></div>
    </div>

    <div class="header">
        <div onclick="alert('Prime Solutions: Secure & Encrypted')" style="cursor:pointer">💎 Prime Solutions</div>
        <div style="display:flex; gap:10px; align-items:center">
            <div id="chat-title" style="font-size:12px; background:var(--p); padding:3px 8px; border-radius:8px;">Global</div>
            <button onclick="toggleMenu()" style="background:none; border:none; color:white; font-size:20px;">👥</button>
        </div>
    </div>

    <div id="chat-box"></div>

    <div class="bar">
        <label for="f-inp" style="font-size:24px; cursor:pointer">🖼️</label>
        <input type="file" id="f-inp" hidden accept="image/*" onchange="upImg(this)">
        <input type="text" id="m-inp" class="box" style="margin:0; text-align:left;" placeholder="Type here, sweetie...">
        <button onclick="send()" style="width:50px; border-radius:50%">➔</button>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp, set, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const conf = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
        };

        const app = initializeApp(conf);
        const db = getDatabase(app);
        let me = "", target = "global", isAdmin = false;

        window.login = () => {
            const u = document.getElementById('u-name').value.trim();
            const p = document.getElementById('u-pass').value.trim();
            if(!u || !p) return;
            me = u;
            if(p === "admin786") isAdmin = true;
            document.getElementById('auth').style.display = 'none';
            set(ref(db, 'online/' + me), true);
            onDisconnect(ref(db, 'online/' + me)).remove();
            load();
        };

        window.toggleMenu = () => {
            const m = document.getElementById('user-menu');
            m.style.display = (m.style.display === 'flex') ? 'none' : 'flex';
        };

        window.setChat = (t) => {
            target = t;
            document.getElementById('chat-title').innerText = t === 'global' ? 'Global' : 'Private: ' + t;
            document.getElementById('chat-box').innerHTML = "";
            toggleMenu();
            load();
        };

        window.send = () => {
            const val = document.getElementById('m-inp').value.trim();
            if(!val) return;
            const path = target === 'global' ? 'msgs/global' : 'msgs/priv/' + getID(me, target);
            push(ref(db, path), { u: me, t: val, ts: serverTimestamp() });
            document.getElementById('m-inp').value = "";
        };

        window.upImg = (i) => {
            const r = new FileReader();
            r.onload = (e) => {
                const path = target === 'global' ? 'msgs/global' : 'msgs/priv/' + getID(me, target);
                push(ref(db, path), { u: me, img: e.target.result, ts: serverTimestamp() });
            };
            r.readAsDataURL(i.files[0]);
        };

        window.delMsg = (id) => {
            if(confirm("Delete this?")) remove(ref(db, (target === 'global' ? 'msgs/global/' : 'msgs/priv/' + getID(me, target) + '/') + id));
        };

        function load() {
            const path = target === 'global' ? 'msgs/global' : 'msgs/priv/' + getID(me, target);
            onChildAdded(ref(db, path), (s) => {
                const d = s.val();
                const isMe = d.u === me;
                const box = document.getElementById('chat-box');
                box.innerHTML += `
                    <div class="msg ${isMe ? 'mine' : 'others'}">
                        <span class="u-name">${isMe ? 'You' : d.u}</span>
                        ${d.t ? `<div>${d.t}</div>` : ''}
                        ${d.img ? `<img src="${d.img}">` : ''}
                        ${(isMe || isAdmin) ? `<span class="del-btn" onclick="delMsg('${s.key}')">Delete</span>` : ''}
                    </div>
                `;
                box.scrollTop = box.scrollHeight;
            });
        }

        onValue(ref(db, 'online'), (s) => {
            const list = document.getElementById('online-list');
            list.innerHTML = "";
            s.forEach(u => {
                if(u.key !== me) list.innerHTML += `<div class="u-card" onclick="setChat('${u.key}')">👤 ${u.key} <span>Chat</span></div>`;
            });
        });

        function getID(a, b) { return a < b ? a + "_" + b : b + "_" + a; }
        document.getElementById('m-inp').onkeypress = (e) => { if(e.key==='Enter') send(); };
    </script>
</body>
</html>
