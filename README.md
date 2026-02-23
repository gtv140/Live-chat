<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | God Override</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#8e44ad; --s:#00d2ff; --bg:#020205; --acc:#ff0055; --glass:rgba(20,20,30,0.8); --border:rgba(255,255,255,0.1); }
* { box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
body { margin:0; background:var(--bg); color:#eee; overflow:hidden; }

/* FREEZE OVERLAY */
#freezeOverlay { display:none; position:fixed; inset:0; background:rgba(0,0,0,0.95); z-index:50000; display:flex; flex-direction:column; align-items:center; justify-content:center; color:red; text-align:center; }

#hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:30000; color:#0f0; padding:20px; font-family:monospace; }

.app { max-width:480px; margin:auto; height:100vh; display:flex; flex-direction:column; position:relative; }

/* LOGIN */
#loginPage { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:30px; }
#loginPage img { width:180px; height:180px; border-radius:50%; border:2px solid var(--s); box-shadow: 0 0 40px var(--p); margin-bottom:20px; }
#loginPage input { width:100%; max-width:300px; padding:18px; border-radius:15px; background:var(--glass); border:1px solid var(--border); color:#fff; text-align:center; margin-bottom:15px; }
#loginPage button { width:100%; max-width:300px; padding:18px; border:none; border-radius:15px; background:linear-gradient(45deg, var(--p), var(--s)); color:#fff; font-weight:900; cursor:pointer; }

header { padding:45px 20px 15px; background:rgba(0,0,0,0.5); border-bottom:1px solid var(--border); display:flex; justify-content:space-between; align-items:center; }
.page { display:none; padding:20px; flex:1; overflow-y:auto; padding-bottom:120px; }
.page.active { display:block; }

.dash-card { background:var(--glass); padding:20px; border-radius:25px; border:1px solid var(--border); margin-bottom:15px; backdrop-filter:blur(10px); }

/* CHAT */
#chatBox { height:55vh; overflow-y:auto; display:flex; flex-direction:column; gap:12px; }
.msg { padding:12px 16px; border-radius:20px; font-size:14px; max-width:85%; position:relative; }
.msg.me { background:var(--p); align-self:flex-end; border-bottom-right-radius:2px; }
.msg.them { background:#1e1e26; align-self:flex-start; border-bottom-left-radius:2px; border:1px solid var(--border); }
.msg b { display:block; font-size:10px; margin-bottom:4px; opacity:0.7; color:var(--s); }

.input-area { display:flex; gap:10px; background:var(--glass); padding:10px; border-radius:25px; border:1px solid var(--border); }
.input-area input { flex:1; background:transparent; border:none; color:#fff; padding:10px; outline:none; }
.input-area button { width:45px; height:45px; border-radius:50%; border:none; background:var(--s); color:#fff; cursor:pointer; }

nav { position:fixed; bottom:20px; left:50%; transform:translateX(-50%); width:90%; background:rgba(10,10,10,0.9); padding:15px 0; border-radius:30px; border:1px solid var(--border); display:flex; justify-content:space-around; z-index:2000; }
nav button { background:none; border:none; font-size:22px; color:#444; cursor:pointer; transition:0.3s; }
nav button.active { color:var(--s); transform:translateY(-5px); }

/* ADMIN CONSOLE */
.admin-only { border:1px solid var(--acc) !important; background:rgba(255,0,85,0.05) !important; }
.user-row { display:flex; justify-content:space-between; align-items:center; padding:10px; border-bottom:1px solid #222; font-size:12px; }
.btn-sm { padding:5px 10px; border-radius:5px; border:none; font-size:10px; cursor:pointer; color:white; margin-left:5px; }
</style>
</head>
<body>

<div id="freezeOverlay">
    <i class="fa-solid fa-skull-crossbones" style="font-size:80px; margin-bottom:20px;"></i>
    <h1>SYSTEM TERMINATED</h1>
    <p>Your access has been revoked by ADMIN786.<br>Reason: Policy Violation.</p>
</div>

<div id="hackOverlay"></div>

<div class="app">
    <div id="loginPage">
        <img src="http://googleusercontent.com/image_generation_content/28.png">
        <h1 style="color:var(--s)">Live Connect</h1>
        <input type="text" id="usernameInput" placeholder="Hash Identity">
        <button onclick="initiateHack()">Initiate Override</button>
    </div>

    <header>
        <h2 style="margin:0; font-size:20px;">Live Connect <span id="admTag" style="display:none; color:var(--acc);">[GOD]</span></h2>
        <button onclick="logout()" style="background:none; border:none; color:#ff4444;"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="home" class="page active">
        <div class="dash-card" style="background:linear-gradient(45deg, #1a1a2e, #0f0f1a); text-align:center;">
            <h3>Welcome, <span id="dashUser" style="color:var(--s);">...</span></h3>
            <p style="font-size:12px; opacity:0.6;">V6.0 God Override Enabled</p>
        </div>

        <div id="adminPanel" style="display:none;" class="dash-card admin-only">
            <h4 style="color:var(--acc); margin-top:0;"><i class="fa-solid fa-ghost"></i> GHOST CONTROL CENTER</h4>
            <div id="adminUserList"></div>
            <button onclick="wipeAll()" style="width:100%; padding:12px; background:var(--acc); border:none; border-radius:10px; color:white; font-weight:bold; margin-top:15px;">NUCLEAR WIPE (ALL DATA)</button>
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
        <h3>Connected Entities</h3>
        <div id="publicUserList"></div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
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

window.initiateHack = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    const overlay = document.getElementById("hackOverlay");
    overlay.style.display = "block";
    const logs = ["BYPASSING FIREWALL...", "LOGGING IP...", "STABILIZING CONNECTION...", "WELCOME MASTER."];
    let i = 0;
    const itv = setInterval(() => {
        overlay.innerHTML += `<p>> ${logs[i]}</p>`; i++;
        if(i >= logs.length) { clearInterval(itv); setTimeout(() => { localStorage.setItem("lc_user", val); location.reload(); }, 600); }
    }, 300);
};

if(user) {
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("dashUser").textContent = user;
    
    // IP LOGGER
    fetch('https://ipapi.co/json/').then(r=>r.json()).then(data=>{
        set(ref(db, "users/"+user), { name:user, online:true, ip:data.ip, city:data.city, frozen:false });
    });

    onDisconnect(ref(db, "users/"+user)).update({ online:false });

    // CHECK IF FROZEN
    onValue(ref(db, "users/"+user+"/frozen"), snap => {
        if(snap.val() === true) document.getElementById("freezeOverlay").style.display = "flex";
        else document.getElementById("freezeOverlay").style.display = "none";
    });

    if(user === "Admin786") {
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("admTag").style.display = "inline";
        
        onValue(ref(db, "users"), snap => {
            const list = document.getElementById("adminUserList"); list.innerHTML = "";
            snap.forEach(u => {
                const d = u.val();
                if(u.key === "Admin786") return;
                list.innerHTML += `<div class="user-row">
                    <span><b>${d.name}</b><br><small>${d.ip} (${d.city})</small></span>
                    <div>
                        <button class="btn-sm" style="background:#ff9800" onclick="freezeUser('${u.key}', ${!d.frozen})">${d.frozen?'Unfreeze':'Freeze'}</button>
                        <button class="btn-sm" style="background:red" onclick="banUser('${u.key}')">Ban</button>
                    </div>
                </div>`;
            });
        });
    }
    loadChat();
}

// ADMIN FUNCTIONS
window.freezeUser = (target, state) => { update(ref(db, "users/"+target), { frozen: state }); };
window.banUser = (target) => { if(confirm("Ban this entity?")) remove(ref(db, "users/"+target)); };
window.wipeAll = () => { if(confirm("Wipe all data?")) { remove(ref(db, "globalChat")); remove(ref(db, "users")); localStorage.clear(); location.reload(); } };

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value) return;
    push(ref(db, "globalChat"), { from: user, text: inp.value });
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
