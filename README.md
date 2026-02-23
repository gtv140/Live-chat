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
        body { margin: 0; font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg); color: var(--text); transition: .3s; }
        .app { max-width: 480px; margin: auto; min-height: 100vh; display: flex; flex-direction: column; background: radial-gradient(circle at top right, rgba(99, 102, 241, 0.15), transparent 300px); }
        
        header { padding: 16px; display: flex; justify-content: space-between; align-items: center; background: var(--card); backdrop-filter: blur(10px); border-bottom: 1px solid var(--border); sticky; top: 0; z-index: 100; }
        header h1 { margin: 0; font-size: 20px; background: linear-gradient(to right, var(--primary), var(--accent)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }
        
        .page { display: none; padding: 16px; flex: 1; animation: slideUp 0.3s ease; }
        .page.active { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .hero { background: linear-gradient(135deg, var(--primary), var(--accent)); color: #fff; padding: 25px; border-radius: 24px; text-align: center; box-shadow: 0 10px 20px rgba(0,0,0,0.2); margin-bottom: 20px; }
        .stat-card { background: var(--card); padding: 15px 20px; margin-bottom: 12px; border-radius: 18px; border: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; }
        .stat-card h4 { margin: 0; font-weight: 500; opacity: 0.8; }
        .stat-card span { font-size: 20px; font-weight: 700; color: var(--primary); }

        .chat-box { height: 55vh; overflow-y: auto; padding: 10px; display: flex; flex-direction: column; gap: 12px; }
        .msg { background: var(--card); padding: 12px; border-radius: 18px; border: 1px solid var(--border); position: relative; max-width: 85%; align-self: flex-start; }
        .msg.me { align-self: flex-end; background: var(--primary); color: white; border: none; }
        .msg img { width: 100%; border-radius: 12px; margin-top: 8px; display: block; }
        .actions { position: absolute; top: 5px; right: 8px; font-size: 12px; cursor: pointer; opacity: 0.6; }
        .comments { margin-top: 10px; font-size: 12px; border-top: 1px solid var(--border); padding-top: 8px; }

        .input-row { display: flex; gap: 8px; padding: 10px; background: var(--card); border-radius: 20px; border: 1px solid var(--border); margin-top: 10px; }
        .input-row input { flex: 1; background: transparent; border: none; color: var(--text); outline: none; padding: 5px; }
        .btn-icon { background: var(--primary); color: white; border: none; width: 40px; height: 40px; border-radius: 50%; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: 0.2s; }
        .btn-icon:hover { transform: scale(1.1); background: var(--accent); }

        .list-item { display: flex; align-items: center; gap: 12px; padding: 15px; background: var(--card); border-radius: 16px; margin-bottom: 10px; border: 1px solid var(--border); cursor: pointer; transition: 0.2s; }
        .list-item:hover { background: rgba(99, 102, 241, 0.1); border-color: var(--primary); }
        .dot { width: 10px; height: 10px; background: var(--success); border-radius: 50%; box-shadow: 0 0 8px var(--success); }

        nav { display: flex; justify-content: space-around; background: var(--card); padding: 10px 0 25px; border-top: 1px solid var(--border); }
        nav button { background: none; border: none; font-size: 20px; color: #64748b; cursor: pointer; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: translateY(-3px); }

        .contact-card { background: var(--card); padding: 15px; border-radius: 16px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; border: 1px solid var(--border); }
        .contact-card i { font-size: 22px; color: var(--primary); }
        .contact-card a { color: var(--text); text-decoration: none; font-weight: 500; }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div>
            <button onclick="toggleTheme()" style="background:none;border:none;color:var(--text);font-size:18px;"><i class="fa-solid fa-circle-half-stroke"></i></button>
            <button onclick="logout()" style="background:none;border:none;color:var(--accent);font-size:18px;margin-left:15px;"><i class="fa-solid fa-power-off"></i></button>
        </div>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; padding: 40px 20px;">
            <div class="hero"><h2>Welcome 🚀</h2><p>The future of real-time chat is here.</p></div>
            <div class="input-row">
                <input type="text" id="usernameInput" placeholder="Enter Username...">
                <button class="btn-icon" onclick="login()"><i class="fa-solid fa-chevron-right"></i></button>
            </div>
        </div>
    </div>

    <div id="home" class="page">
        <div class="hero"><h2>Hello, <span id="dashUser"></span>! 👋</h2><p>Everything looks good today.</p></div>
        <div class="stat-card"><h4>Active Now</h4><span id="onlineCount">0</span></div>
        <div class="stat-card"><h4>Groups</h4><span id="groupCount">0</span></div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox" class="chat-box"></div>
        <div class="input-row">
            <button onclick="sendPhoto()" class="btn-icon" style="background:none; color:var(--primary);"><i class="fa-solid fa-image"></i></button>
            <input id="msgInput" placeholder="Type a message..."/>
            <button onclick="sendMsg()" class="btn-icon"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="users" class="page">
        <h3>Online Users</h3><div id="userList"></div>
        <h3 style="margin-top:20px;">Groups</h3><div id="groupList"></div>
        <div class="input-row">
            <input id="groupInput" placeholder="New group name..."/>
            <button class="btn-icon" onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
        </div>
    </div>

    <div id="about" class="page">
        <h3>About Us</h3>
        <p><b>WebHub Technologies</b> presents a seamless real-time experience.</p>
        <div class="stat-card">Modern Glass UI</div>
        <div class="stat-card">Photo Sharing</div>
        <div class="stat-card">Admin Controls</div>
    </div>

    <div id="contact" class="page">
        <h3>Support</h3>
        <div class="contact-card"><i class="fa-solid fa-envelope"></i><a href="mailto:webhub262@gmail.com">webhub262@gmail.com</a></div>
        <div class="contact-card"><i class="fab fa-facebook"></i><a href="#">Facebook Profile</a></div>
        <div class="contact-card"><i class="fab fa-instagram"></i><a href="#">Instagram</a></div>
        <div class="contact-card"><i class="fa-solid fa-phone"></i><a href="tel:+923001234567">+92 300 1234567</a></div>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="openPage('chat',this)"><i class="fa-solid fa-comment"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
        <button onclick="openPage('about',this)"><i class="fa-solid fa-info-circle"></i></button>
        <button onclick="openPage('contact',this)"><i class="fa-solid fa-headset"></i></button>
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
    document.getElementById("loginPage").classList.remove("active");
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

// Features Restore & Modernize
onValue(ref(db, "users"), snap => {
    let online = 0; document.getElementById("userList").innerHTML = "";
    snap.forEach(u => {
        if(u.val().online) {
            online++;
            if(u.key !== currentUser) {
                const d = document.createElement("div");
                d.className = "list-item";
                d.innerHTML = `<div class="dot"></div>${u.val().name}`;
                d.onclick = () => { curChat=u.key; isGroup=false; loadChat(); openPage('chat', document.querySelectorAll('nav button')[1]); };
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
        d.innerHTML = `<i class="fa-solid fa-hashtag" style="color:var(--primary)"></i> ${g.key}`;
        d.onclick = () => { curChat=g.key; isGroup=true; loadChat(); openPage('chat', document.querySelectorAll('nav button')[1]); };
        document.getElementById("groupList").appendChild(d);
    });
});

window.createGroup = () => {
    const g = document.getElementById("groupInput").value.trim();
    if(g) set(ref(db, "groups/"+g), {createdBy: currentUser});
};

function loadChat() {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const isMe = m.val().from === currentUser;
            const div = document.createElement("div");
            div.className = `msg ${isMe ? 'me' : ''}`;
            
            let content = `<b>${m.val().from}</b><br>${m.val().text}`;
            if(m.val().image) content += `<img src="${m.val().image}">`;
            
            let comms = `<div class="comments">` + (m.val().comments ? Object.values(m.val().comments).map(c=>`<div>${c.from}: ${c.text}</div>`).join('') : '') + `</div>`;
            
            div.innerHTML = `<div class="actions" onclick="deleteMsg('${path}','${m.key}')">🗑️</div>
                             ${content} ${comms}
                             <div style="display:flex;gap:4px;margin-top:5px;">
                                <input style="font-size:10px;flex:1" placeholder="Comment..." id="ci-${m.key}">
                                <button onclick="addComment('${path}','${m.key}')" style="font-size:10px">💬</button>
                             </div>`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.sendMsg = () => {
    const val = document.getElementById('msgInput').value;
    if(!val || !curChat) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    push(ref(db, path), {from: currentUser, text: val});
    document.getElementById('msgInput').value = '';
};

window.sendPhoto = () => {
    const url = prompt("Paste Image URL here:");
    if(!url) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    push(ref(db, path), {from: currentUser, text: "Sent a photo", image: url});
};

window.deleteMsg = (path, key) => { if(confirm("Delete?")) remove(ref(db, path+"/"+key)); };

window.addComment = (path, key) => {
    const inp = document.getElementById("ci-"+key);
    if(inp.value) push(ref(db, path+"/"+key+"/comments"), {from: currentUser, text: inp.value});
    inp.value = "";
};
</script>
</body>
</html>
