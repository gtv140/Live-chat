<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #0f172a;
            --card: rgba(30, 41, 59, 0.7);
            --primary: #8b5cf6;
            --accent: #ec4899;
            --text: #f8fafc;
            --muted: #94a3b8;
            --glass: rgba(255, 255, 255, 0.05);
            --success: #10b981;
        }

        body {
            margin: 0; font-family: 'Segoe UI', system-ui, sans-serif;
            background: radial-gradient(circle at top right, #1e1b4b, #0f172a);
            color: var(--text); min-height: 100vh; transition: 0.3s;
        }

        body.light {
            --bg: #f1f5f9; --card: rgba(255, 255, 255, 0.9);
            --text: #1e293b; --muted: #64748b;
            background: #e2e8f0;
        }

        .app { max-width: 500px; margin: auto; display: flex; flex-direction: column; min-height: 100vh; background: var(--glass); backdrop-filter: blur(10px); position: relative; }

        header {
            padding: 15px 20px; display: flex; justify-content: space-between; align-items: center;
            background: var(--card); border-bottom: 1px solid var(--glass); position: sticky; top: 0; z-index: 100;
        }

        header h1 { font-size: 20px; font-weight: 800; background: linear-gradient(to right, var(--primary), var(--accent)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; animation: fadeIn 0.3s ease; }
        .page.active { display: flex; flex-direction: column; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* CHAT BUBBLES */
        .chat-box { flex: 1; display: flex; flex-direction: column; gap: 12px; padding: 10px; overflow-y: auto; height: 60vh; }
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; box-shadow: 0 4px 10px rgba(0,0,0,0.2); line-height: 1.4; }
        .msg.sent { align-self: flex-end; background: linear-gradient(135deg, var(--primary), #6366f1); color: white; border-bottom-right-radius: 2px; }
        .msg.received { align-self: flex-start; background: var(--card); border-bottom-left-radius: 2px; border: 1px solid var(--glass); }
        .msg b { display: block; font-size: 10px; margin-bottom: 4px; text-transform: uppercase; opacity: 0.8; }
        .msg .actions { position: absolute; top: -10px; right: 0; display: none; cursor: pointer; background: var(--accent); border-radius: 50%; width: 20px; height: 20px; text-align: center; font-size: 12px; }
        .msg:hover .actions { display: block; }

        /* LISTS (Users/Groups) */
        .list-item { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 12px; cursor: pointer; border: 1px solid var(--glass); transition: 0.2s; }
        .list-item:hover { transform: scale(1.02); background: var(--glass); }
        .dot { width: 10px; height: 10px; background: var(--success); border-radius: 50%; box-shadow: 0 0 10px var(--success); }

        /* INPUTS */
        .input-row { padding: 15px; background: var(--card); display: flex; gap: 10px; border-top: 1px solid var(--glass); }
        .input-row input { flex: 1; padding: 12px 20px; border-radius: 25px; border: 1px solid var(--glass); background: rgba(0,0,0,0.2); color: inherit; outline: none; }
        .input-row button { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--primary); color: white; cursor: pointer; }

        /* NAV */
        nav { display: flex; justify-content: space-around; background: var(--card); padding: 12px; border-top: 1px solid var(--glass); }
        nav button { background: none; border: none; font-size: 18px; color: var(--muted); cursor: pointer; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: translateY(-3px); }

        #typing-status { font-size: 11px; color: var(--primary); height: 15px; padding-left: 10px; font-style: italic; }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div style="display:flex; gap:15px;">
            <button onclick="toggleTheme()" style="background:none; border:none; color:inherit; cursor:pointer;"><i class="fa-solid fa-circle-half-stroke"></i></button>
            <button onclick="logout()" style="background:none; border:none; color:inherit; cursor:pointer;"><i class="fa-solid fa-right-from-bracket"></i></button>
        </div>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; padding:50px 0;">
            <div style="font-size: 50px; margin-bottom: 20px;">🚀</div>
            <h2>Welcome Back!</h2>
            <input type="text" id="usernameInput" placeholder="Enter username..." style="width:90%; padding:15px; border-radius:15px; border:1px solid var(--glass); background:var(--card); color:white; margin-bottom:15px;">
            <button onclick="login()" style="width:90%; padding:15px; border-radius:15px; border:none; background:var(--primary); color:white; font-weight:bold;">Login Now</button>
        </div>
    </div>

    <div id="home" class="page">
        <div style="background: linear-gradient(135deg, var(--primary), var(--accent)); padding: 30px; border-radius: 25px; margin-bottom: 20px;">
            <h2 style="margin:0;">Hello, <span id="dashUser">User</span>!</h2>
            <p style="opacity:0.8;">Everything looks good today.</p>
        </div>
        <div style="display:grid; grid-template-columns: 1fr 1fr; gap:15px;">
            <div class="list-item" style="flex-direction:column; text-align:center;">
                <h4 style="margin:0;">Online</h4>
                <p id="onlineCount" style="font-size:24px; font-weight:bold; margin:5px 0;">0</p>
            </div>
            <div class="list-item" style="flex-direction:column; text-align:center;">
                <h4 style="margin:0;">Groups</h4>
                <p id="groupCount" style="font-size:24px; font-weight:bold; margin:5px 0;">0</p>
            </div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatHeader" style="font-weight:bold; margin-bottom:10px; color:var(--primary);">Select a friend to chat</div>
        <div class="chat-box" id="chatBox"></div>
        <div id="typing-status"></div>
        <div class="input-row">
            <input id="msgInput" placeholder="Type message..." oninput="sendTyping()"/>
            <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="users" class="page">
        <h3>Active Friends</h3>
        <div id="userList"></div>
        <h3 style="margin-top:20px;">Community Groups</h3>
        <div id="groupList"></div>
        <div class="input-row" style="margin-top:10px; border-radius:15px;">
            <input id="groupInput" placeholder="New Group Name"/>
            <button onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
        </div>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('chat',this)"><i class="fa-solid fa-comment-dots"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect, serverTimestamp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

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

let currentUser = localStorage.getItem("chat_username");
let curChat = "";
let isGroup = false;

// Auto Login
if(currentUser) {
    setupUser();
}

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return alert("Enter Username");
    currentUser = val;
    localStorage.setItem("chat_username", val);
    setupUser();
};

function setupUser() {
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display = "flex";
    document.getElementById("dashUser").innerText = currentUser;
    
    const uRef = ref(db, "users/" + currentUser);
    set(uRef, { name: currentUser, online: true });
    onDisconnect(uRef).update({ online: false });
    loadUpdates();
}

window.logout = () => {
    localStorage.removeItem("chat_username");
    location.reload();
};

window.toggleTheme = () => document.body.classList.toggle('light');

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    if(btn) btn.classList.add('active');
};

function loadUpdates() {
    // Online Users
    onValue(ref(db, "users"), snap => {
        let count = 0;
        const list = document.getElementById("userList");
        list.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online) {
                count++;
                if(u.key !== currentUser) {
                    const item = document.createElement("div");
                    item.className = "list-item";
                    item.innerHTML = `<div class="dot"></div> ${u.key}`;
                    item.onclick = () => startChat(u.key, false);
                    list.appendChild(item);
                }
            }
        });
        document.getElementById("onlineCount").innerText = count;
    });

    // Groups
    onValue(ref(db, "groups"), snap => {
        const list = document.getElementById("groupList");
        list.innerHTML = "";
        document.getElementById("groupCount").innerText = snap.size;
        snap.forEach(g => {
            const item = document.createElement("div");
            item.className = "list-item";
            item.innerHTML = `<i class="fa-solid fa-hashtag"></i> ${g.key}`;
            item.onclick = () => startChat(g.key, true);
            list.appendChild(item);
        });
    });
}

function startChat(id, groupMode) {
    curChat = id; isGroup = groupMode;
    openPage('chat', document.querySelector('nav button:nth-child(2)'));
    document.getElementById("chatHeader").innerText = (isGroup ? "Group: " : "Chat with: ") + id;
    loadMessages();
}

function loadMessages() {
    const chatID = isGroup ? curChat : [currentUser, curChat].sort().join("_");
    const path = (isGroup ? "groupChats/" : "chats/") + chatID;
    
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const isMe = m.val().from === currentUser;
            const div = document.createElement("div");
            div.className = `msg ${isMe ? 'sent' : 'received'}`;
            div.innerHTML = `<b>${m.val().from}</b>${m.val().text}
                            <div class="actions" onclick="deleteMsg('${path}','${m.key}')">×</div>`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });

    // Typing Listen
    onValue(ref(db, "typing/" + chatID), snap => {
        const status = document.getElementById("typing-status");
        let typers = [];
        snap.forEach(t => { if(t.key !== currentUser) typers.push(t.key); });
        status.innerText = typers.length > 0 ? typers.join(", ") + " is typing..." : "";
    });
}

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value || !curChat) return;
    const chatID = isGroup ? curChat : [currentUser, curChat].sort().join("_");
    const path = (isGroup ? "groupChats/" : "chats/") + chatID;
    push(ref(db, path), { from: currentUser, text: inp.value, time: serverTimestamp() });
    inp.value = "";
    set(ref(db, "typing/" + chatID + "/" + currentUser), null);
};

window.sendTyping = () => {
    if(!curChat) return;
    const chatID = isGroup ? curChat : [currentUser, curChat].sort().join("_");
    set(ref(db, "typing/" + chatID + "/" + currentUser), true);
    setTimeout(() => set(ref(db, "typing/" + chatID + "/" + currentUser), null), 3000);
};

window.createGroup = () => {
    const val = document.getElementById("groupInput").value.trim();
    if(val) set(ref(db, "groups/" + val), { createdBy: currentUser });
    document.getElementById("groupInput").value = "";
};

window.deleteMsg = (path, key) => {
    if(confirm("Delete this message?")) remove(ref(db, path + "/" + key));
};
</script>
</body>
</html>
