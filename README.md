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
            --text: #e6edf3; --success: #00ff88; --glass: rgba(13, 17, 23, 0.9); 
            --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
        }
        /* FIXED BODY SCROLL */
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); height: 100vh; width: 100vw; overflow: hidden; display: flex; flex-direction: column; }
        
        #loginPage { position: fixed; inset: 0; background: radial-gradient(circle at top, #1a1a2e, #000); z-index: 10000; display: flex; align-items: center; justify-content: center; }
        .login-card { background: var(--card); padding: 40px; border-radius: 35px; width: 85%; max-width: 380px; text-align: center; border: 1px solid #30363d; }
        .login-card input { width: 100%; padding: 18px; margin: 20px 0; border-radius: 15px; border: 1px solid #30363d; background: #010409; color: #fff; text-align: center; outline: none; box-sizing: border-box; }
        .login-card button { width: 100%; padding: 18px; border-radius: 15px; border: none; background: var(--grad); color: white; font-weight: 800; }

        header { padding: 50px 20px 15px; flex-shrink: 0; background: var(--glass); border-bottom: 1px solid #30363d; display: flex; justify-content: space-between; align-items: center; z-index: 100; }
        header h1 { margin: 0; font-size: 24px; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 900; }

        /* SCROLLABLE PAGE AREA */
        .page-container { flex-grow: 1; position: relative; overflow: hidden; }
        .page { display: none; position: absolute; inset: 0; padding: 20px; overflow-y: auto; -webkit-overflow-scrolling: touch; padding-bottom: 150px; }
        .page.active { display: block; }

        /* CHAT BOX FIX */
        #chatBox { display: flex; flex-direction: column; gap: 12px; min-height: 100%; padding-bottom: 20px; }
        .msg { padding: 12px 16px; border-radius: 20px; max-width: 80%; font-size: 14px; line-height: 1.5; word-wrap: break-word; }
        .msg.me { align-self: flex-end; background: var(--grad); color: #fff; border-bottom-right-radius: 4px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 4px; }
        .msg b { display: block; font-size: 10px; opacity: 0.6; margin-bottom: 4px; }
        .msg img { max-width: 100%; border-radius: 12px; margin-top: 8px; display: block; }

        .hero { background: var(--grad); padding: 30px 20px; border-radius: 25px; text-align: center; margin-bottom: 20px; }
        .stat-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 20px; }
        .stat-box { background: var(--card); padding: 20px; border-radius: 20px; text-align: center; border: 1px solid #30363d; }

        /* FLOATING INPUT */
        .input-bar { position: fixed; bottom: 100px; width: 92%; max-width: 450px; left: 50%; transform: translateX(-50%); background: #161b22; padding: 10px 15px; border-radius: 35px; display: flex; align-items: center; gap: 10px; border: 1px solid #30363d; z-index: 500; }
        .input-bar input { flex: 1; background: none; border: none; color: white; outline: none; padding: 8px; }

        nav { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); width: 92%; max-width: 450px; background: var(--glass); backdrop-filter: blur(20px); height: 75px; border-radius: 35px; display: flex; justify-content: space-around; align-items: center; border: 1px solid rgba(255,255,255,0.1); z-index: 1000; }
        nav button { background: none; border: none; color: #484f58; font-size: 22px; cursor: pointer; transition: 0.3s; padding: 10px; }
        nav button.active { color: var(--primary); transform: translateY(-5px); }

        #hjkScreen { display: none; position: fixed; inset: 0; background: #000; z-index: 100000; flex-direction: column; align-items: center; justify-content: center; text-align: center; color: var(--accent); }
    </style>
</head>
<body>

<div id="loginPage">
    <div class="login-card">
        <i class="fa-solid fa-satellite-dish" style="font-size: 50px; color: var(--primary);"></i>
        <h1>Live Connect</h1>
        <input type="text" id="uInp" placeholder="Username...">
        <button onclick="doLogin()">START ENGINE</button>
    </div>
</div>

<div id="hjkScreen">
    <i class="fa-solid fa-user-lock" style="font-size:80px; margin-bottom:20px;"></i>
    <h1>TERMINAL BANNED</h1>
</div>

<header>
    <h1>Live Connect</h1>
    <div style="display:flex; gap:20px;">
        <i class="fa-solid fa-shield-halved" id="admIco" style="display:none; color:var(--primary); font-size:20px;" onclick="nav('admin')"></i>
        <i class="fa-solid fa-power-off" style="color:var(--accent); font-size:20px;" onclick="logout()"></i>
    </div>
</header>

<div class="page-container">
    <div id="home" class="page active">
        <div class="hero">
            <h2 id="welcome">Welcome</h2>
            <p style="font-size:12px;">Protocol 80.0 Active</p>
        </div>
        <div class="stat-grid">
            <div class="stat-box"><span id="onStats">0</span><p>ONLINE</p></div>
            <div class="stat-box"><span id="grStats">0</span><p>CLUSTERS</p></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div class="input-bar">
            <label for="imgInp"><i class="fa-solid fa-image" style="color:var(--primary);"></i></label>
            <input type="file" id="imgInp" hidden onchange="upImg(this)">
            <input id="msgInp" placeholder="Type packet...">
            <i class="fa-solid fa-paper-plane" onclick="sendMsg()"></i>
        </div>
    </div>

    <div id="nodes" class="page">
        <h4 style="opacity:0.4; letter-spacing:1px; font-size:12px;">ACTIVE NODES</h4>
        <div id="uList"></div>
        <div style="display:flex; justify-content:space-between; margin:20px 0; align-items:center;">
            <h4 style="opacity:0.4; letter-spacing:1px; font-size:12px;">CLUSTERS</h4>
            <button onclick="mkGr()" style="background:none; border:1px solid var(--primary); color:var(--primary); padding:4px 8px; border-radius:5px;">+ NEW</button>
        </div>
        <div id="gList"></div>
    </div>

    <div id="admin" class="page">
        <h3 style="color:var(--primary);">BROADCAST</h3>
        <input type="text" id="bcMsg" style="width:100%; padding:15px; border-radius:10px; background:#000; border:1px solid #333; color:#fff;" placeholder="Alert message...">
        <button onclick="sendBC()" style="width:100%; padding:12px; background:var(--primary); border:none; border-radius:10px; margin-top:10px; font-weight:bold;">SEND TO ALL</button>
        <h3 style="color:var(--primary); margin-top:30px;">MANAGE USERS</h3>
        <div id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:15px; background:red; color:white; border:none; border-radius:10px; margin-top:20px;">NUKE DATABASE</button>
    </div>
</div>

<nav>
    <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
    <button onclick="nav('chat', this)"><i class="fa-solid fa-comment-dots"></i></button>
    <button onclick="nav('nodes', this)"><i class="fa-solid fa-users"></i></button>
</nav>

<script>
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

if(currentUser) { document.getElementById("loginPage").style.display = "none"; initApp(currentUser); }

function doLogin() {
    var u = document.getElementById("uInp").value.trim();
    if(u) {
        db.ref("users/" + u).update({ online: true, frozen: false }).then(() => {
            localStorage.setItem("lc_u", u); location.reload();
        });
    }
}

function initApp(u) {
    document.getElementById("welcome").innerText = "Welcome, " + u;
    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    db.ref("broadcast").on("value", s => { if(s.val()) alert("📢 ADMIN:\n" + s.val().msg); });
    db.ref("users/"+u+"/frozen").on("value", s => { document.getElementById("hjkScreen").style.display = s.val() ? "flex" : "none"; });

    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList"), on = 0;
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.val().online) on++;
            if(n.key !== u) {
                ul.innerHTML += `<div class="stat-box" style="margin-bottom:10px; text-align:left;" onclick="startChat('${n.key}', false)">
                    <i class="fa-solid fa-circle" style="color:${n.val().online?'#00ff88':'#333'}; font-size:8px; margin-right:10px;"></i>${n.key}</div>`;
                if(u === "Admin786") al.innerHTML += `<div class="stat-box" style="margin-bottom:10px; display:flex; justify-content:space-between;">
                    <span>${n.key}</span><button onclick="hjk('${n.key}', ${!n.val().frozen})" style="background:${n.val().frozen?'green':'red'}; color:white; border:none; padding:5px 10px; border-radius:5px;">${n.val().frozen?'FREE':'BAN'}</button></div>`;
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
        // AUTO SCROLL TO BOTTOM
        var page = document.getElementById("chat");
        page.scrollTo({ top: page.scrollHeight, behavior: 'smooth' });
    });
}

function sendMsg() {
    var i = document.getElementById("msgInp");
    if(i.value) { db.ref(currentPath).push({ from: currentUser, txt: i.value, type: 'text' }); i.value = ""; }
}

function sendBC() {
    var m = document.getElementById("bcMsg").value;
    if(m) db.ref("broadcast").set({ msg: m }).then(() => { document.getElementById("bcMsg").value = ""; });
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
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
}

function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function nuke() { if(confirm("NUKE ALL?")) db.ref().remove(); }
function mkGr() { var n = prompt("Group Name:"); if(n) db.ref("groups/"+n).set({ c: 1 }); }
function logout() { localStorage.clear(); location.reload(); }
</script>
</body>
</html>
