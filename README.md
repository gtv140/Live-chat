<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Smart Sync 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <style>
        :root { 
            --bg: #030508; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; 
            --text: #e6edf3; --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; outline: none; }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); height: 100vh; display: flex; flex-direction: column; overflow: hidden; }

        header { padding: 50px 20px 15px; flex-shrink: 0; background: var(--bg); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #1a1d21; }
        header h1 { margin: 0; font-size: 18px; font-weight: 800; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        .viewport { flex: 1; overflow: hidden; position: relative; display: flex; flex-direction: column; }
        .page { display: none; height: 100%; flex-direction: column; padding: 15px; }
        .page.active { display: flex; }

        /* SCROLLABLE CHAT BOX */
        #chatScroll { 
            flex: 1; overflow-y: auto; padding: 10px; background: rgba(255,255,255,0.02); 
            border-radius: 20px; border: 1px solid #21262d; scroll-behavior: smooth;
        }
        #chatBox { display: flex; flex-direction: column; gap: 10px; }

        /* MODERN BUBBLES */
        .bubble { padding: 10px 15px; border-radius: 18px; max-width: 80%; font-size: 14px; position: relative; line-height: 1.4; }
        .bubble.me { align-self: flex-end; background: var(--grad); border-bottom-right-radius: 2px; color: white; }
        .bubble.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }
        .msg-info { font-size: 9px; opacity: 0.7; display: flex; justify-content: flex-end; gap: 4px; margin-top: 4px; }

        /* LISTS */
        .list-box { flex: 1; overflow-y: auto; background: rgba(255,255,255,0.02); border-radius: 20px; border: 1px solid #21262d; padding: 10px; margin-bottom: 10px; }
        .card { display: flex; align-items: center; gap: 12px; background: var(--card); padding: 12px; border-radius: 15px; margin-bottom: 8px; border: 1px solid #30363d; }
        .card:active { background: #1c2128; }
        .pfp { width: 35px; height: 35px; border-radius: 50%; background: var(--grad); display: flex; align-items: center; justify-content: center; font-weight: bold; font-size: 14px; }

        /* FOOTER */
        .footer { flex-shrink: 0; padding: 10px 0 35px 0; background: var(--bg); }
        .input-wrap { width: 92%; margin: 0 auto 12px auto; background: #161b22; padding: 12px 18px; border-radius: 30px; display: none; align-items: center; gap: 10px; border: 1px solid #30363d; }
        .input-wrap input { flex: 1; background: none; border: none; color: white; }

        nav { width: 92%; margin: 0 auto; height: 70px; background: rgba(13,17,23,0.95); backdrop-filter: blur(15px); border-radius: 35px; display: flex; justify-content: space-around; align-items: center; border: 1px solid #30363d; }
        nav button { background: none; border: none; color: #484f58; font-size: 20px; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: scale(1.1); }

        #loginScreen { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; }
    </style>
</head>
<body>

<div id="loginScreen">
    <div style="width:85%; text-align:center;">
        <i class="fa-solid fa-bolt" style="font-size:50px; color:var(--primary);"></i>
        <h2 style="margin:20px 0;">Live Connect</h2>
        <input type="text" id="uInp" placeholder="Nickname..." style="width:100%; padding:15px; border-radius:15px; background:#111; border:1px solid #333; color:white; text-align:center; margin-bottom:20px;">
        <button onclick="doLogin()" style="width:100%; padding:15px; border-radius:15px; background:var(--grad); color:white; border:none; font-weight:bold;">GET STARTED</button>
    </div>
</div>

<header>
    <h1 id="headerTitle">Live Connect</h1>
    <div style="display:flex; gap:15px; align-items:center;">
        <i class="fa-solid fa-shield-cat" id="admIco" style="display:none; color:gold;" onclick="nav('admin')"></i>
        <i class="fa-solid fa-power-off" style="color:var(--accent);" onclick="logout()"></i>
    </div>
</header>

<div class="viewport">
    <div id="home" class="page active">
        <div style="background:var(--grad); padding:25px; border-radius:25px; margin-bottom:15px;">
            <h2 id="welcome" style="margin:0;">Welcome</h2>
            <p style="font-size:10px; opacity:0.8; margin-top:5px;">System Version 190.0</p>
        </div>
        <div class="list-box" id="broadcastBox" style="font-size:13px; opacity:0.7;">No new alerts.</div>
    </div>

    <div id="chat" class="page">
        <div id="chatScroll">
            <div id="chatBox"></div>
        </div>
    </div>

    <div id="nodes" class="page">
        <div style="font-size:10px; opacity:0.5; margin-bottom:8px;">DIRECT MESSAGES</div>
        <div class="list-box" id="uList"></div>
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:8px;">
            <div style="font-size:10px; opacity:0.5;">GROUPS</div>
            <button onclick="mkGroup()" style="background:none; border:1px solid var(--primary); color:var(--primary); font-size:10px; padding:2px 8px; border-radius:5px;">+ NEW</button>
        </div>
        <div class="list-box" id="gList"></div>
    </div>

    <div id="admin" class="page">
        <div style="font-size:10px; opacity:0.5; margin-bottom:8px;">ADMIN TERMINAL</div>
        <div class="list-box" id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:15px; background:var(--accent); border:none; border-radius:15px; color:white; font-weight:bold;">NUKE ALL DATA</button>
    </div>
</div>

<div class="footer">
    <div class="input-wrap" id="chatInputArea">
        <input id="msgInp" placeholder="Your message...">
        <i class="fa-solid fa-paper-plane" style="color:var(--primary); font-size:22px;" onclick="sendMsg()"></i>
    </div>
    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-home"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-comment-dots"></i></button>
        <button onclick="nav('nodes', this)"><i class="fa-solid fa-user-group"></i></button>
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

if(currentUser) { document.getElementById("loginScreen").style.display = "none"; initApp(currentUser); }

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
                ul.innerHTML += `<div class="card" onclick="startChat('${n.key}', false)"><div class="pfp">${n.key[0]}</div><b>${n.key}</b><i class="fa-solid fa-circle" style="margin-left:auto; font-size:7px; color:${n.val().online?'#00ff88':'#333'};"></i></div>`;
                al.innerHTML += `<div class="card"><b>${n.key}</b><button onclick="hjk('${n.key}', ${!n.val().frozen})" style="margin-left:auto; background:${n.val().frozen?'green':'red'}; color:white; border:none; padding:5px 12px; border-radius:8px;">${n.val().frozen?'FREE':'BAN'}</button></div>`;
            }
        });
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        s.forEach(g => { gl.innerHTML += `<div class="card" onclick="startChat('${g.key}', true)"><div class="pfp" style="background:#222">#</div><b>${g.key}</b></div>`; });
    });
}

var currentPath = "";
function startChat(target, isGroup) {
    currentPath = isGroup ? "groups/"+target : "pvt/"+[currentUser, target].sort().join("_");
    document.getElementById("headerTitle").innerText = isGroup ? "# "+target : target;
    nav('chat', document.querySelectorAll('nav button')[1]);
    
    db.ref(currentPath).off();
    db.ref(currentPath).on("value", s => {
        var box = document.getElementById("chatBox");
        box.innerHTML = "";
        s.forEach(m => {
            var d = m.val();
            var isMe = d.from === currentUser;
            box.innerHTML += `<div class="bubble ${isMe?'me':'other'}">
                <b style="font-size:10px; opacity:0.8;">${d.from}</b><br>${d.txt}
                <div class="msg-info">
                    ${new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}
                    ${isMe ? '<i class="fa-solid fa-check-double"></i>' : ''}
                </div>
            </div>`;
        });
        // AUTO SCROLL
        var scrollDiv = document.getElementById("chatScroll");
        setTimeout(() => { scrollDiv.scrollTop = scrollDiv.scrollHeight; }, 100);
    });
}

function sendMsg() {
    var i = document.getElementById("msgInp");
    if(i.value && currentPath) {
        db.ref(currentPath).push({ from: currentUser, txt: i.value, time: Date.now() });
        i.value = "";
    }
}

function nav(p, b) {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
    document.getElementById("chatInputArea").style.display = (p === 'chat') ? 'flex' : 'none';
    if(p !== 'chat') document.getElementById("headerTitle").innerText = "Live Connect";
}

function mkGroup() { var n = prompt("Group Name:"); if(n) db.ref("groups/"+n).set({ createdBy: currentUser }); }
function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function logout() { db.ref("users/"+currentUser).update({online: false}).then(() => { localStorage.clear(); location.reload(); }); }
function nuke() { if(confirm("DELETE EVERYTHING?")) db.ref().remove(); }
</script>
</body>
</html>
