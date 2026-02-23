<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Ultra 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <style>
        :root { 
            --bg: #030508; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; 
            --text: #e6edf3; --success: #00ff88; --glass: rgba(13, 17, 23, 0.95); 
            --grad: linear-gradient(135deg, #00d2ff 0%, #9d50bb 100%);
        }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); height: 100vh; overflow: hidden; display: flex; flex-direction: column; }
        
        /* LOGIN PAGE */
        #loginPage { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; }
        .login-card { background: var(--card); padding: 40px; border-radius: 30px; width: 85%; max-width: 380px; text-align: center; border: 1px solid #30363d; }
        .login-card input { width: 100%; padding: 15px; margin: 20px 0; border-radius: 12px; border: 1px solid #30363d; background: #010409; color: #fff; text-align: center; outline: none; }
        .login-card button { width: 100%; padding: 15px; border-radius: 12px; border: none; background: var(--grad); color: white; font-weight: bold; cursor: pointer; }

        /* HEADER */
        header { padding: 45px 20px 15px; background: var(--glass); border-bottom: 1px solid #30363d; display: flex; justify-content: space-between; align-items: center; z-index: 100; }
        header h1 { margin: 0; font-size: 22px; font-weight: 900; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* SCROLLABLE CONTENT */
        .main-content { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 180px; scroll-behavior: smooth; }
        .page { display: none; }
        .page.active { display: block; }

        /* DASHBOARD CARDS */
        .hero { background: var(--grad); padding: 30px; border-radius: 25px; text-align: center; margin-bottom: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        .stat-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .stat-box { background: var(--card); padding: 20px; border-radius: 20px; text-align: center; border: 1px solid #30363d; }
        .stat-box i { font-size: 24px; color: var(--primary); margin-bottom: 10px; display: block; }

        /* CHAT SYSTEM */
        #chatBox { display: flex; flex-direction: column; gap: 10px; }
        .msg { padding: 12px 16px; border-radius: 18px; max-width: 80%; font-size: 14px; position: relative; line-height: 1.4; }
        .msg.me { align-self: flex-end; background: var(--grad); color: white; border-bottom-right-radius: 2px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }
        .msg b { display: block; font-size: 10px; opacity: 0.7; margin-bottom: 4px; }
        .msg img { max-width: 100%; border-radius: 10px; margin-top: 5px; }

        /* INPUT BAR */
        .input-bar { position: fixed; bottom: 90px; width: 90%; max-width: 450px; left: 50%; transform: translateX(-50%); background: #161b22; padding: 10px 15px; border-radius: 30px; display: flex; align-items: center; gap: 10px; border: 1px solid #30363d; box-shadow: 0 10px 25px rgba(0,0,0,0.5); }
        .input-bar input { flex: 1; background: none; border: none; color: white; outline: none; padding: 5px; }

        /* NAVIGATION */
        nav { position: fixed; bottom: 15px; left: 50%; transform: translateX(-50%); width: 92%; max-width: 450px; background: var(--glass); backdrop-filter: blur(20px); height: 70px; border-radius: 30px; display: flex; justify-content: space-around; align-items: center; border: 1px solid #30363d; z-index: 1000; }
        nav button { background: none; border: none; color: #484f58; font-size: 22px; cursor: pointer; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: translateY(-5px); }

        /* ONLINE DOT */
        .dot { width: 8px; height: 8px; border-radius: 50%; display: inline-block; margin-right: 8px; }
        .online { background: var(--success); box-shadow: 0 0 10px var(--success); }

        #hjkScreen { display: none; position: fixed; inset: 0; background: #000; z-index: 100000; flex-direction: column; align-items: center; justify-content: center; color: var(--accent); text-align: center; }
    </style>
</head>
<body>

<div id="loginPage">
    <div class="login-card">
        <i class="fa-solid fa-bolt" style="font-size: 50px; color: var(--primary);"></i>
        <h1 style="color:white; margin:10px 0;">Live Connect</h1>
        <input type="text" id="uInp" placeholder="Enter Username...">
        <button onclick="doLogin()">CONNECT NOW</button>
    </div>
</div>

<div id="hjkScreen">
    <i class="fa-solid fa-ban" style="font-size:80px; margin-bottom:20px;"></i>
    <h1>TERMINAL BANNED</h1>
    <p>Unauthorized access detected.</p>
</div>

<header>
    <h1>Live Connect</h1>
    <div style="display:flex; gap:20px;">
        <i class="fa-solid fa-crown" id="admIco" style="display:none; color: gold; font-size:20px;" onclick="nav('admin')"></i>
        <i class="fa-solid fa-power-off" style="color:var(--accent); font-size:20px;" onclick="logout()"></i>
    </div>
</header>

<div class="main-content" id="scrollArea">
    <div id="home" class="page active">
        <div class="hero">
            <h2 id="welcome">Welcome</h2>
            <p style="font-size: 12px; opacity: 0.8;">Secure Protocol V100.0 Active</p>
        </div>
        <div class="stat-grid">
            <div class="stat-box"><i class="fa-solid fa-user-astronaut"></i><span id="onStats">0</span><p>Online</p></div>
            <div class="stat-box"><i class="fa-solid fa-layer-group"></i><span id="grStats">0</span><p>Clusters</p></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
    </div>

    <div id="nodes" class="page">
        <h4 style="opacity:0.4; letter-spacing:1px; font-size:11px; margin-bottom:15px;">ACTIVE TERMINALS</h4>
        <div id="uList"></div>
        <div style="display:flex; justify-content:space-between; margin-top:30px; align-items:center;">
            <h4 style="opacity:0.4; letter-spacing:1px; font-size:11px;">CLUSTERS</h4>
            <button onclick="mkGr()" style="background:none; border:1px solid var(--primary); color:var(--primary); padding:4px 10px; border-radius:8px; font-size:10px;">+ NEW</button>
        </div>
        <div id="gList" style="margin-top:10px;"></div>
    </div>

    <div id="admin" class="page">
        <h3 style="color:var(--primary);">GLOBAL BROADCAST</h3>
        <div style="background:var(--card); padding:15px; border-radius:15px; border:1px solid #333;">
            <input type="text" id="bcMsg" placeholder="Alert message..." style="width:100%; padding:12px; background:#000; border:1px solid #444; color:#fff; border-radius:10px; outline:none;">
            <button onclick="sendBC()" style="width:100%; padding:12px; background:var(--primary); border:none; border-radius:10px; margin-top:10px; font-weight:bold; color:#000;">SEND TO ALL</button>
        </div>
        <h3 style="color:var(--primary); margin-top:30px;">USER MANAGEMENT</h3>
        <div id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:18px; background:var(--accent); color:white; border:none; border-radius:15px; font-weight:bold; margin-top:30px;">NUCLEAR DATA WIPE</button>
    </div>
</div>

<div class="input-bar" id="chatInputArea" style="display:none;">
    <label for="imgInp"><i class="fa-solid fa-image" style="color:var(--primary); cursor:pointer;"></i></label>
    <input type="file" id="imgInp" hidden onchange="upImg(this)">
    <input id="msgInp" placeholder="Enter secure packet...">
    <i class="fa-solid fa-paper-plane" style="color:var(--primary); cursor:pointer;" onclick="sendMsg()"></i>
</div>

<nav>
    <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
    <button onclick="nav('chat', this)"><i class="fa-solid fa-comment-dots"></i></button>
    <button onclick="nav('nodes', this)"><i class="fa-solid fa-fingerprint"></i></button>
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

if(currentUser) { 
    document.getElementById("loginPage").style.display = "none"; 
    initApp(currentUser); 
}

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

    db.ref("broadcast").on("value", s => { if(s.val() && s.val().msg) alert("📢 ADMIN ALERT:\n" + s.val().msg); });
    db.ref("users/"+u+"/frozen").on("value", s => { document.getElementById("hjkScreen").style.display = s.val() ? "flex" : "none"; });

    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList"), on = 0;
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.val().online) on++;
            if(n.key !== u) {
                ul.innerHTML += `<div class="stat-box" style="margin-bottom:10px; display:flex; justify-content:space-between; align-items:center;" onclick="startChat('${n.key}', false)">
                    <span><span class="dot ${n.val().online?'online':''}"></span>${n.key}</span><i class="fa-solid fa-chevron-right" style="opacity:0.2"></i></div>`;
                if(u === "Admin786") al.innerHTML += `<div class="stat-box" style="margin-bottom:10px; display:flex; justify-content:space-between; align-items:center;">
                    <span>${n.key}</span><button onclick="hjk('${n.key}', ${!n.val().frozen})" style="background:${n.val().frozen?'#00ff88':'#ff0055'}; color:#000; border:none; padding:6px 12px; border-radius:8px; font-weight:bold;">${n.val().frozen?'RESTORE':'BAN'}</button></div>`;
            }
        });
        document.getElementById("onStats").innerText = on;
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        document.getElementById("grStats").innerText = s.numChildren() || 0;
        s.forEach(g => { gl.innerHTML += `<div class="stat-box" style="margin-bottom:10px; text-align:left;" onclick="startChat('${g.key}', true)"># ${g.key}</div>`; });
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
        var scrollArea = document.getElementById("scrollArea");
        scrollArea.scrollTop = scrollArea.scrollHeight;
    });
}

function sendMsg() {
    var i = document.getElementById("msgInp");
    if(i.value) { db.ref(currentPath).push({ from: currentUser, txt: i.value, type: 'text' }); i.value = ""; }
}

function sendBC() {
    var m = document.getElementById("bcMsg").value;
    if(m) db.ref("broadcast").set({ msg: m }).then(() => { document.getElementById("bcMsg").value = ""; alert("Sent!"); });
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
    document.getElementById("chatInputArea").style.display = (p === 'chat') ? 'flex' : 'none';
}

function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function delMsg(k) { if(confirm("Delete message?")) db.ref(currentPath+"/"+k).remove(); }
function nuke() { if(confirm("NUCLEAR WIPE? EVERYTHING WILL BE DELETED!")) db.ref().remove(); }
function mkGr() { var n = prompt("Cluster Name:"); if(n) db.ref("groups/"+n).set({ c: Date.now() }); }
function logout() { db.ref("users/"+currentUser).update({online: false}).then(() => { localStorage.clear(); location.reload(); }); }
</script>
</body>
</html>
