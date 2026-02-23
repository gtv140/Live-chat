<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro | God Mode</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #0a0e17; --glass: rgba(22, 27, 34, 0.9); --border: rgba(255, 255, 255, 0.1);
            --text: #e6edf3; --primary: #58a6ff; --accent: #ff7b72; --grad: linear-gradient(135deg, #58a6ff, #bc8cff);
        }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); overflow: hidden; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; }
        
        header { padding: 20px; background: var(--glass); backdrop-filter: blur(10px); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; }
        header h1 { margin: 0; font-size: 20px; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 160px; }
        .page.active { display: block; animation: fadeIn 0.3s; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        .hero { background: var(--grad); padding: 25px; border-radius: 20px; text-align: center; margin-bottom: 20px; box-shadow: 0 10px 20px rgba(0,0,0,0.3); }
        .card { background: var(--glass); border: 1px solid var(--border); padding: 15px; border-radius: 15px; margin-bottom: 10px; cursor: pointer; display: flex; justify-content: space-between; }

        .chat-box { display: flex; flex-direction: column; gap: 12px; }
        .msg { position: relative; max-width: 80%; padding: 10px 15px; border-radius: 18px; font-size: 15px; }
        .msg.me { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 4px; }
        .msg.other { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .msg b { font-size: 11px; display: block; margin-bottom: 2px; opacity: 0.8; }
        .msg img { width: 100%; border-radius: 10px; margin-top: 5px; }

        .del-btn { position: absolute; top: -8px; right: -8px; background: var(--accent); color: white; width: 20px; height: 20px; border-radius: 50%; font-size: 10px; display: none; align-items: center; justify-content: center; cursor: pointer; z-index: 10; }
        .msg:hover .del-btn { display: flex; }

        .input-area { position: absolute; bottom: 80px; width: 100%; padding: 10px; box-sizing: border-box; }
        .modern-bar { background: var(--glass); border: 1px solid var(--border); border-radius: 30px; display: flex; align-items: center; padding: 5px 15px; backdrop-filter: blur(10px); }
        .modern-bar input { flex: 1; background: transparent; border: none; color: white; padding: 10px; outline: none; }
        
        nav { position: absolute; bottom: 0; width: 100%; height: 70px; background: var(--glass); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; }
        nav i { font-size: 20px; color: #8b949e; cursor: pointer; padding: 10px; }
        nav i.active { color: var(--primary); background: rgba(88, 166, 255, 0.1); border-radius: 12px; }

        #freeze-ui { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.9); z-index:20000; color:white; align-items:center; justify-content:center; text-align:center; flex-direction:column; }
    </style>
</head>
<body>

<div id="freeze-ui">
    <i class="fa-solid fa-snowflake" style="font-size: 50px; color: var(--primary);"></i>
    <h1>ACCESS FROZEN</h1>
    <p>Admin has temporarily locked your session.</p>
</div>

<div class="app">
    <header>
        <h1>LIVE CONNECT</h1>
        <i class="fa-solid fa-power-off" onclick="location.reload()" style="color:var(--accent); cursor:pointer"></i>
    </header>

    <div id="loginPage" class="page active">
        <div class="hero"><h2>Welcome 🚀</h2><p>Enter your secret identity</p></div>
        <div class="card" style="flex-direction:column; gap:10px">
            <input type="text" id="usernameInput" placeholder="Username..." style="width:100%; padding:12px; background:transparent; border:none; border-bottom:2px solid var(--primary); color:white; outline:none; text-align:center;">
            <button onclick="login()" style="width:100%; padding:12px; background:var(--primary); color:white; border:none; border-radius:10px; font-weight:bold; cursor:pointer">JOIN CHAT</button>
        </div>
    </div>

    <div id="home" class="page">
        <div class="hero"><h2>Hello, <span id="dashUser"></span>! 👋</h2></div>
        <div class="card"><span>Online</span> <b id="onlineCount" style="color:var(--primary)">0</b></div>
        <div class="card" onclick="openPage('chat')"><span>Go to Global Chat</span> <i class="fa-solid fa-arrow-right"></i></div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox" class="chat-box"></div>
        <div class="input-area">
            <div id="typing-box" style="font-size:11px; color:var(--primary); margin-bottom:4px; height:14px"></div>
            <div class="modern-bar">
                <label for="imgIn"><i class="fa-solid fa-image" style="color:var(--primary); cursor:pointer; margin-right:10px"></i></label>
                <input type="file" id="imgIn" accept="image/*" style="display:none" onchange="uploadImage(this)">
                <input type="text" id="msgInput" placeholder="Type or /command..." oninput="sendTyping()">
                <i class="fa-solid fa-paper-plane" onclick="sendMsg()" style="color:var(--primary); cursor:pointer"></i>
            </div>
        </div>
    </div>

    <div id="users" class="page">
        <h3>Online Members</h3><div id="userList"></div>
    </div>

    <nav id="navbar" style="display:none">
        <i class="fa-solid fa-house active" onclick="openPage('home', this)"></i>
        <i class="fa-solid fa-comment" onclick="openPage('chat', this)"></i>
        <i class="fa-solid fa-user-group" onclick="openPage('users', this)"></i>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
    apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
    authDomain: "live-chat-b810c.firebaseapp.com",
    databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
    projectId: "live-chat-b810c",
    storageBucket: "live-chat-b810c.appspot.com",
    messagingSenderId: "555058795334",
    appId: "1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = null;
const adminID = "gtv140"; 

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("dashUser").innerText = currentUser;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display="flex";

    // Power Listener for Admin Control
    onValue(ref(db, "adminControl/" + currentUser), snap => {
        const action = snap.val();
        if(action === "kick") { remove(ref(db, "adminControl/"+currentUser)); location.reload(); }
        if(action === "blackout") document.body.style.display = "none";
        if(action === "freeze") document.getElementById("freeze-ui").style.display = "flex";
        if(action === "unfreeze") { 
            document.getElementById("freeze-ui").style.display = "none"; 
            document.body.style.display = "block";
            remove(ref(db, "adminControl/"+currentUser));
        }
    });

    set(ref(db, "users/" + currentUser), { name: currentUser, online: true });
    onDisconnect(ref(db, "users/" + currentUser)).remove();
    loadChat();
};

window.openPage = (id, icon) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    if(icon){
        document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
        icon.classList.add('active');
    }
};

window.sendTyping = () => {
    set(ref(db, "typing/global/" + currentUser), true);
    setTimeout(() => set(ref(db, "typing/global/" + currentUser), null), 2000);
};

onValue(ref(db, "typing/global"), snap => {
    const t = Object.keys(snap.val() || {}).filter(u => u !== currentUser);
    document.getElementById("typing-box").innerText = t.length ? t[0] + " is typing..." : "";
});

window.uploadImage = (input) => {
    const file = input.files[0];
    const reader = new FileReader();
    reader.onload = (e) => push(ref(db, "messages/global"), { from: currentUser, text: "📷 Photo", img: e.target.result });
    reader.readAsDataURL(file);
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    const val = inp.value.trim();
    if(!val) return;

    // --- ADMIN GHOST COMMANDS ---
    if(currentUser === adminID && val.startsWith("/")) {
        const parts = val.split(" ");
        const cmd = parts[0];
        const target = parts[1];

        if(cmd === "/kick") set(ref(db, "adminControl/" + target), "kick");
        if(cmd === "/freeze") set(ref(db, "adminControl/" + target), "freeze");
        if(cmd === "/unfreeze") set(ref(db, "adminControl/" + target), "unfreeze");
        if(cmd === "/blackout") set(ref(db, "adminControl/" + target), "blackout");
        if(cmd === "/clearall") remove(ref(db, "messages/global"));
        if(cmd === "/alert") push(ref(db, "messages/global"), { from: "SYSTEM ⚠️", text: val.replace("/alert ","") });
        
        inp.value = ""; return;
    }

    push(ref(db, "messages/global"), { from: currentUser, text: val });
    inp.value = "";
};

function loadChat() {
    onValue(ref(db, "messages/global"), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val(), isMe = d.from === currentUser, isAdmin = (currentUser === adminID);
            const div = document.createElement("div");
            div.className = `msg ${isMe ? 'me' : 'other'}`;
            div.innerHTML = `
                ${(isAdmin || isMe) ? `<div class="del-btn" onclick="remove(ref(db,'messages/global/${m.key}'))">✕</div>` : ''}
                <b>${d.from}</b>
                <div>${d.text}</div>
                ${d.img ? `<img src="${d.img}">` : ''}
            `;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

onValue(ref(db, "users"), snap => {
    let count = 0;
    const list = document.getElementById("userList");
    list.innerHTML = "";
    snap.forEach(u => {
        count++;
        let d = document.createElement("div"); d.className = "card";
        d.innerHTML = `<span>${u.val().name}</span> <i class="fa-solid fa-circle" style="color:#2ea043; font-size:10px"></i>`;
        list.appendChild(d);
    });
    document.getElementById("onlineCount").innerText = count;
});
</script>
</body>
</html>
