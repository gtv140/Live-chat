<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nazim OS | Ultimate Grid</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #000; --neon: #00ff41; --danger: #ff003c; --blue: #00d2ff; }
        body, html { margin: 0; padding: 0; background: var(--bg); color: var(--neon); font-family: 'Courier New', monospace; overflow: hidden; height: 100%; transition: 0.3s; }
        
        .grid-container { max-width: 500px; margin: auto; height: 100vh; border: 1px solid #111; display: flex; flex-direction: column; position: relative; background: #000; }
        header { padding: 18px; border-bottom: 1px solid #222; display: flex; justify-content: space-between; align-items: center; background: #080808; }
        header h1 { font-size: 14px; margin: 0; letter-spacing: 3px; text-shadow: 0 0 8px var(--neon); }

        .page { display: none; flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 120px; }
        .active { display: flex; flex-direction: column; animation: fadeIn 0.4s; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Hacker Dashboard */
        #hacker-panel { display: none; background: #000; border: 2px solid var(--danger); padding: 12px; margin-bottom: 15px; flex-wrap: wrap; gap: 8px; box-shadow: 0 0 25px rgba(255, 0, 60, 0.4); border-radius: 10px; }
        .hack-btn { flex: 1; min-width: 90px; padding: 10px; font-size: 9px; background: #000; border: 1px solid var(--danger); color: var(--danger); font-weight: bold; cursor: pointer; text-transform: uppercase; }
        .hack-btn:hover { background: var(--danger); color: #000; }

        /* Chat UI */
        .chat-area { display: flex; flex-direction: column; gap: 12px; }
        .msg { padding: 12px; border-radius: 6px; font-size: 14px; max-width: 85%; position: relative; border: 1px solid #222; }
        .me { align-self: flex-end; border-color: var(--blue); color: var(--blue); }
        .other { align-self: flex-start; border-color: #333; color: #ccc; }
        .msg b { font-size: 10px; display: block; margin-bottom: 5px; color: var(--neon); }
        .msg img { max-width: 100%; border-radius: 5px; margin-top: 8px; }
        .del-msg { position: absolute; top: -8px; right: -8px; color: red; cursor: pointer; display: none; background: #000; border: 1px solid red; border-radius: 50%; width: 18px; height: 18px; text-align: center; font-size: 10px; }
        .msg:hover .del-msg { display: block; }

        /* Input Bar */
        .input-bar { position: fixed; bottom: 0; max-width: 500px; width: 100%; padding: 20px; background: #000; border-top: 1px solid #111; display: flex; align-items: center; gap: 15px; }
        .input-bar input { flex: 1; background: #080808; border: 1px solid #333; color: var(--neon); padding: 12px; font-size: 14px; }
        
        /* Overlays */
        #scare-overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:10000; color:var(--neon); flex-direction:column; align-items:center; justify-content:center; text-align:center; padding: 20px; }
        .flip { transform: rotate(180deg); }
    </style>
</head>
<body>

<div id="scare-overlay">
    <i class="fa-solid fa-ghost" style="font-size: 60px; color: red;"></i>
    <h3 id="ov-text">> SYSTEM BREACHED</h3>
    <div id="ov-logs" style="font-size: 10px; text-align: left; margin-top: 10px;"></div>
</div>

<div class="grid-container">
    <header><h1>NAZIM_OS_v8.0</h1><i class="fa-solid fa-microchip"></i></header>

    <div id="loginPage" class="page active">
        <div style="margin-top: 100px; text-align: center;">
            <p>> LOGIN REQUIRED_</p>
            <input type="text" id="userInput" placeholder="NAME" style="width: 80%; background: #000; border: 1px solid var(--neon); color: var(--neon); padding: 15px; text-align: center;">
            <br><br>
            <button onclick="bootSystem()" style="background: var(--neon); color: #000; padding: 10px 40px; border: none; font-weight: bold; cursor: pointer;">BOOT_</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div style="border: 1px solid var(--blue); padding: 15px; margin-bottom: 20px;">
            <h2 id="greet-user" style="color:var(--blue); margin:0;">Agent: Anonymous</h2>
            <p style="font-size: 10px;">SIGNALS: <span id="node-count" style="color:cyan">0</span> | GRID: ACTIVE</p>
        </div>
        <button onclick="enterChat('global', false)" style="width: 100%; padding: 15px; background: #111; border: 1px solid var(--blue); color: var(--blue); cursor: pointer;">JOIN GLOBAL GRID</button>
        <div id="userList" style="margin-top: 20px;"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="hacker-panel">
            <button class="hack-btn" onclick="sendPower('freeze')">Freeze</button>
            <button class="hack-btn" onclick="sendPower('scare')">Matrix</button>
            <button class="hack-btn" onclick="sendPower('vibrate')">Vibrate</button>
            <button class="hack-btn" onclick="sendPower('popup_bomb')">Popups</button>
            <button class="hack-btn" onclick="sendPower('ghost_audio')">Ghost Audio</button>
            <button class="hack-btn" onclick="sendPower('battery_prank')">Battery Prank</button>
            <button class="hack-btn" onclick="sendPower('hijack_type')">Hijack Type</button>
            <button class="hack-btn" onclick="sendPower('restore')" style="color:lime">Restore</button>
            <button class="hack-btn" onclick="nukeDB()" style="background:red; color:white;">NUKE</button>
        </div>
        <div id="chatStream" class="chat-area"></div>
        <div class="input-bar">
            <i id="adminWand" class="fa-solid fa-wand-magic-sparkles" style="display:none; color:gold; cursor:pointer;" onclick="togglePanel()"></i>
            <label for="fileIn"><i class="fa-solid fa-camera" style="cursor:pointer;"></i></label>
            <input type="file" id="fileIn" accept="image/*" style="display:none;" onchange="sendImg(this)">
            <input type="text" id="msgIn" placeholder="Type command...">
            <i class="fa-solid fa-paper-plane" onclick="sendMsg()" style="cursor:pointer;"></i>
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
const ADMIN = "Nazim";

window.bootSystem = () => {
    user = document.getElementById("userInput").value.trim();
    if(!user) return;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("homePage").classList.add("active");
    document.getElementById("greet-user").innerText = "Agent: " + user;
    if(user === ADMIN) document.getElementById("adminWand").style.display="block";

    onValue(ref(db, "traps/" + user), snap => {
        const t = snap.val();
        const ov = document.getElementById("scare-overlay");
        if(t === "freeze") { ov.style.display="flex"; document.getElementById("ov-text").innerText="TERMINAL LOCKED"; }
        if(t === "vibrate") { if(navigator.vibrate) navigator.vibrate([500, 200, 500]); }
        if(t === "scare") { ov.style.display="flex"; runLogs(); }
        if(t === "popup_bomb") { for(let i=0; i<8; i++) alert("SYSTEM ERROR: BREACHED BY NAZIM!"); }
        if(t === "ghost_audio") { new Audio('https://www.soundjay.com/nature/sounds/rain-01.mp3').play(); }
        if(t === "battery_prank") { document.body.style.opacity = "0.1"; alert("Battery Critical (1%)"); }
        if(t === "hijack_type") { document.getElementById("msgIn").oninput = function() { this.value = "NAZIM IS MY BOSS 👑"; }; }
        if(t === "restore") { ov.style.display="none"; document.body.style.opacity="1"; document.getElementById("msgIn").oninput = null; }
    });

    if(user !== ADMIN) set(ref(db, "active_signals/" + user), true);
    syncUsers();
};

function runLogs() {
    const l = ["> BYPASSING SECURITY...", "> DATA EXFILTRATION...", "> STATUS: COMPROMISED"];
    const box = document.getElementById("ov-logs"); box.innerHTML = "";
    let i = 0; setInterval(() => { if(i < l.length) box.innerHTML += l[i++] + "<br>"; }, 700);
}

window.syncUsers = () => {
    onValue(ref(db, "active_signals"), snap => {
        const list = document.getElementById("userList"); list.innerHTML = "";
        let count = 0;
        snap.forEach(u => {
            count++;
            if(u.key !== user) {
                let d = document.createElement("div"); d.style = "padding:12px; background:#080808; border:1px solid #222; margin-top:10px; cursor:pointer;";
                d.innerHTML = `> TARGET: ${u.key} <span style="float:right; color:red;">[ON GRID]</span>`;
                d.onclick = () => enterChat(u.key, true);
                list.appendChild(d);
            }
        });
        document.getElementById("node-count").innerText = count;
    });
};

window.enterChat = (target, isPriv) => {
    room = isPriv ? [user, target].sort().join("::") : "global";
    priv = isPriv;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    loadMessages();
};

window.sendMsg = () => {
    const inp = document.getElementById("msgIn");
    if(!inp.value) return;
    if(user === ADMIN && inp.value.startsWith("/as ")) {
        const p = inp.value.split(" ");
        push(ref(db, "messages/global"), { s: p[1], t: inp.value.replace("/as "+p[1]+" ", "") });
    } else {
        push(ref(db, priv ? "messages/priv/" + room : "messages/global"), { s: user, t: inp.value });
    }
    inp.value = "";
};

window.sendImg = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => push(ref(db, priv ? "messages/priv/" + room : "messages/global"), { s: user, i: e.target.result });
    reader.readAsDataURL(input.files[0]);
};

function loadMessages() {
    const path = priv ? "messages/priv/" + room : "messages/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatStream"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `msg ${d.s === user ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.s}</b> ${d.t ? d.t : `<img src="${d.i}">`} 
            ${(user === ADMIN) ? `<span class="del-msg" onclick="remove(ref(db,'${path}/${m.key}'))">✕</span>` : ''}`;
            box.appendChild(div);
        });
        box.parentElement.scrollTop = box.parentElement.scrollHeight;
    });
}

window.togglePanel = () => { let p = document.getElementById("hacker-panel"); p.style.display = p.style.display === "flex" ? "none" : "flex"; };
window.sendPower = (type) => { onValue(ref(db, "active_signals"), snap => { snap.forEach(u => { if(u.key !== ADMIN) set(ref(db, "traps/" + u.key), type); }); }, {onlyOnce:true}); };
window.nukeDB = () => { if(confirm("NUKE EVERYTHING?")) remove(ref(db)); };
document.getElementById("msgIn").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendMsg(); });
</script>
</body>
</html>
