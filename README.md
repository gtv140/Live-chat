<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Pro 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <style>
        :root { 
            --bg: #030508; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; 
            --text: #e6edf3; --success: #00ff88; --glass: rgba(13, 17, 23, 0.7); 
            --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
        }
        body { margin: 0; font-family: 'Inter', sans-serif; background: var(--bg); color: var(--text); height: 100vh; overflow: hidden; }
        
        /* MODERN GLASS UI */
        .glass-panel { background: var(--glass); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.05); }
        
        /* LOGIN PAGE */
        #loginPage { position: fixed; inset: 0; background: radial-gradient(circle at top, #1a1a2e, #000); z-index: 10000; display: flex; align-items: center; justify-content: center; }
        .login-card { background: var(--card); padding: 40px; border-radius: 35px; width: 85%; max-width: 380px; text-align: center; border: 1px solid #30363d; box-shadow: 0 20px 50px rgba(0,0,0,0.5); }
        .login-card i { font-size: 60px; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 20px; }
        .login-card input { width: 100%; padding: 18px; margin: 25px 0; border-radius: 15px; border: 1px solid #30363d; background: #010409; color: #fff; text-align: center; font-size: 16px; outline: none; }
        .login-card button { width: 100%; padding: 18px; border-radius: 15px; border: none; background: var(--grad); color: white; font-weight: 800; cursor: pointer; box-shadow: 0 10px 20px rgba(0,210,255,0.3); }

        /* HEADER */
        header { padding: 50px 20px 15px; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #30363d; }
        header h1 { margin: 0; font-size: 24px; font-weight: 900; letter-spacing: -1px; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* PAGES */
        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; animation: slideUp 0.4s ease-out; }
        .page.active { display: flex; flex-direction: column; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        /* DASHBOARD */
        .hero { background: var(--grad); padding: 40px 20px; border-radius: 30px; text-align: center; margin-bottom: 25px; box-shadow: 0 15px 30px rgba(0,0,0,0.3); }
        .hero h2 { font-size: 30px; margin: 0; text-shadow: 0 2px 10px rgba(0,0,0,0.2); }
        .stat-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .stat-box { background: var(--card); padding: 20px; border-radius: 25px; text-align: center; border: 1px solid #30363d; transition: 0.3s; }
        .stat-box:active { transform: scale(0.95); }
        .stat-box span { display: block; font-size: 28px; font-weight: 800; color: var(--primary); }

        /* CHAT BUBBLES */
        #chatBox { flex: 1; display: flex; flex-direction: column; gap: 10px; }
        .msg { padding: 14px 18px; border-radius: 22px; max-width: 85%; font-size: 14px; position: relative; box-shadow: 0 5px 15px rgba(0,0,0,0.1); }
        .msg.me { align-self: flex-end; background: var(--grad); color: #fff; border-bottom-right-radius: 5px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 5px; }
        .msg b { display: block; font-size: 11px; margin-bottom: 5px; color: rgba(255,255,255,0.6); }

        /* INPUT AREA */
        .input-bar { position: fixed; bottom: 95px; width: 92%; max-width: 450px; left: 50%; transform: translateX(-50%); background: #161b22; padding: 8px 15px; border-radius: 40px; display: flex; align-items: center; gap: 12px; border: 1px solid #30363d; }
        .input-bar input { flex: 1; background: none; border: none; color: white; padding: 12px; outline: none; }
        
        /* STATUS INDICATORS */
        .pulse-on { width: 10px; height: 10px; background: var(--success); border-radius: 50%; display: inline-block; margin-right: 12px; box-shadow: 0 0 10px var(--success); animation: pulse 1.5s infinite; }
        @keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.5; } 100% { opacity: 1; } }

        /* NAV BAR */
        nav { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); width: 92%; max-width: 450px; background: var(--glass); backdrop-filter: blur(25px); height: 75px; border-radius: 35px; display: flex; justify-content: space-around; align-items: center; border: 1px solid rgba(255,255,255,0.1); }
        nav button { background: none; border: none; color: #555; font-size: 24px; transition: 0.3s; padding: 15px; }
        nav button.active { color: var(--primary); transform: translateY(-8px); text-shadow: 0 0 15px var(--primary); }

        /* ADMIN MASTER */
        .admin-card { background: #1a0b16; border-left: 4px solid var(--accent); padding: 15px; border-radius: 15px; margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; }
        
        #hjkScreen { display: none; position: fixed; inset: 0; background: #000; z-index: 100000; flex-direction: column; align-items: center; justify-content: center; text-align: center; }
    </style>
</head>
<body>

<div id="loginPage">
    <div class="login-card">
        <i class="fa-solid fa-satellite-dish"></i>
        <h1 style="color:white; margin:0; font-size:28px;">Live Connect</h1>
        <p style="color:var(--primary); font-size:12px; font-weight:bold; letter-spacing:2px; margin-bottom:10px;">V50.0 STABLE</p>
        <input type="text" id="uInp" placeholder="Enter Username...">
        <button onclick="doLogin()">INITIALIZE LINK</button>
    </div>
</div>

<div id="hjkScreen">
    <i class="fa-solid fa-user-slash" style="font-size:80px; color:var(--accent);"></i>
    <h1 style="letter-spacing:5px; color:var(--accent);">TERMINAL HIJACKED</h1>
    <p style="color:#555;">Unauthorized activity detected by Admin.</p>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div style="display:flex; gap:22px; align-items:center;">
            <i class="fa-solid fa-mask" id="admIco" style="display:none; color:var(--accent); font-size:22px;" onclick="nav('admin', this)"></i>
            <i class="fa-solid fa-sign-out-alt" style="color:var(--accent); font-size:22px;" onclick="logout()"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="hero">
            <h2 id="welcome">Welcome, User</h2>
            <p style="opacity:0.8; font-size:13px;">Secure Connection established via port 8080</p>
        </div>
        <div class="stat-grid">
            <div class="stat-box"><span id="onStats">0</span><p style="margin:5px 0; font-size:10px; opacity:0.5;">LIVE NODES</p></div>
            <div class="stat-box"><span id="grStats">0</span><p style="margin:5px 0; font-size:10px; opacity:0.5;">CLUSTERS</p></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div class="input-bar">
            <label for="imgInp"><i class="fa-solid fa-image" style="color:var(--primary); font-size:20px;"></i></label>
            <input type="file" id="imgInp" hidden onchange="upImg(this)">
            <input id="msgInp" placeholder="Send secure packet...">
            <button onclick="sendMsg()" style="background:none; border:none;"><i class="fa-solid fa-paper-plane" style="color:var(--primary); font-size:20px;"></i></button>
        </div>
    </div>

    <div id="nodes" class="page">
        <h4 style="font-size:11px; opacity:0.4; letter-spacing:2px;">ONLINE TERMINALS</h4>
        <div id="uList"></div>
        <div style="display:flex; justify-content:space-between; margin-top:30px;">
            <h4 style="font-size:11px; opacity:0.4; letter-spacing:2px;">CLUSTERS</h4>
            <button onclick="mkGr()" style="color:var(--primary); background:none; border:1px solid; border-radius:5px; padding:2px 8px; font-size:10px;">NEW +</button>
        </div>
        <div id="gList"></div>
    </div>

    <div id="admin" class="page">
        <h3 style="color:var(--accent);">MASTER TERMINAL</h3>
        <div id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:20px; background:var(--accent); color:white; border:none; border-radius:15px; font-weight:bold; margin-top:20px; box-shadow: 0 10px 20px rgba(255,0,85,0.3);">NUCLEAR DATA WIPE</button>
    </div>

    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house-chimney"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-message"></i></button>
        <button onclick="nav('nodes', this)"><i class="fa-solid fa-users-viewfinder"></i></button>
    </nav>
</div>

<script>
// YOUR FIREBASE CONFIG
var firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.firebasestorage.app",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

firebase.initializeApp(firebaseConfig);
var db = firebase.database();

var currentUser = localStorage.getItem("lc_u");
if(currentUser) {
    document.getElementById("loginPage").style.display = "none";
    initApp(currentUser);
}

function doLogin() {
    var u = document.getElementById("uInp").value.trim();
    if(u) {
        db.ref("users/" + u).update({ online: true, frozen: false, lastActive: Date.now() })
        .then(() => { localStorage.setItem("lc_u", u); location.reload(); });
    }
}

function initApp(u) {
    document.getElementById("welcome").innerText = "Welcome, " + u;
    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    db.ref("users/" + u + "/frozen").on("value", s => {
        document.getElementById("hjkScreen").style.display = s.val() ? "flex" : "none";
    });

    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList"), on = 0;
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.val().online) on++;
            if(n.key !== u) {
                ul.innerHTML += `<div class="stat-box" style="margin-bottom:10px; display:flex; justify-content:space-between; align-items:center;" onclick="startChat('${n.key}', false)">
                    <span><span class="${n.val().online?'pulse-on':''}" style="background:${n.val().online?'':'#333'}"></span>${n.key}</span><i class="fa-solid fa-chevron-right" style="opacity:0.2"></i>
                </div>`;
                if(u === "Admin786") al.innerHTML += `<div class="admin-card">
                    <span>${n.key}</span><button onclick="hjk('${n.key}', ${!n.val().frozen})" style="background:${n.val().frozen?var(--success):var(--accent)}; color:white; border:none; padding:8px 15px; border-radius:10px; font-weight:bold;">${n.val().frozen?'RESTORE':'HIJACK'}</button>
                </div>`;
            }
        });
        document.getElementById("onStats").innerText = on;
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        document.getElementById("grStats").innerText = s.numChildren();
        s.forEach(g => { gl.innerHTML += `<div class="stat-box" style="margin-bottom:10px; text-align:left;" onclick="startChat('${g.key}', true)"># ${g.key}</div>`; });
    });
}

var currentPath = "global";
function startChat(t, isG) {
    currentPath = isG ? "groups/"+t : "pvt/"+[currentUser, t].sort().join("_");
    nav('chat', document.querySelectorAll('nav button')[1]);
    db.ref(currentPath).on("value", s => {
        var box = document.getElementById("chatBox"); box.innerHTML = "";
        s.forEach(m => {
            var d = m.val();
            box.innerHTML += `<div class="msg ${d.from===currentUser?'me':'other'}"><b>${d.from}</b>${d.type==='img'?'<img src="'+d.txt+'">':d.txt}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}

function sendMsg() {
    var i = document.getElementById("msgInp");
    if(i.value) {
        db.ref(currentPath).push({ from: currentUser, txt: i.value, type: 'text', time: Date.now() });
        i.value = "";
    }
}

function upImg(e) {
    var r = new FileReader();
    r.onload = f => db.ref(currentPath).push({ from: currentUser, txt: f.target.result, type: 'img' });
    r.readAsDataURL(e.files[0]);
}

function nav(p, b) {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b) b.classList.add('active');
}

function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function nuke() { if(confirm("NUCLEAR WIPE? EVERYTHING WILL BE DELETED!")) db.ref().remove(); }
function mkGr() { var n = prompt("Cluster Name:"); if(n) db.ref("groups/"+n).set({ created: Date.now() }); }
function logout() { localStorage.clear(); location.reload(); }
</script>
</body>
</html>
