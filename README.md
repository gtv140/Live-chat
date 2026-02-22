<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Connect | Premium</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --p: #00e5ff; --s: #0077ff; --bg: #080a0f;
            --card: rgba(20, 25, 35, 0.95); --border: rgba(255, 255, 255, 0.08);
            --text: #ffffff; --text-dim: #94a3b8;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* Container */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; background: var(--bg); position: relative; }

        /* Header */
        header { 
            padding: 18px 20px; background: rgba(8, 10, 15, 0.8); backdrop-filter: blur(20px);
            border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 100;
        }
        header h1 { font-size: 22px; font-weight: 800; margin: 0; background: linear-gradient(90deg, var(--p), var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* Story/Status Bar */
        .story-bar { display: flex; gap: 15px; padding: 15px 20px; overflow-x: auto; scrollbar-width: none; background: rgba(255,255,255,0.02); }
        .story { min-width: 65px; height: 65px; border-radius: 50%; border: 2px solid var(--p); padding: 3px; cursor: pointer; position: relative; }
        .story img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
        .story .plus { position: absolute; bottom: 0; right: 0; background: var(--p); color: #000; width: 20px; height: 20px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 12px; border: 2px solid var(--bg); }

        /* Viewport & Scrolling Fix */
        .viewport { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; scroll-behavior: smooth; padding-bottom: 160px; }
        .view { display: none; width: 100%; }
        .view.active { display: flex; flex-direction: column; }

        /* Modern Chat Bubbles */
        .chat-area { display: flex; flex-direction: column; gap: 10px; width: 100%; }
        .msg-node { display: flex; flex-direction: column; max-width: 80%; position: relative; margin-bottom: 5px; }
        .msg-node.me { align-self: flex-end; }
        .msg-node.them { align-self: flex-start; }

        .bubble { 
            padding: 12px 16px; border-radius: 20px; font-size: 15px; line-height: 1.4; word-wrap: break-word; position: relative;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        }
        .me .bubble { background: linear-gradient(135deg, var(--p), var(--s)); color: #000; border-bottom-right-radius: 4px; font-weight: 500; }
        .them .bubble { background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; }

        .meta { font-size: 10px; margin-top: 4px; opacity: 0.6; display: flex; align-items: center; gap: 4px; }
        .me .meta { justify-content: flex-end; }

        /* User List Card */
        .user-row { background: var(--card); padding: 16px; border-radius: 20px; border: 1px solid var(--border); display: flex; align-items: center; gap: 15px; margin-bottom: 12px; transition: 0.2s; }
        .user-row:active { transform: scale(0.96); }
        .avatar { width: 48px; height: 48px; border-radius: 50%; background: #1e293b; display: flex; align-items: center; justify-content: center; font-weight: 800; color: var(--p); border: 2px solid var(--border); }

        /* Input Dock (Fixed) */
        .dock {
            position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%);
            width: 92%; max-width: 440px; background: rgba(25, 30, 45, 0.9); backdrop-filter: blur(25px);
            padding: 10px 18px; border-radius: 35px; border: 1px solid var(--border);
            display: flex; align-items: center; gap: 12px; z-index: 1000; box-shadow: 0 15px 40px rgba(0,0,0,0.4);
        }
        .dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 16px; }
        .btn-icon { width: 42px; height: 42px; border-radius: 50%; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; }
        .send-btn { background: var(--p); color: #000; border: none; }

        /* Bottom Nav */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: #080a0f; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        .nav-link { display: flex; flex-direction: column; align-items: center; gap: 4px; color: var(--text-dim); text-decoration: none; font-size: 11px; font-weight: 600; cursor: pointer; }
        .nav-link.active { color: var(--p); }
        .nav-link i { font-size: 22px; }

        /* Delete Feature */
        .del-msg { position: absolute; right: -25px; top: 50%; transform: translateY(-50%); font-size: 14px; color: #ff4444; display: none; cursor: pointer; }
        .msg-node.me:hover .del-msg { display: block; }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1>INFINITY</h1>
        <div style="display:flex; gap:18px; font-size:18px; color:var(--text-dim);">
            <i class="fa-solid fa-bell"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:#ff4444"></i>
        </div>
    </header>

    <div class="story-bar" id="storyBar" style="display:none;">
        <div class="story" onclick="document.getElementById('stInp').click()">
            <img src="https://api.dicebear.com/7.x/identicon/svg?seed=Me">
            <div class="plus"><i class="fa-solid fa-plus"></i></div>
            <input type="file" id="stInp" style="display:none" onchange="upStatus(event)">
        </div>
        <div id="stList" style="display:flex; gap:15px;"></div>
    </div>

    <div class="viewport" id="scrollArea">
        <div id="v-login" class="view active">
            <div style="text-align:center; padding-top:10vh;">
                <div style="font-size:70px; margin-bottom:20px;">⚡</div>
                <h2 style="font-size:28px; margin-bottom:5px;">System Access</h2>
                <p style="color:var(--text-dim); margin-bottom:35px;">Identity verification required.</p>
                <input type="text" id="uInp" placeholder="Enter Username..." style="width:100%; padding:20px; border-radius:20px; background:var(--card); border:1px solid var(--p); color:white; text-align:center; outline:none; font-size:18px;">
                <button onclick="login()" style="width:100%; padding:20px; border-radius:20px; background:var(--p); border:none; color:#000; font-weight:800; margin-top:20px; font-size:18px; box-shadow: 0 10px 25px rgba(0,229,255,0.3);">Initialize Sync</button>
            </div>
        </div>

        <div id="v-home" class="view">
            <div style="background:var(--card); padding:25px; border-radius:25px; border:1px solid var(--border); margin-bottom:25px; position:relative;">
                <h2 id="greet" style="margin:0">Hi, Networker</h2>
                <p style="color:var(--p); font-size:13px; margin-top:5px;">● All systems operational</p>
            </div>
            <h3 style="font-size:16px; color:var(--text-dim); margin-bottom:15px; padding-left:5px;">Active Nodes</h3>
            <div id="uList"></div>
        </div>

        <div id="v-chat" class="view">
            <div style="display:flex; align-items:center; gap:12px; margin-bottom:20px; padding-bottom:10px; border-bottom:1px solid var(--border);">
                <i class="fa-solid fa-arrow-left" onclick="nav('home', 'hBtn')" style="font-size:18px;"></i>
                <b id="activeName" style="font-size:18px;">Global Hub</b>
            </div>
            <div id="chatBox" class="chat-area"></div>
            <div id="typing" style="font-size:12px; color:var(--p); margin-top:10px; display:none;">typing...</div>
        </div>
    </div>

    <div class="dock" id="dock" style="display:none;">
        <i class="fa-solid fa-paperclip" style="font-size:20px; color:var(--p)" onclick="alert('Files Coming Soon')"></i>
        <input type="text" id="mInp" placeholder="Message..." oninput="setTyping()">
        <button class="btn-icon send-btn" onclick="send()">
            <i class="fa-solid fa-paper-plane"></i>
        </button>
    </div>

    <nav id="bNav" style="display:none;">
        <div class="nav-link active" id="hBtn" onclick="nav('home', 'hBtn')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-link" id="cBtn" onclick="nav('chat', 'cBtn')"><i class="fa-solid fa-comments"></i><span>Chats</span></div>
        <div class="nav-link" onclick="alert('Profile Section Lock')"><i class="fa-solid fa-circle-user"></i><span>Profile</span></div>
        <div class="nav-link" onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i><span>Logout</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("apex_v5_user");
let room = "Global_Lobby";
let isP = false;
let tTimer;

if(user) start();

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(!v) return;
    user = v;
    localStorage.setItem("apex_v5_user", v);
    start();
};

function start() {
    document.getElementById("v-login").classList.remove("active");
    document.getElementById("v-home").classList.add("active");
    document.getElementById("bNav").style.display = "flex";
    document.getElementById("storyBar").style.display = "flex";
    document.getElementById("greet").innerText = "Hi, " + user;
    
    set(ref(db, "presence/" + user), true);
    onDisconnect(ref(db, "presence/" + user)).remove();
    loadNodes();
}

window.nav = (vId, bId) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + vId).classList.add("active");
    document.querySelectorAll('.nav-link').forEach(n => n.classList.remove('active'));
    document.getElementById(bId).classList.add('active');
    document.getElementById("dock").style.display = (vId === 'chat') ? 'flex' : 'none';
    if(vId === 'chat') sync();
};

function loadNodes() {
    onValue(ref(db, "presence"), snap => {
        const l = document.getElementById("uList");
        l.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== user) {
                const div = document.createElement("div");
                div.className = "user-row";
                div.innerHTML = `<div class="avatar">${u.key[0].toUpperCase()}</div>
                                 <div style="flex:1"><b>${u.key}</b><br><small style="color:var(--p)">● Active Now</small></div>
                                 <i class="fa-solid fa-chevron-right" style="opacity:0.3"></i>`;
                div.onclick = () => {
                    room = [user, u.key].sort().join("__");
                    isP = true;
                    document.getElementById("activeName").innerText = u.key;
                    nav('chat', 'cBtn');
                };
                l.appendChild(div);
            }
        });
    });

    onValue(ref(db, "typing/" + room), snap => {
        const t = document.getElementById("typing");
        if(snap.val() && snap.val() !== user) t.style.display = "block";
        else t.style.display = "none";
    });
}

window.setTyping = () => {
    set(ref(db, "typing/" + room), user);
    clearTimeout(tTimer);
    tTimer = setTimeout(() => set(ref(db, "typing/" + room), null), 2000);
};

function sync() {
    const path = isP ? "messages_p/" + room : "messages_g/main";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const node = document.createElement("div");
            node.className = "msg-node " + (d.from === user ? "me" : "them");
            node.innerHTML = `
                <div class="bubble">${d.text}</div>
                <div class="meta">${d.from} • Just now ${d.from === user ? '<i class="fa-solid fa-check-double" style="color:var(--p)"></i>' : ''}</div>
                ${d.from === user ? `<i class="fa-solid fa-trash del-msg" onclick="deleteMsg('${m.key}')"></i>` : ''}
            `;
            box.appendChild(node);
        });
        // AUTO SCROLL
        setTimeout(() => {
            const sc = document.getElementById('scrollArea');
            sc.scrollTop = sc.scrollHeight;
        }, 50);
    });
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    const path = isP ? "messages_p/" + room : "messages_g/main";
    push(ref(db, path), { from: user, text: i.value, ts: Date.now() });
    i.value = "";
};

window.deleteMsg = (key) => {
    const path = isP ? "messages_p/" + room : "messages_g/main";
    remove(ref(db, path + "/" + key));
};

window.upStatus = (e) => {
    const reader = new FileReader();
    reader.onload = () => { 
        set(ref(db, "status_v5/" + user), { img: reader.result }); 
        alert("Status Broadcasted!");
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
