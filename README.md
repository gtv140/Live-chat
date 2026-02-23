<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #0d1117;
            --card: #161b22;
            --border: #30363d;
            --text: #c9d1d9;
            --primary: #58a6ff;
            --accent: #f85149;
            --msg-me: #388bfd;
            --msg-other: #21262d;
            --hero-grad: linear-gradient(135deg, #238636, #2ea043);
        }
        body.light {
            --bg: #f6f8fa;
            --card: #ffffff;
            --border: #d0d7de;
            --text: #24292f;
            --msg-me: #0969da;
            --msg-other: #ebf0f4;
            --hero-grad: linear-gradient(135deg, #54a3ff, #005cc5);
        }
        body { margin: 0; font-family: -apple-system, system-ui, sans-serif; background: var(--bg); color: var(--text); overflow: hidden; transition: 0.3s; }
        .app { max-width: 500px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; border-left: 1px solid var(--border); border-right: 1px solid var(--border); }

        /* Header */
        header { padding: 20px; background: var(--card); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; }
        header h1 { margin: 0; font-size: 24px; font-weight: 800; background: linear-gradient(90deg, #79c0ff, #ff7b72); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .nav-icons { display: flex; gap: 15px; font-size: 20px; }
        i { cursor: pointer; }

        /* Page Content */
        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 120px; animation: fadeIn 0.3s; }
        .page.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Dashboard UI */
        .hero { background: var(--hero-grad); color: white; padding: 30px 20px; border-radius: 15px; text-align: center; margin-bottom: 20px; box-shadow: 0 4px 15px rgba(0,0,0,0.2); }
        .stat-card { background: var(--card); border: 1px solid var(--border); padding: 18px; border-radius: 12px; margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; }
        .stat-card b { font-size: 16px; }
        .stat-value { color: var(--primary); font-weight: bold; font-size: 18px; }

        /* Chat UI */
        .chat-box { display: flex; flex-direction: column; gap: 15px; }
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 20px; position: relative; font-size: 15px; }
        .msg.me { align-self: flex-end; background: var(--msg-me); color: white; border-bottom-right-radius: 4px; }
        .msg.other { align-self: flex-start; background: var(--msg-other); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .msg img { width: 100%; border-radius: 10px; margin-top: 8px; }
        .del-msg { position: absolute; top: -8px; right: -8px; background: var(--accent); color: white; border-radius: 50%; width: 20px; height: 20px; font-size: 10px; display: flex; align-items: center; justify-content: center; visibility: hidden; }
        .msg:hover .del-msg { visibility: visible; }

        /* Inputs */
        .bottom-input { position: absolute; bottom: 80px; left: 0; width: 100%; padding: 10px; box-sizing: border-box; }
        .input-bar { background: var(--card); border: 1px solid var(--border); border-radius: 30px; display: flex; align-items: center; padding: 5px 15px; }
        .input-bar input { flex: 1; background: transparent; border: none; color: var(--text); padding: 10px; outline: none; }
        .send-btn { background: var(--primary); color: white; border: none; width: 40px; height: 40px; border-radius: 50%; cursor: pointer; display: flex; align-items: center; justify-content: center; }

        /* Navbar */
        nav { position: absolute; bottom: 0; left: 0; width: 100%; height: 75px; background: var(--card); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; }
        nav i { font-size: 22px; color: #8b949e; transition: 0.3s; }
        nav i.active { color: var(--primary); transform: translateY(-3px); }

        /* Lists */
        .list-item { background: var(--card); padding: 15px; border-radius: 10px; border: 1px solid var(--border); margin-bottom: 8px; cursor: pointer; display: flex; align-items: center; gap: 10px; }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div class="nav-icons">
            <i class="fa-solid fa-circle-half-stroke" onclick="toggleTheme()"></i>
            <i class="fa-solid fa-power-off" style="color:var(--accent)" onclick="logout()"></i>
        </div>
    </header>

    <div id="loginPage" class="page active">
        <div class="hero"><h2>Welcome 🚀</h2><p>Please enter your name to start chatting</p></div>
        <div class="input-bar"><input type="text" id="usernameInput" placeholder="Username..."><i class="fa-solid fa-arrow-right" onclick="login()" style="color:var(--primary)"></i></div>
    </div>

    <div id="home" class="page">
        <div class="hero"><h2>Hello, <span id="dashUser"></span>! 👋</h2></div>
        <div class="stat-card"><b>Online Members</b> <span class="stat-value" id="onlineCount">0</span></div>
        <div class="stat-card"><b>Active Groups</b> <span class="stat-value" id="groupCount">0</span></div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox" class="chat-box"></div>
        <div class="bottom-input">
            <div class="input-bar">
                <label for="imgIn"><i class="fa-solid fa-camera" style="color:var(--primary)"></i></label>
                <input type="file" id="imgIn" accept="image/*" style="display:none" onchange="uploadImg(this)">
                <input type="text" id="msgInput" placeholder="Message...">
                <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <div id="users" class="page">
        <h3>Members</h3><div id="userList"></div>
        <h3 style="margin-top:20px">Groups</h3><div id="groupList"></div>
        <div class="input-bar" style="margin-top:10px">
            <input type="text" id="groupInput" placeholder="Group Name">
            <i class="fa-solid fa-plus" onclick="createGroup()" style="color:var(--primary)"></i>
        </div>
    </div>

    <div id="about" class="page">
        <h3>About Live Connect 🚀</h3>
        <p>Developed by <b>WebHub Technologies</b>.</p>
        <div class="list-item">Real-time Messaging</div>
        <div class="list-item">Photo Sharing Support</div>
        <div class="list-item">Group Communities</div>
    </div>

    <div id="contact" class="page">
        <h3>Support</h3>
        <div class="list-item" onclick="location.href='mailto:webhub262@gmail.com'"><i class="fa-solid fa-envelope"></i> webhub262@gmail.com</div>
        <div class="list-item"><i class="fa-brands fa-facebook"></i> Muhammad Nazim</div>
        <div class="list-item"><i class="fa-brands fa-instagram"></i> Instagram Profile</div>
    </div>

    <nav id="navbar" style="display:none">
        <i class="fa-solid fa-house active" onclick="openPage('home', this)"></i>
        <i class="fa-solid fa-comment" onclick="openPage('chat', this)"></i>
        <i class="fa-solid fa-user-group" onclick="openPage('users', this)"></i>
        <i class="fa-solid fa-circle-info" onclick="openPage('about', this)"></i>
        <i class="fa-solid fa-address-book" onclick="openPage('contact', this)"></i>
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
let curChat = "";
let isGroup = false;

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("dashUser").textContent = currentUser;
    set(ref(db, "users/"+currentUser), {name: currentUser, online: true});
    onDisconnect(ref(db, "users/"+currentUser)).update({online: false});
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display = "flex";
};

window.logout = () => location.reload();
window.toggleTheme = () => document.body.classList.toggle('light');

window.openPage = (id, icon) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
    icon.classList.add('active');
};

onValue(ref(db, "users"), snap => {
    let count = 0; document.getElementById("userList").innerHTML = "";
    snap.forEach(u => {
        if(u.val().online) {
            count++;
            if(u.key !== currentUser) {
                let d = document.createElement("div"); d.className = "list-item";
                d.innerHTML = `<span><i class="fa-solid fa-circle" style="color:#3fb950;font-size:10px"></i> ${u.val().name}</span>`;
                d.onclick = () => { curChat = u.key; isGroup = false; loadChat(); openPage('chat', document.querySelectorAll('nav i')[1]); };
                document.getElementById("userList").appendChild(d);
            }
        }
    });
    document.getElementById("onlineCount").textContent = count;
});

onValue(ref(db, "groups"), snap => {
    document.getElementById("groupList").innerHTML = "";
    document.getElementById("groupCount").textContent = snap.size || 0;
    snap.forEach(g => {
        let d = document.createElement("div"); d.className = "list-item";
        d.innerHTML = `<span># ${g.key}</span>`;
        d.onclick = () => { curChat = g.key; isGroup = true; loadChat(); openPage('chat', document.querySelectorAll('nav i')[1]); };
        document.getElementById("groupList").appendChild(d);
    });
});

window.uploadImg = (input) => {
    const file = input.files[0];
    if(!file || !curChat) return;
    const reader = new FileReader();
    reader.onload = (e) => {
        const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
        push(ref(db, path), {from: currentUser, text: "📷 Photo", image: e.target.result});
    };
    reader.readAsDataURL(file);
};

function loadChat() {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const data = m.val(); const isMe = data.from === currentUser;
            const div = document.createElement("div"); div.className = `msg ${isMe ? 'me' : 'other'}`;
            div.innerHTML = `<span class="del-msg" onclick="deleteMsg('${path}','${m.key}')">X</span>
                             <b>${data.from}</b>${data.text}
                             ${data.image ? `<img src="${data.image}">` : ''}
                             <div style="font-size:10px; margin-top:5px; opacity:0.7">
                                ${data.comments ? Object.values(data.comments).map(c=>`<div>${c.from}: ${c.text}</div>`).join('') : ''}
                             </div>
                             <div style="display:flex;gap:5px;margin-top:5px">
                                <input id="c-${m.key}" placeholder="Reply" style="font-size:10px; flex:1; background:rgba(0,0,0,0.1); border:none; color:white;">
                                <button onclick="addComment('${path}','${m.key}')" style="font-size:10px; border:none; background:var(--accent); color:white; border-radius:4px;">💬</button>
                             </div>`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    push(ref(db, path), {from: currentUser, text: inp.value});
    inp.value = "";
};

window.addComment = (path, key) => {
    const inp = document.getElementById("c-"+key);
    if(inp.value) push(ref(db, path+"/"+key+"/comments"), {from: currentUser, text: inp.value});
    inp.value = "";
};

window.deleteMsg = (p, k) => remove(ref(db, p+"/"+k));
window.createGroup = () => {
    const g = document.getElementById("groupInput").value.trim();
    if(g) set(ref(db, "groups/"+g), {createdBy: currentUser});
    document.getElementById("groupInput").value = "";
};
</script>
</body>
</html>
