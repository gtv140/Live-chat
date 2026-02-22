<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Apex | Master</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --p: #00e5ff; --s: #0077ff; --bg: #030508;
            --card: rgba(22, 28, 38, 0.95); --border: rgba(0, 229, 255, 0.2);
            --text: #ffffff;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; border: 1px solid var(--border); }

        header { padding: 15px 20px; background: rgba(3,5,8,0.9); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 1000; }
        header h1 { font-size: 18px; font-weight: 800; margin: 0; color: var(--p); text-shadow: 0 0 10px var(--p); }

        /* VIEWPORT & SCROLLING FIX */
        .viewport { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; padding-bottom: 180px; scroll-behavior: smooth; }
        .view { display: none; width: 100%; }
        .view.active { display: flex; flex-direction: column; }

        /* DASHBOARD CARDS */
        .dash-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 20px; }
        .stat-card { background: var(--card); padding: 20px; border-radius: 20px; border: 1px solid var(--border); text-align: center; }
        .stat-card i { font-size: 24px; color: var(--p); margin-bottom: 10px; display: block; }
        .stat-card b { font-size: 20px; display: block; }

        /* CHAT BUBBLES */
        .msg-container { display: flex; flex-direction: column; width: 100%; margin-bottom: 25px; clear: both; }
        .msg-container.me { align-items: flex-end; }
        .msg-container.them { align-items: flex-start; }
        .bubble { padding: 12px 16px; border-radius: 22px; font-size: 15px; max-width: 80%; position: relative; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.4); }
        .me .bubble { background: linear-gradient(135deg, var(--p), var(--s)); color: #000; border-bottom-right-radius: 4px; font-weight: 600; }
        .them .bubble { background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .reaction-chip { position: absolute; bottom: -12px; background: #1a212c; border: 1px solid var(--p); border-radius: 10px; padding: 2px 6px; font-size: 12px; }
        .me .reaction-chip { right: 8px; }
        .them .reaction-chip { left: 8px; }

        /* INPUT DOCK (FIXED) */
        .input-dock { position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%); width: 92%; background: #1a212c; padding: 12px 18px; border-radius: 40px; border: 1px solid var(--border); display: flex; align-items: center; gap: 12px; z-index: 2000; box-shadow: 0 0 20px var(--p); }
        .input-dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 16px; }

        /* REACTION POPUP */
        #reactionMenu { position: fixed; display: none; background: #1a212c; border: 1px solid var(--p); border-radius: 30px; padding: 10px; gap: 15px; z-index: 3000; box-shadow: 0 0 20px var(--p); }
        .emoji-opt { font-size: 24px; cursor: pointer; transition: 0.2s; }
        .emoji-opt:hover { transform: scale(1.3); }

        /* NAVIGATION */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: #030508; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-link { color: #64748b; font-size: 11px; text-align: center; transition: 0.3s; }
        .nav-link.active { color: var(--p); text-shadow: 0 0 8px var(--p); }
        .nav-link i { font-size: 22px; display: block; margin-bottom: 4px; }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1 id="headTitle">INFINITY APEX</h1>
        <i class="fa-solid fa-bell icon-btn" style="color:var(--p)"></i>
    </header>

    <div class="viewport" id="scrollArea">
        <div id="v-home" class="view active">
            <h2>System Status</h2>
            <div class="dash-grid">
                <div class="stat-card"><i class="fa-solid fa-users-viewfinder"></i><b id="activeCount">0</b><span>Online</span></div>
                <div class="stat-card"><i class="fa-solid fa-chart-line"></i><b>1.2k</b><span>Syncs</span></div>
                <div class="stat-card"><i class="fa-solid fa-shield-halved"></i><b>SSL</b><span>Secure</span></div>
                <div class="stat-card"><i class="fa-solid fa-bolt"></i><b>24ms</b><span>Ping</span></div>
            </div>
            <h3 style="margin-top:30px;">Available Nodes</h3>
            <div id="uList"></div>
        </div>

        <div id="v-chat" class="view">
            <div id="chatBox"></div>
        </div>
    </div>

    <div id="reactionMenu">
        <span class="emoji-opt" onclick="react('❤️')">❤️</span>
        <span class="emoji-opt" onclick="react('😂')">😂</span>
        <span class="emoji-opt" onclick="react('🔥')">🔥</span>
        <span class="emoji-opt" onclick="react('👍')">👍</span>
    </div>

    <div class="input-dock" id="dock" style="display:none;">
        <i class="fa-solid fa-plus icon-btn" style="color:var(--p)"></i>
        <input type="text" id="mInp" placeholder="Type data..." onkeydown="if(event.key==='Enter') send()">
        <i class="fa-solid fa-paper-plane" onclick="send()" style="color:var(--p); cursor:pointer;"></i>
    </div>

    <nav id="bNav">
        <div class="nav-link active" id="hBtn" onclick="nav('home', 'hBtn')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-link" id="cBtn" onclick="nav('chat', 'cBtn')"><i class="fa-solid fa-message"></i><span>Chats</span></div>
        <div class="nav-link" onclick="location.reload()"><i class="fa-solid fa-rotate"></i><span>Sync</span></div>
        <div class="nav-link" onclick="localStorage.clear(); location.reload();"><i class="fa-solid fa-power-off"></i><span>Exit</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("apex_master_user") || "Node_" + Math.floor(Math.random()*999);
localStorage.setItem("apex_master_user", user);
let room = "Global_Lobby";
let currentMsgId = null;

window.nav = (vId, bId) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + vId).classList.add("active");
    document.querySelectorAll('.nav-link').forEach(n => n.classList.remove('active'));
    document.getElementById(bId).classList.add('active');
    document.getElementById("dock").style.display = (vId === 'chat' ? 'flex' : 'none');
    if(vId === 'chat') sync();
};

onValue(ref(db, "active_nodes"), snap => {
    const uList = document.getElementById("uList");
    uList.innerHTML = "";
    let count = 0;
    snap.forEach(u => {
        count++;
        if(u.key !== user) {
            const div = document.createElement("div");
            div.style = "background:var(--card); padding:18px; border-radius:20px; margin-bottom:12px; border:1px solid var(--border); cursor:pointer; display:flex; justify-content:space-between;";
            div.innerHTML = `<span><b>${u.key}</b></span> <i class="fa-solid fa-circle" style="color:#00ff00; font-size:10px;"></i>`;
            div.onclick = () => { room = [user, u.key].sort().join("_"); document.getElementById("headTitle").innerText = u.key; nav('chat', 'cBtn'); };
            uList.appendChild(div);
        }
    });
    document.getElementById("activeCount").innerText = count;
});
set(ref(db, "active_nodes/" + user), true);

function sync() {
    onValue(ref(db, "master_msgs/" + room), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const cont = document.createElement("div");
            cont.className = "msg-container " + (d.from === user ? "me" : "them");
            cont.innerHTML = `
                <div class="bubble" onclick="openReactions(event, '${m.key}')">
                    ${d.text}
                    ${d.reaction ? `<div class="reaction-chip">${d.reaction}</div>` : ''}
                </div>
                <div style="font-size:9px; opacity:0.4; margin-top:5px;">${d.from}</div>
            `;
            box.appendChild(cont);
        });
        const sc = document.getElementById('scrollArea');
        sc.scrollTop = sc.scrollHeight;
    });
}

window.openReactions = (e, id) => {
    currentMsgId = id;
    const menu = document.getElementById("reactionMenu");
    menu.style.display = "flex";
    menu.style.top = (e.clientY - 60) + "px";
    menu.style.left = "50%";
    menu.style.transform = "translateX(-50%)";
};

window.react = (emoji) => {
    if(currentMsgId) {
        update(ref(db, `master_msgs/${room}/${currentMsgId}`), { reaction: emoji });
        document.getElementById("reactionMenu").style.display = "none";
    }
};

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    push(ref(db, "master_msgs/" + room), { from: user, text: i.value });
    i.value = "";
};
</script>
</body>
</html>
