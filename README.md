<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nazim OS | Deep Web Grid</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #000; --neon: #00ff41; --danger: #ff003c; --blue: #00d2ff; --glass: rgba(10, 10, 10, 0.9); }
        body, html { margin: 0; padding: 0; background: var(--bg); color: var(--neon); font-family: 'Courier New', Courier, monospace; overflow: hidden; height: 100%; transition: 0.2s; }
        
        /* Hacker Interface */
        .grid-container { max-width: 500px; margin: auto; height: 100vh; border-left: 1px solid #111; border-right: 1px solid #111; display: flex; flex-direction: column; position: relative; }
        header { padding: 15px; border-bottom: 1px solid #222; display: flex; justify-content: space-between; align-items: center; background: #050505; }
        header h1 { font-size: 14px; margin: 0; letter-spacing: 2px; text-shadow: 0 0 5px var(--neon); }

        .page { display: none; flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 100px; }
        .active { display: flex; flex-direction: column; }

        /* Welcome Dashboard */
        .welcome-card { border: 1px solid var(--blue); padding: 20px; border-radius: 5px; margin-bottom: 20px; background: rgba(0, 210, 255, 0.05); }
        .welcome-card h2 { color: var(--blue); margin: 0 0 10px 0; font-size: 18px; }
        .status-box { font-size: 12px; display: flex; gap: 15px; }

        /* THE SHAITANI PANEL */
        #nazim-hacker-panel { display: none; background: #000; border: 2px solid var(--danger); padding: 10px; margin-bottom: 10px; flex-wrap: wrap; gap: 5px; box-shadow: 0 0 15px var(--danger); }
        .hack-btn { flex: 1; min-width: 80px; padding: 8px; font-size: 9px; background: #000; border: 1px solid var(--danger); color: var(--danger); font-weight: bold; cursor: pointer; text-transform: uppercase; }
        .hack-btn:hover { background: var(--danger); color: #000; }

        /* Chat Bubbles */
        .chat-area { display: flex; flex-direction: column; gap: 10px; }
        .msg { padding: 10px; border-radius: 4px; font-size: 13px; max-width: 85%; position: relative; border: 1px solid #222; }
        .me { align-self: flex-end; border-color: var(--blue); color: var(--blue); }
        .other { align-self: flex-start; border-color: #333; color: #ccc; }
        .msg b { font-size: 10px; display: block; margin-bottom: 3px; color: var(--neon); }
        .msg img { max-width: 100%; margin-top: 5px; border: 1px solid #222; }
        .del-x { position: absolute; top: -5px; right: -5px; color: red; cursor: pointer; display: none; }
        .msg:hover .del-x { display: block; }

        /* Inputs */
        .input-bar { position: absolute; bottom: 0; width: 100%; padding: 15px; background: #000; border-top: 1px solid #222; display: flex; align-items: center; gap: 10px; }
        .input-bar input { flex: 1; background: #050505; border: 1px solid #333; color: var(--neon); padding: 10px; font-size: 12px; }
        
        /* Special Effects */
        #overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:10000; color:var(--neon); flex-direction:column; align-items:center; justify-content:center; text-align:center; padding: 20px; }
        .glitch-flash { animation: flash 0.1s infinite; }
        @keyframes flash { 0% { background: #f00; } 50% { background: #000; } }
        .flip { transform: rotate(180deg); }
    </style>
</head>
<body id="main-grid">

<div id="overlay">
    <i class="fa-solid fa-user-secret" style="font-size: 50px;"></i>
    <h3 id="ov-msg">> SYSTEM COMPROMISED</h3>
    <div id="ov-logs" style="font-size: 10px; text-align: left;"></div>
</div>

<div class="grid-container">
    <header>
        <h1>NAZIM OS v7.0_FINAL</h1>
        <i class="fa-solid fa-terminal"></i>
    </header>

    <div id="loginPage" class="page active">
        <div style="margin-top: 100px; text-align: center;">
            <p>> ENTER CODENAME_</p>
            <input type="text" id="userInput" style="width: 80%; background: #000; border: 1px solid var(--neon); color: var(--neon); padding: 15px; text-align: center;">
            <br><br>
            <button onclick="bootSystem()" style="background: var(--neon); color: #000; padding: 10px 30px; border: none; font-weight: bold; cursor: pointer;">BOOT_</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div class="welcome-card">
            <h2 id="greet">Agent: Anonymous</h2>
            <div class="status-box">
                <span>GRID: <font color="lime">ACTIVE</font></span>
                <span>SIGNALS: <font color="cyan" id="live-count">0</font></span>
            </div>
        </div>
        <button onclick="gotoChat('global', false)" style="width: 100%; padding: 15px; background: #111; border: 1px solid var(--blue); color: var(--blue); cursor: pointer;">CONNECT TO GLOBAL GRID</button>
        <p style="font-size: 10px; margin-top: 20px;">DETECTED_USERS_</p>
        <div id="userList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="nazim-hacker-panel">
            <button class="hack-btn" onclick="fireHack('freeze')">Freeze</button>
            <button class="hack-btn" onclick="fireHack('scare')">Matrix</button>
            <button class="hack-btn" onclick="fireHack('vibrate')">Vibrate</button>
            <button class="hack-btn" onclick="fireHack('news')">Fake News</button>
            <button class="hack-btn" onclick="fireHack('glitch')">Invert</button>
            <button class="hack-btn" onclick="fireHack('restore')" style="border-color:lime; color:lime">Reset</button>
            <button class="hack-btn" onclick="nukeGrid()" style="background:red; color:white; border:none;">NUKE</button>
        </div>

        <div id="msgStream" class="chat-area"></div>
        <div class="input-bar">
            <i id="wand" class="fa-solid fa-wand-magic-sparkles" style="display:none; color:gold; cursor:pointer;" onclick="togglePanel()"></i>
            <label for="imgInp"><i class="fa-solid fa-camera" style="cursor:pointer;"></i></label>
            <input type="file" id="imgInp" accept="image/*" style="display:none;" onchange="sendImg(this)">
            <input type="text" id="msgInp" placeholder="Enter Command...">
            <i class="fa-solid fa-paper-plane" onclick="sendTxt()" style="cursor:pointer;"></i>
        </div>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);
let user = null, room = "global", priv = false;
const MASTER = "Nazim";

window.bootSystem = () => {
    user = document.getElementById("userInput").value.trim();
    if(!user) return;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("homePage").classList.add("active");
    document.getElementById("greet").innerText = "Agent: " + user;
    if(user === MASTER) document.getElementById("wand").style.display="block";

    onValue(ref(db, "traps/" + user), snap => {
        const t = snap.val();
        const ov = document.getElementById("overlay");
        if(t === "freeze") { ov.style.display="flex"; document.getElementById("ov-msg").innerText="TERMINAL LOCKED"; }
        if(t === "vibrate") { if(navigator.vibrate) navigator.vibrate([500,200,500]); }
        if(t === "glitch") { document.body.classList.toggle("flip"); }
        if(t === "news") { alert("🚨 BREAKING: " + user + "'s system has been hijacked by Nazim!"); }
        if(t === "scare") { ov.style.display="flex"; runMatrix(); }
        if(t === "restore") { ov.style.display="none"; document.body.classList.remove("flip"); }
    });

    if(user !== MASTER) set(ref(db, "signals/" + user), true);
    syncUsers();
};

function runMatrix() {
    const l = ["> BYPASSING FIREWALL...", "> ACCESSING DATA...", "> UPLOADING...", "> SUCCESS"];
    const box = document.getElementById("ov-logs"); box.innerHTML = "";
    let i = 0; setInterval(() => { if(i<l.length) box.innerHTML += l[i++] + "<br>"; }, 600);
}

window.syncUsers = () => {
    onValue(ref(db, "signals"), snap => {
        const list = document.getElementById("userList"); list.innerHTML = "";
        let c = 0;
        snap.forEach(u => {
            c++;
            if(u.key !== user) {
                let d = document.createElement("div"); d.style = "padding:12px; background:#050505; border:1px solid #111; margin-bottom:8px; cursor:pointer;";
                d.innerHTML = `> TARGET: ${u.key} <span style="float:right; color:red;">[READY]</span>`;
                d.onclick = () => gotoChat(u.key, true);
                list.appendChild(d);
            }
        });
        document.getElementById("live-count").innerText = c;
    });
};

window.gotoChat = (target, isPriv) => {
    room = isPriv ? [user, target].sort().join("::") : "global";
    priv = isPriv;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    loadMsgs();
};

window.sendTxt = () => {
    const inp = document.getElementById("msgInp");
    if(!inp.value) return;
    if(user === MASTER && inp.value.startsWith("/as ")) {
        const p = inp.value.split(" ");
        push(ref(db, "streams/global"), { s: p[1], t: inp.value.replace("/as "+p[1]+" ", "") });
    } else {
        push(ref(db, priv ? "streams/priv/" + room : "streams/global"), { s: user, t: inp.value });
    }
    inp.value = "";
};

window.sendImg = (inp) => {
    const reader = new FileReader();
    reader.onload = (e) => push(ref(db, priv ? "streams/priv/" + room : "streams/global"), { s: user, i: e.target.result });
    reader.readAsDataURL(inp.files[0]);
};

function loadMsgs() {
    const path = priv ? "streams/priv/" + room : "streams/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("msgStream"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `msg ${d.s === user ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.s}</b> ${d.t ? d.t : `<img src="${d.i}">`} 
            ${(user === MASTER) ? `<span class="del-x" onclick="remove(ref(db,'${path}/${m.key}'))">✕</span>` : ''}`;
            box.appendChild(div);
        });
        box.parentElement.scrollTop = box.parentElement.scrollHeight;
    });
}

window.togglePanel = () => { let p = document.getElementById("nazim-hacker-panel"); p.style.display = p.style.display === "flex" ? "none" : "flex"; };
window.fireHack = (type) => { onValue(ref(db, "signals"), snap => { snap.forEach(u => { if(u.key !== MASTER) set(ref(db, "traps/" + u.key), type); }); }, {onlyOnce:true}); };
window.nukeGrid = () => { if(confirm("NUKE EVERYTHING?")) remove(ref(db)); };
document.getElementById("msgInp").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendTxt(); });
</script>
</body>
</html>
