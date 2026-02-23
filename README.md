<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | God Override V6.0</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#8e44ad; --s:#00d2ff; --bg:#020205; --acc:#ff0055; --glass:rgba(20,20,30,0.8); --border:rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Segoe UI', sans-serif; transition: 0.3s; }
body { margin:0; background:var(--bg); color:#eee; overflow:hidden; background-image: radial-gradient(circle at center, #1a1a2e 0%, #020205 100%); }

/* FREEZE OVERLAY */
#freezeOverlay { display:none; position:fixed; inset:0; background:rgba(0,0,0,0.98); z-index:50000; flex-direction:column; align-items:center; justify-content:center; color:red; text-align:center; backdrop-filter: blur(20px); }
#freezeOverlay i { font-size: 100px; text-shadow: 0 0 30px red; animation: shake 0.5s infinite; }
@keyframes shake { 0% { transform: translate(1px, 1px); } 20% { transform: translate(-3px, 0px); } 40% { transform: translate(3px, 2px); } 100% { transform: translate(1px, -1px); } }

/* MATRIX OVERLAY */
#hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:30000; color:#0f0; padding:25px; font-family:monospace; font-size:12px; }

.app { max-width:480px; margin:auto; height:100vh; display:flex; flex-direction:column; position:relative; }

/* LOGIN SCREEN */
#loginPage { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:30px; text-align: center; }
#loginPage img { width:190px; height:190px; border-radius:50%; border:2px solid var(--s); box-shadow: 0 0 50px var(--p); margin-bottom:20px; animation: pulse 2s infinite; }
@keyframes pulse { 0% { box-shadow: 0 0 20px var(--s); } 50% { box-shadow: 0 0 60px var(--p); } 100% { box-shadow: 0 0 20px var(--s); } }
#loginPage input { width:100%; max-width:320px; padding:20px; border-radius:18px; background:var(--glass); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:15px; outline: none; }
#loginPage button { width:100%; max-width:320px; padding:20px; border:none; border-radius:18px; background:linear-gradient(45deg, var(--p), var(--s)); color:#fff; font-weight:900; cursor:pointer; text-transform: uppercase; letter-spacing: 2px; }

header { padding:50px 20px 15px; background:rgba(0,0,0,0.4); border-bottom:1px solid var(--border); display:flex; justify-content:space-between; align-items:center; backdrop-filter: blur(10px); }
header h2 { margin:0; font-size:22px; font-weight: 900; background: linear-gradient(to right, var(--s), #fff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

.page { display:none; padding:20px; flex:1; overflow-y:auto; padding-bottom:120px; animation: fadeIn 0.4s; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
.page.active { display:block; }

.dash-card { background:var(--glass); padding:20px; border-radius:28px; border:1px solid var(--border); margin-bottom:15px; backdrop-filter:blur(15px); box-shadow: 0 10px 30px rgba(0,0,0,0.5); }

/* CHAT AREA */
#chatBox { height:55vh; overflow-y:auto; display:flex; flex-direction:column; gap:12px; padding: 5px; }
.msg { padding:14px 18px; border-radius:22px; font-size:14px; max-width:85%; position:relative; line-height: 1.5; }
.msg.me { background: linear-gradient(45deg, var(--p), #673ab7); align-self:flex-end; border-bottom-right-radius:2px; box-shadow: 0 5px 15px rgba(142,68,173,0.3); }
.msg.them { background:#1e1e26; align-self:flex-start; border-bottom-left-radius:2px; border:1px solid var(--border); }
.msg b { display:block; font-size:10px; margin-bottom:4px; opacity:0.8; color:var(--s); text-transform: uppercase; }

.input-area { display:flex; gap:10px; background:var(--glass); padding:10px; border-radius:30px; border:1px solid var(--border); margin-top: 5px; }
.input-area input { flex:1; background:transparent; border:none; color:#fff; padding:10px 15px; outline:none; }
.input-area button { width:50px; height:50px; border-radius:50%; border:none; background:var(--s); color:#fff; cursor:pointer; box-shadow: 0 0 15px var(--s); }

/* NAV BAR */
nav { position:fixed; bottom:20px; left:50%; transform:translateX(-50%); width:92%; background:rgba(15,15,25,0.9); padding:18px 0; border-radius:35px; border:1px solid var(--border); display:flex; justify-content:space-around; z-index:2000; backdrop-filter: blur(20px); }
nav button { background:none; border:none; font-size:24px; color:#444; cursor:pointer; }
nav button.active { color:var(--s); transform:translateY(-8px); text-shadow: 0 0 20px var(--s); }

/* ADMIN POWERS */
.admin-card { border:1px solid var(--acc) !important; background:rgba(255,0,85,0.05) !important; }
.user-row { display:flex; justify-content:space-between; align-items:center; padding:12px; border-bottom:1px solid #222; font-size:12px; }
.btn-sm { padding:6px 12px; border-radius:8px; border:none; font-size:10px; cursor:pointer; color:white; font-weight: bold; margin-left:5px; }
</style>
</head>
<body>

<div id="freezeOverlay">
    <i class="fa-solid fa-skull-crossbones"></i>
    <h1 style="margin-top:20px; font-size: 30px;">CONNECTION TERMINATED</h1>
    <p style="opacity: 0.7;">Your IP has been flagged.<br>Access blocked by Admin786.</p>
</div>

<div id="hackOverlay"></div>

<div class="app">
    <div id="loginPage">
        <img src="http://googleusercontent.com/image_generation_content/28.png">
        <h1>Live Connect</h1>
        <p style="color:var(--muted); font-size: 11px; letter-spacing: 2px; margin-top: -5px;">SECURE TERMINAL V6.0</p>
        <input type="text" id="usernameInput" placeholder="Enter Identity Hash" autocomplete="off">
        <button onclick="initiateHack()">Initiate Override</button>
    </div>

    <header>
        <h2>Live Connect <span id="admTag" style="display:none; color:var(--acc); font-size:10px; border:1px solid var(--acc); padding:2px 6px; border-radius:5px; margin-left:8px;">GOD</span></h2>
        <button onclick="logout()" style="background:none; border:none; color:#ff4444; font-size: 20px; cursor:pointer;"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="home" class="page active">
        <div class="dash-card" style="background:linear-gradient(135deg, rgba(142,68,173,0.2), rgba(0,210,255,0.2)); text-align:center;">
            <h3 style="margin:0;">Welcome, <span id="dashUser" style="color:var(--s);">...</span></h3>
            <p style="font-size:11px; opacity:0.6; margin-top:8px;">Nodes Active | Encryption: Quantum Safe</p>
        </div>

        <div class="dash-card">
            <div style="display:flex; justify-content:space-between;">
                <span>Live Entities:</span>
                <span id="onlineCount" style="color:var(--s); font-weight:900;">0</span>
            </div>
        </div>

        <div id="adminPanel" style="display:none;" class="dash-card admin-card">
            <h4 style="color:var(--acc); margin:0 0 15px;"><i class="fa-solid fa-eye"></i> GOD CONTROL PANEL</h4>
            <div id="adminUserList" style="max-height: 250px; overflow-y: auto;"></div>
            <button onclick="wipeAll()" style="width:100%; padding:15px; background:var(--acc); border:none; border-radius:15px; color:white; font-weight:bold; margin-top:15px; box-shadow: 0 0 20px var(--acc);">EXECUTE GLOBAL WIPE</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div class="input-area">
            <input id="msgInput" placeholder="Transmit packet..."/>
            <button onclick="sendMsg()"><i class="fa-solid fa-bolt-lightning"></i></button>
        </div>
    </div>

    <div id="usersPage" class="page">
        <h3 style="font-size:14px; text-transform:uppercase; color:var(--s);">Detected Nodes</h3>
        <div id="publicUserList"></div>
        <h3 style="font-size:14px; text-transform:uppercase; color:var(--p); margin-top:20px;">Sub-Net Groups</h3>
        <div id="groupList"></div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house-chimney-window"></i></button>
        <button onclick="openPage('chat',this)"><i class="fa-solid fa-terminal"></i></button>
        <button onclick="openPage('usersPage',this)"><i class="fa-solid fa-microchip"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");

// LOGIN ANIMATION
window.initiateHack = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    const overlay = document.getElementById("hackOverlay");
    overlay.style.display = "block";
    const logs = ["BYPASSING FIREWALL...", "FETCHING GEOLOCATION...", "LOGGING TARGET IP...", "STABILIZING NODE...", "ACCESS GRANTED."];
    let i = 0;
    const itv = setInterval(() => {
        overlay.innerHTML += `<p style='margin:5px 0'>> ${logs[i]}</p>`; i++;
        if(i >= logs.length) { clearInterval(itv); setTimeout(() => { localStorage.setItem("lc_user", val); location.reload(); }, 600); }
    }, 350);
};

if(user) {
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("dashUser").textContent = user;
    
    // IP LOGGER & STATUS
    fetch('https://ipapi.co/json/').then(r=>r.json()).then(data=>{
        update(ref(db, "users/"+user), { name:user, online:true, ip:data.ip, city:data.city, frozen:false });
    });

    onDisconnect(ref(db, "users/"+user)).update({ online:false });

    // FREEZE CHECKER
    onValue(ref(db, "users/"+user+"/frozen"), snap => {
        if(snap.val() === true) document.getElementById("freezeOverlay").style.display = "flex";
        else document.getElementById("freezeOverlay").style.display = "none";
    });

    // ADMIN PRIVILEGES
    if(user === "Admin786") {
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("admTag").style.display = "inline";
        
        onValue(ref(db, "users"), snap => {
            const list = document.getElementById("adminUserList"); list.innerHTML = "";
            snap.forEach(u => {
                const d = u.val(); if(u.key === "Admin786") return;
                list.innerHTML += `<div class="user-row">
                    <span><b>${d.name}</b><br><small style='color:#0f0'>${d.ip} | ${d.city}</small></span>
                    <div>
                        <button class="btn-sm" style="background:#ff9800" onclick="freezeEntity('${u.key}', ${!d.frozen})">${d.frozen?'Unfreeze':'Freeze'}</button>
                        <button class="btn-sm" style="background:red" onclick="banEntity('${u.key}')">Ban</button>
                    </div>
                </div>`;
            });
        });
    }

    // ONLINE USERS LIST
    onValue(ref(db, "users"), snap => {
        let count = 0;
        const list = document.getElementById("publicUserList"); list.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online) { count++;
                list.innerHTML += `<div class="dash-card" style="margin-top:10px; padding:15px; display:flex; align-items:center; gap:12px;">
                    <div style="width:10px; height:10px; background:var(--s); border-radius:50%; box-shadow:0 0 10px var(--s);"></div>
                    ${u.val().name}
                </div>`;
            }
        });
        document.getElementById("onlineCount").textContent = count;
    });

    loadChat();
}

// ADMIN CORE FUNCTIONS
window.freezeEntity = (target, state) => { update(ref(db, "users/"+target), { frozen: state }); };
window.banEntity = (target) => { if(confirm("Permanently ban this node?")) remove(ref(db, "users/"+target)); };
window.wipeAll = () => { if(confirm("EXECUTE TOTAL WIPE?")) { remove(ref(db, "globalChat")); remove(ref(db, "users")); localStorage.clear(); location.reload(); } };

// CHAT CORE
window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value) return;
    push(ref(db, "globalChat"), { from: user, text: inp.value });
    if(user !== "Admin786") {
        setTimeout(() => { push(ref(db, "globalChat"), { from: "SYSTEM", text: "Packet received by Admin786." }); }, 3000);
    }
    inp.value = "";
};

function loadChat() {
    onValue(ref(db, "globalChat"), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            const type = v.from === user ? 'me' : 'them';
            box.innerHTML += `<div class="msg ${type}"><b>${v.from}</b>${v.text}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
