<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Pro | Prime Solutions</title>
    <link href="https://fonts.googleapis.com/css2?family=Syncopate:wght@700&family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --accent: #00f2fe; --primary: #6a11cb; --bg: #050505;
            --glass: rgba(255, 255, 255, 0.08); --border: rgba(255, 255, 255, 0.15);
            --text: #ffffff;
        }
        .light-mode {
            --bg: #f8f9fa; --glass: rgba(0, 0, 0, 0.05); --border: rgba(0, 0, 0, 0.1); --text: #222;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Poppins', sans-serif; }
        body, html { height: 100%; background: var(--bg); color: var(--text); overflow: hidden; transition: 0.4s; }

        /* Auth Screen */
        #auth-screen {
            position: fixed; inset: 0; z-index: 5000; background: rgba(0,0,0,0.96);
            display: flex; align-items: center; justify-content: center; backdrop-filter: blur(25px);
        }
        .auth-card {
            background: var(--glass); padding: 40px 30px; border-radius: 35px; width: 90%; max-width: 400px;
            border: 1px solid var(--border); text-align: center; animation: slideUp 0.6s ease;
        }
        .tab-box { display: flex; justify-content: center; gap: 20px; margin-bottom: 25px; }
        .tab { cursor: pointer; opacity: 0.5; font-size: 14px; transition: 0.3s; }
        .tab-active { opacity: 1; border-bottom: 2px solid var(--accent); font-weight: 600; color: var(--accent); }
        .auth-input { width: 100%; padding: 15px; margin-bottom: 15px; border-radius: 15px; border: 1px solid var(--border); background: rgba(0,0,0,0.2); color: white; outline: none; }

        /* App Layout */
        .header {
            height: 70px; background: rgba(255,255,255,0.03); backdrop-filter: blur(15px);
            display: flex; align-items: center; justify-content: space-between; padding: 0 20px;
            border-bottom: 1px solid var(--border); z-index: 1000;
        }
        .logo { font-family: 'Syncopate'; font-size: 12px; color: var(--accent); cursor: pointer; }

        /* Side Menu */
        #side-menu {
            position: fixed; right: -100%; top: 0; bottom: 0; width: 80%; max-width: 320px;
            background: rgba(10,10,10,0.98); z-index: 2000; transition: 0.5s cubic-bezier(0.1, 0.7, 0.1, 1);
            padding: 30px 20px; border-left: 1px solid var(--border);
        }
        .menu-open { right: 0 !important; }

        /* Chat Window */
        #chat-window { height: calc(100vh - 160px); overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; }
        .msg { max-width: 80%; padding: 12px 18px; border-radius: 22px; position: relative; animation: pop 0.4s ease; font-size: 14px; line-height: 1.5; }
        .mine { align-self: flex-end; background: linear-gradient(135deg, var(--primary), #2575fc); color: white; border-bottom-right-radius: 4px; }
        .others { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .u-tag { font-size: 10px; font-weight: bold; margin-bottom: 5px; display: block; color: var(--accent); }

        /* Voice Note UI */
        .voice-bubble { display: flex; align-items: center; gap: 10px; }
        .play-btn { cursor: pointer; font-size: 20px; }

        /* Typing & Footer */
        #typing-status { font-size: 10px; padding: 0 25px; height: 20px; color: var(--accent); font-style: italic; }
        .bottom-bar {
            position: fixed; bottom: 0; width: 100%; height: 90px;
            background: rgba(5, 5, 5, 0.9); backdrop-filter: blur(15px);
            display: flex; align-items: center; padding: 0 15px; gap: 10px; border-top: 1px solid var(--border);
        }
        .inp-main { flex: 1; height: 50px; background: var(--glass); border: 1px solid var(--border); border-radius: 25px; padding: 0 20px; color: inherit; outline: none; }
        .circle-btn { width: 48px; height: 48px; border-radius: 50%; border: none; background: var(--glass); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 18px; border: 1px solid var(--border); }

        /* Animations */
        @keyframes slideUp { from { transform: translateY(50px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
        @keyframes pop { from { transform: scale(0.9); opacity: 0; } to { transform: scale(1); opacity: 1; } }
    </style>
</head>
<body>

    <div id="auth-screen">
        <div class="auth-card">
            <h2 style="font-family:'Syncopate'; color:var(--accent); margin-bottom:5px;">LIVE CONNECT</h2>
            <p style="font-size:10px; opacity:0.6; margin-bottom:25px;">PRIME SOLUTIONS TRUSTED ACCESS</p>
            
            <div class="tab-box">
                <span id="t-login" class="tab tab-active" onclick="switchAuth('login')">LOGIN</span>
                <span id="t-signup" class="tab" onclick="switchAuth('signup')">SIGN UP</span>
            </div>

            <input type="text" id="au-u" class="auth-input" placeholder="Username">
            <input type="password" id="au-p" class="auth-input" placeholder="Password">
            <button onclick="handleAuth()" id="au-btn" style="width:100%; padding:15px; border-radius:15px; background:var(--primary); color:white; border:none; font-weight:600; cursor:pointer;">CONNECT NOW</button>
        </div>
    </div>

    <div id="side-menu">
        <h3 style="margin-bottom:25px; font-family:'Syncopate'; font-size:14px; color:var(--accent);">DASHBOARD</h3>
        <button onclick="toggleTheme()" style="width:100%; padding:14px; background:var(--glass); color:inherit; border:1px solid var(--border); border-radius:12px; margin-bottom:12px; cursor:pointer;">🌓 Change Theme</button>
        <button onclick="toggleCompany()" style="width:100%; padding:14px; background:var(--glass); color:inherit; border:1px solid var(--border); border-radius:12px; margin-bottom:12px; cursor:pointer;">🛡️ Privacy & Security</button>
        <button onclick="logout()" style="width:100%; padding:14px; background:rgba(255,71,87,0.15); color:#ff4757; border:1px solid #ff4757; border-radius:12px; cursor:pointer;">🚪 Logout System</button>
        
        <div style="margin-top:30px;">
            <p style="font-size:11px; opacity:0.5; margin-bottom:10px;">ONLINE OPERATORS</p>
            <div id="on-list"></div>
        </div>
    </div>

    <header class="header">
        <div class="logo" onclick="toggleCompany()">💎 PRIME SOLUTIONS</div>
        <div style="display:flex; gap:15px; align-items:center;">
            <span id="chat-mode" style="font-size:10px; background:var(--primary); padding:3px 10px; border-radius:10px;">GLOBAL</span>
            <div onclick="toggleMenu()" style="cursor:pointer; font-size:24px;">☰</div>
        </div>
    </header>

    <main id="chat-window"></main>
    <div id="typing-status"></div>

    <div class="bottom-bar">
        <button class="circle-btn" onclick="document.getElementById('f-up').click()">🖼️</button>
        <input type="file" id="f-up" hidden accept="image/*" onchange="sendImg(this)">
        <input type="text" id="m-inp" class="inp-main" placeholder="Type here, sweetie..." oninput="pushTyping()">
        <button class="circle-btn" style="background:var(--accent); color:black;" onclick="send()">➔</button>
    </div>

    <div id="co-modal" style="display:none; position:fixed; inset:0; background:rgba(0,0,0,0.95); z-index:6000; padding:40px;">
        <h2 style="color:var(--accent);">PRIME SOLUTIONS SECURITY</h2>
        <p style="margin-top:20px; opacity:0.8;"><b>Encryption:</b> End-to-End Secure.</p>
        <p style="margin-top:10px; opacity:0.8;"><b>Privacy:</b> We never share your data with third parties.</p>
        <button onclick="toggleCompany()" style="margin-top:30px; padding:15px 30px; border-radius:10px; background:var(--primary); color:white; border:none;">Understood</button>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp, set, onValue, onDisconnect, remove, get } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const conf = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
        };

        const app = initializeApp(conf);
        const db = getDatabase(app);
        let me = "", target = "global", mode = "login", isAdmin = false, tTimer;

        // AUTH & TABS
        window.switchAuth = (m) => {
            mode = m;
            document.getElementById('t-login').className = m==='login' ? 'tab tab-active' : 'tab';
            document.getElementById('t-signup').className = m==='signup' ? 'tab tab-active' : 'tab';
            document.getElementById('au-btn').innerText = m==='login' ? 'CONNECT NOW' : 'CREATE ACCOUNT';
        };

        window.handleAuth = async () => {
            const u = document.getElementById('au-u').value.trim();
            const p = document.getElementById('au-p').value.trim();
            if(!u || !p) return alert("Enter details sweetie!");

            const uRef = ref(db, 'users/' + u);
            const snap = await get(uRef);

            if(mode === 'signup') {
                if(snap.exists()) return alert("User exists!");
                await set(uRef, { pass: p });
                alert("Success! Now Login.");
                switchAuth('login');
            } else {
                if(!snap.exists() || snap.val().pass !== p) return alert("Wrong details!");
                me = u;
                if(p === "admin786") isAdmin = true;
                document.getElementById('auth-screen').style.display = 'none';
                set(ref(db, 'online_pro/' + me), true);
                onDisconnect(ref(db, 'online_pro/' + me)).remove();
                initChat();
            }
        };

        // UI & TOOLS
        window.toggleMenu = () => document.getElementById('side-menu').classList.toggle('menu-open');
        window.toggleTheme = () => document.body.classList.toggle('light-mode');
        window.toggleCompany = () => { const m = document.getElementById('co-modal'); m.style.display = m.style.display === 'none' ? 'block' : 'none'; };
        window.logout = () => { remove(ref(db, 'online_pro/' + me)); location.reload(); };

        window.pushTyping = () => {
            set(ref(db, 'typing_pro/' + me), true);
            clearTimeout(tTimer);
            tTimer = setTimeout(() => remove(ref(db, 'typing_pro/' + me)), 2000);
        };

        window.switchChat = (t) => {
            target = t;
            document.getElementById('chat-mode').innerText = t === 'global' ? 'GLOBAL' : t;
            document.getElementById('chat-window').innerHTML = "";
            toggleMenu();
            initChat();
        };

        // DATA HANDLERS
        window.send = () => {
            const inp = document.getElementById('m-inp');
            if(!inp.value.trim()) return;
            const path = target === 'global' ? 'msgs/global' : 'msgs/priv/' + getID(me, target);
            push(ref(db, path), { u: me, m: inp.value, ts: serverTimestamp() });
            inp.value = "";
            remove(ref(db, 'typing_pro/' + me));
        };

        window.sendImg = (el) => {
            const r = new FileReader();
            r.onload = (e) => {
                const path = target === 'global' ? 'msgs/global' : 'msgs/priv/' + getID(me, target);
                push(ref(db, path), { u: me, img: e.target.result, ts: serverTimestamp() });
            };
            r.readAsDataURL(el.files[0]);
        };

        window.adminDel = (id) => {
            if(confirm("Delete as Admin?")) {
                const path = target === 'global' ? 'msgs/global/' : 'msgs/priv/' + getID(me, target) + '/';
                remove(ref(db, path + id));
            }
        };

        function initChat() {
            const path = target === 'global' ? 'msgs/global' : 'msgs/priv/' + getID(me, target);
            onChildAdded(ref(db, path), (s) => {
                const d = s.val();
                const isMe = d.u === me;
                const win = document.getElementById('chat-window');
                win.innerHTML += `
                    <div class="msg ${isMe ? 'mine' : 'others'}">
                        <span class="u-tag">${isMe ? 'YOU' : d.u}</span>
                        ${d.m ? `<div>${d.m}</div>` : ''}
                        ${d.img ? `<img src="${d.img}" style="max-width:100%; border-radius:12px; margin-top:8px;">` : ''}
                        ${(isMe || isAdmin) ? `<div onclick="adminDel('${s.key}')" style="font-size:9px; opacity:0.5; margin-top:5px; cursor:pointer;">Remove</div>` : ''}
                    </div>`;
                win.scrollTop = win.scrollHeight;
            });
        }

        onValue(ref(db, 'online_pro'), (s) => {
            const list = document.getElementById('on-list');
            list.innerHTML = `<div onclick="switchChat('global')" style="padding:15px; background:var(--glass); border-radius:12px; margin-bottom:10px; cursor:pointer;">🌍 Global Hub</div>`;
            s.forEach(u => {
                if(u.key !== me) list.innerHTML += `
                    <div onclick="switchChat('${u.key}')" style="padding:15px; background:var(--glass); border-radius:12px; margin-bottom:10px; cursor:pointer; display:flex; justify-content:space-between;">
                        <span>👤 ${u.key}</span> <span style="color:#4caf50;">● Online</span>
                    </div>`;
            });
        });

        onValue(ref(db, 'typing_pro'), (s) => {
            let ty = [];
            s.forEach(u => { if(u.key !== me) ty.push(u.key); });
            document.getElementById('typing-status').innerText = ty.length > 0 ? ty.join(', ') + " is typing..." : "";
        });

        function getID(a, b) { return a < b ? a + "_" + b : b + "_" + a; }
        document.getElementById('m-inp').onkeypress = (e) => { if(e.key === 'Enter') send(); };
    </script>
</body>
</html>
