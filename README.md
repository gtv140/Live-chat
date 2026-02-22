<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Pro Max 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --primary: #00f2fe; --secondary: #4facfe; --bg: #06080f;
            --card: #121620; --glass: rgba(255, 255, 255, 0.08);
            --text: #ffffff; --muted: #8e9297; --accent: #ff007a;
        }
        * { box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
        body, html { margin:0; padding:0; height:100%; background: var(--bg); color: var(--text); overflow: hidden; }

        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

        header { padding: 15px 20px; background: var(--card); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--glass); z-index: 1000; }
        header h1 { font-size: 18px; font-weight: 800; background: linear-gradient(45deg, var(--primary), var(--secondary)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        .viewport { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 180px; scroll-behavior: smooth; }
        .view { display: none; }
        .view.active { display: block; animation: fadeIn 0.3s ease; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Chat Bubbles */
        .bubble { padding: 10px 14px; border-radius: 18px; max-width: 80%; font-size: 14px; margin-bottom: 12px; position: relative; line-height: 1.4; }
        .bubble.sent { align-self: flex-end; background: #005c4b; margin-left: auto; border-bottom-right-radius: 2px; }
        .bubble.received { align-self: flex-start; background: var(--card); border: 1px solid var(--glass); border-bottom-left-radius: 2px; }
        .bubble img { max-width: 100%; border-radius: 10px; margin-top: 5px; }

        /* FIXED INPUT DOCK */
        .input-dock { 
            position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%);
            width: 95%; max-width: 480px; background: #1c222d; padding: 10px 15px; 
            border-radius: 25px; display: flex; align-items: center; gap: 12px; 
            border: 1px solid var(--glass); z-index: 2000; box-shadow: 0 5px 25px rgba(0,0,0,0.7);
        }
        .input-dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 15px; }
        .input-dock i { color: var(--primary); font-size: 20px; cursor: pointer; transition: 0.2s; }

        /* Image Preview Box */
        #imgPreviewBox { 
            position: fixed; bottom: 145px; left: 50%; transform: translateX(-50%);
            width: 90%; max-width: 400px; background: var(--card); border: 1px solid var(--primary);
            border-radius: 15px; padding: 10px; display: none; z-index: 2100;
        }
        #imgPreviewBox img { width: 100%; border-radius: 10px; height: 150px; object-fit: cover; }

        /* Bottom Nav */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: var(--card); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--glass); z-index: 2000; }
        nav button { background: none; border: none; color: var(--muted); display: flex; flex-direction: column; align-items: center; gap: 5px; cursor: pointer; }
        nav button.active { color: var(--primary); }

        .row { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; cursor: pointer; }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1>INFINITY ULTRA PRO</h1>
        <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--accent)"></i>
    </header>

    <div class="viewport" id="scrollArea">
        <div id="v-login" class="view active">
            <div style="text-align:center; padding-top:100px;">
                <i class="fa-solid fa-bolt" style="font-size:60px; color:var(--primary)"></i>
                <h2>Quantum Login</h2>
                <input type="text" id="uInp" placeholder="Create Alias..." style="width:80%; padding:15px; border-radius:12px; background:var(--card); border:1px solid var(--primary); color:white; outline:none; margin-top:20px;">
                <button onclick="login()" style="width:80%; padding:15px; border-radius:12px; background:var(--primary); color:black; font-weight:bold; border:none; margin-top:20px;">INITIALIZE SYNC</button>
            </div>
        </div>

        <div id="v-home" class="view">
            <div style="background: linear-gradient(135deg, #1c222d, #06080f); padding: 25px; border-radius: 20px; border: 1px solid var(--primary); margin-bottom: 20px;">
                <h2 id="greet" style="margin:0">Welcome</h2>
                <p style="opacity:0.6;">System Status: Secure</p>
            </div>
            <h3>Active Connections</h3>
            <div id="dashUsers"></div>
        </div>

        <div id="v-chat" class="view">
            <div style="text-align:center; padding-bottom:15px; color:var(--primary); font-weight:bold;" id="chatTitle">Global Channel</div>
            <div id="chatBox" style="display:flex; flex-direction:column;"></div>
        </div>

        <div id="v-users" class="view">
            <h3>Network Directory</h3>
            <div id="fullList"></div>
        </div>
    </div>

    <div id="imgPreviewBox">
        <div style="display:flex; justify-content:space-between; margin-bottom:5px;">
            <span>Photo Preview</span>
            <i class="fa-solid fa-times" onclick="cancelImg()" style="color:red"></i>
        </div>
        <img id="previewImg" src="">
    </div>

    <div class="input-dock" id="typeDock" style="display:none;">
        <input type="file" id="fileInp" style="display:none" accept="image/*" onchange="previewFile(event)">
        <i class="fa-solid fa-plus-circle" onclick="document.getElementById('fileInp').click()"></i>
        <input type="text" id="mInp" placeholder="Type a secure message...">
        <i class="fa-solid fa-paper-plane" onclick="send()"></i>
    </div>

    <nav id="footer" style="display:none;">
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="nav('chat', this)" id="cTab"><i class="fa-solid fa-comments"></i></button>
        <button onclick="nav('users', this)"><i class="fa-solid fa-user-group"></i></button>
        <button onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("ultra_user");
let room = "Global_Lobby";
let isP = false;
let selectedImg = "";

if(user) startApp();

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(!v) return;
    user = v;
    localStorage.setItem("ultra_user", v);
    startApp();
};

function startApp() {
    document.getElementById("v-login").classList.remove("active");
    document.getElementById("v-home").classList.add("active");
    document.getElementById("footer").style.display = "flex";
    document.getElementById("greet").innerText = "Welcome, " + user;
    
    set(ref(db, "users/" + user), { online: true });
    onDisconnect(ref(db, "users/" + user)).set({ online: false });
    load();
}

window.nav = (id, btn) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + id).classList.add("active");
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    document.getElementById("typeDock").style.display = (id === 'chat') ? 'flex' : 'none';
    if(id === 'chat') sync();
};

function load() {
    onValue(ref(db, "users"), snap => {
        const fl = document.getElementById("fullList");
        const dl = document.getElementById("dashUsers");
        fl.innerHTML = ""; dl.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online && u.key !== user) {
                const html = `<div class="row"><div style="width:40px;height:40px;background:var(--primary);border-radius:50%;display:flex;align-items:center;justify-content:center;color:black;font-weight:bold">${u.key[0]}</div><div><b>${u.key}</b><br><small style="color:var(--primary)">Available</small></div></div>`;
                const div = document.createElement("div");
                div.innerHTML = html;
                div.onclick = () => {
                    room = [user, u.key].sort().join("_");
                    isP = true;
                    document.getElementById("chatTitle").innerText = "Secure: " + u.key;
                    nav('chat', document.getElementById('cTab'));
                };
                fl.appendChild(div.cloneNode(true));
                dl.appendChild(div);
            }
        });
    });
}

window.previewFile = (event) => {
    const file = event.target.files[0];
    const reader = new FileReader();
    reader.onload = () => {
        selectedImg = reader.result;
        document.getElementById("previewImg").src = selectedImg;
        document.getElementById("imgPreviewBox").style.display = "block";
    };
    reader.readAsDataURL(file);
};

window.cancelImg = () => {
    selectedImg = "";
    document.getElementById("imgPreviewBox").style.display = "none";
};

function sync() {
    const path = isP ? "private/" + room : "global/main";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div");
            div.className = "bubble " + (d.from === user ? "sent" : "received");
            let content = `<b>${d.from}</b>${d.text}`;
            if(d.img) content += `<br><img src="${d.img}">`;
            div.innerHTML = content;
            box.appendChild(div);
        });
        document.getElementById('scrollArea').scrollTop = document.getElementById('scrollArea').scrollHeight;
    });
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim() && !selectedImg) return;
    const path = isP ? "private/" + room : "global/main";
    push(ref(db, path), { from: user, text: i.value, img: selectedImg });
    i.value = "";
    cancelImg();
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
