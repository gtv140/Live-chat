<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Apex | Pro</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --p: #00e5ff; --s: #0077ff; --bg: #030508;
            --card: rgba(22, 28, 38, 0.95); --border: rgba(0, 229, 255, 0.2);
            --text: #ffffff;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100vh; background: var(--bg); color: var(--text); overflow: hidden; }

        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; background: var(--bg); }

        header { padding: 15px 20px; background: #030508; border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 1000; }
        header h1 { font-size: 18px; font-weight: 800; margin: 0; color: var(--p); text-shadow: 0 0 10px var(--p); }

        /* SCROLL AREA OPTIMIZATION */
        .viewport { 
            flex: 1; 
            overflow-y: auto; 
            padding: 15px; 
            display: flex; 
            flex-direction: column; 
            gap: 10px;
            padding-bottom: 180px; /* Space for dock and nav */
            -webkit-overflow-scrolling: touch;
        }

        .view { display: none; width: 100%; }
        .view.active { display: flex; flex-direction: column; }

        /* MESSAGES (ANTI-OVERLAP) */
        .msg-node { display: flex; flex-direction: column; width: 100%; margin-bottom: 15px; clear: both; }
        .msg-node.me { align-items: flex-end; }
        .msg-node.them { align-items: flex-start; }

        .bubble { 
            padding: 12px 16px; border-radius: 20px; font-size: 15px; line-height: 1.4; 
            max-width: 80%; word-wrap: break-word; position: relative;
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
        }
        .me .bubble { background: linear-gradient(135deg, var(--p), var(--s)); color: #000; border-bottom-right-radius: 4px; font-weight: 600; }
        .them .bubble { background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; }

        .reaction-chip { position: absolute; bottom: -10px; background: #1a212c; border: 1px solid var(--p); border-radius: 12px; padding: 2px 6px; font-size: 11px; }
        .me .reaction-chip { right: 5px; }
        .them .reaction-chip { left: 5px; }

        /* DASHBOARD GRID */
        .dash-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-top: 15px; }
        .stat-card { background: var(--card); padding: 15px; border-radius: 15px; border: 1px solid var(--border); text-align: center; }
        .stat-card i { font-size: 20px; color: var(--p); margin-bottom: 8px; display: block; }

        /* FLOATING INPUT DOCK */
        .dock { 
            position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%); 
            width: 92%; max-width: 440px; background: #1a212c; padding: 10px 18px; 
            border-radius: 40px; border: 1px solid var(--border); display: flex; 
            align-items: center; gap: 10px; z-index: 2000; box-shadow: 0 5px 20px rgba(0,0,0,0.5);
        }
        .dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 16px; }

        /* REACTION MENU */
        #reactMenu { position: fixed; display: none; background: #1a212c; border: 1px solid var(--p); border-radius: 30px; padding: 10px; gap: 15px; z-index: 3000; }
        .emoji-opt { font-size: 24px; cursor: pointer; transition: 0.2s; }

        /* NAV BAR */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: #030508; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-link { color: #64748b; font-size: 11px; text-align: center; cursor: pointer; }
        .nav-link.active { color: var(--p); }
        .nav-link i { font-size: 22px; display: block; margin-bottom: 3px; }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1 id="headTitle">INFINITY APEX</h1>
        <i class="fa-solid fa-bolt" style="color:var(--p)"></i>
    </header>

    <div class="viewport" id="scrollArea">
        <div id="v-home" class="view active">
            <h2>Dashboard</h2>
            <div class="dash-grid">
                <div class="stat-card"><i class="fa-solid fa-wifi"></i><b id="onCount">0</b><span>Online</span></div>
                <div class="stat-card"><i class="fa-solid fa-message"></i><b>Global</b><span>Active</span></div>
            </div>
            <h3 style="margin-top:20px;">Connections</h3>
            <div id="uList"></div>
        </div>
        <div id="v-chat" class="view">
            <div id="chatBox"></div>
        </div>
    </div>

    <div id="reactMenu">
        <span class="emoji-opt" onclick="react('❤️')">❤️</span>
        <span class="emoji-opt" onclick="react('😂')">😂</span>
        <span class="emoji-opt" onclick="react('🔥')">🔥</span>
        <span class="emoji-opt" onclick="react('👍')">👍</span>
    </div>

    <div class="dock" id="dock" style="display:none;">
        <i class="fa-solid fa-camera" style="color:var(--p)"></i>
        <input type="text" id="mInp" placeholder="Enter message..." onkeydown="if(event.key==='Enter') send()">
        <i class="fa-solid fa-paper-plane" onclick="send()" style="color:var(--p); cursor:pointer;"></i>
    </div>

    <nav id="bNav">
        <div class="nav-link active" id="hBtn" onclick="nav('home', 'hBtn')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-link" id="cBtn" onclick="nav('chat', 'cBtn')"><i class="fa-solid fa-comments"></i><span>Chats</span></div>
        <div class="nav-link" onclick="localStorage.clear(); location.reload();"><i class="fa-solid fa-power-off"></i><span>Exit</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("apex_v15") || "User_" + Math.floor(Math.random()*99);
localStorage.setItem("apex_v15", user);
let room = "Global";
let currentId = null;

// Handle Navigation
window.nav = (vId, bId) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + vId).classList.add("active");
    document.querySelectorAll('.nav-link').forEach(n => n.classList.remove('active'));
    document.getElementById(bId).classList.add('active');
    document.getElementById("dock").style.display = (vId === 'chat' ? 'flex' : 'none');
    if(vId === 'chat') { sync(); scrollToBottom(); }
};

// Sync Users
onValue(ref(db, "online"), snap => {
    const list = document.getElementById("uList");
    list.innerHTML = "";
    let count = 0;
    snap.forEach(u => {
        count++;
        if(u.key !== user) {
            const div = document.createElement("div");
            div.style = "background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px; border:1px solid var(--border); display:flex; justify-content:space-between; align-items:center;";
            div.innerHTML = `<b>${u.key}</b> <i class="fa-solid fa-circle" style="color:#00ff00; font-size:8px"></i>`;
            div.onclick = () => { room = [user, u.key].sort().join("_"); document.getElementById("headTitle").innerText = u.key; nav('chat', 'cBtn'); };
            list.appendChild(div);
        }
    });
    document.getElementById("onCount").innerText = count;
});
set(ref(db, "online/" + user), true);

// Sync Messages
function sync() {
    onValue(ref(db, "msgs_v15/" + room), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const node = document.createElement("div");
            node.className = "msg-node " + (d.from === user ? "me" : "them");
            node.innerHTML = `
                <div class="bubble" onclick="openReact(event, '${m.key}')">
                    ${d.text}
                    ${d.reaction ? `<div class="reaction-chip">${d.reaction}</div>` : ''}
                </div>
            `;
            box.appendChild(node);
        });
        scrollToBottom();
    });
}

function scrollToBottom() {
    const sc = document.getElementById('scrollArea');
    sc.scrollTop = sc.scrollHeight;
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    push(ref(db, "msgs_v15/" + room), { from: user, text: i.value });
    i.value = "";
    setTimeout(scrollToBottom, 100);
};

window.openReact = (e, id) => {
    currentId = id;
    const menu = document.getElementById("reactMenu");
    menu.style.display = "flex";
    menu.style.top = (e.clientY - 60) + "px";
    menu.style.left = "50%";
    menu.style.transform = "translateX(-50%)";
};

window.react = (emo) => {
    if(currentId) {
        update(ref(db, `msgs_v15/${room}/${currentId}`), { reaction: emo });
        document.getElementById("reactMenu").style.display = "none";
    }
};

// Close reaction on outside click
document.addEventListener('click', (e) => {
    if(!e.target.closest('.bubble') && !e.target.closest('#reactMenu')) {
        document.getElementById("reactMenu").style.display = "none";
    }
});
</script>
</body>
</html>
