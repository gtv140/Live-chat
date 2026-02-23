<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Live Connect Mobile Fit</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <style>
        :root { 
            --bg: #010409; --card: #0d1117; --primary: #00d2ff; --text: #e6edf3;
            --grad: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
        }
        
        /* MOBILE RESET */
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; margin: 0; padding: 0; }
        body { 
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: var(--bg); color: var(--text);
            height: 100vh; height: 100dvh; /* Dynamic Viewport for Mobile */
            display: flex; flex-direction: column; overflow: hidden;
        }

        /* HEADER - Compact for Small Screens */
        header { 
            height: 60px; padding: 0 15px; display: flex; justify-content: space-between; 
            align-items: center; border-bottom: 1px solid #21262d; flex-shrink: 0;
        }
        header h1 { font-size: 18px; font-weight: 800; color: var(--primary); }

        /* MAIN CONTENT AREA */
        .viewport { flex: 1; overflow: hidden; display: flex; flex-direction: column; position: relative; }
        .page { display: none; height: 100%; flex-direction: column; overflow: hidden; }
        .page.active { display: flex; }

        /* CHAT BOX - AUTO ADJUSTING HEIGHT */
        .chat-wrapper { 
            flex: 1; display: flex; flex-direction: column; 
            overflow: hidden; background: #010409;
        }
        #chatScroll { 
            flex: 1; overflow-y: auto; padding: 15px; 
            display: flex; flex-direction: column; gap: 10px;
            -webkit-overflow-scrolling: touch;
        }

        /* BUBBLES - Optimized for narrow screens */
        .bubble { padding: 10px 14px; border-radius: 18px; max-width: 85%; font-size: 14px; word-wrap: break-word; }
        .bubble.me { align-self: flex-end; background: var(--grad); border-bottom-right-radius: 2px; }
        .bubble.other { align-self: flex-start; background: #161b22; border: 1px solid #30363d; border-bottom-left-radius: 2px; }
        .bubble img { width: 100%; max-height: 200px; object-fit: cover; border-radius: 10px; margin-top: 5px; }

        /* INPUT AREA - PINNED ABOVE NAV */
        .input-area { 
            padding: 10px; background: #0d1117; border-top: 1px solid #21262d;
            display: flex; align-items: center; gap: 10px; flex-shrink: 0;
        }
        .input-area input { 
            flex: 1; background: #010409; border: 1px solid #30363d; 
            padding: 10px 15px; border-radius: 20px; color: white; outline: none; font-size: 16px; /* 16px prevents zoom on iPhone */
        }

        /* TAB BAR - Modern & Mobile Friendly */
        nav { 
            height: 65px; display: flex; justify-content: space-around; 
            align-items: center; background: #010409; border-top: 1px solid #21262d;
            flex-shrink: 0; padding-bottom: env(safe-area-inset-bottom);
        }
        nav button { background: none; border: none; color: #484f58; font-size: 20px; flex: 1; height: 100%; }
        nav button.active { color: var(--primary); }

        /* LISTS */
        .list-container { flex: 1; overflow-y: auto; padding: 10px; }
        .card { background: #0d1117; padding: 12px; border-radius: 12px; margin-bottom: 8px; border: 1px solid #21262d; display: flex; align-items: center; gap: 10px; }

        #login { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 20px; }
    </style>
</head>
<body>

<div id="login">
    <h2 style="margin-bottom:20px; color:var(--primary);">Connect</h2>
    <input type="text" id="uInp" placeholder="Username" style="width:100%; max-width:300px; padding:15px; border-radius:12px; background:#111; border:1px solid #333; color:white; text-align:center;">
    <button onclick="doLogin()" style="width:100%; max-width:300px; padding:15px; margin-top:15px; border-radius:12px; background:var(--grad); color:white; border:none; font-weight:bold;">START</button>
</div>

<header>
    <h1 id="viewTitle">Live Connect</h1>
    <i class="fa-solid fa-power-off" style="color:#ff0055;" onclick="logout()"></i>
</header>

<div class="viewport">
    <div id="home" class="page active">
        <div class="list-container">
            <div style="background:var(--grad); padding:20px; border-radius:15px; margin-bottom:15px;">
                <h3 id="welcome">Welcome</h3>
                <p style="font-size:12px; opacity:0.8;">Mobile Optimized Build</p>
            </div>
        </div>
    </div>

    <div id="chat" class="page">
        <div class="chat-wrapper">
            <div id="chatScroll">
                <div id="chatBox"></div>
            </div>
            <div class="input-area">
                <label for="imgInp"><i class="fa-solid fa-image" style="color:var(--primary); font-size:20px;"></i></label>
                <input type="file" id="imgInp" hidden onchange="upImg(this)" accept="image/*">
                <input id="msgInp" placeholder="Type a message...">
                <i class="fa-solid fa-paper-plane" style="color:var(--primary); font-size:20px;" onclick="sendMsg()"></i>
            </div>
        </div>
    </div>

    <div id="nodes" class="page">
        <div class="list-container" id="uList"></div>
    </div>
</div>

<nav>
    <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
    <button onclick="nav('chat', this)"><i class="fa-solid fa-comment"></i></button>
    <button onclick="nav('nodes', this)"><i class="fa-solid fa-users"></i></button>
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
    document.getElementById("welcome").innerText = "Hi, " + u;
    db.ref("users").on("value", s => {
        var ul = document.getElementById("uList"); ul.innerHTML = "";
        s.forEach(n => {
            if(n.key !== u) ul.innerHTML += `<div class="card" onclick="startChat('${n.key}')"><div style="width:35px; height:35px; background:var(--grad); border-radius:50%; display:flex; align-items:center; justify-content:center; font-weight:bold;">${n.key[0]}</div><b>${n.key}</b></div>`;
        });
    });
}

var currentPath = "";
function startChat(t) {
    currentPath = "pvt/"+[currentUser, t].sort().join("_");
    document.getElementById("viewTitle").innerText = t;
    nav('chat', document.querySelectorAll('nav button')[1]);
    db.ref(currentPath).off();
    db.ref(currentPath).on("value", s => {
        var box = document.getElementById("chatBox"); box.innerHTML = "";
        s.forEach(m => {
            var d = m.val();
            var content = d.type === 'img' ? `<img src="${d.txt}">` : d.txt;
            box.innerHTML += `<div class="bubble ${d.from===currentUser?'me':'other'}"><b>${d.from}</b><br>${content}</div>`;
        });
        scrollDown();
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

function scrollDown() {
    var sc = document.getElementById("chatScroll");
    setTimeout(() => { sc.scrollTop = sc.scrollHeight; }, 100);
}

function nav(p, b) {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
    if(p === 'chat') scrollDown();
}

function logout() { localStorage.clear(); location.reload(); }
</script>
</body>
</html>
