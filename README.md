<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Modern</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        /* Pehle wala saara CSS aur ye naye styles */
        :root { --bg: #0a0e17; --glass: rgba(22, 27, 34, 0.8); --primary: #58a6ff; --text: #e6edf3; }
        body { margin: 0; font-family: sans-serif; background: var(--bg); color: var(--text); overflow: hidden; }
        .app-container { max-width: 500px; margin: auto; height: 100vh; position: relative; display: flex; flex-direction: column; }
        
        header { padding: 20px; background: var(--glass); border-bottom: 1px solid rgba(255,255,255,0.1); display: flex; justify-content: space-between; }
        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 150px; }
        .page.active { display: block; }
        
        /* Message & Reaction Styles */
        .msg { position: relative; max-width: 80%; padding: 12px; border-radius: 18px; margin-bottom: 15px; cursor: pointer; }
        .msg.me { align-self: flex-end; background: var(--primary); margin-left: auto; }
        .msg.other { background: var(--glass); border: 1px solid rgba(255,255,255,0.1); }
        .reactions-display { position: absolute; bottom: -12px; right: 10px; background: #21262d; border-radius: 10px; padding: 2px 6px; font-size: 12px; border: 1px solid rgba(255,255,255,0.1); }
        
        .reaction-menu { display: none; position: absolute; top: -40px; background: #161b22; border-radius: 20px; padding: 5px; box-shadow: 0 5px 15px rgba(0,0,0,0.5); z-index: 100; gap: 8px; border: 1px solid var(--primary); }
        .reaction-menu span { cursor: pointer; font-size: 18px; transition: 0.2s; }
        .reaction-menu span:hover { transform: scale(1.3); }

        #typing-status { font-size: 12px; color: var(--primary); height: 15px; margin-bottom: 5px; font-style: italic; }

        .input-wrapper { position: absolute; bottom: 85px; width: 100%; padding: 0 15px; box-sizing: border-box; }
        .modern-input { background: var(--glass); border-radius: 30px; padding: 8px 15px; display: flex; align-items: center; gap: 10px; border: 1px solid rgba(255,255,255,0.1); }
        .modern-input input { flex: 1; background: transparent; border: none; color: white; outline: none; }
        nav { position: absolute; bottom: 0; width: 100%; height: 70px; background: var(--glass); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid rgba(255,255,255,0.1); }
        nav i { font-size: 22px; color: #8b949e; cursor: pointer; }
        nav i.active { color: var(--primary); }
    </style>
</head>
<body>

<div class="app-container">
    <header><h1>Live Connect</h1></header>

    <div id="loginPage" class="page active">
        <input type="text" id="usernameInput" placeholder="Enter Name..." style="padding:10px; width:80%; margin:20px;">
        <button onclick="login()" style="padding:10px 20px; background:var(--primary); color:white; border:none; border-radius:5px;">Login</button>
    </div>

    <div id="chat" class="page">
        <div id="chatBox" style="display:flex; flex-direction:column;"></div>
        <div class="input-wrapper">
            <div id="typing-status"></div>
            <div class="modern-input">
                <input type="text" id="msgInput" placeholder="Message..." oninput="handleTyping()">
                <button onclick="sendMsg()" style="background:none; border:none; color:var(--primary); font-size:20px;"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <nav id="navbar" style="display:none">
        <i class="fa-solid fa-house" onclick="openPage('home', this)"></i>
        <i class="fa-solid fa-comment active" onclick="openPage('chat', this)"></i>
        <i class="fa-solid fa-user-group" onclick="openPage('users', this)"></i>
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
let curChat = "Global_Group"; // Default group
let typingTimeout;

window.login = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return;
    currentUser = val;
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("chat").classList.add("active");
    document.getElementById("navbar").style.display = "flex";
    loadChat();
};

// Typing Indicator Logic
window.handleTyping = () => {
    set(ref(db, `typing/${curChat}/${currentUser}`), true);
    clearTimeout(typingTimeout);
    typingTimeout = setTimeout(() => {
        set(ref(db, `typing/${curChat}/${currentUser}`), null);
    }, 2000);
};

function loadChat() {
    const chatRef = ref(db, `chats/${curChat}`);
    const typingRef = ref(db, `typing/${curChat}`);

    // Listen for Typing
    onValue(typingRef, snap => {
        const users = snap.val() || {};
        const typers = Object.keys(users).filter(u => u !== currentUser);
        document.getElementById("typing-status").innerText = typers.length ? `${typers.join(', ')} typing...` : "";
    });

    // Listen for Messages
    onValue(chatRef, snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const data = m.val();
            const isMe = data.from === currentUser;
            const msgId = m.key;
            
            const div = document.createElement("div");
            div.className = `msg ${isMe ? 'me' : 'other'}`;
            div.innerHTML = `
                <b>${data.from}</b>
                <div>${data.text}</div>
                <div class="reactions-display">${data.reaction || ""}</div>
                <div class="reaction-menu" id="menu-${msgId}">
                    <span onclick="react('${msgId}','❤️')">❤️</span>
                    <span onclick="react('${msgId}','😂')">😂</span>
                    <span onclick="react('${msgId}','👍')">👍</span>
                    <span onclick="react('${msgId}','🔥')">🔥</span>
                </div>
            `;
            div.onclick = () => {
                const menu = document.getElementById(`menu-${msgId}`);
                menu.style.display = menu.style.display === 'flex' ? 'none' : 'flex';
            };
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.react = (msgId, emoji) => {
    set(ref(db, `chats/${curChat}/${msgId}/reaction`), emoji);
};

window.sendMsg = () => {
    const inp = document.getElementById("msgInput");
    if(!inp.value) return;
    push(ref(db, `chats/${curChat}`), { from: currentUser, text: inp.value });
    inp.value = "";
    set(ref(db, `typing/${curChat}/${currentUser}`), null);
};

window.openPage = (id, icon) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
    icon.classList.add('active');
};
</script>
</body>
</html>
