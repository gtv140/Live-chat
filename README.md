<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #0a0e17;
            --glass: rgba(22, 27, 34, 0.8);
            --border: rgba(255, 255, 255, 0.1);
            --text: #e6edf3;
            --primary: #58a6ff;
            --accent: #ff7b72;
            --secondary: #238636;
            --grad: linear-gradient(135deg, #58a6ff, #bc8cff);
        }
        body.light {
            --bg: #f0f2f5;
            --glass: rgba(255, 255, 255, 0.9);
            --border: rgba(0, 0, 0, 0.1);
            --text: #1c1e21;
            --primary: #0084ff;
        }
        body { margin: 0; font-family: 'Inter', system-ui, sans-serif; background: var(--bg); color: var(--text); overflow: hidden; transition: 0.4s; }
        
        .app-container { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; background: radial-gradient(circle at top right, rgba(88, 166, 255, 0.1), transparent); }

        /* Header Modernized */
        header { padding: 25px 20px; background: var(--glass); backdrop-filter: blur(15px); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 10; }
        header h1 { margin: 0; font-size: 26px; font-weight: 900; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; letter-spacing: -1px; }
        .head-btns i { font-size: 22px; margin-left: 15px; cursor: pointer; transition: 0.3s; opacity: 0.8; }
        .head-btns i:hover { transform: scale(1.2); opacity: 1; }

        /* Smooth Pages */
        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 150px; scroll-behavior: smooth; }
        .page.active { display: block; animation: slideUp 0.4s ease-out; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        /* Modern Cards */
        .hero-banner { background: var(--grad); padding: 35px 20px; border-radius: 25px; color: white; text-align: center; margin-bottom: 25px; box-shadow: 0 10px 30px rgba(88, 166, 255, 0.3); }
        .card { background: var(--glass); border: 1px solid var(--border); padding: 20px; border-radius: 20px; margin-bottom: 15px; backdrop-filter: blur(10px); transition: 0.3s; }
        .card:hover { border-color: var(--primary); transform: translateY(-3px); }

        /* Chat Bubbles like Screenshot */
        .chat-box { display: flex; flex-direction: column; gap: 20px; }
        .msg { position: relative; max-width: 85%; padding: 15px; border-radius: 22px; line-height: 1.5; font-size: 15px; box-shadow: 0 5px 15px rgba(0,0,0,0.1); }
        .msg.me { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 5px; }
        .msg.other { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 5px; }
        .msg b { font-size: 12px; text-transform: uppercase; letter-spacing: 0.5px; margin-bottom: 5px; display: block; opacity: 0.9; }
        .msg img { width: 100%; border-radius: 15px; margin-top: 10px; cursor: pointer; }

        /* Reply/Comment System */
        .comment-pill { background: rgba(0,0,0,0.1); padding: 8px 12px; border-radius: 12px; margin-top: 8px; font-size: 13px; border-left: 3px solid var(--accent); }
        .reply-input { display: flex; gap: 8px; margin-top: 12px; border-top: 1px solid rgba(255,255,255,0.1); padding-top: 10px; }
        .reply-input input { background: rgba(255,255,255,0.05); border: none; border-radius: 10px; color: white; padding: 5px 10px; flex: 1; font-size: 12px; }

        /* Floating Bottom Input Bar */
        .input-wrapper { position: absolute; bottom: 85px; left: 0; width: 100%; padding: 0 15px; box-sizing: border-box; }
        .modern-input { background: var(--glass); backdrop-filter: blur(20px); border: 1px solid var(--border); border-radius: 40px; padding: 8px 10px; display: flex; align-items: center; gap: 10px; box-shadow: 0 10px 25px rgba(0,0,0,0.2); }
        .modern-input input { flex: 1; background: transparent; border: none; color: var(--text); padding: 10px; outline: none; font-size: 16px; }
        .circle-btn { width: 45px; height: 45px; border-radius: 50%; background: var(--primary); color: white; border: none; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 18px; transition: 0.3s; }
        .circle-btn:active { transform: scale(0.9); }

        /* Navigation Bar */
        nav { position: absolute; bottom: 0; left: 0; width: 100%; height: 75px; background: var(--glass); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; backdrop-filter: blur(15px); }
        nav i { font-size: 24px; color: #8b949e; transition: 0.3s; padding: 10px; border-radius: 15px; }
        nav i.active { color: var(--primary); background: rgba(88, 166, 255, 0.1); }

        /* Delete Button */
        .del-msg { position: absolute; top: -10px; right: -5px; background: var(--accent); width: 24px; height: 24px; border-radius: 50%; font-size: 12px; color: white; display: flex; align-items: center; justify-content: center; opacity: 0; transition: 0.3s; cursor: pointer; }
        .msg:hover .del-msg { opacity: 1; top: -5px; }
    </style>
</head>
<body>

<div class="app-container">
    <header>
        <h1>Live Connect</h1>
        <div class="head-btns">
            <i class="fa-solid fa-circle-half-stroke" onclick="toggleTheme()"></i>
            <i class="fa-solid fa-power-off" style="color:var(--accent)" onclick="logout()"></i>
        </div>
    </header>

    <div id="loginPage" class="page active" style="display: flex; flex-direction: column; justify-content: center;">
        <div class="hero-banner"><h2>Ready to Connect?</h2><p>Join the conversation instantly.</p></div>
        <div class="card">
            <input type="text" id="usernameInput" placeholder="Enter your name..." style="width:100%; background:transparent; border:none; border-bottom:2px solid var(--primary); color:var(--text); font-size:18px; padding:10px; outline:none;">
            <button class="circle-btn" style="width:100%; border-radius:15px; margin-top:20px;" onclick="login()">Enter Chat <i class="fa-solid fa-arrow-right"></i></button>
        </div>
    </div>

    <div id="home" class="page">
        <div class="hero-banner"><h2>Hello, <span id="dashUser"></span>! 👋</h2></div>
        <div class="card"><b>Online Members</b> <span style="float:right; color:var(--primary)" id="onlineCount">0</span></div>
        <div class="card"><b>Active Groups</b> <span style="float:right; color:var(--primary)" id="groupCount">0</span></div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox" class="chat-box"></div>
        <div class="input-wrapper">
            <div class="modern-input">
                <label for="imgIn"><i class="fa-solid fa-camera circle-btn" style="background:none; color:var(--primary)"></i></label>
                <input type="file" id="imgIn" accept="image/*" style="display:none" onchange="uploadImg(this)">
                <input type="text" id="msgInput" placeholder="Write a message...">
                <button class="circle-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <div id="users" class="page">
        <h3 style="margin-bottom:15px;">Available Contacts</h3><div id="userList"></div>
        <h3 style="margin:25px 0 15px;">Groups</h3><div id="groupList"></div>
        <div class="modern-input" style="margin-top:10px">
            <input type="text" id="groupInput" placeholder="New Group Name">
            <i class="fa-solid fa-plus circle-btn" onclick="createGroup()"></i>
        </div>
    </div>

    <div id="about" class="page">
        <h3>About This App</h3>
        <div class="card"><h4>WebHub Technologies</h4><p>High-speed real-time engine with modern UI aesthetics.</p></div>
        <div class="card"><h4>Features</h4><p>Photo Gallery, Admin Controls, and End-to-End Visuals.</p></div>
    </div>

    <div id="contact" class="page">
        <h3>Contact Support</h3>
        <a href="mailto:webhub262@gmail.com" class="card" style="display:block; text-decoration:none;"><i class="fa-solid fa-envelope"></i> Email Support</a>
        <div class="card"><i class="fa-brands fa-facebook"></i> Facebook Admin</div>
        <div class="card"><i class="fa-brands fa-instagram"></i> Instagram</div>
    </div>

    <nav id="navbar" style="display:none">
        <i class="fa-solid fa-house active" onclick="openPage('home', this)"></i>
        <i class="fa-solid fa-comment" onclick="openPage('chat', this)"></i>
        <i class="fa-solid fa-user-group" onclick="openPage('users', this)"></i>
        <i class="fa-solid fa-info-circle" onclick="openPage('about', this)"></i>
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
    document.getElementById("loginPage").classList.remove("active");
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
                let d = document.createElement("div"); d.className = "card";
                d.innerHTML = `<span><i class="fa-solid fa-circle" style="color:#2ea043; font-size:10px;"></i> ${u.val().name}</span>`;
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
        let d = document.createElement("div"); d.className = "card";
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
        push(ref(db, path), {from: currentUser, text: "📷 Sent a photo", image: e.target.result});
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
            
            let imgPart = data.image ? `<img src="${data.image}" onclick="window.open('${data.image}')">` : '';
            let comms = data.comments ? Object.values(data.comments).map(c=>`<div class="comment-pill"><b>${c.from}</b> ${c.text}</div>`).join('') : '';

            div.innerHTML = `
                <div class="del-msg" onclick="deleteMsg('${path}','${m.key}')">✕</div>
                <b>${data.from}</b>${data.text} ${imgPart}
                <div style="margin-top:10px;">${comms}</div>
                <div class="reply-input">
                    <input id="r-${m.key}" placeholder="Reply...">
                    <i class="fa-solid fa-paper-plane" onclick="addReply('${path}','${m.key}')"></i>
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

window.addReply = (path, key) => {
    const inp = document.getElementById("r-"+key);
    if(inp.value) push(ref(db, path+"/"+key+"/comments"), {from: currentUser, text: inp.value});
    inp.value = "";
};

window.deleteMsg = (p, k) => { if(confirm("Delete?")) remove(ref(db, p+"/"+k)); };
window.createGroup = () => {
    const g = document.getElementById("groupInput").value.trim();
    if(g) set(ref(db, "groups/"+g), {createdBy: currentUser});
    document.getElementById("groupInput").value = "";
};
</script>
</body>
</html>
