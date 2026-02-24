<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Empire</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --primary: #6366f1; --accent: #10b981; --bg: #020617; --card: #1e293b; }
        * { box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.3s; }
        body { margin: 0; background: var(--bg); color: white; height: 100dvh; overflow: hidden; display: flex; }

        /* --- Sidebar Navigation --- */
        .sidebar { width: 70px; background: #0b0f1a; display: flex; flex-direction: column; align-items: center; padding: 20px 0; gap: 25px; border-right: 1px solid rgba(255,255,255,0.05); z-index: 1000; }
        .side-icon { font-size: 1.5rem; color: #64748b; cursor: pointer; position: relative; }
        .side-icon:hover, .side-icon.active { color: var(--primary); }
        .side-icon.active::after { content: ''; position: absolute; left: -15px; top: 25%; height: 50%; width: 4px; background: var(--primary); border-radius: 0 4px 4px 0; }

        /* --- Main Content --- */
        .main-container { flex: 1; display: flex; flex-direction: column; position: relative; }
        
        header { padding: 20px; display: flex; justify-content: space-between; align-items: center; background: rgba(2,6,23,0.8); backdrop-filter: blur(10px); }
        .welcome-text { font-weight: 800; font-size: 1.2rem; background: linear-gradient(to right, #818cf8, #fb7185); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* --- Chat Sections --- */
        .content-area { flex: 1; display: flex; overflow: hidden; }
        .chat-section { flex: 1; display: flex; flex-direction: column; background: rgba(255,255,255,0.02); }
        
        .messages-list { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; padding-bottom: 120px; }
        .bubble { max-width: 75%; padding: 12px 16px; border-radius: 20px; font-size: 0.95rem; line-height: 1.4; position: relative; }
        .bubble.me { align-self: flex-end; background: var(--primary); border-bottom-right-radius: 4px; }
        .bubble.other { align-self: flex-start; background: var(--card); border-bottom-left-radius: 4px; }
        .bubble img { max-width: 100%; border-radius: 12px; margin-top: 5px; }

        /* --- Input Dock --- */
        .input-dock { position: absolute; bottom: 20px; left: 20px; right: 20px; background: #1e293b; padding: 10px 15px; border-radius: 50px; display: flex; align-items: center; gap: 12px; box-shadow: 0 10px 25px rgba(0,0,0,0.4); }
        .input-dock input { flex: 1; background: none; border: none; color: white; outline: none; font-size: 1rem; }
        .icon-btn { color: #94a3b8; cursor: pointer; font-size: 1.2rem; }
        .send-btn { background: var(--primary); width: 45px; height: 45px; border-radius: 50%; display: flex; align-items: center; justify-content: center; border: none; color: white; }

        /* --- Active Users Sidebar --- */
        .users-panel { width: 260px; background: rgba(255,255,255,0.03); border-left: 1px solid rgba(255,255,255,0.05); padding: 20px; overflow-y: auto; }
        @media (max-width: 800px) { .users-panel { display: none; } }

        /* --- Modals --- */
        .modal { position: fixed; inset: 0; background: rgba(0,0,0,0.8); display: none; align-items: center; justify-content: center; z-index: 2000; padding: 20px; }
        .modal-content { background: var(--card); padding: 30px; border-radius: 24px; max-width: 400px; width: 100%; text-align: center; }

        #authScreen { position: fixed; inset: 0; z-index: 5000; background: var(--bg); display: flex; align-items: center; justify-content: center; }
    </style>
</head>
<body>

<div id="authScreen">
    <div style="text-align: center; width: 80%; max-width: 350px;">
        <h1 style="font-size: 2.5rem; margin-bottom: 10px;">Connect_</h1>
        <input type="text" id="userName" placeholder="Your Name" style="width: 100%; padding: 15px; border-radius: 15px; border: none; background: #1e293b; color: white; margin-bottom: 15px; text-align: center;">
        <button onclick="startEmpire()" style="width: 100%; padding: 15px; border-radius: 15px; border: none; background: var(--primary); color: white; font-weight: 800; cursor: pointer;">ENTER SYSTEM</button>
    </div>
</div>

<nav class="sidebar">
    <div class="side-icon active" title="Chats"><i class="fa-solid fa-comment-dots"></i></div>
    <div class="side-icon" title="Groups"><i class="fa-solid fa-user-group"></i></div>
    <div class="side-icon" onclick="showModal('settingsModal')"><i class="fa-solid fa-gear"></i></div>
    <div class="side-icon" onclick="showModal('contactModal')"><i class="fa-solid fa-circle-info"></i></div>
</nav>

<div class="main-container">
    <header>
        <div class="welcome-text" id="welcomeMsg">Welcome, Pilot</div>
        <div style="display: flex; gap: 15px; align-items: center;">
            <span id="statusLed" style="width: 10px; height: 10px; background: var(--accent); border-radius: 50%; box-shadow: 0 0 10px var(--accent);"></span>
            <div id="roomTag" style="font-size: 0.7rem; opacity: 0.5; border: 1px solid rgba(255,255,255,0.1); padding: 4px 10px; border-radius: 20px;">GLOBAL_CORE</div>
        </div>
    </header>

    <div class="content-area">
        <div class="chat-section">
            <div class="messages-list" id="chatStream"></div>
            
            <div class="input-dock">
                <label for="imgUpload" class="icon-btn"><i class="fa-solid fa-image"></i></label>
                <input type="file" id="imgUpload" accept="image/*" style="display:none" onchange="uploadPhoto(this)">
                <input type="text" id="msgInput" placeholder="Write a message..." onkeydown="if(event.key==='Enter') sendMsg()">
                <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>

        <aside class="users-panel">
            <h4 style="margin-top:0; opacity:0.6">Active Nodes</h4>
            <div id="userList" style="display:flex; flex-direction:column; gap:15px;"></div>
        </aside>
    </div>
</div>

<div class="modal" id="settingsModal">
    <div class="modal-content">
        <h3>Settings</h3>
        <p style="opacity:0.6">Privacy & Account Security</p>
        <button onclick="closeModal('settingsModal')" style="background:var(--primary); border:none; color:white; padding:10px 20px; border-radius:10px; width:100%; margin-top:20px;">Save Changes</button>
    </div>
</div>

<div class="modal" id="contactModal">
    <div class="modal-content">
        <h3>About Empire</h3>
        <p>Built for Nazim by Gemini Titan v80.</p>
        <p style="font-size:0.8rem; opacity:0.5">Contact: support@empire-chat.io</p>
        <button onclick="closeModal('contactModal')" style="background:var(--primary); border:none; color:white; padding:10px 20px; border-radius:10px; width:100%; margin-top:20px;">Close</button>
    </div>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getAuth, signInAnonymously } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
    import { getDatabase, ref, set, push, onValue, serverTimestamp, query, limitToLast } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

    const firebaseConfig = {
        apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
        authDomain: "live-chat-b810c.firebaseapp.com",
        databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
        projectId: "live-chat-b810c",
        storageBucket: "live-chat-b810c.firebasestorage.app",
        messagingSenderId: "555058795334",
        appId: "1:555058795334:web:f668887409800c32970b47"
    };

    const app = initializeApp(firebaseConfig);
    const auth = getAuth(app);
    const db = getDatabase(app);

    let myUID, myName;

    window.startEmpire = async () => {
        myName = document.getElementById("userName").value.trim();
        if(!myName) return;

        const cred = await signInAnonymously(auth);
        myUID = cred.user.uid;
        document.getElementById("welcomeMsg").innerText = `Welcome, ${myName}`;
        document.getElementById("authScreen").style.display = "none";
        
        set(ref(db, 'nodes/' + myUID), { name: myName, online: true });
        loadStream();
    };

    window.sendMsg = (imgData = null) => {
        const inp = document.getElementById("msgInput");
        if(!inp.value.trim() && !imgData) return;

        push(ref(db, 'empire_chat'), {
            uid: myUID,
            sender: myName,
            text: inp.value.trim(),
            image: imgData,
            ts: serverTimestamp()
        });
        inp.value = "";
    };

    window.uploadPhoto = (el) => {
        const file = el.files[0];
        const reader = new FileReader();
        reader.onload = (e) => sendMsg(e.target.result);
        reader.readAsDataURL(file);
    };

    function loadStream() {
        onValue(query(ref(db, 'empire_chat'), limitToLast(50)), snap => {
            const stream = document.getElementById("chatStream");
            stream.innerHTML = "";
            snap.forEach(s => {
                const d = s.val();
                stream.innerHTML += `
                    <div class="bubble ${d.uid === myUID ? 'me' : 'other'}">
                        <small style="opacity:0.6; display:block; margin-bottom:4px;">${d.sender}</small>
                        ${d.text ? `<div>${d.text}</div>` : ''}
                        ${d.image ? `<img src="${d.image}">` : ''}
                    </div>
                `;
            });
            stream.scrollTop = stream.scrollHeight;
        });

        onValue(ref(db, 'nodes'), snap => {
            const list = document.getElementById("userList");
            list.innerHTML = "";
            snap.forEach(s => {
                const d = s.val();
                list.innerHTML += `<div style="display:flex; align-items:center; gap:10px;">
                    <img src="https://ui-avatars.com/api/?name=${d.name}&background=6366f1&color=fff" style="width:30px; border-radius:50%">
                    <span style="font-size:0.9rem">${d.name}</span>
                </div>`;
            });
        });
    }

    window.showModal = id => document.getElementById(id).style.display = 'flex';
    window.closeModal = id => document.getElementById(id).style.display = 'none';
</script>
</body>
</html>
