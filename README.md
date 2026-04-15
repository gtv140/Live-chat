<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Admin & Social Pro</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root { --gold: #FFD700; --purple: #8A2BE2; --dark: #05050a; --glass: rgba(255, 255, 255, 0.05); }
        body { margin: 0; background: var(--dark); color: white; font-family: 'Rajdhani', sans-serif; height: 100vh; display: flex; flex-direction: column; }
        
        /* Main Layout */
        .app-container { display: flex; flex: 1; overflow: hidden; }
        .sidebar { width: 250px; background: rgba(0,0,0,0.8); border-right: 1px solid var(--purple); padding: 20px; }
        .chat-area { flex: 1; display: flex; flex-direction: column; position: relative; }
        
        /* Admin Panel Overlay */
        #admin-panel { position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: var(--dark); z-index: 500; display: none; padding: 30px; }
        .user-row { display: flex; justify-content: space-between; padding: 10px; border-bottom: 1px solid var(--purple); }

        /* Messages */
        #messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 10px; }
        .msg-card { background: var(--glass); padding: 12px; border-radius: 15px; position: relative; max-width: 80%; }
        .reply-box { font-size: 0.8rem; background: rgba(255,255,255,0.1); padding: 5px; border-left: 3px solid var(--gold); margin-bottom: 5px; }
        
        /* Reactions & Actions */
        .msg-actions { font-size: 0.8rem; margin-top: 5px; display: flex; gap: 10px; color: var(--gold); cursor: pointer; }
        .reaction-bar { display: flex; gap: 5px; margin-top: 5px; font-size: 1rem; }

        /* Input Area */
        .reply-preview { display: none; background: #1a1a2e; padding: 10px; border-top: 2px solid var(--gold); }
        .input-bar { padding: 15px; background: #000; display: flex; gap: 10px; align-items: center; }
        input { flex: 1; background: var(--glass); border: 1px solid var(--purple); padding: 12px; border-radius: 20px; color: white; }
        button { background: var(--gold); border: none; padding: 10px 20px; border-radius: 20px; font-weight: bold; cursor: pointer; }

        #login-screen { position: fixed; inset: 0; background: var(--dark); z-index: 1000; display: flex; align-items: center; justify-content: center; }
    </style>
</head>
<body>

    <div id="login-screen">
        <div class="card" style="text-align: center; background: var(--glass); padding: 40px; border-radius: 20px; border: 1px solid var(--purple);">
            <h1 style="font-family: 'Orbitron'; color: var(--gold);">LIVE CONNECT</h1>
            <input type="text" id="userInput" placeholder="Username" style="display:block; width:100%; margin-bottom:10px;">
            <input type="password" id="passInput" placeholder="Password" style="display:block; width:100%; margin-bottom:20px;">
            <button onclick="handleAuth()">SECURE LOGIN</button>
        </div>
    </div>

    <div class="app-container">
        <div class="sidebar">
            <h2 style="font-family: 'Orbitron'; font-size: 0.8rem; color: var(--gold);">CHANNELS</h2>
            <div style="cursor:pointer; padding:10px;" onclick="location.reload()"># Global-Hub</div>
            <div id="admin-btn-area" style="margin-top:20px;"></div>
        </div>

        <div class="chat-area">
            <div id="admin-panel">
                <button onclick="document.getElementById('admin-panel').style.display='none'">CLOSE ADMIN</button>
                <h2 style="font-family: 'Orbitron'; color: var(--gold);">USER MANAGEMENT</h2>
                <div id="user-list-admin"></div>
            </div>

            <div id="messages"></div>

            <div id="reply-preview" class="reply-preview">
                <span id="reply-text"></span> 
                <button onclick="cancelReply()" style="padding:2px 5px; font-size:0.6rem;">X</button>
            </div>

            <div class="input-bar">
                <input type="text" id="msgInput" placeholder="Say something, sweetie...">
                <button onclick="doSend()">SEND</button>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, get, set, update, remove, onValue, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
        let isAdmin = false;
        let replyingTo = null;

        window.handleAuth = async () => {
            const user = document.getElementById('userInput').value.trim();
            const pass = document.getElementById('passInput').value.trim();
            
            if(user === "admin" && pass === "admin786") {
                isAdmin = true;
                currentUser = "Admin";
                document.getElementById('admin-btn-area').innerHTML = `<button onclick="openAdmin()" style="width:100%; background:red;">ADMIN PANEL</button>`;
            } else {
                const userRef = ref(db, 'users/' + user.toLowerCase());
                const snap = await get(userRef);
                if(snap.exists() && snap.val().banned) return alert("You are BANNED, sweetie! 🚫");
                if(snap.exists() && snap.val().password !== pass) return alert("Wrong Password!");
                if(!snap.exists()) await set(userRef, { password: pass, banned: false });
                currentUser = user;
            }
            document.getElementById('login-screen').style.display = 'none';
            listenMessages();
        };

        window.openAdmin = () => {
            document.getElementById('admin-panel').style.display = 'block';
            onValue(ref(db, 'users'), (snap) => {
                const list = document.getElementById('user-list-admin');
                list.innerHTML = "";
                snap.forEach(u => {
                    list.innerHTML += `<div class="user-row">${u.key} 
                        <button onclick="toggleBan('${u.key}', ${u.val().banned})">${u.val().banned ? 'UNBAN' : 'BAN'}</button>
                    </div>`;
                });
            });
        };

        window.toggleBan = (uid, status) => update(ref(db, 'users/' + uid), { banned: !status });

        window.doSend = () => {
            const text = document.getElementById('msgInput').value;
            if(!text) return;
            push(ref(db, 'messages/global'), {
                user: currentUser,
                text: text,
                replyTo: replyingTo,
                time: serverTimestamp()
            });
            cancelReply();
            document.getElementById('msgInput').value = "";
        };

        window.setReply = (user, text) => {
            replyingTo = { user, text };
            document.getElementById('reply-preview').style.display = 'block';
            document.getElementById('reply-text').innerText = `Replying to ${user}: ${text.substring(0,20)}...`;
        };

        window.cancelReply = () => {
            replyingTo = null;
            document.getElementById('reply-preview').style.display = 'none';
        };

        window.deleteMsg = (id) => { if(confirm("Delete message?")) remove(ref(db, 'messages/global/' + id)); };

        window.addReaction = (id, emoji) => {
            const rRef = ref(db, `messages/global/${id}/reactions/${emoji}`);
            get(rRef).then(s => update(ref(db, `messages/global/${id}/reactions`), { [emoji]: (s.val() || 0) + 1 }));
        };

        function listenMessages() {
            onChildAdded(ref(db, 'messages/global'), (snap) => {
                const d = snap.val();
                const id = snap.key;
                const msgBox = document.getElementById('messages');
                
                const replyHtml = d.replyTo ? `<div class="reply-box">@${d.replyTo.user}: ${d.replyTo.text}</div>` : "";
                const adminTools = (isAdmin || d.user === currentUser) ? `<span onclick="deleteMsg('${id}')">🗑️</span>` : "";
                const reactions = d.reactions ? Object.entries(d.reactions).map(([e, c]) => `<span>${e}${c}</span>`).join(" ") : "";

                const html = `
                    <div class="msg-card" id="${id}">
                        ${replyHtml}
                        <b style="color:var(--gold)">${d.user}</b>: ${d.text}
                        <div class="reaction-bar">${reactions}</div>
                        <div class="msg-actions">
                            <span onclick="setReply('${d.user}', '${d.text}')">↩️ Reply</span>
                            <span onclick="addReaction('${id}', '❤️')">❤️</span>
                            <span onclick="addReaction('${id}', '🔥')">🔥</span>
                            ${adminTools}
                        </div>
                    </div>`;
                msgBox.insertAdjacentHTML('beforeend', html);
                msgBox.scrollTop = msgBox.scrollHeight;
            });
        }
    </script>
</body>
</html>
