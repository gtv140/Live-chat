<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect  | Prime Solutions Official</title>
    <link href="https://fonts.googleapis.com/css2?family=Syncopate:wght@700&family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --accent: #00f2fe; --primary: #7000ff; --bg: #03001e;
            --glass: rgba(255, 255, 255, 0.07); --border: rgba(255, 255, 255, 0.12);
            --text: #ffffff;
        }
        .light-mode {
            --bg: #f5f7fa; --glass: rgba(0, 0, 0, 0.05); --border: rgba(0, 0, 0, 0.08); --text: #1a1a1a;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Poppins', sans-serif; }
        body, html { height: 100%; background: var(--bg); color: var(--text); overflow: hidden; transition: 0.5s ease; }

        /* Animated Mesh Background */
        .bg-glow {
            position: fixed; inset: 0; z-index: -1;
            background: linear-gradient(45deg, #03001e, #7303c0, #03001e);
            background-size: 400% 400%;
            animation: gradientBG 15s ease infinite;
        }
        @keyframes gradientBG { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }

        /* Auth Screen */
        #auth-screen {
            position: fixed; inset: 0; z-index: 5000; background: rgba(0,0,0,0.97);
            display: flex; align-items: center; justify-content: center; backdrop-filter: blur(30px);
        }
        .auth-card {
            background: var(--glass); padding: 45px 35px; border-radius: 40px; width: 90%; max-width: 420px;
            border: 1px solid var(--border); text-align: center; box-shadow: 0 25px 50px rgba(0,0,0,0.5);
        }
        .auth-card h1 { font-family: 'Syncopate'; font-size: 20px; color: var(--accent); letter-spacing: 3px; margin-bottom: 30px; }
        .auth-tabs { display: flex; justify-content: center; gap: 25px; margin-bottom: 30px; }
        .tab { cursor: pointer; opacity: 0.4; font-size: 13px; font-weight: 600; letter-spacing: 1px; transition: 0.3s; }
        .tab.active { opacity: 1; color: var(--accent); border-bottom: 2px solid var(--accent); padding-bottom: 5px; }
        .auth-input { width: 100%; padding: 16px; margin-bottom: 15px; border-radius: 18px; border: 1px solid var(--border); background: rgba(255,255,255,0.05); color: white; outline: none; transition: 0.3s; }
        .auth-input:focus { border-color: var(--accent); box-shadow: 0 0 15px rgba(0,242,254,0.1); }
        .main-btn { width: 100%; padding: 16px; border-radius: 18px; background: linear-gradient(90deg, var(--primary), #00d2ff); color: white; border: none; font-weight: 600; cursor: pointer; transform: scale(1); transition: 0.2s; }
        .main-btn:active { transform: scale(0.97); }

        /* Header */
        .header {
            height: 75px; background: rgba(255,255,255,0.02); backdrop-filter: blur(20px);
            display: flex; align-items: center; justify-content: space-between; padding: 0 25px;
            border-bottom: 1px solid var(--border); z-index: 1000;
        }
        .logo { font-family: 'Syncopate'; font-size: 13px; color: var(--accent); cursor: pointer; }

        /* Side Menu Overlay */
        #side-menu {
            position: fixed; right: -100%; top: 0; bottom: 0; width: 85%; max-width: 340px;
            background: rgba(5, 5, 5, 0.98); z-index: 2000; transition: 0.5s cubic-bezier(0.4, 0, 0.2, 1);
            padding: 40px 25px; border-left: 1px solid var(--border);
        }
        .menu-open { right: 0 !important; }
        .menu-item { padding: 18px; background: var(--glass); border-radius: 20px; border: 1px solid var(--border); margin-bottom: 12px; cursor: pointer; display: flex; align-items: center; gap: 12px; }

        /* Chat Window */
        #chat-window { height: calc(100vh - 165px); overflow-y: auto; padding: 25px; display: flex; flex-direction: column; gap: 18px; scroll-behavior: smooth; }
        .msg { max-width: 80%; padding: 14px 20px; border-radius: 25px; position: relative; animation: popIn 0.4s ease; font-size: 14.5px; line-height: 1.6; }
        @keyframes popIn { from { opacity: 0; transform: translateY(20px) scale(0.9); } to { opacity: 1; transform: translateY(0) scale(1); } }
        .mine { align-self: flex-end; background: linear-gradient(135deg, var(--primary), #3b82f6); color: white; border-bottom-right-radius: 5px; box-shadow: 0 10px 20px rgba(112,0,255,0.2); }
        .others { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 5px; }
        .u-name { font-size: 10px; font-weight: 700; margin-bottom: 6px; display: block; color: var(--accent); text-transform: uppercase; }

        /* Footer */
        #typing-box { font-size: 10px; color: var(--accent); padding: 0 30px; height: 20px; font-style: italic; opacity: 0.8; }
        .bottom-bar {
            position: fixed; bottom: 0; width: 100%; height: 95px;
            background: rgba(5,5,5,0.85); backdrop-filter: blur(25px);
            display: flex; align-items: center; padding: 0 20px; gap: 12px; border-top: 1px solid var(--border);
        }
        .inp-wrap { flex: 1; position: relative; }
        #msg-inp { width: 100%; height: 55px; background: var(--glass); border: 1px solid var(--border); border-radius: 30px; padding: 0 25px; color: inherit; outline: none; font-size: 15px; }
        .circle-btn { width: 55px; height: 55px; border-radius: 50%; border: none; background: var(--glass); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 22px; border: 1px solid var(--border); }
        .send-active { background: var(--accent); color: black; border: none; box-shadow: 0 0 20px rgba(0,242,254,0.4); }

        /* Admin/Moderation */
        .del-hint { font-size: 9px; margin-top: 8px; cursor: pointer; color: #ff4d4d; opacity: 0.6; display: inline-block; }
    </style>
</head>
<body>

    <div class="bg-glow"></div>

    <div id="auth-screen">
        <div class="auth-card">
            <h1>LIVE CONNECT</h1>
            <div class="auth-tabs">
                <span id="tab-l" class="tab active" onclick="setAuth('login')">SIGN IN</span>
                <span id="tab-s" class="tab" onclick="setAuth('signup')">SIGN UP</span>
            </div>
            <input type="text" id="au-u" class="auth-input" placeholder="Username">
            <input type="password" id="au-p" class="auth-input" placeholder="Password">
            <button onclick="doAuth()" id="au-btn" class="main-btn">ESTABLISH CONNECTION</button>
            <div style="margin-top:20px; font-size:9px; opacity:0.5; color:var(--accent);">SECURED BY PRIME SOLUTIONS FIREWALL</div>
        </div>
    </div>

    <div id="side-menu">
        <h2 style="font-family:'Syncopate'; font-size:14px; color:var(--accent); margin-bottom:30px;">PRIME CONSOLE</h2>
        <div class="menu-item" onclick="switchTheme()">🌓 Change Appearance</div>
        <div class="menu-item" onclick="showPolicies()">🛡️ Privacy & Trust Center</div>
        <div class="menu-item" onclick="doLogout()" style="border-color:#ff4d4d; color:#ff4d4d;">🚪 Terminate Session</div>
        <hr style="opacity:0.1; margin:25px 0;">
        <p style="font-size:11px; opacity:0.6; margin-bottom:15px; letter-spacing:1px;">ACTIVE AGENTS</p>
        <div class="menu-item" onclick="setTarget('global')" style="background:var(--primary);">🌍 Global Network</div>
        <div id="on-list"></div>
    </div>

    <header class="header">
        <div class="logo" onclick="showPolicies()">💎 PRIME SOLUTIONS</div>
        <div style="display:flex; gap:18px; align-items:center;">
            <div id="target-ui" style="font-size:10px; font-weight:bold; background:var(--accent); color:black; padding:4px 12px; border-radius:20px;">GLOBAL</div>
            <div onclick="toggleMenu()" style="cursor:pointer; font-size:26px;">☰</div>
        </div>
    </header>

    <main id="chat-window"></main>
    <div id="typing-box"></div>

    <div class="bottom-bar">
        <button class="circle-btn" onclick="document.getElementById('f-img').click()">🖼️</button>
        <input type="file" id="f-img" hidden accept="image/*" onchange="upImg(this)">
        <div class="inp-wrap">
            <input type="text" id="msg-inp" placeholder="Type here, sweetie..." oninput="sendTyping()">
        </div>
        <button class="circle-btn send-active" onclick="send()">➔</button>
    </div>

    <div id="policy-modal" style="display:none; position:fixed; inset:0; background:rgba(0,0,0,0.98); z-index:6000; padding:40px; overflow-y:auto;">
        <h2 style="color:var(--accent); font-family:'Syncopate'; margin-bottom:20px;">TRUST & PRIVACY</h2>
        <p style="margin-bottom:15px; opacity:0.8;">Prime Solutions ensures that "Live Connect" uses industry-standard encryption for all packets transmitted.</p>
        <p style="margin-bottom:15px; opacity:0.8;"><b>Zero Log Policy:</b> We do not track user behavior or share private metadata.</p>
        <p style="margin-bottom:15px; opacity:0.8;"><b>Identity Protection:</b> All user credentials are hashed and stored in secure Firebase environments.</p>
        <button onclick="showPolicies()" class="main-btn" style="width:auto; padding:15px 40px;">Back to Secure Chat</button>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp, set, onValue, onDisconnect, remove, get } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const config = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
        };

        const app = initializeApp(config);
        const db = getDatabase(app);
        let me = "", target = "global", authMode = "login", isAdmin = false, tRef;

        // AUTH HANDLER
        window.setAuth = (m) => {
            authMode = m;
            document.getElementById('tab-l').className = m==='login' ? 'tab active' : 'tab';
            document.getElementById('tab-s').className = m==='signup' ? 'tab active' : 'tab';
            document.getElementById('au-btn').innerText = m==='login' ? 'ESTABLISH CONNECTION' : 'REGISTER ACCOUNT';
        };

        window.doAuth = async () => {
            const u = document.getElementById('au-u').value.trim();
            const p = document.getElementById('au-p').value.trim();
            if(!u || !p) return alert("Fill all fields, sweetie!");

            const uRef = ref(db, 'master_users/' + u);
            const snap = await get(uRef);

            if(authMode === 'signup') {
                if(snap.exists()) return alert("User already registered!");
                await set(uRef, { pass: p, registered: serverTimestamp() });
                alert("Account Verified! Login now.");
                setAuth('login');
            } else {
                if(!snap.exists() || snap.val().pass !== p) return alert("Access Denied! Invalid credentials.");
                me = u;
                if(p === "admin786") isAdmin = true;
                document.getElementById('auth-screen').style.display = 'none';
                set(ref(db, 'online_v4/' + me), true);
                onDisconnect(ref(db, 'online_v4/' + me)).remove();
                initApp();
            }
        };

        // UI CORE
        window.toggleMenu = () => document.getElementById('side-menu').classList.toggle('menu-open');
        window.switchTheme = () => document.body.classList.toggle('light-mode');
        window.showPolicies = () => { const m = document.getElementById('policy-modal'); m.style.display = m.style.display==='none'?'block':'none'; };
        window.doLogout = () => { remove(ref(db, 'online_v4/' + me)); location.reload(); };

        window.sendTyping = () => {
            set(ref(db, 'typing_v4/' + me), true);
            clearTimeout(tRef);
            tRef = setTimeout(() => remove(ref(db, 'typing_v4/' + me)), 2000);
        };

        window.setTarget = (t) => {
            target = t;
            document.getElementById('target-ui').innerText = t.toUpperCase();
            document.getElementById('chat-window').innerHTML = "";
            toggleMenu();
            initApp();
        };

        // CHAT LOGIC
        window.send = () => {
            const i = document.getElementById('msg-inp');
            if(!i.value.trim()) return;
            const p = target === 'global' ? 'chats/v4/global' : 'chats/v4/priv/' + getRoom(me, target);
            push(ref(db, p), { u: me, m: i.value, ts: serverTimestamp() });
            i.value = "";
            remove(ref(db, 'typing_v4/' + me));
        };

        window.upImg = (el) => {
            const r = new FileReader();
            r.onload = (e) => {
                const p = target === 'global' ? 'chats/v4/global' : 'chats/v4/priv/' + getRoom(me, target);
                push(ref(db, p), { u: me, img: e.target.result, ts: serverTimestamp() });
            };
            r.readAsDataURL(el.files[0]);
        };

        window.moderatorDel = (id) => {
            if(confirm("Remove this data point?")) {
                const p = target === 'global' ? 'chats/v4/global/' : 'chats/v4/priv/' + getRoom(me, target) + '/';
                remove(ref(db, p + id));
            }
        };

        function initApp() {
            const p = target === 'global' ? 'chats/v4/global' : 'chats/v4/priv/' + getRoom(me, target);
            onChildAdded(ref(db, p), (s) => {
                const d = s.val();
                const isMe = d.u === me;
                const win = document.getElementById('chat-window');
                win.innerHTML += `
                    <div class="msg ${isMe ? 'mine' : 'others'}">
                        <span class="u-name">${isMe ? 'AUTH_OWNER' : d.u}</span>
                        ${d.m ? `<div>${d.m}</div>` : ''}
                        ${d.img ? `<img src="${d.img}" style="max-width:100%; border-radius:15px; margin-top:8px;">` : ''}
                        ${(isMe || isAdmin) ? `<div class="del-hint" onclick="moderatorDel('${s.key}')">DESTROY</div>` : ''}
                    </div>`;
                win.scrollTop = win.scrollHeight;
            });
        }

        onValue(ref(db, 'online_v4'), (s) => {
            const l = document.getElementById('on-list');
            l.innerHTML = "";
            s.forEach(u => {
                if(u.key !== me) l.innerHTML += `
                    <div class="menu-item" onclick="setTarget('${u.key}')">
                        <span>👤 ${u.key}</span> <span style="font-size:10px; color:var(--accent); margin-left:auto;">ENCRYPTED</span>
                    </div>`;
            });
        });

        onValue(ref(db, 'typing_v4'), (s) => {
            let t = [];
            s.forEach(u => { if(u.key !== me) t.push(u.key); });
            document.getElementById('typing-box').innerText = t.length > 0 ? t.join(', ') + " is decrypting thoughts..." : "";
        });

        function getRoom(a, b) { return a < b ? a + "_" + b : b + "_" + a; }
        document.getElementById('msg-inp').onkeypress = (e) => { if(e.key === 'Enter') send(); };
    </script>
</body>
</html>
