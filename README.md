<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Fixed 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <style>
        :root { 
            --bg: #030508; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; 
            --text: #e6edf3; --glass: rgba(13, 17, 23, 0.98);
            --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; outline: none; }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); height: 100vh; display: flex; flex-direction: column; overflow: hidden; }

        /* HEADER FIXED */
        header { height: 70px; padding: 0 20px; flex-shrink: 0; background: var(--bg); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #1a1d21; z-index: 2000; }
        header h1 { margin: 0; font-size: 18px; font-weight: 800; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* VIEWPORT ADJUSTED FOR FIXED FOOTER */
        .viewport { flex: 1; overflow: hidden; position: relative; margin-bottom: 140px; } /* Added margin for input + nav */
        .page { display: none; height: 100%; flex-direction: column; padding: 15px; }
        .page.active { display: flex; }

        /* CHAT AREA FIX */
        #chatScroll { flex: 1; overflow-y: auto; padding: 10px; -webkit-overflow-scrolling: touch; }
        #chatBox { display: flex; flex-direction: column; gap: 12px; padding-bottom: 40px; }
        
        .bubble { padding: 10px 14px; border-radius: 18px; max-width: 85%; font-size: 14px; position: relative; }
        .bubble.me { align-self: flex-end; background: var(--grad); border-bottom-right-radius: 2px; }
        .bubble.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }
        .bubble img { width: 100%; max-width: 250px; border-radius: 12px; margin-top: 5px; display: block; border: 1px solid rgba(255,255,255,0.1); }
        .status { font-size: 9px; opacity: 0.6; display: flex; justify-content: flex-end; margin-top: 4px; gap: 4px; }

        /* THE FIX: FLOATING INPUT AREA */
        .action-container { position: fixed; bottom: 0; left: 0; width: 100%; background: var(--bg); z-index: 3000; padding-bottom: env(safe-area-inset-bottom); border-top: 1px solid #1a1d21; }
        
        .input-bar { width: 92%; margin: 10px auto; background: #161b22; padding: 10px 15px; border-radius: 25px; display: none; align-items: center; gap: 12px; border: 1px solid #30363d; }
        .input-bar input { flex: 1; background: none; border: none; color: white; font-size: 15px; height: 35px; }

        /* NAVIGATION */
        nav { width: 92%; margin: 5px auto 15px; height: 65px; background: var(--glass); backdrop-filter: blur(20px); border-radius: 30px; display: flex; justify-content: space-around; align-items: center; border: 1px solid rgba(255,255,255,0.1); }
        nav button { background: none; border: none; color: #484f58; font-size: 20px; padding: 10px; transition: 0.2s; }
        nav button.active { color: var(--primary); transform: scale(1.1); }

        /* UTILS */
        .list-box { flex: 1; overflow-y: auto; background: rgba(255,255,255,0.02); border-radius: 20px; border: 1px solid #21262d; padding: 10px; }
        .card { display: flex; align-items: center; gap: 12px; background: var(--card); padding: 12px; border-radius: 15px; margin-bottom: 8px; border: 1px solid #30363d; }
        
        #loginOverlay { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; }
    </style>
</head>
<body>

<div id="loginOverlay">
    <div style="width:80%; text-align:center;">
        <i class="fa-solid fa-rocket" style="font-size:50px; color:var(--primary); margin-bottom:20px;"></i>
        <input type="text" id="uInp" placeholder="Enter Name" style="width:100%; padding:15px; border-radius:15px; background:#111; border:1px solid #333; color:white; text-align:center; margin-bottom:15px;">
        <button onclick="doLogin()" style="width:100%; padding:15px; border-radius:15px; background:var(--grad); color:white; border:none; font-weight:bold;">CONNECT</button>
    </div>
</div>

<header>
    <h1 id="headerTitle">Live Connect</h1>
    <div style="display:flex; gap:15px; align-items:center;">
        <i class="fa-solid fa-crown" id="admIco" style="display:none; color:gold;" onclick="nav('admin')"></i>
        <i class="fa-solid fa-power-off" style="color:var(--accent);" onclick="logout()"></i>
    </div>
</header>

<div class="viewport">
    <div id="home" class="page active">
        <div style="background:var(--grad); padding:25px; border-radius:25px; margin-bottom:15px;">
            <h2 id="welcome" style="margin:0;">Ready</h2>
        </div>
        <div class="list-box">System active. Secure channel established.</div>
    </div>

    <div id="chat" class="page">
        <div id="chatScroll">
            <div id="chatBox"></div>
        </div>
    </div>

    <div id="nodes" class="page">
        <div class="list-box" id="uList"></div>
        <div style="height:10px;"></div>
        <div class="list-box" id="gList"></div>
    </div>

    <div id="admin" class="page">
        <div class="list-box" id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:15px; background:red; border:none; border-radius:15px; color:white; font-weight:bold; margin-top:10px;">NUKE SYSTEM</button>
    </div>
</div>

<div class="action-container">
    <div class="input-bar" id="chatInputArea">
        <label for="imgInp"><i class="fa-solid fa-camera" style="color:var(--primary); font-size:22px;"></i></label>
        <input type="file" id="imgInp" hidden onchange="upImg(this)" accept="image/*">
        <input id="msgInp" placeholder="Message...">
        <i class="fa-solid fa-paper-plane" style="color:var(--primary); font-size:22px;" onclick="sendMsg()"></i>
    </div>
    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-comments"></i></button>
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
    document.getElementById("welcome").innerText = "Operator: " + u;
    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"), al = document.getElementById("aList");
        ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            if(n.key !== u) {
                ul.innerHTML += `<div class="card" onclick="startChat('${n.key}', false)"><b>${n.key}</b></div>`;
                al.innerHTML += `<div class="card"><b>${n.key}</b><button onclick="hjk('${n.key}', ${!n.val().frozen})" style="margin-left:auto; background:${n.val().frozen?'green':'red'}; color:white; border:none; padding:5px 10px; border-radius:8px;">${n.val().frozen?'FREE':'BAN'}</button></div>`;
            }
        });
    });

    db.ref("groups").on("value", s => {
        var gl = document.getElementById("gList"); gl.innerHTML = "";
        s.forEach(g => { gl.innerHTML += `<div class="card" onclick="startChat('${g.key}', true)"><b># ${g.key}</b></div>`; });
    });
}

var currentPath = "";
function startChat(target, isG) {
    currentPath = isG ? "groups/"+target : "pvt/"+[currentUser, target].sort().join("_");
    document.getElementById("headerTitle").innerText = target;
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
                <div class="status">${new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})} ${isMe?'✓✓':''}</div>
            </div>`;
        });
        scrollToBottom();
    });
}

function scrollToBottom() {
    var scroller = document.getElementById("chatScroll");
    setTimeout(() => { scroller.scrollTop = scroller.scrollHeight; }, 100);
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
        reader.onload = f => { db.ref(currentPath).push({ from: currentUser, txt: f.target.result, type: 'img' }); };
        reader.readAsDataURL(file);
    }
}

function nav(p, b) {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
    document.getElementById("chatInputArea").style.display = (p === 'chat') ? 'flex' : 'none';
    if(p === 'chat') scrollToBottom();
}

function logout() { db.ref("users/"+currentUser).update({online: false}).then(() => { localStorage.clear(); location.reload(); }); }
function hjk(t, s) { db.ref("users/"+t).update({ frozen: s }); }
function nuke() { if(confirm("NUKE ALL?")) db.ref().remove(); }
</script>
</body>
</html>
