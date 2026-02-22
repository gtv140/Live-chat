<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect | Nexus</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root {
  --p: #6b5bff; --a: #ff5c8d; --bg: #030303; --card: rgba(255,255,255,0.04);
  --text: #ffffff; --muted: #777; --border: rgba(255,255,255,0.08); --glass: rgba(0,0,0,0.8);
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; outline: none; }
body { margin: 0; background: var(--bg); color: var(--text); transition: 0.5s; overflow: hidden; }

.mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 10% 10%, rgba(107,91,255,0.12) 0%, transparent 40%), radial-gradient(circle at 90% 90%, rgba(255,92,141,0.12) 0%, transparent 40%); }
.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Header */
header { padding: 55px 24px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(25px); background: var(--glass); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 22px; font-weight: 800; background: linear-gradient(90deg, var(--p), var(--a)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; letter-spacing: -1px; }

/* Navigation */
nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 92%; height: 75px; background: var(--glass); backdrop-filter: blur(30px); border-radius: 30px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; box-shadow: 0 10px 40px rgba(0,0,0,0.6); }
.nav-btn { color: var(--muted); font-size: 22px; cursor: pointer; transition: 0.4s; }
.nav-btn.active { color: var(--p); text-shadow: 0 0 15px var(--p); transform: translateY(-3px); }

/* Pages */
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; animation: pageIn 0.4s ease-out; }
.page.active { display: block; }
@keyframes pageIn { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }

/* Home Dashboard */
.welcome-card { background: linear-gradient(145deg, var(--p), #4a3aff); padding: 30px; border-radius: 35px; color: #fff; margin-bottom: 20px; position: relative; overflow: hidden; }
.grid-stats { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 20px; }
.stat-item { background: var(--card); border: 1px solid var(--border); padding: 20px; border-radius: 25px; text-align: center; }

/* Chat Core */
.chat-container { background: var(--card); border-radius: 28px; border: 1px solid var(--border); height: 48vh; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 15px; }
.bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; background: #181818; border: 1px solid var(--border); max-width: 80%; position: relative; }
.bubble.me { background: var(--p); border: none; align-self: flex-end; border-bottom-right-radius: 4px; }
.bubble.them { border-bottom-left-radius: 4px; }

/* Group Name Label */
.sender-name { font-size: 10px; font-weight: 800; color: var(--a); margin-bottom: 4px; display: block; text-transform: uppercase; letter-spacing: 0.5px; }
.bubble.me .sender-name { color: rgba(255,255,255,0.7); text-align: right; }

/* Reactions & Menu */
.reacts { position: absolute; bottom: -10px; right: 10px; background: #222; border-radius: 8px; padding: 2px 5px; font-size: 10px; border: 1px solid var(--border); }
#actionMenu { position: fixed; background: #121212; padding: 10px; border-radius: 20px; display: none; flex-direction: column; gap: 10px; z-index: 10000; border: 1px solid var(--p); box-shadow: 0 10px 40px rgba(0,0,0,0.9); }

/* Input Dock */
.dock-bar { display: flex; gap: 10px; align-items: center; background: var(--card); padding: 10px; border-radius: 25px; border: 1px solid var(--border); margin-top: 10px; }
.dock-input { flex: 1; background: transparent; border: none; color: #fff; padding: 10px; }
.send-btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--p); color: #fff; cursor: pointer; }

/* Auth */
#auth { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; }
</style>
</head>
<body>

<div class="mesh"></div>

<div id="actionMenu">
    <div style="display:flex; gap:12px; font-size:20px; border-bottom:1px solid var(--border); padding-bottom:8px;">
        <span onclick="setReact('❤️')">❤️</span><span onclick="setReact('😂')">😂</span><span onclick="setReact('🔥')">🔥</span><span onclick="setReact('👍')">👍</span>
    </div>
    <div onclick="deleteMsg()" id="delOpt" style="color:var(--a); font-size:13px; font-weight:600; padding:5px; text-align:center; cursor:pointer;">UNSEND</div>
</div>

<div class="app">
  <header>
    <h1>Live Connect</h1>
    <button onclick="logout()" style="background:none; border:none; color:var(--muted); font-size:18px;"><i class="fa-solid fa-power-off"></i></button>
  </header>

  <div id="auth">
    <i class="fa-solid fa-satellite-dish" style="font-size:50px; color:var(--p); margin-bottom:20px;"></i>
    <h2>Identify Yourself</h2>
    <input type="text" id="userInput" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--card); border:1px solid var(--border); color:#fff; text-align:center; margin:20px 0;" placeholder="Enter Name">
    <button onclick="login()" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--p); border:none; color:#fff; font-weight:800;">CONNECT</button>
  </div>

  <div id="home" class="page active">
    <div class="welcome-card">
        <p style="margin:0; font-size:14px; opacity:0.8;">Welcome back,</p>
        <h2 style="margin:5px 0 0; font-size:32px; font-weight:800;" id="uName">User</h2>
    </div>
    <div class="grid-stats">
        <div class="stat-item"><h3>LIVE</h3><p>Status</p></div>
        <div class="stat-item"><h3 id="onlineCount">0</h3><p>Active Nodes</p></div>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatWith" style="font-size:11px; font-weight:800; color:var(--p); margin-bottom:12px;">ENCRYPTED CHANNEL</div>
    <div class="chat-container" id="chatBox"></div>
    <div id="replyBar" style="background:var(--card); padding:8px 15px; border-radius:15px 15px 0 0; border:1px solid var(--border); display:none; justify-content:space-between; align-items:center;">
        <div id="replyContent" style="font-size:11px; color:var(--muted);"></div>
        <i class="fa-solid fa-xmark" onclick="cancelReply()"></i>
    </div>
    <div class="dock-bar">
      <label for="imgInp" style="color:var(--p); cursor:pointer;"><i class="fa-solid fa-circle-plus"></i></label>
      <input type="file" id="imgInp" hidden onchange="sendImg(event)">
      <input id="msgInput" class="dock-input" placeholder="Type a message..."/>
      <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <div id="userList"></div>
    <h4 style="font-size:11px; color:var(--muted); margin:25px 0 15px;">PUBLIC CHANNELS</h4>
    <div id="groupList"></div>
  </div>

  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-house"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-comment-dots"></i></div>
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
    if(!n) return; user = n; localStorage.setItem("lc_user", n);
    document.getElementById("auth").style.display = "none"; start();
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

onValue(ref(db, "users"), snap => {
    let count = 0; const list = document.getElementById("userList"); list.innerHTML = "<h4>ONLINE</h4>";
    snap.forEach(u => { if(u.val().online) { count++; if(u.key !== user) {
        const d = document.createElement("div"); d.style = "padding:18px; background:var(--card); border-radius:20px; margin-bottom:10px; display:flex; align-items:center; gap:12px; border:1px solid var(--border); cursor:pointer;";
        d.innerHTML = `<div style="width:10px; height:10px; background:#22c55e; border-radius:50%;"></div><b>${u.val().name}</b>`;
        d.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("chatWith").innerText=u.key; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
        list.appendChild(d);
    }}});
    document.getElementById("onlineCount").textContent = count;
});

onValue(ref(db, "groups"), snap => {
    const list = document.getElementById("groupList"); list.innerHTML = "";
    snap.forEach(g => {
        const d = document.createElement("div"); d.style = "padding:18px; background:var(--card); border-radius:20px; margin-bottom:10px; display:flex; align-items:center; gap:12px; border:1px solid var(--border); cursor:pointer;";
        d.innerHTML = `<i class="fa-solid fa-hashtag" style="color:var(--p)"></i><b>${g.key}</b>`;
        d.onclick = () => { curChat=g.key; isGroup=true; document.getElementById("chatWith").innerText="Group: "+g.key; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
        list.appendChild(d);
    });
});

function loadChat() {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const isMe = m.val().from === user;
            const div = document.createElement("div"); div.className = "bubble " + (isMe ? "me " : "them ") + (m.val().deleted ? "deleted" : "");
            
            if(m.val().deleted) {
                div.innerHTML = `<i class="fa-solid fa-ban"></i> Message deleted`;
            } else {
                let nameLabel = isGroup ? `<span class="sender-name">${m.val().from}</span>` : "";
                let rTag = m.val().replyTo ? `<div style="background:rgba(0,0,0,0.2); padding:5px; border-radius:8px; border-left:2px solid var(--a); margin-bottom:5px; font-size:10px;">${m.val().replyTo.text.substring(0,20)}...</div>` : "";
                let reacts = m.val().reacts ? `<div class="reacts">${Object.values(m.val().reacts).join("")}</div>` : "";
                
                div.innerHTML = `${nameLabel}${rTag}${m.val().img ? `<img src="${m.val().img}" style="width:100%; border-radius:12px;">` : m.val().text}${reacts}`;
                div.onclick = (e) => showMenu(e, path, m.key, isMe);
                div.ondblclick = () => { replyData = {text: m.val().text, from: m.val().from}; document.getElementById("replyBar").style.display="flex"; document.getElementById("replyContent").innerText="Replying to: " + m.val().text; };
            }
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

function showMenu(e, path, key, isMe) {
    menuTarget = {path, key}; const menu = document.getElementById("actionMenu");
    document.getElementById("delOpt").style.display = isMe ? "block" : "none";
    menu.style.display = "flex"; menu.style.top = (e.clientY - 60) + "px"; menu.style.left = (e.clientX - 40) + "px";
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
