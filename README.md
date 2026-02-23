<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Auto-Scroll 🚀</title>
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

        header { padding: 50px 20px 15px; flex-shrink: 0; background: var(--bg); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #1a1d21; }
        header h1 { margin: 0; font-size: 20px; font-weight: 800; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        .viewport { flex: 1; overflow: hidden; position: relative; display: flex; flex-direction: column; }
        .page { display: none; height: 100%; flex-direction: column; padding: 15px; }
        .page.active { display: flex; }

        /* FIXED CHAT BOX WITH AUTO SCROLL */
        #chatScroll { 
            flex: 1; 
            overflow-y: auto; 
            padding: 10px; 
            background: rgba(255,255,255,0.02); 
            border-radius: 20px; 
            border: 1px solid #21262d;
            scroll-behavior: smooth; /* Smooth scrolling effect */
        }
        #chatBox { display: flex; flex-direction: column; gap: 10px; }

        .bubble { padding: 12px 16px; border-radius: 18px; max-width: 80%; font-size: 14px; position: relative; }
        .bubble.me { align-self: flex-end; background: var(--grad); border-bottom-right-radius: 2px; color: white; }
        .bubble.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }
        
        /* OTHER UI ELEMENTS */
        .content-box { flex: 1; overflow-y: auto; background: rgba(255,255,255,0.02); border-radius: 20px; border: 1px solid #21262d; padding: 10px; }
        .card { display: flex; align-items: center; gap: 12px; background: var(--card); padding: 14px; border-radius: 18px; margin-bottom: 8px; border: 1px solid #30363d; }
        .pfp { width: 35px; height: 35px; border-radius: 50%; background: var(--grad); display: flex; align-items: center; justify-content: center; font-weight: bold; }

        .footer { flex-shrink: 0; padding: 10px 0 35px 0; background: var(--bg); }
        .input-area { width: 92%; margin: 0 auto 12px auto; background: #161b22; padding: 12px; border-radius: 30px; display: none; align-items: center; gap: 10px; border: 1px solid #30363d; }
        .input-area input { flex: 1; background: none; border: none; color: white; outline: none; }

        nav { width: 92%; margin: 0 auto; height: 70px; background: rgba(13,17,23,0.9); backdrop-filter: blur(15px); border-radius: 35px; display: flex; justify-content: space-around; align-items: center; border: 1px solid #30363d; }
        nav button { background: none; border: none; color: #484f58; font-size: 22px; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: scale(1.1); }

        #loginOverlay { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; }
    </style>
</head>
<body>

<div id="loginOverlay">
    <div style="width:80%; text-align:center;">
        <h2 style="background:var(--grad); -webkit-background-clip:text; -webkit-text-fill-color:transparent; font-size:30px;">Live Connect</h2>
        <input type="text" id="uInp" placeholder="Username" style="width:100%; padding:15px; border-radius:15px; background:#111; border:1px solid #333; color:white; margin:20px 0; text-align:center;">
        <button onclick="doLogin()" style="width:100%; padding:15px; border-radius:15px; background:var(--grad); color:white; border:none; font-weight:bold;">LOGIN</button>
    </div>
</div>

<header>
    <h1>Live Connect</h1>
    <div style="display:flex; gap:15px;">
        <i class="fa-solid fa-crown" id="admIco" style="display:none; color:gold;" onclick="nav('admin')"></i>
        <i class="fa-solid fa-power-off" style="color:var(--accent);" onclick="logout()"></i>
    </div>
</header>

<div class="viewport">
    <div id="home" class="page active">
        <div style="background:var(--grad); padding:25px; border-radius:25px; margin-bottom:15px;">
            <h2 id="welcome" style="margin:0;">Welcome</h2>
        </div>
        <div class="content-box">System is online and secure.</div>
    </div>

    <div id="chat" class="page">
        <div id="chatScroll">
            <div id="chatBox"></div>
            <div id="scrollAnchor"></div>
        </div>
    </div>

    <div id="nodes" class="page">
        <div class="content-box" id="uList"></div>
        <div style="margin:10px 5px; font-size:10px; opacity:0.5;">CLUSTERS</div>
        <div class="content-box" id="gList"></div>
    </div>

    <div id="admin" class="page">
        <div class="content-box" id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:15px; background:red; border:none; border-radius:15px; color:white; font-weight:bold; margin-top:10px;">NUKE ALL DATA</button>
    </div>
</div>

<div class="footer">
    <div class="input-area" id="chatInputArea">
        <input id="msgInp" placeholder="Type a message...">
        <i class="fa-solid fa-paper-plane" style="color:var(--primary); font-size:20px;" onclick="sendMsg()"></i>
    </div>
    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-comment"></i></button>
        <button onclick="nav('nodes', this)"><i class="fa-solid fa-users"></i></button>
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

if(currentUser) { document.getElementById("loginOverlay").style.display = "none"; initApp(currentUser); }

function doLogin() {
    var u = document.getElementById("uInp").value.trim();
    if(u) { db.ref("users/"+u).update({ online: true, frozen: false }).then(() => { localStorage.setItem("lc_u", u); location.reload(); }); }
}

function initApp(u) {
    document.getElementById("welcome").innerText = "Hi, " + u;
    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList");
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.key !== u) {
                ul.innerHTML += `<div class="card" onclick="startChat('${n.key}', false)"><div class="pfp">${n.key[0]}</div><b>${n.key}</b></div>`;
                al.innerHTML += `<div class="card"><b>${n.key}</b><button onclick="hjk('${n.key}', ${!n.val().frozen})" style="margin-left:auto; background:${n.val().frozen?'green':'red'}; color:white; border:none; padding:5px 10px; border-radius:8px;">${n.val().frozen?'FREE':'BAN'}</button></div>`;
            }
        });
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        s.forEach(g => { gl.innerHTML += `<div class="card" onclick="startChat('${g.key}', true)"><b># ${g.key}</b></div>`; });
    });
}

var currentPath = "global";
function startChat(t, isG) {
    currentPath = isG ? "groups/"+t : "pvt/"+[currentUser, t].sort().join("_");
    nav('chat', document.querySelectorAll('nav button')[1]);
    db.ref(currentPath).off();
    db.ref(currentPath).on("value", s => {
        var box = document.getElementById("chatBox"); 
        box.innerHTML = "";
        s.forEach(m => {
            var d = m.val();
            box.innerHTML += `<div class="bubble ${d.from===currentUser?'me':'other'}"><b>${d.from}</b><br>${d.txt}</div>`;
        });
        
        // --- AUTO SCROLL LOGIC ---
        setTimeout(() => {
            var scrollDiv = document.getElementById("chatScroll");
            scrollDiv.scrollTop = scrollDiv.scrollHeight; 
        }, 100);
    });
}

function sendMsg() {
    var i = document.getElementById("msgInp");
    if(i.value) {
        db.ref(currentPath).push({ from: currentUser, txt: i.value });
        i.value = "";
    }
}

function nav(p, b) {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
    document.getElementById("chatInputArea").style.display = (p === 'chat') ? 'flex' : 'none';
}

function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function logout() { db.ref("users/"+currentUser).update({online: false}).then(() => { localStorage.clear(); location.reload(); }); }
function nuke() { if(confirm("NUKE ALL?")) db.ref().remove(); }
</script>
</body>
</html>
