<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Prime Solutions Elite</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg: #f0f2f5; --sidebar: #ffffff; --text: #1c1e21; --card: #ffffff;
            --primary: #6a11cb; --accent: #2575fc; --gold: #b8860b;
            --msg-mine: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%);
            --msg-other: #ffffff; --input-bg: #ffffff; --border: rgba(0,0,0,0.1);
        }

        [data-theme="dark"] {
            --bg: #080810; --sidebar: #0f0f1e; --text: #e4e6eb; --card: #1c1c2d;
            --primary: #8A2BE2; --accent: #2575fc; --gold: #FFD700;
            --msg-mine: linear-gradient(135deg, #8A2BE2 0%, #4b0082 100%);
            --msg-other: #242526; --input-bg: #18191a; --border: rgba(255,255,255,0.1);
        }

        * { box-sizing: border-box; transition: 0.2s ease; }
        body { margin: 0; background: var(--bg); color: var(--text); font-family: 'Rajdhani', sans-serif; height: 100vh; overflow: hidden; }
        
        .app-wrapper { display: flex; height: 100vh; position: relative; }

        /* Sidebar */
        .sidebar { 
            width: 280px; background: var(--sidebar); border-right: 1px solid var(--border); 
            display: flex; flex-direction: column; padding: 20px; z-index: 1000;
        }

        .main-container { flex: 1; display: flex; flex-direction: column; height: 100vh; position: relative; }

        .header { 
            padding: 12px 20px; border-bottom: 1px solid var(--border); 
            display: flex; align-items: center; background: var(--sidebar); gap: 15px;
        }

        /* Features Styling */
        #messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; height: calc(100vh - 130px); }
        .msg-card { max-width: 80%; padding: 12px 16px; border-radius: 18px; position: relative; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .mine { align-self: flex-end; background: var(--msg-mine); color: white; border-bottom-right-radius: 4px; }
        .others { align-self: flex-start; background: var(--msg-other); color: var(--text); border-bottom-left-radius: 4px; border: 1px solid var(--border); }
        
        .sender-info { font-size: 0.7rem; color: var(--gold); font-weight: bold; margin-bottom: 4px; display: block; font-family: 'Orbitron'; }
        .reply-tag { font-size: 0.75rem; background: rgba(0,0,0,0.1); padding: 5px; border-left: 3px solid var(--gold); margin-bottom: 5px; border-radius: 4px; }
        .react-btn { cursor: pointer; opacity: 0.6; font-size: 0.8rem; margin-right: 8px; }
        .react-btn:hover { opacity: 1; }

        /* Input Area */
        #reply-preview { display: none; background: var(--sidebar); padding: 10px 20px; border-top: 2px solid var(--gold); font-size: 0.8rem; }
        .input-box { padding: 15px; background: var(--sidebar); display: flex; gap: 10px; align-items: center; border-top: 1px solid var(--border); }
        #msgInput { flex: 1; background: var(--bg); border: 1px solid var(--border); padding: 12px 20px; border-radius: 25px; color: var(--text); outline: none; }
        .action-icon { font-size: 1.4rem; cursor: pointer; color: var(--gold); }

        /* Responsive */
        @media (max-width: 768px) {
            .sidebar { position: absolute; left: -280px; height: 100%; transition: 0.3s; }
            .sidebar.open { left: 0; box-shadow: 10px 0 20px rgba(0,0,0,0.2); }
            .msg-card { max-width: 90%; }
        }

        .page { display: none; flex: 1; padding: 30px; overflow-y: auto; }
        .active-page { display: flex; flex-direction: column; }
        .btn-prime { background: var(--gold); color: white; border: none; padding: 10px 20px; border-radius: 20px; cursor: pointer; font-weight: bold; }
    </style>
</head>
<body data-theme="light">

    <div id="login-overlay" style="position:fixed; inset:0; background:var(--bg); z-index:9999; display:flex; align-items:center; justify-content:center;">
        <div style="background:var(--sidebar); padding:40px; border-radius:20px; border:1px solid var(--gold); text-align:center; width:90%; max-width:350px;">
            <h1 style="font-family:'Orbitron'; color:var(--primary);">PRIME CONNECT</h1>
            <input type="text" id="userInput" placeholder="Username" style="width:100%; padding:12px; margin: 10px 0; border-radius:10px; border:1px solid var(--border);">
            <input type="password" id="passInput" placeholder="Password" style="width:100%; padding:12px; margin-bottom:20px; border-radius:10px; border:1px solid var(--border);">
            <button onclick="handleAuth()" class="btn-prime" style="width:100%">LOGIN</button>
        </div>
    </div>

    <div class="app-wrapper">
        <div class="sidebar" id="sidebar">
            <h2 style="font-family:'Orbitron'; color:var(--primary); font-size:1rem;">PRIME SOLUTIONS</h2>
            <div onclick="showPage('chat-page')" style="cursor:pointer; padding:10px; font-weight:bold;">🌐 GLOBAL HUB</div>
            <div onclick="showPage('about-page')" style="cursor:pointer; padding:10px;">🏢 ABOUT COMPANY</div>
            <div onclick="showPage('privacy-page')" style="cursor:pointer; padding:10px;">🛡️ PRIVACY POLICY</div>
            <div onclick="location.reload()" style="cursor:pointer; padding:10px; color:red; margin-top:auto;">🚪 LOGOUT</div>
            <hr style="width:100%; border:0; border-top:1px solid var(--border);">
            <div id="online-list"></div>
        </div>

        <div class="main-container">
            <div class="header">
                <button onclick="document.getElementById('sidebar').classList.toggle('open')" style="background:none; border:1px solid var(--primary); border-radius:5px; padding:5px 10px;">☰</button>
                <div style="font-weight:bold; color:var(--gold);"># LIVE CONNECT</div>
                <button onclick="toggleTheme()" style="margin-left:auto; cursor:pointer; background:none; border:1px solid var(--border); padding:5px 10px; border-radius:15px;">🌓 THEME</button>
            </div>

            <div id="chat-page" class="page active-page" style="padding:0;">
                <div id="messages"></div>
                <div id="reply-preview">
                    <span id="reply-info">Replying...</span>
                    <button onclick="cancelReply()" style="float:right; border:none; background:none; cursor:pointer;">✖</button>
                </div>
                <div class="input-box">
                    <label for="imgInput" class="action-icon">🖼️</label>
                    <input type="file" id="imgInput" style="display:none" accept="image/*">
                    <span id="voice-btn" class="action-icon" onclick="toggleRecord()">🎤</span>
                    <input type="text" id="msgInput" placeholder="Type a message, sweetie...">
                    <button onclick="doSend()" class="send-btn" style="color:white; font-weight:bold;">➔</button>
                </div>
            </div>

            <div id="about-page" class="page">
                <h1 style="color:var(--gold)">PRIME SOLUTIONS</h1>
                <p>Welcome to <b>Live Connect</b>, the official communication tool for Prime Solutions. We specialize in real-time data sync and professional web architecture.</p>
                <p><b>Version:</b> 4.5 Ultimate Edition</p>
            </div>

            <div id="privacy-page" class="page">
                <h1 style="color:var(--gold)">Privacy Policy</h1>
                <p>1. Data is encrypted using Firebase Secure Core.<br>2. Private messages are never logged on local servers.<br>3. Media content is processed via Base64 for instant delivery.</p>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp, set, onDisconnect } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
        let replyingTo = null;
        let mediaRecorder; let chunks = [];

        window.toggleTheme = () => {
            const current = document.body.getAttribute('data-theme');
            document.body.setAttribute('data-theme', current === 'light' ? 'dark' : 'light');
        };

        window.handleAuth = () => {
            const u = document.getElementById('userInput').value.trim();
            if(!u) return alert("Enter Username!");
            currentUser = u;
            document.getElementById('login-overlay').style.display='none';
            set(ref(db, 'presence/' + u), { name: u, online: true });
            onDisconnect(ref(db, 'presence/' + u)).remove();
            initApp();
        };

        function initApp() {
            onChildAdded(ref(db, 'messages/global'), (snap) => {
                const d = snap.val(); const isMe = d.user === currentUser;
                const mBox = document.getElementById('messages');
                let content = d.text || "";
                if(d.image) content = `<img src="${d.image}" style="max-width:100%; border-radius:10px;">`;
                if(d.voice) content = `<audio controls src="${d.voice}" style="width:200px;"></audio>`;
                
                const replyHtml = d.reply ? `<div class="reply-tag"><b>@${d.reply.user}:</b> ${d.reply.text}</div>` : "";

                const html = `<div class="msg-card ${isMe ? 'mine' : 'others'}">
                    ${!isMe ? `<span class="sender-info">${d.user.toUpperCase()}</span>` : ""}
                    ${replyHtml} ${content}
                    <div style="margin-top:5px; font-size:0.7rem;">
                        <span class="react-btn" onclick="startReply('${d.user}', '${d.text || "Media"}')">↩️</span>
                        <span class="react-btn">❤️</span><span class="react-btn">🔥</span>
                    </div>
                </div>`;
                mBox.insertAdjacentHTML('beforeend', html);
                mBox.scrollTop = mBox.scrollHeight;
            });
        }

        window.showPage = (id) => {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
            document.getElementById(id).classList.add('active-page');
            if(window.innerWidth < 768) document.getElementById('sidebar').classList.remove('open');
        };

        window.doSend = () => {
            const inp = document.getElementById('msgInput'); if(!inp.value.trim()) return;
            const data = { user: currentUser, text: inp.value, time: serverTimestamp() };
            if(replyingTo) data.reply = replyingTo;
            push(ref(db, 'messages/global'), data);
            inp.value = ""; cancelReply();
        };

        document.getElementById('imgInput').onchange = (e) => {
            const file = e.target.files[0]; if(!file) return;
            const r = new FileReader(); r.onload = (ev) => push(ref(db, 'messages/global'), { user: currentUser, image: ev.target.result, time: serverTimestamp() });
            r.readAsDataURL(file);
        };

        window.toggleRecord = async () => {
            const b = document.getElementById('voice-btn');
            if(!mediaRecorder || mediaRecorder.state === "inactive") {
                const s = await navigator.mediaDevices.getUserMedia({audio:true});
                mediaRecorder = new MediaRecorder(s);
                mediaRecorder.ondataavailable = (e) => chunks.push(e.data);
                mediaRecorder.onstop = () => {
                    const blob = new Blob(chunks, {type:'audio/mp3'});
                    const r = new FileReader(); r.onload=(e)=>push(ref(db, 'messages/global'), {user:currentUser, voice:e.target.result});
                    r.readAsDataURL(blob); chunks=[]; b.innerText="🎤";
                };
                mediaRecorder.start(); b.innerText="🛑";
            } else { mediaRecorder.stop(); }
        };

        window.startReply = (u, t) => { replyingTo={user:u, text:t}; document.getElementById('reply-preview').style.display="block"; document.getElementById('reply-info').innerText="Replying to @"+u; };
        window.cancelReply = () => { replyingTo=null; document.getElementById('reply-preview').style.display="none"; };
    </script>
</body>
</html>
