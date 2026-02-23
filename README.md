<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Box 🚀</title>
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

        header { height: 60px; padding: 40px 20px 10px; flex-shrink: 0; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #1a1d21; }
        header h1 { font-size: 18px; margin: 0; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        .viewport { flex: 1; position: relative; overflow: hidden; }
        .page { display: none; height: 100%; flex-direction: column; padding: 15px; }
        .page.active { display: flex; }

        /* FIXED CHAT BOX SYSTEM */
        .chat-container { 
            flex: 1; 
            display: flex; 
            flex-direction: column; 
            background: #0d1117; 
            border-radius: 25px; 
            border: 1px solid #21262d; 
            overflow: hidden; /* Box se bahar kuch nahi jayega */
            max-height: 75vh; /* Mobile screen ke mutabiq height fix */
        }

        #chatScroll { 
            flex: 1; 
            overflow-y: auto; 
            padding: 15px; 
            display: flex; 
            flex-direction: column; 
            gap: 12px;
            scroll-behavior: smooth;
        }

        /* INPUT AREA INSIDE BOX */
        .chat-input-row { 
            padding: 12px; 
            background: #161b22; 
            border-top: 1px solid #30363d; 
            display: flex; 
            align-items: center; 
            gap: 10px; 
        }
        .chat-input-row input { 
            flex: 1; background: #030508; border: 1px solid #30363d; 
            padding: 12px; border-radius: 20px; color: white; font-size: 14px;
        }

        /* BUBBLES */
        .bubble { padding: 10px 14px; border-radius: 18px; max-width: 85%; font-size: 14px; }
        .bubble.me { align-self: flex-end; background: var(--grad); border-bottom-right-radius: 2px; }
        .bubble.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }
        .bubble img { width: 100%; border-radius: 10px; margin-top: 5px; }

        /* FOOTER NAV */
        nav { height: 70px; display: flex; justify-content: space-around; align-items: center; background: var(--bg); border-top: 1px solid #1a1d21; padding-bottom: 20px; }
        nav button { background: none; border: none; color: #484f58; font-size: 20px; }
        nav button.active { color: var(--primary); }

        .card { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; border: 1px solid #30363d; }
        #login { position: fixed; inset: 0; background: #000; z-index: 5000; display: flex; align-items: center; justify-content: center; }
    </style>
</head>
<body>

<div id="login">
    <div style="width:80%; text-align:center;">
        <h2 style="color:var(--primary);">Live Connect</h2>
        <input type="text" id="uInp" placeholder="Username" style="width:100%; padding:15px; border-radius:12px; margin:20px 0; background:#111; border:1px solid #333; color:white;">
        <button onclick="doLogin()" style="width:100%; padding:15px; border-radius:12px; background:var(--grad); color:white; border:none; font-weight:bold;">ENTER</button>
    </div>
</div>

<header>
    <h1 id="chatTitle">Live Connect</h1>
    <i class="fa-solid fa-power-off" style="color:var(--accent);" onclick="logout()"></i>
</header>

<div class="viewport">
    <div id="home" class="page active">
        <div class="card"><h3>Welcome!</h3><p>Select a user to start chatting in the fixed box.</p></div>
    </div>

    <div id="chat" class="page">
        <div class="chat-container">
            <div id="chatScroll">
                <div id="chatBox"></div>
            </div>
            <div class="chat-input-row">
                <label for="fInp"><i class="fa-solid fa-paperclip" style="color:var(--primary);"></i></label>
                <input type="file" id="fInp" hidden onchange="upImg(this)" accept="image/*">
                <input id="msgInp" placeholder="Type here...">
                <i class="fa-solid fa-paper-plane" style="color:var(--primary); font-size:20px;" onclick="sendMsg()"></i>
            </div>
        </div>
    </div>

    <div id="nodes" class="page">
        <div id="uList"></div>
    </div>
</div>

<nav>
    <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
    <button onclick="nav('chat', this)"><i class="fa-solid fa-comment-dots"></i></button>
    <button onclick="nav('nodes', this)"><i class="fa-solid fa-user-group"></i></button>
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
    if(u) { localStorage.setItem("lc_u", u); location.reload(); }
}

function initApp(u) {
    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"); ul.innerHTML = "";
        s.forEach(n => {
            if(n.key !== u) ul.innerHTML += `<div class="card" onclick="startChat('${n.key}')"><b>${n.key}</b></div>`;
        });
    });
}

var currentPath = "";
function startChat(t) {
    currentPath = "pvt/"+[currentUser, t].sort().join("_");
    document.getElementById("chatTitle").innerText = t;
    nav('chat', document.querySelectorAll('nav button')[1]);
    db.ref(currentPath).off();
    db.ref(currentPath).on("value", s => {
        var b = document.getElementById("chatBox"); b.innerHTML = "";
        s.forEach(m => {
            var d = m.val();
            var content = d.type === 'img' ? `<img src="${d.txt}">` : d.txt;
            b.innerHTML += `<div class="bubble ${d.from===currentUser?'me':'other'}"><b>${d.from}</b><br>${content}</div>`;
        });
        var sc = document.getElementById("chatScroll");
        setTimeout(() => { sc.scrollTop = sc.scrollHeight; }, 100);
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

function nav(p, b) {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
}

function logout() { localStorage.clear(); location.reload(); }
</script>
</body>
</html>
