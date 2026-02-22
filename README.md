<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Ultra Pro Max 🚀</title>
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

        /* Header */
        header { padding: 15px 20px; background: var(--card); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--glass); z-index: 1000; }
        header h1 { font-size: 18px; font-weight: 800; background: linear-gradient(45deg, var(--primary), var(--secondary)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        /* Status Bar */
        .status-bar { display: flex; gap: 12px; padding: 15px; overflow-x: auto; scrollbar-width: none; background: rgba(255,255,255,0.02); }
        .status-node { min-width: 60px; height: 60px; border-radius: 50%; border: 2px solid var(--primary); padding: 2px; cursor: pointer; position: relative; }
        .status-node img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
        .add-status { position: absolute; bottom: 0; right: 0; background: var(--secondary); border-radius: 50%; width: 18px; height: 18px; display: flex; align-items: center; justify-content: center; font-size: 12px; border: 2px solid var(--bg); }

        /* Viewport */
        .viewport { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 180px; scroll-behavior: smooth; }
        .view { display: none; }
        .view.active { display: block; animation: fadeIn 0.3s ease; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* Bubbles */
        .bubble { padding: 10px 14px; border-radius: 18px; max-width: 85%; font-size: 14px; margin-bottom: 12px; position: relative; animation: slideIn 0.2s ease-out; }
        @keyframes slideIn { from { opacity: 0; transform: scale(0.9); } to { opacity: 1; transform: scale(1); } }
        .bubble.sent { align-self: flex-end; background: #005c4b; margin-left: auto; border-bottom-right-radius: 2px; }
        .bubble.received { align-self: flex-start; background: var(--card); border: 1px solid var(--glass); border-bottom-left-radius: 2px; }
        .bubble img { max-width: 100%; border-radius: 10px; margin-top: 8px; border: 1px solid rgba(255,255,255,0.1); }

        /* FIXED INPUT DOCK */
        .input-dock { 
            position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%);
            width: 95%; max-width: 480px; background: #1c222d; padding: 10px 15px; 
            border-radius: 25px; display: flex; align-items: center; gap: 12px; 
            border: 1px solid var(--glass); z-index: 2000; box-shadow: 0 5px 25px rgba(0,0,0,0.7);
        }
        .input-dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 15px; }
        .input-dock i { color: var(--primary); font-size: 20px; cursor: pointer; }

        /* Nav */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: var(--card); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--glass); z-index: 2000; }
        nav button { background: none; border: none; color: var(--muted); display: flex; flex-direction: column; align-items: center; gap: 5px; cursor: pointer; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: translateY(-5px); }

        /* Preview Box */
        #imgPreviewBox { 
            position: fixed; bottom: 145px; left: 50%; transform: translateX(-50%);
            width: 90%; max-width: 400px; background: var(--card); border: 1px solid var(--primary);
            border-radius: 15px; padding: 10px; display: none; z-index: 2100;
        }
        #imgPreviewBox img { width: 100%; border-radius: 10px; max-height: 200px; object-fit: contain; }

        .row { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; cursor: pointer; transition: 0.2s; }
        .row:active { transform: scale(0.98); }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1>INFINITY ULTRA</h1>
        <div style="display:flex; gap:18px;">
            <i class="fa-solid fa-search" onclick="alert('Search feature active')"></i>
            <i class="fa-solid fa-moon" onclick="document.body.style.filter='hue-rotate(90deg)'"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--accent)"></i>
        </div>
    </header>

    <div class="status-bar" id="statusArea" style="display:none;">
        <div class="status-node" onclick="document.getElementById('statusInp').click()">
            <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Me" id="myStatusImg">
            <div class="add-status"><i class="fa-solid fa-plus"></i></div>
            <input type="file" id="statusInp" style="display:none" accept="image/*" onchange="uploadStatus(event)">
        </div>
        <div id="otherStatuses" style="display:flex; gap:12px;"></div>
    </div>

    <div class="viewport" id="scrollArea">
        <div id="v-login" class="view active">
            <div style="text-align:center; padding-top:100px;">
                <div style="font-size:80px; margin-bottom:20px;">🚀</div>
                <h2>Initialize Connection</h2>
                <input type="text" id="uInp" placeholder="Enter Username..." style="width:85%; padding:18px; border-radius:15px; background:var(--card); border:1px solid var(--primary); color:white; outline:none; margin-top:10px;">
                <button onclick="login()" style="width:85%; padding:18px; border-radius:15px; background:var(--primary); color:black; font-weight:bold; border:none; margin-top:20px; box-shadow: 0 0 20px rgba(0,242,254,0.4);">CONNECT NOW</button>
            </div>
        </div>

        <div id="v-home" class="view">
            <div style="background: linear-gradient(135deg, #121620, #1c222d); padding: 25px; border-radius: 20px; border: 1px solid var(--glass); margin-bottom: 20px;">
                <h2 id="greet" style="margin:0">Hi!</h2>
                <p style="opacity:0.6; font-size: 14px;">Network: <span id="onlineCount" style="color:var(--primary)">0</span> Users Live</p>
            </div>
            <h3 style="margin-left:5px;">Recent Contacts</h3>
            <div id="dashUsers"></div>
        </div>

        <div id="v-chat" class="view">
            <div style="display:flex; justify-content:space-between; align-items:center; padding-bottom:15px; border-bottom:1px solid var(--glass); margin-bottom:15px;">
                <i class="fa-solid fa-chevron-left" onclick="nav('home', document.getElementById('hBtn'))"></i>
                <b id="chatTitle">Global Lobby</b>
                <i class="fa-solid fa-video" onclick="alert('Video Call Feature Coming Soon')"></i>
            </div>
            <div id="chatBox" style="display:flex; flex-direction:column;"></div>
        </div>
    </div>

    <div id="imgPreviewBox">
        <div style="display:flex; justify-content:space-between; margin-bottom:8px;">
            <span style="font-weight:bold">Sending Photo...</span>
            <i class="fa-solid fa-circle-xmark" onclick="cancelImg()" style="color:var(--accent); cursor:pointer;"></i>
        </div>
        <img id="previewImg" src="">
    </div>

    <div class="input-dock" id="typeDock" style="display:none;">
        <input type="file" id="fileInp" style="display:none" accept="image/*" onchange="previewFile(event)">
        <i class="fa-solid fa-image" onclick="document.getElementById('fileInp').click()"></i>
        <input type="text" id="mInp" placeholder="Type a message...">
        <i class="fa-solid fa-microphone" onclick="alert('Mic Active')"></i>
        <i class="fa-solid fa-paper-plane" style="color:var(--primary)" onclick="send()"></i>
    </div>

    <nav id="footer" style="display:none;">
        <button onclick="nav('home', this)" id="hBtn" class="active"><i class="fa-solid fa-house-chimney"></i></button>
        <button onclick="nav('chat', this)" id="cTab"><i class="fa-solid fa-message"></i></button>
        <button onclick="nav('users', this)"><i class="fa-solid fa-globe"></i></button>
        <button onclick="alert('Settings Locked')"><i class="fa-solid fa-sliders"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("ultra_v4_user");
let room = "Global_Main";
let isP = false;
let selectedImg = "";

if(user) startApp();

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(!v) return;
    user = v;
    localStorage.setItem("ultra_v4_user", v);
    startApp();
};

function startApp() {
    document.getElementById("v-login").classList.remove("active");
    document.getElementById("v-home").classList.add("active");
    document.getElementById("footer").style.display = "flex";
    document.getElementById("statusArea").style.display = "flex";
    document.getElementById("greet").innerText = "Hi, " + user + "!";
    
    set(ref(db, "online/" + user), { status: "live", lastSeen: Date.now() });
    onDisconnect(ref(db, "online/" + user)).remove();
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
    onValue(ref(db, "online"), snap => {
        const dl = document.getElementById("dashUsers");
        let count = 0; dl.innerHTML = "";
        snap.forEach(u => {
            count++;
            if(u.key !== user) {
                const div = document.createElement("div");
                div.className = "row";
                div.innerHTML = `<div style="width:45px;height:45px;background:var(--primary);border-radius:50%;display:flex;align-items:center;justify-content:center;color:black;font-weight:bold">${u.key[0]}</div>
                                 <div><b>${u.key}</b><br><small style="color:var(--primary)">• Online</small></div>`;
                div.onclick = () => {
                    room = [user, u.key].sort().join("_");
                    isP = true;
                    document.getElementById("chatTitle").innerText = u.key;
                    nav('chat', document.getElementById('cTab'));
                };
                dl.appendChild(div);
            }
        });
        document.getElementById("onlineCount").innerText = count;
    });

    // Load Statuses
    onValue(ref(db, "status"), snap => {
        const stArea = document.getElementById("otherStatuses");
        stArea.innerHTML = "";
        snap.forEach(s => {
            if(s.key !== user) {
                stArea.innerHTML += `<div class="status-node" onclick="viewStatus('${s.val().img}')"><img src="${s.val().img}"></div>`;
            }
        });
    });
}

window.uploadStatus = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        set(ref(db, "status/" + user), { img: reader.result, time: Date.now() });
        document.getElementById("myStatusImg").src = reader.result;
        alert("Status Updated!");
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.viewStatus = (url) => {
    const win = window.open("", "_blank");
    win.document.write(`<body style="margin:0;background:black;display:flex;align-items:center;justify-content:center;"><img src="${url}" style="max-width:100%; max-height:100vh;"></body>`);
};

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
    const path = isP ? "private_v4/" + room : "global_v4/main";
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
        const v = document.getElementById('scrollArea');
        v.scrollTop = v.scrollHeight;
    });
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim() && !selectedImg) return;
    const path = isP ? "private_v4/" + room : "global_v4/main";
    push(ref(db, path), { from: user, text: i.value, img: selectedImg, ts: Date.now() });
    i.value = "";
    cancelImg();
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
