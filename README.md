<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <style>
        :root { --bg: #05070a; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; --text: #e6edf3; --success: #00ff88; --glass: rgba(13, 17, 23, 0.85); }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); height: 100vh; overflow: hidden; }
        
        /* LOGIN UI */
        #loginPage { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; }
        .login-card { background: var(--card); padding: 40px; border-radius: 30px; width: 85%; max-width: 380px; text-align: center; border: 1px solid #30363d; }
        .login-card i { font-size: 50px; color: var(--primary); margin-bottom: 20px; }
        .login-card input { width: 100%; padding: 15px; margin: 25px 0; border-radius: 12px; border: 1px solid #30363d; background: #010409; color: #fff; text-align: center; box-sizing: border-box; }
        .login-card button { width: 100%; padding: 16px; border-radius: 12px; border: none; background: linear-gradient(135deg, var(--primary), #bc8cff); color: white; font-weight: bold; cursor: pointer; }

        /* APP LAYOUT */
        header { padding: 50px 20px 15px; display: flex; justify-content: space-between; align-items: center; background: var(--glass); border-bottom: 1px solid #30363d; }
        header h1 { margin: 0; font-size: 22px; color: var(--primary); font-weight: 800; }
        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; }
        .page.active { display: flex; flex-direction: column; }
        
        /* DASHBOARD */
        .hero { background: linear-gradient(135deg, #2188ff, #8957e5); padding: 35px; border-radius: 25px; text-align: center; margin-bottom: 25px; }
        .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .stat-box { background: var(--card); padding: 20px; border-radius: 20px; text-align: center; border: 1px solid #30363d; }
        .stat-box span { display: block; font-size: 26px; font-weight: bold; color: var(--primary); }

        /* CHAT */
        #chatBox { flex: 1; display: flex; flex-direction: column; gap: 12px; }
        .msg { padding: 12px 16px; border-radius: 18px; max-width: 80%; font-size: 14px; position: relative; }
        .msg.me { align-self: flex-end; background: var(--primary); color: #000; }
        .msg.other { align-self: flex-start; background: #21262d; }
        .msg img { max-width: 100%; border-radius: 12px; margin-top: 8px; }

        .input-area { position: fixed; bottom: 85px; width: 90%; max-width: 440px; left: 50%; transform: translateX(-50%); background: #161b22; padding: 10px 15px; border-radius: 30px; display: flex; align-items: center; gap: 10px; border: 1px solid #30363d; }
        .input-area input { flex: 1; background: none; border: none; color: white; outline: none; }

        /* HIJACK */
        #hjkScreen { display: none; position: fixed; inset: 0; background: #000; z-index: 100000; flex-direction: column; align-items: center; justify-content: center; color: var(--accent); }

        nav { position: fixed; bottom: 15px; left: 50%; transform: translateX(-50%); width: 90%; max-width: 440px; background: var(--glass); height: 70px; border-radius: 25px; display: flex; justify-content: space-around; align-items: center; border: 1px solid #30363d; }
        nav button { background: none; border: none; color: #484f58; font-size: 22px; cursor: pointer; }
        nav button.active { color: var(--primary); }
    </style>
</head>
<body>

<div id="loginPage">
    <div class="login-card">
        <i class="fa-solid fa-satellite-dish"></i>
        <h1>Live Connect</h1>
        <input type="text" id="uInp" placeholder="Enter Username...">
        <button onclick="doLogin()">Initialize Link</button>
    </div>
</div>

<div id="hjkScreen">
    <i class="fa-solid fa-skull-crossbones" style="font-size:70px;"></i>
    <h1>HIJACKED</h1>
    <p>Access Revoked by Master Admin.</p>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div style="display:flex; gap:20px;">
            <i class="fa-solid fa-ghost" id="admIco" style="display:none; color:#bc8cff;" onclick="nav('admin', this)"></i>
            <i class="fa-solid fa-power-off" style="color:var(--accent);" onclick="logout()"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="hero">
            <h2 id="welcome">Welcome</h2>
            <p>Secure Network Active</p>
        </div>
        <div class="stats-grid">
            <div class="stat-box"><span id="onStats">0</span><h5>ONLINE</h5></div>
            <div class="stat-box"><span id="grStats">0</span><h5>CLUSTERS</h5></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div class="input-area">
            <label for="imgInp"><i class="fa-solid fa-camera" style="color:var(--primary);"></i></label>
            <input type="file" id="imgInp" hidden onchange="upImg(this)">
            <input id="msgInp" placeholder="Type message...">
            <i class="fa-solid fa-paper-plane" style="color:var(--primary);" onclick="sendMsg()"></i>
        </div>
    </div>

    <div id="nodes" class="page">
        <div id="uList"></div>
        <div id="gList" style="margin-top:20px;"></div>
    </div>

    <div id="admin" class="page">
        <h3>Master Control</h3>
        <div id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:15px; background:red; color:white; border:none; border-radius:10px; margin-top:20px;">NUCLEAR WIPE</button>
    </div>

    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-comments"></i></button>
        <button onclick="nav('nodes', this)"><i class="fa-solid fa-fingerprint"></i></button>
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
        .then(() => {
            localStorage.setItem("lc_u", u);
            location.reload();
        });
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
                ul.innerHTML += `<div class="stat-box" style="margin-bottom:10px; display:flex; justify-content:space-between;" onclick="startChat('${n.key}', false)">
                    <span>${n.key}</span><i class="fa-solid fa-circle" style="color:${n.val().online?'#00ff88':'#333'}; font-size:10px;"></i>
                </div>`;
                if(u === "Admin786") al.innerHTML += `<div class="stat-box" style="margin-bottom:10px; display:flex; justify-content:space-between;">
                    <span>${n.key}</span><button onclick="hjk('${n.key}', ${!n.val().frozen})" style="background:${n.val().frozen?'green':'red'}; color:white; border:none; border-radius:5px; padding:5px;">${n.val().frozen?'FREE':'HJK'}</button>
                </div>`;
            }
        });
        document.getElementById("onStats").innerText = on;
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        document.getElementById("grStats").innerText = s.numChildren();
        s.forEach(g => { gl.innerHTML += `<div class="stat-box" style="margin-bottom:10px;" onclick="startChat('${g.key}', true)"># ${g.key}</div>`; });
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
        db.ref(currentPath).push({ from: currentUser, txt: i.value, type: 'text' });
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
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
}

function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function nuke() { if(confirm("DELETE EVERYTHING?")) db.ref().remove(); }
function logout() { localStorage.clear(); location.reload(); }
</script>
</body>
</html>
