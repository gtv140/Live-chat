<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | God Mode Admin</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #0a0e17; --glass: rgba(22, 27, 34, 0.85); --border: rgba(255, 255, 255, 0.1);
            --text: #e6edf3; --primary: #58a6ff; --accent: #ff7b72; --grad: linear-gradient(135deg, #58a6ff, #bc8cff);
        }
        body { margin: 0; font-family: 'Inter', sans-serif; background: var(--bg); color: var(--text); overflow: hidden; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; }
        
        header { padding: 20px; background: var(--glass); backdrop-filter: blur(15px); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 100; }
        header h1 { margin: 0; font-size: 22px; font-weight: 800; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 160px; animation: slideUp 0.3s; }
        .page.active { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        .hero { background: var(--grad); padding: 30px; border-radius: 20px; color: white; text-align: center; margin-bottom: 20px; box-shadow: 0 10px 20px rgba(0,0,0,0.3); }
        .card { background: var(--glass); border: 1px solid var(--border); padding: 15px; border-radius: 15px; margin-bottom: 10px; cursor: pointer; transition: 0.3s; }
        .card:hover { border-color: var(--primary); }

        .chat-box { display: flex; flex-direction: column; gap: 15px; }
        .msg { position: relative; max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 15px; box-shadow: 0 5px 10px rgba(0,0,0,0.1); }
        .msg.me { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 4px; }
        .msg.other { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .msg img { width: 100%; border-radius: 12px; margin-top: 8px; cursor: zoom-in; }
        .msg b { font-size: 11px; display: block; margin-bottom: 4px; opacity: 0.7; }

        .del-btn { position: absolute; top: -10px; right: -5px; background: var(--accent); color: white; width: 22px; height: 22px; border-radius: 50%; font-size: 10px; display: none; align-items: center; justify-content: center; cursor: pointer; }
        .msg:hover .del-btn { display: flex; }
        .react-badge { position: absolute; bottom: -10px; right: 10px; background: #161b22; border-radius: 10px; padding: 2px 5px; font-size: 12px; border: 1px solid var(--border); }

        .input-area { position: absolute; bottom: 85px; width: 100%; padding: 15px; box-sizing: border-box; z-index: 50; }
        .modern-bar { background: var(--glass); border: 1px solid var(--border); border-radius: 35px; display: flex; align-items: center; padding: 5px 15px; backdrop-filter: blur(10px); }
        .modern-bar input { flex: 1; background: transparent; border: none; color: white; padding: 12px; outline: none; }
        .icon-btn { color: var(--primary); font-size: 20px; cursor: pointer; margin: 0 8px; }

        nav { position: absolute; bottom: 0; width: 100%; height: 75px; background: var(--glass); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; backdrop-filter: blur(15px); }
        nav i { font-size: 22px; color: #8b949e; cursor: pointer; transition: 0.3s; padding: 10px; border-radius: 12px; }
        nav i.active { color: var(--primary); background: rgba(88, 166, 255, 0.1); }

        #overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:black; z-index:10000; color:white; align-items:center; justify-content:center; text-align:center; }
    </style>
</head>
<body>

<div id="overlay"><h1>SYSTEM FROZEN ❄️<br><small>Admin ne aapki screen lock kar di hai</small></h1></div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <i class="fa-solid fa-power-off" onclick="location.reload()" style="color:var(--accent); cursor:pointer"></i>
    </header>

    <div id="loginPage" class="page active">
        <div class="hero"><h2>Gumnaam Chat 🚀</h2><p>Apna secret naam enter karein</p></div>
        <div class="card" style="display:flex; flex-direction:column; gap:15px">
            <input type="text" id="usernameInput" placeholder="Enter Name..." style="width:100%; padding:12px; background:transparent; border:none; border-bottom:2px solid var(--primary); color:white; outline:none; text-align:center; font-size:18px">
            <button onclick="login()" style="width:100%; padding:15px; background:var(--primary); color:white; border:none; border-radius:12px; font-weight:bold; cursor:pointer">START CHATTING</button>
        </div>
    </div>

    <div id="home" class="page">
        <div class="hero"><h2>Khush-Amdeed, <span id="dashUser"></span>! 👋</h2></div>
        <div class="card"><span>Online Members</span> <b id="onlineCount" style="color:var(--primary)">0</b></div>
        <div class="card"><span>Active Groups</span> <b id="groupCount" style="color:var(--primary)">0</b></div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox" class="chat-box"></div>
        <div class="input-area">
            <div id="typing-box" style="font-size:12px; color:var(--primary); margin-bottom:5px; height:15px"></div>
            <div class="modern-bar">
                <label for="imgIn"><i class="fa-solid fa-camera icon-btn"></i></label>
                <input type="file" id="imgIn" accept="image/*" style="display:none" onchange="uploadPhoto(this)">
                <input type="text" id="msgInput" placeholder="Message or /command..." oninput="handleTyping()">
                <i class="fa-solid fa-paper-plane icon-btn" onclick="sendMsg()"></i>
            </div>
        </div>
    </div>

    <div id="users" class="page">
        <h3>Contacts</h3><div id="userList"></div>
        <h3>Groups</h3><div id="groupList"></div>
        <div class="modern-bar" style="margin-top:20px">
            <input id="groupInput" placeholder="Create New Group...">
            <i class="fa-solid fa-plus icon-btn" onclick="createGroup()"></i>
        </div>
    </div>

    <div id="about" class="page">
        <h3>About App</h3>
        <div class="card">Developed by WebHub Technologies</div>
        <div class="card">Modern Engine v4.0 (God Mode Enabled)</div>
    </div>

    <nav id="navbar" style="display:none">
        <i class="fa-solid fa-house active" onclick="openPage('home', this)"></i>
        <i class="fa-solid fa-comment" onclick="openPage('chat', this)"></i>
        <i class="fa-solid fa-user-group" onclick="openPage('users', this)"></i>
        <i class="fa-solid fa-info-circle" onclick="openPage('about', this)"></i>
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

let currentUser = null, curChat = "Global", isGroup = true, typeTimer;
const adminID = "gtv140"; // CHANGE THIS IF NEEDED

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("dashUser").innerText = currentUser;
    document.getElementById("loginPage").style.display="none";
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display="flex";

    // Power Listener (For Ghost Admin)
    onValue(ref(db, "powers/" + currentUser), snap => {
        const p = snap.val();
        if(p === "kick") location.reload();
        if(p === "black") document.body.style.display = "none";
        if(p === "freeze") document.getElementById("overlay").style.display = "flex";
        if(p === "unfreeze") { document.getElementById("overlay").style.display = "none"; document.body.style.display = "block"; }
    });
    
    set(ref(db, "users/"+currentUser), {name: currentUser, online: true});
    onDisconnect(ref(db, "users/"+currentUser)).update({online: false});
    loadChat();
};

window.openPage = (id, icon) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
    if(icon) icon.classList.add('active');
};

window.handleTyping = () => {
    set(ref(db, `typing/${curChat}/${currentUser}`), true);
    clearTimeout(typeTimer);
    typeTimer = setTimeout(() => set(ref(db, `typing/${curChat}/${currentUser}`), null), 2000);
};

onValue(ref(db, `typing/${curChat}`), snap => {
    const typers = Object.keys(snap.val() || {}).filter(u => u !== currentUser);
    document.getElementById("typing-box").innerText = typers.length ? typers[0] + " typing..." : "";
});

window.uploadPhoto = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => {
        const path = (isGroup ? "groupChats/" : "chats/") + curChat;
        push(ref(db, path), {from: currentUser, text: "📷 Photo", image: e.target.result});
    };
    reader.readAsDataURL(input.files[0]);
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    const val = inp.value.trim();
    if(!val) return;

    // Admin Commands Logic
    if(currentUser === adminID && val.startsWith("/")) {
        const cmd = val.split(" ");
        if(cmd[0] === "/kick") set(ref(db, "powers/"+cmd[1]), "kick");
        if(cmd[0] === "/freeze") set(ref(db, "powers/"+cmd[1]), "freeze");
        if(cmd[0] === "/blackout") set(ref(db, "powers/"+cmd[1]), "black");
        if(cmd[0] === "/unfreeze") set(ref(db, "powers/"+cmd[1]), "unfreeze");
        if(cmd[0] === "/clearall") { remove(ref(db, "groupChats")); remove(ref(db, "chats")); }
        if(cmd[0] === "/alert") push(ref(db, "groupChats/Global"), {from: "SYSTEM ⚠️", text: val.replace("/alert ","")});
        inp.value = ""; return;
    }

    const path = (isGroup ? "groupChats/" : "chats/") + curChat;
    push(ref(db, path), {from: currentUser, text: val});
    inp.value = "";
};

function loadChat() {
    const path = (isGroup ? "groupChats/" : "chats/") + curChat;
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val(), isMe = d.from === currentUser, isAdmin = currentUser === adminID;
            const div = document.createElement("div"); div.className = `msg ${isMe ? 'me' : 'other'}`;
            div.innerHTML = `
                ${(isAdmin || isMe) ? `<div class="del-btn" onclick="remove(ref(db,'${path}/${m.key}'))">✕</div>` : ''}
                <b>${d.from}</b> ${d.text} ${d.image ? `<img src="${d.image}">` : ''}
                <div class="react-badge" onclick="react('${path}','${m.key}')">${d.react || '🤍'}</div>`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.react = (p, k) => {
    const r = prompt("Enter Emoji (❤️, 😂, 👍, 🔥):");
    if(r) set(ref(db, p+"/"+k+"/react"), r);
};

// Syncing Members & Groups
onValue(ref(db, "users"), snap => {
    let c = 0; document.getElementById("userList").innerHTML = "";
    snap.forEach(u => { if(u.val().online) { c++; if(u.key!==currentUser){
        let d = document.createElement("div"); d.className="card"; d.innerHTML=`${u.val().name} 💬`;
        d.onclick=()=>{curChat=u.key; isGroup=false; loadChat(); openPage('chat')};
        document.getElementById("userList").appendChild(d);
    }}});
    document.getElementById("onlineCount").innerText = c;
});

onValue(ref(db, "groups"), snap => {
    document.getElementById("groupList").innerHTML = "";
    document.getElementById("groupCount").innerText = snap.size || 0;
    snap.forEach(g => {
        let d = document.createElement("div"); d.className="card"; d.innerHTML=`# ${g.key}`;
        d.onclick=()=>{curChat=g.key; isGroup=true; loadChat(); openPage('chat')};
        document.getElementById("groupList").appendChild(d);
    });
});

window.createGroup = () => {
    const g = document.getElementById("groupInput").value.trim();
    if(g) { set(ref(db, "groups/"+g), {by: currentUser}); document.getElementById("groupInput").value=""; }
};
</script>
</body>
</html>
