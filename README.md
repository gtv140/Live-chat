<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Infinity Chat Pro</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;500;700;800&display=swap" rel="stylesheet">
    <style>
        :root { --primary: #007AFF; --bg: #000000; --card: #121212; --border: #262626; --text: #ffffff; --sub: #8E8E93; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* --- AUTH SCREEN --- */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; padding: 30px; }
        .auth-card { width: 100%; max-width: 380px; text-align: center; }
        .logo-icon { width: 70px; height: 70px; background: var(--primary); border-radius: 20px; display: flex; align-items: center; justify-content: center; font-size: 35px; margin: 0 auto 20px; box-shadow: 0 15px 30px rgba(0,122,255,0.4); }
        .auth-input { width: 100%; padding: 20px; background: #1c1c1e; border: 1px solid var(--border); border-radius: 18px; color: #fff; margin-bottom: 15px; outline: none; text-align: center; font-size: 16px; }
        .btn-start { width: 100%; padding: 18px; background: var(--primary); border: none; border-radius: 18px; color: #fff; font-weight: 800; cursor: pointer; transition: 0.3s; }

        /* --- APP SHELL --- */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
        header { padding: 50px 20px 15px; background: rgba(0,0,0,0.85); backdrop-filter: blur(20px); border-bottom: 0.5px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 100; }
        header h1 { font-size: 24px; font-weight: 800; letter-spacing: -1px; margin: 0; }

        .viewport { flex: 1; overflow-y: auto; padding-bottom: 100px; -webkit-overflow-scrolling: touch; }
        .viewport::-webkit-scrollbar { display: none; }

        /* --- CHAT LIST ITEM --- */
        .user-row { display: flex; align-items: center; gap: 15px; padding: 15px 20px; border-bottom: 0.5px solid #111; cursor: pointer; transition: 0.2s; }
        .user-row:active { background: #1a1a1a; }
        .u-pfp { width: 55px; height: 55px; border-radius: 50%; object-fit: cover; background: #222; border: 2px solid var(--border); }
        .u-meta { flex: 1; }
        .u-meta b { font-size: 16px; display: block; margin-bottom: 2px; }
        .u-meta p { margin: 0; font-size: 13px; color: var(--sub); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }

        /* --- PRIVATE CHAT OVERLAY --- */
        #chatRoom { position: fixed; inset: 0; background: #000; z-index: 5000; display: none; flex-direction: column; animation: slideUp 0.3s ease-out; }
        .chat-nav { padding: 50px 15px 15px; display: flex; align-items: center; gap: 12px; border-bottom: 0.5px solid var(--border); background: #000; }
        .chat-body { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 10px; background: #0a0a0a; }

        .msg-bubble { padding: 12px 16px; border-radius: 20px; max-width: 80%; font-size: 15px; line-height: 1.4; position: relative; }
        .msg-bubble.me { background: var(--primary); align-self: flex-end; border-bottom-right-radius: 4px; }
        .msg-bubble.them { background: #26262b; align-self: flex-start; border-bottom-left-radius: 4px; }
        .msg-time { font-size: 10px; opacity: 0.6; display: block; text-align: right; margin-top: 5px; }
        .msg-img { width: 100%; border-radius: 12px; margin-top: 5px; }

        /* --- INPUT DOCK --- */
        .input-dock { padding: 15px 15px 35px; background: #000; display: flex; align-items: center; gap: 10px; border-top: 0.5px solid var(--border); }
        .main-input { flex: 1; padding: 14px 20px; background: #1c1c1e; border: none; border-radius: 25px; color: #fff; outline: none; font-size: 15px; }
        .action-btn { width: 48px; height: 48px; border-radius: 50%; border: none; background: var(--primary); color: #fff; font-size: 18px; display: flex; align-items: center; justify-content: center; cursor: pointer; }

        /* --- NAV BAR --- */
        nav { position: absolute; bottom: 0; width: 100%; height: 85px; background: rgba(0,0,0,0.9); border-top: 0.5px solid var(--border); display: flex; justify-content: space-around; padding-top: 10px; z-index: 1000; }
        .nav-link { display: flex; flex-direction: column; align-items: center; color: var(--sub); cursor: pointer; font-size: 11px; font-weight: 600; gap: 4px; }
        .nav-link.active { color: var(--primary); }
        .nav-link i { font-size: 24px; }

        @keyframes slideUp { from { transform: translateY(100%); } to { transform: translateY(0); } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <div class="logo-icon"><i class="fa-solid fa-bolt"></i></div>
        <h1 style="font-size: 32px; margin-bottom: 5px;">Infinity Pro</h1>
        <p style="color: var(--sub); margin-bottom: 30px;">Premium Real-time Messaging</p>
        <input type="text" id="uName" class="auth-input" placeholder="What's your name?">
        <button onclick="join()" class="btn-start">Connect Now</button>
    </div>
</div>

<div id="chatRoom">
    <div class="chat-nav">
        <i class="fa-solid fa-chevron-left" onclick="closeChat()" style="font-size: 22px; padding: 5px;"></i>
        <img id="activePfp" src="" class="u-pfp" style="width:40px; height:40px;">
        <div>
            <b id="activeTitle" style="font-size: 17px; display: block;">Name</b>
            <small style="color: var(--primary); font-weight: 700;">● Online</small>
        </div>
    </div>
    <div class="chat-body" id="chatFlow"></div>
    <div class="input-dock">
        <label for="mediaFile"><i class="fa-solid fa-paperclip" style="color: var(--sub); font-size: 20px; padding: 10px;"></i></label>
        <input type="file" id="mediaFile" hidden accept="image/*" onchange="sendImage(event)">
        <input type="text" id="msgInp" class="main-input" placeholder="Type a message...">
        <button onclick="send()" class="action-btn"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h1>Infinity</h1>
        <div style="display:flex; gap:20px; font-size:20px;">
            <i class="fa-solid fa-camera"></i>
            <i class="fa-solid fa-pen-to-square"></i>
        </div>
    </header>

    <div class="viewport">
        <div class="user-row" onclick="openChat('Global_Lounge', 'https://cdn-icons-png.flaticon.com/512/33/33308.png', true)">
            <div class="u-pfp" style="background: var(--primary); display:flex; align-items:center; justify-content:center; border:none;"><i class="fa-solid fa-users"></i></div>
            <div class="u-meta">
                <b>Global Lounge</b>
                <p>Public group for everyone...</p>
            </div>
            <small style="color: var(--sub);">Online</small>
        </div>

        <div style="padding: 15px 20px 5px; font-size: 11px; color: var(--sub); font-weight: 800; letter-spacing: 1px;">DIRECT MESSAGES</div>
        <div id="contactsList"></div>
    </div>

    <nav>
        <div class="nav-link active"><i class="fa-solid fa-comment-dots"></i><span>Chats</span></div>
        <div class="nav-link"><i class="fa-solid fa-compass"></i><span>Explore</span></div>
        <div class="nav-link" onclick="location.reload()"><i class="fa-solid fa-circle-user"></i><span>Profile</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let me = localStorage.getItem("pro_chat_user");
let currentRoom = null;

if(me) { document.getElementById("authBox").style.display="none"; init(); }

window.join = () => {
    const n = document.getElementById("uName").value.trim();
    if(n) { 
        localStorage.setItem("pro_chat_user", n); 
        set(ref(db, `pro_users/${n}`), { name: n, lastSeen: Date.now() });
        location.reload(); 
    }
};

function init() {
    // Sync Users
    onValue(ref(db, 'pro_users'), (snap) => {
        const list = document.getElementById("contactsList"); list.innerHTML = "";
        snap.forEach(s => {
            if(s.key !== me) {
                list.innerHTML += `
                <div class="user-row" onclick="openChat('${s.key}', 'https://cdn-icons-png.flaticon.com/512/149/149071.png')">
                    <img src="https://cdn-icons-png.flaticon.com/512/149/149071.png" class="u-pfp">
                    <div class="u-meta"><b>${s.key}</b><p>Active now</p></div>
                </div>`;
            }
        });
    });
}

window.openChat = (target, pfp, isGroup = false) => {
    currentRoom = isGroup ? target : [me, target].sort().join("_");
    document.getElementById("activeTitle").innerText = target;
    document.getElementById("activePfp").src = pfp;
    document.getElementById("chatRoom").style.display = "flex";

    onValue(ref(db, `pro_rooms/${currentRoom}`), (snap) => {
        const flow = document.getElementById("chatFlow"); flow.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.sender === me;
            const time = new Date(m.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
            flow.innerHTML += `
                <div class="msg-bubble ${isMe?'me':'them'}">
                    ${isGroup && !isMe ? `<small style="font-weight:700; color:var(--primary)">${m.sender}</small><br>` : ''}
                    ${m.img ? `<img src="${m.img}" class="msg-img">` : m.text}
                    <span class="msg-time">${time} ${isMe?'✓':''}</span>
                </div>`;
        });
        flow.scrollTop = flow.scrollHeight;
    });
};

window.send = () => {
    const i = document.getElementById("msgInp");
    if(i.value.trim() && currentRoom) {
        push(ref(db, `pro_rooms/${currentRoom}`), { sender: me, text: i.value, time: Date.now() });
        i.value = "";
    }
};

window.sendImage = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    reader.onload = () => {
        push(ref(db, `pro_rooms/${currentRoom}`), { sender: me, img: reader.result, time: Date.now() });
    };
    reader.readAsDataURL(file);
};

window.closeChat = () => { document.getElementById("chatRoom").style.display = "none"; currentRoom = null; };

document.getElementById("msgInp").addEventListener("keypress", (e) => { if(e.key === "Enter") send(); });
</script>
</body>
</html>
