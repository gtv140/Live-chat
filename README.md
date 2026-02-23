<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Gold 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <style>
        :root { 
            --bg: #030508; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; 
            --text: #e6edf3; --glass: rgba(13, 17, 23, 0.98); 
            --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
        }

        /* FLEXBOX LAYOUT FIX */
        * { box-sizing: border-box; }
        body { 
            margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); 
            height: 100vh; display: flex; flex-direction: column; overflow: hidden; 
        }
        
        /* LOGIN PAGE */
        #loginPage { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; }
        .login-card { background: var(--card); padding: 35px; border-radius: 30px; width: 85%; max-width: 380px; text-align: center; border: 1px solid #30363d; }
        .login-card input { width: 100%; padding: 15px; margin: 20px 0; border-radius: 12px; border: 1px solid #30363d; background: #010409; color: #fff; text-align: center; outline: none; }
        .login-card button { width: 100%; padding: 15px; border-radius: 12px; border: none; background: var(--grad); color: white; font-weight: bold; }

        /* HEADER */
        header { 
            height: 110px; padding: 50px 20px 10px; flex-shrink: 0; 
            background: var(--bg); border-bottom: 1px solid #30363d; 
            display: flex; justify-content: space-between; align-items: center; 
        }
        header h1 { margin: 0; font-size: 20px; font-weight: 900; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* SCROLL AREA FIX */
        .main-container { 
            flex-grow: 1; overflow-y: auto; overflow-x: hidden; 
            padding: 20px; -webkit-overflow-scrolling: touch; 
            position: relative; z-index: 1;
        }
        .page { display: none; width: 100%; }
        .page.active { display: block; }

        /* CARDS & UI */
        .card { background: var(--card); padding: 20px; border-radius: 20px; border: 1px solid #30363d; margin-bottom: 15px; }
        .hero { background: var(--grad); padding: 25px; border-radius: 25px; text-align: center; margin-bottom: 20px; box-shadow: 0 10px 20px rgba(0,0,0,0.3); }
        
        /* CHAT BUBBLES */
        #chatBox { display: flex; flex-direction: column; gap: 10px; width: 100%; padding-bottom: 20px; }
        .msg { padding: 12px 15px; border-radius: 18px; max-width: 85%; font-size: 14px; line-height: 1.4; word-wrap: break-word; }
        .msg.me { align-self: flex-end; background: var(--grad); color: white; border-bottom-right-radius: 2px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }

        /* INPUT AREA FIX */
        .footer-tools { 
            height: 140px; flex-shrink: 0; display: flex; flex-direction: column; 
            align-items: center; justify-content: flex-start; padding-top: 5px; 
            background: transparent; pointer-events: none; 
        }
        .input-bar { 
            pointer-events: auto; width: 90%; max-width: 450px; background: #161b22; 
            padding: 10px 15px; border-radius: 30px; display: none; align-items: center; 
            gap: 12px; border: 1px solid #30363d; margin-bottom: 10px; 
        }
        .input-bar input { flex: 1; background: none; border: none; color: white; outline: none; }

        /* NAVIGATION BAR */
        nav { 
            pointer-events: auto; width: 92%; max-width: 450px; background: var(--glass); 
            backdrop-filter: blur(15px); height: 70px; border-radius: 30px; 
            display: flex; justify-content: space-around; align-items: center; 
            border: 1px solid #30363d; box-shadow: 0 -5px 25px rgba(0,0,0,0.5); 
        }
        nav button { background: none; border: none; color: #484f58; font-size: 20px; padding: 10px; }
        nav button.active { color: var(--primary); transform: translateY(-5px); transition: 0.3s; }

        /* ADMIN */
        .ban-btn { background: var(--accent); color: white; border: none; padding: 8px 15px; border-radius: 8px; font-weight: bold; }
        
        #hjkScreen { display: none; position: fixed; inset: 0; background: #000; z-index: 100000; flex-direction: column; align-items: center; justify-content: center; }
    </style>
</head>
<body>

<div id="loginPage">
    <div class="login-card">
        <i class="fa-solid fa-satellite-dish" style="font-size: 50px; color: var(--primary);"></i>
        <h1>Live Connect</h1>
        <input type="text" id="uInp" placeholder="Enter Username...">
        <button onclick="doLogin()">INITIALIZE LINK</button>
    </div>
</div>

<div id="hjkScreen">
    <i class="fa-solid fa-user-lock" style="font-size:80px; color:red; margin-bottom:20px;"></i>
    <h1>BANNED BY ADMIN</h1>
</div>

<header>
    <h1>Live Connect</h1>
    <div style="display:flex; gap:18px; align-items:center;">
        <i class="fa-solid fa-crown" id="admIco" style="display:none; color: gold; font-size:18px;" onclick="nav('admin')"></i>
        <i class="fa-solid fa-power-off" style="color:var(--accent); font-size:18px;" onclick="logout()"></i>
    </div>
</header>

<div class="main-container" id="scrollArea">
    <div id="home" class="page active">
        <div class="hero">
            <h2 id="welcome">Welcome</h2>
            <p style="font-size:11px; opacity:0.8;">Secure Shell V110.0</p>
        </div>
        <div style="display:grid; grid-template-columns:1fr 1fr; gap:15px;">
            <div class="card" style="text-align:center;"><i class="fa-solid fa-bolt" style="color:var(--primary);"></i><h3 id="onStats" style="margin:10px 0 5px 0;">0</h3><p style="font-size:10px; margin:0;">NODES</p></div>
            <div class="card" style="text-align:center;"><i class="fa-solid fa-shield-cat" style="color:var(--primary);"></i><h3 id="grStats" style="margin:10px 0 5px 0;">0</h3><p style="font-size:10px; margin:0;">CLUSTERS</p></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
    </div>

    <div id="nodes" class="page">
        <h4 style="opacity:0.4; font-size:11px; letter-spacing:1px;">ACTIVE TERMINALS</h4>
        <div id="uList"></div>
        <div style="display:flex; justify-content:space-between; margin:25px 0 10px 0; align-items:center;">
            <h4 style="opacity:0.4; font-size:11px; letter-spacing:1px;">CLUSTERS</h4>
            <button onclick="mkGr()" style="background:none; border:1px solid var(--primary); color:var(--primary); padding:3px 10px; border-radius:6px; font-size:10px;">+ NEW</button>
        </div>
        <div id="gList"></div>
    </div>

    <div id="admin" class="page">
        <h4 style="color:var(--primary); font-size:12px;">GLOBAL BROADCAST</h4>
        <div class="card">
            <input type="text" id="bcMsg" placeholder="Type alert..." style="width:100%; padding:12px; background:#000; border:1px solid #333; color:#fff; border-radius:10px; outline:none;">
            <button onclick="sendBC()" style="width:100%; padding:12px; background:var(--primary); border:none; border-radius:10px; margin-top:10px; font-weight:bold;">SEND TO ALL</button>
        </div>
        <h4 style="color:var(--primary); font-size:12px; margin-top:20px;">USER MANAGEMENT</h4>
        <div id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:18px; background:red; border:none; border-radius:15px; font-weight:bold; margin-top:20px;">NUKE DATABASE</button>
    </div>
</div>

<div class="footer-tools">
    <div class="input-bar" id="chatInputArea">
        <label for="imgInp"><i class="fa-solid fa-image" style="color:var(--primary); font-size:20px;"></i></label>
        <input type="file" id="imgInp" hidden onchange="upImg(this)">
        <input id="msgInp" placeholder="Enter packet...">
        <i class="fa-solid fa-paper-plane" style="color:var(--primary); font-size:20px;" onclick="sendMsg()"></i>
    </div>

    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-comment-dots"></i></button>
        <button onclick="nav('nodes', this)"><i class="fa-solid fa-fingerprint"></i></button>
    </nav>
</div>

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
    if(u) { db.ref("users/"+u).update({ online: true, frozen: false }).then(() => { localStorage.setItem("lc_u", u); location.reload(); }); }
}

function initApp(u) {
    document.getElementById("welcome").innerText = "Welcome, " + u;
    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    db.ref("broadcast").on("value", s => { if(s.val() && s.val().msg) alert("📢 BROADCAST:\n" + s.val().msg); });
    db.ref("users/"+u+"/frozen").on("value", s => { document.getElementById("hjkScreen").style.display = s.val() ? "flex" : "none"; });

    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList"), on = 0;
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.val().online) on++;
            if(n.key !== u) {
                ul.innerHTML += `<div class="card" style="margin-bottom:10px; display:flex; justify-content:space-between; align-items:center;" onclick="startChat('${n.key}', false)">
                    <span><i class="fa-solid fa-circle" style="color:${n.val().online?'#00ff88':'#333'}; font-size:8px; margin-right:10px;"></i>${n.key}</span><i class="fa-solid fa-chevron-right" style="opacity:0.2"></i></div>`;
                if(u === "Admin786") al.innerHTML += `<div class="card" style="margin-bottom:10px; display:flex; justify-content:space-between; align-items:center;">
                    <span>${n.key}</span><button class="ban-btn" onclick="hjk('${n.key}', ${!n.val().frozen})" style="background:${n.val().frozen?'#00ff88':'#ff0055'}">${n.val().frozen?'UNBAN':'BAN'}</button></div>`;
            }
        });
        document.getElementById("onStats").innerText = on;
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        document.getElementById("grStats").innerText = s.numChildren() || 0;
        s.forEach(g => { gl.innerHTML += `<div class="card" style="margin-bottom:10px;" onclick="startChat('${g.key}', true)"># ${g.key}</div>`; });
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
            box.innerHTML += `<div class="msg ${d.from===currentUser?'me':'other'}"><b>${d.from}</b>${d.type==='img'?'<img src="'+d.txt+'" style="max-width:100%; border-radius:10px; margin-top:5px;">':d.txt}</div>`;
        });
        document.getElementById("scrollArea").scrollTo({ top: 999999, behavior: 'smooth' });
    });
}

function sendMsg() {
    var i = document.getElementById("msgInp");
    if(i.value) { db.ref(currentPath).push({ from: currentUser, txt: i.value, type: 'text' }); i.value = ""; }
}

function sendBC() {
    var m = document.getElementById("bcMsg").value;
    if(m) { db.ref("broadcast").set({ msg: m }).then(() => { document.getElementById("bcMsg").value = ""; alert("Broadcast Sent!"); }); }
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
function nuke() { if(confirm("DELETE ALL DATA?")) db.ref().remove(); }
function mkGr() { var n = prompt("Group Name:"); if(n) db.ref("groups/"+n).set({ c: 1 }); }
function logout() { db.ref("users/"+currentUser).update({online: false}).then(() => { localStorage.clear(); location.reload(); }); }
</script>
</body>
</html>
