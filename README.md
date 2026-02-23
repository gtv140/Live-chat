<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀 | Master Console</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#0a0a0a; --card:#111; --text:#e5e5e5; --muted:#aaa;
  --primary:#6b5bff; --accent:#ff5c8d; --success:#22c55e; --border: rgba(255,255,255,0.1);
}
body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--text);overflow:hidden;}
body.light{--bg:#f1f1f1;--card:#fff;--text:#111;--muted:#555;}

/* MATRIX HACKING OVERLAY */
#hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:20000; color:#0f0; padding:20px; font-family:monospace; font-size:11px; overflow:hidden; }

.app{max-width:480px; margin:auto; height:100vh; display:flex; flex-direction:column; position: relative;}

/* LOGIN SCREEN WITH SKULL */
#loginPage { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 30px; text-align: center; }
#loginPage img { width: 180px; height: 180px; border-radius: 50%; border: 3px solid var(--primary); box-shadow: 0 0 50px var(--primary); object-fit: cover; margin-bottom: 20px; }
#loginPage h1 { font-size: 32px; margin: 0; color: var(--primary); font-weight: 800; }
#loginPage input { width: 100%; max-width: 300px; padding: 18px; border-radius: 15px; background: #111; border: 1px solid var(--border); color: #fff; margin: 20px 0 10px; text-align: center; }
#loginPage button { width: 100%; max-width: 300px; padding: 18px; border: none; border-radius: 15px; background: linear-gradient(90deg, var(--accent), var(--primary)); color: #fff; font-weight: bold; cursor: pointer; box-shadow: 0 0 20px var(--accent); }

header{padding:45px 16px 15px; display:flex; justify-content:space-between; align-items:center; background:var(--card); border-bottom: 1px solid var(--border); z-index: 100;}
header h1{margin:0;font-size:22px;color:var(--primary); font-weight: 800;}

.page{display:none; padding:16px; flex:1; overflow-y: auto; padding-bottom: 110px;}
.page.active{display:block;}

.hero{background:linear-gradient(135deg,#ff5c8d,#6b5bff);color:#fff;padding:28px;border-radius:20px;text-align:center; box-shadow:0 0 15px #6b5bff;}
.dashboard-stat{background:var(--card);padding:16px;margin-top:12px;border-radius:16px;border:1px solid var(--border); display:flex;justify-content:space-between;align-items:center;}

.chat-box{background:var(--card);border-radius:18px;padding:14px;height:50vh;overflow-y:auto;margin-bottom:12px; border: 1px solid var(--border);}
.msg{background:#1a1a1a;color:#fff;padding:10px 14px;border-radius:16px;margin-bottom:10px;font-size:14px; border: 1px solid var(--border); position: relative;}
.msg b { color: var(--primary); font-size: 11px; display: block; margin-bottom: 4px; }

.input-row{display:flex;gap:10px;}
.input-row input{flex:1;padding:14px;border-radius:14px;border:1px solid #444;background:#111;color:#fff;outline:none;}
.input-row button{padding:14px;border:none;border-radius:14px;background:var(--primary);color:#fff;cursor:pointer;}

nav{position: fixed; bottom: 0; width: 100%; max-width: 480px; display:flex;justify-content:space-around;background:var(--card);padding:15px 0; border-top: 1px solid var(--border); z-index: 1000;}
nav button{background:none;border:none;font-size:20px;color:var(--muted);cursor:pointer;}
nav button.active{color:var(--primary);}

.admin-card { border: 1px solid gold !important; background: rgba(255, 215, 0, 0.05) !important; margin-top: 20px;}
.ip-log { font-family: monospace; font-size: 11px; color: #0f0; border-bottom: 1px solid #222; padding: 5px 0; }
</style>
</head>
<body>

<div id="hackOverlay"></div>

<div class="app">
    <div id="loginPage" class="page active">
        <img src="http://googleusercontent.com/image_generation_content/28.png" alt="Live Connect Skull">
        <h1>Live Connect</h1>
        <p style="color:var(--muted)">Security Protocol Active</p>
        <input type="text" id="usernameInput" placeholder="Enter Identity">
        <button onclick="initiateHack()">Initiate Access</button>
    </div>

    <header>
        <h1>Live Connect <span id="admTag" style="display:none; color:gold; font-size:10px;">[GOD]</span></h1>
        <div style="display:flex; gap:15px;">
            <button onclick="document.body.classList.toggle('light')" style="background:none;border:none;color:var(--text);font-size:18px;"><i class="fa-solid fa-moon"></i></button>
            <button onclick="logout()" style="background:none;border:none;color:var(--text);font-size:18px;"><i class="fa-solid fa-power-off"></i></button>
        </div>
    </header>

    <div id="home" class="page">
        <div class="hero">
            <h2>Welcome <span id="dashUser"></span> 🚀</h2>
            <p>Real-Time Chat & Secure Node Connection</p>
        </div>
        <div class="dashboard-stat"><h4>Online Users</h4><span id="onlineCount">0</span></div>
        
        <div id="adminPanel" style="display:none;" class="dash-card admin-card">
            <h4 style="color:gold; margin-top:0;"><i class="fa-solid fa-user-secret"></i> MASTER LOGS (IP & CITY)</h4>
            <div id="ipLogs"></div>
            <button onclick="wipeData()" style="width:100%; padding:10px; background:red; color:white; border:none; border-radius:10px; margin-top:10px; font-weight:bold;">SELF-DESTRUCT (WIPE)</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div class="chat-box" id="chatBox"></div>
        <div class="input-row">
            <input id="msgInput" placeholder="Encrypted Message..."/>
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="usersPage" class="page">
        <h3>Online Entities</h3><div id="userList"></div>
        <h3 style="margin-top:20px;">Secure Groups</h3><div id="groupList"></div>
        <div class="input-row" style="margin-top:10px;">
            <input id="groupInput" placeholder="New Group Name"/>
            <button onclick="createGroup()">CREATE</button>
        </div>
    </div>

    <div id="about" class="page">
        <h3>About Live Connect 🚀</h3>
        <p>Live Connect is a pro-level real-time communication platform built for secure networking.</p>
        <ul>
            <li>Hacking Interface & Matrix Effects</li>
            <li>Admin IP Logger & Geolocation Tracking</li>
            <li>Real-time Group & Private Node Chat</li>
            <li>Global Data Wipe Controls (Admin Only)</li>
        </ul>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
        <button onclick="openPage('usersPage',this)"><i class="fa-solid fa-user-group"></i></button>
        <button onclick="openPage('about',this)"><i class="fa-solid fa-circle-info"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("lc_user");

// MATRIX HACKING EFFECT ON LOGIN
window.initiateHack = () => {
    const uname = document.getElementById("usernameInput").value.trim();
    if(!uname) return;
    
    const overlay = document.getElementById("hackOverlay");
    overlay.style.display = "block";
    const steps = ["SCANNING NETWORK...", "BYPASSING FIREWALL...", "LOGGING IP ADDRESS...", "FETCHING GEOLOCATION...", "ACCESS GRANTED!"];
    let i = 0;
    const interval = setInterval(() => {
        overlay.innerHTML += `<p>> ${steps[i]}</p>`;
        i++;
        if(i >= steps.length) {
            clearInterval(interval);
            setTimeout(() => {
                localStorage.setItem("lc_user", uname);
                location.reload();
            }, 800);
        }
    }, 400);
};

// LOG IP DATA
async function logIP() {
    try {
        const res = await fetch('https://ipapi.co/json/');
        const data = await res.json();
        set(ref(db, "logs/" + currentUser), {
            name: currentUser, ip: data.ip, city: data.city, org: data.org, time: new Date().toLocaleTimeString()
        });
    } catch(e) {}
}

if(currentUser) {
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("dashUser").textContent = currentUser;
    logIP();
    
    if(currentUser === "Admin786") {
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("admTag").style.display = "inline";
        onValue(ref(db, "logs"), snap => {
            const logs = document.getElementById("ipLogs"); logs.innerHTML = "";
            snap.forEach(l => {
                const v = l.val();
                logs.innerHTML += `<div class="ip-log">> <b>${v.name}</b>: ${v.ip} (${v.city})</div>`;
            });
        });
    }
    
    const userRef = ref(db, "users/" + currentUser);
    set(userRef, { name: currentUser, online: true });
    onDisconnect(userRef).update({ online: false });
    loadChat();
}

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
};

onValue(ref(db, "users"), snap => {
    let count = 0;
    const list = document.getElementById("userList"); list.innerHTML = "";
    snap.forEach(u => {
        if(u.val().online) {
            count++;
            list.innerHTML += `<div style="padding:10px; background:var(--card); margin-bottom:5px; border-radius:10px; border-left:4px solid var(--success)">${u.val().name}</div>`;
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

onValue(ref(db, "groups"), snap => {
    const glist = document.getElementById("groupList"); glist.innerHTML = "";
    snap.forEach(g => {
        glist.innerHTML += `<div onclick="openGroupChat('${g.key}')" style="padding:10px; background:var(--card); margin-bottom:5px; border-radius:10px; cursor:pointer; border-left:4px solid var(--primary)">${g.key}</div>`;
    });
});

window.sendMsg = () => {
    const input = document.getElementById("msgInput");
    if(!input.value) return;
    push(ref(db, "globalChat"), { from: currentUser, text: input.value });
    input.value = "";
};

function loadChat() {
    onValue(ref(db, "globalChat"), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            const align = v.from === currentUser ? 'flex-end' : 'flex-start';
            const bg = v.from === currentUser ? 'var(--primary)' : '#222';
            box.innerHTML += `<div class="msg" style="align-self:${align}; background:${bg}"><b>${v.from}</b>${v.text}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.createGroup = () => {
    const g = document.getElementById("groupInput").value.trim();
    if(g) set(ref(db, "groups/" + g), { createdBy: currentUser });
    document.getElementById("groupInput").value = "";
};

window.wipeData = () => { if(confirm("System Self-Destruct?")) { remove(ref(db, "globalChat")); remove(ref(db, "logs")); } };
window.logout = () => { localStorage.clear(); location.reload(); };

</script>
</body>
</html>
