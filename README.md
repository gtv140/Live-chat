<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root { --bg: #05070a; --card: #0d1117; --primary: #00d2ff; --accent: #ff0055; --text: #e6edf3; --success: #00ff88; --glass: rgba(13, 17, 23, 0.85); }
body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); height: 100dvh; overflow: hidden; }

/* LOGIN SCREEN */
#loginPage { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; }
.login-card { background: var(--card); padding: 40px; border-radius: 30px; width: 85%; max-width: 380px; text-align: center; border: 1px solid #30363d; box-shadow: 0 0 50px rgba(0, 210, 255, 0.1); }
.login-card i { font-size: 50px; color: var(--primary); margin-bottom: 20px; }
.login-card input { width: 100%; padding: 15px; margin: 25px 0; border-radius: 12px; border: 1px solid #30363d; background: #010409; color: #fff; outline: none; text-align: center; font-size: 16px; box-sizing: border-box; }
.login-card button { width: 100%; padding: 16px; border-radius: 12px; border: none; background: linear-gradient(135deg, var(--primary), #bc8cff); color: white; font-weight: bold; cursor: pointer; text-transform: uppercase; letter-spacing: 1px; }

/* HEADER */
header { padding: 50px 20px 15px; display: flex; justify-content: space-between; align-items: center; background: var(--glass); backdrop-filter: blur(15px); border-bottom: 1px solid #30363d; z-index: 100; }
header h1 { margin: 0; font-size: 22px; background: linear-gradient(to right, #00d2ff, #9d50bb); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }

.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 180px; animation: fadeIn 0.4s ease; }
.page.active { display: flex; flex-direction: column; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

/* DASHBOARD */
.hero { background: linear-gradient(135deg, #2188ff, #8957e5); padding: 35px; border-radius: 25px; text-align: center; margin-bottom: 25px; box-shadow: 0 15px 35px rgba(0,0,0,0.4); }
.hero h2 { margin: 0; font-size: 28px; }
.stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
.stat-box { background: var(--card); padding: 20px; border-radius: 20px; text-align: center; border: 1px solid #30363d; }
.stat-box span { display: block; font-size: 26px; font-weight: bold; color: var(--primary); }

/* CHAT BOX */
#chatBox { flex: 1; display: flex; flex-direction: column; gap: 12px; }
.msg { padding: 12px 16px; border-radius: 18px; max-width: 80%; font-size: 14px; position: relative; line-height: 1.4; }
.msg.me { align-self: flex-end; background: var(--primary); color: #000; border-bottom-right-radius: 2px; }
.msg.other { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; }
.msg b { display: block; font-size: 10px; opacity: 0.7; margin-bottom: 4px; }
.msg img { max-width: 100%; border-radius: 12px; margin-top: 8px; border: 1px solid rgba(255,255,255,0.1); }

.input-area { position: fixed; bottom: 85px; width: 90%; max-width: 440px; left: 50%; transform: translateX(-50%); background: #161b22; padding: 10px 15px; border-radius: 30px; display: flex; align-items: center; gap: 10px; border: 1px solid #30363d; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
.input-area input { flex: 1; background: none; border: none; color: white; outline: none; padding: 10px; }

/* STATUS DOTS */
.dot { width: 8px; height: 8px; border-radius: 50%; display: inline-block; margin-right: 10px; }
.online { background: var(--success); box-shadow: 0 0 10px var(--success); }
.offline { background: #30363d; }

/* NAVIGATION */
nav { position: fixed; bottom: 15px; left: 50%; transform: translateX(-50%); width: 90%; max-width: 440px; background: var(--glass); backdrop-filter: blur(20px); height: 70px; border-radius: 25px; display: flex; justify-content: space-around; align-items: center; border: 1px solid #30363d; z-index: 1000; }
nav button { background: none; border: none; color: #484f58; font-size: 22px; cursor: pointer; transition: 0.3s; }
nav button.active { color: var(--primary); transform: translateY(-5px); }

/* HIJACK SCREEN */
#hjkScreen { display: none; position: fixed; inset: 0; background: #000; z-index: 100000; flex-direction: column; align-items: center; justify-content: center; color: var(--accent); text-align: center; }
</style>
</head>
<body>

<div id="loginPage">
    <div class="login-card">
        <i class="fa-solid fa-satellite-dish"></i>
        <h1 style="color:white; margin:0;">Live Connect</h1>
        <p style="color:var(--primary); font-size:12px; margin-top:5px;">SECURE NODE INITIALIZATION</p>
        <input type="text" id="uInp" placeholder="Enter Username...">
        <button onclick="login()">Initialize Link</button>
    </div>
</div>

<div id="hjkScreen">
    <i class="fa-solid fa-skull-crossbones" style="font-size:70px; margin-bottom:20px;"></i>
    <h1 style="letter-spacing:10px;">HIJACKED</h1>
    <p style="opacity:0.5;">Master Admin has revoked your terminal access.</p>
</div>

<div class="app" style="display:flex; flex-direction:column; height:100dvh;">
    <header>
        <h1>Live Connect</h1>
        <div style="display:flex; gap:20px; align-items:center;">
            <i class="fa-solid fa-ghost" id="admIco" style="display:none; color:#bc8cff; font-size:20px;" onclick="nav('admin', this)"></i>
            <i class="fa-solid fa-power-off" style="color:var(--accent); font-size:20px;" onclick="logout()"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="hero">
            <h2 id="welcome">Welcome, Node</h2>
            <p style="font-size:12px; opacity:0.8;">Secure Network Active</p>
        </div>
        <div class="stats-grid">
            <div class="stat-box"><span id="onStats">0</span><h5>ONLINE</h5></div>
            <div class="stat-box"><span id="grStats">0</span><h5>CLUSTERS</h5></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div class="input-area">
            <label for="imgInp"><i class="fa-solid fa-camera" style="color:var(--primary); cursor:pointer;"></i></label>
            <input type="file" id="imgInp" hidden accept="image/*" onchange="upImg(this)">
            <input id="msgInp" placeholder="Enter secure packet...">
            <i class="fa-solid fa-paper-plane" style="color:var(--primary); cursor:pointer;" onclick="send()"></i>
        </div>
    </div>

    <div id="nodes" class="page">
        <h4 style="font-size:11px; opacity:0.4; letter-spacing:2px; margin-bottom:15px;">ACTIVE TERMINALS</h4>
        <div id="uList"></div>
        <div style="margin-top:30px; display:flex; justify-content:space-between; align-items:center;">
            <h4 style="font-size:11px; opacity:0.4; letter-spacing:2px;">CLUSTERS</h4>
            <button onclick="mkGr()" style="background:none; border:1px solid var(--primary); color:var(--primary); font-size:10px; padding:4px 10px; border-radius:8px;">+ NEW CLUSTER</button>
        </div>
        <div id="gList" style="margin-top:10px;"></div>
    </div>

    <div id="admin" class="page">
        <h3 style="color:var(--accent); font-size:14px; margin-bottom:20px;">> MASTER_CONTROL_UNIT</h3>
        <div id="aList"></div>
        <button onclick="nuke()" style="width:100%; padding:18px; background:var(--accent); color:white; border:none; border-radius:15px; font-weight:bold; margin-top:30px; cursor:pointer;">NUCLEAR WIPE</button>
    </div>

    <nav>
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="nav('chat', this)"><i class="fa-solid fa-comments"></i></button>
        <button onclick="nav('nodes', this)"><i class="fa-solid fa-fingerprint"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect, get } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

// YOUR CONFIG
const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.firebasestorage.app",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let user = localStorage.getItem("lc_u");
if(user) { document.getElementById("loginPage").style.display = "none"; start(user); }

window.login = () => {
    let u = document.getElementById("uInp").value.trim();
    if(u) {
        const userRef = ref(db, "users/" + u);
        set(userRef, { online: true, frozen: false, lastSeen: Date.now() })
        .then(() => { localStorage.setItem("lc_u", u); location.reload(); })
        .catch(() => alert("Error: Rules Check Karein!"));
    }
};

function start(u) {
    document.getElementById("welcome").innerText = "Welcome, " + u;
    const myRef = ref(db, "users/"+u);
    update(myRef, { online: true });
    onDisconnect(myRef).update({ online: false });

    if(u === "Admin786") document.getElementById("admIco").style.display = "block";

    onValue(ref(db, "users/"+u+"/frozen"), s => {
        if(s.val()) document.getElementById("hjkScreen").style.display = "flex";
        else document.getElementById("hjkScreen").style.display = "none";
    });

    onValue(ref(db, "users"), s => {
        const ul = document.getElementById("uList"), al = document.getElementById("aList");
        let on = 0; ul.innerHTML = ""; al.innerHTML = "";
        s.forEach(n => {
            const d = n.val(); if(d.online) on++;
            if(n.key !== u) {
                ul.innerHTML += `<div style="background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px; display:flex; justify-content:space-between; align-items:center;" onclick="joinChat('${n.key}', false)">
                    <span><span class="dot ${d.online?'online':'offline'}"></span>${n.key}</span><i class="fa-solid fa-chevron-right" style="opacity:0.2"></i>
                </div>`;
                if(u === "Admin786") al.innerHTML += `<div style="background:#161b22; padding:12px; border-radius:12px; margin-bottom:8px; display:flex; justify-content:space-between; align-items:center;">
                    <span>${n.key}</span><button onclick="hjk('${n.key}', ${!d.frozen})" style="background:${d.frozen?var(--success):var(--accent)}; color:white; border:none; border-radius:8px; padding:6px 12px; font-size:10px; font-weight:bold; cursor:pointer;">${d.frozen?'RESTORE':'HIJACK'}</button>
                </div>`;
            }
        });
        document.getElementById("onStats").innerText = on;
    });

    onValue(ref(db, "groups"), s => {
        const gl = document.getElementById("gList"); gl.innerHTML = "";
        document.getElementById("grStats").innerText = s.size || 0;
        s.forEach(g => { gl.innerHTML += `<div style="background:var(--card); padding:15px; border-radius:15px; margin-bottom:10px;" onclick="joinChat('${g.key}', true)"># ${g.key}</div>`; });
    });
}

let path = "global";
window.joinChat = (t, isG) => {
    path = isG ? "groups/"+t : "pvt/"+[localStorage.getItem("lc_u"), t].sort().join("_");
    nav('chat', document.querySelectorAll('nav button')[1]);
    onValue(ref(db, path), s => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        s.forEach(m => {
            const d = m.val(); const isMe = d.from === localStorage.getItem("lc_u");
            const del = (localStorage.getItem("lc_u") === "Admin786") ? `<i class="fa-solid fa-trash" onclick="delM('${path}','${m.key}')" style="margin-left:10px; color:red; font-size:10px; cursor:pointer;"></i>` : "";
            box.innerHTML += `<div class="msg ${isMe?'me':'other'}"><b>${d.from}</b>${d.type==='img'?'<img src="'+d.txt+'">':d.txt}${del}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
};

window.send = () => {
    let i = document.getElementById("msgInp"); if(!i.value) return;
    push(ref(db, path), { from: localStorage.getItem("lc_u"), txt: i.value, type: 'text', time: Date.now() });
    i.value = "";
};

window.upImg = (e) => {
    let r = new FileReader();
    r.onload = (f) => push(ref(db, path), { from: localStorage.getItem("lc_u"), txt: f.target.result, type: 'img', time: Date.now() });
    r.readAsDataURL(e.files[0]);
};

window.nav = (p, b) => {
    document.querySelectorAll('.page').forEach(x => x.classList.remove('active'));
    document.getElementById(p).classList.add('active');
    document.querySelectorAll('nav button').forEach(x => x.classList.remove('active'));
    if(b && b.tagName === 'BUTTON') b.classList.add('active');
};

window.hjk = (t, s) => update(ref(db, "users/"+t), { frozen: s });
window.delM = (p, k) => { if(confirm("Delete this message?")) remove(ref(db, p+"/"+k)); };
window.nuke = () => { if(confirm("NUCLEAR WIPE? EVERYTHING WILL BE DELETED!")) { remove(ref(db)); location.reload(); } };
window.mkGr = () => { let n = prompt("Cluster Name:"); if(n) set(ref(db, "groups/"+n), { c: Date.now() }); };
window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
