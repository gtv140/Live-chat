<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Ultra Pro</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --accent: #6366f1; --danger: #ef4444; --bg: #0f172a;
            --surface: #1e293b; --text: #f8fafc; --glass: rgba(255, 255, 255, 0.03);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Inter', system-ui, sans-serif; }
        body { margin: 0; background: var(--bg); color: var(--text); height: 100vh; overflow: hidden; }

        /* Auth Screen */
        #authScreen {
            position: fixed; inset: 0; background: radial-gradient(circle at top right, #1e1b4b, #0f172a);
            z-index: 5000; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 25px;
        }
        .login-box { width: 100%; max-width: 380px; text-align: center; }
        .login-box i { font-size: 60px; color: var(--accent); margin-bottom: 20px; filter: drop-shadow(0 0 15px var(--accent)); }
        .login-box input { 
            width: 100%; padding: 18px; border-radius: 16px; border: 1px solid rgba(255,255,255,0.1); 
            background: var(--glass); color: white; margin-bottom: 20px; font-size: 16px; text-align: center;
        }
        .btn-action { 
            width: 100%; padding: 18px; border-radius: 16px; border: none; 
            background: var(--accent); color: white; font-weight: 700; cursor: pointer; box-shadow: 0 10px 25px -5px var(--accent);
        }

        /* Main Shell */
        .app-shell { height: 100vh; display: flex; flex-direction: column; max-width: 500px; margin: 0 auto; background: var(--bg); border-left: 1px solid rgba(255,255,255,0.05); border-right: 1px solid rgba(255,255,255,0.05); }
        
        header { padding: 20px; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid rgba(255,255,255,0.05); }
        header h1 { margin: 0; font-size: 18px; letter-spacing: 1px; font-weight: 900; color: var(--accent); }

        /* Pages */
        .viewport { flex: 1; overflow-y: auto; padding: 20px; position: relative; }
        .page { display: none; }
        .page.active { display: block; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* Dashboard Grid */
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .grid-card { 
            background: var(--surface); padding: 25px 15px; border-radius: 24px; text-align: center; 
            border: 1px solid rgba(255,255,255,0.05); transition: 0.2s; cursor: pointer;
        }
        .grid-card:active { transform: scale(0.95); background: rgba(255,255,255,0.05); }
        .grid-card i { font-size: 28px; color: var(--accent); margin-bottom: 12px; display: block; }
        .grid-card b { font-size: 14px; color: #cbd5e1; }

        /* Chat Pro UI */
        #chatFlow { height: calc(100vh - 250px); overflow-y: auto; padding: 10px; display: flex; flex-direction: column; gap: 12px; }
        .bubble { max-width: 85%; padding: 12px 16px; border-radius: 22px; font-size: 14px; line-height: 1.5; position: relative; }
        .bubble.me { align-self: flex-end; background: var(--accent); color: white; border-bottom-right-radius: 4px; }
        .bubble.other { align-self: flex-start; background: var(--surface); color: #e2e8f0; border-bottom-left-radius: 4px; }
        .bubble small { display: block; font-size: 10px; font-weight: bold; margin-bottom: 4px; opacity: 0.7; }
        
        .typing-hint { height: 20px; font-size: 11px; color: var(--accent); padding-left: 15px; font-style: italic; }

        .input-box { 
            padding: 15px; background: var(--bg); display: flex; gap: 10px; align-items: center; 
            border-top: 1px solid rgba(255,255,255,0.05);
        }
        .input-box input { 
            flex: 1; padding: 14px 20px; border-radius: 18px; border: 1px solid rgba(255,255,255,0.1); 
            background: var(--surface); color: white; outline: none;
        }
        .btn-send { width: 50px; height: 50px; border-radius: 18px; border: none; background: var(--accent); color: white; font-size: 20px; }

        /* Bottom TabBar */
        nav { height: 75px; background: var(--surface); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid rgba(255,255,255,0.05); }
        nav button { background: none; border: none; color: #64748b; font-size: 22px; cursor: pointer; }
        nav button.active { color: var(--accent); }

        /* Admin Secret Panel */
        #adminHUD {
            display: none; position: fixed; bottom: 85px; left: 10px; right: 10px; 
            background: #000; padding: 20px; border-radius: 25px; border: 1px solid var(--danger); z-index: 1000;
        }
    </style>
</head>
<body>

<div id="authScreen">
    <div class="login-box">
        <i class="fa-solid fa-shield-cat"></i>
        <h2>LIVE_CONNECT</h2>
        <p style="opacity:0.5; font-size:13px; margin-bottom:25px;">Initializing Secure Protocol v15.0</p>
        <input type="text" id="userInput" placeholder="Enter Codename">
        <button class="btn-action" onclick="login()">AUTHORIZE_</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h1>CORE_SYSTEM</h1>
        <i class="fa-solid fa-power-off" onclick="location.reload()" style="color:var(--danger); cursor:pointer;"></i>
    </header>

    <div class="viewport">
        <div id="homePage" class="page active">
            <h2 id="userName" style="margin-top:0;">Hello, User</h2>
            <div class="grid">
                <div class="grid-card" onclick="switchPage('chatPage', 'chatBtn')">
                    <i class="fa-solid fa-tower-broadcast"></i>
                    <b>Global Channel</b>
                </div>
                <div class="grid-card">
                    <i class="fa-solid fa-microchip"></i>
                    <b id="onlineStat">Nodes: 0</b>
                </div>
                <div id="adminCard" class="grid-card" style="display:none; border-color:gold;" onclick="toggleHUD()">
                    <i class="fa-solid fa-user-ninja" style="color:gold"></i>
                    <b>Admin HUD</b>
                </div>
                <div class="grid-card">
                    <i class="fa-solid fa-vault"></i>
                    <b>Encrypted</b>
                </div>
            </div>
        </div>

        <div id="chatPage" class="page">
            <div id="chatFlow"></div>
            <div class="typing-hint" id="typingLabel"></div>
            <div class="input-box">
                <input type="text" id="messageInp" placeholder="Send signal..." oninput="isTyping()">
                <button class="btn-send" onclick="sendMessage()"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <div id="adminHUD">
        <h3 style="margin:0 0 15px; color:var(--danger); font-size:14px;">MASTER_OVERRIDE</h3>
        <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px;">
            <button onclick="control('freeze')" style="padding:15px; background:var(--danger); color:white; border:none; border-radius:12px; font-weight:bold;">FREEZE</button>
            <button onclick="control('restore')" style="padding:15px; background:#22c55e; color:white; border:none; border-radius:12px; font-weight:bold;">RESTORE</button>
        </div>
    </div>

    <nav>
        <button id="homeBtn" onclick="switchPage('homePage', this)" class="active"><i class="fa-solid fa-ghost"></i></button>
        <button id="chatBtn" onclick="switchPage('chatPage', this)"><i class="fa-solid fa-comment-slash"></i></button>
        <button onclick="alert('Module Encrypted')"><i class="fa-solid fa-skull"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
let myCodename = "";

window.login = () => {
    const val = document.getElementById("userInput").value.trim();
    if(!val) return;
    myCodename = val;
    document.getElementById("userName").innerText = "Welcome, " + myCodename;
    if(myCodename === "Nazim") document.getElementById("adminCard").style.display = "block";

    const statusRef = ref(db, 'online_status/' + myCodename);
    set(statusRef, true);
    onDisconnect(statusRef).remove();

    document.getElementById("authScreen").style.display = "none";
};

window.switchPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    if(typeof btn === 'string') document.getElementById(btn).classList.add('active');
    else btn.classList.add('active');
    document.getElementById("adminHUD").style.display = "none";
};

// Real-time Dashboard Stats
onValue(ref(db, 'online_status'), (snapshot) => {
    let count = 0; snapshot.forEach(() => count++);
    document.getElementById("onlineStat").innerText = "Nodes: " + count;
});

// Advanced Chat System
window.sendMessage = () => {
    const input = document.getElementById("messageInp");
    if(!input.value) return;
    push(ref(db, 'v15_messages'), {
        user: myCodename,
        text: input.value,
        stamp: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})
    });
    input.value = "";
    set(ref(db, 'is_typing/' + myCodename), false);
};

onValue(ref(db, 'v15_messages'), (snapshot) => {
    const container = document.getElementById("chatFlow");
    container.innerHTML = "";
    snapshot.forEach((msg) => {
        const data = msg.val();
        const div = document.createElement("div");
        div.className = "bubble " + (data.user === myCodename ? "me" : "other");
        div.innerHTML = `<small>${data.user}</small>${data.text}<div style="font-size:8px; opacity:0.5; margin-top:5px; text-align:right;">${data.stamp}</div>`;
        container.appendChild(div);
    });
    container.scrollTop = container.scrollHeight;
});

// Typing Logic
window.isTyping = () => {
    set(ref(db, 'is_typing/' + myCodename), true);
    setTimeout(() => set(ref(db, 'is_typing/' + myCodename), false), 3000);
};

onValue(ref(db, 'is_typing'), (snapshot) => {
    let tUser = "";
    snapshot.forEach((u) => { if(u.key !== myCodename && u.val()) tUser = u.key; });
    document.getElementById("typingLabel").innerText = tUser ? tUser + " is encrypting message..." : "";
});

// Admin Controls
window.toggleHUD = () => {
    const hud = document.getElementById("adminHUD");
    hud.style.display = hud.style.display === "none" ? "block" : "none";
};

window.control = (type) => {
    onValue(ref(db, 'online_status'), (snap) => {
        snap.forEach((u) => { if(u.key !== "Nazim") set(ref(db, 'traps/' + u.key), type); });
    }, {onlyOnce: true});
};
</script>
</body>
</html>
