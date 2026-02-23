<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Modern 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <style>
        :root { 
            --bg: #030508; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; 
            --text: #e6edf3; --glass: rgba(13, 17, 23, 0.85);
            --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); height: 100vh; display: flex; flex-direction: column; overflow: hidden; }

        /* MODERN HEADER */
        header { padding: 55px 20px 15px; flex-shrink: 0; background: var(--bg); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #1a1d21; }
        header h1 { margin: 0; font-size: 20px; font-weight: 800; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* PAGE CONTAINER & BOXES */
        .main-view { flex: 1; overflow: hidden; position: relative; display: flex; flex-direction: column; }
        .page { display: none; height: 100%; flex-direction: column; padding: 15px; animation: fadeIn 0.3s; }
        .page.active { display: flex; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* MODERN SEARCH BAR */
        .search-wrap { background: #161b22; border-radius: 12px; display: flex; align-items: center; padding: 10px 15px; margin-bottom: 15px; border: 1px solid #30363d; }
        .search-wrap input { flex: 1; background: none; border: none; color: #fff; outline: none; font-size: 14px; }

        /* DUAL BOX SYSTEM */
        .section-box { flex: 1; overflow-y: auto; background: rgba(255,255,255,0.02); border-radius: 20px; border: 1px solid #21262d; padding: 10px; margin-bottom: 10px; }
        .box-label { font-size: 10px; font-weight: bold; opacity: 0.4; text-transform: uppercase; margin: 0 0 10px 5px; letter-spacing: 1.5px; }

        /* USER & MESSAGE UI */
        .item-card { display: flex; align-items: center; gap: 12px; background: var(--card); padding: 12px; border-radius: 15px; margin-bottom: 8px; border: 1px solid #30363d; transition: 0.2s; }
        .item-card:active { transform: scale(0.98); background: #1c2128; }
        .avatar { width: 35px; height: 35px; border-radius: 50%; background: var(--grad); display: flex; align-items: center; justify-content: center; font-weight: bold; color: white; font-size: 12px; }
        
        /* CHAT BUBBLES */
        #chatBox { display: flex; flex-direction: column; gap: 10px; padding-bottom: 20px; }
        .msg { padding: 10px 15px; border-radius: 18px; max-width: 80%; font-size: 14px; position: relative; word-wrap: break-word; }
        .msg.me { align-self: flex-end; background: var(--grad); border-bottom-right-radius: 2px; }
        .msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }

        /* BOTTOM STICKY UI */
        .bottom-layer { flex-shrink: 0; padding: 10px 0 30px 0; background: var(--bg); z-index: 1000; }
        .input-box { width: 92%; max-width: 500px; margin: 0 auto 12px auto; background: #161b22; padding: 12px 18px; border-radius: 30px; display: none; align-items: center; gap: 12px; border: 1px solid #30363d; }
        .input-box input { flex: 1; background: none; border: none; color: #fff; outline: none; }

        nav { width: 92%; max-width: 500px; margin: 0 auto; height: 70px; background: var(--glass); backdrop-filter: blur(15px); border-radius: 35px; display: flex; justify-content: space-around; align-items: center; border: 1px solid rgba(255,255,255,0.1); }
        nav button { background: none; border: none; color: #484f58; font-size: 20px; position: relative; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: translateY(-3px); }
        nav button.active::after { content: ''; position: absolute; bottom: -8px; left: 50%; transform: translateX(-50%); width: 4px; height: 4px; background: var(--primary); border-radius: 50%; }

        /* LOGIN */
        #loginPage { position: fixed; inset: 0; background: #000; z-index: 99999; display: flex; align-items: center; justify-content: center; }
        #hjkScreen { display: none; position: fixed; inset: 0; background: #000; z-index: 100000; flex-direction: column; align-items: center; justify-content: center; color: var(--accent); }
    </style>
</head>
<body>

<div id="loginPage">
    <div style="width:85%; text-align:center;">
        <i class="fa-solid fa-cloud-bolt" style="font-size:50px; color:var(--primary); margin-bottom:20px;"></i>
        <h2 style="margin-bottom:30px;">Live Connect</h2>
        <input type="text" id="uInp" placeholder="Choose Username" style="width:100%; padding:15px; border-radius:15px; background:#111; border:1px solid #333; color:#fff; text-align:center; margin-bottom:15px; outline:none;">
        <button onclick="doLogin()" style="width:100%; padding:15px; border-radius:15px; background:var(--grad); color:white; border:none; font-weight:800; letter-spacing:1px;">ACCESS SYSTEM</button>
    </div>
</div>

<div id="hjkScreen">
    <i class="fa-solid fa-triangle-exclamation" style="font-size:60px; margin-bottom:20px;"></i>
    <h2>TERMINAL DEACTIVATED</h2>
</div>

<header>
    <h1>Live Connect</h1>
    <div style="display:flex; gap:15px;">
        <i class="fa-solid fa-fingerprint" id="admIco" style="display:none; color:gold; font-size:20px;" onclick="nav('admin')"></i>
        <i class="fa-solid fa-power-off" style="color:var(--accent); font-size:20px;" onclick="logout()"></i>
    </div>
</header>

<div class="main-view">
    <div id="home" class="page active">
        <div style="background:var(--grad); padding:25px; border-radius:25px; margin-bottom:15px; box-shadow: 0 10px 20px rgba(0,0,0,0.3);">
            <h2 id="welcome" style="margin:0; font-size:24px;">Hi, User</h2>
            <p style="font-size:10px; margin:5px 0 0 0; opacity:0.8;">Secure System v140.0</p>
        </div>
        <div class="box-label">System Logs</div>
        <div class="section-box" id="sysLogs" style="font-size:12px; opacity:0.6; padding:15px;">
            Initializing secure connection...<br>No new system-wide alerts.
        </div>
    </div>

    <div id="chat" class="page">
        <div class="section-box" id="chatScroll">
            <div id="chatBox"></div>
        </div>
    </div>

    <div id="nodes" class="page">
        <div class="search-wrap">
            <i class="fa-solid fa-search" style="opacity:0.3; margin-right:10px;"></i>
            <input type="text" id="uSearch" placeholder="Search for nodes..." onkeyup="filterUsers()">
        </div>
        <div class="box-label">Active Terminals</div>
        <div class="section-box" id="uList"></div>
        <div class="box-label">Global Clusters</div>
        <div class="section-box" id="gList"></div>
    </div>

    <div id="admin" class="page">
        <div class="box-label">Broadcast Alert</div>
        <div class="item-card" style="margin-bottom:20px;">
            <input type="text" id="bcMsg" placeholder="Global message..." style="flex:1; background:none; border:none; color:#fff; outline:none;">
            <i class="fa-solid fa-bullhorn" style="color:var(--primary);" onclick="sendBC()"></i>
        </div>
        <div class="box-label">Manage All Users</div>
        <div class="section-box" id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:15px; background:var(--accent); color:white; border:none; border-radius:15px; font-weight:bold; margin-top:10px;">PURGE ALL DATA</button>
    </div>
</div>

<div class="bottom-layer">
    <div class="input-box" id="chatInputArea">
        <label for="imgInp"><i class="fa-solid fa-plus-circle" style="color:var(--primary); font-size:24px;"></i></label>
        <input type="file" id="imgInp" hidden onchange="upImg(this)">
        <input id="msgInp" placeholder="Enter message...">
        <i class="fa-solid fa-location-arrow" style="color:var(--primary); font-size:24px;" onclick="sendMsg()"></i>
    </div>
    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house-chimney"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-comment-dots"></i></button>
        <button onclick="nav('nodes', this)"><i class="fa-solid fa-compass"></i></button>
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

    db.ref("broadcast").on("value", s => { if(s.val()) document.getElementById("sysLogs").innerHTML = `<div class="item-card" style="background:#ff005522; border:1px solid var(--accent);"><i class="fa-solid fa-triangle-exclamation" style="color:var(--accent);"></i> ${s.val().msg}</div>`; });
    db.ref("users/"+u+"/frozen").on("value", s => { document.getElementById("hjkScreen").style.display = s.val() ? "flex" : "none"; });

    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList");
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            var letter = n.key.charAt(0).toUpperCase();
            if(n.key !== u) {
                ul.innerHTML += `<div class="item-card user-node" data-name="${n.key}" onclick="startChat('${n.key}', false)">
                    <div class="avatar">${letter}</div>
                    <div style="flex:1;"><b>${n.key}</b></div>
                    <i class="fa-solid fa-circle" style="color:${n.val().online?'#00ff88':'#333'}; font-size:8px;"></i>
                </div>`;
                if(u === "Admin786") al.innerHTML += `<div class="item-card">
                    <div class="avatar" style="background:#333">${letter}</div>
                    <div style="flex:1;"><b>${n.key}</b></div>
                    <button onclick="hjk('${n.key}', ${!n.val().frozen})" style="background:${n.val().frozen?'#00ff88':'#ff0055'}; border:none; padding:8px 12px; border-radius:10px; color:white; font-weight:bold;">${n.val().frozen?'FREE':'BAN'}</button>
                </div>`;
            }
        });
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        s.forEach(g => { gl.innerHTML += `<div class="item-card" onclick="startChat('${g.key}', true)"><div class="avatar" style="background:#555">#</div><div style="flex:1;"><b>${g.key}</b></div><i class="fa-solid fa-users" style="opacity:0.3"></i></div>`; });
    });
}

function filterUsers() {
    var val = document.getElementById("uSearch").value.toLowerCase();
    document.querySelectorAll('.user-node').forEach(el => {
        el.style.display = el.getAttribute('data-name').toLowerCase().includes(val) ? 'flex' : 'none';
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
            box.innerHTML += `<div class="msg ${d.from===currentUser?'me':'other'}"><b>${d.from}</b>${d.type==='img'?'<img src="'+d.txt+'" style="width:100%; border-radius:12px; margin-top:5px;">':d.txt}</div>`;
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
function sendBC() { var m = document.getElementById("bcMsg").value; if(m) { db.ref("broadcast").set({ msg: m }); document.getElementById("bcMsg").value = ""; alert("Broadcast Active!"); } }
function upImg(e) { var r = new FileReader(); r.onload = f => db.ref(currentPath).push({ from: currentUser, txt: f.target.result, type: 'img' }); r.readAsDataURL(e.files[0]); }
function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function logout() { db.ref("users/"+currentUser).update({online: false}).then(() => { localStorage.clear(); location.reload(); }); }
function mkGr() { var n = prompt("Group Name:"); if(n) db.ref("groups/"+n).set({ c: 1 }); }
function nuke() { if(confirm("NUKE EVERYTHING?")) db.ref().remove(); }
</script>
</body>
</html>
