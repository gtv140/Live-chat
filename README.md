<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Prime Solutions Ultimate</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root { --gold: #FFD700; --purple: #8A2BE2; --dark: #05050a; --glass: rgba(255, 255, 255, 0.05); }
        body { margin: 0; background: var(--dark); color: white; font-family: 'Rajdhani', sans-serif; height: 100vh; display: flex; flex-direction: column; overflow: hidden; }
        .app-wrapper { display: flex; flex: 1; overflow: hidden; }
        
        /* Sidebar */
        .sidebar { width: 280px; background: rgba(0,0,0,0.9); border-right: 1px solid var(--purple); display: flex; flex-direction: column; padding: 20px; backdrop-filter: blur(15px); }
        .sidebar h2 { font-family: 'Orbitron'; font-size: 0.75rem; color: var(--gold); margin: 25px 0 10px; text-transform: uppercase; border-bottom: 1px solid rgba(255,255,255,0.1); }
        .nav-item { padding: 12px; cursor: pointer; border-radius: 10px; margin-bottom: 5px; transition: 0.3s; color: #bbb; display: flex; align-items: center; gap: 10px; }
        .nav-item:hover, .nav-item.active { background: var(--purple); color: white; box-shadow: 0 0 15px rgba(138,43,226,0.3); }
        .status-dot { width: 8px; height: 8px; background: #00ff88; border-radius: 50%; box-shadow: 0 0 8px #00ff88; }

        /* Main Area */
        .main-container { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at center, #0f0f1e, #05050a); position: relative; }
        .header { padding: 15px 25px; border-bottom: 1px solid rgba(138,43,226,0.2); display: flex; justify-content: space-between; align-items: center; }
        
        /* Pages */
        .page { display: none; flex: 1; flex-direction: column; overflow: hidden; }
        .active-page { display: flex; }

        /* Messages */
        #messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; }
        .msg-card { max-width: 75%; padding: 12px; border-radius: 15px; background: var(--glass); border: 1px solid rgba(255,255,255,0.03); position: relative; }
        .mine { align-self: flex-end; border-color: var(--purple); background: linear-gradient(135deg, rgba(138,43,226,0.1), rgba(0,0,0,0.4)); }
        .others { align-self: flex-start; }
        .bot-msg { align-self: center; border: 1px solid var(--gold); background: rgba(255, 215, 0, 0.05); font-style: italic; font-size: 0.85rem; text-align: center; }
        
        .sender-info { font-size: 0.7rem; color: var(--gold); font-weight: bold; margin-bottom: 4px; display: block; }
        .shared-img { max-width: 100%; border-radius: 10px; margin-top: 5px; border: 1px solid var(--purple); }
        
        /* Actions */
        .msg-actions { font-size: 0.7rem; margin-top: 8px; display: flex; gap: 10px; color: #666; }
        .msg-actions span:hover { color: var(--gold); cursor: pointer; }

        /* Input */
        .input-box { padding: 15px 25px; background: rgba(0,0,0,0.8); display: flex; gap: 12px; align-items: center; }
        input[type="text"] { flex: 1; background: rgba(255,255,255,0.05); border: 1px solid var(--purple); padding: 12px 20px; border-radius: 30px; color: white; outline: none; }
        
        /* Overlays */
        #login-overlay { position: fixed; inset: 0; background: var(--dark); z-index: 5000; display: flex; align-items: center; justify-content: center; }
        #admin-overlay { position: absolute; inset: 0; background: var(--dark); z-index: 1000; display: none; padding: 30px; overflow-y: auto; }
        
        .info-content { padding: 40px; line-height: 1.6; color: #ccc; max-width: 800px; margin: 0 auto; }
        .info-content h1 { color: var(--gold); font-family: 'Orbitron'; }
    </style>
</head>
<body>

    <div id="login-overlay">
        <div style="background: var(--glass); padding: 50px; border-radius: 30px; border: 1px solid var(--purple); text-align: center; width: 320px;">
            <h1 style="font-family: 'Orbitron'; color: var(--gold); margin-bottom: 10px;">PRIME LOGIN</h1>
            <p style="font-size: 0.7rem; color: #888; margin-bottom: 20px;">LIVE CONNECT v3.0</p>
            <input type="text" id="userInput" placeholder="Username" style="display:block; width:90%; margin: 0 auto 10px auto; padding: 10px; border-radius: 5px; border: none;">
            <input type="password" id="passInput" placeholder="Password" style="display:block; width:90%; margin: 0 auto 20px auto; padding: 10px; border-radius: 5px; border: none;">
            <button onclick="handleAuth()" style="width: 100%; padding: 12px; background: var(--gold); border: none; border-radius: 25px; font-weight: bold; cursor: pointer;">ENTER CORE</button>
        </div>
    </div>

    <div class="app-wrapper">
        <div class="sidebar">
            <h2 id="myUserDisplay">@USER</h2>
            <div class="nav-item active" onclick="showPage('chat-page')">🌐 Global Hub</div>
            <div class="nav-item" onclick="showPage('about-page')">🏢 About Prime</div>
            <div class="nav-item" onclick="showPage('privacy-page')">🛡️ Security</div>
            
            <h2>Online Operators</h2>
            <div id="online-list"></div>
            <div id="admin-zone" style="margin-top: auto;"></div>
        </div>

        <div class="main-container">
            <div id="admin-overlay">
                <div style="display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid var(--purple); padding-bottom:10px; margin-bottom:20px;">
                    <h2 style="color:var(--gold); font-family:'Orbitron';">ADMIN CONTROL CENTER</h2>
                    <button onclick="document.getElementById('admin-overlay').style.display='none'" style="background:red; color:white; border:none; padding:5px 15px; cursor:pointer;">CLOSE</button>
                </div>
                <div id="admin-user-list"></div>
            </div>

            <div id="chat-page" class="page active-page">
                <div class="header">
                    <div id="active-target" style="font-family: 'Orbitron'; color: var(--gold);"># GLOBAL-HUB</div>
                    <div id="upload-status" style="font-size: 0.7rem; color: #00ff88;">● SYSTEM ACTIVE</div>
                </div>
                <div id="messages"></div>
                <div class="input-box">
                    <label for="imgInput" style="cursor:pointer; font-size:1.5rem;" title="Select Photo">🖼️</label>
                    <input type="file" id="imgInput" style="display:none" accept="image/*">
                    <input type="text" id="msgInput" placeholder="Type message, sweetie...">
                    <button onclick="doSend()" style="background:var(--gold); border:none; padding:10px 25px; border-radius:30px; font-weight:bold; cursor:pointer;">SEND</button>
                </div>
            </div>

            <div id="about-page" class="page">
                <div class="info-content">
                    <h1>About PRIME SOLUTIONS</h1>
                    <p>Welcome to the future of communication. Prime Solutions provides end-to-end encrypted real-time ecosystems for the modern age.</p>
                </div>
            </div>

            <div id="privacy-page" class="page">
                <div class="info-content">
                    <h1>Security & Privacy</h1>
                    <p>Your data is protected by Prime's proprietary Firebase-backed security core. All private messages are sorted and visible only to the intended recipients.</p>
                </div>
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

        window.handleAuth = async () => {
            const user = document.getElementById('userInput').value.trim().toLowerCase();
            const pass = document.getElementById('passInput').value.trim();
            if(!user || !pass) return;

            if(user === "admin" && pass === "admin786") {
                isAdmin = true; currentUser = "Admin";
                document.getElementById('admin-zone').innerHTML = `<div class="nav-item" style="color:red; border:1px solid red" onclick="openAdmin()">⚠️ ADMIN CORE</div>`;
            } else {
                const uRef = ref(db, 'users/' + user);
                const snap = await get(uRef);
                if(snap.exists() && snap.val().banned) return alert("ACCESS DENIED: BANNED 🚫");
                if(snap.exists() && snap.val().password !== pass) return alert("Invalid Password!");
                if(!snap.exists()) await set(uRef, { password: pass, banned: false });
                currentUser = user;
            }

            const pRef = ref(db, 'presence/' + currentUser);
            set(pRef, { name: currentUser, online: true });
            onDisconnect(pRef).remove();

            // Welcome Bot Message
            if(!isAdmin) {
                push(ref(db, 'messages/global'), { user: "Prime Bot", text: `Welcome back, ${currentUser}! 🚀`, isBot: true, time: serverTimestamp() });
            }

            document.getElementById('login-overlay').style.display = 'none';
            document.getElementById('myUserDisplay').innerText = "@" + currentUser.toUpperCase();
            initApp();
        };

        function initApp() {
            onValue(ref(db, 'presence'), (snap) => {
                const list = document.getElementById('online-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    const u = c.val();
                    if(u.name !== currentUser) {
                        list.innerHTML += `<div class="nav-item" onclick="setTarget('${u.name}')"><span class="status-dot"></span> ${u.name.toUpperCase()}</div>`;
                    }
                });
            });
            listenMessages();
        }

        window.showPage = (id) => {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
            document.getElementById(id).classList.add('active-page');
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
        };

        window.setTarget = (t) => {
            chatTarget = t;
            document.getElementById('active-target').innerText = t === 'global' ? '# GLOBAL-HUB' : `@ ${t.toUpperCase()}`;
            document.getElementById('messages').innerHTML = "";
            showPage('chat-page');
            listenMessages();
        };

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            push(ref(db, getPath()), { user: currentUser, text: inp.value, time: serverTimestamp() });
            inp.value = "";
        };

        // NEW PHOTO SYSTEM (NO STORAGE RULES NEEDED)
        document.getElementById('imgInput').onchange = function(e) {
            const file = e.target.files[0];
            if (!file) return;
            if (file.size > 1500000) return alert("Sweetie, photo bohot badi hai! 1.5MB se kam ki bhejain.");

            const reader = new FileReader();
            document.getElementById('upload-status').innerText = "● SENDING PHOTO...";
            reader.onload = (event) => {
                push(ref(db, getPath()), { user: currentUser, image: event.target.result, time: serverTimestamp() });
                document.getElementById('upload-status').innerText = "● SYSTEM ACTIVE";
            };
            reader.readAsDataURL(file);
        };

        function getPath() {
            return chatTarget === 'global' ? 'messages/global' : `messages/private/${[currentUser, chatTarget].sort().join('_')}`;
        }

        function listenMessages() {
            onChildAdded(ref(db, getPath()), (snap) => {
                const d = snap.val(); const id = snap.key;
                const isMe = d.user === currentUser;
                const mBox = document.getElementById('messages');
                const content = d.image ? `<img src="${d.image}" class="shared-img">` : d.text;
                const adminBtn = (isAdmin || isMe) ? `<span onclick="deleteMsg('${id}')">🗑️ Delete</span>` : "";

                const html = `
                    <div class="msg-card ${d.isBot ? 'bot-msg' : (isMe ? 'mine' : 'others')}">
                        ${!d.isBot ? `<span class="sender-info">${d.user.toUpperCase()}</span>` : ''}
                        ${content}
                        <div class="msg-actions">${adminBtn}</div>
                    </div>`;
                mBox.insertAdjacentHTML('beforeend', html);
                mBox.scrollTop = mBox.scrollHeight;
            });
        }

        window.deleteMsg = (id) => { if(confirm("Delete message for everyone?")) remove(ref(db, getPath() + '/' + id)); };

        window.openAdmin = () => {
            document.getElementById('admin-overlay').style.display='block';
            onValue(ref(db, 'users'), (snap) => {
                const list = document.getElementById('admin-user-list'); list.innerHTML = "";
                snap.forEach(u => {
                    list.innerHTML += `<div style="padding:15px; border-bottom:1px solid #333; display:flex; justify-content:space-between; background:var(--glass); margin-bottom:5px; border-radius:10px;">
                        <span>${u.key.toUpperCase()} - Status: ${u.val().banned ? '🔴 BANNED' : '🟢 ACTIVE'}</span>
                        <button onclick="toggleBan('${u.key}', ${u.val().banned})" style="padding:5px 15px; border-radius:5px; border:none; cursor:pointer; background:${u.val().banned ? 'green' : 'red'}; color:white;">
                            ${u.val().banned ? 'UNBAN' : 'BAN USER'}
                        </button>
                    </div>`;
                });
            });
        };
        window.toggleBan = (uid, s) => update(ref(db, 'users/' + uid), { banned: !s });

    </script>
</body>
</html>
