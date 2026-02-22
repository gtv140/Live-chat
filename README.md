<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect | Nexus</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root {
  --p: #6b5bff; --a: #ff5c8d; --bg: #030303; --card: rgba(255,255,255,0.06);
  --text: #ffffff; --muted: #888; --border: rgba(255,255,255,0.1); --glass: rgba(0,0,0,0.8);
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; outline: none; }
body { margin: 0; background: var(--bg); color: var(--text); transition: 0.5s; overflow: hidden; }

.mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 10% 10%, rgba(107,91,255,0.12) 0%, transparent 40%), radial-gradient(circle at 90% 90%, rgba(255,92,141,0.12) 0%, transparent 40%); }
.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Header & Nav */
header { padding: 55px 24px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(25px); background: var(--glass); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 22px; font-weight: 800; background: linear-gradient(90deg, var(--p), var(--a)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 92%; height: 75px; background: var(--glass); backdrop-filter: blur(30px); border-radius: 30px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; box-shadow: 0 10px 40px rgba(0,0,0,0.6); }
.nav-btn { color: var(--muted); font-size: 22px; cursor: pointer; transition: 0.4s; }
.nav-btn.active { color: var(--p); text-shadow: 0 0 15px var(--p); transform: translateY(-3px); }

/* Pages */
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; animation: pageIn 0.4s ease-out; }
.page.active { display: block; }
@keyframes pageIn { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }

/* Chat Bubbles */
.chat-container { background: var(--card); border-radius: 28px; border: 1px solid var(--border); height: 50vh; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px; }
.bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; background: #1a1a1a; border: 1px solid var(--border); max-width: 80%; position: relative; }
.bubble.me { background: var(--p); border: none; align-self: flex-end; border-bottom-right-radius: 4px; }
.bubble.them { border-bottom-left-radius: 4px; }

/* Fixed: Group Name Style */
.sender-tag { font-size: 11px; font-weight: 800; color: var(--a); margin-bottom: 4px; display: block; border-bottom: 1px solid rgba(255,255,255,0.1); padding-bottom: 2px; }
.bubble.me .sender-tag { color: rgba(255,255,255,0.8); }

/* Input Dock */
.dock-bar { display: flex; gap: 10px; align-items: center; background: var(--card); padding: 10px; border-radius: 25px; border: 1px solid var(--border); margin-top: 10px; }
.dock-input { flex: 1; background: transparent; border: none; color: #fff; padding: 10px; }
.send-btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--p); color: #fff; cursor: pointer; }

/* User Card */
.u-card { padding: 18px; background: var(--card); border-radius: 22px; margin-bottom: 12px; display: flex; align-items: center; gap: 12px; border: 1px solid var(--border); cursor: pointer; }

/* Auth */
#auth { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; }
</style>
</head>
<body>

<div class="mesh"></div>

<div class="app">
  <header>
    <h1>Live Connect</h1>
    <button onclick="logout()" style="background:none; border:none; color:var(--muted); font-size:18px;"><i class="fa-solid fa-power-off"></i></button>
  </header>

  <div id="auth">
    <h2>Access Nexus</h2>
    <input type="text" id="userInput" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--card); border:1px solid var(--border); color:#fff; text-align:center;" placeholder="Your Name">
    <button onclick="login()" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--p); border:none; color:#fff; font-weight:800; margin-top:20px;">INITIALIZE</button>
  </div>

  <div id="home" class="page active">
    <div style="background:linear-gradient(135deg, var(--p), var(--a)); padding:30px; border-radius:30px;">
        <h2 style="margin:0;">Hi, <span id="uName">...</span></h2>
        <p>Your connections are secure.</p>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatWith" style="font-size:11px; font-weight:800; color:var(--p); margin-bottom:12px;">ACTIVE CHANNEL</div>
    <div class="chat-container" id="chatBox"></div>
    <div class="dock-bar">
      <input id="msgInput" class="dock-input" placeholder="Message..."/>
      <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <h4 style="font-size:11px; color:var(--muted); margin-bottom:15px;">TERMINALS</h4>
    <div id="userList"></div>
    <h4 style="font-size:11px; color:var(--muted); margin:25px 0 15px;">GROUPS</h4>
    <div id="groupList"></div>
  </div>

  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-house"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-users"></i></div>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
let curChat = "", isGroup = false;

if(user) { document.getElementById("auth").style.display = "none"; start(); }

window.login = () => {
    const n = document.getElementById("userInput").value.trim();
    if(n) { user = n; localStorage.setItem("lc_user", n); location.reload(); }
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

// Fixed: User List Validation
onValue(ref(db, "users"), snap => {
    const list = document.getElementById("userList"); list.innerHTML = "";
    snap.forEach(u => {
        const data = u.val();
        // Sirf unhe dikhao jin ka name valid hai aur 'undefined' nahi hai
        if(data && data.name && data.name !== "undefined" && u.key !== user && data.online) {
            const d = document.createElement("div"); d.className = "u-card";
            d.innerHTML = `<div style="width:10px; height:10px; background:#22c55e; border-radius:50%;"></div><b>${data.name}</b>`;
            d.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("chatWith").innerText=data.name; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
            list.appendChild(d);
        }
    });
});

onValue(ref(db, "groups"), snap => {
    const list = document.getElementById("groupList"); list.innerHTML = "";
    snap.forEach(g => {
        if(g.key && g.key !== "undefined") {
            const d = document.createElement("div"); d.className = "u-card";
            d.innerHTML = `<i class="fa-solid fa-hashtag" style="color:var(--p)"></i><b>${g.key}</b>`;
            d.onclick = () => { curChat=g.key; isGroup=true; document.getElementById("chatWith").innerText="Group: "+g.key; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
            list.appendChild(d);
        }
    });
});

function loadChat() {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const val = m.val();
            if(!val.deleted) {
                const isMe = val.from === user;
                const div = document.createElement("div"); div.className = "bubble " + (isMe ? "me" : "them");
                
                // Fixed: Name Always Visible in Group
                let nameHtml = isGroup ? `<span class="sender-tag">${val.from}</span>` : "";
                div.innerHTML = `${nameHtml}${val.text}`;
                box.appendChild(div);
            }
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.sendMsg = () => {
    const i = document.getElementById('msgInput'); if(!i.value || !curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
    push(ref(db, path), { from: user, text: i.value, time: Date.now() });
    i.value = '';
};
</script>
</body>
</html>
