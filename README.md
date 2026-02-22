<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect | Elite Messaging</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root {
  --p: #6b5bff; --a: #ff5c8d; --bg: #030303; --card: rgba(255,255,255,0.06);
  --text: #ffffff; --muted: #888; --border: rgba(255,255,255,0.1); --glass: rgba(0,0,0,0.85);
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; outline: none; }
body { margin: 0; background: var(--bg); color: var(--text); overflow: hidden; line-height: 1.6; }

/* Subtle Animated Background */
.mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 15% 15%, rgba(107,91,255,0.15) 0%, transparent 45%), radial-gradient(circle at 85% 85%, rgba(255,92,141,0.15) 0%, transparent 45%); filter: blur(30px); }

.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Modern Header */
header { padding: 55px 25px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(25px); background: var(--glass); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 22px; font-weight: 800; background: linear-gradient(90deg, #fff, var(--p)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; letter-spacing: -0.5px; }

/* Floating Nav */
nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 92%; height: 72px; background: rgba(15,15,15,0.8); backdrop-filter: blur(20px); border-radius: 28px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; box-shadow: 0 15px 40px rgba(0,0,0,0.7); }
.nav-btn { color: var(--muted); font-size: 22px; cursor: pointer; transition: 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
.nav-btn.active { color: var(--p); transform: translateY(-3px); text-shadow: 0 0 15px var(--p); }

/* Pages */
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; animation: fadeIn 0.4s ease; }
.page.active { display: block; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

/* Chat Polishing */
.chat-container { background: rgba(255,255,255,0.02); border-radius: 30px; border: 1px solid var(--border); height: 52vh; overflow-y: auto; padding: 18px; display: flex; flex-direction: column; gap: 15px; scrollbar-width: none; }
.bubble { padding: 14px 18px; border-radius: 22px; font-size: 14px; background: #1a1a1a; border: 1px solid var(--border); max-width: 82%; position: relative; }
.bubble.me { background: linear-gradient(135deg, var(--p), #4a3aff); border: none; align-self: flex-end; border-bottom-right-radius: 4px; }
.bubble.them { border-bottom-left-radius: 4px; }

/* Group Sender Identity */
.sender-tag { font-size: 11px; font-weight: 800; color: var(--a); margin-bottom: 4px; display: block; text-transform: uppercase; letter-spacing: 0.5px; opacity: 0.9; }

/* Dashboard Widgets */
.dash-card { background: linear-gradient(145deg, #181818, #0a0a0a); border: 1px solid var(--border); border-radius: 28px; padding: 22px; margin-bottom: 15px; box-shadow: 0 8px 20px rgba(0,0,0,0.3); }

/* Action Menu & Dock */
#actionMenu { position: fixed; background: #121212; padding: 12px; border-radius: 22px; display: none; flex-direction: column; gap: 10px; z-index: 10000; border: 1px solid var(--p); box-shadow: 0 15px 40px rgba(0,0,0,1); }
.dock-bar { display: flex; gap: 10px; align-items: center; background: var(--card); padding: 12px; border-radius: 25px; border: 1px solid var(--border); margin-top: 10px; }
.dock-input { flex: 1; background: transparent; border: none; color: #fff; font-size: 14px; }
.send-btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--p); color: #fff; cursor: pointer; box-shadow: 0 5px 15px rgba(107,91,255,0.3); }

/* Auth */
#auth { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; text-align: center; }
</style>
</head>
<body>

<div class="mesh"></div>

<div id="actionMenu">
    <div style="display:flex; gap:15px; font-size:22px; padding-bottom:8px; border-bottom:1px solid var(--border);">
        <span onclick="setReact('❤️')">❤️</span><span onclick="setReact('😂')">😂</span><span onclick="setReact('🔥')">🔥</span><span onclick="setReact('👍')">👍</span>
    </div>
    <div onclick="deleteMsg()" id="delOpt" style="color:var(--a); font-size:12px; font-weight:800; text-align:center; cursor:pointer; padding-top:5px;">UNSEND</div>
</div>

<div class="app">
  <header>
    <h1>Live Connect</h1>
    <div onclick="logout()" style="color:var(--muted); cursor:pointer;"><i class="fa-solid fa-power-off"></i></div>
  </header>

  <div id="auth">
    <div style="font-size: 50px; color: var(--p); margin-bottom: 15px;"><i class="fa-solid fa-circle-nodes"></i></div>
    <h2 style="margin: 0;">Welcome</h2>
    <p style="color: var(--muted); margin-bottom: 25px; font-size: 14px;">Connect with your world in real-time.</p>
    <input type="text" id="userInput" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--card); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:15px;" placeholder="Enter Username">
    <button onclick="login()" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--p); border:none; color:#fff; font-weight:800; cursor:pointer;">START CHATTING</button>
  </div>

  <div id="home" class="page active">
    <div class="dash-card" style="background: linear-gradient(135deg, var(--p), #4a3aff);">
        <h2 style="margin:0; font-size:28px;">Hello, <span id="uName">...</span></h2>
        <p style="opacity:0.9; margin-top:5px; font-size:14px;">Your private space is ready.</p>
    </div>
    <div style="display:grid; grid-template-columns:1fr 1fr; gap:15px;">
        <div class="dash-card" style="text-align:center;">
            <h3 id="onlineCount" style="margin:0; font-size:24px;">0</h3>
            <p style="margin:0; font-size:10px; color:var(--muted); font-weight:800;">LIVE NODES</p>
        </div>
        <div class="dash-card" style="text-align:center;">
            <h3 style="margin:0; font-size:24px; color:var(--a);">Active</h3>
            <p style="margin:0; font-size:10px; color:var(--muted); font-weight:800;">ENCRYPTION</p>
        </div>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatWith" style="font-size:11px; font-weight:800; color:var(--p); margin-bottom:12px; letter-spacing:1px;">SECURE CHANNEL</div>
    <div class="chat-container" id="chatBox"></div>
    <div id="replyBar" style="background:rgba(255,255,255,0.05); padding:10px 15px; border-radius:15px 15px 0 0; border:1px solid var(--border); display:none; justify-content:space-between; align-items:center;">
        <div id="replyContent" style="font-size:11px; color:var(--muted);"></div>
        <i class="fa-solid fa-xmark" onclick="cancelReply()"></i>
    </div>
    <div class="dock-bar">
      <label for="imgInp" style="color:var(--p); cursor:pointer; font-size:20px;"><i class="fa-solid fa-circle-plus"></i></label>
      <input type="file" id="imgInp" hidden onchange="sendImg(event)">
      <input id="msgInput" class="dock-input" placeholder="Message..."/>
      <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <div id="userList"></div>
    <h4 style="font-size:10px; color:var(--muted); letter-spacing:1px; margin:25px 0 12px; font-weight:800;">ACTIVE CHANNELS</h4>
    <div id="groupList"></div>
  </div>

  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-house"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></div>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
let curChat = "", isGroup = false, replyData = null, menuTarget = null;

if(user) { document.getElementById("auth").style.display = "none"; start(); }

window.login = () => {
    const n = document.getElementById("userInput").value.trim();
    if(n && n.toLowerCase() !== "undefined") { user = n; localStorage.setItem("lc_user", n); location.reload(); }
};

function start() {
    document.getElementById("uName").textContent = user;
    set(ref(db, "users/"+user), {name: user, online: true});
    onDisconnect(ref(db, "users/"+user)).update({online: false});
}

window.logout = () => { localStorage.removeItem("lc_user"); location.reload(); };

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
};

// Fixed: Clean list without 'undefined'
onValue(ref(db, "users"), snap => {
    const list = document.getElementById("userList"); list.innerHTML = "";
    let count = 0;
    snap.forEach(u => {
        const d = u.val();
        if(d && d.name && d.name !== "undefined" && d.online) {
            count++;
            if(u.key !== user) {
                const div = document.createElement("div"); div.className = "dash-card";
                div.style.padding = "16px"; div.style.cursor = "pointer";
                div.innerHTML = `<div style="width:8px; height:8px; background:#00e676; border-radius:50%; display:inline-block; margin-right:10px; box-shadow:0 0 8px #00e676;"></div><b>${d.name}</b>`;
                div.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("chatWith").innerText=d.name; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
                list.appendChild(div);
            }
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

onValue(ref(db, "groups"), snap => {
    const list = document.getElementById("groupList"); list.innerHTML = "";
    snap.forEach(g => {
        if(g.key && g.key !== "undefined" && !g.key.startsWith("-")) {
            const div = document.createElement("div"); div.className = "dash-card";
            div.style.padding = "16px"; div.style.cursor = "pointer";
            div.innerHTML = `<i class="fa-solid fa-hashtag" style="color:var(--p); margin-right:10px;"></i><b>${g.key}</b>`;
            div.onclick = () => { curChat=g.key; isGroup=true; document.getElementById("chatWith").innerText="Group: "+g.key; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
            list.appendChild(div);
        }
    });
});

function loadChat() {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            if(!v.deleted) {
                const isMe = v.from === user;
                const div = document.createElement("div"); div.className = "bubble " + (isMe ? "me" : "them");
                let nTag = isGroup ? `<span class="sender-tag">${v.from}</span>` : "";
                let rTag = v.replyTo ? `<div style="font-size:10px; opacity:0.7; background:rgba(0,0,0,0.15); padding:5px; border-radius:5px; margin-bottom:5px; border-left:2px solid var(--a);">Replying: ${v.replyTo.text.substring(0,20)}...</div>` : "";
                let reacts = v.reacts ? `<div style="position:absolute; bottom:-12px; right:10px; background:#222; border-radius:8px; padding:2px 5px; font-size:10px; border:1px solid var(--border);">${Object.values(v.reacts).join("")}</div>` : "";
                
                div.innerHTML = `${nTag}${rTag}${v.img ? `<img src="${v.img}" style="width:100%; border-radius:15px;">` : v.text}${reacts}`;
                div.onclick = (e) => showMenu(e, path, m.key, isMe);
                div.ondblclick = () => { replyData = {text: v.text, from: v.from}; document.getElementById("replyBar").style.display="flex"; document.getElementById("replyContent").innerText="Reply to: " + v.text; };
                box.appendChild(div);
            }
        });
        box.scrollTop = box.scrollHeight;
    });
}

function showMenu(e, path, key, isMe) {
    menuTarget = {path, key}; const menu = document.getElementById("actionMenu");
    document.getElementById("delOpt").style.display = isMe ? "block" : "none";
    menu.style.display = "flex"; menu.style.top = (e.clientY - 65) + "px"; menu.style.left = (e.clientX - 45) + "px";
    setTimeout(() => { document.onclick = () => { menu.style.display = "none"; document.onclick = null; }; }, 100);
}

window.setReact = (emoji) => { if(menuTarget) update(ref(db, menuTarget.path + "/" + menuTarget.key + "/reacts"), { [user]: emoji }); };
window.deleteMsg = () => { if(menuTarget) update(ref(db, menuTarget.path + "/" + menuTarget.key), { deleted: true, text: "", img: null, replyTo: null }); };
window.cancelReply = () => { replyData = null; document.getElementById("replyBar").style.display = "none"; };

window.sendMsg = () => {
    const i = document.getElementById('msgInput'); if(!i.value || !curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
    push(ref(db, path), { from: user, text: i.value, replyTo: replyData, time: Date.now() });
    i.value = ''; cancelReply();
};

window.sendImg = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
        push(ref(db, path), { from: user, img: reader.result, replyTo: replyData, time: Date.now() });
        cancelReply();
    };
    reader.readAsDataURL(e.target.files[0]);
};
</script>
</body>
</html>
