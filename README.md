<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | Fixed</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#c026d3; --s:#0ea5e9; --bg:#05050a; --acc:#f43f5e; --glass:rgba(20,20,35,0.95); --border:rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Inter', sans-serif; transition: 0.2s; -webkit-tap-highlight-color: transparent; }
body { margin:0; background:var(--bg); color:#eee; overflow:hidden; height: 100dvh; background-image: radial-gradient(circle at 10% 20%, #1a1a2e 0%, #05050a 100%); }

/* OVERLAYS */
#freezeOverlay { display:none; position:fixed; inset:0; background:#000; z-index:99999; flex-direction:column; align-items:center; justify-content:center; color:var(--acc); text-align:center; backdrop-filter: blur(60px); }

.app { max-width:480px; margin:auto; height:100dvh; display:flex; flex-direction:column; position:relative; border-left: 1px solid var(--border); border-right: 1px solid var(--border); }

/* HEADER */
header { padding:50px 15px 15px; background:rgba(0,0,0,0.8); backdrop-filter: blur(25px); border-bottom: 1px solid var(--border); display:flex; justify-content:space-between; align-items:center; }
header h1 { margin:0; font-size:18px; font-weight:900; background: linear-gradient(to right, #fff, var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

/* NAVIGATION */
nav { position:fixed; bottom:20px; left:50%; transform:translateX(-50%); width:90%; background:rgba(15,15,25,0.95); padding:15px 0; border-radius:30px; border:1px solid var(--border); display:flex; justify-content:space-around; backdrop-filter: blur(20px); z-index:2000; box-shadow: 0 10px 40px rgba(0,0,0,0.5); }
nav button { background:none; border:none; font-size:22px; color:#475569; cursor:pointer; }
nav button.active { color:var(--s); transform:translateY(-5px); text-shadow: 0 0 15px var(--s); }

.page { display:none; padding:15px; flex:1; overflow-y:auto; padding-bottom:120px; }
.page.active { display:block; animation: fadeIn 0.4s ease forwards; }
@keyframes fadeIn { from { opacity:0; transform:translateY(10px); } to { opacity:1; transform:translateY(0); } }

/* CARDS */
.card { background:var(--glass); padding:16px; border-radius:22px; border:1px solid var(--border); margin-bottom:12px; position:relative; overflow:hidden; }
.card::before { content:''; position:absolute; top:0; left:0; width:100%; height:2px; background:linear-gradient(90deg, transparent, var(--s), transparent); opacity:0.3; }

/* CHAT AREA */
#chatBox { height:calc(100dvh - 300px); overflow-y:auto; display:flex; flex-direction:column; gap:12px; padding:5px; }
.msg { padding:12px 16px; border-radius:20px; font-size:14px; max-width:85%; line-height:1.5; position:relative; border: 1px solid var(--border); background:rgba(255,255,255,0.03); }
.msg.me { background: linear-gradient(135deg, var(--p), #701a75); align-self:flex-end; border-bottom-right-radius:4px; border:none; box-shadow: 0 4px 15px rgba(192,38,211,0.2); }
.msg b { display:block; font-size:10px; color:var(--s); margin-bottom:4px; text-transform:uppercase; letter-spacing:1px; }
.msg .del-btn { position:absolute; top:5px; right:8px; font-size:10px; opacity:0.5; color:#fff; display:none; }
.admin-mode .del-btn { display:block; }

/* INPUT */
.input-row { display:flex; gap:10px; background:var(--glass); padding:10px; border-radius:30px; border:1px solid var(--border); align-items: center; margin-top:10px; }
.input-row input { flex:1; background:transparent; border:none; color:#fff; outline:none; font-size:15px; }
.input-row button { width:45px; height:45px; border-radius:50%; border:none; background:var(--s); color:#fff; box-shadow: 0 0 15px rgba(14,165,233,0.4); }

/* LOGIN */
#loginPage { position:fixed; inset:0; background:#05050a; z-index:10000; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:30px; }
#loginPage img { width:120px; border-radius:50%; border:2px solid var(--s); box-shadow: 0 0 40px var(--p); margin-bottom:25px; }
#loginPage input { width:100%; max-width:300px; padding:18px; border-radius:18px; background:var(--glass); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:15px; font-size:16px; outline:none; }
#loginPage button { width:100%; max-width:300px; padding:18px; border:none; border-radius:18px; background: linear-gradient(90deg, var(--p), var(--s)); color:#fff; font-weight:900; letter-spacing:1px; cursor:pointer; }

/* ADMIN PANEL */
.admin-item { background:rgba(244,63,94,0.08); border:1px solid var(--acc); padding:12px; border-radius:18px; margin-bottom:10px; display:flex; justify-content:space-between; align-items:center; }
.god-tag { background:var(--acc); color:#fff; padding:2px 8px; border-radius:6px; font-size:10px; font-weight:bold; letter-spacing:1px; }
</style>
</head>
<body class="">

<div id="freezeOverlay"><i class="fa-solid fa-biohazard" style="font-size:70px"></i><h2>SYSTEM BREACHED</h2><p>Node locked by Admin786</p></div>

<div class="app">
    <div id="loginPage">
        <img src="https://i.pinimg.com/736x/8e/4a/1b/8e4a1b802a8069d273e04098675402a7.jpg">
        <h2 style="margin-bottom:20px; letter-spacing:2px;">LIVE CONNECT</h2>
        <input type="text" id="usernameInput" placeholder="Enter Access Key">
        <button onclick="login()">AUTHORIZE ACCESS</button>
    </div>

    <header>
        <h1>LIVE CONNECT <span id="godBadge" class="god-tag" style="display:none">GOD</span></h1>
        <button onclick="logout()" style="color:var(--acc); background:none; border:none;"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="home" class="page active">
        <div class="card" style="background:linear-gradient(135deg, rgba(192,38,211,0.15), rgba(14,165,233,0.15)); text-align:center; padding:30px 15px;">
            <p style="margin:0; opacity:0.6; font-size:12px; text-transform:uppercase;">Active Node</p>
            <h2 style="margin:5px 0; color:var(--s); letter-spacing:1px;" id="dashUser">...</h2>
        </div>
        
        <div class="card" style="display:flex; justify-content:space-between; align-items:center;">
            <span>Detected Nodes:</span><b id="onlineCount" style="color:var(--s); font-size:18px;">0</b>
        </div>
        
        <div id="adminPanel" style="display:none">
            <p style="color:var(--acc); font-size:11px; font-weight:900; margin:20px 0 10px 5px; letter-spacing:2px;"><i class="fa-solid fa-terminal"></i> GHOST CONTROL</p>
            <div id="adminUserList"></div>
            <button onclick="wipeData()" style="width:100%; padding:18px; background:var(--acc); border:none; border-radius:18px; color:white; font-weight:900; margin-top:10px; box-shadow: 0 0 20px rgba(244,63,94,0.3);">NUCLEAR DESTRUCTION</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div class="card" style="display:flex; align-items:center; gap:15px; padding:12px 18px; margin-bottom:15px;">
            <button onclick="openPage('users')" style="background:none; border:none; color:var(--s); font-size:18px;"><i class="fa-solid fa-arrow-left"></i></button>
            <span id="chatWith" style="font-weight:900; font-size:15px; letter-spacing:1px;">SELECT NODE</span>
        </div>
        <div id="chatBox"></div>
        <div class="input-row">
            <label for="imgInput" style="color:var(--s); padding:0 10px; cursor:pointer; font-size:20px;"><i class="fa-solid fa-camera-retro"></i></label>
            <input type="file" id="imgInput" style="display:none" accept="image/*" onchange="sendImg(this)">
            <input id="msgInput" placeholder="Inject message packet…">
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="users" class="page">
        <p style="color:#475569; font-size:11px; font-weight:900; margin-bottom:15px; letter-spacing:2px; padding-left:5px;">NETWORK ENTITIES</p>
        <div id="userList"></div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house-chimney-window"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-ghost"></i></button>
        <button onclick="openPage('chat',this)" id="chatBtn"><i class="fa-solid fa-fingerprint"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.firebasestorage.app",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

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

    if(user === "Admin786") {
        document.body.classList.add("admin-mode");
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("godBadge").style.display = "inline";
    }

    // IP TRACING & STATUS UPDATE
    fetch('https://ipapi.co/json/').then(r=>r.json()).then(d=>{
        update(ref(db, "users/"+user), { 
            name: user, 
            online: true, 
            ip: d.ip || '0.0.0.0', 
            city: d.city || 'Hidden', 
            frozen: false,
            lastSeen: Date.now()
        });
    }).catch(() => {
        update(ref(db, "users/"+user), { name: user, online: true, frozen: false });
    });
    
    onDisconnect(ref(db, "users/"+user)).update({ online: false });

    // FREEZE CHECK
    onValue(ref(db, "users/"+user+"/frozen"), snap => {
        document.getElementById("freezeOverlay").style.display = snap.val() ? "flex" : "none";
    });

    // RENDER LISTS (UNDEFINED FIX APPLIED)
    onValue(ref(db, "users"), snap => {
        const uList = document.getElementById("userList");
        const aList = document.getElementById("adminUserList");
        let online = 0; 
        uList.innerHTML = ""; aList.innerHTML = "";
        
        snap.forEach(u => {
            const d = u.val();
            const displayName = d.name || u.key; // Agar 'name' undefined ho toh 'key' use karega
            
            if(d.online) online++;
            if(u.key === user) return;

            uList.innerHTML += `<div class="card" onclick="startChat('${u.key}')" style="display:flex; justify-content:space-between; align-items:center; cursor:pointer;">
                <span><i class="fa-solid fa-circle" style="color:${d.online?'#22c55e':'#333'}; font-size:10px; margin-right:10px;"></i> ${displayName}</span>
                <i class="fa-solid fa-chevron-right" style="opacity:0.3; font-size:12px;"></i>
            </div>`;

            if(user === "Admin786") {
                aList.innerHTML += `<div class="admin-item">
                    <span><b>${displayName}</b><br><small style="opacity:0.6">${d.ip || 'No IP'} | ${d.city || ''}</small></span>
                    <div>
                        <button onclick="startChat('${u.key}')" style="background:var(--s); border:none; padding:8px 12px; border-radius:10px; font-weight:bold; font-size:11px; color:#000;">SPY</button>
                        <button onclick="setFreeze('${u.key}', ${!d.frozen})" style="background:var(--acc); border:none; padding:8px 12px; border-radius:10px; color:white; margin-left:8px;"><i class="fa-solid ${d.frozen?'fa-unlock':'fa-lock'}"></i></button>
                    </div>
                </div>`;
            }
        });
        document.getElementById("onlineCount").textContent = online;
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
            const side = v.from === user ? 'me' : 'them';
            let content = v.type === 'image' ? `<img src="${v.text}" style="width:100%;">` : v.text;
            
            box.innerHTML += `<div class="msg ${side}">
                <span class="del-btn" onclick="deleteMsg('${chatID}','${m.key}')"><i class="fa-solid fa-trash"></i></span>
                <b>${v.from}</b>${content}
            </div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !target) return;
    const chatID = [user, target].sort().join("_");
    push(ref(db, "messages/" + chatID), { from: user, text: inp.value, type: 'text', time: Date.now() });
    inp.value = "";
};

window.sendImg = (input) => {
    const file = input.files[0]; if(!file) return;
    const reader = new FileReader();
    reader.onload = (e) => {
        const chatID = [user, target].sort().join("_");
        push(ref(db, "messages/" + chatID), { from: user, text: e.target.result, type: 'image', time: Date.now() });
    };
    reader.readAsDataURL(file);
};

// GOD MODE FUNCTIONS
window.deleteMsg = (chatID, msgKey) => { if(user === "Admin786") remove(ref(db, `messages/${chatID}/${msgKey}`)); };
window.setFreeze = (id, state) => { update(ref(db, "users/"+id), { frozen: state }); };
window.wipeData = () => { if(confirm("CONFIRM TOTAL DESTRUCTION?")) { remove(ref(db, "messages")); remove(ref(db, "users")); location.reload(); } };

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    if(btn) { document.querySelectorAll('nav button').forEach(b => b.classList.remove('active')); btn.classList.add('active'); }
};
window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
