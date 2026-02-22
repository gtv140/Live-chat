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
            --card: rgba(22, 27, 34, 1); --border: rgba(255, 255, 255, 0.1);
            --text: #ffffff;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

        header { padding: 15px 20px; background: #030508; border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 1000; }
        header h1 { font-size: 18px; font-weight: 800; margin: 0; color: var(--p); }

        /* FIXED OVERLAP: Added heavy bottom padding */
        .viewport { 
            flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column;
            padding-bottom: 180px; 
            scroll-behavior: smooth;
        }

        /* MESSAGE STYLING */
        .msg-container { display: flex; flex-direction: column; width: 100%; margin-bottom: 25px; position: relative; clear: both; }
        .msg-container.me { align-items: flex-end; }
        .msg-container.them { align-items: flex-start; }

        .bubble { 
            padding: 12px 16px; border-radius: 22px; font-size: 15px; line-height: 1.4; 
            max-width: 80%; word-wrap: break-word; position: relative; cursor: pointer;
            box-shadow: 0 4px 10px rgba(0,0,0,0.3); transition: 0.2s;
        }
        .me .bubble { background: linear-gradient(135deg, var(--p), var(--s)); color: #000; border-bottom-right-radius: 4px; font-weight: 600; }
        .them .bubble { background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; }

        /* REACTION CHIP */
        .reaction-chip {
            position: absolute; bottom: -12px; background: #252d38; 
            border: 1px solid var(--border); border-radius: 12px; padding: 2px 6px;
            font-size: 12px; box-shadow: 0 4px 8px rgba(0,0,0,0.5);
        }
        .me .reaction-chip { right: 10px; }
        .them .reaction-chip { left: 10px; }

        .msg-info { font-size: 10px; opacity: 0.5; margin-top: 15px; }

        /* INPUT DOCK FIXED AT BOTTOM */
        .input-dock {
            position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%);
            width: 94%; max-width: 440px; background: #1a212c; 
            padding: 10px 15px; border-radius: 35px; border: 1px solid var(--border);
            display: flex; align-items: center; gap: 10px; z-index: 2000;
        }
        .input-dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 16px; }

        /* REACTION MENU */
        #reactionMenu {
            position: fixed; display: none; background: #1a212c; border: 1px solid var(--p);
            border-radius: 30px; padding: 8px 15px; gap: 12px; z-index: 3000;
        }
        .emoji-opt { font-size: 22px; cursor: pointer; }

        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: #030508; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-link { color: #64748b; font-size: 11px; text-align: center; }
        .nav-link.active { color: var(--p); }
        .nav-link i { font-size: 22px; display: block; margin-bottom: 2px; }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1 id="headTitle">INFINITY APEX</h1>
        <i class="fa-solid fa-power-off" onclick="logout()" style="color:#ff4444"></i>
    </header>

    <div class="viewport" id="scrollArea">
        <div id="v-home" class="view active">
            <h3 style="margin-left:10px;">Connections</h3>
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
        <i class="fa-solid fa-camera icon-btn" style="color:var(--p)"></i>
        <input type="text" id="mInp" placeholder="Message..." onkeydown="if(event.key==='Enter') send()">
        <i class="fa-solid fa-paper-plane" onclick="send()" style="color:var(--p); font-size:20px; cursor:pointer;"></i>
    </div>

    <nav id="bNav">
        <div class="nav-link active" id="hBtn" onclick="nav('home', 'hBtn')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-link" id="cBtn" onclick="nav('chat', 'cBtn')"><i class="fa-solid fa-comment"></i><span>Chats</span></div>
        <div class="nav-link" onclick="logout()"><i class="fa-solid fa-user"></i><span>Profile</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("apex_final_user") || "User_" + Math.floor(Math.random()*99);
localStorage.setItem("apex_final_user", user);
let room = "Global";
let currentMsgId = null;

window.nav = (vId, bId) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + vId).classList.add("active");
    document.querySelectorAll('.nav-link').forEach(n => n.classList.remove('active'));
    document.getElementById(bId).classList.add('active');
    document.getElementById("dock").style.display = (vId === 'chat' ? 'flex' : 'none');
    if(vId === 'chat') sync();
};

onValue(ref(db, "active_users"), snap => {
    const list = document.getElementById("uList");
    list.innerHTML = "";
    snap.forEach(u => {
        if(u.key !== user) {
            const div = document.createElement("div");
            div.style = "background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px; cursor:pointer; border: 1px solid var(--border);";
            div.innerHTML = `<b>${u.key}</b> <span style="color:var(--p); float:right;">● Online</span>`;
            div.onclick = () => { room = [user, u.key].sort().join("_"); document.getElementById("headTitle").innerText = u.key; nav('chat', 'cBtn'); };
            list.appendChild(div);
        }
    });
});
set(ref(db, "active_users/" + user), true);

function sync() {
    onValue(ref(db, "msgs/" + room), snap => {
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
                <div class="msg-info">${d.from}</div>
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
        update(ref(db, `msgs/${room}/${currentMsgId}`), { reaction: emoji });
        document.getElementById("reactionMenu").style.display = "none";
    }
};

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    push(ref(db, "msgs/" + room), { from: user, text: i.value });
    i.value = "";
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
