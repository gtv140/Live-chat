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
            --text: #e6edf3; --success: #00ff88; --glass: rgba(13, 17, 23, 0.8); 
            --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
        }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); height: 100dvh; overflow: hidden; }
        
        /* LOGIN UI */
        #loginPage { position: fixed; inset: 0; background: radial-gradient(circle at top, #1a1a2e, #000); z-index: 10000; display: flex; align-items: center; justify-content: center; }
        .login-card { background: var(--card); padding: 40px; border-radius: 35px; width: 85%; max-width: 380px; text-align: center; border: 1px solid #30363d; }
        .login-card i { font-size: 60px; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 20px; }
        .login-card input { width: 100%; padding: 18px; margin: 20px 0; border-radius: 15px; border: 1px solid #30363d; background: #010409; color: #fff; text-align: center; font-size: 16px; outline: none; box-sizing: border-box; }
        .login-card button { width: 100%; padding: 18px; border-radius: 15px; border: none; background: var(--grad); color: white; font-weight: 800; cursor: pointer; }

        /* HEADER */
        header { padding: 45px 20px 15px; display: flex; justify-content: space-between; align-items: center; background: var(--glass); border-bottom: 1px solid #30363d; }
        header h1 { margin: 0; font-size: 24px; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 900; }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; }
        .page.active { display: flex; flex-direction: column; }

        /* DASHBOARD */
        .hero { background: var(--grad); padding: 35px 20px; border-radius: 30px; text-align: center; margin-bottom: 25px; }
        .stat-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .stat-box { background: var(--card); padding: 20px; border-radius: 20px; text-align: center; border: 1px solid #30363d; }
        .stat-box span { display: block; font-size: 26px; font-weight: 800; color: var(--primary); }

        /* CHAT */
        #chatBox { flex: 1; display: flex; flex-direction: column; gap: 10px; }
        .msg { padding: 12px 16px; border-radius: 20px; max-width: 80%; font-size: 14px; position: relative; }
        .msg.me { align-self: flex-end; background: var(--grad); color: #fff; border-bottom-right-radius: 4px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 4px; }
        .msg b { display: block; font-size: 10px; opacity: 0.6; margin-bottom: 4px; }
        .msg img { max-width: 100%; border-radius: 12px; margin-top: 8px; }

        .input-bar { position: fixed; bottom: 95px; width: 92%; max-width: 450px; left: 50%; transform: translateX(-50%); background: #161b22; padding: 10px 15px; border-radius: 35px; display: flex; align-items: center; gap: 10px; border: 1px solid #30363d; }
        .input-bar input { flex: 1; background: none; border: none; color: white; outline: none; }

        /* NAV */
        nav { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); width: 92%; max-width: 450px; background: var(--glass); backdrop-filter: blur(20px); height: 75px; border-radius: 35px; display: flex; justify-content: space-around; align-items: center; border: 1px solid rgba(255,255,255,0.1); }
        nav button { background: none; border: none; color: #484f58; font-size: 22px; cursor: pointer; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: translateY(-5px); }

        /* BROADCAST BOX */
        .broadcast-card { background: #1a1a2e; border: 1px solid var(--primary); padding: 15px; border-radius: 15px; margin-bottom: 20px; }
        .broadcast-card input { width: 100%; padding: 10px; border-radius: 10px; border: 1px solid #333; background: #000; color: #fff; margin-bottom: 10px; }
        
        #hjkScreen { display: none; position: fixed; inset: 0; background: #000; z-index: 100000; flex-direction: column; align-items: center; justify-content: center; text-align: center; color: var(--accent); }
    </style>
</head>
<body>

<div id="loginPage">
    <div class="login-card">
        <i class="fa-solid fa-satellite-dish"></i>
        <h1>Live Connect</h1>
        <input type="text" id="uInp" placeholder="Enter Username...">
        <button id="lBtn" onclick="doLogin()">Initialize Link</button>
    </div>
</div>

<div id="hjkScreen">
    <i class="fa-solid fa-user-lock" style="font-size:80px; margin-bottom:20px;"></i>
    <h1>ACCOUNT BANNED</h1>
    <p>Contact Admin to restore access.</p>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div style="display:flex; gap:20px; align-items:center;">
            <i class="fa-solid fa-shield-halved" id="admIco" style="display:none; color:var(--primary); font-size:22px;" onclick="nav('admin', this)"></i>
            <i class="fa-solid fa-power-off" style="color:var(--accent); font-size:22px;" onclick="logout()"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="hero">
            <h2 id="welcome">Welcome</h2>
            <p style="font-size:12px; opacity:0.7;">V70.0 Fully Operational</p>
        </div>
        <div class="stat-grid">
            <div class="stat-box"><span id="onStats">0</span><p style="font-size:10px; margin:5px 0;">NODES</p></div>
            <div class="stat-box"><span id="grStats">0</span><p style="font-size:10px; margin:5px 0;">CLUSTERS</p></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div class="input-bar">
            <label for="imgInp"><i class="fa-solid fa-camera" style="color:var(--primary); font-size:20px;"></i></label>
            <input type="file" id="imgInp" hidden onchange="upImg(this)">
            <input id="msgInp" placeholder="Message...">
            <i class="fa-solid fa-paper-plane" style="color:var(--primary); font-size:20px;" onclick="sendMsg()"></i>
        </div>
    </div>

    <div id="nodes" class="page">
        <div id="uList"></div>
        <div id="gList" style="margin-top:20px;"></div>
    </div>

    <div id="admin" class="page">
        <h3 style="color:var(--primary);">GLOBAL BROADCAST</h3>
        <div class="broadcast-card">
            <input type="text" id="bcMsg" placeholder="Type global alert...">
            <button onclick="sendBC()" style="width:100%; padding:10px; background:var(--primary); border:none; border-radius:10px; font-weight:bold;">SEND TO ALL</button>
        </div>
        <h3 style="color:var(--primary);">USER CONTROL</h3>
        <div id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:20px; background:var(--accent); color:white; border:none; border-radius:15px; font-weight:bold; margin-top:20px;">NUCLEAR WIPE</button>
    </div>

    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-comment-dots"></i></button>
        <button onclick="nav('nodes', this)"><i class="fa-solid fa-users"></i></button>
    </nav>
</div>

<script>
// YOUR CONFIG
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
    if(!u) return;
    db.ref("users/" + u).update({ online: true, frozen: false, lastSeen: Date.now() }, function(err){
        if(!err) { localStorage.setItem("lc_u", u); location.reload(); }
    });
}

function initApp(u) {
    document.getElementById("welcome").innerText = "Welcome, " + u;
    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    // Listen for Broadcast
    db.ref("broadcast").on("value", s => {
        if(s.val() && s.val().msg) alert("📢 GLOBAL ALERT:\n\n" + s.val().msg);
    });

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
                    <span>${n.key}</span><button onclick="hjk('${n.key}', ${!n.val().frozen})" style="background:${n.val().frozen?'green':'red'}; color:white; border:none; padding:5px 10px; border-radius:5px;">${n.val().frozen?'UNBAN':'BAN'}</button>
                </div>`;
            }
        });
        document.getElementById("onStats").innerText = on;
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        document.getElementById("grStats").innerText = s.numChildren() || 0;
        s.forEach(g => { gl.innerHTML += `<div class="stat-box" style="margin-bottom:10px;" onclick="startChat('${g.key}', true)"># ${g.key}</div>`; });
    });
}

var currentPath = "global";
function startChat(t, isG) {
    currentPath = isG ? "groups/"+t : "pvt/"+[currentUser, t].sort().join("_");
    nav('chat', document.querySelectorAll('nav button')[1]);
    db.ref(currentPath).off();
    db.ref(currentPath).on("value", s => {
        var box = document.getElementById("chatBox"); box.innerHTML = "";
        s.forEach(m => {
            var d = m.val();
            var del = (currentUser === "Admin786") ? `<i class="fa-solid fa-trash" onclick="delMsg('${m.key}')" style="margin-left:10px; color:red; font-size:10px;"></i>` : "";
            box.innerHTML += `<div class="msg ${d.from===currentUser?'me':'other'}"><b>${d.from}</b>${d.type==='img'?'<img src="'+d.txt+'">':d.txt}${del}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}

function sendMsg() {
    var i = document.getElementById("msgInp");
    if(i.value) { db.ref(currentPath).push({ from: currentUser, txt: i.value, type: 'text' }); i.value = ""; }
}

function sendBC() {
    var m = document.getElementById("bcMsg").value;
    if(m) db.ref("broadcast").set({ msg: m, time: Date.now() }).then(() => {
        document.getElementById("bcMsg").value = "";
        alert("Broadcast Sent!");
    });
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
function delMsg(k) { if(confirm("Delete message?")) db.ref(currentPath+"/"+k).remove(); }
function nuke() { if(confirm("NUCLEAR WIPE? EVERYTHING WILL BE DELETED!")) db.ref().remove(); }
function mkGr() { var n = prompt("Cluster Name:"); if(n) db.ref("groups/"+n).set({ c: Date.now() }); }
function logout() { localStorage.clear(); location.reload(); }
</script>
</body>
</html>
