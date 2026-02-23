<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro | God Edition</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #0a0e17; --glass: rgba(22, 27, 34, 0.9); --border: rgba(255, 255, 255, 0.1); --text: #e6edf3; --primary: #58a6ff; --accent: #ff7b72; --grad: linear-gradient(135deg, #58a6ff, #bc8cff); }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); overflow: hidden; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; }
        header { padding: 20px; background: var(--glass); backdrop-filter: blur(10px); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; }
        header h1 { margin: 0; font-size: 20px; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }
        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 160px; }
        .page.active { display: block; }
        .hero { background: var(--grad); padding: 25px; border-radius: 20px; text-align: center; margin-bottom: 20px; }
        .card { background: var(--glass); border: 1px solid var(--border); padding: 15px; border-radius: 15px; margin-bottom: 10px; cursor: pointer; display: flex; justify-content: space-between; align-items: center; }
        .chat-box { display: flex; flex-direction: column; gap: 12px; }
        .msg { position: relative; max-width: 80%; padding: 10px 15px; border-radius: 18px; font-size: 15px; }
        .msg.me { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 4px; }
        .msg.other { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .input-area { position: absolute; bottom: 80px; width: 100%; padding: 10px; box-sizing: border-box; }
        .modern-bar { background: var(--glass); border: 1px solid var(--border); border-radius: 30px; display: flex; align-items: center; padding: 5px 15px; }
        .modern-bar input { flex: 1; background: transparent; border: none; color: white; padding: 10px; outline: none; }
        nav { position: absolute; bottom: 0; width: 100%; height: 70px; background: var(--glass); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; }
        nav i { font-size: 20px; color: #8b949e; cursor: pointer; padding: 10px; }
        nav i.active { color: var(--primary); background: rgba(88, 166, 255, 0.1); border-radius: 12px; }
        #admin-panel { display:none; background: var(--glass); border: 1px solid var(--primary); border-radius: 15px; padding: 10px; margin-bottom: 10px; flex-wrap: wrap; gap: 5px; }
        .admin-btn { font-size: 10px; padding: 5px 10px; background: var(--accent); color: white; border-radius: 5px; cursor: pointer; border: none; }
        #freeze-ui { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:black; z-index:20000; color:white; align-items:center; justify-content:center; text-align:center; flex-direction:column; }
    </style>
</head>
<body>

<div id="freeze-ui"><h1>SYSTEM FROZEN ❄️</h1><p>Locked by Admin</p></div>

<div class="app">
    <header>
        <h1 id="chatTitle">LIVE CONNECT</h1>
        <i class="fa-solid fa-house" onclick="openPage('home')" style="color:var(--primary); cursor:pointer"></i>
    </header>

    <div id="loginPage" class="page active">
        <div class="hero"><h2>Welcome 🚀</h2></div>
        <input type="text" id="usernameInput" placeholder="Username..." style="width:100%; padding:12px; background:var(--glass); border:1px solid var(--primary); color:white; border-radius:10px; text-align:center;">
        <button onclick="login()" style="width:100%; padding:12px; background:var(--primary); color:white; border:none; border-radius:10px; margin-top:10px; cursor:pointer">JOIN</button>
    </div>

    <div id="home" class="page">
        <div class="hero"><h2>Hi, <span id="dashUser"></span>!</h2></div>
        <div class="card" onclick="startGlobal()"><span>Global Chat</span> <i class="fa-solid fa-earth-americas"></i></div>
        <div id="userList"></div>
    </div>

    <div id="chat" class="page">
        <div id="admin-panel">
            <button class="admin-btn" onclick="adminAction('kick')">Kick All</button>
            <button class="admin-btn" onclick="adminAction('freeze')">Freeze All</button>
            <button class="admin-btn" onclick="adminAction('unfreeze')">Unfreeze All</button>
            <button class="admin-btn" onclick="adminAction('clear')">Clear Chat</button>
        </div>
        
        <div id="chatBox" class="chat-box"></div>
        <div class="input-area">
            <div id="typing-box" style="font-size:10px; color:var(--primary)"></div>
            <div class="modern-bar">
                <i id="admin-toggle" class="fa-solid fa-wand-magic-sparkles" style="display:none; color:gold; margin-right:10px; cursor:pointer" onclick="toggleAdminPanel()"></i>
                <input type="text" id="msgInput" placeholder="Message...">
                <i class="fa-solid fa-paper-plane" onclick="sendMsg()" style="color:var(--primary); cursor:pointer"></i>
            </div>
        </div>
    </div>

    <nav id="navbar" style="display:none">
        <i class="fa-solid fa-house active" onclick="openPage('home', this)"></i>
        <i class="fa-solid fa-comment" onclick="openPage('chat', this)"></i>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser = null, curChat = "global", isPrivate = false;
const adminID = "gtv140";

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("dashUser").innerText = currentUser;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display="flex";
    if(currentUser === adminID) document.getElementById("admin-toggle").style.display="block";

    onValue(ref(db, "powers/" + currentUser), snap => {
        if(snap.val() === "freeze") document.getElementById("freeze-ui").style.display="flex";
        if(snap.val() === "unfreeze") { document.getElementById("freeze-ui").style.display="none"; remove(ref(db, "powers/"+currentUser)); }
        if(snap.val() === "kick") location.reload();
    });

    set(ref(db, "users/" + currentUser), { name: currentUser, online: true });
    onDisconnect(ref(db, "users/" + currentUser)).remove();
    syncUsers();
};

window.startGlobal = () => { curChat = "global"; isPrivate = false; document.getElementById("chatTitle").innerText="GLOBAL CHAT"; openPage('chat'); loadChat(); };

window.startPrivate = (otherUser) => {
    curChat = [currentUser, otherUser].sort().join("_");
    isPrivate = true;
    document.getElementById("chatTitle").innerText = "CHAT: " + otherUser;
    openPage('chat');
    loadChat();
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInput"); if(!inp.value) return;
    const path = isPrivate ? "private/" + curChat : "messages/global";
    push(ref(db, path), { from: currentUser, text: inp.value });
    inp.value = "";
};

function loadChat() {
    const path = isPrivate ? "private/" + curChat : "messages/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val(), isMe = d.from === currentUser;
            const div = document.createElement("div"); div.className = `msg ${isMe ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.from}</b><div>${d.text}</div>
            ${(currentUser === adminID || isMe) ? `<div class="del-btn" style="display:block; position:absolute; right:0; top:-15px; color:red; cursor:pointer" onclick="remove(ref(db,'${path}/${m.key}'))">×</div>` : ''}`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

function syncUsers() {
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("userList"); list.innerHTML = "<h3>Direct Messages</h3>";
        snap.forEach(u => {
            if(u.key !== currentUser) {
                let d = document.createElement("div"); d.className = "card";
                d.innerHTML = `<span>${u.val().name}</span> <i class="fa-solid fa-message" style="color:var(--primary)"></i>`;
                d.onclick = () => startPrivate(u.key);
                list.appendChild(d);
            }
        });
    });
}

window.toggleAdminPanel = () => {
    const p = document.getElementById("admin-panel");
    p.style.display = p.style.display === "flex" ? "none" : "flex";
};

window.adminAction = (act) => {
    onValue(ref(db, "users"), snap => {
        snap.forEach(u => { if(u.key !== adminID) set(ref(db, "powers/" + u.key), act); });
    }, {onlyOnce: true});
    if(act === "clear") remove(ref(db, "messages/global"));
    alert("Admin Action Executed: " + act);
};

window.openPage = (id, icon) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
};
</script>
</body>
</html>
