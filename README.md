<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Prime Solutions Enterprise</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root { --gold: #FFD700; --purple: #8A2BE2; --dark: #05050a; --glass: rgba(255, 255, 255, 0.05); }
        body { margin: 0; background: var(--dark); color: white; font-family: 'Rajdhani', sans-serif; height: 100vh; display: flex; flex-direction: column; overflow: hidden; }
        
        .app-wrapper { display: flex; flex: 1; overflow: hidden; }
        
        /* Sidebar */
        .sidebar { width: 280px; background: rgba(0,0,0,0.9); border-right: 1px solid var(--purple); display: flex; flex-direction: column; padding: 20px; backdrop-filter: blur(15px); }
        .sidebar h2 { font-family: 'Orbitron'; font-size: 0.75rem; color: var(--gold); margin: 25px 0 10px; text-transform: uppercase; letter-spacing: 1px; }
        .nav-item { padding: 12px; cursor: pointer; border-radius: 10px; margin-bottom: 5px; transition: 0.3s; color: #bbb; display: flex; align-items: center; gap: 10px; font-weight: 600; }
        .nav-item:hover, .nav-item.active { background: var(--purple); color: white; box-shadow: 0 0 15px rgba(138,43,226,0.3); }
        .status-dot { width: 8px; height: 8px; background: #00ff88; border-radius: 50%; box-shadow: 0 0 8px #00ff88; }

        /* Main Area */
        .main-container { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at center, #0f0f1e, #05050a); position: relative; }
        .header { padding: 15px 25px; border-bottom: 1px solid rgba(138,43,226,0.2); display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.4); }
        
        /* Pages */
        .page { display: none; flex: 1; flex-direction: column; overflow: hidden; }
        .active-page { display: flex; }

        /* Messages */
        #messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; scroll-behavior: smooth; }
        .msg-card { max-width: 75%; padding: 12px; border-radius: 15px; position: relative; background: var(--glass); border: 1px solid rgba(255,255,255,0.03); }
        .mine { align-self: flex-end; border-color: var(--purple); background: linear-gradient(135deg, rgba(138,43,226,0.1), rgba(0,0,0,0.4)); }
        .others { align-self: flex-start; }
        .sender-info { font-size: 0.7rem; color: var(--gold); font-weight: bold; margin-bottom: 4px; display: block; }
        .reply-ref { font-size: 0.75rem; background: rgba(255,255,255,0.05); padding: 8px; border-left: 3px solid var(--gold); margin-bottom: 8px; border-radius: 4px; }
        .shared-img { max-width: 100%; border-radius: 10px; margin-top: 5px; border: 1px solid var(--purple); }
        
        /* Message Actions */
        .msg-actions { font-size: 0.75rem; margin-top: 8px; display: flex; gap: 12px; color: #888; }
        .msg-actions span:hover { color: var(--gold); cursor: pointer; }
        .reactions { margin-top: 5px; font-size: 0.9rem; }

        /* Input System */
        .input-box { padding: 15px 25px; background: rgba(0,0,0,0.8); display: flex; gap: 12px; align-items: center; }
        #reply-bar { display: none; background: #1a1a2e; padding: 10px 25px; border-top: 2px solid var(--gold); font-size: 0.8rem; }
        input[type="text"] { flex: 1; background: rgba(255,255,255,0.05); border: 1px solid var(--purple); padding: 12px 20px; border-radius: 30px; color: white; outline: none; }
        .send-btn { background: var(--gold); border: none; padding: 10px 25px; border-radius: 30px; font-weight: bold; cursor: pointer; font-family: 'Orbitron'; font-size: 0.7rem; transition: 0.3s; }

        /* Info Screens (About/Security/Privacy) */
        .info-content { padding: 40px; overflow-y: auto; max-width: 800px; margin: 0 auto; line-height: 1.6; color: #ccc; }
        .info-content h1, .info-content h2 { color: var(--gold); font-family: 'Orbitron'; }
        
        /* Auth/Admin Overlays */
        #login-overlay { position: fixed; inset: 0; background: var(--dark); z-index: 5000; display: flex; align-items: center; justify-content: center; }
        #admin-overlay { position: absolute; inset: 0; background: var(--dark); z-index: 1000; display: none; padding: 30px; }

        @media (max-width: 768px) { .sidebar { width: 60px; padding: 10px; } .sidebar h2, .nav-item span { display: none; } }
    </style>
</head>
<body>

    <div id="login-overlay">
        <div style="background: var(--glass); padding: 50px; border-radius: 30px; border: 1px solid var(--purple); text-align: center; width: 320px;">
            <h1 style="font-family: 'Orbitron'; color: var(--gold); margin-bottom: 30px;">PRIME LOGIN</h1>
            <input type="text" id="userInput" placeholder="Username" style="display:block; width:90%; margin: 0 auto 10px auto;">
            <input type="password" id="passInput" placeholder="Password" style="display:block; width:90%; margin: 0 auto 20px auto;">
            <button onclick="handleAuth()" style="width: 100%; padding: 12px; background: var(--gold); border: none; border-radius: 25px; font-weight: bold; cursor: pointer;">ACCESS SECURE CORE</button>
        </div>
    </div>

    <div class="app-wrapper">
        <div class="sidebar">
            <h2 id="myUserDisplay">@USER</h2>
            <div class="nav-item active" onclick="showPage('chat-page')">🌐 Global Hub</div>
            <div class="nav-item" onclick="showPage('about-page')">🏢 About Prime</div>
            <div class="nav-item" onclick="showPage('privacy-page')">🛡️ Privacy & Security</div>
            
            <h2>Online Operators</h2>
            <div id="online-list"></div>

            <div id="admin-zone" style="margin-top: auto;"></div>
        </div>

        <div class="main-container">
            <div id="admin-overlay">
                <div style="display:flex; justify-content:space-between; margin-bottom:20px;">
                    <h2>ADMIN CONTROL</h2>
                    <button onclick="document.getElementById('admin-overlay').style.display='none'">EXIT</button>
                </div>
                <div id="admin-user-list"></div>
            </div>

            <div id="chat-page" class="page active-page">
                <div class="header">
                    <div id="active-target" style="font-family: 'Orbitron'; color: var(--gold);"># GLOBAL-HUB</div>
                    <div style="color: #00ff88; font-size: 0.8rem;">● ENCRYPTED</div>
                </div>
                <div id="messages"></div>
                <div id="reply-bar">
                    <span id="reply-info"></span> <span onclick="cancelReply()" style="float:right; cursor:pointer;">✖</span>
                </div>
                <div class="input-box">
                    <label for="imgInput" style="cursor:pointer; font-size:1.5rem;">🖼️</label>
                    <input type="file" id="imgInput" style="display:none" accept="image/*">
                    <input type="text" id="msgInput" placeholder="Type message, sweetie...">
                    <button class="send-btn" onclick="doSend()">SEND</button>
                </div>
            </div>

            <div id="about-page" class="page">
                <div class="info-content">
                    <h1>About PRIME SOLUTIONS</h1>
                    <p>Welcome to <b>Prime Solutions</b>, the next generation of SaaS communication. We specialize in building hyper-fast, secure, and futuristic web ecosystems.</p>
                    <h2>Our Vision</h2>
                    <p>To bridge the gap between human interaction and advanced technology. Live Connect is our flagship real-time communication platform designed for individuals and enterprises who value privacy and speed.</p>
                </div>
            </div>

            <div id="privacy-page" class="page">
                <div class="info-content">
                    <h1>Privacy & Security</h1>
                    <p><b>Data Encryption:</b> All messages transmitted through the Prime Solutions network are encrypted in real-time using Firebase Secure Protocols.</p>
                    <h2>Privacy Policy</h2>
                    <ul>
                        <li>We do not share your private chat data with third parties.</li>
                        <li>Anonymized data is only used to improve system performance.</li>
                        <li>Users have complete control over their account deletion.</li>
                    </ul>
                    <h2>Admin Security</h2>
                    <p>The system is monitored by an advanced Admin Core to prevent harassment and ensure a safe environment for all users.</p>
                </div>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, get, set, update, remove, onValue, onDisconnect, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

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
        const storage = getStorage(app);

        let currentUser = "";
        let chatTarget = "global";
        let isAdmin = false;
        let replyingTo = null;

        // AUTH & PRESENCE
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
                if(snap.exists() && snap.val().banned) return alert("System Ban Active 🚫");
                if(snap.exists() && snap.val().password !== pass) return alert("Wrong Password!");
                if(!snap.exists()) await set(uRef, { password: pass, banned: false });
                currentUser = user;
            }

            // Presence Logic
            const pRef = ref(db, 'presence/' + currentUser);
            set(pRef, { name: currentUser, online: true });
            onDisconnect(pRef).remove();

            document.getElementById('login-overlay').style.display = 'none';
            document.getElementById('myUserDisplay').innerText = "@" + currentUser.toUpperCase();
            initCore();
        };

        function initCore() {
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

        // PAGE & TARGET LOGIC
        window.showPage = (id) => {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
            document.getElementById(id).classList.add('active-page');
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
        };

        window.setTarget = (target) => {
            chatTarget = target;
            document.getElementById('active-target').innerText = target === 'global' ? '# GLOBAL-HUB' : `@ ${target.toUpperCase()}`;
            document.getElementById('messages').innerHTML = "";
            showPage('chat-page');
            listenMessages();
        };

        // MESSAGING
        function getPath() {
            return chatTarget === 'global' ? 'messages/global' : `messages/private/${[currentUser, chatTarget].sort().join('_')}`;
        }

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            push(ref(db, getPath()), { user: currentUser, text: inp.value, reply: replyingTo, time: serverTimestamp() });
            inp.value = ""; cancelReply();
        };

        document.getElementById('imgInput').onchange = async (e) => {
            const file = e.target.files[0]; if(!file) return;
            const sPath = sRef(storage, `gallery/${Date.now()}_${file.name}`);
            const upload = await uploadBytes(sPath, file);
            const url = await getDownloadURL(upload.ref);
            push(ref(db, getPath()), { user: currentUser, image: url, time: serverTimestamp() });
        };

        function listenMessages() {
            onChildAdded(ref(db, getPath()), (snap) => {
                const d = snap.val(); const id = snap.key;
                const isMe = d.user === currentUser;
                const mBox = document.getElementById('messages');
                
                const replyHtml = d.reply ? `<div class="reply-ref"><b>@${d.reply.user}</b>: ${d.reply.text}</div>` : "";
                const content = d.image ? `<img src="${d.image}" class="shared-img">` : d.text;
                const adminBtn = (isAdmin || isMe) ? `<span onclick="deleteMsg('${id}')">🗑️</span>` : "";
                const reactHtml = d.reacts ? Object.entries(d.reacts).map(([e, c]) => `<span>${e}${c}</span>`).join(" ") : "";

                const html = `
                    <div class="msg-card ${isMe ? 'mine' : 'others'}">
                        <span class="sender-info">${d.user.toUpperCase()}</span>
                        ${replyHtml}${content}
                        <div class="reactions">${reactHtml}</div>
                        <div class="msg-actions">
                            <span onclick="startReply('${d.user}', '${d.text || "Image"}')">↩️ Reply</span>
                            <span onclick="addReact('${id}', '❤️')">❤️</span>
                            <span onclick="addReact('${id}', '🔥')">🔥</span>
                            ${adminBtn}
                        </div>
                    </div>`;
                mBox.insertAdjacentHTML('beforeend', html);
                mBox.scrollTop = mBox.scrollHeight;
            });
        }

        // FEATURES
        window.startReply = (u, t) => { replyingTo = { user: u, text: t }; document.getElementById('reply-bar').style.display='block'; document.getElementById('reply-info').innerText=`Replying to ${u}`; };
        window.cancelReply = () => { replyingTo = null; document.getElementById('reply-bar').style.display='none'; };
        window.deleteMsg = (id) => { if(confirm("Delete?")) remove(ref(db, getPath() + '/' + id)); };
        window.addReact = (id, e) => {
            const rRef = ref(db, getPath() + `/${id}/reacts/${e}`);
            get(rRef).then(s => update(ref(db, getPath() + `/${id}/reacts`), { [e]: (s.val() || 0) + 1 }));
        };

        // ADMIN
        window.openAdmin = () => {
            document.getElementById('admin-overlay').style.display='block';
            onValue(ref(db, 'users'), (snap) => {
                const list = document.getElementById('admin-user-list'); list.innerHTML = "";
                snap.forEach(u => {
                    list.innerHTML += `<div style="padding:10px; border-bottom:1px solid #333; display:flex; justify-content:space-between;">
                        ${u.key} [${u.val().banned ? 'BANNED' : 'ACTIVE'}]
                        <button onclick="toggleBan('${u.key}', ${u.val().banned})">${u.val().banned ? 'UNBAN' : 'BAN'}</button>
                    </div>`;
                });
            });
        };
        window.toggleBan = (uid, s) => update(ref(db, 'users/' + uid), { banned: !s });

    </script>
</body>
</html>
