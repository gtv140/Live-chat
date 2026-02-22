<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Apex | Ultimate</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #00e5ff; --s: #0077ff; --bg: #05070a; --glass: rgba(255, 255, 255, 0.05); --border: rgba(255, 255, 255, 0.1); }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: #fff; font-family: 'Plus Jakarta Sans', sans-serif; overflow: hidden; }

        /* WELCOME/LOGIN SCREEN */
        #loginScreen { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 20px; }
        .login-card { background: var(--glass); backdrop-filter: blur(20px); padding: 40px 25px; border-radius: 30px; border: 1px solid var(--border); width: 100%; max-width: 360px; text-align: center; }
        .login-card h1 { font-size: 28px; color: var(--p); margin-bottom: 10px; }
        .login-input { width: 100%; padding: 15px; border-radius: 12px; border: 1px solid var(--border); background: rgba(0,0,0,0.3); color: #fff; margin-bottom: 20px; outline: none; }
        .login-btn { width: 100%; padding: 15px; border-radius: 12px; border: none; background: linear-gradient(135deg, var(--p), var(--s)); color: #000; font-weight: 800; cursor: pointer; }

        /* MAIN APP SHELL */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
        header { padding: 20px; background: rgba(5,7,10,0.9); backdrop-filter: blur(10px); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 1000; }
        header h2 { margin: 0; font-size: 20px; color: var(--p); }

        .viewport { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 120px; display: none; }
        .viewport.active { display: block; }
        
        /* USER CARDS */
        .user-card { background: var(--glass); padding: 18px; border-radius: 18px; border: 1px solid var(--border); margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; cursor: pointer; transition: 0.3s; }
        .user-card:hover { border-color: var(--p); background: rgba(0, 229, 255, 0.05); }
        .status-dot { width: 8px; height: 8px; border-radius: 50%; background: #00ff00; box-shadow: 0 0 10px #00ff00; }

        /* CHAT BUBBLES */
        .msg-box { display: flex; flex-direction: column; gap: 12px; }
        .msg { display: flex; flex-direction: column; max-width: 80%; }
        .msg.me { align-self: flex-end; align-items: flex-end; }
        .msg.them { align-self: flex-start; align-items: flex-start; }
        .bubble { padding: 12px 16px; border-radius: 20px; font-size: 15px; position: relative; }
        .me .bubble { background: linear-gradient(135deg, var(--p), var(--s)); color: #000; border-bottom-right-radius: 4px; font-weight: 600; }
        .them .bubble { background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .sender { font-size: 10px; opacity: 0.5; margin-bottom: 4px; padding: 0 8px; }

        /* FLOATING INPUT */
        .input-area { position: absolute; bottom: 85px; left: 50%; transform: translateX(-50%); width: 92%; background: rgba(255,255,255,0.08); backdrop-filter: blur(20px); padding: 8px 15px; border-radius: 30px; border: 1px solid var(--border); display: none; align-items: center; gap: 10px; z-index: 2000; }
        .input-area.active { display: flex; }
        .input-area input { flex: 1; background: transparent; border: none; color: #fff; padding: 10px; font-size: 16px; outline: none; }
        .send-btn { width: 40px; height: 40px; border-radius: 50%; background: var(--p); border: none; color: #000; cursor: pointer; }

        /* NAVIGATION */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: #050710; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-item { color: #64748b; text-align: center; cursor: pointer; transition: 0.3s; }
        .nav-item.active { color: var(--p); }
        .nav-item i { font-size: 22px; display: block; margin-bottom: 4px; }
        .nav-item span { font-size: 11px; font-weight: 600; }
    </style>
</head>
<body>

<div id="loginScreen">
    <div class="login-card">
        <h1>INFINITY</h1>
        <p style="opacity:0.6; margin-bottom:25px">Enter your alias to connect</p>
        <input type="text" id="nickInp" class="login-input" placeholder="Your Name...">
        <button class="login-btn" onclick="startApp()">CONNECT NODE</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2 id="headerTitle">INFINITY APEX</h2>
        <i class="fa-solid fa-power-off" onclick="logout()" style="color:#ff4b2b; cursor:pointer"></i>
    </header>

    <div id="v-home" class="viewport active">
        <h3 style="margin: 0 0 20px 5px">Online Nodes</h3>
        <div id="userList"></div>
    </div>

    <div id="v-chat" class="viewport">
        <div id="chatMessages" class="msg-box"></div>
    </div>

    <div class="input-area" id="inputDock">
        <i class="fa-solid fa-circle-plus" style="opacity:0.5"></i>
        <input type="text" id="msgInp" placeholder="Send encrypted data..." onfocus="autoScroll()">
        <button class="send-btn" onclick="sendMessage()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>

    <nav>
        <div class="nav-item active" id="homeTab" onclick="switchTab('home')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-item" id="chatTab" onclick="switchTab('chat')"><i class="fa-solid fa-comments"></i><span>Lobby</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let myName = localStorage.getItem("apex_v4_user");
let currentRoom = "Global_Lobby";

if(myName) {
    document.getElementById("loginScreen").style.display = "none";
    initApp();
}

window.startApp = () => {
    const val = document.getElementById("nickInp").value.trim();
    if(val.length < 2) return alert("Name too short");
    localStorage.setItem("apex_v4_user", val);
    location.reload();
};

function initApp() {
    // Online Status
    const myStatusRef = ref(db, 'nodes/' + myName);
    set(myStatusRef, { online: true, lastSeen: Date.now() });
    onDisconnect(myStatusRef).remove();

    // Listen for Users
    onValue(ref(db, 'nodes'), (snap) => {
        const list = document.getElementById("userList");
        list.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== myName) {
                const card = document.createElement("div");
                card.className = "user-card";
                card.innerHTML = `<span>${u.key}</span><div class="status-dot"></div>`;
                card.onclick = () => startPrivateChat(u.key);
                list.appendChild(card);
            }
        });
    });

    syncChat();
}

window.switchTab = (tab) => {
    document.querySelectorAll('.viewport').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('active'));
    
    if(tab === 'home') {
        document.getElementById('v-home').classList.add('active');
        document.getElementById('homeTab').classList.add('active');
        document.getElementById('inputDock').classList.remove('active');
        document.getElementById('headerTitle').innerText = "INFINITY APEX";
    } else {
        currentRoom = "Global_Lobby";
        document.getElementById('v-chat').classList.add('active');
        document.getElementById('chatTab').classList.add('active');
        document.getElementById('inputDock').classList.add('active');
        document.getElementById('headerTitle').innerText = "GLOBAL LOBBY";
        syncChat();
    }
};

function startPrivateChat(otherUser) {
    currentRoom = [myName, otherUser].sort().join("_");
    document.getElementById('headerTitle').innerText = otherUser;
    document.getElementById('v-home').classList.remove('active');
    document.getElementById('v-chat').classList.add('active');
    document.getElementById('inputDock').classList.add('active');
    syncChat();
}

function syncChat() {
    onValue(ref(db, 'v4_msgs/' + currentRoom), (snap) => {
        const box = document.getElementById("chatMessages");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div");
            div.className = `msg ${d.from === myName ? 'me' : 'them'}`;
            div.innerHTML = `
                <span class="sender">${d.from}</span>
                <div class="bubble">${d.text}</div>
            `;
            box.appendChild(div);
        });
        autoScroll();
    });
}

window.autoScroll = () => {
    const v = document.getElementById('v-chat');
    setTimeout(() => { v.scrollTop = v.scrollHeight; }, 100);
};

window.sendMessage = () => {
    const inp = document.getElementById("msgInp");
    if(!inp.value.trim()) return;
    push(ref(db, 'v4_msgs/' + currentRoom), { from: myName, text: inp.value, ts: Date.now() });
    inp.value = "";
    autoScroll();
};

window.logout = () => { localStorage.clear(); location.reload(); };

document.getElementById("msgInp").addEventListener("keypress", (e) => {
    if(e.key === "Enter") sendMessage();
});
</script>
</body>
</html>
