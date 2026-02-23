<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect Pro 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --bg: #0d0d0f; --card: #1a1a1d; --text: #f0f0f0;
  --primary: #6366f1; --accent: #f43f5e; --glass: rgba(255,255,255,0.05);
}
* { box-sizing: border-box; font-family: 'Inter', sans-serif; }
body { margin:0; background: var(--bg); color: var(--text); height: 100dvh; display: flex; flex-direction: column; overflow: hidden; }

/* Header */
header { padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; background: var(--card); border-bottom: 1px solid var(--glass); z-index: 10; }
header h1 { margin: 0; font-size: 20px; font-weight: 800; background: linear-gradient(90deg, #6366f1, #f43f5e); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

/* Pages */
.app { flex: 1; overflow: hidden; display: flex; flex-direction: column; max-width: 500px; margin: auto; width: 100%; }
.page { display: none; padding: 15px; flex: 1; overflow-y: auto; animation: fadeIn 0.3s; }
.page.active { display: block; }
@keyframes fadeIn { from { opacity:0; transform: translateY(10px); } to { opacity:1; transform: translateY(0); } }

/* Modern Cards */
.card { background: var(--card); border-radius: 20px; padding: 20px; margin-bottom: 15px; border: 1px solid var(--glass); }
.hero { background: linear-gradient(135deg, var(--primary), #8b5cf6); color: white; border: none; }

/* Chat Box */
.chat-box { height: 55vh; overflow-y: auto; display: flex; flex-direction: column; gap: 10px; padding-bottom: 20px; }
.msg { max-width: 80%; padding: 12px; border-radius: 18px; position: relative; font-size: 14px; background: #252529; border-bottom-left-radius: 4px; align-self: flex-start; }
.msg.me { align-self: flex-end; background: var(--primary); border-bottom-left-radius: 18px; border-bottom-right-radius: 4px; }
.msg img { width: 100%; border-radius: 12px; margin-top: 5px; }
.reaction-bar { font-size: 12px; margin-top: 5px; opacity: 0.8; }

/* Inputs */
.input-area { background: var(--card); padding: 12px; display: flex; gap: 10px; border-top: 1px solid var(--glass); align-items: center; }
.input-area input { flex: 1; background: #000; border: 1px solid var(--glass); padding: 12px; border-radius: 25px; color: white; outline: none; }
.input-area i { font-size: 20px; color: var(--primary); cursor: pointer; }

/* Navigation */
nav { display: flex; justify-content: space-around; background: var(--card); padding: 15px 0; border-top: 1px solid var(--glass); }
nav i { font-size: 22px; color: #555; cursor: pointer; transition: 0.3s; }
nav i.active { color: var(--primary); transform: scale(1.1); }

/* Admin Badge */
.admin-badge { background: gold; color: black; font-size: 10px; padding: 2px 6px; border-radius: 5px; font-weight: bold; margin-left: 5px; }
</style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect 🚀</h1>
        <div style="display:flex; gap:15px;">
            <i class="fa-solid fa-moon" onclick="document.body.classList.toggle('light')"></i>
            <i class="fa-solid fa-power-off" onclick="logout()"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="card hero">
            <h3>Welcome, <span id="dashUser">User</span>!</h3>
            <p>Admin Status: <span id="adminStatus">Member</span></p>
        </div>
        <div style="display:grid; grid-template-columns:1fr 1fr; gap:10px;">
            <div class="card" style="text-align:center;"><h4>Online</h4><h2 id="onlineCount" style="color:var(--primary)">0</h2></div>
            <div class="card" style="text-align:center;"><h4>Groups</h4><h2 id="groupCount" style="color:var(--accent)">0</h2></div>
        </div>
        <div id="adminArea" style="display:none;" class="card">
            <h4 style="color:var(--accent); margin-top:0;">🛡️ Admin Dashboard</h4>
            <input id="bcMsg" placeholder="Global Broadcast..." style="width:100%; padding:10px; border-radius:10px; border:none; background:#000; color:white;">
            <button onclick="sendBC()" style="width:100%; margin-top:10px; padding:10px; border-radius:10px; background:var(--primary); color:white; border:none;">Send Broadcast</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatName" style="padding-bottom:10px; font-weight:bold; color:var(--primary)">Select a Chat</div>
        <div class="chat-box" id="chatBox"></div>
        <div id="replyPreview" style="display:none; padding:5px; background:var(--glass); font-size:12px; border-left:3px solid var(--primary);"></div>
        <div class="input-area">
            <label for="fileInp"><i class="fa-solid fa-paperclip"></i></label>
            <input type="file" id="fileInp" hidden accept="image/*" onchange="upImage(this)">
            <input id="msgInput" placeholder="Write message...">
            <i class="fa-solid fa-paper-plane" onclick="sendMsg()"></i>
        </div>
    </div>

    <div id="users" class="page">
        <h4>People</h4><div id="userList"></div>
        <h4 style="margin-top:20px;">Groups</h4><div id="groupList"></div>
    </div>

    <nav>
        <i class="fa-solid fa-house active" onclick="openPage('home',this)"></i>
        <i class="fa-solid fa-comment-dots" onclick="openPage('chat',this)"></i>
        <i class="fa-solid fa-users" onclick="openPage('users',this)"></i>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("chat_user") || prompt("Enter Username:");
if(!user) location.reload();
localStorage.setItem("chat_user", user);

const admins = ["Muhammad Nazim", "Admin786"];
const isAdmin = admins.includes(user);

document.getElementById("dashUser").textContent = user;
if(isAdmin) {
    document.getElementById("adminArea").style.display = "block";
    document.getElementById("adminStatus").textContent = "Grand Admin 👑";
}

// Online Status
const statusRef = ref(db, "users/" + user);
set(statusRef, {name: user, online: true});
onDisconnect(statusRef).update({online: false});

window.openPage = (id, el) => {
    document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav i').forEach(i=>i.classList.remove('active'));
    el.classList.add('active');
};

let curChat = "";
let isGroup = false;
let replyTo = null;

// Load Users & Groups
onValue(ref(db, "users"), snap => {
    let count = 0; userList.innerHTML = "";
    snap.forEach(u => {
        if(u.val().online) {
            count++;
            if(u.key !== user) {
                userList.innerHTML += `<div class="card" onclick="startChat('${u.key}', false)" style="padding:15px; margin-bottom:10px;">🟢 ${u.key} ${admins.includes(u.key)?'<span class="admin-badge">ADMIN</span>':''} ${isAdmin?`<button onclick="event.stopPropagation();banUser('${u.key}')" style="float:right; background:red; border:none; color:white; border-radius:5px;">Ban</button>`:''}</div>`;
            }
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

window.startChat = (target, groupFlag) => {
    curChat = target; isGroup = groupFlag;
    document.getElementById("chatName").textContent = target;
    openPage('chat', document.querySelectorAll('nav i')[1]);
    loadMsgs();
};

function loadMsgs() {
    const path = (isGroup ? "groupChats/" : "chats/") + [user, curChat].sort().join("_");
    onValue(ref(db, path), snap => {
        chatBox.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div");
            div.className = `msg ${d.from === user ? 'me' : ''}`;
            let reactionStr = d.reactions ? Object.values(d.reactions).join(" ") : "";
            div.innerHTML = `
                <div style="font-size:10px; opacity:0.6;">${d.from}</div>
                ${d.replyTo ? `<div style="background:rgba(0,0,0,0.2); padding:5px; font-size:11px; margin-bottom:5px;">🔙 ${d.replyTo}</div>` : ''}
                <div>${d.type === 'img' ? `<img src="${d.text}">` : d.text}</div>
                <div class="reaction-bar">${reactionStr}</div>
                <div style="font-size:10px; margin-top:5px; display:flex; gap:10px;">
                    <span onclick="setReply('${d.text}')">Reply</span>
                    <span onclick="react('${path}','${m.key}','❤️')">❤️</span>
                    <span onclick="react('${path}','${m.key}','👍')">👍</span>
                    ${isAdmin || d.from === user ? `<span onclick="deleteMsg('${path}','${m.key}')">🗑️</span>` : ''}
                </div>
            `;
            chatBox.appendChild(div);
        });
        chatBox.scrollTop = chatBox.scrollHeight;
    });
}

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + [user, curChat].sort().join("_");
    push(ref(db, path), {from: user, text: inp.value, type: 'text', replyTo: replyTo});
    inp.value = ""; setReply(null);
};

window.setReply = (txt) => {
    replyTo = txt;
    const pre = document.getElementById("replyPreview");
    if(txt) { pre.style.display="block"; pre.textContent = "Replying to: " + txt; }
    else { pre.style.display="none"; }
};

window.react = (path, key, emoji) => {
    set(ref(db, path + "/" + key + "/reactions/" + user), emoji);
};

window.upImage = (e) => {
    const reader = new FileReader();
    reader.onload = (f) => {
        const path = (isGroup ? "groupChats/" : "chats/") + [user, curChat].sort().join("_");
        push(ref(db, path), {from: user, text: f.target.result, type: 'img'});
    };
    reader.readAsDataURL(e.files[0]);
};

window.sendBC = () => {
    const m = document.getElementById("bcMsg").value;
    if(m) set(ref(db, "broadcast"), {msg: m, from: user});
};

onValue(ref(db, "broadcast"), snap => {
    if(snap.exists()) alert("📢 BROADCAST: " + snap.val().msg);
});

window.banUser = (t) => { if(confirm("Ban " + t + "?")) remove(ref(db, "users/" + t)); };
window.deleteMsg = (p, k) => { if(confirm("Delete?")) remove(ref(db, p + "/" + k)); };
window.logout = () => { localStorage.removeItem("chat_user"); location.reload(); };

// Groups Listener
onValue(ref(db, "groups"), snap => {
    groupCount.textContent = snap.size || 0;
    groupList.innerHTML = "";
    snap.forEach(g => {
        groupList.innerHTML += `<div class="card" onclick="startChat('${g.key}', true)"># ${g.key}</div>`;
    });
});
</script>
</body>
</html>
