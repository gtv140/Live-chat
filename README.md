<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Prime Solutions Elite</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root { --gold: #FFD700; --purple: #8A2BE2; --dark: #080810; --glass: rgba(255, 255, 255, 0.05); }
        
        * { box-sizing: border-box; }
        body { margin: 0; background: var(--dark); color: white; font-family: 'Rajdhani', sans-serif; height: 100vh; overflow: hidden; }
        
        .app-wrapper { display: flex; height: 100vh; position: relative; }

        /* Sidebar - Responsive Fix */
        .sidebar { 
            width: 280px; background: rgba(5, 5, 15, 0.98); border-right: 1px solid rgba(138, 43, 226, 0.3); 
            display: flex; flex-direction: column; padding: 20px; z-index: 100; transition: 0.4s;
        }
        
        /* Main Container */
        .main-container { flex: 1; display: flex; flex-direction: column; position: relative; background: radial-gradient(circle at top right, #1a0b2e, #080810); }

        /* Mobile Menu Toggle */
        .menu-btn { display: none; background: var(--purple); color: white; border: none; padding: 10px; cursor: pointer; border-radius: 5px; margin-right: 10px; }

        .header { padding: 15px 20px; border-bottom: 1px solid rgba(255,255,255,0.05); display: flex; align-items: center; background: rgba(0,0,0,0.3); }

        #messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 15px; scroll-behavior: smooth; }

        /* Chat Bubbles */
        .msg-card { max-width: 85%; padding: 12px 16px; border-radius: 20px; position: relative; font-size: 1rem; line-height: 1.4; animation: fadeIn 0.3s ease; }
        .mine { align-self: flex-end; background: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%); border-bottom-right-radius: 2px; box-shadow: 0 4px 15px rgba(37, 117, 252, 0.2); }
        .others { align-self: flex-start; background: #1e1e2e; border: 1px solid rgba(255,255,255,0.1); border-bottom-left-radius: 2px; }
        .bot-msg { align-self: center; background: rgba(255,215,0,0.1); border: 1px solid var(--gold); font-size: 0.8rem; text-align: center; color: var(--gold); border-radius: 30px; }

        .sender-info { font-size: 0.75rem; color: var(--gold); font-weight: 700; margin-bottom: 5px; font-family: 'Orbitron'; }
        .shared-img { max-width: 100%; border-radius: 12px; margin-top: 8px; border: 1px solid rgba(255,255,255,0.1); }

        /* Input Bar */
        .input-box { padding: 15px 20px; background: #0c0c14; display: flex; gap: 10px; align-items: center; border-top: 1px solid rgba(138,43,226,0.2); }
        input[type="text"] { flex: 1; background: #161625; border: 1px solid #333; padding: 14px 20px; border-radius: 25px; color: white; outline: none; transition: 0.3s; }
        input[type="text"]:focus { border-color: var(--purple); box-shadow: 0 0 10px rgba(138,43,226,0.3); }
        .send-btn { background: var(--gold); border: none; width: 45px; height: 45px; border-radius: 50%; font-weight: bold; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 1.2rem; flex-shrink: 0; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* MOBILE RESPONSIVENESS */
        @media (max-width: 768px) {
            .sidebar { position: absolute; left: -280px; height: 100%; box-shadow: 10px 0 20px rgba(0,0,0,0.5); }
            .sidebar.open { left: 0; }
            .menu-btn { display: block; }
            .msg-card { max-width: 90%; }
        }

        /* Nav Items */
        .nav-item { padding: 12px; cursor: pointer; border-radius: 12px; margin-bottom: 8px; color: #888; display: flex; align-items: center; gap: 10px; font-weight: 600; }
        .nav-item.active, .nav-item:hover { background: rgba(138, 43, 226, 0.2); color: white; border: 1px solid var(--purple); }
    </style>
</head>
<body>

    <div id="login-overlay" style="position:fixed; inset:0; background:var(--dark); z-index:5000; display:flex; align-items:center; justify-content:center; padding:20px;">
        <div style="background:rgba(20,20,30,0.9); padding:40px; border-radius:30px; border:1px solid var(--purple); text-align:center; width:100%; max-width:350px;">
            <h1 style="font-family:'Orbitron'; color:var(--gold); margin:0;">PRIME</h1>
            <p style="letter-spacing:3px; font-size:0.8rem; color:#666; margin-bottom:30px;">CONNECT</p>
            <input type="text" id="userInput" placeholder="Username" style="width:100%; padding:15px; margin-bottom:10px; border-radius:10px; border:none; background:#000; color:white;">
            <input type="password" id="passInput" placeholder="Password" style="width:100%; padding:15px; margin-bottom:20px; border-radius:10px; border:none; background:#000; color:white;">
            <button onclick="handleAuth()" style="width:100%; padding:15px; background:var(--gold); border:none; border-radius:10px; font-weight:bold; cursor:pointer; font-family:'Orbitron';">AUTHENTICATE</button>
        </div>
    </div>

    <div class="app-wrapper">
        <div class="sidebar" id="sidebar">
            <h2 id="myUserDisplay" style="color:var(--gold); font-size:1rem; font-family:'Orbitron';">@USER</h2>
            <div class="nav-item active" onclick="showPage('chat-page'); toggleSidebar()">🌐 Global Hub</div>
            <div class="nav-item" onclick="showPage('about-page'); toggleSidebar()">🏢 About Prime</div>
            
            <h2 style="font-size:0.7rem; color:#555; margin-top:30px;">ACTIVE OPERATORS</h2>
            <div id="online-list"></div>
            
            <div id="admin-zone" style="margin-top:auto;"></div>
        </div>

        <div class="main-container">
            <div class="header">
                <button class="menu-btn" onclick="toggleSidebar()">☰</button>
                <div id="active-target" style="font-family:'Orbitron'; color:var(--gold); font-size:0.9rem;"># GLOBAL-HUB</div>
                <div style="margin-left:auto; font-size:0.7rem; color:#00ff88;">● SECURE</div>
            </div>

            <div id="chat-page" class="page active-page">
                <div id="messages"></div>
                <div id="upload-status" style="padding:0 20px; font-size:0.7rem; color:var(--gold);"></div>
                <div class="input-box">
                    <label for="imgInput" style="cursor:pointer; font-size:1.5rem;">🖼️</label>
                    <input type="file" id="imgInput" style="display:none" accept="image/*">
                    <input type="text" id="msgInput" placeholder="Message...">
                    <button class="send-btn" onclick="doSend()">➔</button>
                </div>
            </div>

            <div id="about-page" class="page" style="padding:40px; overflow-y:auto;">
                <h1 style="color:var(--gold); font-family:'Orbitron';">PRIME SOLUTIONS</h1>
                <p>Welcome to Live Connect. This is a high-speed, encrypted communication node developed for Prime Solutions Enterprise.</p>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, get, set, update, remove, onValue, onDisconnect, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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

        let currentUser = "";
        let chatTarget = "global";
        let isAdmin = false;

        window.toggleSidebar = () => {
            document.getElementById('sidebar').classList.toggle('open');
        };

        window.handleAuth = async () => {
            const user = document.getElementById('userInput').value.trim().toLowerCase();
            const pass = document.getElementById('passInput').value.trim();
            if(!user || !pass) return;

            if(user === "admin" && pass === "admin786") {
                isAdmin = true; currentUser = "Admin";
                document.getElementById('admin-zone').innerHTML = `<div class="nav-item" style="color:red; border:1px solid red" onclick="alert('Admin Panel Open')">⚠️ ADMIN CORE</div>`;
            } else {
                currentUser = user;
            }

            const pRef = ref(db, 'presence/' + currentUser);
            set(pRef, { name: currentUser, online: true });
            onDisconnect(pRef).remove();

            document.getElementById('login-overlay').style.display = 'none';
            document.getElementById('myUserDisplay').innerText = "@" + currentUser.toUpperCase();
            initApp();
        };

        function initApp() {
            onValue(ref(db, 'presence'), (snap) => {
                const list = document.getElementById('online-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    if(c.val().name !== currentUser) {
                        list.innerHTML += `<div class="nav-item" onclick="setTarget('${c.val().name}')"><span class="status-dot"></span> ${c.val().name.toUpperCase()}</div>`;
                    }
                });
            });
            listenMessages();
        }

        window.showPage = (id) => {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
            document.getElementById(id).classList.add('active-page');
        };

        window.setTarget = (t) => {
            chatTarget = t;
            document.getElementById('active-target').innerText = t === 'global' ? '# GLOBAL-HUB' : `@ ${t.toUpperCase()}`;
            document.getElementById('messages').innerHTML = "";
            showPage('chat-page');
            listenMessages();
            if(window.innerWidth <= 768) toggleSidebar();
        };

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            push(ref(db, getPath()), { user: currentUser, text: inp.value, time: serverTimestamp() });
            inp.value = "";
        };

        document.getElementById('imgInput').onchange = function(e) {
            const file = e.target.files[0];
            if (!file || file.size > 1000000) return alert("Photo too big (Max 1MB)");
            const reader = new FileReader();
            document.getElementById('upload-status').innerText = "Sending Photo...";
            reader.onload = (event) => {
                push(ref(db, getPath()), { user: currentUser, image: event.target.result, time: serverTimestamp() });
                document.getElementById('upload-status').innerText = "";
            };
            reader.readAsDataURL(file);
        };

        function getPath() {
            return chatTarget === 'global' ? 'messages/global' : `messages/private/${[currentUser, chatTarget].sort().join('_')}`;
        }

        function listenMessages() {
            onChildAdded(ref(db, getPath()), (snap) => {
                const d = snap.val();
                const isMe = d.user === currentUser;
                const mBox = document.getElementById('messages');
                const content = d.image ? `<img src="${d.image}" class="shared-img">` : d.text;
                
                const html = `
                    <div class="msg-card ${d.isBot ? 'bot-msg' : (isMe ? 'mine' : 'others')}">
                        ${!isMe && !d.isBot ? `<span class="sender-info">${d.user.toUpperCase()}</span>` : ''}
                        ${content}
                    </div>`;
                mBox.insertAdjacentHTML('beforeend', html);
                mBox.scrollTop = mBox.scrollHeight;
            });
        }
    </script>
</body>
</html>
