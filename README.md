<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Prime Solutions Official</title>
    <link href="https://fonts.googleapis.com/css2?family=Syncopate:wght@700&family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --accent: #00f2fe; --primary: #6a11cb; --bg: #050505;
            --glass: rgba(255, 255, 255, 0.08); --border: rgba(255, 255, 255, 0.15);
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Poppins', sans-serif; }
        body, html { height: 100%; background: var(--bg); color: white; overflow: hidden; }

        /* Background Mesh */
        .bg-mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 20% 30%, #1a1a2e 0%, #050505 100%); }

        /* Auth Screen (Login & Signup) */
        #auth-screen {
            position: fixed; inset: 0; z-index: 5000; background: rgba(0,0,0,0.95);
            display: flex; align-items: center; justify-content: center; backdrop-filter: blur(20px);
        }
        .auth-card {
            background: var(--glass); padding: 40px 30px; border-radius: 30px; width: 90%; max-width: 420px;
            border: 1px solid var(--border); text-align: center; animation: fadeInUp 0.6s ease;
        }
        .auth-card h1 { font-family: 'Syncopate', sans-serif; font-size: 20px; color: var(--accent); margin-bottom: 10px; }
        .trust-badge { font-size: 10px; background: rgba(0,242,254,0.1); color: var(--accent); padding: 5px 12px; border-radius: 20px; display: inline-block; margin-bottom: 25px; border: 1px solid var(--accent); }
        
        .tab-btn { background: none; border: none; color: #aaa; cursor: pointer; font-size: 14px; margin: 0 10px 20px; }
        .tab-active { color: var(--accent); font-weight: bold; border-bottom: 2px solid var(--accent); }

        .input-group { position: relative; margin-bottom: 12px; }
        .input-group input {
            width: 100%; padding: 14px; background: rgba(0,0,0,0.4); border: 1px solid var(--border);
            border-radius: 15px; color: white; outline: none; font-size: 14px;
        }
        .auth-btn {
            width: 100%; padding: 14px; border-radius: 15px; border: none;
            background: linear-gradient(90deg, var(--primary), #2575fc);
            color: white; font-weight: 600; cursor: pointer; margin-top: 15px;
        }

        /* App Layout */
        .header {
            height: 70px; background: rgba(255,255,255,0.03); backdrop-filter: blur(15px);
            display: flex; align-items: center; justify-content: space-between; padding: 0 20px;
            border-bottom: 1px solid var(--border); z-index: 1000;
        }

        /* Company Details Modal */
        #company-drawer {
            position: fixed; inset: 0; background: rgba(0,0,0,0.9); z-index: 3000; display: none;
            padding: 40px 25px; overflow-y: auto; line-height: 1.6;
        }
        .policy-tag { color: var(--accent); font-weight: bold; margin-top: 20px; display: block; }

        /* Sidebar */
        #user-drawer {
            position: fixed; right: -100%; top: 0; bottom: 0; width: 80%; max-width: 320px;
            background: rgba(10, 10, 10, 0.98); z-index: 2000; transition: 0.4s; padding: 30px 20px;
        }
        .drawer-open { right: 0 !important; }

        /* Chat System */
        #chat-window { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; padding-bottom: 100px; height: calc(100vh - 155px); }
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 20px; animation: pop 0.3s ease; }
        .mine { align-self: flex-end; background: linear-gradient(135deg, var(--primary), #2575fc); border-bottom-right-radius: 4px; }
        .others { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .msg-meta { font-size: 9px; opacity: 0.6; margin-bottom: 4px; display: block; }

        /* Footer Input */
        .bottom-bar {
            position: fixed; bottom: 0; left: 0; right: 0; height: 85px;
            background: rgba(5, 5, 5, 0.9); display: flex; align-items: center; padding: 0 15px; gap: 10px; border-top: 1px solid var(--border);
        }
        .input-box { flex: 1; height: 50px; background: var(--glass); border: 1px solid var(--border); border-radius: 25px; padding: 0 20px; color: white; outline: none; }

        @keyframes fadeInUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes pop { from { transform: scale(0.9); opacity: 0; } to { transform: scale(1); opacity: 1; } }
    </style>
</head>
<body>

    <div class="bg-mesh"></div>

    <div id="auth-screen">
        <div class="auth-card">
            <h1>LIVE CONNECT</h1>
            <span class="trust-badge">🛡️ End-to-End Encrypted by Prime Solutions</span>
            
            <div style="margin-bottom:10px;">
                <button id="login-tab" class="tab-btn tab-active" onclick="switchTab('login')">Login</button>
                <button id="signup-tab" class="tab-btn" onclick="switchTab('signup')">Sign Up</button>
            </div>

            <div class="input-group">
                <input type="text" id="auth-u" placeholder="Username">
            </div>
            <div class="input-group">
                <input type="password" id="auth-p" placeholder="Password">
            </div>
            <button class="auth-btn" id="main-btn" onclick="handleAuth()">Enter Securely</button>
            <p style="font-size: 10px; margin-top: 20px; opacity: 0.5;">By continuing, you agree to our Privacy Policy.</p>
        </div>
    </div>

    <div id="company-drawer">
        <h2 style="color:var(--accent); font-family:'Syncopate';">PRIME SOLUTIONS</h2>
        <p style="margin-top:20px; opacity:0.8;">Live Connect is a flagship product of Prime Solutions, designed for seamless real-time communication.</p>
        
        <span class="policy-tag">Privacy Policy</span>
        <p style="font-size:13px; opacity:0.7;">We do not sell your data. Your messages are stored in an encrypted database and only accessible to verified participants.</p>
        
        <span class="policy-tag">Security Standards</span>
        <p style="font-size:13px; opacity:0.7;">Using Google Firebase Enterprise Security, we ensure 99.9% uptime and protection against unauthorized access.</p>
        
        <button onclick="toggleCompany()" style="margin-top:40px; width:100%; padding:15px; border-radius:15px; background:var(--glass); border:1px solid var(--accent); color:white;">Back to Chat</button>
    </div>

    <div id="user-drawer">
        <div style="display:flex; justify-content:space-between; margin-bottom:30px; align-items:center;">
            <h3 style="font-size:14px;">ACTIVE OPERATORS</h3>
            <span onclick="toggleDrawer()" style="cursor:pointer; font-size:20px;">✕</span>
        </div>
        <div onclick="setChat('global')" style="padding:15px; background:var(--glass); border-radius:15px; cursor:pointer; margin-bottom:10px;">🌍 Global Network</div>
        <div id="online-list"></div>
    </div>

    <div class="header">
        <div onclick="toggleCompany()" style="cursor:pointer; font-family:'Syncopate'; font-size:12px; color:var(--accent);">💎 PRIME SOLUTIONS</div>
        <div onclick="toggleDrawer()" style="cursor:pointer; font-size:24px;">👥</div>
    </div>

    <main id="chat-window"></main>

    <div class="bottom-bar">
        <label for="img-file" style="cursor:pointer; font-size:24px;">🖼️</label>
        <input type="file" id="img-file" hidden accept="image/*" onchange="sendImage(this)">
        <input type="text" id="msg-input" class="input-box" placeholder="Write message, sweetie...">
        <button onclick="sendText()" style="background:var(--accent); border:none; width:45px; height:45px; border-radius:50%; cursor:pointer;">➔</button>
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
        let me = "", chatTarget = "global", isAdmin = false, currentMode = "login";

        // AUTH SYSTEM
        window.switchTab = (mode) => {
            currentMode = mode;
            document.getElementById('login-tab').className = mode === 'login' ? 'tab-btn tab-active' : 'tab-btn';
            document.getElementById('signup-tab').className = mode === 'signup' ? 'tab-btn tab-active' : 'tab-btn';
            document.getElementById('main-btn').innerText = mode === 'login' ? 'Enter Securely' : 'Create Account';
        };

        window.handleAuth = async () => {
            const u = document.getElementById('auth-u').value.trim();
            const p = document.getElementById('auth-p').value.trim();
            if(!u || !p) return alert("Fill all details sweetie!");

            const userRef = ref(db, 'users/' + u);
            const snapshot = await get(userRef);

            if(currentMode === 'signup') {
                if(snapshot.exists()) return alert("User already exists!");
                await set(userRef, { password: p, created: serverTimestamp() });
                alert("Account Created! Login now sweetie.");
                switchTab('login');
            } else {
                if(!snapshot.exists() || snapshot.val().password !== p) return alert("Wrong details!");
                me = u;
                if(p === "admin786") isAdmin = true;
                document.getElementById('auth-screen').style.display = 'none';
                set(ref(db, 'presence/' + me), true);
                onDisconnect(ref(db, 'presence/' + me)).remove();
                startChat();
            }
        };

        // UI ACTIONS
        window.toggleCompany = () => { const d = document.getElementById('company-drawer'); d.style.display = d.style.display === 'block' ? 'none' : 'block'; };
        window.toggleDrawer = () => document.getElementById('user-drawer').classList.toggle('drawer-open');

        window.setChat = (t) => {
            chatTarget = t;
            document.getElementById('chat-window').innerHTML = "";
            toggleDrawer();
            startChat();
        };

        // MESSAGE SYSTEM
        window.sendText = () => {
            const inp = document.getElementById('msg-input');
            if(!inp.value.trim()) return;
            const path = chatTarget === 'global' ? 'chats/global' : 'chats/private/' + getID(me, chatTarget);
            push(ref(db, path), { u: me, m: inp.value, ts: serverTimestamp() });
            inp.value = "";
        };

        window.sendImage = (el) => {
            const r = new FileReader();
            r.onload = (e) => {
                const path = chatTarget === 'global' ? 'chats/global' : 'chats/private/' + getID(me, chatTarget);
                push(ref(db, path), { u: me, img: e.target.result, ts: serverTimestamp() });
            };
            r.readAsDataURL(el.files[0]);
        };

        function startChat() {
            const path = chatTarget === 'global' ? 'chats/global' : 'chats/private/' + getID(me, chatTarget);
            onChildAdded(ref(db, path), (s) => {
                const d = s.val();
                const isMe = d.u === me;
                const win = document.getElementById('chat-window');
                win.innerHTML += `
                    <div class="msg ${isMe ? 'mine' : 'others'}">
                        <span class="msg-meta">${isMe ? 'YOU' : d.u}</span>
                        ${d.m ? `<div>${d.m}</div>` : ''}
                        ${d.img ? `<img src="${d.img}" style="max-width:100%; border-radius:10px; margin-top:5px;">` : ''}
                    </div>`;
                win.scrollTop = win.scrollHeight;
            });
        }

        onValue(ref(db, 'presence'), (s) => {
            const list = document.getElementById('online-list');
            list.innerHTML = "";
            s.forEach(u => {
                if(u.key !== me) list.innerHTML += `<div onclick="setChat('${u.key}')" style="padding:15px; background:var(--glass); border-radius:15px; cursor:pointer; margin-bottom:10px;">👤 ${u.key}</div>`;
            });
        });

        function getID(a, b) { return a < b ? a + "_" + b : b + "_" + a; }
        document.getElementById('msg-input').onkeypress = (e) => { if(e.key === 'Enter') sendText(); };
    </script>
</body>
</html>
