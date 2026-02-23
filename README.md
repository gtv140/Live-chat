<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #0a0f1e;
            --card: rgba(23, 32, 53, 0.8);
            --border: rgba(255, 255, 255, 0.1);
            --text: #f1f5f9;
            --primary: #6366f1;
            --accent: #f43f5e;
            --success: #10b981;
        }
        body.light {
            --bg: #f8fafc;
            --card: rgba(255, 255, 255, 0.9);
            --border: rgba(0, 0, 0, 0.1);
            --text: #1e293b;
        }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); transition: .3s; }
        .app { max-width: 480px; margin: auto; min-height: 100vh; display: flex; flex-direction: column; position: relative; }
        
        header { padding: 16px; display: flex; justify-content: space-between; align-items: center; background: var(--card); backdrop-filter: blur(10px); border-bottom: 1px solid var(--border); position: sticky; top: 0; z-index: 100; }
        header h1 { margin: 0; font-size: 20px; background: linear-gradient(to right, var(--primary), var(--accent)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }
        
        .page { display: none; padding: 16px; flex: 1; animation: slideUp 0.3s ease; padding-bottom: 80px; }
        .page.active { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .hero { background: linear-gradient(135deg, var(--primary), var(--accent)); color: #fff; padding: 25px; border-radius: 24px; text-align: center; box-shadow: 0 10px 20px rgba(0,0,0,0.2); margin-bottom: 20px; }
        
        .chat-box { height: calc(100vh - 220px); overflow-y: auto; padding: 10px; display: flex; flex-direction: column; gap: 15px; }
        .msg { background: var(--card); padding: 12px; border-radius: 18px; border: 1px solid var(--border); position: relative; max-width: 80%; align-self: flex-start; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        .msg.me { align-self: flex-end; background: var(--primary); color: white; border: none; }
        .msg img { width: 100%; border-radius: 12px; margin-top: 8px; cursor: pointer; }
        .actions { position: absolute; top: 8px; right: 10px; font-size: 12px; cursor: pointer; opacity: 0.5; }
        .actions:hover { opacity: 1; color: var(--accent); }

        .comment-box { margin-top: 8px; font-size: 11px; background: rgba(0,0,0,0.2); padding: 5px; border-radius: 8px; }
        
        .input-container { position: fixed; bottom: 70px; width: 100%; max-width: 480px; padding: 10px; background: var(--bg); }
        .input-row { display: flex; gap: 8px; padding: 8px 12px; background: var(--card); border-radius: 30px; border: 1px solid var(--border); align-items: center; }
        .input-row input { flex: 1; background: transparent; border: none; color: var(--text); outline: none; }
        
        .btn-icon { background: var(--primary); color: white; border: none; width: 38px; height: 38px; border-radius: 50%; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: 0.2s; flex-shrink: 0; }
        
        .list-item { display: flex; align-items: center; gap: 12px; padding: 12px; background: var(--card); border-radius: 16px; margin-bottom: 8px; border: 1px solid var(--border); cursor: pointer; }
        
        nav { position: fixed; bottom: 0; width: 100%; max-width: 480px; display: flex; justify-content: space-around; background: var(--card); padding: 12px 0 20px; border-top: 1px solid var(--border); }
        nav button { background: none; border: none; font-size: 20px; color: #64748b; cursor: pointer; }
        nav button.active { color: var(--primary); }

        .contact-card { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 12px; border: 1px solid var(--border); text-decoration: none; color: inherit; }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div>
            <button onclick="toggleTheme()" style="background:none;border:none;color:var(--text);font-size:18px;"><i class="fa-solid fa-moon"></i></button>
            <button onclick="logout()" style="background:none;border:none;color:var(--accent);font-size:18px;margin-left:12px;"><i class="fa-solid fa-power-off"></i></button>
        </div>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; padding: 40px 20px;">
            <div class="hero"><h2>Welcome 🚀</h2><p>Experience real-time modern chat.</p></div>
            <div class="input-row">
                <input type="text" id="usernameInput" placeholder="Your name...">
                <button class="btn-icon" onclick="login()"><i class="fa-solid fa-arrow-right"></i></button>
            </div>
        </div>
    </div>

    <div id="home" class="page">
        <div class="hero"><h2>Hi, <span id="dashUser"></span>! 👋</h2><p>Connecting you to the world.</p></div>
        <div class="list-item"><h4>Online Users: <span id="onlineCount" style="color:var(--success)">0</span></h4></div>
        <div class="list-item"><h4>Total Groups: <span id="groupCount" style="color:var(--primary)">0</span></h4></div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox" class="chat-box"></div>
        <div class="input-container">
            <div class="input-row">
                <label for="fileInput" class="btn-icon" style="background:none; color:var(--primary); width:30px;"><i class="fa-solid fa-camera"></i></label>
                <input type="file" id="fileInput" accept="image/*" style="display:none;" onchange="uploadImage(this)">
                <input id="msgInput" placeholder="Type message..."/>
                <button onclick="sendMsg()" class="btn-icon"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <div id="users" class="page">
        <h3>Live Users</h3><div id="userList"></div>
        <h3>Communities</h3><div id="groupList"></div>
        <div class="input-row">
            <input id="groupInput" placeholder="Create group name..."/>
            <button class="btn-icon" onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
        </div>
    </div>

    <div id="contact" class="page">
        <h3>Support & Social</h3>
        <a href="mailto:webhub262@gmail.com" class="contact-card"><i class="fa-solid fa-envelope"></i> webhub262@gmail.com</a>
        <a href="#" class="contact-card"><i class="fab fa-facebook"></i> Facebook</a>
        <a href="#" class="contact-card"><i class="fab fa-instagram"></i> Instagram</a>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('chat',this)"><i class="fa-solid fa-comment"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
        <button onclick="openPage('contact',this)"><i class="fa-solid fa-address-book"></i></button>
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
    const uname = document.getElementById("usernameInput").value.trim();
    if(!uname) return;
    currentUser = uname;
    document.getElementById("dashUser").textContent = currentUser;
    set(ref(db, "users/" + uname), { name: uname, online: true });
    onDisconnect(ref(db, "users/" + uname)).update({ online: false });
    document.getElementById("loginPage").style.display = "none";
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display = "flex";
};

window.logout = () => { location.reload(); };

window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
};

window.toggleTheme = () => document.body.classList.toggle('light');

// Sync Users & Groups
onValue(ref(db, "users"), snap => {
    let online = 0; document.getElementById("userList").innerHTML = "";
    snap.forEach(u => {
        if(u.val().online) {
            online++;
            if(u.key !== currentUser) {
                const d = document.createElement("div");
                d.className = "list-item";
                d.innerHTML = `<div style="width:8px;height:8px;background:var(--success);border-radius:50%"></div> ${u.val().name}`;
                d.onclick = () => { startChat(u.key, false); };
                document.getElementById("userList").appendChild(d);
            }
        }
    });
    document.getElementById("onlineCount").textContent = online;
});

onValue(ref(db, "groups"), snap => {
    document.getElementById("groupList").innerHTML = "";
    document.getElementById("groupCount").textContent = snap.size || 0;
    snap.forEach(g => {
        const d = document.createElement("div");
        d.className = "list-item";
        d.innerHTML = `<i class="fa-solid fa-hashtag"></i> ${g.key}`;
        d.onclick = () => { startChat(g.key, true); };
        document.getElementById("groupList").appendChild(d);
    });
});

function startChat(id, group) {
    curChat = id; isGroup = group;
    loadChat();
    openPage('chat', document.querySelectorAll('nav button')[1]);
}

window.uploadImage = (input) => {
    const file = input.files[0];
    if (!file) return;
    if (file.size > 500000) { alert("Photo size 500kb se kam honi chahiye!"); return; }

    const reader = new FileReader();
    reader.onload = (e) => {
        const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
        push(ref(db, path), { from: currentUser, text: "📷 Photo", image: e.target.result });
    };
    reader.readAsDataURL(file);
};

function loadChat() {
    if(!curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const val = m.val();
            const isMe = val.from === currentUser;
            const div = document.createElement("div");
            div.className = `msg ${isMe ? 'me' : ''}`;
            
            let imgPart = val.image ? `<img src="${val.image}" onclick="window.open('${val.image}')">` : '';
            let comms = val.comments ? Object.values(val.comments).map(c=>`<div class="comment-box"><b>${c.from}:</b> ${c.text}</div>`).join('') : '';

            div.innerHTML = `
                <div class="actions" onclick="deleteMsg('${path}','${m.key}')">🗑️</div>
                <b>${val.from}</b><br>${val.text}
                ${imgPart}
                ${comms}
                <div style="display:flex; margin-top:5px; gap:5px;">
                    <input id="c-${m.key}" placeholder="Comment..." style="font-size:10px; flex:1; background:rgba(255,255,255,0.1); border:none; color:white; border-radius:5px;">
                    <button onclick="addComment('${path}','${m.key}')" style="font-size:10px; border:none; background:var(--accent); color:white; border-radius:5px;">💬</button>
                </div>`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.sendMsg = () => {
    const inp = document.getElementById('msgInput');
    if(!inp.value || !curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    push(ref(db, path), {from: currentUser, text: inp.value});
    inp.value = '';
};

window.deleteMsg = (path, key) => { if(confirm("Delete this message?")) remove(ref(db, path+"/"+key)); };
window.addComment = (path, key) => {
    const inp = document.getElementById("c-"+key);
    if(inp.value) push(ref(db, path+"/"+key+"/comments"), {from: currentUser, text: inp.value});
    inp.value = "";
};
window.createGroup = () => {
    const g = document.getElementById("groupInput").value.trim();
    if(g) set(ref(db, "groups/"+g), {createdBy: currentUser});
    document.getElementById("groupInput").value = "";
};
</script>
</body>
</html>
