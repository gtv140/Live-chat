<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro Max 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #030712;
            --card: rgba(17, 24, 39, 0.8);
            --primary: #6366f1;
            --accent: #f43f5e;
            --text: #f9fafb;
            --glass: rgba(255, 255, 255, 0.05);
            --success: #10b981;
        }

        body {
            margin: 0; font-family: 'Inter', system-ui, sans-serif;
            background: radial-gradient(circle at 0% 0%, #1e1b4b 0%, #030712 100%);
            color: var(--text); min-height: 100vh; overflow: hidden;
        }

        .app { max-width: 500px; margin: auto; display: flex; flex-direction: column; height: 100vh; background: var(--glass); backdrop-filter: blur(20px); border: 1px solid var(--glass); }

        header {
            padding: 20px; display: flex; justify-content: space-between; align-items: center;
            background: var(--card); border-bottom: 1px solid var(--glass);
        }

        header h1 { font-size: 22px; font-weight: 800; background: linear-gradient(to right, #818cf8, #f472b6); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; scroll-behavior: smooth; }
        .page.active { display: flex; flex-direction: column; }

        /* CHAT BUBBLES */
        .chat-box { flex: 1; display: flex; flex-direction: column; gap: 15px; padding: 10px; overflow-y: auto; }
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; animation: slideUp 0.3s ease; }
        .msg.sent { align-self: flex-end; background: linear-gradient(135deg, var(--primary), #4338ca); color: white; border-bottom-right-radius: 4px; box-shadow: 0 4px 15px rgba(99, 102, 241, 0.3); }
        .msg.received { align-self: flex-start; background: var(--card); border-bottom-left-radius: 4px; border: 1px solid var(--glass); }
        .msg b { display: block; font-size: 10px; margin-bottom: 4px; opacity: 0.7; }
        .msg img { max-width: 100%; border-radius: 10px; margin-top: 5px; border: 1px solid rgba(255,255,255,0.1); }
        .msg .time { font-size: 9px; margin-top: 5px; opacity: 0.6; text-align: right; }

        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* INPUT AREA */
        .input-area { padding: 15px; background: var(--card); border-top: 1px solid var(--glass); display: flex; flex-direction: column; gap: 10px; }
        .input-row { display: flex; gap: 10px; align-items: center; }
        .input-row input { flex: 1; padding: 12px 20px; border-radius: 25px; border: 1px solid var(--glass); background: rgba(0,0,0,0.4); color: white; outline: none; }
        .btn-icon { width: 40px; height: 40px; border-radius: 50%; border: none; background: var(--glass); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: 0.3s; }
        .btn-send { background: var(--primary); box-shadow: 0 0 15px var(--primary); }
        .btn-icon:hover { transform: scale(1.1); background: var(--primary); }

        /* NAV BAR */
        nav { display: flex; justify-content: space-around; background: var(--card); padding: 15px; border-top: 1px solid var(--glass); }
        nav button { background: none; border: none; font-size: 20px; color: var(--muted); cursor: pointer; transition: 0.3s; position: relative; }
        nav button.active { color: var(--primary); }
        nav button.active::after { content: ''; position: absolute; bottom: -5px; left: 50%; transform: translateX(-50%); width: 5px; height: 5px; background: var(--primary); border-radius: 50%; }

        /* STATUS */
        .online-tag { font-size: 10px; background: var(--success); color: white; padding: 2px 8px; border-radius: 10px; margin-left: 10px; }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect <span style="font-size: 10px; vertical-align: middle; opacity: 0.6;">PRO MAX</span></h1>
        <button onclick="logout()" class="btn-icon"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; margin-top: 50px;">
            <div style="font-size: 80px; text-shadow: 0 0 30px var(--primary);">🛰️</div>
            <h2 style="margin: 20px 0;">Secure Real-time Chat</h2>
            <input type="text" id="usernameInput" placeholder="Enter Username" style="width:85%; padding:15px; border-radius:15px; border:1px solid var(--glass); background:var(--card); color:white; margin-bottom:20px;">
            <button onclick="login()" style="width:85%; padding:15px; border-radius:15px; border:none; background:var(--primary); color:white; font-weight:bold; cursor:pointer; box-shadow: 0 10px 20px rgba(99,102,241,0.4);">Enter Galaxy</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatHeader" style="text-align:center; padding:5px; font-size:12px; color:var(--primary); font-weight:bold;"></div>
        <div class="chat-box" id="chatBox"></div>
        <div id="typing-status" style="font-size:10px; color:var(--primary); padding-left:10px; font-style:italic; height:15px;"></div>
        
        <div class="input-area">
            <div class="input-row">
                <label for="imgInput" class="btn-icon"><i class="fa-solid fa-image"></i></label>
                <input type="file" id="imgInput" hidden accept="image/*" onchange="sendImage(this)">
                <input id="msgInput" placeholder="Message..." oninput="handleTyping()"/>
                <button onclick="sendMsg()" class="btn-icon btn-send"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <div id="users" class="page">
        <h3>Explorers Online</h3>
        <div id="userList"></div>
        <h3 style="margin-top:20px;">Space Stations (Groups)</h3>
        <div id="groupList"></div>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="openPage('chat',this)" class="active"><i class="fa-solid fa-rocket"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-user-astronaut"></i></button>
    </nav>
</div>

<audio id="msgSound" src="https://assets.mixkit.co/active_storage/sfx/2354/2354-preview.mp3"></audio>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, serverTimestamp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.appspot.com",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("chat_user");
let curChat = "";
let isGroup = false;

const msgSound = document.getElementById("msgSound");

if(currentUser) { setupApp(); }

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    localStorage.setItem("chat_user", val);
    setupApp();
};

function setupApp() {
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("chat").classList.add("active");
    document.getElementById("navbar").style.display = "flex";
    
    const uRef = ref(db, "users/" + currentUser);
    set(uRef, { name: currentUser, online: true, lastSeen: serverTimestamp() });
    onDisconnect(uRef).set({ name: currentUser, online: false });
    
    listenUpdates();
}

window.logout = () => {
    set(ref(db, "users/" + currentUser), { online: false });
    localStorage.removeItem("chat_user");
    location.reload();
};

function listenUpdates() {
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("userList");
        list.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online && u.key !== currentUser) {
                const div = document.createElement("div");
                div.style = "background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px; cursor:pointer; display:flex; justify-content:space-between; align-items:center;";
                div.innerHTML = `<span>👨‍🚀 ${u.key}</span> <span class="online-tag">Live</span>`;
                div.onclick = () => startChat(u.key, false);
                list.appendChild(div);
            }
        });
    });

    onValue(ref(db, "groups"), snap => {
        const list = document.getElementById("groupList");
        list.innerHTML = "";
        snap.forEach(g => {
            const div = document.createElement("div");
            div.style = "background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px; cursor:pointer;";
            div.innerHTML = `🛰️ ${g.key}`;
            div.onclick = () => startChat(g.key, true);
            list.appendChild(div);
        });
    });
}

window.startChat = (id, groupMode) => {
    curChat = id; isGroup = groupMode;
    document.getElementById("chatHeader").innerText = "CONNECTED TO: " + id;
    openPage('chat', document.querySelector('nav button:first-child'));
    loadMessages();
};

function loadMessages() {
    const chatID = isGroup ? curChat : [currentUser, curChat].sort().join("_");
    const path = (isGroup ? "groupChats/" : "chats/") + chatID;
    
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const data = m.val();
            const isMe = data.from === currentUser;
            const div = document.createElement("div");
            div.className = `msg ${isMe ? 'sent' : 'received'}`;
            let content = data.image ? `<img src="${data.image}">` : data.text;
            div.innerHTML = `<b>${data.from}</b>${content}<div class="time">${data.time || ''}</div>`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
        if(snap.size > 0) msgSound.play();
    });
}

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !curChat) return;
    const chatID = isGroup ? curChat : [currentUser, curChat].sort().join("_");
    push(ref(db, (isGroup ? "groupChats/" : "chats/") + chatID), {
        from: currentUser,
        text: inp.value,
        time: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})
    });
    inp.value = "";
};

window.sendImage = (input) => {
    if(!input.files[0] || !curChat) return;
    const reader = new FileReader();
    reader.onload = (e) => {
        const chatID = isGroup ? curChat : [currentUser, curChat].sort().join("_");
        push(ref(db, (isGroup ? "groupChats/" : "chats/") + chatID), {
            from: currentUser,
            image: e.target.result,
            time: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})
        });
    };
    reader.readAsDataURL(input.files[0]);
};

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
};
</script>
</body>
</html>
