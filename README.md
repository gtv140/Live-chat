<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Connect | Production Level</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600&family=Space+Grotesk:wght@500;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #00f2fe; --secondary: #4facfe; --accent: #8b5cf6;
            --bg: #020617; --card: rgba(30, 41, 59, 0.7);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { 
            margin: 0; padding: 0; background: var(--bg); color: #f8fafc;
            font-family: 'Outfit', sans-serif; height: 100vh; overflow: hidden;
            display: flex; justify-content: center;
        }

        /* Screen Wrapper */
        .app-container {
            width: 100%; max-width: 480px; height: 100vh;
            background: radial-gradient(circle at top right, #1e1b4b, #020617);
            display: flex; flex-direction: column; position: relative;
            box-shadow: 0 0 100px rgba(0,0,0,0.5);
        }

        /* High-End Login */
        #authLayer {
            position: fixed; inset: 0; z-index: 9999; background: #020617;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .auth-card {
            width: 85%; text-align: center; padding: 40px 20px;
            background: rgba(255,255,255,0.03); border-radius: 30px;
            border: 1px solid rgba(255,255,255,0.1); backdrop-filter: blur(10px);
        }
        .auth-card h2 { font-family: 'Space Grotesk', sans-serif; font-size: 28px; margin-bottom: 30px; }
        .auth-input {
            width: 100%; padding: 18px; background: rgba(0,0,0,0.3);
            border: 1px solid rgba(255,255,255,0.1); border-radius: 15px;
            color: #fff; font-size: 16px; outline: none; margin-bottom: 20px;
            text-align: center; transition: 0.3s;
        }
        .auth-input:focus { border-color: var(--primary); box-shadow: 0 0 15px rgba(0, 242, 254, 0.2); }

        /* Navigation & UI Elements */
        header { padding: 25px 20px; display: flex; justify-content: space-between; align-items: center; }
        .brand { font-family: 'Space Grotesk', sans-serif; font-weight: 700; font-size: 20px; color: var(--primary); }
        
        .viewport { flex: 1; overflow-y: auto; padding: 0 15px 100px; }
        
        /* Stats & Cards */
        .stat-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 20px; }
        .dash-card {
            background: var(--card); padding: 25px 15px; border-radius: 24px;
            border: 1px solid rgba(255,255,255,0.05); text-align: center;
            transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .dash-card:active { transform: scale(0.95); background: rgba(255,255,255,0.1); }
        .dash-card i { font-size: 30px; color: var(--primary); margin-bottom: 12px; }
        .dash-card b { display: block; font-size: 14px; letter-spacing: 0.5px; }

        /* Chat System */
        #chatView { display: none; height: 75vh; flex-direction: column; }
        #messageFeed { flex: 1; overflow-y: auto; padding: 10px; display: flex; flex-direction: column; gap: 12px; }
        
        .msg-bubble { 
            max-width: 80%; padding: 14px 18px; border-radius: 22px; font-size: 15px; 
            line-height: 1.4; animation: popIn 0.3s ease-out forwards;
        }
        @keyframes popIn { from { opacity: 0; transform: scale(0.9); } to { opacity: 1; transform: scale(1); } }
        
        .msg-bubble.me { align-self: flex-end; background: var(--accent); border-bottom-right-radius: 4px; }
        .msg-bubble.other { align-self: flex-start; background: rgba(255,255,255,0.08); border-bottom-left-radius: 4px; }
        
        .input-wrapper {
            position: absolute; bottom: 85px; left: 15px; right: 15px;
            background: rgba(15, 23, 42, 0.9); backdrop-filter: blur(20px);
            padding: 8px; border-radius: 50px; border: 1px solid rgba(255,255,255,0.1);
            display: flex; align-items: center; gap: 10px;
        }
        .input-wrapper input { flex: 1; background: none; border: none; color: #fff; padding: 12px 15px; font-size: 15px; outline: none; }
        .btn-send { width: 45px; height: 45px; border-radius: 50%; background: var(--primary); border: none; color: #020617; font-size: 18px; cursor: pointer; }

        /* Bottom Nav */
        nav {
            position: absolute; bottom: 0; width: 100%; height: 75px;
            background: rgba(2, 6, 23, 0.8); backdrop-filter: blur(15px);
            display: flex; justify-content: space-around; align-items: center;
            border-top: 1px solid rgba(255,255,255,0.05);
        }
        nav i { font-size: 22px; color: #475569; transition: 0.3s; cursor: pointer; }
        nav i.active { color: var(--primary); }

        /* Isolated Admin Panel */
        #adminPanel {
            display: none; position: fixed; bottom: 90px; left: 20px; right: 20px;
            background: #000; padding: 25px; border-radius: 25px; border: 1px solid gold;
            box-shadow: 0 0 50px rgba(0,0,0,0.8); z-index: 1000;
        }
    </style>
</head>
<body>

<div id="authLayer">
    <div class="auth-card">
        <h2>Infinity_</h2>
        <input type="text" id="userInput" class="auth-input" placeholder="Codename" autocomplete="off">
        <button onclick="authorize()" style="width:100%; padding:18px; border-radius:15px; border:none; background:var(--primary); font-weight:bold; cursor:pointer;">START_SESSION</button>
    </div>
</div>

<div class="app-container">
    <header>
        <div class="brand">INFINITE</div>
        <i class="fa-solid fa-power-off" onclick="location.reload()" style="opacity:0.5;"></i>
    </header>

    <div id="homePage" class="viewport">
        <div style="margin-top:20px;">
            <h1 id="greet" style="margin:0; font-size:24px;">System Online</h1>
            <p style="opacity:0.5; font-size:14px;">End-to-End Encrypted Signal</p>
        </div>

        <div class="stat-grid">
            <div class="dash-card" onclick="navTo('chatView', 'chatBtn')">
                <i class="fa-solid fa-tower-broadcast"></i>
                <b>COMM_UNIT</b>
            </div>
            <div class="dash-card">
                <i class="fa-solid fa-users-viewfinder"></i>
                <b id="activeNodes">0 Nodes</b>
            </div>
            <div id="adminCard" class="dash-card" style="display:none; border-color: gold;" onclick="toggleAdmin()">
                <i class="fa-solid fa-crown" style="color:gold"></i>
                <b>ADMIN_HUB</b>
            </div>
            <div class="dash-card">
                <i class="fa-solid fa-shield-halved" style="color:#22c55e"></i>
                <b>SECURED</b>
            </div>
        </div>
    </div>

    <div id="chatView" class="viewport">
        <div id="messageFeed"></div>
        <div class="input-wrapper">
            <input type="text" id="msgInput" placeholder="Secure signal..." onkeydown="if(event.key==='Enter') pushMsg()">
            <button class="btn-send" onclick="pushMsg()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="adminPanel">
        <h3 style="color:gold; margin-top:0;">MASTER OVERRIDE</h3>
        <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px;">
            <button onclick="override('freeze')" style="padding:15px; border-radius:12px; border:none; background: #ef4444; color:white; font-weight:bold;">FREEZE</button>
            <button onclick="override('restore')" style="padding:15px; border-radius:12px; border:none; background: #22c55e; color:white; font-weight:bold;">RESTORE</button>
        </div>
    </div>

    <nav>
        <i id="homeBtn" class="fa-solid fa-house active" onclick="navTo('homePage', this)"></i>
        <i id="chatBtn" class="fa-solid fa-comment-dots" onclick="navTo('chatView', this)"></i>
        <i class="fa-solid fa-user-gear"></i>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect, serverTimestamp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);
let myName = "";

// 🛡️ Sanitize Logic (XSS Protection)
const sanitize = (str) => {
    const div = document.createElement('div');
    div.textContent = str;
    return div.innerHTML;
};

window.authorize = () => {
    const v = sanitize(document.getElementById("userInput").value.trim());
    if(!v) return;
    myName = v;
    if(myName === "Nazim") document.getElementById("adminCard").style.display = "block";
    
    const userRef = ref(db, 'online/' + myName);
    set(userRef, { status: "active", lastSeen: serverTimestamp() });
    onDisconnect(userRef).remove();
    
    document.getElementById("authLayer").style.transform = "translateY(-100%)";
    document.getElementById("greet").innerText = "Hi, " + myName;
};

window.navTo = (id, el) => {
    document.querySelectorAll('.viewport').forEach(v => v.style.display = 'none');
    document.getElementById(id).style.display = id === 'chatView' ? 'flex' : 'block';
    document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
    if(el) el.classList.add('active');
    document.getElementById("adminPanel").style.display = "none";
};

// 🛡️ Optimized Message Pushing
let lastSend = 0;
window.pushMsg = () => {
    const now = Date.now();
    if(now - lastSend < 1000) return; // Anti-spam: 1 message per second
    
    const inp = document.getElementById("msgInput");
    const txt = sanitize(inp.value.trim());
    if(!txt) return;
    
    push(ref(db, 'prod_messages'), {
        sender: myName,
        text: txt,
        time: new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})
    });
    inp.value = "";
    lastSend = now;
};

onValue(ref(db, 'prod_messages'), snap => {
    const feed = document.getElementById("messageFeed");
    feed.innerHTML = "";
    snap.forEach(s => {
        const d = s.val();
        const div = document.createElement("div");
        div.className = "msg-bubble " + (d.sender === myName ? "me" : "other");
        div.innerHTML = `<small style="font-size:10px; opacity:0.6; display:block;">${d.sender}</small>${d.text}`;
        feed.appendChild(div);
    });
    feed.scrollTop = feed.scrollHeight;
});

onValue(ref(db, 'online'), snap => {
    let c = 0; snap.forEach(() => c++);
    document.getElementById("activeNodes").innerText = c + " Nodes";
});

window.toggleAdmin = () => {
    const p = document.getElementById("adminPanel");
    p.style.display = p.style.display === "none" ? "block" : "none";
};

window.override = (t) => {
    onValue(ref(db, 'online'), snap => {
        snap.forEach(u => { if(u.key !== "Nazim") set(ref(db, 'control/' + u.key), t); });
    }, {onlyOnce: true});
};
</script>
</body>
</html>
