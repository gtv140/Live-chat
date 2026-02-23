<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #05070a; --neon-blue: #00d2ff; --neon-red: #ff0055; --neon-green: #00ff41; --glass: rgba(10, 15, 25, 0.9); }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: #e6edf3; overflow: hidden; transition: 0.1s; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; border: 1px solid #1b1f23; }
        
        header { padding: 15px; background: var(--glass); border-bottom: 1px solid #30363d; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(10px); z-index: 1000; }
        header h1 { margin: 0; font-size: 1.2rem; background: linear-gradient(90deg, var(--neon-blue), #bc8cff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; }
        .active { display: block; animation: fadeIn 0.3s; }

        /* Nazim's Secret Command Center */
        #hacker-console { display: none; background: #000; border: 2px solid var(--neon-red); padding: 10px; margin: 10px; border-radius: 10px; flex-wrap: wrap; gap: 5px; box-shadow: 0 0 20px rgba(255, 0, 85, 0.4); z-index: 2000; }
        .pwr-btn { flex: 1; min-width: 80px; padding: 8px; font-size: 9px; background: #000; border: 1px solid var(--neon-red); color: var(--neon-red); cursor: pointer; font-weight: bold; text-transform: uppercase; }
        .pwr-btn:hover { background: var(--neon-red); color: white; box-shadow: 0 0 10px var(--neon-red); }

        /* Chat Styles */
        .msg { position: relative; padding: 10px 15px; border-radius: 18px; max-width: 80%; margin-bottom: 10px; font-size: 14px; border: 1px solid #30363d; animation: pop 0.2s; }
        .msg.me { align-self: flex-end; background: #238636; border-bottom-right-radius: 2px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }
        .del-btn { position: absolute; top: -5px; right: -5px; background: red; color: white; width: 18px; height: 18px; border-radius: 50%; font-size: 10px; display: none; align-items: center; justify-content: center; cursor: pointer; }
        .msg:hover .del-btn { display: flex; }

        /* Fake UI Overlays */
        #horror-overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:10000; color:red; flex-direction:column; align-items:center; justify-content:center; }
        .flash-bomb { animation: flash 0.1s infinite; }
        @keyframes flash { 0% { background: red; } 50% { background: blue; } 100% { background: yellow; } }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body id="mainBody">

<div id="horror-overlay">
    <i class="fa-solid fa-ghost" style="font-size: 100px; margin-bottom: 20px;"></i>
    <h1 id="errorMsg">SYSTEM CORRUPTED</h1>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <i class="fa-solid fa-fingerprint" style="color:var(--neon-blue)"></i>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align: center; margin-top: 80px;">
            <i class="fa-solid fa-user-shield" style="font-size: 60px; color: var(--neon-blue);"></i>
            <h3>Encrypted Login</h3>
            <input type="text" id="usernameInput" style="width:100%; background:#161b22; border:1px solid #30363d; color:white; padding:15px; border-radius:25px; text-align:center; margin-top:20px;" placeholder="Codename">
            <button onclick="login()" style="width:100%; padding:15px; margin-top:20px; background:var(--neon-blue); color:black; border:none; border-radius:25px; font-weight:bold; cursor:pointer">INITIALIZE</button>
        </div>
    </div>

    <div id="home" class="page">
        <button onclick="startChat('global', false)" style="width:100%; padding:20px; background:var(--glass); border:1px solid var(--neon-blue); color:white; border-radius:15px; cursor:pointer; margin-bottom:20px;">GLOBAL ENCRYPTED CHAT</button>
        <p style="font-size: 12px; color: #8b949e;">CONNECTED DEVICES:</p>
        <div id="userList"></div>
    </div>

    <div id="chat" class="page">
        <div id="hacker-console">
            <button class="pwr-btn" onclick="sendPower('freeze')">Freeze</button>
            <button class="pwr-btn" onclick="sendPower('horror')">Horror</button>
            <button class="pwr-btn" onclick="sendPower('flash')">Flash Bomb</button>
            <button class="pwr-btn" onclick="sendPower('battery')">Fake 1%</button>
            <button class="pwr-btn" onclick="sendPower('unfreeze')" style="color:var(--neon-green); border-color:var(--neon-green)">Reset</button>
            <button class="pwr-btn" onclick="nukeChat()" style="background:red; color:white;">NUKE ALL</button>
        </div>
        <div id="chatBox" style="display:flex; flex-direction:column;"></div>
        <div style="position: absolute; bottom: 80px; width: 100%; padding: 15px; box-sizing: border-box;">
            <input type="text" id="msgInput" style="width:100%; background:#161b22; border:1px solid #30363d; color:white; padding:12px 20px; border-radius:25px; outline:none;" placeholder="Transmit message...">
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

let currentUser = null, curChat = "global", isPrivate = false;
const adminName = "Nazim"; 

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("home").classList.add("active");
    if(currentUser === adminName) document.getElementById("hacker-console").style.display="flex";

    // POWER LISTENER (The Trap)
    onValue(ref(db, "powers/" + currentUser), snap => {
        const p = snap.val();
        const overlay = document.getElementById("horror-overlay");
        if(p === "freeze") { overlay.style.display="flex"; document.getElementById("errorMsg").innerText="SYSTEM LOCKED"; }
        if(p === "horror") { 
            overlay.style.display="flex"; 
            new Audio('https://www.soundjay.com/nature/sounds/rain-01.mp3').play(); // Replace with scary mp3 link
            document.getElementById("errorMsg").innerText="I SEE YOU..."; 
        }
        if(p === "flash") { document.body.classList.add("flash-bomb"); }
        if(p === "battery") { alert("Critical Error: Battery 1%. Shutting down..."); overlay.style.display="flex"; document.getElementById("errorMsg").innerText="LOW BATTERY - SHUTDOWN"; }
        if(p === "unfreeze") { overlay.style.display="none"; document.body.classList.remove("flash-bomb"); }
    });

    if(currentUser !== adminName) set(ref(db, "users/" + currentUser), { name: currentUser, online: true });
    syncUsers();
};

window.sendPower = (act) => {
    onValue(ref(db, "users"), snap => {
        snap.forEach(u => { if(u.key !== adminName) set(ref(db, "powers/" + u.key), act); });
    }, {onlyOnce: true});
};

window.startChat = (target, priv) => {
    curChat = priv ? [currentUser, target].sort().join("_") : "global";
    isPrivate = priv;
    document.getElementById("home").classList.remove("active");
    document.getElementById("chat").classList.add("active");
    loadChat();
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value.trim()) return;

    if(currentUser === adminName && inp.value.startsWith("/as ")) {
        const p = inp.value.split(" ");
        const victim = p[1];
        const msg = inp.value.replace("/as "+victim+" ", "");
        push(ref(db, "messages/global"), { from: victim, text: msg });
        inp.value = ""; return;
    }

    const path = isPrivate ? "private/" + curChat : "messages/global";
    push(ref(db, path), { from: currentUser, text: inp.value });
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
                d.style = "background:#161b22; padding:15px; border-radius:10px; margin-bottom:10px; cursor:pointer; display:flex; justify-content:space-between; border:1px solid #30363d;";
                d.innerHTML = `<span>${u.key}</span> <i class="fa-solid fa-bolt" style="color:var(--neon-green)"></i>`;
                d.onclick = () => startChat(u.key, true);
                list.appendChild(d);
            }
        });
    });
}

window.nukeChat = () => { if(confirm("NUKE ALL DATA?")) remove(ref(db)); };

</script>
</body>
</html>
