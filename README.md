<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Infinity Nexus | Global</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #00f2ff; --s: #7000ff; --bg: #080808; --glass: rgba(255,255,255,0.06); --border: rgba(255,255,255,0.1); }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Outfit', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: #fff; overflow: hidden; }

        /* --- LUXURY LOGIN --- */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 30px; }
        .auth-card { width: 100%; max-width: 360px; text-align: center; }
        .auth-logo { font-size: 60px; margin-bottom: 10px; background: linear-gradient(45deg, var(--p), var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }
        .auth-input { width: 100%; padding: 20px; background: #111; border: 1px solid var(--border); border-radius: 20px; color: #fff; margin: 20px 0; outline: none; font-size: 16px; text-align: center; }
        .btn-glow { width: 100%; padding: 20px; background: linear-gradient(45deg, var(--s), var(--p)); border: none; border-radius: 20px; color: #fff; font-weight: 800; cursor: pointer; box-shadow: 0 10px 30px rgba(112,0,255,0.4); }

        /* --- APP SHELL --- */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; background: radial-gradient(circle at top, #111, #000); }
        header { padding: 50px 20px 15px; display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.7); backdrop-filter: blur(20px); border-bottom: 1px solid var(--border); }
        header h2 { font-weight: 800; font-size: 26px; letter-spacing: -1px; margin: 0; }

        .viewport { flex: 1; overflow-y: scroll; padding-bottom: 110px; scroll-behavior: smooth; }
        .viewport::-webkit-scrollbar { display: none; }

        /* --- CONTACT TILES --- */
        .contact-card { margin: 8px 15px; padding: 15px; background: var(--glass); border: 1px solid var(--border); border-radius: 22px; display: flex; align-items: center; gap: 15px; transition: 0.3s; }
        .contact-card:active { transform: scale(0.96); background: rgba(255,255,255,0.1); }
        .avatar-ring { width: 55px; height: 55px; border-radius: 50%; padding: 2px; background: linear-gradient(45deg, #f09433, #bc1888); }
        .avatar-img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; border: 2px solid #000; }
        .contact-info b { font-size: 17px; display: block; }
        .contact-info p { margin: 3px 0 0; font-size: 13px; color: #888; }

        /* --- CHAT OVERLAY --- */
        #chatView { position: fixed; inset: 0; background: #000; z-index: 5000; display: none; flex-direction: column; animation: slideIn 0.3s cubic-bezier(0.1, 0.7, 0.1, 1); }
        .chat-header { padding: 50px 20px 15px; display: flex; align-items: center; gap: 15px; border-bottom: 1px solid var(--border); background: rgba(0,0,0,0.9); }
        #msgFlow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 12px; background: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png'); background-opacity: 0.05; }

        .bubble { padding: 12px 18px; border-radius: 22px; max-width: 80%; font-size: 15px; line-height: 1.5; position: relative; animation: pop 0.2s ease; }
        .bubble.me { background: var(--s); align-self: flex-end; border-bottom-right-radius: 4px; box-shadow: 0 5px 15px rgba(112,0,255,0.3); }
        .bubble.them { background: #222; align-self: flex-start; border-bottom-left-radius: 4px; border: 1px solid var(--border); }
        .bubble small { font-size: 10px; opacity: 0.6; display: block; margin-top: 5px; text-align: right; }

        /* --- INPUT BAR --- */
        .action-bar { padding: 15px 15px 40px; background: #000; display: flex; align-items: center; gap: 10px; border-top: 1px solid var(--border); }
        .input-box { flex: 1; padding: 15px 20px; background: #1a1a1a; border: 1px solid var(--border); border-radius: 30px; color: #fff; outline: none; }
        .btn-circle { width: 50px; height: 50px; border-radius: 50%; background: var(--p); border: none; color: #000; font-size: 20px; display: flex; align-items: center; justify-content: center; box-shadow: 0 0 15px var(--p); }

        /* --- TAB NAV --- */
        nav { position: absolute; bottom: 20px; left: 50%; transform: translateX(-50%); width: 90%; height: 70px; background: rgba(20,20,20,0.8); backdrop-filter: blur(20px); border-radius: 35px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        .nav-tab { color: #555; font-size: 22px; transition: 0.3s; }
        .nav-tab.active { color: var(--p); transform: translateY(-3px); }

        @keyframes slideIn { from { transform: translateX(100%); } to { transform: translateX(0); } }
        @keyframes pop { from { transform: scale(0.9); opacity: 0; } to { transform: scale(1); opacity: 1; } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <div class="auth-logo">INFINITY</div>
        <p style="color: #666; letter-spacing: 2px;">NEXT-GEN MESSENGER</p>
        <input type="text" id="uInp" class="auth-input" placeholder="Enter Username">
        <button onclick="login()" class="btn-glow">JOIN THE NEXUS</button>
    </div>
</div>

<div id="chatView">
    <div class="chat-header">
        <i class="fa-solid fa-arrow-left" onclick="closeChat()"></i>
        <div class="avatar-ring" style="width:40px; height:40px;"><img id="activeImg" src="" class="avatar-img"></div>
        <b id="activeTitle">User</b>
    </div>
    <div id="msgFlow"></div>
    <div class="action-bar">
        <label for="imgSend"><i class="fa-solid fa-circle-plus" style="font-size: 24px; color: #666;"></i></label>
        <input type="file" id="imgSend" hidden accept="image/*" onchange="sendImg(event)">
        <input type="text" id="mInp" class="input-box" placeholder="Message...">
        <button onclick="send()" class="btn-circle"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2>Nexus</h2>
        <div style="display:flex; gap:20px; font-size:20px;">
            <i class="fa-solid fa-magnifying-glass"></i>
            <i class="fa-solid fa-ellipsis-vertical"></i>
        </div>
    </header>

    <div class="viewport">
        <div class="contact-card" onclick="openChat('Global Lounge', 'https://cdn-icons-png.flaticon.com/512/33/33308.png', true)">
            <div class="avatar-ring" style="background: linear-gradient(45deg, var(--p), var(--s));">
                <div class="avatar-img" style="display:flex; align-items:center; justify-content:center; background:#000;"><i class="fa-solid fa-globe" style="color:var(--p)"></i></div>
            </div>
            <div class="contact-info">
                <b>Global Lounge</b>
                <p>Chat with the whole world</p>
            </div>
            <span style="background:var(--p); color:#000; font-size:10px; font-weight:800; padding:2px 8px; border-radius:10px;">ACTIVE</span>
        </div>

        <div style="padding: 20px 20px 10px; font-size: 11px; color: #444; font-weight: 800; letter-spacing: 1px;">DIRECT MESSAGES</div>
        <div id="userList"></div>
    </div>

    <nav>
        <div class="nav-tab active"><i class="fa-solid fa-comment-dots"></i></div>
        <div class="nav-tab"><i class="fa-solid fa-user-group"></i></div>
        <div class="nav-tab" onclick="location.reload()"><i class="fa-solid fa-circle-user"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let me = localStorage.getItem("nexus_user");
let room = null;

if(me) { document.getElementById("authBox").style.display="none"; start(); }

window.login = () => {
    const n = document.getElementById("uInp").value.trim();
    if(n) { 
        localStorage.setItem("nexus_user", n); 
        set(ref(db, `nexus_users/${n}`), { name: n, pfp: 'https://cdn-icons-png.flaticon.com/512/149/149071.png' });
        location.reload(); 
    }
};

function start() {
    onValue(ref(db, 'nexus_users'), (snap) => {
        const list = document.getElementById("userList"); list.innerHTML = "";
        snap.forEach(s => {
            if(s.key !== me) {
                list.innerHTML += `
                <div class="contact-card" onclick="openChat('${s.key}', '${s.val().pfp}')">
                    <div class="avatar-ring"><img src="${s.val().pfp}" class="avatar-img"></div>
                    <div class="contact-info"><b>${s.key}</b><p>Available for chat</p></div>
                </div>`;
            }
        });
    });
}

window.openChat = (target, pfp, isGroup = false) => {
    room = isGroup ? target : [me, target].sort().join("_");
    document.getElementById("activeTitle").innerText = target;
    document.getElementById("activeImg").src = pfp;
    document.getElementById("chatView").style.display = "flex";

    onValue(ref(db, `nexus_rooms/${room}`), (snap) => {
        const flow = document.getElementById("msgFlow"); flow.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.sender === me;
            flow.innerHTML += `
                <div class="bubble ${isMe?'me':'them'}">
                    ${isGroup && !isMe ? `<small style="color:var(--p); font-weight:800;">${m.sender}</small>` : ''}
                    ${m.img ? `<img src="${m.img}" style="width:100%; border-radius:12px; margin-bottom:5px;">` : m.text}
                    <small>${new Date(m.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</small>
                </div>`;
        });
        flow.scrollTop = flow.scrollHeight;
    });
};

window.send = () => {
    const i = document.getElementById("mInp");
    if(i.value.trim()) {
        push(ref(db, `nexus_rooms/${room}`), { sender: me, text: i.value, time: Date.now() });
        i.value = "";
    }
};

window.sendImg = (e) => {
    const reader = new FileReader();
    reader.onload = () => push(ref(db, `nexus_rooms/${room}`), { sender: me, img: reader.result, time: Date.now() });
    reader.readAsDataURL(e.target.files[0]);
};

window.closeChat = () => document.getElementById("chatView").style.display = "none";
document.getElementById("mInp").addEventListener("keypress", (e) => { if(e.key === "Enter") send(); });
</script>
</body>
</html>
