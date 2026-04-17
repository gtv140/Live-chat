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
        .sidebar { width: 280px; background: rgba(0,0,0,0.9); border-right: 1px solid var(--purple); display: flex; flex-direction: column; padding: 20px; backdrop-filter: blur(15px); }
        .sidebar h2 { font-family: 'Orbitron'; font-size: 0.75rem; color: var(--gold); margin: 25px 0 10px; text-transform: uppercase; border-bottom: 1px solid rgba(255,255,255,0.1); }
        .nav-item { padding: 12px; cursor: pointer; border-radius: 10px; margin-bottom: 5px; transition: 0.3s; color: #bbb; display: flex; align-items: center; gap: 10px; }
        .nav-item:hover, .nav-item.active { background: var(--purple); color: white; }
        .status-dot { width: 8px; height: 8px; background: #00ff88; border-radius: 50%; box-shadow: 0 0 8px #00ff88; }
        .main-container { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at center, #0f0f1e, #05050a); position: relative; }
        .header { padding: 15px 25px; border-bottom: 1px solid rgba(138,43,226,0.2); display: flex; justify-content: space-between; align-items: center; }
        .page { display: none; flex: 1; flex-direction: column; overflow: hidden; }
        .active-page { display: flex; }
        #messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; }
        .msg-card { max-width: 75%; padding: 12px; border-radius: 15px; background: var(--glass); border: 1px solid rgba(255,255,255,0.03); }
        .mine { align-self: flex-end; border-color: var(--purple); background: linear-gradient(135deg, rgba(138,43,226,0.1), rgba(0,0,0,0.4)); }
        .others { align-self: flex-start; }
        .bot-msg { align-self: center; border-color: var(--gold); background: rgba(255, 215, 0, 0.1); font-style: italic; font-size: 0.85rem; text-align: center; }
        .shared-img { max-width: 100%; border-radius: 10px; margin-top: 5px; border: 1px solid var(--purple); cursor: pointer; }
        .input-box { padding: 15px 25px; background: rgba(0,0,0,0.8); display: flex; gap: 12px; align-items: center; }
        #login-overlay { position: fixed; inset: 0; background: var(--dark); z-index: 5000; display: flex; align-items: center; justify-content: center; }
    </style>
</head>
<body>

    <div id="login-overlay">
        <div style="background: var(--glass); padding: 50px; border-radius: 30px; border: 1px solid var(--purple); text-align: center; width: 320px;">
            <h1 style="font-family: 'Orbitron'; color: var(--gold); margin-bottom: 30px;">PRIME LOGIN</h1>
            <input type="text" id="userInput" placeholder="Username" style="display:block; width:90%; margin: 0 auto 10px auto; padding: 10px;">
            <input type="password" id="passInput" placeholder="Password" style="display:block; width:90%; margin: 0 auto 20px auto; padding: 10px;">
            <button onclick="handleAuth()" style="width: 100%; padding: 12px; background: var(--gold); border: none; border-radius: 25px; font-weight: bold; cursor: pointer;">ACCESS SECURE CORE</button>
        </div>
    </div>

    <div class="app-wrapper">
        <div class="sidebar">
            <h2 id="myUserDisplay">@USER</h2>
            <div class="nav-item active" onclick="showPage('chat-page')">🌐 Global Hub</div>
            <div class="nav-item" onclick="showPage('about-page')">🏢 About Prime</div>
            <h2>Online Operators</h2>
            <div id="online-list"></div>
            <div id="admin-zone" style="margin-top: auto;"></div>
        </div>

        <div class="main-container">
            <div id="chat-page" class="page active-page">
                <div class="header">
                    <div id="active-target" style="font-family: 'Orbitron'; color: var(--gold);"># GLOBAL-HUB</div>
                    <div id="upload-status" style="font-size: 0.7rem;"></div>
                </div>
                <div id="messages"></div>
                <div class="input-box">
                    <label for="imgInput" style="cursor:pointer; font-size:1.5rem;" title="Upload Image">🖼️</label>
                    <input type="file" id="imgInput" style="display:none" accept="image/*">
                    <input type="text" id="msgInput" placeholder="Write something, sweetie...">
                    <button onclick="doSend()" style="background:var(--gold); border:none; padding:10px 20px; border-radius:20px; font-weight:bold; cursor:pointer;">SEND</button>
                </div>
            </div>
            </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, get, set, onValue, onDisconnect, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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

        window.handleAuth = async () => {
            const user = document.getElementById('userInput').value.trim().toLowerCase();
            const pass = document.getElementById('passInput').value.trim();
            if(!user || !pass) return;

            // Simple login logic (Update with your database check)
            currentUser = user;
            document.getElementById('login-overlay').style.display = 'none';
            document.getElementById('myUserDisplay').innerText = "@" + currentUser.toUpperCase();
            
            // Welcome Bot Message
            push(ref(db, 'messages/global'), {
                user: "Prime Bot",
                text: `Welcome to the system, ${currentUser}! 🚀 Prime Solutions is happy to have you.`,
                isBot: true,
                time: serverTimestamp()
            });

            const pRef = ref(db, 'presence/' + currentUser);
            set(pRef, { name: currentUser, online: true });
            onDisconnect(pRef).remove();

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

        window.doSend = () => {
            const inp = document.getElementById('msgInput');
            if(!inp.value.trim()) return;
            push(ref(db, getPath()), { user: currentUser, text: inp.value, time: serverTimestamp() });
            inp.value = "";
        };

        // FIXED PHOTO UPLOAD SYSTEM
        document.getElementById('imgInput').onchange = async (e) => {
            const file = e.target.files[0];
            if(!file) return;

            document.getElementById('upload-status').innerText = "Uploading photo...";
            try {
                const fileName = `${Date.now()}_${file.name}`;
                const storageRef = sRef(storage, `chat_images/${fileName}`);
                
                // Upload task
                const snapshot = await uploadBytes(storageRef, file);
                const url = await getDownloadURL(snapshot.ref);

                push(ref(db, getPath()), { 
                    user: currentUser, 
                    image: url, 
                    time: serverTimestamp() 
                });
                document.getElementById('upload-status').innerText = "✅ Done";
                setTimeout(() => document.getElementById('upload-status').innerText = "", 2000);
            } catch (err) {
                console.error(err);
                alert("Photo send nahi ho saki. Sweetie, check karo Firebase Storage ke 'Rules' mein 'allow read, write' true hai ya nahi!");
                document.getElementById('upload-status').innerText = "❌ Failed";
            }
        };

        function getPath() {
            return chatTarget === 'global' ? 'messages/global' : `messages/private/${[currentUser, chatTarget].sort().join('_')}`;
        }

        function listenMessages() {
            onChildAdded(ref(db, getPath()), (snap) => {
                const d = snap.val();
                const isMe = d.user === currentUser;
                const mBox = document.getElementById('messages');
                
                const content = d.image ? `<img src="${d.image}" class="shared-img" onclick="window.open('${d.image}', '_blank')">` : d.text;
                const cardClass = d.isBot ? 'bot-msg' : (isMe ? 'mine' : 'others');

                const html = `
                    <div class="msg-card ${cardClass}">
                        ${!d.isBot ? `<span style="font-size:0.7rem; color:var(--gold); font-weight:bold;">${d.user.toUpperCase()}</span><br>` : ''}
                        ${content}
                    </div>`;
                mBox.insertAdjacentHTML('beforeend', html);
                mBox.scrollTop = mBox.scrollHeight;
            });
        }
    </script>
</body>
</html>
