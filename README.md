<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Chat | Real-time</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --bg: #000; --card: #121212; --border: #262626; --text: #fff; --sub: #8e8e93; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* --- LOGIN --- */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; padding: 30px; }
        .auth-card { width: 100%; max-width: 350px; text-align: center; }
        .auth-card i { font-size: 50px; color: var(--p); margin-bottom: 20px; }
        .auth-input { width: 100%; padding: 18px; background: #1a1a1a; border: 1px solid var(--border); border-radius: 15px; color: #fff; margin-bottom: 15px; outline: none; text-align: center; font-size: 16px; }
        .btn-join { width: 100%; padding: 18px; background: var(--p); border: none; border-radius: 15px; color: #fff; font-weight: 800; cursor: pointer; }

        /* --- MAIN LAYOUT --- */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; background: #000; position: relative; }
        header { padding: 50px 20px 15px; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--border); background: rgba(0,0,0,0.8); backdrop-filter: blur(10px); }
        header h2 { font-weight: 800; font-size: 22px; margin: 0; }

        .viewport { flex: 1; overflow-y: auto; padding-bottom: 20px; }
        .viewport::-webkit-scrollbar { display: none; }

        /* --- USER LIST --- */
        .user-item { display: flex; align-items: center; gap: 15px; padding: 15px 20px; border-bottom: 1px solid #111; cursor: pointer; transition: 0.2s; }
        .user-item:active { background: #1a1a1a; }
        .user-pfp { width: 50px; height: 50px; border-radius: 50%; background: #222; object-fit: cover; }
        .user-info b { font-size: 16px; }
        .user-info p { margin: 3px 0 0; font-size: 13px; color: var(--sub); }

        /* --- CHAT OVERLAY --- */
        #chatOverlay { position: fixed; inset: 0; background: #000; z-index: 5000; display: none; flex-direction: column; }
        .chat-head { padding: 50px 15px 15px; display: flex; align-items: center; gap: 15px; border-bottom: 1px solid var(--border); background: #000; }
        #chatFlow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 8px; }
        
        .bubble { padding: 10px 16px; border-radius: 18px; max-width: 75%; font-size: 15px; line-height: 1.4; position: relative; }
        .bubble.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 2px; }
        .bubble.them { background: #26262b; align-self: flex-start; border-bottom-left-radius: 2px; }
        .time { font-size: 10px; opacity: 0.6; display: block; margin-top: 4px; text-align: right; }

        /* --- INPUT AREA --- */
        .input-area { padding: 15px 15px 35px; background: #000; display: flex; gap: 10px; align-items: center; border-top: 1px solid var(--border); }
        .msg-input { flex: 1; padding: 14px 20px; background: #1c1c1e; border: none; border-radius: 25px; color: #fff; outline: none; font-size: 15px; }
        .send-btn { width: 45px; height: 45px; background: var(--p); border: none; border-radius: 50%; color: #fff; font-size: 18px; cursor: pointer; display: flex; align-items: center; justify-content: center; }

        /* --- NAV TABS --- */
        nav { display: flex; justify-content: space-around; padding: 15px 0 30px; border-top: 1px solid var(--border); background: #000; }
        .nav-item { color: var(--sub); text-align: center; cursor: pointer; font-size: 12px; font-weight: 600; }
        .nav-item i { font-size: 22px; margin-bottom: 5px; display: block; }
        .nav-item.active { color: var(--p); }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <i class="fa-solid fa-comments"></i>
        <h1>Infinity Chat</h1>
        <input type="text" id="uInp" class="auth-input" placeholder="Enter your name">
        <button onclick="login()" class="btn-join">Start Chatting</button>
    </div>
</div>

<div id="chatOverlay">
    <div class="chat-head">
        <i class="fa-solid fa-arrow-left" onclick="closeChat()" style="font-size: 20px;"></i>
        <img id="activePfp" src="" class="user-pfp" style="width:35px; height:35px;">
        <b id="activeName" style="font-size: 18px;">Chat</b>
    </div>
    <div id="chatFlow"></div>
    <div class="input-area">
        <input type="text" id="msgInp" class="msg-input" placeholder="Type a message...">
        <button onclick="sendMsg()" class="send-btn"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2>Messages</h2>
        <i class="fa-solid fa-user-plus" style="font-size: 20px; color: var(--p);"></i>
    </header>

    <div class="viewport">
        <div id="groupTab" class="user-item" onclick="openChat('Global_Room', 'https://cdn-icons-png.flaticon.com/512/33/33308.png')">
            <div class="user-pfp" style="display:flex; align-items:center; justify-content:center; background:var(--p);"><i class="fa-solid fa-earth-americas"></i></div>
            <div class="user-info">
                <b>Global Public Group</b>
                <p>Chat with everyone online</p>
            </div>
        </div>
        <div style="padding: 15px 20px 5px; font-size: 12px; color: var(--sub); font-weight: 800; letter-spacing: 1px;">ALL USERS</div>
        <div id="userList"></div>
    </div>

    <nav>
        <div class="nav-item active"><i class="fa-solid fa-message"></i>Chats</div>
        <div class="nav-item" onclick="alert('Profile Settings coming soon!')"><i class="fa-solid fa-circle-user"></i>Profile</div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let me = localStorage.getItem("chat_user_name");
let currentChatId = null;

if(me) { document.getElementById("authBox").style.display="none"; init(); }

window.login = () => {
    const n = document.getElementById("uInp").value.trim();
    if(n) { 
        localStorage.setItem("chat_user_name", n); 
        set(ref(db, `users/${n}`), { name: n, online: true });
        location.reload(); 
    }
};

function init() {
    // 1. Load All Users
    onValue(ref(db, 'users'), (snap) => {
        const list = document.getElementById("userList"); list.innerHTML = "";
        snap.forEach(s => {
            if(s.key !== me) {
                list.innerHTML += `
                <div class="user-item" onclick="openChat('${s.key}', 'https://cdn-icons-png.flaticon.com/512/149/149071.png')">
                    <img src="https://cdn-icons-png.flaticon.com/512/149/149071.png" class="user-pfp">
                    <div class="user-info"><b>${s.key}</b><p>Tap to message</p></div>
                </div>`;
            }
        });
    });
}

window.openChat = (target, pfp) => {
    currentChatId = target === 'Global_Room' ? 'Global_Room' : [me, target].sort().join("_");
    document.getElementById("activeName").innerText = target;
    document.getElementById("activePfp").src = pfp || "https://cdn-icons-png.flaticon.com/512/149/149071.png";
    document.getElementById("chatOverlay").style.display = "flex";

    onValue(ref(db, `chats/${currentChatId}`), (snap) => {
        const flow = document.getElementById("chatFlow"); flow.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.sender === me;
            flow.innerHTML += `
                <div class="bubble ${isMe?'me':'them'}">
                    <small style="font-size:10px; opacity:0.7; font-weight:800;">${m.sender}</small><br>
                    ${m.text}
                    <span class="time">${new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
                </div>`;
        });
        flow.scrollTop = flow.scrollHeight;
    });
};

window.sendMsg = () => {
    const i = document.getElementById("msgInp");
    if(i.value.trim() && currentChatId) {
        push(ref(db, `chats/${currentChatId}`), { sender: me, text: i.value });
        i.value = "";
    }
};

window.closeChat = () => { 
    document.getElementById("chatOverlay").style.display = "none"; 
    currentChatId = null;
};

// Handle "Enter" key for sending
document.getElementById("msgInp")?.addEventListener("keypress", (e) => {
    if(e.key === "Enter") sendMsg();
});
</script>
</body>
</html>
