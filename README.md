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
            --text: #e6edf3; --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); height: 100vh; display: flex; flex-direction: column; overflow: hidden; }

        /* HEADER */
        header { padding: 50px 20px 15px; flex-shrink: 0; background: var(--bg); border-bottom: 1px solid #30363d; display: flex; justify-content: space-between; align-items: center; }
        header h1 { margin: 0; font-size: 20px; font-weight: 900; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* DYNAMIC CONTENT AREA */
        .page-wrapper { flex: 1; position: relative; overflow: hidden; display: flex; flex-direction: column; }
        .page { display: none; height: 100%; width: 100%; flex-direction: column; padding: 15px; }
        .page.active { display: flex; }

        /* SCROLLABLE BOXES (ALAG ALAG SYSTEM) */
        .scroll-box { 
            flex: 1; overflow-y: auto; background: rgba(255,255,255,0.02); 
            border: 1px solid #30363d; border-radius: 20px; padding: 10px;
            margin-bottom: 10px; scroll-behavior: smooth;
        }
        .box-title { font-size: 11px; opacity: 0.5; margin: 5px 0 10px 10px; letter-spacing: 1px; }

        /* CARDS */
        .user-card { display: flex; justify-content: space-between; align-items: center; background: var(--card); padding: 12px 15px; border-radius: 15px; margin-bottom: 8px; border: 1px solid #21262d; }
        .ban-btn { background: var(--accent); color: white; border: none; padding: 8px 12px; border-radius: 8px; font-weight: bold; font-size: 12px; }

        /* FOOTER & NAV */
        .bottom-nav-area { flex-shrink: 0; padding-bottom: 25px; background: var(--bg); }
        nav { width: 92%; max-width: 450px; margin: 0 auto; background: var(--card); height: 70px; border-radius: 35px; display: flex; justify-content: space-around; align-items: center; border: 1px solid #30363d; }
        nav button { background: none; border: none; color: #484f58; font-size: 20px; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: scale(1.1); }

        /* CHAT INPUT */
        .input-bar { width: 92%; max-width: 450px; margin: 0 auto 10px auto; background: #161b22; padding: 12px; border-radius: 30px; display: none; align-items: center; gap: 10px; border: 1px solid #30363d; }
        .input-bar input { flex: 1; background: none; border: none; color: white; outline: none; }

        /* LOGIN & BANNED */
        #loginPage { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; }
        #hjkScreen { display: none; position: fixed; inset: 0; background: #000; z-index: 100000; flex-direction: column; align-items: center; justify-content: center; color: red; }
    </style>
</head>
<body>

<div id="loginPage">
    <div style="background:var(--card); padding:35px; border-radius:30px; width:85%; max-width:380px; text-align:center; border:1px solid #30363d;">
        <i class="fa-solid fa-satellite-dish" style="font-size:45px; color:var(--primary);"></i>
        <h2>Live Connect</h2>
        <input type="text" id="uInp" placeholder="Username..." style="width:100%; padding:15px; margin:20px 0; border-radius:12px; background:#000; border:1px solid #333; color:#fff; text-align:center;">
        <button onclick="doLogin()" style="width:100%; padding:15px; border-radius:12px; background:var(--grad); color:white; border:none; font-weight:bold;">CONNECT</button>
    </div>
</div>

<div id="hjkScreen"><h1>BANNED</h1></div>

<header>
    <h1>Live Connect</h1>
    <div style="display:flex; gap:20px;">
        <i class="fa-solid fa-crown" id="admIco" style="display:none; color:gold;" onclick="nav('admin')"></i>
        <i class="fa-solid fa-power-off" style="color:var(--accent);" onclick="logout()"></i>
    </div>
</header>

<div class="page-wrapper">
    <div id="home" class="page active">
        <div style="background:var(--grad); padding:25px; border-radius:25px; text-align:center; margin-bottom:15px;">
            <h2 id="welcome" style="margin:0;">Welcome</h2>
        </div>
        <div class="scroll-box">
            <div class="box-title">ANNOUNCEMENTS</div>
            <div id="annBox" style="padding:10px; font-size:13px; opacity:0.8;">No new updates.</div>
        </div>
    </div>

    <div id="chat" class="page">
        <div class="scroll-box" id="chatScroll">
            <div id="chatBox"></div>
        </div>
    </div>

    <div id="nodes" class="page">
        <div class="box-title">USERS ONLINE</div>
        <div class="scroll-box" id="uList"></div>
        
        <div style="display:flex; justify-content:space-between; align-items:center;">
            <div class="box-title">GROUPS</div>
            <button onclick="mkGr()" style="background:none; border:1px solid var(--primary); color:var(--primary); font-size:10px; padding:2px 8px; border-radius:5px; margin-bottom:5px;">+ NEW</button>
        </div>
        <div class="scroll-box" id="gList"></div>
    </div>

    <div id="admin" class="page">
        <div class="box-title">BROADCAST CENTER</div>
        <div class="user-card">
            <input type="text" id="bcMsg" placeholder="Type alert..." style="flex:1; background:none; border:none; color:white; outline:none;">
            <button onclick="sendBC()" style="background:var(--primary); border:none; padding:8px 15px; border-radius:10px; font-weight:bold;">SEND</button>
        </div>
        
        <div class="box-title">MANAGE TERMINALS</div>
        <div class="scroll-box" id="aList"></div>
        
        <button onclick="nuke()" style="width:100%; padding:15px; background:red; border:none; border-radius:15px; font-weight:bold; color:white;">NUKE ALL DATA</button>
    </div>
</div>

<div class="bottom-nav-area">
    <div class="input-bar" id="chatInputArea">
        <label for="imgInp"><i class="fa-solid fa-camera" style="color:var(--primary); font-size:20px;"></i></label>
        <input type="file" id="imgInp" hidden onchange="upImg(this)">
        <input id="msgInp" placeholder="Type message...">
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
    document.getElementById("welcome").innerText = "Hi, " + u;
    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    db.ref("broadcast").on("value", s => { if(s.val()) document.getElementById("annBox").innerText = s.val().msg; });
    db.ref("users/"+u+"/frozen").on("value", s => { document.getElementById("hjkScreen").style.display = s.val() ? "flex" : "none"; });

    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList");
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.key !== u) {
                ul.innerHTML += `<div class="user-card" onclick="startChat('${n.key}', false)"><span>${n.key}</span><i class="fa-solid fa-circle" style="color:${n.val().online?'#00ff88':'#333'}; font-size:8px;"></i></div>`;
                if(u === "Admin786") al.innerHTML += `<div class="user-card"><span>${n.key}</span><button class="ban-btn" onclick="hjk('${n.key}', ${!n.val().frozen})" style="background:${n.val().frozen?'green':'red'}">${n.val().frozen?'FREE':'BAN'}</button></div>`;
            }
        });
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        s.forEach(g => { gl.innerHTML += `<div class="user-card" onclick="startChat('${g.key}', true)"># ${g.key}</div>`; });
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
            box.innerHTML += `<div class="msg ${d.from===currentUser?'me':'other'}"><b>${d.from}</b>${d.type==='img'?'<img src="'+d.txt+'" style="width:100%; border-radius:10px;">':d.txt}</div>`;
        });
        document.getElementById("chatScroll").scrollTop = 999999;
    });
}

function nav(p, b) {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
    document.getElementById("chatInputArea").style.display = (p === 'chat') ? 'flex' : 'none';
}

function sendMsg() { var i = document.getElementById("msgInp"); if(i.value) { db.ref(currentPath).push({ from: currentUser, txt: i.value, type: 'text' }); i.value = ""; } }
function sendBC() { var m = document.getElementById("bcMsg").value; if(m) { db.ref("broadcast").set({ msg: m }); document.getElementById("bcMsg").value = ""; } }
function upImg(e) { var r = new FileReader(); r.onload = f => db.ref(currentPath).push({ from: currentUser, txt: f.target.result, type: 'img' }); r.readAsDataURL(e.files[0]); }
function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function logout() { db.ref("users/"+currentUser).update({online: false}).then(() => { localStorage.clear(); location.reload(); }); }
function mkGr() { var n = prompt("Group Name:"); if(n) db.ref("groups/"+n).set({ c: 1 }); }
function nuke() { if(confirm("NUKE EVERYTHING?")) db.ref().remove(); }
</script>
</body>
</html>
