<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Final 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <style>
        :root { 
            --bg: #030508; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; 
            --text: #e6edf3; --glass: rgba(13, 17, 23, 0.96);
            --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; outline: none; }
        body { margin: 0; font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg); color: var(--text); height: 100vh; display: flex; flex-direction: column; overflow: hidden; }

        /* NEON HEADER */
        header { padding: 50px 20px 15px; flex-shrink: 0; background: var(--bg); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #1a1d21; z-index: 1000; }
        header h1 { margin: 0; font-size: 18px; font-weight: 800; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        .viewport { flex: 1; overflow: hidden; position: relative; display: flex; flex-direction: column; }
        .page { display: none; height: 100%; flex-direction: column; padding: 15px; animation: slideUp 0.3s ease; }
        .page.active { display: flex; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }

        /* SCROLL CONTAINERS */
        .content-box { flex: 1; overflow-y: auto; background: rgba(255,255,255,0.02); border-radius: 24px; border: 1px solid #21262d; padding: 10px; margin-bottom: 12px; -webkit-overflow-scrolling: touch; }
        .label { font-size: 10px; font-weight: bold; opacity: 0.5; text-transform: uppercase; letter-spacing: 1.5px; margin: 10px 0 8px 10px; }

        /* MODERN CARDS */
        .card { display: flex; align-items: center; gap: 12px; background: var(--card); padding: 12px; border-radius: 18px; margin-bottom: 8px; border: 1px solid #30363d; transition: 0.2s; }
        .card:active { transform: scale(0.97); background: #1c2128; }
        .pfp { width: 40px; height: 40px; border-radius: 12px; background: var(--grad); display: flex; align-items: center; justify-content: center; font-weight: bold; color: white; }

        /* CHAT ENGINE */
        #chatScroll { scroll-behavior: smooth; }
        #chatBox { display: flex; flex-direction: column; gap: 10px; padding-bottom: 20px; }
        .bubble { padding: 12px 16px; border-radius: 20px; max-width: 80%; font-size: 14px; position: relative; word-wrap: break-word; }
        .bubble.me { align-self: flex-end; background: var(--grad); border-bottom-right-radius: 2px; }
        .bubble.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }
        .bubble img { width: 100%; border-radius: 12px; margin-top: 5px; display: block; }
        .status { font-size: 9px; opacity: 0.6; display: flex; justify-content: flex-end; margin-top: 4px; gap: 3px; }

        /* FOOTER & NAV */
        .footer { flex-shrink: 0; padding: 10px 0 35px 0; background: var(--bg); }
        .input-bar { width: 92%; margin: 0 auto 12px auto; background: #161b22; padding: 10px 15px; border-radius: 30px; display: none; align-items: center; gap: 12px; border: 1px solid #30363d; }
        .input-bar input { flex: 1; background: none; border: none; color: white; font-size: 14px; }

        nav { width: 92%; margin: 0 auto; height: 70px; background: var(--glass); backdrop-filter: blur(20px); border-radius: 35px; display: flex; justify-content: space-around; align-items: center; border: 1px solid rgba(255,255,255,0.1); box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        nav button { background: none; border: none; color: #484f58; font-size: 22px; transition: 0.3s; padding: 10px; }
        nav button.active { color: var(--primary); transform: translateY(-5px); }

        /* ADMIN EXTRAS */
        .btn-ban { background: rgba(255,0,85,0.1); color: var(--accent); border: 1px solid var(--accent); padding: 6px 12px; border-radius: 10px; font-weight: bold; font-size: 11px; }
        .btn-free { background: rgba(0,255,136,0.1); color: #00ff88; border: 1px solid #00ff88; padding: 6px 12px; border-radius: 10px; font-weight: bold; font-size: 11px; }

        /* LOGIN */
        #loginPage { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; text-align: center; }
        .login-card { width: 85%; max-width: 350px; }
        .login-card input { width: 100%; padding: 18px; border-radius: 18px; background: #111; border: 1px solid #333; color: white; margin: 20px 0; text-align: center; }
        .login-card button { width: 100%; padding: 18px; border-radius: 18px; background: var(--grad); color: white; border: none; font-weight: 800; letter-spacing: 1px; }
    </style>
</head>
<body>

<div id="loginPage">
    <div class="login-card">
        <i class="fa-solid fa-user-astronaut" style="font-size:60px; color:var(--primary);"></i>
        <h2 style="margin:15px 0 5px;">Live Connect</h2>
        <p style="font-size:10px; opacity:0.5;">VERSION 200.0 MASTER</p>
        <input type="text" id="uInp" placeholder="Enter Nickname...">
        <button onclick="doLogin()">START SESSION</button>
    </div>
</div>

<header>
    <h1 id="viewTitle">Live Connect</h1>
    <div style="display:flex; gap:18px; align-items:center;">
        <i class="fa-solid fa-fingerprint" id="admIco" style="display:none; color:gold; font-size:20px;" onclick="nav('admin')"></i>
        <i class="fa-solid fa-power-off" style="color:var(--accent); font-size:20px;" onclick="logout()"></i>
    </div>
</header>

<div class="viewport">
    <div id="home" class="page active">
        <div style="background:var(--grad); padding:30px; border-radius:30px; margin-bottom:20px; box-shadow: 0 10px 20px rgba(0,210,255,0.2);">
            <h2 id="welcome" style="margin:0; font-size:24px;">Welcome</h2>
            <p style="font-size:10px; margin-top:5px; opacity:0.8;">Secure Biometric Access Active</p>
        </div>
        <div class="label">System Broadcast</div>
        <div class="content-box" id="bcBox" style="font-size:13px; opacity:0.6; line-height:1.6;">Initial booting complete. No alerts.</div>
    </div>

    <div id="chat" class="page">
        <div class="content-box" id="chatScroll">
            <div id="chatBox"></div>
        </div>
    </div>

    <div id="nodes" class="page">
        <div class="label">Private Terminals</div>
        <div class="content-box" id="uList"></div>
        <div style="display:flex; justify-content:space-between; align-items:center;">
            <div class="label">Data Clusters</div>
            <button onclick="mkGr()" style="background:none; border:1px solid var(--primary); color:var(--primary); font-size:10px; padding:2px 10px; border-radius:6px; margin-bottom:5px;">+ CREATE</button>
        </div>
        <div class="content-box" id="gList"></div>
    </div>

    <div id="admin" class="page">
        <div class="label">Global Announcement</div>
        <div class="card" style="border-style:dashed;">
            <input type="text" id="bcMsg" placeholder="Broadcast message..." style="flex:1; background:none; border:none; color:white;">
            <i class="fa-solid fa-paper-plane" style="color:var(--primary);" onclick="sendBC()"></i>
        </div>
        <div class="label">Node Control</div>
        <div class="content-box" id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:15px; border-radius:15px; background:var(--accent); border:none; color:white; font-weight:bold;">NUKE ALL DATA</button>
    </div>
</div>

<div class="footer">
    <div class="input-bar" id="chatInputArea">
        <label for="imgInp"><i class="fa-solid fa-image" style="color:var(--primary); font-size:22px;"></i></label>
        <input type="file" id="imgInp" hidden onchange="upImg(this)" accept="image/*">
        <input id="msgInp" placeholder="Enter data packet...">
        <i class="fa-solid fa-circle-arrow-up" style="color:var(--primary); font-size:30px;" onclick="sendMsg()"></i>
    </div>
    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house-chimney-window"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-comment-dots"></i></button>
        <button onclick="nav('nodes', this)"><i class="fa-solid fa-users-viewfinder"></i></button>
    </nav>
</div>

<script>
// --- FIREBASE CONFIG ---
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
    document.getElementById("welcome").innerText = "Operator: " + u;
    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    // BROADCAST LISTENER
    db.ref("broadcast").on("value", s => { if(s.val()) document.getElementById("bcBox").innerHTML = `<div class="card" style="background:#ff005511; border-color:var(--accent);"><i class="fa-solid fa-triangle-exclamation" style="color:var(--accent);"></i> ${s.val().msg}</div>`; });

    // USERS & ADMIN LISTENER
    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList");
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.key !== u) {
                ul.innerHTML += `<div class="card" onclick="startChat('${n.key}', false)">
                    <div class="pfp">${n.key[0]}</div><div style="flex:1;"><b>${n.key}</b></div>
                    <i class="fa-solid fa-circle" style="color:${n.val().online?'#00ff88':'#333'}; font-size:7px;"></i></div>`;
                
                al.innerHTML += `<div class="card">
                    <div class="pfp" style="background:#222">${n.key[0]}</div><div style="flex:1;"><b>${n.key}</b></div>
                    <button class="${n.val().frozen?'btn-free':'btn-ban'}" onclick="hjk('${n.key}', ${!n.val().frozen})">${n.val().frozen?'UNBAN':'BAN'}</button></div>`;
            }
        });
    });

    // GROUPS LISTENER
    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        s.forEach(g => { gl.innerHTML += `<div class="card" onclick="startChat('${g.key}', true)"><div class="pfp" style="background:#222">#</div><b>${g.key}</b></div>`; });
    });
}

var currentPath = "";
function startChat(target, isGroup) {
    currentPath = isGroup ? "groups/"+target : "pvt/"+[currentUser, target].sort().join("_");
    document.getElementById("viewTitle").innerText = isGroup ? "# "+target : target;
    nav('chat', document.querySelectorAll('nav button')[1]);
    
    db.ref(currentPath).off();
    db.ref(currentPath).on("value", s => {
        var box = document.getElementById("chatBox");
        box.innerHTML = "";
        s.forEach(m => {
            var d = m.val();
            var isMe = d.from === currentUser;
            var content = d.type === 'img' ? `<img src="${d.txt}">` : d.txt;
            box.innerHTML += `<div class="bubble ${isMe?'me':'other'}">
                <b style="font-size:10px; opacity:0.7;">${d.from}</b><br>${content}
                <div class="status">${new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})} ${isMe?'<i class="fa-solid fa-check-double"></i>':''}</div>
            </div>`;
        });
        // AUTO SCROLL ENGINE
        var scroller = document.getElementById("chatScroll");
        setTimeout(() => { scroller.scrollTop = scroller.scrollHeight; }, 100);
    });
}

function sendMsg() {
    var i = document.getElementById("msgInp");
    if(i.value && currentPath) {
        db.ref(currentPath).push({ from: currentUser, txt: i.value, type: 'text' });
        i.value = "";
    }
}

function upImg(e) {
    var file = e.files[0];
    if(file && currentPath) {
        var reader = new FileReader();
        reader.onload = function(f) {
            db.ref(currentPath).push({ from: currentUser, txt: f.target.result, type: 'img' });
        };
        reader.readAsDataURL(file);
    }
}

function nav(p, b) {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
    document.getElementById("chatInputArea").style.display = (p === 'chat') ? 'flex' : 'none';
    if(p !== 'chat') document.getElementById("viewTitle").innerText = "Live Connect";
}

function mkGr() { var n = prompt("Cluster Name:"); if(n) db.ref("groups/"+n).set({ c: 1 }); }
function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function sendBC() { var m = document.getElementById("bcMsg").value; if(m) { db.ref("broadcast").set({ msg: m }); document.getElementById("bcMsg").value = ""; } }
function logout() { db.ref("users/"+currentUser).update({online: false}).then(() => { localStorage.clear(); location.reload(); }); }
function nuke() { if(confirm("NUKE EVERYTHING?")) db.ref().remove(); }
</script>
</body>
</html>
