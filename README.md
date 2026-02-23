<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro | Ultimate God Edition</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #0a0e17; --glass: rgba(22, 27, 34, 0.9); --border: rgba(255, 255, 255, 0.1); --text: #e6edf3; --primary: #58a6ff; --accent: #ff7b72; --grad: linear-gradient(135deg, #58a6ff, #bc8cff); }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); overflow: hidden; transition: 0.3s; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; }
        
        header { padding: 20px; background: var(--glass); backdrop-filter: blur(10px); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 100; }
        header h1 { margin: 0; font-size: 22px; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 160px; }
        .page.active { display: block; animation: slideUp 0.3s; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        .hero { background: var(--grad); padding: 25px; border-radius: 20px; text-align: center; margin-bottom: 20px; box-shadow: 0 10px 20px rgba(0,0,0,0.3); }
        .card { background: var(--glass); border: 1px solid var(--border); padding: 15px; border-radius: 15px; margin-bottom: 10px; cursor: pointer; display: flex; justify-content: space-between; align-items: center; transition: 0.2s; }
        .card:hover { border-color: var(--primary); transform: scale(1.02); }

        .chat-box { display: flex; flex-direction: column; gap: 12px; }
        .msg { position: relative; max-width: 80%; padding: 10px 15px; border-radius: 18px; font-size: 15px; line-height: 1.4; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
        .msg.me { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 4px; }
        .msg.other { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .msg img { width: 100%; border-radius: 10px; margin-top: 8px; cursor: pointer; }
        .msg b { font-size: 11px; display: block; margin-bottom: 2px; opacity: 0.8; }

        .del-btn { position: absolute; top: -8px; right: -8px; background: var(--accent); color: white; width: 22px; height: 22px; border-radius: 50%; font-size: 10px; display: none; align-items: center; justify-content: center; cursor: pointer; border: 2px solid var(--bg); }
        .msg:hover .del-btn { display: flex; }
        .react-badge { position: absolute; bottom: -10px; right: 10px; background: #161b22; border-radius: 10px; padding: 2px 5px; font-size: 12px; border: 1px solid var(--border); cursor: pointer; }

        .input-area { position: absolute; bottom: 80px; width: 100%; padding: 15px; box-sizing: border-box; z-index: 50; }
        .modern-bar { background: var(--glass); border: 1px solid var(--border); border-radius: 35px; display: flex; align-items: center; padding: 5px 15px; backdrop-filter: blur(10px); }
        .modern-bar input { flex: 1; background: transparent; border: none; color: white; padding: 10px; outline: none; }
        .icon-btn { color: var(--primary); font-size: 20px; cursor: pointer; margin: 0 8px; }

        nav { position: absolute; bottom: 0; width: 100%; height: 75px; background: var(--glass); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; backdrop-filter: blur(15px); }
        nav i { font-size: 22px; color: #8b949e; cursor: pointer; padding: 10px; transition: 0.3s; }
        nav i.active { color: var(--primary); background: rgba(88, 166, 255, 0.1); border-radius: 15px; }

        #admin-panel { display:none; background: var(--glass); border: 1px solid var(--primary); border-radius: 15px; padding: 10px; margin-bottom: 10px; flex-wrap: wrap; gap: 8px; box-shadow: 0 -5px 15px rgba(0,0,0,0.3); }
        .admin-btn { font-size: 11px; padding: 6px 12px; background: var(--accent); color: white; border-radius: 8px; cursor: pointer; border: none; font-weight: bold; }
        #freeze-ui { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.95); z-index:20000; color:white; align-items:center; justify-content:center; text-align:center; flex-direction:column; font-family: 'Courier New', Courier, monospace; }
    </style>
</head>
<body>

<div id="freeze-ui">
    <i class="fa-solid fa-snowflake fa-spin" style="font-size: 60px; color: var(--primary); margin-bottom: 20px;"></i>
    <h1>SYSTEM TERMINATED</h1>
    <p>Admin has locked your current session.</p>
</div>

<div class="app">
    <header>
        <h1 id="chatTitle">Live Connect</h1>
        <i class="fa-solid fa-power-off" onclick="location.reload()" style="color:var(--accent); cursor:pointer"></i>
    </header>

    <div id="loginPage" class="page active">
        <div class="hero"><h2>Gumnaam Chat 🚀</h2><p>Ultimate Power Edition</p></div>
        <div class="card" style="flex-direction:column; gap:15px">
            <input type="text" id="usernameInput" placeholder="Secret Identity..." style="width:100%; padding:15px; background:transparent; border:none; border-bottom:2px solid var(--primary); color:white; outline:none; text-align:center; font-size:18px">
            <button onclick="login()" style="width:100%; padding:15px; background:var(--primary); color:white; border:none; border-radius:12px; font-weight:bold; cursor:pointer">ACTIVATE</button>
        </div>
    </div>

    <div id="home" class="page">
        <div class="hero"><h2>Welcome, <span id="dashUser"></span>! 👋</h2></div>
        <div class="card" onclick="startGlobal()"><span>Global Community</span> <i class="fa-solid fa-earth-americas"></i></div>
        <div id="userList"></div>
    </div>

    <div id="chat" class="page">
        <div id="admin-panel">
            <button class="admin-btn" onclick="adminAction('kick')"><i class="fa-solid fa-door-open"></i> Kick All</button>
            <button class="admin-btn" onclick="adminAction('freeze')"><i class="fa-solid fa-snowflake"></i> Freeze All</button>
            <button class="admin-btn" onclick="adminAction('unfreeze')" style="background:#2ea043">Unfreeze All</button>
            <button class="admin-btn" onclick="adminAction('clear')">Wipe Chat</button>
        </div>
        
        <div id="chatBox" class="chat-box"></div>
        <div class="input-area">
            <div id="typing-box" style="font-size:11px; color:var(--primary); margin-bottom:5px; height:15px"></div>
            <div class="modern-bar">
                <i id="admin-toggle" class="fa-solid fa-wand-magic-sparkles" style="display:none; color:gold; margin-right:10px; cursor:pointer" onclick="toggleAdminPanel()"></i>
                <label for="imgIn"><i class="fa-solid fa-camera icon-btn"></i></label>
                <input type="file" id="imgIn" accept="image/*" style="display:none" onchange="uploadPhoto(this)">
                <input type="text" id="msgInput" placeholder="Say something..." oninput="handleTyping()">
                <i class="fa-solid fa-paper-plane icon-btn" onclick="sendMsg()"></i>
            </div>
        </div>
    </div>

    <nav id="navbar" style="display:none">
        <i class="fa-solid fa-house active" onclick="openPage('home', this)"></i>
        <i class="fa-solid fa-comment" onclick="openPage('chat', this)"></i>
        <i class="fa-solid fa-circle-info" onclick="openPage('about', this)"></i>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser = null, curChat = "global", isPrivate = false, typeTimer;
const adminName = "Nazim"; // 👑 AAPKA NAYA ADMIN USERNAME

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("dashUser").innerText = currentUser;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display="flex";
    
    if(currentUser === adminName) document.getElementById("admin-toggle").style.display="block";

    // Ghost Admin Action Listener
    onValue(ref(db, "powers/" + currentUser), snap => {
        const p = snap.val();
        if(p === "kick") { remove(ref(db, "powers/"+currentUser)); location.reload(); }
        if(p === "freeze") document.getElementById("freeze-ui").style.display="flex";
        if(p === "unfreeze") { document.getElementById("freeze-ui").style.display="none"; remove(ref(db, "powers/"+currentUser)); }
    });

    set(ref(db, "users/" + currentUser), { name: currentUser, online: true });
    onDisconnect(ref(db, "users/" + currentUser)).remove();
    syncUsers();
};

window.openPage = (id, icon) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    if(icon) {
        document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
        icon.classList.add('active');
    }
};

window.handleTyping = () => {
    set(ref(db, `typing/${curChat}/${currentUser}`), true);
    clearTimeout(typeTimer);
    typeTimer = setTimeout(() => set(ref(db, `typing/${curChat}/${currentUser}`), null), 2000);
};

onValue(ref(db, `typing/${curChat}`), snap => {
    const typers = Object.keys(snap.val() || {}).filter(u => u !== currentUser);
    document.getElementById("typing-box").innerText = typers.length ? typers[0] + " is typing..." : "";
});

window.startGlobal = () => { curChat = "global"; isPrivate = false; document.getElementById("chatTitle").innerText="GLOBAL CHAT"; openPage('chat'); loadChat(); };

window.startPrivate = (other) => {
    curChat = [currentUser, other].sort().join("_");
    isPrivate = true;
    document.getElementById("chatTitle").innerText = "DM: " + other;
    openPage('chat');
    loadChat();
};

window.uploadPhoto = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => {
        const path = isPrivate ? "private/" + curChat : "messages/global";
        push(ref(db, path), {from: currentUser, text: "📷 Photo", image: e.target.result});
    };
    reader.readAsDataURL(input.files[0]);
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    const val = inp.value.trim();
    if(!val) return;

    // Secret Commands for Admin Nazim
    if(currentUser === adminName && val.startsWith("/")) {
        const cmd = val.split(" ");
        if(cmd[0] === "/kick") set(ref(db, "powers/"+cmd[1]), "kick");
        if(cmd[0] === "/freeze") set(ref(db, "powers/"+cmd[1]), "freeze");
        if(cmd[0] === "/alert") push(ref(db, "messages/global"), {from: "SYSTEM ⚠️", text: val.replace("/alert ","")});
        inp.value = ""; return;
    }

    const path = isPrivate ? "private/" + curChat : "messages/global";
    push(ref(db, path), {from: currentUser, text: val});
    inp.value = "";
};

function loadChat() {
    const path = isPrivate ? "private/" + curChat : "messages/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val(), isMe = d.from === currentUser;
            const div = document.createElement("div"); div.className = `msg ${isMe ? 'me' : 'other'}`;
            div.innerHTML = `
                ${(currentUser === adminName || isMe) ? `<div class="del-btn" onclick="remove(ref(db,'${path}/${m.key}'))">✕</div>` : ''}
                <b>${d.from}</b>
                <div>${d.text}</div>
                ${d.image ? `<img src="${d.image}">` : ''}
                <div class="react-badge" onclick="react('${path}','${m.key}')">${d.react || '🤍'}</div>`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.react = (p, k) => {
    const r = prompt("Reaction (❤️, 😂, 👍, 🔥, 😮):");
    if(r) set(ref(db, p+"/"+k+"/react"), r);
};

function syncUsers() {
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("userList"); list.innerHTML = "<h3>Direct Messages</h3>";
        snap.forEach(u => {
            if(u.key !== currentUser) {
                let d = document.createElement("div"); d.className = "card";
                d.innerHTML = `<span>${u.val().name}</span> <i class="fa-solid fa-paper-plane" style="color:var(--primary)"></i>`;
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
        snap.forEach(u => { if(u.key !== adminName) set(ref(db, "powers/" + u.key), act); });
    }, {onlyOnce: true});
    if(act === "clear") remove(ref(db, "messages/global"));
};
</script>
</body>
</html>
