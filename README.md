<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | v5.0</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #0d1117; --primary: #58a6ff; --danger: #ff4b4b; --green: #238636; --border: rgba(255, 255, 255, 0.1); }
        body { margin: 0; font-family: 'Inter', sans-serif; background: var(--bg); color: #c9d1d9; overflow: hidden; transition: 0.3s; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; background: #010409; }
        
        header { padding: 18px; background: rgba(22, 27, 34, 0.95); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(12px); z-index: 1000; }
        header h1 { margin: 0; font-size: 1.1rem; font-weight: 700; color: var(--primary); }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; }
        .active { display: block; animation: slideUp 0.3s ease; }

        /* SHAITANI DASHBOARD */
        #nazim-dashboard { display: none; background: #000; border: 1px solid var(--danger); padding: 12px; margin: 10px; border-radius: 12px; flex-wrap: wrap; gap: 6px; box-shadow: 0 0 20px rgba(255, 75, 75, 0.4); }
        .pwr-btn { flex: 1; min-width: 80px; padding: 8px; font-size: 9px; background: #161b22; border: 1px solid var(--danger); color: var(--danger); cursor: pointer; font-weight: bold; border-radius: 6px; text-transform: uppercase; }
        .pwr-btn:hover { background: var(--danger); color: white; }

        /* Message Bubbles */
        .msg { position: relative; padding: 12px; border-radius: 18px; max-width: 75%; font-size: 14px; margin-bottom: 10px; word-wrap: break-word; }
        .msg.me { align-self: flex-end; background: var(--green); color: white; border-bottom-right-radius: 2px; }
        .msg.other { align-self: flex-start; background: #21262d; border: 1px solid var(--border); border-bottom-left-radius: 2px; }
        .msg img { max-width: 100%; border-radius: 10px; margin-top: 5px; }
        .del-msg { position: absolute; top: -5px; right: -5px; background: var(--danger); width: 18px; height: 18px; border-radius: 50%; display: none; align-items: center; justify-content: center; cursor: pointer; font-size: 10px; }
        .msg:hover .del-msg { display: flex; }

        .typing-box { font-size: 11px; color: var(--primary); margin-bottom: 10px; font-style: italic; height: 15px; }

        .input-wrap { position: absolute; bottom: 85px; width: 100%; padding: 0 15px; box-sizing: border-box; }
        .modern-input { background: #161b22; border: 1px solid var(--border); border-radius: 30px; display: flex; align-items: center; padding: 5px 15px; }
        .modern-input input { flex: 1; background: transparent; border: none; color: white; padding: 10px; outline: none; }

        #system-overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000; z-index:99999; color: #00ff41; flex-direction:column; align-items:center; justify-content:center; text-align:center; font-family: 'Courier New', monospace; }
        .glitch-flash { animation: flash 0.1s infinite; }
        @keyframes flash { 0%, 100% { background: #000; } 50% { background: #f00; } }
    </style>
</head>
<body>

<div id="system-overlay">
    <i class="fa-solid fa-ghost" style="font-size: 60px; color: red;"></i>
    <h2 id="ov-text">FATAL ERROR</h2>
</div>

<div class="app">
    <header><h1>LIVE CONNECT</h1><i id="nav-icon" class="fa-solid fa-bolt"></i></header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; margin-top:100px;">
            <input type="text" id="userInput" class="modern-input" placeholder="Codename..." style="margin: 20px auto; width: 80%;">
            <button onclick="initApp()" style="width:80%; padding:15px; background:var(--primary); color:white; border:none; border-radius:30px; font-weight:bold; cursor:pointer;">CONNECT</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div onclick="openChat('global', false)" style="background:var(--green); padding:20px; border-radius:15px; cursor:pointer; font-weight:bold;">🌐 GLOBAL HUB</div>
        <p style="font-size: 11px; color: #8b949e; margin-top: 20px;">ACTIVE TARGETS:</p>
        <div id="userList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="nazim-dashboard">
            <button class="pwr-btn" onclick="sendHack('freeze')">Freeze</button>
            <button class="pwr-btn" onclick="sendHack('horror')">Horror</button>
            <button class="pwr-btn" onclick="sendHack('glitch')">Glitch</button>
            <button class="pwr-btn" onclick="sendHack('type_trap')">Type Trap</button>
            <button class="pwr-btn" onclick="sendHack('restore')" style="color:lime">Restore</button>
            <button class="pwr-btn" onclick="nuke()" style="background:red; color:white;">NUKE</button>
        </div>

        <div id="typingArea" class="typing-box"></div>
        <div id="msgStream" style="display:flex; flex-direction:column;"></div>
        
        <div class="input-wrap">
            <div class="modern-input">
                <i id="admin-wand" class="fa-solid fa-wand-magic-sparkles" style="display:none; color:gold; cursor:pointer; margin-right:10px;" onclick="togglePanel()"></i>
                <label for="imgInp"><i class="fa-solid fa-image" style="color:#8b949e; cursor:pointer;"></i></label>
                <input type="file" id="imgInp" accept="image/*" style="display:none;" onchange="uploadImg(this)">
                <input type="text" id="msgInp" placeholder="Type message..." oninput="isTyping()">
                <i class="fa-solid fa-paper-plane" style="color:var(--primary); cursor:pointer; margin-left:10px;" onclick="sendMsg()"></i>
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
let user = null, room = "global", priv = false;
const ADMIN = "Nazim";

window.initApp = () => {
    user = document.getElementById("userInput").value.trim();
    if(!user) return;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("homePage").classList.add("active");
    if(user === ADMIN) document.getElementById("admin-wand").style.display="block";

    onValue(ref(db, "traps/" + user), snap => {
        const t = snap.val();
        const ov = document.getElementById("system-overlay");
        if(t === "freeze") { ov.style.display="flex"; document.getElementById("ov-text").innerText="DEVICE LOCKED"; }
        if(t === "horror") { ov.style.display="flex"; document.getElementById("ov-text").innerText="I AM WATCHING YOU"; new Audio('https://www.soundjay.com/nature/sounds/rain-01.mp3').play(); }
        if(t === "glitch") { document.body.classList.add("glitch-flash"); }
        if(t === "type_trap") { isTyping(true); }
        if(t === "restore") { ov.style.display="none"; document.body.classList.remove("glitch-flash"); }
    });

    if(user !== ADMIN) set(ref(db, "active_users/" + user), true);
    syncUsers();
};

window.isTyping = (forced = false) => {
    set(ref(db, "typing/" + user), forced ? "is typing forever..." : "is typing...");
    setTimeout(() => set(ref(db, "typing/" + user), ""), 3000);
};

onValue(ref(db, "typing"), snap => {
    let tText = "";
    snap.forEach(u => { if(u.key !== user && u.val()) tText = u.key + " " + u.val(); });
    document.getElementById("typingArea").innerText = tText;
});

window.uploadImg = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => {
        push(ref(db, priv ? "streams/priv/" + room : "streams/global"), { s: user, img: e.target.result });
    };
    reader.readAsDataURL(input.files[0]);
};

window.openChat = (target, isPriv) => {
    room = isPriv ? [user, target].sort().join("::") : "global";
    priv = isPriv;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    loadMsgs();
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInp");
    if(!inp.value) return;
    if(user === ADMIN && inp.value.startsWith("/as ")) {
        const p = inp.value.split(" ");
        push(ref(db, "streams/global"), { s: p[1], t: inp.value.replace("/as "+p[1]+" ", "") });
    } else {
        push(ref(db, priv ? "streams/priv/" + room : "streams/global"), { s: user, t: inp.value });
    }
    inp.value = "";
};

function loadMsgs() {
    const path = priv ? "streams/priv/" + room : "streams/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("msgStream"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `msg ${d.s === user ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.s}</b>${d.t ? d.t : `<img src="${d.img}">`} ${(user === ADMIN) ? `<span class="del-msg" onclick="remove(ref(db,'${path}/${m.key}'))">✕</span>` : ''}`;
            box.appendChild(div);
        });
        box.parentElement.scrollTop = box.parentElement.scrollHeight;
    });
}

function syncUsers() {
    onValue(ref(db, "active_users"), snap => {
        const list = document.getElementById("userList"); list.innerHTML = "";
        snap.forEach(u => { if(u.key !== user) {
            let d = document.createElement("div"); d.style = "padding:15px; background:#161b22; margin-top:10px; border-radius:10px; cursor:pointer;";
            d.innerHTML = `${u.key} <i class="fa-solid fa-circle" style="float:right; color:lime; font-size:10px;"></i>`;
            d.onclick = () => openChat(u.key, true);
            list.appendChild(d);
        }});
    });
}

window.togglePanel = () => { let p = document.getElementById("nazim-dashboard"); p.style.display = p.style.display === "flex" ? "none" : "flex"; };
window.sendHack = (type) => { onValue(ref(db, "active_users"), snap => { snap.forEach(u => { if(u.key !== ADMIN) set(ref(db, "traps/" + u.key), type); }); }, {onlyOnce: true}); };
window.nuke = () => { if(confirm("NUKE ALL?")) remove(ref(db)); };
document.getElementById("msgInp").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendMsg(); });
</script>
</body>
</html>
