<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nazim | Hacker Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #050505; --neon: #00ff41; --danger: #ff003c; --glass: rgba(10, 10, 10, 0.95); --border: #00ff4133; }
        body { margin: 0; font-family: 'Courier New', monospace; background: var(--bg); color: var(--neon); overflow: hidden; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; border-left: 1px solid var(--border); border-right: 1px solid var(--border); }
        
        header { padding: 15px; background: var(--glass); border-bottom: 1px solid var(--neon); text-align: center; box-shadow: 0 0 15px var(--neon); }
        header h1 { margin: 0; font-size: 1.5rem; letter-spacing: 5px; text-shadow: 0 0 10px var(--neon); }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; }
        .active { display: block; }

        /* Hacker Style Cards */
        .card { background: #000; border: 1px solid var(--neon); padding: 15px; border-radius: 5px; margin-bottom: 10px; cursor: pointer; transition: 0.3s; position: relative; overflow: hidden; }
        .card:hover { background: var(--neon); color: #000; box-shadow: 0 0 20px var(--neon); }

        /* Admin Console UI */
        #admin-console { display: none; background: #000; border: 2px solid var(--danger); padding: 15px; border-radius: 10px; margin: 10px; flex-wrap: wrap; gap: 10px; box-shadow: 0 0 20px var(--danger); }
        .hack-btn { flex: 1; padding: 10px; background: #000; border: 1px solid var(--danger); color: var(--danger); font-weight: bold; cursor: pointer; font-size: 10px; text-transform: uppercase; }
        .hack-btn:hover { background: var(--danger); color: #fff; }

        .msg { margin-bottom: 15px; padding: 10px; border-left: 3px solid var(--neon); background: rgba(0, 255, 65, 0.05); position: relative; }
        .msg b { color: var(--danger); font-size: 12px; text-transform: uppercase; }
        .del-btn { position: absolute; top: 5px; right: 5px; color: var(--danger); cursor: pointer; display: none; }
        .msg:hover .del-btn { display: block; }

        .input-area { position: absolute; bottom: 80px; width: 100%; padding: 15px; box-sizing: border-box; background: var(--bg); }
        .hack-input { width: 100%; background: #000; border: 1px solid var(--neon); color: var(--neon); padding: 12px; border-radius: 5px; outline: none; box-shadow: inset 0 0 5px var(--neon); }

        #freeze-overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:99999; color:var(--danger); flex-direction:column; align-items:center; justify-content:center; }
        .vibrate { animation: shake 0.5s infinite; }
        @keyframes shake { 0% { transform: translate(1px, 1px); } 10% { transform: translate(-1px, -2px); } 20% { transform: translate(-3px, 0px); } }
    </style>
</head>
<body id="bodyTag">

<div id="freeze-overlay">
    <i class="fa-solid fa-skull-crossbones" style="font-size: 100px;"></i>
    <h1>SYSTEM HIJACKED</h1>
    <p>ACCESS DENIED BY ADMIN</p>
</div>

<div class="app">
    <header><h1>NAZIM_OS v1.0</h1></header>

    <div id="loginPage" class="page active">
        <div style="text-align: center; margin-top: 50px;">
            <i class="fa-solid fa-user-secret" style="font-size: 80px;"></i>
            <p>> INITIALIZING SECURITY BYPASS...</p>
            <input type="text" id="usernameInput" class="hack-input" placeholder="ENTER CODENAME..." style="text-align:center">
            <button onclick="login()" style="width:100%; padding:15px; margin-top:20px; background:var(--neon); color:#000; border:none; font-weight:bold; cursor:pointer">ACCESS SYSTEM</button>
        </div>
    </div>

    <div id="home" class="page">
        <p>> TARGETS DETECTED:</p>
        <div id="userList"></div>
        <button onclick="startGlobal()" class="card" style="width:100%">[ ACCESS GLOBAL CHAT ]</button>
    </div>

    <div id="chat" class="page">
        <div id="admin-console">
            <button class="hack-btn" onclick="executeHack('freeze')">Freeze All</button>
            <button class="hack-btn" onclick="executeHack('prank')">Vibrate Prank</button>
            <button class="hack-btn" onclick="executeHack('hijack')">YouTube Hijack</button>
            <button class="hack-btn" onclick="executeHack('unfreeze')" style="border-color:var(--neon); color:var(--neon)">Restore All</button>
        </div>
        <div id="chatBox"></div>
        <div class="input-area">
            <input type="text" id="msgInput" class="hack-input" placeholder="Execute command or message...">
            <button onclick="sendMsg()" style="display:none"></button>
        </div>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
    apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
    authDomain: "live-chat-b810c.firebaseapp.com",
    databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
    projectId: "live-chat-b810c"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = null, curChat = "global", isPrivate = false;
const adminName = "Nazim"; 

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("home").classList.add("active");
    
    if(currentUser === adminName) document.getElementById("admin-console").style.display="flex";

    // Power Listener for Victims
    onValue(ref(db, "powers/" + currentUser), snap => {
        const p = snap.val();
        if(p === "freeze") document.getElementById("freeze-overlay").style.display="flex";
        if(p === "unfreeze") { document.getElementById("freeze-overlay").style.display="none"; document.getElementById("bodyTag").classList.remove("vibrate"); }
        if(p === "prank") { document.getElementById("bodyTag").classList.add("vibrate"); new Audio('https://www.soundjay.com/buttons/beep-01a.mp3').play(); }
        if(p === "hijack") window.location.href = "https://www.youtube.com";
    });

    set(ref(db, "users/" + currentUser), { name: currentUser, online: true });
    syncUsers();
};

window.startGlobal = () => { curChat = "global"; isPrivate = false; openPage('chat'); loadChat(); };

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    const val = inp.value.trim();
    if(!val) return;

    // --- NAZIM'S HACKING COMMANDS ---
    const cmd = val.split(" ");
    if(currentUser === adminName) {
        if(cmd[0] === "/as") { // Impersonate: /as Ali Hello
            const victim = cmd[1];
            const msg = val.replace("/as "+victim+" ", "");
            push(ref(db, "messages/global"), { from: victim, text: msg });
            inp.value = ""; return;
        }
        if(cmd[0] === "/freeze") { set(ref(db, "powers/"+cmd[1]), "freeze"); inp.value = ""; return; }
    }

    const path = isPrivate ? "private/" + curChat : "messages/global";
    push(ref(db, path), { from: currentUser, text: val });
    inp.value = "";
};

// Handle Enter Key
document.getElementById("msgInput").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendMsg(); });

function loadChat() {
    const path = isPrivate ? "private/" + curChat : "messages/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = "msg";
            div.innerHTML = `<b>[${d.from}]</b>: ${d.text} 
            ${(currentUser === adminName) ? `<i class="fa-solid fa-trash del-btn" onclick="remove(ref(db,'${path}/${m.key}'))"></i>` : ''}`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

function syncUsers() {
    onValue(ref(db, "users"), snap => {
        const list = document.getElementById("userList"); list.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== currentUser) {
                let d = document.createElement("div"); d.className = "card";
                d.innerHTML = `<span>> TARGET: ${u.key}</span> <i class="fa-solid fa-microchip"></i>`;
                d.onclick = () => { curChat = [currentUser, u.key].sort().join("_"); isPrivate = true; openPage('chat'); loadChat(); };
                list.appendChild(d);
            }
        });
    });
}

window.executeHack = (act) => {
    onValue(ref(db, "users"), snap => {
        snap.forEach(u => { if(u.key !== adminName) set(ref(db, "powers/" + u.key), act); });
    }, {onlyOnce: true});
};

window.openPage = (id) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
};
</script>
</body>
</html>
