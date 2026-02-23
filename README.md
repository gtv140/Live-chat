<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#050505; --card:#121212; --text:#ffffff; --primary:#00d2ff; --accent:#ff007a; --success:#00ff88;
}
body{margin:0;font-family:'Segoe UI',Roboto,sans-serif;background:var(--bg);color:var(--text); height: 100dvh; display: flex; flex-direction: column; overflow: hidden;}

/* Header Style from Screenshot */
header{padding:15px; background:var(--card); border-bottom:1px solid #333; display:flex; justify-content:space-between; align-items:center;}
header h1{margin:0; font-size:24px; color:var(--primary); font-weight:bold;}
header .icons{display:flex; gap:15px; align-items:center;}

.page{display:none; padding:15px; flex:1; overflow-y:auto;}
.page.active{display:block;}

/* Dashboard Card - Screenshot 3 */
.welcome-card{
    background: linear-gradient(135deg, #ff5c8d, #6b5bff);
    padding: 30px 20px; border-radius: 25px; text-align: center;
    box-shadow: 0 0 20px rgba(107, 91, 255, 0.5); margin-bottom: 20px;
}
.stat-card{
    background: #111; border: 1px solid #222; padding: 18px;
    border-radius: 20px; display: flex; justify-content: space-between;
    margin-bottom: 12px; box-shadow: 0 0 10px rgba(0,210,255,0.1);
}
.stat-card h4{margin:0; color:var(--accent);}

/* Admin UI - Screenshot 1 */
.admin-box{background:#0f1115; border:1px solid #1f2229; padding:15px; border-radius:20px; margin-top:20px;}
.broadcast-btn{width:100%; padding:15px; background:var(--primary); color:#000; border:none; border-radius:12px; font-weight:bold; margin-top:10px;}
.ban-card{background:var(--card); padding:15px; border-radius:15px; display:flex; justify-content:space-between; align-items:center; margin-top:10px; border:1px solid #222;}
.ban-btn{background:var(--accent); color:white; border:none; padding:8px 15px; border-radius:8px;}

/* Chat Bubbles - Screenshot 2 */
.chat-box{height:60vh; overflow-y:auto; padding:10px; display:flex; flex-direction:column; gap:12px;}
.msg{max-width:80%; padding:12px; border-radius:18px; font-size:14px; position:relative;}
.msg.received{background:#26262b; align-self:flex-start; border-bottom-left-radius:2px;}
.msg.sent{background:linear-gradient(to right, #00c6ff, #0072ff); align-self:flex-end; border-bottom-right-radius:2px;}
.msg img{width:100%; border-radius:12px; margin-top:5px;}
.msg .time{font-size:10px; opacity:0.6; display:block; text-align:right; margin-top:5px;}

.input-area{background:var(--card); padding:12px; display:flex; gap:10px; align-items:center; border-top:1px solid #222;}
.input-area input{flex:1; background:#000; border:1px solid #333; padding:12px; border-radius:12px; color:#fff; outline:none;}
.input-area button{background:var(--primary); border:none; width:45px; height:45px; border-radius:12px; color:#000;}

/* Nav */
nav{display:flex; justify-content:space-around; background:var(--card); padding:15px 0; border-top:1px solid #222;}
nav i{font-size:22px; color:#666; cursor:pointer;}
nav i.active{color:var(--primary);}
</style>
</head>
<body>

<header>
    <h1>Live-chat</h1>
    <div class="icons">
        <i class="fa-solid fa-moon"></i>
        <i class="fa-solid fa-right-from-bracket" onclick="logout()"></i>
    </div>
</header>

<div class="page active" id="home">
    <div style="padding:10px;"><h2 style="color:var(--primary); border-bottom:2px solid #333; display:inline-block; padding-bottom:5px;">Live Connect</h2></div>
    
    <div class="welcome-card">
        <h2 style="margin:0;">Welcome <span id="userNameDisp">User</span> 🚀</h2>
        <p style="opacity:0.8; font-size:14px;">Real-Time Chat & Active Connections</p>
    </div>

    <div class="stat-card"><h4>Total Users</h4><span id="uCount">0</span></div>
    <div class="stat-card"><h4>Total Groups</h4><span id="gCount">0</span></div>

    <div id="adminArea" style="display:none;">
        <p style="color:var(--primary); font-size:12px; font-weight:bold; margin-top:25px;">GLOBAL BROADCAST</p>
        <div class="admin-box">
            <input type="text" id="bcMsg" placeholder="Type alert..." style="width:100%; background:black; border:none; padding:15px; color:white; border-radius:10px;">
            <button class="broadcast-btn" onclick="sendBC()">SEND TO ALL</button>
        </div>
        <p style="color:var(--primary); font-size:12px; font-weight:bold; margin-top:20px;">USER MANAGEMENT</p>
        <div id="banList"></div>
    </div>
</div>

<div class="page" id="chat">
    <div class="chat-box" id="msgBox"></div>
    <div class="input-area">
        <label for="fileInp"><i class="fa-solid fa-paperclip" style="color:var(--primary); cursor:pointer;"></i></label>
        <input type="file" id="fileInp" hidden onchange="uploadPhoto(this)">
        <input type="text" id="msgInp" placeholder="Message...">
        <button onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div class="page" id="usersList">
    <h3 style="color:var(--primary)">Online Nodes</h3>
    <div id="activeUsers"></div>
</div>

<nav>
    <i class="fa-solid fa-house active" onclick="showP('home',this)"></i>
    <i class="fa-solid fa-comments" onclick="showP('chat',this)"></i>
    <i class="fa-solid fa-user-group" onclick="showP('usersList',this)"></i>
    <i class="fa-solid fa-circle-info"></i>
    <i class="fa-solid fa-envelope"></i>
</nav>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
};

const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("chat_user") || prompt("Username?");
if(user) {
    localStorage.setItem("chat_user", user);
    document.getElementById("userNameDisp").textContent = user;
    update(ref(db, "users/"+user), {online:true});
    if(user === "Admin786") document.getElementById("adminArea").style.display = "block";
}

window.showP = (id, el) => {
    document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav i').forEach(i=>i.classList.remove('active'));
    el.classList.add('active');
};

// Listeners for Stats & Users
onValue(ref(db, "users"), s => {
    let count = 0; document.getElementById("activeUsers").innerHTML = "";
    document.getElementById("banList").innerHTML = "";
    s.forEach(u => {
        if(u.val().online) count++;
        if(u.key !== user){
            document.getElementById("activeUsers").innerHTML += `<div class="ban-card" onclick="startChat('${u.key}')">${u.key} <div style="width:10px;height:10px;background:var(--success);border-radius:50%"></div></div>`;
            document.getElementById("banList").innerHTML += `<div class="ban-card"><span>${u.key}</span><button class="ban-btn">BAN</button></div>`;
        }
    });
    document.getElementById("uCount").textContent = count;
});

let currentTarget = "";
window.startChat = (target) => {
    currentTarget = target;
    showP('chat', document.querySelectorAll('nav i')[1]);
    const path = "chats/" + [user, target].sort().join("_");
    onValue(ref(db, path), snap => {
        const box = document.getElementById("msgBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const isMe = d.from === user;
            const div = document.createElement("div");
            div.className = `msg ${isMe ? 'sent' : 'received'}`;
            let content = d.type === 'img' ? `<img src="${d.text}">` : d.text;
            div.innerHTML = `<small style="font-size:10px; display:block; margin-bottom:3px; color:var(--primary)">${d.from}</small>${content}<span class="time">07:45 AM</span>`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
};

window.send = () => {
    const inp = document.getElementById("msgInp");
    if(!inp.value || !currentTarget) return;
    const path = "chats/" + [user, currentTarget].sort().join("_");
    push(ref(db, path), {from:user, text:inp.value, type:'text'});
    inp.value = "";
};

window.uploadPhoto = (e) => {
    const reader = new FileReader();
    reader.onload = (f) => {
        const path = "chats/" + [user, currentTarget].sort().join("_");
        push(ref(db, path), {from:user, text:f.target.result, type:'img'});
    };
    reader.readAsDataURL(e.files[0]);
};

window.logout = () => { update(ref(db, "users/"+user), {online:false}).then(()=>location.reload()); };
</script>
</body>
</html>
