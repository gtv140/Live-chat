<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect: Zenith Elite</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root {
  --p: #6b5bff; --a: #ff5c8d; --bg: #030303; --card: rgba(255,255,255,0.04);
  --text: #ffffff; --muted: #777; --border: rgba(255,255,255,0.08); --glass: rgba(0,0,0,0.8);
}
body.light { --bg: #f5f7fb; --card: #ffffff; --text: #0a0a0a; --border: rgba(0,0,0,0.05); --glass: rgba(255,255,255,0.8); }
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; outline: none; }
body { margin: 0; background: var(--bg); color: var(--text); transition: 0.5s; overflow: hidden; }

.mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 10% 10%, rgba(107,91,255,0.1) 0%, transparent 40%), radial-gradient(circle at 90% 90%, rgba(255,92,141,0.1) 0%, transparent 40%); }
.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Header & Nav */
header { padding: 55px 24px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(25px); background: var(--glass); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 20px; font-weight: 800; background: linear-gradient(135deg, var(--p), #fff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 90%; height: 75px; background: var(--glass); backdrop-filter: blur(30px); border-radius: 30px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
.nav-btn { color: var(--muted); font-size: 22px; cursor: pointer; transition: 0.4s; }
.nav-btn.active { color: var(--p); text-shadow: 0 0 15px var(--p); }

/* Pages */
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; }
.page.active { display: block; }

/* Chat Container */
.chat-container { background: var(--card); border-radius: 28px; border: 1px solid var(--border); height: 50vh; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 15px; }

/* Bubble & Reaction & Reply */
.bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; background: #151515; border: 1px solid var(--border); max-width: 80%; position: relative; transition: 0.2s; cursor: pointer; }
.bubble.me { background: var(--p); border: none; align-self: flex-end; border-bottom-right-radius: 4px; }
.bubble .reply-preview-in-msg { background: rgba(0,0,0,0.2); padding: 5px 8px; border-radius: 8px; border-left: 3px solid var(--a); margin-bottom: 5px; font-size: 12px; opacity: 0.8; }
.bubble .reacts { position: absolute; bottom: -12px; right: 10px; background: #222; border-radius: 10px; padding: 2px 6px; font-size: 12px; border: 1px solid var(--border); display: flex; gap: 3px; }

/* Reply Bar (Hidden by default) */
#replyBar { background: var(--card); padding: 10px 15px; border-radius: 15px 15px 0 0; border: 1px solid var(--border); border-bottom: none; display: none; justify-content: space-between; align-items: center; }

/* Reaction Overlay */
#reactBox { position: fixed; background: #222; padding: 10px; border-radius: 20px; display: none; gap: 15px; font-size: 20px; z-index: 10000; border: 1px solid var(--p); box-shadow: 0 0 20px var(--p); }

/* Input Dock */
.dock-bar { display: flex; gap: 10px; align-items: center; background: var(--card); padding: 10px; border-radius: 0 0 25px 25px; border: 1px solid var(--border); }
.dock-input { flex: 1; background: transparent; border: none; color: #fff; padding: 10px; }
.action-btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--p); color: #fff; cursor: pointer; }

/* Auth */
#authPanel { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; }
</style>
</head>
<body>

<div class="mesh"></div>
<div id="reactBox">
    <span onclick="setReact('❤️')">❤️</span>
    <span onclick="setReact('😂')">😂</span>
    <span onclick="setReact('👍')">👍</span>
    <span onclick="setReact('🔥')">🔥</span>
</div>

<div class="app">
  <header>
    <h1>ZENITH ELITE</h1>
    <button onclick="logout()" style="background:none; border:none; color:var(--text); font-size:20px;"><i class="fa-solid fa-power-off"></i></button>
  </header>

  <div id="authPanel">
    <h2 style="letter-spacing:2px;">IDENTITY REQUIRED</h2>
    <input type="text" id="usernameInput" style="width:100%; max-width:300px; padding:15px; border-radius:15px; background:var(--card); border:1px solid var(--border); color:#fff; text-align:center; margin:20px 0;" placeholder="Enter Name">
    <button onclick="login()" style="width:100%; max-width:300px; padding:15px; border-radius:15px; background:var(--p); border:none; color:#fff; font-weight:800;">CONNECT</button>
  </div>

  <div id="home" class="page active">
    <div style="background:linear-gradient(135deg, var(--p), var(--a)); padding:30px; border-radius:30px; color:#fff;">
        <h2 style="margin:0;">Hi, <span id="dashUser"></span></h2>
        <p>Double-tap any message to Reply.</p>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="targetLabel" style="color:var(--p); font-weight:800; font-size:12px; margin-bottom:10px;">SELECT A CHAT</div>
    <div class="chat-container" id="chatBox"></div>
    
    <div id="replyBar">
        <div id="replyText" style="font-size:12px; color:var(--muted); border-left:2px solid var(--p); padding-left:10px;"></div>
        <i class="fa-solid fa-times" onclick="cancelReply()" style="cursor:pointer; color:var(--a);"></i>
    </div>

    <div class="dock-bar">
      <label for="imgInp" style="color:var(--p); font-size:20px; cursor:pointer;"><i class="fa-solid fa-camera"></i></label>
      <input type="file" id="imgInp" hidden onchange="sendImg(event)">
      <input id="msgInput" class="dock-input" placeholder="Message..."/>
      <button class="action-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <div id="userList"></div>
    <h4 style="margin:20px 0;">CHANNELS</h4>
    <div id="groupList"></div>
  </div>

  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-home"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-message"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-users"></i></div>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("zenith_id");
let curChat = "", isGroup = false, replyTo = null, reactTarget = null;

if(currentUser) { document.getElementById("authPanel").style.display = "none"; init(); }

window.login = () => {
    const n = document.getElementById("usernameInput").value.trim();
    if(!n) return; currentUser = n; localStorage.setItem("zenith_id", n);
    init(); document.getElementById("authPanel").style.display = "none";
};

function init() {
    document.getElementById("dashUser").textContent = currentUser;
    set(ref(db, "users/"+currentUser), {name: currentUser, online: true});
    onDisconnect(ref(db, "users/"+currentUser)).update({online: false});
}

window.logout = () => { localStorage.removeItem("zenith_id"); location.reload(); };

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
};

// Users/Groups Sync
onValue(ref(db, "users"), snap => {
    const list = document.getElementById("userList"); list.innerHTML = "<h4>ONLINE</h4>";
    snap.forEach(u => { if(u.val().online && u.key !== currentUser) {
        const d = document.createElement("div"); d.style = "padding:15px; background:var(--card); border-radius:15px; margin-bottom:10px; cursor:pointer; border:1px solid var(--border)";
        d.innerHTML = `🟢 ${u.val().name}`;
        d.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("targetLabel").innerText=u.key; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
        list.appendChild(d);
    }});
});

onValue(ref(db, "groups"), snap => {
    const list = document.getElementById("groupList"); list.innerHTML = "";
    snap.forEach(g => {
        const d = document.createElement("div"); d.style = "padding:15px; background:var(--card); border-radius:15px; margin-bottom:10px; cursor:pointer; border:1px solid var(--border)";
        d.innerHTML = `# ${g.key}`;
        d.onclick = () => { curChat=g.key; isGroup=true; document.getElementById("targetLabel").innerText=g.key; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
        list.appendChild(d);
    });
});

// Chat Engine
function loadChat() {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const isMe = m.val().from === currentUser;
            const div = document.createElement("div"); div.className = "bubble " + (isMe ? "me" : "them");
            
            let replyHTML = m.val().replyTo ? `<div class="reply-preview-in-msg"><b>Replying to:</b><br>${m.val().replyTo.text.substring(0,30)}...</div>` : "";
            let reactsHTML = m.val().reacts ? `<div class="reacts">${Object.values(m.val().reacts).join("")}</div>` : "";
            
            div.innerHTML = `${replyHTML}${m.val().img ? `<img src="${m.val().img}" style="width:100%; border-radius:10px;">` : m.val().text}${reactsHTML}`;
            
            // Interaction: React & Reply
            div.onclick = (e) => showReacts(e, path, m.key);
            div.ondblclick = () => setReply(m.val().text, m.val().from);
            
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

// Reaction Logic
function showReacts(e, path, key) {
    reactTarget = {path, key};
    const box = document.getElementById("reactBox");
    box.style.display = "flex";
    box.style.top = (e.clientY - 50) + "px";
    box.style.left = (e.clientX - 50) + "px";
    setTimeout(() => { document.onclick = () => { box.style.display = "none"; document.onclick = null; }; }, 100);
}

window.setReact = (emoji) => {
    if(!reactTarget) return;
    set(ref(db, reactTarget.path + "/" + reactTarget.key + "/reacts/" + currentUser), emoji);
};

// Reply Logic
function setReply(text, from) {
    replyTo = {text, from};
    document.getElementById("replyBar").style.display = "flex";
    document.getElementById("replyText").innerHTML = `<b>${from}</b>: ${text}`;
}
window.cancelReply = () => { replyTo = null; document.getElementById("replyBar").style.display = "none"; };

window.sendMsg = () => {
    const i = document.getElementById('msgInput'); if(!i.value || !curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    push(ref(db, path), { from: currentUser, text: i.value, replyTo: replyTo, time: Date.now() });
    i.value = ''; cancelReply();
};

window.sendImg = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
        push(ref(db, path), { from: currentUser, img: reader.result, replyTo: replyTo, time: Date.now() });
        cancelReply();
    };
    reader.readAsDataURL(e.target.files[0]);
};
</script>
</body>
</html>
