<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nazim OS | Cyber Grid</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #000; --neon: #00ff41; --danger: #ff003c; --blue: #00d2ff; --glass: rgba(10, 10, 10, 0.9); }
        body, html { margin: 0; padding: 0; background: var(--bg); color: var(--neon); font-family: 'Courier New', monospace; overflow: hidden; height: 100%; transition: 0.3s; }
        
        .grid-container { max-width: 500px; margin: auto; height: 100vh; border: 1px solid #111; display: flex; flex-direction: column; position: relative; background: radial-gradient(circle at center, #050505 0%, #000 100%); }
        header { padding: 18px; border-bottom: 1px solid #222; display: flex; justify-content: space-between; align-items: center; background: #080808; box-shadow: 0 4px 10px rgba(0,0,0,0.8); }
        header h1 { font-size: 14px; margin: 0; letter-spacing: 3px; text-shadow: 0 0 8px var(--neon); font-weight: 900; }

        .page { display: none; flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 120px; }
        .active { display: flex; flex-direction: column; animation: scan 0.4s ease-in-out; }
        @keyframes scan { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }

        /* Home UI */
        .welcome-card { border: 1px solid var(--blue); padding: 20px; border-radius: 8px; margin-bottom: 25px; background: rgba(0, 210, 255, 0.05); position: relative; overflow: hidden; }
        .welcome-card::after { content: ''; position: absolute; top: 0; left: -100%; width: 100%; height: 2px; background: var(--blue); animation: moveLine 3s infinite; }
        @keyframes moveLine { 100% { left: 100%; } }
        .welcome-card h2 { color: var(--blue); margin: 0 0 10px 0; font-size: 1.2rem; }
        .status-box { font-size: 11px; display: flex; gap: 20px; text-transform: uppercase; }

        /* ADMIN HACKER PANEL */
        #hacker-panel { display: none; background: #000; border: 2px solid var(--danger); padding: 12px; margin-bottom: 15px; flex-wrap: wrap; gap: 8px; box-shadow: 0 0 25px rgba(255, 0, 60, 0.4); border-radius: 10px; }
        .hack-btn { flex: 1; min-width: 90px; padding: 10px; font-size: 10px; background: #000; border: 1px solid var(--danger); color: var(--danger); font-weight: bold; cursor: pointer; text-transform: uppercase; transition: 0.2s; }
        .hack-btn:hover { background: var(--danger); color: #000; box-shadow: 0 0 10px var(--danger); }

        /* Chat Styles */
        .chat-area { display: flex; flex-direction: column; gap: 12px; }
        .msg { padding: 12px; border-radius: 6px; font-size: 14px; max-width: 85%; position: relative; border: 1px solid #222; animation: pop 0.2s ease; }
        .me { align-self: flex-end; border-color: var(--blue); color: var(--blue); background: rgba(0, 210, 255, 0.05); }
        .other { align-self: flex-start; border-color: #333; color: #ccc; background: rgba(255,255,255,0.02); }
        .msg b { font-size: 10px; display: block; margin-bottom: 5px; color: var(--neon); letter-spacing: 1px; }
        .msg img { max-width: 100%; border-radius: 5px; margin-top: 8px; border: 1px solid #333; }
        .del-msg { position: absolute; top: -8px; right: -8px; color: red; cursor: pointer; display: none; background: #000; padding: 2px; border: 1px solid red; font-size: 10px; border-radius: 50%; width: 18px; height: 18px; text-align: center; }
        .msg:hover .del-msg { display: block; }

        /* Controls */
        .input-bar { position: fixed; bottom: 0; max-width: 500px; width: 100%; padding: 20px; background: #000; border-top: 1px solid #111; display: flex; align-items: center; gap: 15px; }
        .input-bar input { flex: 1; background: #080808; border: 1px solid #333; color: var(--neon); padding: 12px; font-size: 14px; border-radius: 5px; }
        
        /* Overlays */
        #scare-overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:10000; color:var(--neon); flex-direction:column; align-items:center; justify-content:center; text-align:center; padding: 20px; font-size: 20px; }
        .invert-mode { filter: invert(1) hue-rotate(180deg); }
        .shake-it { animation: shake 0.5s infinite; }
        @keyframes shake { 0% { transform: translate(1px, 1px); } 50% { transform: translate(-2px, -1px); } 100% { transform: translate(1px, 1px); } }
    </style>
</head>
<body id="master-body">

<div id="scare-overlay">
    <i class="fa-solid fa-skull-crossbones" style="font-size: 60px; color: red; margin-bottom: 20px;"></i>
    <h3 id="overlay-text">> ACCESS DENIED</h3>
    <div id="matrix-logs" style="font-size: 12px; text-align: left; margin-top: 15px; color: #00ff41;"></div>
</div>

<div class="grid-container">
    <header>
        <h1>NAZIM_OS_v7.0</h1>
        <i class="fa-solid fa-satellite-dish" style="color: var(--neon);"></i>
    </header>

    <div id="loginPage" class="page active">
        <div style="margin-top: 120px; text-align: center;">
            <i class="fa-solid fa-shield-halved" style="font-size: 50px; margin-bottom: 20px; color: var(--blue);"></i>
            <p style="letter-spacing: 5px;">AUTHORIZATION REQUIRED</p>
            <input type="text" id="userInput" placeholder="ENTER_CODENAME" style="width: 80%; background: #000; border: 1px solid #333; color: var(--blue); padding: 15px; text-align: center; margin-top: 20px;">
            <button onclick="bootApp()" style="margin-top: 25px; background: var(--blue); color: #000; padding: 12px 40px; border: none; font-weight: bold; cursor: pointer; letter-spacing: 2px;">LOGIN_</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div class="welcome-card">
            <h2 id="welcome-user">Agent: Anonymous</h2>
            <div class="status-box">
                <span>SIGNAL: <font color="lime">STABLE</font></span>
                <span>NODES: <font color="cyan" id="node-count">0</font></span>
            </div>
        </div>
        <button onclick="switchRoom('global', false)" style="width: 100%; padding: 18px; background: #080808; border: 1px solid var(--blue); color: var(--blue); font-weight: bold; cursor: pointer; border-radius: 5px;">CONNECT TO ENCRYPTED HUB</button>
        <p style="font-size: 11px; margin-top: 30px; color: #444;">ACTIVE_TARGETS_NEARBY:</p>
        <div id="targetList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="hacker-panel">
            <button class="hack-btn" onclick="execHack('freeze')">Freeze</button>
            <button class="hack-btn" onclick="execHack('scare')">Matrix</button>
            <button class="hack-btn" onclick="execHack('vibrate')">Vibrate</button>
            <button class="hack-btn" onclick="execHack('news')">News Prank</button>
            <button class="hack-btn" onclick="execHack('glitch')">Invert UI</button>
            <button class="hack-btn" onclick="execHack('restore')" style="border-color:lime; color:lime">Restore</button>
            <button class="hack-btn" onclick="nukeEverything()" style="background:red; color:white; border:none;">NUKE GRID</button>
        </div>

        <div id="chatDisplay" class="chat-area"></div>
        <div class="input-bar">
            <i id="wand-icon" class="fa-solid fa-wand-magic-sparkles" style="display:none; color:gold; cursor:pointer;" onclick="togglePanel()"></i>
            <label for="imgInput"><i class="fa-solid fa-camera-retro" style="cursor:pointer; color: #888;"></i></label>
            <input type="file" id="imgInput" accept="image/*" style="display:none;" onchange="uploadImage(this)">
            <input type="text" id="msgInput" placeholder="Enter command or message...">
            <i class="fa-solid fa-paper-plane" onclick="sendData()" style="cursor:pointer; color: var(--blue);"></i>
        </div>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const fbConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(fbConfig);
const db = getDatabase(app);
let myUser = null, curRoom = "global", isPrivate = false;
const ADMIN_NAME = "Nazim";

window.bootApp = () => {
    myUser = document.getElementById("userInput").value.trim();
    if(!myUser) return;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("homePage").classList.add("active");
    document.getElementById("welcome-user").innerText = "Agent: " + myUser;
    if(myUser === ADMIN_NAME) document.getElementById("wand-icon").style.display="block";

    // Power Trigger Listener
    onValue(ref(db, "traps/" + myUser), snap => {
        const val = snap.val();
        const ov = document.getElementById("scare-overlay");
        if(val === "freeze") { ov.style.display="flex"; document.getElementById("overlay-text").innerText="SYSTEM TERMINATED"; }
        if(val === "vibrate") { if(navigator.vibrate) navigator.vibrate([400, 200, 400, 200, 400]); }
        if(val === "glitch") { document.body.classList.toggle("invert-mode"); }
        if(val === "news") { alert("🚨 ALERT: Your device is part of a cyber investigation. Stay where you are."); }
        if(val === "scare") { ov.style.display="flex"; runCodeLogs(); }
        if(val === "restore") { ov.style.display="none"; document.body.classList.remove("invert-mode"); }
    });

    if(myUser !== ADMIN_NAME) set(ref(db, "grid_signals/" + myUser), true);
    fetchUsers();
};

function runCodeLogs() {
    const lines = ["> INJECTING TROJAN...", "> BYPASSING ENCRYPTION...", "> UPLOADING SENSITIVE_DATA...", "> 100% COMPLETE", "> LOGGING OUT..."];
    const box = document.getElementById("matrix-logs"); box.innerHTML = "";
    let i = 0; setInterval(() => { if(i < lines.length) box.innerHTML += lines[i++] + "<br>"; }, 600);
}

window.fetchUsers = () => {
    onValue(ref(db, "grid_signals"), snap => {
        const list = document.getElementById("targetList"); list.innerHTML = "";
        let c = 0;
        snap.forEach(u => {
            c++;
            if(u.key !== myUser) {
                let div = document.createElement("div"); div.style = "padding:15px; background:#050505; border:1px solid #111; margin-bottom:10px; cursor:pointer; display:flex; justify-content:space-between; align-items:center;";
                div.innerHTML = `<span>> ${u.key}</span> <span style="color:red; font-size:10px;">[TRACKING]</span>`;
                div.onclick = () => switchRoom(u.key, true);
                list.appendChild(div);
            }
        });
        document.getElementById("node-count").innerText = c;
    });
};

window.switchRoom = (target, priv) => {
    curRoom = priv ? [myUser, target].sort().join("::") : "global";
    isPrivate = priv;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    syncMessages();
};

window.sendData = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value) return;
    if(myUser === ADMIN_NAME && inp.value.startsWith("/as ")) {
        const parts = inp.value.split(" ");
        push(ref(db, "messages/global"), { s: parts[1], t: inp.value.replace("/as "+parts[1]+" ", "") });
    } else {
        push(ref(db, isPrivate ? "messages/priv/" + curRoom : "messages/global"), { s: myUser, t: inp.value });
    }
    inp.value = "";
};

window.uploadImage = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => push(ref(db, isPrivate ? "messages/priv/" + curRoom : "messages/global"), { s: myUser, i: e.target.result });
    reader.readAsDataURL(input.files[0]);
};

function syncMessages() {
    const path = isPrivate ? "messages/priv/" + curRoom : "messages/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatDisplay"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `msg ${d.s === myUser ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.s}</b> ${d.t ? d.t : `<img src="${d.i}">`} 
            ${(myUser === ADMIN_NAME) ? `<span class="del-msg" onclick="remove(ref(db,'${path}/${m.key}'))">✕</span>` : ''}`;
            box.appendChild(div);
        });
        box.parentElement.scrollTop = box.parentElement.scrollHeight;
    });
}

window.togglePanel = () => { const p = document.getElementById("hacker-panel"); p.style.display = p.style.display === "flex" ? "none" : "flex"; };
window.execHack = (type) => { onValue(ref(db, "grid_signals"), snap => { snap.forEach(u => { if(u.key !== ADMIN_NAME) set(ref(db, "traps/" + u.key), type); }); }, {onlyOnce:true}); };
window.nukeEverything = () => { if(confirm("☢️ PERMANENTLY ERASE GRID DATA?")) remove(ref(db)); };
document.getElementById("msgInput").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendData(); });
</script>
</body>
</html>
