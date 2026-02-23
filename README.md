<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro | Modern</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #0a0e17;
            --glass: rgba(22, 27, 34, 0.85);
            --border: rgba(255, 255, 255, 0.1);
            --text: #e6edf3;
            --primary: #58a6ff;
            --accent: #ff7b72;
            --grad: linear-gradient(135deg, #58a6ff, #bc8cff);
        }
        body.light {
            --bg: #f0f2f5;
            --glass: rgba(255, 255, 255, 0.9);
            --border: rgba(0, 0, 0, 0.1);
            --text: #1c1e21;
        }
        body { margin: 0; font-family: 'Inter', sans-serif; background: var(--bg); color: var(--text); overflow: hidden; transition: 0.4s; }
        .app { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; }

        /* Header */
        header { padding: 20px; background: var(--glass); backdrop-filter: blur(15px); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 10; }
        header h1 { margin: 0; font-size: 22px; font-weight: 800; background: var(--grad); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* Navigation */
        nav { position: absolute; bottom: 0; width: 100%; height: 75px; background: var(--glass); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; backdrop-filter: blur(15px); z-index: 10; }
        nav i { font-size: 22px; color: #8b949e; cursor: pointer; transition: 0.3s; padding: 12px; border-radius: 15px; }
        nav i.active { color: var(--primary); background: rgba(88, 166, 255, 0.1); }

        /* Pages */
        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 160px; scroll-behavior: smooth; }
        .page.active { display: block; animation: slideIn 0.3s ease-out; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }

        /* Dashboard Cards */
        .hero { background: var(--grad); padding: 30px; border-radius: 20px; color: white; text-align: center; margin-bottom: 20px; box-shadow: 0 10px 20px rgba(0,0,0,0.2); }
        .card { background: var(--glass); border: 1px solid var(--border); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }

        /* Chat Bubbles */
        .chat-box { display: flex; flex-direction: column; gap: 15px; }
        .msg { position: relative; max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 15px; line-height: 1.4; }
        .msg.me { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 4px; }
        .msg.other { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .msg img { width: 100%; border-radius: 12px; margin-top: 8px; }
        .msg b { font-size: 11px; display: block; margin-bottom: 4px; opacity: 0.8; }
        
        /* Reaction & Delete */
        .msg:hover .del-btn { display: flex; }
        .del-btn { position: absolute; top: -10px; right: -5px; background: var(--accent); color: white; width: 22px; height: 22px; border-radius: 50%; font-size: 10px; display: none; align-items: center; justify-content: center; cursor: pointer; }
        .react-badge { position: absolute; bottom: -10px; right: 10px; background: #161b22; border: 1px solid var(--border); border-radius: 10px; padding: 2px 5px; font-size: 12px; }
        
        /* Reaction Menu */
        .react-menu { display: none; position: absolute; top: -45px; background: #1c2128; border: 1px solid var(--primary); border-radius: 20px; padding: 5px 10px; gap: 10px; z-index: 20; }
        .msg:active .react-menu { display: flex; }

        /* Typing Status */
        #typing-box { font-size: 12px; color: var(--primary); margin-bottom: 5px; min-height: 15px; font-style: italic; }

        /* Bottom Input */
        .input-area { position: absolute; bottom: 85px; width: 100%; padding: 0 15px; box-sizing: border-box; }
        .modern-bar { background: var(--glass); border: 1px solid var(--border); border-radius: 35px; display: flex; align-items: center; padding: 5px 15px; backdrop-filter: blur(20px); }
        .modern-bar input { flex: 1; background: transparent; border: none; color: var(--text); padding: 12px; outline: none; font-size: 15px; }
        .icon-btn { color: var(--primary); font-size: 20px; cursor: pointer; margin: 0 5px; }

        /* Comments */
        .comm-section { border-top: 1px solid rgba(255,255,255,0.1); margin-top: 8px; padding-top: 5px; font-size: 12px; }
        .comm-inp { background: rgba(0,0,0,0.2); border: none; border-radius: 10px; color: white; padding: 5px; width: 100%; margin-top: 5px; font-size: 11px; outline: none; }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div>
            <i class="fa-solid fa-moon" onclick="toggleTheme()" style="cursor:pointer; margin-right:15px"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--accent); cursor:pointer"></i>
        </div>
    </header>

    <div id="loginPage" class="page active" style="display:flex; flex-direction:column; justify-content:center">
        <div class="hero"><h2>Welcome! 🚀</h2><p>Sign in to start chatting</p></div>
        <div class="card" style="flex-direction:column; gap:15px">
            <input type="text" id="usernameInput" placeholder="Enter Name..." style="width:100%; padding:10px; background:transparent; border:none; border-bottom:2px solid var(--primary); color:white; outline:none; text-align:center">
            <button onclick="login()" style="width:100%; padding:12px; background:var(--primary); color:white; border:none; border-radius:12px; cursor:pointer">Connect Now</button>
        </div>
    </div>

    <div id="home" class="page">
        <div class="hero"><h2>Hi, <span id="dashUser"></span>! 👋</h2></div>
        <div class="card"><span>Online Now</span> <b id="onlineCount" style="color:var(--primary)">0</b></div>
        <div class="card"><span>Active Groups</span> <b id="groupCount" style="color:var(--primary)">0</b></div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox" class="chat-box"></div>
        <div class="input-area">
            <div id="typing-box"></div>
            <div class="modern-bar">
                <label for="imgIn"><i class="fa-solid fa-camera icon-btn"></i></label>
                <input type="file" id="imgIn" accept="image/*" style="display:none" onchange="uploadPhoto(this)">
                <input type="text" id="msgInput" placeholder="Message..." oninput="isTyping()">
                <i class="fa-solid fa-paper-plane icon-btn" onclick="sendMsg()"></i>
            </div>
        </div>
    </div>

    <div id="users" class="page">
        <h3>Members</h3><div id="userList"></div>
        <h3>Communities</h3><div id="groupList"></div>
        <div class="modern-bar" style="margin-top:15px">
            <input id="groupInput" placeholder="New Group Name">
            <i class="fa-solid fa-plus icon-btn" onclick="createGroup()"></i>
        </div>
    </div>

    <div id="about" class="page">
        <h3>About Us</h3>
        <div class="card">Developed by WebHub Technologies</div>
        <div class="card">Real-time Modern Messaging Engine</div>
    </div>

    <div id="contact" class="page">
        <h3>Contact</h3>
        <div class="card" onclick="location.href='mailto:webhub262@gmail.com'"><i class="fa-solid fa-envelope"></i> Email Us</div>
        <div class="card"><i class="fa-brands fa-facebook"></i> Facebook</div>
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

let currentUser = null, curChat = "Global", isGroup = true, typeTimer;

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("dashUser").textContent = currentUser;
    set(ref(db, "users/"+currentUser), {name: currentUser, online: true});
    onDisconnect(ref(db, "users/"+currentUser)).update({online: false});
    document.getElementById("loginPage").style.display="none";
    document.getElementById("home").classList.add("active");
    document.getElementById("navbar").style.display="flex";
};

window.logout = () => location.reload();
window.toggleTheme = () => document.body.classList.toggle('light');

window.openPage = (id, icon) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
    icon.classList.add('active');
};

// Typing Logic
window.isTyping = () => {
    set(ref(db, `typing/${curChat}/${currentUser}`), true);
    clearTimeout(typeTimer);
    typeTimer = setTimeout(() => set(ref(db, `typing/${curChat}/${currentUser}`), null), 2000);
};

onValue(ref(db, `typing/${curChat}`), snap => {
    const typers = Object.keys(snap.val() || {}).filter(u => u !== currentUser);
    document.getElementById("typing-box").innerText = typers.length ? typers.join(', ') + " typing..." : "";
});

// Sync
onValue(ref(db, "users"), snap => {
    let c = 0; document.getElementById("userList").innerHTML = "";
    snap.forEach(u => {
        if(u.val().online) { c++; if(u.key!==currentUser){
            let d = document.createElement("div"); d.className="card"; d.innerHTML=`${u.val().name} 💬`;
            d.onclick=()=>{curChat=u.key; isGroup=false; loadChat(); openPage('chat',document.querySelectorAll('nav i')[1])};
            document.getElementById("userList").appendChild(d);
        }}
    });
    document.getElementById("onlineCount").innerText = c;
});

onValue(ref(db, "groups"), snap => {
    document.getElementById("groupList").innerHTML = "";
    document.getElementById("groupCount").innerText = snap.size || 0;
    snap.forEach(g => {
        let d = document.createElement("div"); d.className="card"; d.innerHTML=`# ${g.key}`;
        d.onclick=()=>{curChat=g.key; isGroup=true; loadChat(); openPage('chat',document.querySelectorAll('nav i')[1])};
        document.getElementById("groupList").appendChild(d);
    });
});

window.uploadPhoto = (input) => {
    const reader = new FileReader();
    reader.onload = (e) => {
        const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
        push(ref(db, path), {from: currentUser, text: "📷 Photo", image: e.target.result});
    };
    reader.readAsDataURL(input.files[0]);
};

function loadChat() {
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val(), isMe = d.from === currentUser;
            const div = document.createElement("div"); div.className = `msg ${isMe ? 'me' : 'other'}`;
            div.innerHTML = `
                <div class="del-btn" onclick="deleteMsg('${path}','${m.key}')">✕</div>
                <b>${d.from}</b>${d.text} ${d.image ? `<img src="${d.image}">` : ''}
                <div class="react-badge">${d.react || ''}</div>
                <div class="react-menu">
                    <span onclick="react('${path}','${m.key}','❤️')">❤️</span>
                    <span onclick="react('${path}','${m.key}','😂')">😂</span>
                    <span onclick="react('${path}','${m.key}','👍')">👍</span>
                </div>
                <div class="comm-section">
                    ${d.comms ? Object.values(d.comms).map(c=>`<div>${c.f}: ${c.t}</div>`).join('') : ''}
                    <input class="comm-inp" placeholder="Reply..." onkeydown="if(event.key==='Enter')addComm('${path}','${m.key}',this.value)">
                </div>`;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.sendMsg = () => {
    const inp = document.getElementById("msgInput"); if(!inp.value) return;
    const path = (isGroup ? "groupChats/" : "chats/") + (isGroup ? curChat : [currentUser, curChat].sort().join("_"));
    push(ref(db, path), {from: currentUser, text: inp.value});
    inp.value = "";
};

window.react = (p, k, e) => set(ref(db, p+"/"+k+"/react"), e);
window.addComm = (p, k, v) => { if(v) push(ref(db, p+"/"+k+"/comms"), {f: currentUser, t: v}); };
window.deleteMsg = (p, k) => remove(ref(db, p+"/"+k));
window.createGroup = () => {
    const g = document.getElementById("groupInput").value.trim();
    if(g) set(ref(db, "groups/"+g), {by: currentUser});
};
</script>
</body>
</html>
