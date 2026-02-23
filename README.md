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
            --bg: #010409; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; 
            --text: #e6edf3; --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
            --glass: rgba(13, 17, 23, 0.9);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { margin: 0; font-family: 'Inter', -apple-system, sans-serif; background: var(--bg); color: var(--text); height: 100dvh; display: flex; flex-direction: column; overflow: hidden; }

        /* NEON HEADER */
        header { height: 65px; padding: 45px 20px 10px; background: var(--bg); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #21262d; z-index: 1000; }
        header h1 { font-size: 20px; font-weight: 900; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        /* VIEWPORT CONFIG */
        .viewport { flex: 1; position: relative; overflow: hidden; display: flex; flex-direction: column; }
        .page { display: none; height: 100%; flex-direction: column; animation: fadeIn 0.3s ease; }
        .page.active { display: flex; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* THE FIXED CHAT BOX */
        .chat-frame { 
            display: flex; flex-direction: column; height: 100%; width: 100%; background: #010409;
        }

        #chatScroll { 
            flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px;
            scroll-behavior: smooth; -webkit-overflow-scrolling: touch;
        }

        /* MESSAGE BUBBLES */
        .bubble { padding: 12px 16px; border-radius: 22px; max-width: 82%; font-size: 14px; line-height: 1.5; position: relative; }
        .bubble.me { align-self: flex-end; background: var(--grad); border-bottom-right-radius: 4px; box-shadow: 0 4px 15px rgba(0, 210, 255, 0.2); }
        .bubble.other { align-self: flex-start; background: #161b22; border: 1px solid #30363d; border-bottom-left-radius: 4px; }
        .bubble img { width: 100%; border-radius: 12px; margin-top: 8px; border: 1px solid rgba(255,255,255,0.1); }
        .meta { font-size: 9px; opacity: 0.6; display: flex; justify-content: flex-end; margin-top: 5px; gap: 4px; }

        /* FLOATING INPUT BAR */
        .input-pocket { 
            padding: 12px 15px; background: var(--bg); border-top: 1px solid #21262d; 
            display: flex; align-items: center; gap: 12px; 
        }
        .input-pocket .box { 
            flex: 1; background: #0d1117; border: 1px solid #30363d; 
            padding: 12px 18px; border-radius: 25px; display: flex; align-items: center; gap: 10px;
        }
        .input-pocket input { flex: 1; background: none; border: none; color: white; font-size: 15px; outline: none; }

        /* NAVIGATION BAR */
        nav { 
            height: 75px; display: flex; justify-content: space-around; align-items: center; 
            background: var(--glass); backdrop-filter: blur(20px); border-top: 1px solid #21262d; padding-bottom: 15px;
        }
        nav button { background: none; border: none; color: #484f58; font-size: 22px; transition: 0.3s; position: relative; }
        nav button.active { color: var(--primary); }
        nav button.active::after { content: ''; position: absolute; bottom: -8px; left: 50%; transform: translateX(-50%); width: 5px; height: 5px; background: var(--primary); border-radius: 50%; }

        /* LISTS */
        .scroller { flex: 1; overflow-y: auto; padding: 15px; }
        .node-card { background: var(--card); padding: 16px; border-radius: 20px; margin-bottom: 10px; border: 1px solid #21262d; display: flex; align-items: center; gap: 15px; }
        .node-pfp { width: 45px; height: 45px; border-radius: 15px; background: var(--grad); display: flex; align-items: center; justify-content: center; font-weight: 800; font-size: 18px; }

        /* OVERLAYS */
        #login { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; padding: 20px; }
        .login-panel { width: 100%; max-width: 350px; text-align: center; }
        .login-panel input { width: 100%; padding: 18px; border-radius: 20px; background: #0d1117; border: 1px solid #30363d; color: white; margin: 25px 0; text-align: center; font-size: 16px; }
        .login-panel button { width: 100%; padding: 18px; border-radius: 20px; background: var(--grad); color: white; border: none; font-weight: 800; box-shadow: 0 10px 25px rgba(0, 210, 255, 0.3); }

        /* ADMIN PANEL */
        .btn-ban { background: #ff005522; color: var(--accent); border: 1px solid var(--accent); padding: 8px 15px; border-radius: 12px; font-weight: 800; font-size: 11px; }
        .btn-free { background: #00ff8822; color: #00ff88; border: 1px solid #00ff88; padding: 8px 15px; border-radius: 12px; font-weight: 800; font-size: 11px; }
    </style>
</head>
<body>

<div id="login">
    <div class="login-panel">
        <div style="width:80px; height:80px; background:var(--grad); border-radius:25px; margin:0 auto 20px; display:flex; align-items:center; justify-content:center; box-shadow: 0 10px 30px rgba(0,210,255,0.4);">
            <i class="fa-solid fa-satellite-dish" style="font-size:40px; color:white;"></i>
        </div>
        <h1 style="font-size:28px; letter-spacing:-1px;">Live Connect</h1>
        <p style="opacity:0.5; font-size:12px;">ENCRYPTED MOBILE TERMINAL</p>
        <input type="text" id="uInp" placeholder="Assign Username...">
        <button onclick="doLogin()">AUTHORIZE ACCESS</button>
    </div>
</div>

<header>
    <div style="display:flex; align-items:center; gap:12px;">
        <i class="fa-solid fa-crown" id="admIco" style="display:none; color:gold; font-size:18px;" onclick="nav('admin')"></i>
        <h1 id="topTitle">Live Connect</h1>
    </div>
    <i class="fa-solid fa-power-off" style="color:var(--accent); font-size:22px;" onclick="logout()"></i>
</header>

<div class="viewport">
    <div id="home" class="page active">
        <div class="scroller">
            <div style="background:var(--grad); padding:30px; border-radius:30px; margin-bottom:20px; box-shadow: 0 15px 35px rgba(0,210,255,0.25);">
                <h2 id="welcome" style="margin:0; font-size:26px;">Hello!</h2>
                <p style="font-size:11px; opacity:0.8; margin-top:8px;">System Version 230.0 Final Master</p>
            </div>
            <div id="bcAlert"></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div class="chat-frame">
            <div id="chatScroll">
                <div id="chatBox"></div>
            </div>
            <div class="input-pocket">
                <label for="imgInp"><i class="fa-solid fa-image" style="color:var(--primary); font-size:22px;"></i></label>
                <input type="file" id="imgInp" hidden onchange="upImg(this)" accept="image/*">
                <div class="box">
                    <input id="msgInp" placeholder="Write message...">
                    <i class="fa-solid fa-paper-plane" style="color:var(--primary); font-size:20px;" onclick="sendMsg()"></i>
                </div>
            </div>
        </div>
    </div>

    <div id="nodes" class="page">
        <div class="scroller">
            <p style="font-size:10px; opacity:0.4; letter-spacing:1px; margin-bottom:10px;">DIRECT CHANNELS</p>
            <div id="uList"></div>
            <p style="font-size:10px; opacity:0.4; letter-spacing:1px; margin:20px 0 10px;">GROUP CLUSTERS</p>
            <div id="gList"></div>
        </div>
    </div>

    <div id="admin" class="page">
        <div class="scroller">
            <div class="node-card" style="border-style:dashed; background:none;">
                <input type="text" id="bcMsg" placeholder="Broadcast to all..." style="flex:1; background:none; border:none; color:white;">
                <i class="fa-solid fa-bullhorn" style="color:var(--primary);" onclick="sendBC()"></i>
            </div>
            <p style="font-size:10px; opacity:0.4; margin:15px 0;">MANAGE TERMINALS</p>
            <div id="aList"></div>
            <button onclick="nuke()" style="width:100%; padding:18px; border-radius:20px; background:var(--accent); border:none; color:white; font-weight:bold; margin-top:15px;">TERMINATE ALL DATA</button>
        </div>
    </div>
</div>

<nav>
    <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-grip"></i></button>
    <button onclick="nav('chat', this)"><i class="fa-solid fa-message"></i></button>
    <button onclick="nav('nodes', this)"><i class="fa-solid fa-users-rays"></i></button>
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

if(currentUser) { document.getElementById("login").style.display = "none"; initApp(currentUser); }

function doLogin() {
    var u = document.getElementById("uInp").value.trim();
    if(u) { db.ref("users/"+u).update({ online: true, frozen: false }).then(() => { localStorage.setItem("lc_u", u); location.reload(); }); }
}

function initApp(u) {
    document.getElementById("welcome").innerText = "Operator: " + u;
    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    db.ref("broadcast").on("value", s => { if(s.val()) document.getElementById("bcAlert").innerHTML = `<div class="node-card" style="border-color:var(--accent); background:#ff005508;"><i class="fa-solid fa-shield-halved" style="color:var(--accent);"></i> ${s.val().msg}</div>`; });

    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList");
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.key !== u) {
                ul.innerHTML += `<div class="node-card" onclick="startChat('${n.key}', false)"><div class="node-pfp">${n.key[0]}</div><b>${n.key}</b><i class="fa-solid fa-circle" style="margin-left:auto; font-size:7px; color:${n.val().online?'#00ff88':'#333'};"></i></div>`;
                al.innerHTML += `<div class="node-card"><div class="node-pfp" style="background:#222">${n.key[0]}</div><b style="flex:1;">${n.key}</b><button class="${n.val().frozen?'btn-free':'btn-ban'}" onclick="hjk('${n.key}', ${!n.val().frozen})">${n.val().frozen?'FREE':'BAN'}</button></div>`;
            }
        });
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        s.forEach(g => { gl.innerHTML += `<div class="node-card" onclick="startChat('${g.key}', true)"><div class="node-pfp" style="background:#111">#</div><b>${g.key}</b></div>`; });
    });
}

var currentPath = "";
function startChat(target, isG) {
    currentPath = isG ? "groups/"+target : "pvt/"+[currentUser, target].sort().join("_");
    document.getElementById("topTitle").innerText = isG ? "# "+target : target;
    nav('chat', document.querySelectorAll('nav button')[1]);
    
    db.ref(currentPath).off();
    db.ref(currentPath).on("value", s => {
        var box = document.getElementById("chatBox"); box.innerHTML = "";
        s.forEach(m => {
            var d = m.val();
            var isMe = d.from === currentUser;
            var content = d.type === 'img' ? `<img src="${d.txt}">` : d.txt;
            box.innerHTML += `<div class="bubble ${isMe?'me':'other'}">
                <b style="font-size:10px; opacity:0.7;">${d.from}</b><br>${content}
                <div class="meta">${new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})} ${isMe?'<i class="fa-solid fa-check-double"></i>':''}</div>
            </div>`;
        });
        scrollToEnd();
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
    var reader = new FileReader();
    reader.onload = f => { db.ref(currentPath).push({ from: currentUser, txt: f.target.result, type: 'img' }); };
    reader.readAsDataURL(e.files[0]);
}

function scrollToEnd() {
    var sc = document.getElementById("chatScroll");
    setTimeout(() => { sc.scrollTop = sc.scrollHeight; }, 100);
}

function nav(p, b) {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
    if(p !== 'chat') document.getElementById("topTitle").innerText = "Live Connect";
    if(p === 'chat') scrollToEnd();
}

function sendBC() { var m = document.getElementById("bcMsg").value; if(m) { db.ref("broadcast").set({ msg: m }); document.getElementById("bcMsg").value = ""; } }
function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function logout() { db.ref("users/"+currentUser).update({online: false}).then(() => { localStorage.clear(); location.reload(); }); }
function nuke() { if(confirm("NUKE SYSTEM?")) db.ref().remove(); }
</script>
</body>
</html>
