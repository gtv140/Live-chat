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
            --header-bg: #161b22;
            --border: #30363d;
            --text: #c9d1d9;
            --primary: #58a6ff;
            --accent: #f85149;
            --msg-me: #388bfd;
            --msg-other: #21262d;
        }
        body.light {
            --bg: #f6f8fa;
            --card: #ffffff;
            --header-bg: #ffffff;
            --border: #d0d7de;
            --text: #24292f;
            --msg-me: #0969da;
            --msg-other: #ebf0f4;
        }
        body { margin: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; overflow: hidden; }
        
        .app { max-width: 500px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; border-right: 1px solid var(--border); border-left: 1px solid var(--border); }
        
        /* Fixed Header from Screenshot */
        header { padding: 20px; background: var(--header-bg); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; }
        header h1 { margin: 0; font-size: 24px; font-weight: 800; background: linear-gradient(90deg, #79c0ff, #ff7b72); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        header .nav-icons { display: flex; gap: 15px; font-size: 20px; }
        header i { cursor: pointer; transition: 0.2s; }
        header i:hover { color: var(--primary); }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 100px; }
        .page.active { display: flex; flex-direction: column; }

        /* Dashboard & Lists */
        .hero { background: linear-gradient(135deg, #238636, #2ea043); color: white; padding: 25px; border-radius: 12px; margin-bottom: 20px; text-align: center; }
        .stat-item { background: var(--card); border: 1px solid var(--border); padding: 15px; border-radius: 10px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }

        /* Chat UI Improvements */
        .chat-box { flex: 1; display: flex; flex-direction: column; gap: 15px; padding: 10px; }
        .msg { position: relative; max-width: 75%; padding: 12px 16px; border-radius: 20px; font-size: 15px; line-height: 1.4; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .msg.me { align-self: flex-end; background: var(--msg-me); color: white; border-bottom-right-radius: 4px; }
        .msg.other { align-self: flex-start; background: var(--msg-other); color: var(--text); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        
        .msg b { font-size: 13px; display: block; margin-bottom: 4px; opacity: 0.9; }
        .msg img { width: 100%; border-radius: 10px; margin: 8px 0; border: 1px solid rgba(255,255,255,0.1); }
        .msg .delete-btn { position: absolute; top: -10px; right: 0; font-size: 12px; cursor: pointer; display: none; background: var(--accent); color: white; padding: 2px 6px; border-radius: 10px; }
        .msg:hover .delete-btn { display: block; }

        /* Comments Styling */
        .comment-area { margin-top: 8px; font-size: 12px; border-top: 1px solid rgba(255,255,255,0.1); padding-top: 6px; }
        .comment-item { margin-bottom: 4px; opacity: 0.8; }
        .comment-input-wrap { display: flex; gap: 5px; margin-top: 8px; background: rgba(0,0,0,0.2); border-radius: 15px; padding: 4px 10px; }
        .comment-input-wrap input { background: transparent; border: none; color: white; font-size: 11px; flex: 1; outline: none; }

        /* Modern Bottom Input */
        .input-container { position: absolute; bottom: 70px; left: 0; width: 100%; padding: 10px; box-sizing: border-box; background: var(--bg); }
        .input-bar { background: var(--card); border: 1px solid var(--border); border-radius: 30px; display: flex; align-items: center; padding: 5px 15px; gap: 10px; }
        .input-bar input { flex: 1; background: transparent; border: none; color: var(--text); padding: 10px 0; outline: none; font-size: 15px; }
        .icon-btn { color: var(--primary); font-size: 20px; cursor: pointer; transition: 0.2s; }
        .send-btn { background: var(--primary); color: white; width: 40px; height: 40px; border-radius: 50%; border: none; display: flex; align-items: center; justify-content: center; cursor: pointer; }

        /* Navbar from Screenshot */
        nav { position: absolute; bottom: 0; left: 0; width: 100%; height: 70px; background: var(--card); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; }
        nav i { font-size: 22px; color: #8b949e; cursor: pointer; transition: 0.3s; }
        nav i.active { color: var(--primary); }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div class="nav-icons">
            <i class="fa-solid fa-moon" onclick="toggleTheme()"></i>
            <i class="fa-solid fa-power-off" style="color:var(--accent)" onclick="logout()"></i>
        </div>
    </header>

    <div id="loginPage" class="page active" style="justify-content: center;">
        <div class="hero"><h2>Welcome 🚀</h2><p>Sign in to connect</p></div>
        <div class="input-bar">
            <input type="text" id="usernameInput" placeholder="Enter Username">
            <i class="fa-solid fa-circle-right icon-btn" onclick="login()"></i>
        </div>
    </div>

    <div id="home" class="page">
        <div class="hero"><h2>Hello, <span id="dashUser"></span>! 👋</h2></div>
        <div class="stat-item"><b>Online Members</b> <span id="onlineCount" style="color:var(--primary)">0</span></div>
        <div class="stat-item"><b>Active Groups</b> <span id="groupCount" style="color:var(--primary)">0</span></div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox" class="chat-box"></div>
        <div class="input-container">
            <div class="input-bar">
                <label for="imgUpload"><i class="fa-solid fa-camera icon-btn"></i></label>
                <input type="file" id="imgUpload" accept="image/*" style="display:none" onchange="uploadPhoto(this)">
                <input type="text" id="msgInput" placeholder="Type message...">
                <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <div id="users" class="page">
        <h3>Members</h3><div id="userList"></div>
        <h3 style="margin-top:20px">Groups</h3><div id="groupList"></div>
        <div class="input-bar" style="margin-top:10px">
            <input type="text" id="groupInput" placeholder="Group Name">
            <i class="fa-solid fa-plus icon-btn" onclick="createGroup()"></i>
        </div>
    </div>

    <div id="contact" class="page">
        <h3>Support</h3>
        <div class="stat-item" onclick="location.href='mailto:webhub262@gmail.com'"><i class="fa-solid fa-envelope"></i> Email Us</div>
        <div class="stat-item"><i class="fa-brands fa-facebook"></i> Facebook</div>
        <div class="stat-item"><i class="fa-brands fa-instagram"></i> Instagram</div>
    </div>

    <nav id="navbar" style="display:none">
        <i class="fa-solid fa-house active" onclick="openPage('home', this)"></i>
        <i class="fa-solid fa-comment" onclick="openPage('chat', this)"></i>
        <i class="fa-solid fa-user-group" onclick="openPage('users', this)"></i>
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

// Database Logic
onValue(ref(db, "users"), snap => {
    let count = 0; document.getElementById("userList").innerHTML = "";
    snap.forEach(u => {
        if(u.val().online) {
            count++;
            if(u.key !== currentUser) {
                let d = document.createElement("div");
                d.className = "stat-item";
                d.innerHTML = `<span><i class="fa-solid fa-circle" style="color:#3fb950;font-size:10px"></i> ${u.val().name}</span> <i class="fa-solid fa-message icon-btn"></i>`;
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
        let d = document.createElement("div");
        d.className = "stat-item";
        d.innerHTML = `<span># ${g.key}</span> <i class="fa-solid fa-users icon-btn"></i>`;
        d.onclick = () => { curChat = g.key; isGroup = true; loadChat(); openPage('chat', document.querySelectorAll('nav i')[1]); };
        document.getElementById("groupList").appendChild(d);
    });
});

window.uploadPhoto = (input) => {
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
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const data = m.val();
            const isMe = data.from === currentUser;
            const div = document.createElement("div");
            div.className = `msg ${isMe ? 'me' : 'other'}`;
            
            let img = data.image ? `<img src="${data.image}">` : "";
            let comms = data.comments ? Object.values(data.comments).map(c => `<div class="comment-item"><b>${c.from}:</b> ${c.text}</div>`).join('') : "";

            div.innerHTML = `
                <span class="delete-btn" onclick="deleteMsg('${path}','${m.key}')">🗑️</span>
                <b>${data.from}</b>
                ${data.text}
                ${img}
                <div class="comment-area">${comms}</div>
                <div class="comment-input-wrap">
                    <input id="in-${m.key}" placeholder="Reply...">
                    <i class="fa-solid fa-paper-plane" style="font-size:12px;cursor:pointer" onclick="addComment('${path}','${m.key}')"></i>
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
    const inp = document.getElementById("in-"+key);
    if(inp.value) push(ref(db, path+"/"+key+"/comments"), {from: currentUser, text: inp.value});
    inp.value = "";
};

window.deleteMsg = (path, key) => remove(ref(db, path+"/"+key));
window.createGroup = () => {
    const g = document.getElementById("groupInput").value.trim();
    if(g) set(ref(db, "groups/"+g), {createdBy: currentUser});
    document.getElementById("groupInput").value = "";
};
</script>
</body>
</html>
