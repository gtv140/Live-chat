<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect Modern</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --bg:#020408; --card:#0d1117; --text:#c9d1d9; --primary:#58a6ff; --accent:#f85149; --neon:#79c0ff; --glass:rgba(13, 17, 23, 0.8); }
body{margin:0; font-family:'Segoe UI',Roboto,sans-serif; background:var(--bg); color:var(--text); height:100dvh; overflow:hidden;}

/* LOGIN SCREEN */
#loginPage{position:fixed; inset:0; background:#000; z-index:9999; display:flex; align-items:center; justify-content:center;}
.login-box{background:var(--card); padding:40px; border-radius:30px; width:85%; max-width:380px; text-align:center; border:1px solid #30363d; box-shadow:0 0 50px rgba(88,166,255,0.15);}
.login-box input{width:100%; padding:15px; border-radius:15px; border:1px solid #30363d; background:#010409; color:#fff; margin:25px 0; outline:none; text-align:center; font-size:16px;}
.login-box button{width:100%; padding:16px; border-radius:15px; border:none; background:linear-gradient(135deg, #58a6ff, #bc8cff); color:white; font-weight:bold; cursor:pointer; box-shadow:0 5px 15px rgba(88,166,255,0.3);}

/* APP STRUCTURE */
.app{max-width:480px; margin:auto; height:100dvh; display:flex; flex-direction:column; position:relative;}
header{padding:50px 20px 20px; display:flex; justify-content:space-between; align-items:center; background:var(--glass); backdrop-filter:blur(10px); border-bottom:1px solid #30363d; z-index:100;}
header h1{margin:0; font-size:24px; font-weight:800; background:linear-gradient(to right, #58a6ff, #bc8cff); -webkit-background-clip:text; -webkit-text-fill-color:transparent;}

/* PAGES */
.page{display:none; padding:20px; flex:1; overflow-y:auto; padding-bottom:180px; animation:fadeIn 0.3s ease;}
.page.active{display:flex; flex-direction:column;}
@keyframes fadeIn{from{opacity:0; transform:translateY(10px);} to{opacity:1; transform:translateY(0);}}

/* MODERN DASHBOARD (Screenshot Style) */
.hero-card{background:linear-gradient(135deg, #2188ff, #8957e5); padding:40px 25px; border-radius:25px; text-align:center; margin-bottom:25px; box-shadow:0 15px 35px rgba(0,0,0,0.4); position:relative; overflow:hidden;}
.hero-card::after{content:''; position:absolute; top:-50%; left:-50%; width:200%; height:200%; background:radial-gradient(circle, rgba(255,255,255,0.1) 0%, transparent 70%);}
.hero-card h2{margin:0; font-size:26px; font-weight:700; color:white;}
.stats-grid{display:grid; grid-template-columns:1fr 1fr; gap:15px;}
.stat-item{background:var(--card); padding:25px 15px; border-radius:20px; text-align:center; border:1px solid #30363d; box-shadow:0 10px 20px rgba(0,0,0,0.2);}
.stat-item h4{margin:0 0 10px; font-size:11px; color:#8b949e; letter-spacing:2px; text-transform:uppercase;}
.stat-item span{font-size:28px; font-weight:bold; color:var(--primary);}

/* MODERN ADMIN CONSOLE */
.admin-header{display:flex; justify-content:space-between; align-items:center; margin-bottom:20px; border-left:4px solid var(--accent); padding-left:15px;}
.user-node{background:#161b22; padding:15px; border-radius:15px; margin-bottom:12px; display:flex; justify-content:space-between; align-items:center; border:1px solid #30363d;}
.hjk-btn{padding:8px 16px; border-radius:10px; border:none; font-weight:bold; cursor:pointer; font-size:11px;}

/* CHAT SYSTEM */
#chatBox{flex:1; overflow-y:auto; display:flex; flex-direction:column; gap:15px; padding:10px 5px;}
.msg{padding:14px 18px; border-radius:22px; max-width:75%; font-size:14px; position:relative; box-shadow:0 5px 15px rgba(0,0,0,0.1);}
.msg.me{align-self:flex-end; background:linear-gradient(135deg, #388cf2, #3178d4); color:white; border-bottom-right-radius:4px;}
.msg.other{align-self:flex-start; background:#21262d; border:1px solid #30363d; border-bottom-left-radius:4px;}
.msg b{display:block; font-size:10px; opacity:0.6; margin-bottom:5px; letter-spacing:1px;}
.msg img{max-width:100%; border-radius:15px; margin-top:10px; border:2px solid rgba(255,255,255,0.05);}

/* INPUT BOX */
.input-area{position:fixed; bottom:85px; width:100%; max-width:480px; padding:15px 20px; background:var(--card); display:flex; gap:12px; align-items:center; border-top:1px solid #30363d;}
.input-area input{flex:1; background:#010409; border:1px solid #30363d; color:white; padding:14px 22px; border-radius:30px; outline:none; font-size:14px;}
.action-btn{font-size:20px; color:#8b949e; cursor:pointer; transition:0.3s;}
.action-btn:hover{color:var(--primary);}

/* BOTTOM NAV */
nav{position:fixed; bottom:0; width:100%; max-width:480px; height:80px; background:var(--glass); backdrop-filter:blur(15px); display:flex; justify-content:space-around; align-items:center; border-top:1px solid #30363d; z-index:100;}
nav button{background:none; border:none; color:#484f58; font-size:24px; cursor:pointer; transition:0.3s; position:relative;}
nav button.active{color:var(--primary);}
nav button.active::after{content:''; position:absolute; bottom:-10px; left:25%; width:50%; height:3px; background:var(--primary); border-radius:10px; box-shadow:0 0 10px var(--primary);}

/* SCARY OVERLAY */
#freezeOverlay{display:none; position:fixed; inset:0; background:#000; z-index:10001; flex-direction:column; align-items:center; justify-content:center; color:#f85149; text-align:center;}
</style>
</head>
<body>

<div id="loginPage">
    <div class="login-box">
        <div style="width:80px; height:80px; background:rgba(88,166,255,0.1); border-radius:50%; display:flex; align-items:center; justify-content:center; margin:0 auto 20px;">
            <i class="fa-solid fa-user-shield" style="font-size:35px; color:var(--primary);"></i>
        </div>
        <h2>Node Identity</h2>
        <p style="font-size:12px; color:#8b949e;">Initialize your secure terminal session</p>
        <input type="text" id="userInput" placeholder="Enter Username...">
        <button onclick="doLogin()">START SESSION</button>
    </div>
</div>

<div id="freezeOverlay">
    <i class="fa-solid fa-skull-crossbones" style="font-size:70px; margin-bottom:20px; animation: pulse 1s infinite;"></i>
    <h1 style="letter-spacing:5px; font-family:monospace;">SESSION HIJACKED</h1>
    <p style="opacity:0.5;">Protocol 666 is currently active on this node.</p>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div style="display:flex; gap:20px; align-items:center;">
            <i class="fa-solid fa-ghost" id="admIco" style="display:none; color:#bc8cff; font-size:20px; cursor:pointer;" onclick="openTab('admin', this)"></i>
            <i class="fa-solid fa-power-off" style="color:var(--accent); font-size:18px; cursor:pointer;" onclick="doLogout()"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="hero-card">
            <h2 id="greet">Welcome, User</h2>
            <p style="font-size:12px; opacity:0.8; margin-top:8px;">End-to-End Encryption Verified</p>
        </div>
        <div class="stats-grid">
            <div class="stat-item"><h4>Online Nodes</h4><span id="onCount">0</span></div>
            <div class="stat-item"><h4>Active Groups</h4><span id="grCount">0</span></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div class="input-area">
            <label for="pInput" class="action-btn"><i class="fa-solid fa-image"></i></label>
            <input type="file" id="pInput" hidden accept="image/*" onchange="upImg(this)">
            <input id="mInput" placeholder="Write message...">
            <button style="background:none; border:none; color:var(--primary); font-size:22px;" onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="nodes" class="page">
        <h3 style="font-size:11px; color:#8b949e; letter-spacing:2px; margin-bottom:15px;">DISCOVERED NODES</h3>
        <div id="uList"></div>
        
        <div style="margin-top:35px; display:flex; justify-content:space-between; align-items:center;">
            <h3 style="font-size:11px; color:#8b949e; letter-spacing:2px;">CLUSTER GROUPS</h3>
            <button onclick="mkGr()" style="background:rgba(88,166,255,0.1); color:var(--primary); border:1px solid var(--primary); border-radius:8px; font-size:10px; padding:5px 12px; font-weight:bold;">+ CREATE</button>
        </div>
        <div id="gList" style="margin-top:15px;"></div>
    </div>

    <div id="admin" class="page">
        <div class="admin-header">
            <h3 style="margin:0;">MASTER CONSOLE</h3>
            <span style="font-size:10px; background:rgba(248,81,73,0.1); color:var(--accent); padding:4px 8px; border-radius:5px;">ROOT ACCESS</span>
        </div>
        <div id="aList"></div>
        <button onclick="wipeAll()" style="width:100%; padding:18px; background:#161b22; color:var(--accent); border:1px solid var(--accent); border-radius:15px; font-weight:bold; margin-top:20px; cursor:pointer;">EXECUTE NUCLEAR PURGE</button>
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
            const d = n.val();
            if(d.online) c++;
            if(n.key !== u) {
                ul.innerHTML += `<div class="user-node" onclick="chatWith('${n.key}', false)">
                    <span><i class="fa-solid fa-circle" style="font-size:8px; color:${d.online?'#238636':'#30363d'}; margin-right:10px;"></i>${n.key}</span>
                    <i class="fa-solid fa-chevron-right" style="opacity:0.2; font-size:12px;"></i>
                </div>`;
                if(u === "Admin786") {
                    al.innerHTML += `<div class="user-node" style="border-left:2px solid ${d.frozen?var(--accent):'#30363d'}">
                        <span>${n.key}</span>
                        <button onclick="hjk('${n.key}', ${!d.frozen})" class="hjk-btn" style="background:${d.frozen?'#238636':'#f85149'}; color:white;">${d.frozen?'RESTORE':'HIJACK'}</button>
                    </div>`;
                }
            }
        });
        document.getElementById("onCount").innerText = c;
    });

    onValue(ref(db, "groups"), s => {
        const gl = document.getElementById("gList"); gl.innerHTML = "";
        document.getElementById("grCount").innerText = s.size;
        s.forEach(g => { gl.innerHTML += `<div class="user-node" onclick="chatWith('${g.key}', true)"><span><i class="fa-solid fa-hashtag" style="margin-right:10px; color:var(--primary);"></i>${g.key}</span></div>`; });
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
window.wipeAll = () => { if(confirm("NUCLEAR WIPE? EVERYTHING WILL BE DELETED.")) { remove(ref(db)); location.reload(); } };
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
