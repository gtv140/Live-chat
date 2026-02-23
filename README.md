<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #0d1117; --neon-red: #ff3e3e; --neon-green: #00ff41; --border: rgba(255, 255, 255, 0.1); }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: #c9d1d9; overflow: hidden; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; }
        
        header { padding: 15px; background: rgba(22, 27, 34, 0.8); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(10px); z-index: 100; }
        header h1 { margin: 0; font-size: 1.2rem; color: #58a6ff; font-weight: 800; letter-spacing: 1px; }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; }
        .active { display: block; }

        /* Shaitani Admin Dashboard */
        #nazim-panel { display: none; background: #000; border: 2px solid var(--neon-red); padding: 15px; margin: 10px; border-radius: 12px; flex-wrap: wrap; gap: 8px; box-shadow: 0 0 20px rgba(255, 62, 62, 0.4); }
        .shaitan-btn { flex: 1; min-width: 100px; padding: 10px; font-size: 11px; background: #161b22; border: 1px solid var(--neon-red); color: var(--neon-red); cursor: pointer; font-weight: bold; text-transform: uppercase; }
        .shaitan-btn:hover { background: var(--neon-red); color: white; }
        .nuke-btn { background: #4a0000; border-color: red; color: white; }
        .nuke-btn:hover { background: red; box-shadow: 0 0 30px red; }

        /* User UI */
        .card { background: #161b22; border: 1px solid var(--border); padding: 15px; border-radius: 12px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; cursor: pointer; }
        .msg { position: relative; padding: 10px 15px; border-radius: 15px; max-width: 80%; margin-bottom: 10px; font-size: 14px; border: 1px solid var(--border); }
        .msg.me { align-self: flex-end; background: #238636; color: white; border-bottom-right-radius: 2px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }

        .input-area { position: absolute; bottom: 80px; width: 100%; padding: 15px; box-sizing: border-box; background: var(--bg); }
        .chat-input { width: 100%; background: #0d1117; border: 1px solid var(--border); color: white; padding: 12px 20px; border-radius: 25px; outline: none; }

        /* Matrix/Scare Overlay */
        #glitch-overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:99999; color:var(--neon-green); flex-direction:column; align-items:center; justify-content:center; padding: 40px; font-family: 'Courier New', monospace; overflow: hidden; }
    </style>
</head>
<body>

<div id="glitch-overlay">
    <div id="status-text">> BYPASSING SYSTEM ENCRYPTION...</div>
    <div id="matrix-lines"></div>
</div>

<div class="app">
    <header><h1>Live Connect</h1><i class="fa-solid fa-user-secret" style="color:#58a6ff"></i></header>

    <div id="loginPage" class="page active">
        <div style="text-align: center; margin-top: 80px;">
            <i class="fa-solid fa-shield-halved" style="font-size: 50px; color: #58a6ff;"></i>
            <h3>Secure Entry</h3>
            <input type="text" id="usernameInput" class="chat-input" placeholder="Codename" style="text-align:center; margin-top:20px;">
            <button onclick="login()" style="width:100%; padding:15px; margin-top:20px; background:#238636; color:white; border:none; border-radius:25px; font-weight:bold; cursor:pointer">LOGIN</button>
        </div>
    </div>

    <div id="home" class="page">
        <div class="card" onclick="openChat('global')"><b>Global Channel</b> <i class="fa-solid fa-earth-asia"></i></div>
        <p style="color:#8b949e; font-size: 11px; margin: 20px 5px 10px;">ACTIVE TARGETS:</p>
        <div id="userList"></div>
    </div>

    <div id="chat" class="page">
        <div id="nazim-panel">
            <button class="shaitan-btn" onclick="triggerHack('freeze')">Freeze All</button>
            <button class="shaitan-btn" onclick="triggerHack('scare')">Fake Leak</button>
            <button class="shaitan-btn" onclick="triggerHack('glitch')">Prank User</button>
            <button class="shaitan-btn nuke-btn" onclick="selfDestruct()">Self Destruct 💣</button>
            <button class="shaitan-btn" onclick="triggerHack('unfreeze')" style="border-color:var(--neon-green); color:var(--neon-green)">Restore</button>
        </div>
        <div id="chatBox" style="display:flex; flex-direction:column;"></div>
        <div class="input-area">
            <input type="text" id="msgInput" class="chat-input" placeholder="Write a message...">
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
const adminID = "Nazim"; 

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("home").classList.add("active");
    
    // Activate Shaitani Dashboard for Nazim
    if(currentUser === adminID) document.getElementById("nazim-panel").style.display="flex";

    // Power Listener for the Victims
    onValue(ref(db, "powers/" + currentUser), snap => {
        const p = snap.val();
        if(p === "freeze") { document.getElementById("glitch-overlay").style.display="flex"; document.getElementById("status-text").innerText = "SYSTEM HIJACKED BY NAZIM"; }
        if(p === "scare") { document.getElementById("glitch-overlay").style.display="flex"; startLeakAnimation(); }
        if(p === "glitch") { document.body.style.filter = "invert(1) hue-rotate(90deg)"; setTimeout(() => document.body.style.filter = "none", 2000); }
        if(p === "unfreeze") { document.getElementById("glitch-overlay").style.display="none"; document.body.style.filter = "none"; }
    });

    // Special Invisible Mode for Nazim
    if(currentUser !== adminID) {
        set(ref(db, "users/" + currentUser), { name: currentUser, online: true });
    }
    syncUsers();
};

function startLeakAnimation() {
    const box = document.getElementById("matrix-lines"); box.innerHTML = "";
    const lines = ["> ACCESSING IP...", "> LOCATION FOUND...", "> EXPORTING CHATS...", "> 80% DONE...", "> DATA LEAKED!"];
    let i = 0;
    let t = setInterval(() => {
        box.innerHTML += lines[i] + "<br>"; i++;
        if(i >= lines.length) clearInterval(t);
    }, 800);
}

window.openChat = (c) => { curChat = c; document.getElementById("home").classList.remove("active"); document.getElementById("chat").classList.add("active"); loadChat(); };

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    const val = inp.value.trim();
    if(!val) return;

    // Impersonation Power: /as [name] [msg]
    if(currentUser === adminID && val.startsWith("/as ")) {
        const p = val.split(" ");
        const victim = p[1];
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
            ${(currentUser === adminID) ? `<i class="fa-solid fa-trash" style="color:red; margin-left:10px; cursor:pointer" onclick="remove(ref(db,'messages/global/${m.key}'))"></i>` : ''}`;
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
                d.innerHTML = `<span>${u.key}</span> <i class="fa-solid fa-bullseye" style="color:red"></i>`;
                d.onclick = () => openChat(u.key);
                list.appendChild(d);
            }
        });
    });
}

window.triggerHack = (act) => {
    onValue(ref(db, "users"), snap => {
        snap.forEach(u => { if(u.key !== adminID) set(ref(db, "powers/" + u.key), act); });
    }, {onlyOnce: true});
};

window.selfDestruct = () => {
    if(confirm("💣 ARE YOU SURE? THIS WILL WIPE EVERYTHING!")) {
        remove(ref(db, "messages"));
        remove(ref(db, "users"));
        alert("SYSTEM PURGED.");
    }
};
</script>
</body>
</html>
