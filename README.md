<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nazim OS | Cyber Network</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #030712; --accent: #3b82f6; --danger: #ef4444; --glass: rgba(17, 24, 39, 0.8); --border: rgba(255, 255, 255, 0.05); }
        * { box-sizing: border-box; outline: none; }
        body { margin: 0; font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg); color: #f3f4f6; overflow: hidden; }
        
        /* Modern Glass UI */
        .container { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; background: #000; border: 1px solid var(--border); position: relative; }
        header { padding: 20px; background: var(--glass); backdrop-filter: blur(15px); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 1000; }
        header h1 { font-size: 1.2rem; margin: 0; background: linear-gradient(to right, #60a5fa, #a855f7); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }

        .page { display: none; flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 100px; }
        .active { display: flex; flex-direction: column; animation: fadeIn 0.4s ease-out; }

        /* Home Page Styling */
        .welcome-banner { background: linear-gradient(135deg, #1e3a8a, #581c87); padding: 25px; border-radius: 20px; margin-bottom: 25px; box-shadow: 0 10px 25px rgba(0,0,0,0.5); }
        .welcome-banner h2 { margin: 0; font-size: 1.5rem; }
        .stats { display: flex; gap: 10px; margin-top: 15px; }
        .stat-card { background: rgba(0,0,0,0.3); padding: 10px 15px; border-radius: 12px; font-size: 12px; flex: 1; border: 1px solid var(--border); }

        /* Hacker Admin Dashboard */
        #hacker-panel { display: none; background: #000; border: 2px solid var(--danger); border-radius: 15px; padding: 15px; margin: 10px; flex-wrap: wrap; gap: 10px; box-shadow: 0 0 30px rgba(239, 68, 68, 0.3); position: absolute; top: 70px; left: 10px; right: 10px; z-index: 2000; }
        .hack-btn { flex: 1; min-width: 100px; padding: 12px; font-size: 10px; background: #111; border: 1px solid var(--danger); color: var(--danger); font-weight: bold; cursor: pointer; border-radius: 8px; text-transform: uppercase; transition: 0.2s; }
        .hack-btn:hover { background: var(--danger); color: white; }
        
        /* Chat UI */
        .chat-stream { display: flex; flex-direction: column; gap: 12px; padding-bottom: 20px; }
        .bubble { max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 14px; line-height: 1.5; position: relative; }
        .me { align-self: flex-end; background: #2563eb; color: white; border-bottom-right-radius: 4px; }
        .other { align-self: flex-start; background: #1f2937; border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .bubble b { display: block; font-size: 10px; opacity: 0.7; margin-bottom: 4px; text-transform: uppercase; }
        .bubble img { max-width: 100%; border-radius: 10px; margin-top: 8px; border: 1px solid rgba(255,255,255,0.1); }
        
        /* Trash button for Admin */
        .del-btn { position: absolute; top: -5px; right: -5px; background: #000; color: #ef4444; border: 1px solid #ef4444; width: 20px; height: 20px; border-radius: 50%; display: none; align-items: center; justify-content: center; font-size: 10px; cursor: pointer; }
        .bubble:hover .del-btn { display: flex; }

        /* Fixed Input Bar */
        .input-bar { position: fixed; bottom: 0; max-width: 480px; width: 100%; padding: 20px; background: linear-gradient(to top, #000, transparent); }
        .input-container { background: #111827; border: 1px solid var(--border); border-radius: 50px; padding: 8px 15px; display: flex; align-items: center; gap: 12px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        .input-container input { flex: 1; background: transparent; border: none; color: white; padding: 10px; font-size: 14px; }
        
        /* Scare Overlays */
        #scare-screen { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:99999; color: #00ff00; flex-direction:column; align-items:center; justify-content:center; font-family: 'Courier New', monospace; padding: 40px; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>

<div id="scare-screen">
    <i class="fa-solid fa-radiation" style="font-size: 80px; color: #ef4444; margin-bottom: 20px;"></i>
    <h2 id="scare-title">SYSTEM INTRUSION</h2>
    <div id="scare-logs" style="font-size: 12px; text-align: left;"></div>
</div>

<div class="container">
    <header>
        <h1>NAZIM OS v6.0</h1>
        <i class="fa-solid fa-fingerprint" style="color: var(--accent); font-size: 20px;"></i>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align: center; margin-top: 100px;">
            <div style="font-size: 60px; margin-bottom: 20px;">🛡️</div>
            <h2 style="letter-spacing: 2px;">SECURE ACCESS</h2>
            <input type="text" id="userInput" placeholder="Enter Codename" style="width: 80%; background: #111; border: 1px solid #333; color: white; padding: 15px; border-radius: 15px; text-align: center; margin-top: 30px;">
            <button onclick="startSystem()" style="width: 80%; padding: 16px; background: var(--accent); color: white; border: none; border-radius: 15px; font-weight: bold; margin-top: 20px; cursor: pointer;">CONNECT TO GRID</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div class="welcome-banner">
            <h2 id="welcomeText">Welcome, Agent</h2>
            <div class="stats">
                <div class="stat-card">Status: <span style="color: #10b981;">Online</span></div>
                <div class="stat-card">Active Signals: <span id="onlineCount">0</span></div>
            </div>
        </div>
        <div onclick="openChat('global', false)" style="background: rgba(59, 130, 246, 0.1); border: 1px solid var(--accent); padding: 20px; border-radius: 20px; cursor: pointer; text-align: center; font-weight: bold; color: var(--accent);">
            <i class="fa-solid fa-globe-americas"></i> ENTER GLOBAL NETWORK
        </div>
        <p style="font-size: 12px; color: #6b7280; margin-top: 30px; letter-spacing: 1px;">DIRECT TARGETS:</p>
        <div id="userList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="hacker-panel">
            <button class="hack-btn" onclick="triggerHack('freeze')">Freeze All</button>
            <button class="hack-btn" onclick="triggerHack('scare')">Data Leak</button>
            <button class="hack-btn" onclick="triggerHack('warning')">Admin Warning</button>
            <button class="hack-btn" onclick="triggerHack('glitch')">Glitch Screen</button>
            <button class="hack-btn" onclick="triggerHack('restore')" style="border-color: #10b981; color: #10b981;">Restore</button>
            <button class="hack-btn" onclick="nukeDB()" style="background: var(--danger); color: white;">NUKE DATABASE</button>
        </div>

        <div id="chatStream" class="chat-stream"></div>

        <div class="input-bar">
            <div class="input-container">
                <i id="adminWand" class="fa-solid fa-wand-magic-sparkles" style="display:none; color: gold; cursor: pointer;" onclick="togglePanel()"></i>
                <label for="fileInp" style="cursor: pointer;"><i class="fa-solid fa-image" style="color: #9ca3af;"></i></label>
                <input type="file" id="fileInp" accept="image/*" style="display:none;" onchange="sendImage(this)">
                <input type="text" id="msgInp" placeholder="Secure message...">
                <i class="fa-solid fa-paper-plane" style="color: var(--accent); cursor: pointer;" onclick="sendText()"></i>
            </div>
        </div>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
let currentUser = null, roomID = "global", isPriv = false;
const ADMIN_ID = "Nazim";

window.startSystem = () => {
    currentUser = document.getElementById("userInput").value.trim();
    if(!currentUser) return;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("homePage").classList.add("active");
    document.getElementById("welcomeText").innerText = `Welcome, ${currentUser} ${currentUser === ADMIN_ID ? '👑' : '🎖️'}`;
    
    if(currentUser === ADMIN_ID) document.getElementById("adminWand").style.display="block";

    // Power Listener (The Trap)
    onValue(ref(db, "traps/" + currentUser), snap => {
        const t = snap.val();
        const sc = document.getElementById("scare-screen");
        if(t === "freeze") { sc.style.display="flex"; document.getElementById("scare-title").innerText="SYSTEM TERMINATED"; }
        if(t === "scare") { sc.style.display="flex"; runMatrix(); }
        if(t === "warning") { alert("⚠️ SECURITY ALERT: Unauthorized access detected. Your IP has been reported to local authorities."); }
        if(t === "glitch") { document.body.style.filter = "invert(1) contrast(200%)"; }
        if(t === "restore") { sc.style.display="none"; document.body.style.filter="none"; }
    });

    if(currentUser !== ADMIN_ID) set(ref(db, "online_signals/" + currentUser), true);
    syncSignals();
};

function runMatrix() {
    const lines = ["> INJECTING EXPLOIT...", "> ACCESSING CONTACTS...", "> EXFILTRATING GALLERY...", "> BYPASSING 2FA...", "> STATUS: COMPROMISED"];
    const lb = document.getElementById("scare-logs"); lb.innerHTML = "";
    let i = 0; setInterval(() => { if(i < lines.length) lb.innerHTML += lines[i++] + "<br>"; }, 700);
}

window.syncSignals = () => {
    onValue(ref(db, "online_signals"), snap => {
        const list = document.getElementById("userList"); list.innerHTML = "";
        let count = 0;
        snap.forEach(u => {
            count++;
            if(u.key !== currentUser) {
                let d = document.createElement("div"); d.style = "padding:15px; background: #111; border-radius:15px; margin-bottom:10px; border: 1px solid #222; cursor:pointer; display:flex; justify-content:space-between; align-items:center;";
                d.innerHTML = `<span>${u.key}</span> <i class="fa-solid fa-circle" style="color:#10b981; font-size:8px;"></i>`;
                d.onclick = () => openChat(u.key, true);
                list.appendChild(d);
            }
        });
        document.getElementById("onlineCount").innerText = count;
    });
};

window.openChat = (target, priv) => {
    roomID = priv ? [currentUser, target].sort().join("--") : "global";
    isPriv = priv;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    loadMessages();
};

window.sendText = () => {
    const inp = document.getElementById("msgInp");
    if(!inp.value) return;
    if(currentUser === ADMIN_ID && inp.value.startsWith("/as ")) {
        const p = inp.value.split(" ");
        push(ref(db, "messages/global"), { s: p[1], t: inp.value.replace("/as "+p[1]+" ", "") });
    } else {
        push(ref(db, isPriv ? "messages/priv/" + roomID : "messages/global"), { s: currentUser, t: inp.value });
    }
    inp.value = "";
};

window.sendImage = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => {
        push(ref(db, isPriv ? "messages/priv/" + roomID : "messages/global"), { s: currentUser, img: e.target.result });
    };
    reader.readAsDataURL(input.files[0]);
};

function loadMessages() {
    const path = isPriv ? "messages/priv/" + roomID : "messages/global";
    onValue(ref(db, path), snap => {
        const stream = document.getElementById("chatStream"); stream.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `bubble ${d.s === currentUser ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.s}</b> ${d.t ? d.t : `<img src="${d.img}">`} 
            ${(currentUser === ADMIN_ID) ? `<span class="del-btn" onclick="remove(ref(db,'${path}/${m.key}'))">✕</span>` : ''}`;
            stream.appendChild(div);
        });
        stream.parentElement.scrollTop = stream.parentElement.scrollHeight;
    });
}

window.togglePanel = () => { const p = document.getElementById("hacker-panel"); p.style.display = p.style.display === "flex" ? "none" : "flex"; };
window.triggerHack = (t) => { onValue(ref(db, "online_signals"), snap => { snap.forEach(u => { if(u.key !== ADMIN_ID) set(ref(db, "traps/" + u.key), t); }); }, {onlyOnce: true}); };
window.nukeDB = () => { if(confirm("☢️ ERASE GRID?")) remove(ref(db)); };
document.getElementById("msgInp").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendText(); });
</script>
</body>
</html>
