<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Master 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --p: #00f2fe; --s: #4facfe; --bg: #05070a;
            --card: rgba(25, 30, 45, 0.8); --glass: rgba(255, 255, 255, 0.06);
            --text: #ffffff; --accent: #ff3b30;
        }
        * { box-sizing: border-box; font-family: 'Inter', sans-serif; }
        body, html { margin:0; padding:0; height:100%; background: var(--bg); color: var(--text); overflow: hidden; }

        .app { max-width: 480px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

        /* Modern Header */
        header { padding: 18px 20px; background: rgba(10, 15, 25, 0.9); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--glass); z-index: 1000; }
        header h1 { font-size: 20px; font-weight: 900; background: linear-gradient(90deg, var(--p), var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        /* Story Bar */
        .stories { display: flex; gap: 15px; padding: 15px; overflow-x: auto; scrollbar-width: none; background: rgba(0,0,0,0.2); }
        .story { min-width: 62px; height: 62px; border-radius: 50%; border: 2.5px solid var(--p); padding: 3px; position: relative; }
        .story img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }

        /* Viewport */
        .main { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 180px; scroll-behavior: smooth; }
        .page { display: none; }
        .page.active { display: block; animation: fadeInUp 0.4s ease; }
        @keyframes fadeInUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        /* Bubbles & Actions */
        .msg-wrap { display: flex; flex-direction: column; margin-bottom: 15px; position: relative; }
        .bbl { padding: 12px 16px; border-radius: 20px; max-width: 80%; font-size: 14px; position: relative; }
        .bbl.me { align-self: flex-end; background: linear-gradient(135deg, var(--s), #2575fc); border-bottom-right-radius: 4px; }
        .bbl.them { align-self: flex-start; background: var(--card); border: 1px solid var(--glass); border-bottom-left-radius: 4px; }
        .del-btn { position: absolute; top: -10px; right: -10px; background: var(--accent); color: white; border-radius: 50%; width: 20px; height: 20px; font-size: 10px; display: none; align-items: center; justify-content: center; cursor: pointer; border: 2px solid var(--bg); }
        .msg-wrap:hover .del-btn { display: flex; }

        /* Typing Indicator */
        #typing { font-size: 12px; color: var(--p); margin-bottom: 10px; display: none; padding-left: 10px; }

        /* Input Dock */
        .dock { 
            position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%);
            width: 92%; max-width: 440px; background: rgba(30, 35, 50, 0.95); backdrop-filter: blur(10px);
            padding: 10px 18px; border-radius: 30px; display: flex; align-items: center; gap: 12px; border: 1px solid var(--glass); z-index: 2000;
        }
        .dock input { flex: 1; background: transparent; border: none; color: white; outline: none; }
        .dock i { color: var(--p); font-size: 20px; cursor: pointer; }

        /* Footer Nav */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 480px; height: 75px; background: #0a0f19; display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--glass); z-index: 2000; }
        nav button { background: none; border: none; color: #50586c; font-size: 22px; cursor: pointer; transition: 0.3s; }
        nav button.active { color: var(--p); transform: translateY(-5px); }

        .u-row { background: var(--card); padding: 15px; border-radius: 18px; margin-bottom: 12px; display: flex; align-items: center; gap: 15px; border: 1px solid transparent; }
        .u-row:active { border-color: var(--p); }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>INFINITY APEX</h1>
        <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--accent)"></i>
    </header>

    <div class="stories" id="stArea" style="display:none;">
        <div class="story" onclick="document.getElementById('stInp').click()">
            <img src="https://api.dicebear.com/7.x/pixel-art/svg?seed=Me">
            <input type="file" id="stInp" style="display:none" onchange="uploadSt(event)">
        </div>
        <div id="stList" style="display:flex; gap:12px;"></div>
    </div>

    <div class="main" id="scrollArea">
        <div id="v-login" class="view active">
            <div style="text-align:center; padding-top:100px;">
                <h1 style="font-size:60px;">💫</h1>
                <h2>Quantum Login</h2>
                <input type="text" id="uInp" placeholder="Node Name..." style="width:85%; padding:18px; border-radius:15px; background:var(--card); border:1px solid var(--p); color:white; outline:none; margin-top:20px; text-align:center;">
                <button onclick="login()" style="width:85%; padding:18px; border-radius:15px; background:var(--p); color:black; font-weight:bold; border:none; margin-top:20px; cursor:pointer;">CONNECT SESSION</button>
            </div>
        </div>

        <div id="v-home" class="view">
            <div style="background: linear-gradient(135deg, #1c222d, #05070a); padding: 25px; border-radius: 20px; border: 1px solid var(--glass); margin-bottom: 25px;">
                <h2 id="greet" style="margin:0">Hi!</h2>
                <p style="opacity:0.6; font-size:14px;">Node Status: <span style="color:var(--p)">Encrypted</span></p>
            </div>
            <h3>Live Directory</h3>
            <div id="uList"></div>
        </div>

        <div id="v-chat" class="view">
            <div style="padding-bottom:15px; border-bottom:1px solid var(--glass); margin-bottom:15px; display:flex; align-items:center; gap:10px;">
                <i class="fa-solid fa-chevron-left" onclick="nav('home', document.getElementById('hBtn'))"></i>
                <b id="cName">Global Base</b>
            </div>
            <div id="chatBox" style="display:flex; flex-direction:column;"></div>
            <div id="typing">Typing...</div>
        </div>
    </div>

    <div class="dock" id="dock" style="display:none;">
        <input type="file" id="fInp" style="display:none" accept="image/*" onchange="previewImg(event)">
        <i class="fa-solid fa-paperclip" onclick="document.getElementById('fInp').click()"></i>
        <input type="text" id="mInp" placeholder="Type message..." oninput="handleTyping()">
        <i class="fa-solid fa-paper-plane" onclick="send()"></i>
    </div>

    <nav id="nav" style="display:none;">
        <button onclick="nav('home', this)" id="hBtn" class="active"><i class="fa-solid fa-house-user"></i></button>
        <button onclick="nav('chat', this)" id="cBtn"><i class="fa-solid fa-comments"></i></button>
        <button onclick="nav('users', this)"><i class="fa-solid fa-users-viewfinder"></i></button>
        <button onclick="logout()"><i class="fa-solid fa-gear"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("master_user");
let room = "Global_Lobby";
let isP = false;
let tempImg = "";
let typeTimer;

if(user) start();

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(!v) return;
    user = v;
    localStorage.setItem("master_user", v);
    start();
};

function start() {
    document.getElementById("v-login").classList.remove("active");
    document.getElementById("v-home").classList.add("active");
    document.getElementById("nav").style.display = "flex";
    document.getElementById("stArea").style.display = "flex";
    document.getElementById("greet").innerText = "Hi, " + user;
    
    set(ref(db, "presence/" + user), { online: true });
    onDisconnect(ref(db, "presence/" + user)).remove();
    load();
}

window.nav = (id, btn) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + id).classList.add("active");
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    document.getElementById("dock").style.display = (id === 'chat') ? 'flex' : 'none';
    if(id === 'chat') sync();
};

function load() {
    onValue(ref(db, "presence"), snap => {
        const ul = document.getElementById("uList");
        ul.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== user) {
                const div = document.createElement("div");
                div.className = "u-row";
                div.innerHTML = `<img src="https://api.dicebear.com/7.x/identicon/svg?seed=${u.key}" style="width:40px; border-radius:50%">
                                 <div><b>${u.key}</b><br><small style="color:var(--p)">Active</small></div>`;
                div.onclick = () => {
                    room = [user, u.key].sort().join("__");
                    isP = true;
                    document.getElementById("cName").innerText = u.key;
                    nav('chat', document.getElementById('cBtn'));
                };
                ul.appendChild(div);
            }
        });
    });

    onValue(ref(db, "typing/" + room), snap => {
        const t = document.getElementById("typing");
        if(snap.val() && snap.val() !== user) t.style.display = "block";
        else t.style.display = "none";
    });
}

window.handleTyping = () => {
    set(ref(db, "typing/" + room), user);
    clearTimeout(typeTimer);
    typeTimer = setTimeout(() => { set(ref(db, "typing/" + room), null); }, 2000);
};

window.previewImg = (e) => {
    const reader = new FileReader();
    reader.onload = () => { tempImg = reader.result; alert("Image Ready to Send!"); };
    reader.readAsDataURL(e.target.files[0]);
};

function sync() {
    const path = isP ? "private_m/" + room : "global_m/main";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const wrap = document.createElement("div");
            wrap.className = "msg-wrap";
            wrap.innerHTML = `<div class="bbl ${d.from === user ? "me" : "them"}">
                                <b>${d.from}</b>${d.text}
                                ${d.img ? `<br><img src="${d.img}" style="max-width:100%; border-radius:10px; margin-top:5px;">` : ''}
                              </div>
                              ${d.from === user ? `<div class="del-btn" onclick="delMsg('${m.key}')"><i class="fa-solid fa-trash"></i></div>` : ''}`;
            box.appendChild(wrap);
        });
        document.getElementById('scrollArea').scrollTop = document.getElementById('scrollArea').scrollHeight;
    });
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim() && !tempImg) return;
    const path = isP ? "private_m/" + room : "global_m/main";
    push(ref(db, path), { from: user, text: i.value, img: tempImg });
    i.value = ""; tempImg = "";
};

window.delMsg = (key) => {
    const path = isP ? "private_m/" + room : "global_m/main";
    remove(ref(db, path + "/" + key));
};

window.uploadSt = (e) => {
    const reader = new FileReader();
    reader.onload = () => { set(ref(db, "status_m/" + user), { img: reader.result }); alert("Status Updated!"); };
    reader.readAsDataURL(e.target.files[0]);
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
