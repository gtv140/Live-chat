<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | Ghost Protocol</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#c026d3; --s:#0ea5e9; --bg:#05050a; --acc:#f43f5e; --glass:rgba(15,15,25,0.9); --border:rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Inter', sans-serif; transition: 0.3s; }
body { margin:0; background:var(--bg); color:#eee; overflow:hidden; height: 100dvh; background-image: radial-gradient(circle at top right, #1e1b4b, #05050a); }

/* ALERTS */
#freezeOverlay { display:none; position:fixed; inset:0; background:#000; z-index:99999; flex-direction:column; align-items:center; justify-content:center; color:var(--acc); text-align:center; backdrop-filter: blur(50px); }

.app { max-width:480px; margin:auto; height:100dvh; display:flex; flex-direction:column; position:relative; border: 1px solid var(--border); }

/* HEADER */
header { padding:45px 20px 15px; background:rgba(0,0,0,0.6); backdrop-filter: blur(20px); border-bottom: 1px solid var(--border); display:flex; justify-content:space-between; align-items:center; }
header h1 { margin:0; font-size:20px; font-weight:900; background: linear-gradient(to right, #fff, var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

/* PAGES */
.page { display:none; padding:15px; flex:1; overflow-y:auto; padding-bottom:100px; }
.page.active { display:block; animation: fadeIn 0.3s ease; }
@keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }

/* LOGIN SCREEN */
#loginPage { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:30px; text-align:center; }
#loginPage img { width:120px; height:120px; border-radius:50%; border:2px solid var(--s); box-shadow: 0 0 40px var(--p); margin-bottom:20px; }
#loginPage input { width:100%; max-width:300px; padding:18px; border-radius:15px; background:var(--glass); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:15px; font-size:16px; outline:none; }
#loginPage button { width:100%; max-width:300px; padding:18px; border:none; border-radius:15px; background: linear-gradient(90deg, var(--p), var(--s)); color:#fff; font-weight:900; cursor:pointer; }

/* CARDS & CHAT */
.card { background:var(--glass); padding:15px; border-radius:20px; border:1px solid var(--border); margin-bottom:12px; }
#chatBox { height:calc(100dvh - 300px); overflow-y:auto; display:flex; flex-direction:column; gap:10px; }
.msg { padding:12px 15px; border-radius:18px; font-size:14px; max-width:85%; line-height:1.4; position:relative; border: 1px solid var(--border); }
.msg.me { background: var(--p); align-self:flex-end; border-bottom-right-radius:2px; border:none; }
.msg img { max-width:100%; border-radius:12px; margin-top:5px; }
.msg b { display:block; font-size:10px; color:var(--s); margin-bottom:4px; text-transform:uppercase; }

/* INPUT AREA */
.input-row { display:flex; gap:10px; background:var(--glass); padding:10px; border-radius:30px; border:1px solid var(--border); align-items: center; }
.input-row input { flex:1; background:transparent; border:none; color:#fff; outline:none; padding:5px; }
.input-row button { width:45px; height:45px; border-radius:50%; border:none; background:var(--s); color:#fff; cursor:pointer; }

/* NAVIGATION */
nav { position:fixed; bottom:20px; left:50%; transform:translateX(-50%); width:90%; background:rgba(10,10,20,0.95); padding:12px 0; border-radius:30px; border:1px solid var(--border); display:flex; justify-content:space-around; backdrop-filter: blur(20px); z-index:2000; }
nav button { background:none; border:none; font-size:22px; color:#475569; }
nav button.active { color:var(--s); transform:translateY(-5px); }

/* ADMIN UI */
.admin-ctrl { background:rgba(244,63,94,0.1); border:1px solid var(--acc); padding:10px; border-radius:15px; margin-top:10px; font-size:12px; display:flex; justify-content:space-between; align-items:center; }
.badge-god { background:var(--acc); color:#fff; padding:2px 8px; border-radius:5px; font-size:10px; font-weight:900; }
</style>
</head>
<body>

<div id="freezeOverlay"><i class="fa-solid fa-radiation" style="font-size:80px"></i><h1>NODE TERMINATED</h1><p>Access blocked by Admin786</p></div>

<div class="app">
    <div id="loginPage">
        <img src="https://i.pinimg.com/736x/8e/4a/1b/8e4a1b802a8069d273e04098675402a7.jpg">
        <h1>Live Connect</h1>
        <input type="text" id="usernameInput" placeholder="Identity Hash">
        <button onclick="login()">Enter Matrix</button>
    </div>

    <header>
        <h1>LIVE CONNECT <span id="godBadge" class="badge-god" style="display:none">GOD</span></h1>
        <button onclick="logout()" style="color:var(--acc)"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="home" class="page active">
        <div class="card" style="background:linear-gradient(135deg, rgba(192,38,211,0.2), rgba(14,165,233,0.2)); text-align:center;">
            <h3>Master: <span id="dashUser" style="color:var(--s)">...</span></h3>
        </div>
        <div class="card" style="display:flex; justify-content:space-between">
            <span>Online Entities:</span><b id="onlineCount" style="color:var(--s)">0</b>
        </div>
        
        <div id="adminPanel" style="display:none">
            <h4 style="color:var(--acc); margin-left:5px;"><i class="fa-solid fa-skull"></i> GOD CONSOLE</h4>
            <div id="adminUserList"></div>
            <button onclick="nuclearWipe()" style="width:100%; padding:15px; background:var(--acc); border:none; border-radius:15px; color:white; font-weight:900; margin-top:10px;">NUCLEAR WIPE</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div class="card" id="chatHeader" style="display:flex; justify-content:space-between; align-items:center;">
            <button onclick="openPage('users')" style="background:none; border:none; color:var(--s)"><i class="fa-solid fa-arrow-left"></i></button>
            <span id="chatWith" style="font-weight:900">SELECT TARGET</span>
            <div style="width:20px"></div>
        </div>
        <div id="chatBox"></div>
        <div class="input-row">
            <label for="imgInput" style="color:var(--s); padding:0 10px; cursor:pointer;"><i class="fa-solid fa-image"></i></label>
            <input type="file" id="imgInput" style="display:none" onchange="sendImg(this)">
            <input id="msgInput" placeholder="Message…">
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="users" class="page">
        <h3 style="font-size:12px; color:var(--s); letter-spacing:2px;">DETECTED NODES</h3>
        <div id="userList"></div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-ghost"></i></button>
        <button onclick="openPage('chat',this)" id="chatBtn"><i class="fa-solid fa-terminal"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
let target = "";

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    localStorage.setItem("lc_user", val);
    location.reload();
};

if(user) {
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("dashUser").textContent = user;

    // TRACE & STATUS
    fetch('https://ipapi.co/json/').then(r=>r.json()).then(d=>{
        update(ref(db, "users/"+user), { name:user, online:true, ip:d.ip, city:d.city, frozen:false });
    });
    onDisconnect(ref(db, "users/"+user)).update({ online:false });

    // FREEZE CHECK
    onValue(ref(db, "users/"+user+"/frozen"), snap => {
        document.getElementById("freezeOverlay").style.display = snap.val() ? "flex" : "none";
    });

    if(user === "Admin786") {
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("godBadge").style.display = "inline";
    }

    // LISTS
    onValue(ref(db, "users"), snap => {
        const uList = document.getElementById("userList");
        const aList = document.getElementById("adminUserList");
        let count = 0; uList.innerHTML = ""; aList.innerHTML = "";
        
        snap.forEach(u => {
            const d = u.val();
            if(d.online) count++;
            if(u.key === user) return;

            uList.innerHTML += `<div class="card" onclick="startChat('${u.key}')" style="display:flex; justify-content:space-between; align-items:center;">
                <span><i class="fa-solid fa-circle" style="color:${d.online?'#22c55e':'#333'}; font-size:10px;"></i> ${d.name}</span>
                <i class="fa-solid fa-chevron-right" style="opacity:0.3"></i>
            </div>`;

            if(user === "Admin786") {
                aList.innerHTML += `<div class="admin-ctrl">
                    <span><b>${d.name}</b><br><small>${d.ip} | ${d.city}</small></span>
                    <div>
                        <button onclick="startChat('${u.key}')" style="background:var(--s); border:none; padding:5px 10px; border-radius:5px; font-size:10px;">SPY</button>
                        <button onclick="setFreeze('${u.key}', ${!d.frozen})" style="background:var(--acc); border:none; padding:5px 10px; border-radius:5px; font-size:10px; color:white;">${d.frozen?'Unfreeze':'Freeze'}</button>
                    </div>
                </div>`;
            }
        });
        document.getElementById("onlineCount").textContent = count;
    });
}

window.startChat = (id) => {
    target = id;
    document.getElementById("chatWith").textContent = id.toUpperCase();
    openPage('chat', document.getElementById('chatBtn'));
    const chatID = [user, target].sort().join("_");
    onValue(ref(db, "messages/" + chatID), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            const type = v.from === user ? 'me' : 'them';
            let content = v.type === 'image' ? `<img src="${v.text}">` : v.text;
            box.innerHTML += `<div class="msg ${type}"><b>${v.from}</b>${content}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !target) return;
    const chatID = [user, target].sort().join("_");
    push(ref(db, "messages/" + chatID), { from: user, text: inp.value, type: 'text' });
    inp.value = "";
};

window.sendImg = (input) => {
    const file = input.files[0]; if(!file) return;
    const reader = new FileReader();
    reader.onload = (e) => {
        const chatID = [user, target].sort().join("_");
        push(ref(db, "messages/" + chatID), { from: user, text: e.target.result, type: 'image' });
    };
    reader.readAsDataURL(file);
};

window.setFreeze = (id, state) => { update(ref(db, "users/"+id), { frozen: state }); };
window.nuclearWipe = () => { if(confirm("DESTROY ALL DATA?")) { remove(ref(db, "messages")); remove(ref(db, "users")); location.reload(); } };
window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    if(btn) { document.querySelectorAll('nav button').forEach(b => b.classList.remove('active')); btn.classList.add('active'); }
};
window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
