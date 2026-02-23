<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --bg:#05070a; --card:#0d1117; --text:#e6edf3; --primary:#6b5bff; --accent:#ff5c8d; --success:#238636; }
body{margin:0; font-family:system-ui,-apple-system; background:var(--bg); color:var(--text); height:100dvh; overflow:hidden;}

/* LOGIN SCREEN (FIXED) */
#loginPage{position:fixed; inset:0; background:#000; z-index:9999; display:flex; align-items:center; justify-content:center;}
.login-box{background:var(--card); padding:30px; border-radius:20px; width:85%; max-width:350px; text-align:center; border:1px solid #30363d; box-shadow:0 0 20px rgba(107,91,255,0.2);}
.login-box input{width:100%; padding:14px; border-radius:12px; border:1px solid #30363d; background:#010409; color:#fff; margin:20px 0; outline:none; box-sizing:border-box;}
.login-box button{width:100%; padding:14px; border-radius:12px; border:none; background:linear-gradient(45deg, #6b5bff, #ff5c8d); color:white; font-weight:bold; cursor:pointer;}

/* APP LAYOUT */
.app{max-width:480px; margin:auto; height:100dvh; display:flex; flex-direction:column; position:relative;}
header{padding:50px 20px 15px; display:flex; justify-content:space-between; align-items:center; background:var(--card); border-bottom:1px solid #30363d;}
header h1{margin:0; font-size:22px; color:var(--primary); text-shadow:0 0 10px rgba(107,91,255,0.5);}

/* PAGES */
.page{display:none; padding:15px; flex:1; overflow-y:auto; padding-bottom:180px;}
.page.active{display:flex; flex-direction:column;}

/* DASHBOARD (As per Screenshot) */
.hero-card{background:linear-gradient(135deg, #3b82f6, #8b5cf6); padding:35px 20px; border-radius:20px; text-align:center; margin-bottom:20px; box-shadow:0 10px 25px rgba(0,0,0,0.3);}
.stats-row{display:grid; grid-template-columns:1fr 1fr; gap:15px;}
.stat-item{background:var(--card); padding:20px; border-radius:15px; text-align:center; border:1px solid #30363d;}
.stat-item h4{margin:0; font-size:10px; letter-spacing:1px; color:var(--primary);}
.stat-item span{font-size:22px; font-weight:bold;}

/* CHAT STYLING */
#chatBox{flex:1; overflow-y:auto; display:flex; flex-direction:column; gap:10px; padding:10px 0;}
.msg{padding:12px 16px; border-radius:18px; max-width:80%; font-size:14px; line-height:1.4;}
.msg.me{align-self:flex-end; background:var(--primary); color:white; border-bottom-right-radius:2px;}
.msg.other{align-self:flex-start; background:#21262d; border-bottom-left-radius:2px;}
.msg img{max-width:100%; border-radius:10px; margin-top:8px;}

/* INPUT FIX */
.input-area{position:fixed; bottom:80px; width:100%; max-width:480px; padding:15px; background:var(--card); display:flex; gap:10px; border-top:1px solid #30363d; box-sizing:border-box;}
.input-area input{flex:1; background:#010409; border:1px solid #30363d; color:white; padding:12px 20px; border-radius:30px; outline:none;}
.send-btn{width:45px; height:45px; border-radius:50%; background:var(--primary); color:white; border:none; display:flex; align-items:center; justify-content:center;}

/* BOTTOM NAV */
nav{position:fixed; bottom:0; width:100%; max-width:480px; height:75px; background:var(--card); display:flex; justify-content:space-around; align-items:center; border-top:1px solid #30363d;}
nav button{background:none; border:none; color:#7d8590; font-size:22px; cursor:pointer;}
nav button.active{color:var(--primary);}

/* HIJACK SCREEN */
#freezeOverlay{display:none; position:fixed; inset:0; background:#000; z-index:10001; flex-direction:column; align-items:center; justify-content:center; color:red; text-align:center;}
</style>
</head>
<body>

<div id="loginPage">
    <div class="login-box">
        <i class="fa-solid fa-shield-halved" style="font-size:45px; color:var(--primary);"></i>
        <h2 style="margin:15px 0 5px;">Identify Node</h2>
        <input type="text" id="userInput" placeholder="Username...">
        <button onclick="doLogin()">INITIALIZE SESSION</button>
    </div>
</div>

<div id="freezeOverlay">
    <i class="fa-solid fa-biohazard" style="font-size:60px; margin-bottom:20px;"></i>
    <h2 style="font-family:monospace;">SYSTEM COMPROMISED</h2>
    <p style="opacity:0.6; font-size:12px;">Protocol 666: Access Revoked by Master.</p>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div style="display:flex; gap:15px;">
            <i class="fa-solid fa-ghost" id="admIco" style="display:none; color:var(--accent);" onclick="openTab('admin', this)"></i>
            <i class="fa-solid fa-power-off" style="color:var(--accent);" onclick="doLogout()"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="hero-card">
            <h2 id="greet">Welcome, User</h2>
            <div style="height:1px; background:rgba(255,255,255,0.2); margin:10px 0;"></div>
            <p style="font-size:12px; margin:0;">Secure Network Active</p>
        </div>
        <div class="stats-row">
            <div class="stat-item"><h4>ONLINE</h4><span id="onCount">0</span></div>
            <div class="stat-item"><h4>GROUPS</h4><span id="grCount">0</span></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div class="input-area">
            <label for="pInput" style="padding:10px; color:var(--primary);"><i class="fa-solid fa-camera"></i></label>
            <input type="file" id="pInput" hidden accept="image/*" onchange="upImg(this)">
            <input id="mInput" placeholder="Enter secure packet...">
            <button class="send-btn" onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="nodes" class="page">
        <h3 style="font-size:11px; opacity:0.4; letter-spacing:2px;">DETECTED NODES</h3>
        <div id="uList"></div>
        <div style="margin-top:20px; display:flex; justify-content:space-between;">
            <h3 style="font-size:11px; opacity:0.4;">GROUPS</h3>
            <button onclick="mkGr()" style="background:none; color:var(--primary); border:1px solid; border-radius:5px; font-size:10px;">+ NEW</button>
        </div>
        <div id="gList"></div>
    </div>

    <div id="admin" class="page">
        <h3 style="color:var(--accent); font-size:12px;">MASTER_CONTROL</h3>
        <div id="aList"></div>
    </div>

    <nav>
        <button onclick="openTab('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openTab('chat', this)"><i class="fa-solid fa-comments"></i></button>
        <button onclick="openTab('nodes', this)"><i class="fa-solid fa-fingerprint"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user");
if(user) { 
    document.getElementById("loginPage").style.display = "none";
    start(user);
}

window.doLogin = () => {
    let u = document.getElementById("userInput").value.trim();
    if(u) { localStorage.setItem("lc_user", u); location.reload(); }
};

function start(u) {
    document.getElementById("greet").innerText = "Welcome, " + u;
    const r = ref(db, "users/"+u);
    update(r, { online:true, frozen:false });
    onDisconnect(r).update({ online:false });

    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    onValue(ref(db, "users/"+u+"/frozen"), s => {
        if(s.val()) document.getElementById("freezeOverlay").style.display = "flex";
    });

    onValue(ref(db, "users"), s => {
        const ul = document.getElementById("uList"), al = document.getElementById("aList");
        let c = 0; ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.val().online) c++;
            if(n.key !== u) {
                ul.innerHTML += `<div style="background:var(--card); padding:15px; border-radius:10px; margin-bottom:10px;" onclick="chatWith('${n.key}', false)">${n.key}</div>`;
                if(u === "Admin786") al.innerHTML += `<div style="padding:10px; display:flex; justify-content:space-between;">${n.key} <button onclick="hjk('${n.key}', ${!n.val().frozen})" style="background:red; color:white; border:none; padding:5px; border-radius:5px;">${n.val().frozen?'FREE':'HJK'}</button></div>`;
            }
        });
        document.getElementById("onCount").innerText = c;
    });

    onValue(ref(db, "groups"), s => {
        const gl = document.getElementById("gList"); gl.innerHTML = "";
        document.getElementById("grCount").innerText = s.size;
        s.forEach(g => { gl.innerHTML += `<div style="background:var(--card); padding:15px; border-radius:10px; margin-bottom:10px;" onclick="chatWith('${g.key}', true)">${g.key}</div>`; });
    });
}

let path = "global";
window.chatWith = (t, g) => {
    path = g ? "groups/"+t : "pvt/"+[localStorage.getItem("lc_user"), t].sort().join("_");
    openTab('chat', document.querySelectorAll('nav button')[1]);
    onValue(ref(db, path), s => {
        const b = document.getElementById("chatBox"); b.innerHTML = "";
        s.forEach(m => {
            const d = m.val();
            const isMe = d.from === localStorage.getItem("lc_user");
            b.innerHTML += `<div class="msg ${isMe?'me':'other'}"><b>${d.from}</b>${d.type==='img'?'<img src="'+d.txt+'">':d.txt}</div>`;
        });
        b.scrollTop = b.scrollHeight;
    });
};

window.send = () => {
    let i = document.getElementById("mInput"); if(!i.value) return;
    push(ref(db, path), { from:localStorage.getItem("lc_user"), txt:i.value, type:'text', time:Date.now() });
    i.value = "";
};

window.upImg = (e) => {
    let r = new FileReader();
    r.onload = (x) => push(ref(db, path), { from:localStorage.getItem("lc_user"), txt:x.target.result, type:'img', time:Date.now() });
    r.readAsDataURL(e.files[0]);
};

window.mkGr = () => { let n = prompt("Group Name:"); if(n) set(ref(db, "groups/"+n), { created:Date.now() }); };
window.hjk = (t, s) => update(ref(db, "users/"+t), { frozen:s });
window.doLogout = () => { localStorage.clear(); location.reload(); };
window.openTab = (p, b) => {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
};
</script>
</body>
</html>
