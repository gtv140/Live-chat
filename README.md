<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>Live Connect | God Mode</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
:root { --p: #6b5bff; --a: #ff5c8d; --bg: #030303; --card: rgba(255,255,255,0.08); --text: #ffffff; --muted: #888; --border: rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; outline: none; transition: 0.3s ease; }
body { margin: 0; background: var(--bg); color: var(--text); overflow: hidden; }
.mesh { position: fixed; inset: 0; z-index: -1; background: radial-gradient(circle at 10% 10%, rgba(107,91,255,0.15) 0%, transparent 40%), radial-gradient(circle at 90% 90%, rgba(255,92,141,0.15) 0%, transparent 40%); filter: blur(40px); }
.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

/* Global Announcement Bar */
#newsBar { display: none; background: linear-gradient(90deg, #ff4444, #ff8c00); color: white; padding: 8px; font-size: 12px; font-weight: 800; text-align: center; z-index: 2000; position: absolute; top: 0; width: 100%; box-shadow: 0 5px 15px rgba(255,68,68,0.4); }

header { padding: 50px 25px 15px; display: flex; justify-content: space-between; align-items: center; backdrop-filter: blur(20px); border-bottom: 1px solid var(--border); }
header h1 { margin: 0; font-size: 20px; font-weight: 800; letter-spacing: -1px; }

nav { position: absolute; bottom: 25px; left: 50%; transform: translateX(-50%); width: 90%; height: 70px; background: rgba(20,20,20,0.9); backdrop-filter: blur(15px); border-radius: 25px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
.nav-btn { color: var(--muted); font-size: 20px; cursor: pointer; }
.nav-btn.active { color: var(--p); transform: translateY(-5px); text-shadow: 0 0 10px var(--p); }

.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; animation: slideUp 0.4s ease; }
.page.active { display: block; }
@keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

.chat-container { background: rgba(255,255,255,0.02); border-radius: 25px; border: 1px solid var(--border); height: 50vh; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px; scrollbar-width: none; }
.bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; background: #1a1a1a; max-width: 80%; position: relative; }
.bubble.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 4px; }
.bubble.them { border-bottom-left-radius: 4px; border: 1px solid var(--border); }

.dash-card { background: var(--card); border-radius: 22px; padding: 20px; margin-bottom: 15px; border: 1px solid var(--border); }
.admin-badge { color: gold; font-size: 10px; border: 1px solid gold; padding: 2px 6px; border-radius: 6px; margin-left: 8px; font-weight: 800; }
.magic-input { width: 100%; padding: 12px; border-radius: 12px; background: rgba(0,0,0,0.3); border: 1px solid var(--border); color: white; margin-bottom: 10px; font-size: 12px; }
.magic-btn { width: 100%; padding: 12px; border-radius: 12px; border: none; font-weight: 800; cursor: pointer; font-size: 12px; }

#auth { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; }
#auth input { width: 100%; max-width: 280px; padding: 18px; border-radius: 20px; background: var(--card); border: 1px solid var(--border); color: #fff; text-align: center; margin-bottom: 15px; }
</style>
</head>
<body>
<div id="newsBar"></div>
<div class="mesh"></div>
<div class="app">
  <header>
    <h1>Live Connect <span id="ghostBadge" style="display:none;" class="admin-badge">GHOST MODE</span></h1>
    <div onclick="logout()" style="color:var(--muted); cursor:pointer;"><i class="fa-solid fa-power-off"></i></div>
  </header>

  <div id="auth">
    <div style="font-size: 60px; color: var(--p); margin-bottom: 20px;"><i class="fa-solid fa-user-secret"></i></div>
    <h2>Identity Check</h2>
    <input type="text" id="userInput" placeholder="Enter Your Name">
    <button onclick="login()" style="width:100%; max-width:280px; padding:18px; border-radius:20px; background:var(--p); border:none; color:#fff; font-weight:800; cursor:pointer;">ACCESS PORTAL</button>
  </div>

  <div id="home" class="page active">
    <div class="dash-card" style="background: linear-gradient(135deg, #6b5bff, #ff5c8d); border:none;">
        <h2 style="margin:0;">Master <span id="uNameDisp">...</span></h2>
        <p id="roleNote" style="opacity:0.8; font-size:14px;">User protocol active.</p>
    </div>

    <div id="godConsole" style="display:none;">
        <h4 style="color:gold; font-size:10px; letter-spacing:2px;">GHOST ADMIN CONSOLE</h4>
        <div class="dash-card">
            <input type="text" id="newsInput" class="magic-input" placeholder="Type news for all users...">
            <button class="magic-btn" style="background:gold; color:black;" onclick="broadcastNews()">BROADCAST TO ALL</button>
            <button class="magic-btn" style="background:#ff4444; color:white; margin-top:8px;" onclick="stopNews()">STOP NEWS</button>
        </div>
    </div>

    <div style="display:grid; grid-template-columns:1fr 1fr; gap:10px;">
        <div class="dash-card" style="text-align:center;"><h3 id="onlineCount" style="margin:0;">0</h3><p style="margin:0; font-size:10px;">NODES</p></div>
        <div class="dash-card" style="text-align:center;"><h3 style="margin:0; color:var(--a);">Locked</h3><p style="margin:0; font-size:10px;">ENCRYPT</p></div>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatWith" style="font-size:11px; font-weight:800; color:var(--p); margin-bottom:12px;">TUNNEL</div>
    <div class="chat-container" id="chatBox"></div>
    <div class="dock-bar">
      <label for="imgInp" style="color:var(--p); cursor:pointer; font-size:18px;"><i class="fa-solid fa-circle-plus"></i></label>
      <input type="file" id="imgInp" hidden onchange="sendImg(event)">
      <input id="msgInput" class="dock-input" placeholder="Whisper something..."/>
      <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <div id="userList"></div>
    <h4 style="font-size:10px; color:var(--muted); margin:20px 0 10px;">GROUPS</h4>
    <div id="groupList"></div>
  </div>
  
  <nav>
    <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-ghost"></i></div>
    <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-comment-dots"></i></div>
    <div class="nav-btn" onclick="openPage('users',this)"><i class="fa-solid fa-shield-virus"></i></div>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, update, get } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
let role = localStorage.getItem("lc_role") || "user";
let curChat = "", isGroup = false;

const ADMIN_ID = "Admin786";

if(user) checkEntry(user);

window.login = () => {
    const n = document.getElementById("userInput").value.trim();
    if(n) checkEntry(n, true);
};

async function checkEntry(name, isNew = false) {
    const s = await get(ref(db, "bannedList/" + name));
    if (s.exists()) { alert("❌ CONNECTION REFUSED."); localStorage.clear(); location.reload(); }
    else {
        if(isNew) {
            user = name; localStorage.setItem("lc_user", name);
            role = (name === ADMIN_ID) ? "admin" : "user";
            localStorage.setItem("lc_role", role);
            location.reload();
        }
        document.getElementById("auth").style.display = "none";
        init();
    }
}

function init() {
    document.getElementById("uNameDisp").textContent = user;
    if(role === "admin") {
        document.getElementById("ghostBadge").style.display = "inline";
        document.getElementById("godConsole").style.display = "block";
        document.getElementById("roleNote").textContent = "Ghost Mode Active: You are invisible.";
    } else {
        // Only non-admins are visible in the list
        set(ref(db, "users/"+user), {name: user, online: true});
        onDisconnect(ref(db, "users/"+user)).update({online: false});
    }

    // Global News Listener
    onValue(ref(db, "globalNews"), snap => {
        const bar = document.getElementById("newsBar");
        if(snap.exists() && snap.val().text) {
            bar.innerText = "🚨 ALERT: " + snap.val().text;
            bar.style.display = "block";
        } else { bar.style.display = "none"; }
    });
}

window.logout = () => { localStorage.clear(); location.reload(); };
window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
};

onValue(ref(db, "users"), snap => {
    const list = document.getElementById("userList"); list.innerHTML = "";
    let count = 0;
    snap.forEach(u => {
        const d = u.val();
        if(d && d.online) {
            count++;
            if(u.key !== user) {
                const div = document.createElement("div"); div.className = "dash-card";
                div.style.display="flex"; div.style.justifyContent="space-between";
                div.innerHTML = `<b>${d.name}</b>`;
                if(role === "admin") {
                    div.innerHTML += `<button onclick="event.stopPropagation(); banUser('${d.name}')" style="background:red; color:white; border:none; border-radius:5px; font-size:10px; cursor:pointer;">KICK</button>`;
                }
                div.onclick = () => { curChat=u.key; isGroup=false; document.getElementById("chatWith").innerText=d.name; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
                list.appendChild(div);
            }
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

// Admin Magic Functions
window.broadcastNews = () => { const t = document.getElementById("newsInput").value; if(t) set(ref(db, "globalNews"), {text: t}); };
window.stopNews = () => { remove(ref(db, "globalNews")); };
window.banUser = (n) => { if(confirm("Kick "+n+"?")) set(ref(db, "bannedList/"+n), true); };

onValue(ref(db, "groups"), snap => {
    const list = document.getElementById("groupList"); list.innerHTML = "";
    snap.forEach(g => {
        const div = document.createElement("div"); div.className = "dash-card";
        div.innerHTML = `<i class="fa-solid fa-hashtag" style="color:var(--p);"></i> <b>${g.key}</b>`;
        div.onclick = () => { curChat=g.key; isGroup=true; document.getElementById("chatWith").innerText="Group: "+g.key; loadChat(); openPage('chat', document.querySelectorAll('.nav-btn')[1]); };
        list.appendChild(div);
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
                let wand = (role === "admin") ? `<i class="fa-solid fa-wand-sparkles" style="color:gold; font-size:10px; margin-left:10px; cursor:pointer;" onclick="deleteMsg('${path}', '${m.key}')"></i>` : "";
                div.innerHTML = `${isGroup?`<small style="display:block; font-size:9px; opacity:0.6;">${v.from}</small>`:''}${v.img ? `<img src="${v.img}" style="width:100%; border-radius:15px; margin-top:5px;">` : v.text} ${wand}`;
                box.appendChild(div);
            }
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.deleteMsg = (p, id) => { update(ref(db, p + "/" + id), { deleted: true }); };
window.sendMsg = () => {
    const i = document.getElementById('msgInput'); if(!i.value || !curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
    push(ref(db, path), { from: user, text: i.value, time: Date.now() });
    i.value = '';
};
window.sendImg = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [user, curChat].sort().join("_"));
        push(ref(db, path), { from: user, img: reader.result, time: Date.now() });
    };
    reader.readAsDataURL(e.target.files[0]);
};
</script>
</body>
</html>
