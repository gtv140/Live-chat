<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Elite Messenger</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #00e5ff; --s: #0077ff; --bg: #020408; --glass: rgba(255, 255, 255, 0.05); --border: rgba(255, 255, 255, 0.1); }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Outfit', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: #fff; overflow: hidden; }

        /* AUTH SCREEN */
        #authScreen { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 20px; }
        .auth-card { background: var(--glass); backdrop-filter: blur(20px); padding: 40px 25px; border-radius: 35px; border: 1px solid var(--border); width: 100%; max-width: 380px; text-align: center; box-shadow: 0 10px 50px rgba(0,229,255,0.15); }
        .auth-card h1 { font-size: 32px; font-weight: 800; color: var(--p); margin-bottom: 5px; }
        .auth-card p { opacity: 0.6; margin-bottom: 30px; }
        .pfp-selector { display: flex; justify-content: center; gap: 15px; margin-bottom: 25px; }
        .avatar { width: 50px; height: 50px; border-radius: 50%; border: 2px solid transparent; cursor: pointer; transition: 0.3s; }
        .avatar.active { border-color: var(--p); transform: scale(1.2); }
        .auth-input { width: 100%; padding: 15px; border-radius: 15px; border: 1px solid var(--border); background: rgba(0,0,0,0.4); color: #fff; margin-bottom: 20px; outline: none; text-align: center; font-size: 16px; }
        .auth-btn { width: 100%; padding: 16px; border-radius: 15px; border: none; background: linear-gradient(135deg, var(--p), var(--s)); color: #000; font-weight: 800; font-size: 17px; cursor: pointer; }

        /* MAIN APP */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
        header { padding: 20px; background: rgba(2,4,8,0.9); backdrop-filter: blur(15px); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 1000; }
        header h2 { margin: 0; font-size: 22px; font-weight: 800; background: linear-gradient(to right, #fff, var(--p)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        .viewport { flex: 1; overflow-y: auto; padding: 20px; display: none; padding-bottom: 150px; }
        .viewport.active { display: block; animation: fadeInUp 0.4s ease; }
        @keyframes fadeInUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        /* DASHBOARD STATS */
        .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 30px; }
        .stat-box { background: var(--glass); padding: 20px; border-radius: 25px; border: 1px solid var(--border); text-align: center; }
        .stat-box i { font-size: 24px; color: var(--p); margin-bottom: 10px; display: block; }
        .stat-box b { font-size: 22px; display: block; }

        /* CHAT BUBBLES */
        .chat-container { display: flex; flex-direction: column; gap: 18px; }
        .msg-wrapper { display: flex; flex-direction: column; max-width: 85%; }
        .msg-wrapper.me { align-self: flex-end; align-items: flex-end; }
        .msg-wrapper.them { align-self: flex-start; align-items: flex-start; }
        .bubble { padding: 14px 18px; border-radius: 24px; font-size: 15px; line-height: 1.5; }
        .me .bubble { background: linear-gradient(135deg, var(--p), var(--s)); color: #000; font-weight: 600; border-bottom-right-radius: 4px; }
        .them .bubble { background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .msg-meta { font-size: 10px; opacity: 0.4; margin: 5px 10px; font-weight: 700; }

        /* DOCK INPUT */
        .input-dock { position: absolute; bottom: 95px; left: 50%; transform: translateX(-50%); width: 92%; background: rgba(30,35,45,0.9); backdrop-filter: blur(25px); padding: 10px 15px; border-radius: 40px; border: 1px solid var(--border); display: none; align-items: center; gap: 12px; z-index: 2000; box-shadow: 0 10px 40px rgba(0,0,0,0.5); }
        .input-dock input { flex: 1; background: transparent; border: none; color: #fff; font-size: 16px; outline: none; }
        .send-icon { width: 45px; height: 45px; border-radius: 50%; background: var(--p); border: none; display: flex; align-items: center; justify-content: center; cursor: pointer; color: #000; }

        /* NAV BAR */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 80px; background: #020408; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-link { color: #5c6a7e; text-align: center; cursor: pointer; transition: 0.3s; }
        .nav-link.active { color: var(--p); text-shadow: 0 0 10px var(--p); }
        .nav-link i { font-size: 24px; display: block; margin-bottom: 5px; }
        .nav-link span { font-size: 11px; font-weight: 700; }
    </style>
</head>
<body>

<div id="authScreen">
    <div class="auth-card">
        <h1>LIVE CONNECT</h1>
        <p>Initialize your secure node</p>
        <div class="pfp-selector" id="pfpList">
            <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Felix" class="avatar active" onclick="selPfp(this)">
            <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Aneka" class="avatar" onclick="selPfp(this)">
            <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Jack" class="avatar" onclick="selPfp(this)">
        </div>
        <input type="text" id="userName" class="auth-input" placeholder="Your Display Name">
        <button class="auth-btn" onclick="initNode()">CONNECT NOW</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2 id="mainTitle">LIVE CONNECT</h2>
        <i class="fa-solid fa-bell-concierge" style="color:var(--p)"></i>
    </header>

    <div id="v-dash" class="viewport active">
        <div class="stats-grid">
            <div class="stat-box"><i class="fa-solid fa-satellite-dish"></i><b id="activeNodes">1</b><span>Active Nodes</span></div>
            <div class="stat-box"><i class="fa-solid fa-fingerprint"></i><b>E2EE</b><span>Encrypted</span></div>
        </div>
        <div style="background:var(--glass); padding:25px; border-radius:30px; border:1px solid var(--border)">
            <h3>About System</h3>
            <p style="opacity:0.7; font-size:14px; line-height:1.6">Live Connect is a futuristic P2P bridge. All your data is synced in real-time across decentralized clusters.</p>
        </div>
    </div>

    <div id="v-chat" class="viewport">
        <div id="chatFeed" class="chat-container"></div>
    </div>

    <div id="v-contact" class="viewport">
        <div style="background:var(--glass); padding:30px; border-radius:30px; border:1px solid var(--border)">
            <h2>Contact Center</h2>
            <input type="text" class="auth-input" placeholder="Subject">
            <textarea class="auth-input" style="height:120px; text-align:left" placeholder="Message details..."></textarea>
            <button class="auth-btn">SEND SIGNAL</button>
        </div>
    </div>

    <div class="input-dock" id="chatDock">
        <i class="fa-solid fa-paperclip" style="opacity:0.5"></i>
        <input type="text" id="msgInput" placeholder="Write message..." onfocus="scrBot()">
        <button class="send-icon" onclick="pushMsg()"><i class="fa-solid fa-location-arrow"></i></button>
    </div>

    <nav>
        <div class="nav-link active" onclick="tab('dash')"><i class="fa-solid fa-house-chimney-window"></i><span>Home</span></div>
        <div class="nav-link" onclick="tab('chat')"><i class="fa-solid fa-comments"></i><span>Lobby</span></div>
        <div class="nav-link" onclick="tab('contact')"><i class="fa-solid fa-headset"></i><span>Support</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, push, onValue, set } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const fbConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(fbConfig);
const db = getDatabase(app);

let myId = localStorage.getItem("lc_user");
let myPfp = "https://api.dicebear.com/7.x/avataaars/svg?seed=Felix";

if(myId) {
    document.getElementById("authScreen").style.display = "none";
    loadSystem();
}

window.selPfp = (el) => {
    document.querySelectorAll('.avatar').forEach(a => a.classList.remove('active'));
    el.classList.add('active');
    myPfp = el.src;
};

window.initNode = () => {
    const name = document.getElementById("userName").value.trim();
    if(name.length < 2) return;
    localStorage.setItem("lc_user", name);
    location.reload();
};

function loadSystem() {
    set(ref(db, 'nodes_v5/' + myId), { online: true });
    
    // Sync Msgs
    onValue(ref(db, 'msgs_v5/global'), (snap) => {
        const feed = document.getElementById("chatFeed");
        feed.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div");
            div.className = `msg-wrapper ${d.from === myId ? 'me' : 'them'}`;
            div.innerHTML = `
                <span class="msg-meta">${d.from.toUpperCase()}</span>
                <div class="bubble">${d.text}</div>
            `;
            feed.appendChild(div);
        });
        scrBot();
    });
}

window.tab = (v) => {
    document.querySelectorAll('.viewport').forEach(el => el.classList.remove('active'));
    document.querySelectorAll('.nav-link').forEach(el => el.classList.remove('active'));
    document.getElementById('v-'+v).classList.add('active');
    event.currentTarget.classList.add('active');
    document.getElementById('chatDock').style.display = (v === 'chat') ? 'flex' : 'none';
};

window.scrBot = () => {
    const v = document.getElementById('v-chat');
    setTimeout(() => { v.scrollTop = v.scrollHeight; }, 150);
};

window.pushMsg = () => {
    const i = document.getElementById("msgInput");
    if(!i.value.trim()) return;
    push(ref(db, 'msgs_v5/global'), { from: myId, text: i.value });
    i.value = "";
    scrBot();
};
</script>
</body>
</html>
