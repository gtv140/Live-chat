<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Elite 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <style>
        :root { 
            --bg: #020408; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; 
            --text: #e6edf3; --glass: rgba(13, 17, 23, 0.95);
            --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
            --danger: linear-gradient(135deg, #ff416c 0%, #ff4b2b 100%);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; outline: none; }
        body { margin: 0; font-family: 'Inter', 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); height: 100vh; display: flex; flex-direction: column; overflow: hidden; }

        /* NEON HEADER */
        header { padding: 50px 20px 15px; flex-shrink: 0; background: var(--bg); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #1a1d21; box-shadow: 0 4px 20px rgba(0,0,0,0.5); }
        header h1 { margin: 0; font-size: 20px; font-weight: 800; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; letter-spacing: -0.5px; }

        .viewport { flex: 1; overflow: hidden; position: relative; }
        .page { display: none; height: 100%; flex-direction: column; padding: 15px; animation: slideUp 0.4s cubic-bezier(0, 0, 0.2, 1); }
        .page.active { display: flex; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        /* ADMIN ANALYTICS CARDS */
        .analytics-grid { display: grid; grid-template-columns: 1fr 1.5fr; gap: 12px; margin-bottom: 20px; }
        .ana-card { background: var(--card); padding: 18px; border-radius: 24px; border: 1px solid #21262d; position: relative; overflow: hidden; }
        .ana-card i { position: absolute; right: -10px; bottom: -10px; font-size: 50px; opacity: 0.05; color: var(--primary); }
        .ana-card h4 { margin: 0; font-size: 10px; opacity: 0.5; text-transform: uppercase; letter-spacing: 1px; }
        .ana-card h2 { margin: 8px 0 0 0; font-size: 22px; font-weight: 800; }

        /* BOX SYSTEM */
        .content-box { flex: 1; overflow-y: auto; background: rgba(255,255,255,0.02); border-radius: 25px; border: 1px solid #21262d; padding: 12px; margin-bottom: 15px; }
        .section-label { font-size: 11px; font-weight: 700; opacity: 0.4; margin: 10px 0 10px 8px; text-transform: uppercase; }

        /* MODERN LIST ITEMS */
        .list-item { display: flex; align-items: center; gap: 15px; background: var(--card); padding: 15px; border-radius: 20px; margin-bottom: 10px; border: 1px solid #30363d; }
        .node-pfp { width: 42px; height: 42px; border-radius: 14px; background: var(--grad); display: flex; align-items: center; justify-content: center; font-weight: bold; font-size: 18px; color: #fff; }
        
        /* ADMIN ACTIONS */
        .action-tray { display: flex; gap: 10px; }
        .icon-btn { width: 38px; height: 38px; border-radius: 12px; border: none; display: flex; align-items: center; justify-content: center; font-size: 16px; transition: 0.2s; }
        .btn-red { background: rgba(255, 0, 85, 0.1); color: var(--accent); border: 1px solid rgba(255, 0, 85, 0.2); }
        .btn-green { background: rgba(0, 255, 136, 0.1); color: #00ff88; border: 1px solid rgba(0, 255, 136, 0.2); }

        /* CHAT BUBBLES */
        #chatBox { display: flex; flex-direction: column; gap: 10px; }
        .bubble { padding: 12px 18px; border-radius: 22px; max-width: 85%; font-size: 14px; position: relative; }
        .bubble.me { align-self: flex-end; background: var(--grad); border-bottom-right-radius: 4px; color: white; }
        .bubble.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 4px; }

        /* BOTTOM NAV */
        .footer { flex-shrink: 0; padding: 10px 0 35px 0; background: var(--bg); }
        nav { width: 92%; max-width: 500px; margin: 0 auto; height: 75px; background: var(--glass); backdrop-filter: blur(25px); border-radius: 35px; display: flex; justify-content: space-around; align-items: center; border: 1px solid rgba(255,255,255,0.08); }
        nav button { background: none; border: none; color: #484f58; font-size: 22px; transition: 0.3s; width: 50px; height: 50px; border-radius: 15px; }
        nav button.active { color: var(--primary); background: rgba(0, 210, 255, 0.1); transform: scale(1.1); }

        /* OVERLAYS */
        #loginOverlay { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; }
        .login-box { width: 85%; max-width: 350px; text-align: center; }
        .login-box input { width: 100%; padding: 18px; border-radius: 20px; background: #111; border: 1px solid #333; color: white; margin: 25px 0; text-align: center; font-size: 16px; }
        .login-box button { width: 100%; padding: 18px; border-radius: 20px; background: var(--grad); color: white; border: none; font-weight: 800; cursor: pointer; box-shadow: 0 10px 20px rgba(0, 210, 255, 0.3); }

        .sys-btn { width: 100%; padding: 15px; border-radius: 18px; border: none; font-weight: 700; color: white; margin-top: 10px; cursor: pointer; }
    </style>
</head>
<body>

<div id="loginOverlay">
    <div class="login-box">
        <div style="width:70px; height:70px; background:var(--grad); border-radius:22px; margin:0 auto 20px; display:flex; align-items:center; justify-content:center;">
            <i class="fa-solid fa-shield-halved" style="font-size:35px; color:white;"></i>
        </div>
        <h2 style="font-weight:900;">LIVE CONNECT</h2>
        <p style="opacity:0.5; font-size:12px;">ENCRYPTED TERMINAL ACCESS</p>
        <input type="text" id="uInp" placeholder="Enter Terminal ID">
        <button onclick="doLogin()">AUTHORIZE ACCESS</button>
    </div>
</div>

<header>
    <div style="display:flex; align-items:center; gap:10px;">
        <div id="admBadge" style="display:none; padding:4px 8px; background:gold; color:black; border-radius:6px; font-size:9px; font-weight:900;">ADMIN</div>
        <h1>Live Connect</h1>
    </div>
    <div style="display:flex; gap:20px; align-items:center;">
        <i class="fa-solid fa-solar-panel" id="admIco" style="display:none; color:var(--primary); font-size:20px;" onclick="nav('admin')"></i>
        <i class="fa-solid fa-power-off" style="color:var(--accent); font-size:20px;" onclick="logout()"></i>
    </div>
</header>

<div class="viewport">
    <div id="home" class="page active">
        <div class="ana-card" style="background:var(--grad); color:white; margin-bottom:15px; border:none;">
            <h4 style="color:white; opacity:0.8;">Active Node</h4>
            <h2 id="welcome">Terminal ID: --</h2>
            <p id="uptime" style="font-size:10px; margin-top:10px; opacity:0.8;">SYSTEM UPTIME: 00:00:00</p>
        </div>
        <div class="section-label">Broadcast History</div>
        <div class="content-box" id="sysLog" style="font-size:13px; opacity:0.6; padding:15px;">
            Initializing secure layers...
        </div>
    </div>

    <div id="chat" class="page">
        <div class="content-box" id="chatScroll"><div id="chatBox"></div></div>
    </div>

    <div id="nodes" class="page">
        <div class="section-label">Discovery Mode</div>
        <div class="content-box" id="uList"></div>
        <div class="section-label">Data Clusters</div>
        <div class="content-box" id="gList"></div>
    </div>

    <div id="admin" class="page">
        <div class="analytics-grid">
            <div class="ana-card"><h4>Live</h4><h2 id="onCount">0</h2><i class="fa-solid fa-wifi"></i></div>
            <div class="ana-card"><h4>System Nodes</h4><h2 id="totalCount">0</h2><i class="fa-solid fa-database"></i></div>
        </div>

        <div class="section-label">Global Command</div>
        <div class="list-item" style="border-style:dashed;">
            <input type="text" id="bcMsg" placeholder="Broadcast to all nodes..." style="flex:1; background:none; border:none; color:white;">
            <i class="fa-solid fa-paper-plane" style="color:var(--primary);" onclick="sendBC()"></i>
        </div>

        <div class="section-label">Terminal Management</div>
        <div class="content-box" id="aList"></div>
        
        <div style="display:grid; grid-template-columns:1fr 1fr; gap:10px;">
            <button class="sys-btn" style="background:#21262d; border:1px solid #30363d;" onclick="clearHistory()">CLEAR CHATS</button>
            <button class="sys-btn" style="background:var(--danger);" onclick="nuke()">NUKE SYSTEM</button>
        </div>
    </div>
</div>

<div class="footer">
    <div id="chatInputArea" style="display:none; width:92%; margin:0 auto 12px auto; background:#161b22; border:1px solid #30363d; padding:12px; border-radius:30px; align-items:center; gap:10px;">
        <input id="msgInp" placeholder="Type encrypted message..." style="flex:1; background:none; border:none; color:white;">
        <i class="fa-solid fa-arrow-up-circle" style="color:var(--primary); font-size:28px;" onclick="sendMsg()"></i>
    </div>
    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house-user"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-comment-nodes"></i></button>
        <button onclick="nav('nodes', this)"><i class="fa-solid fa-network-wired"></i></button>
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
var startTime = Date.now();

setInterval(() => {
    var diff = Math.floor((Date.now() - startTime) / 1000);
    var h = Math.floor(diff/3600).toString().padStart(2,'0');
    var m = Math.floor((diff%3600)/60).toString().padStart(2,'0');
    var s = (diff%60).toString().padStart(2,'0');
    document.getElementById("uptime").innerText = `SYSTEM UPTIME: ${h}:${m}:${s}`;
}, 1000);

if(currentUser) { document.getElementById("loginOverlay").style.display = "none"; initApp(currentUser); }

function doLogin() {
    var u = document.getElementById("uInp").value.trim();
    if(u) { db.ref("users/"+u).update({ online: true, frozen: false }).then(() => { localStorage.setItem("lc_u", u); location.reload(); }); }
}

function initApp(u) {
    document.getElementById("welcome").innerText = "Node: " + u;
    if(u === "Admin786") {
        document.getElementById("admIco").style.display = "block";
        document.getElementById("admBadge").style.display = "block";
    }

    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList");
        var total = s.numChildren(), online = 0;
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.val().online) online++;
            if(n.key !== u) {
                ul.innerHTML += `<div class="list-item" onclick="startChat('${n.key}', false)">
                    <div class="node-pfp">${n.key[0]}</div><div style="flex:1;"><b>${n.key}</b></div>
                    <i class="fa-solid fa-circle" style="color:${n.val().online?'#00ff88':'#333'}; font-size:8px;"></i></div>`;
                
                al.innerHTML += `<div class="list-item">
                    <div class="node-pfp" style="background:#1c2128">${n.key[0]}</div>
                    <div style="flex:1;"><b>${n.key}</b><br><small style="opacity:0.5">${n.val().online?'ONLINE':'OFFLINE'}</small></div>
                    <div class="action-tray">
                        <button class="icon-btn ${n.val().frozen?'btn-green':'btn-red'}" onclick="hjk('${n.key}', ${!n.val().frozen})">
                            <i class="fa-solid ${n.val().frozen?'fa-lock-open':'fa-user-slash'}"></i>
                        </button>
                    </div></div>`;
            }
        });
        document.getElementById("totalCount").innerText = total;
        document.getElementById("onCount").innerText = online;
    });

    db.ref("broadcast").on("value", s => { if(s.val()) document.getElementById("sysLog").innerHTML = `<div class="list-item" style="background:rgba(255,0,85,0.05); border-color:var(--accent);"><i class="fa-solid fa-shield-halved" style="color:var(--accent);"></i> ${s.val().msg}</div>`; });
    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        s.forEach(g => { gl.innerHTML += `<div class="list-item" onclick="startChat('${g.key}', true)"><div class="node-pfp" style="background:#21262d">#</div><div style="flex:1;"><b>${g.key}</b></div><i class="fa-solid fa-chevron-right" style="opacity:0.2"></i></div>`; });
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
            box.innerHTML += `<div class="bubble ${d.from===currentUser?'me':'other'}"><b>${d.from}</b><br>${d.txt}</div>`;
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

function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function sendBC() { var m = document.getElementById("bcMsg").value; if(m) { db.ref("broadcast").set({ msg: m }); document.getElementById("bcMsg").value = ""; } }
function sendMsg() { var i = document.getElementById("msgInp"); if(i.value) { db.ref(currentPath).push({ from: currentUser, txt: i.value }); i.value = ""; } }
function clearHistory() { if(confirm("Clear current chat?")) db.ref(currentPath).remove(); }
function logout() { db.ref("users/"+currentUser).update({online: false}).then(() => { localStorage.clear(); location.reload(); }); }
function nuke() { if(confirm("NUKE EVERYTHING?")) db.ref().remove(); }
</script>
</body>
</html>
