<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Apex | Secure</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #00e5ff; --s: #0077ff; --bg: #030508; --card: rgba(22, 28, 38, 0.95); --border: rgba(0, 229, 255, 0.2); }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: #fff; overflow: hidden; }

        /* LOGIN MODAL */
        #loginOverlay { position: fixed; inset: 0; background: var(--bg); z-index: 5000; display: flex; align-items: center; justify-content: center; padding: 20px; }
        .login-box { background: var(--card); padding: 30px; border-radius: 25px; border: 1px solid var(--border); width: 100%; max-width: 350px; text-align: center; box-shadow: 0 0 30px rgba(0,229,255,0.2); }
        .login-box h2 { color: var(--p); margin-bottom: 20px; }
        .login-box input { width: 100%; padding: 12px; border-radius: 10px; border: 1px solid var(--border); background: rgba(0,0,0,0.3); color: #fff; margin-bottom: 20px; outline: none; }
        .login-box button { background: var(--p); color: #000; border: none; padding: 12px 30px; border-radius: 10px; font-weight: bold; cursor: pointer; width: 100%; }

        /* MAIN APP */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
        header { padding: 15px 20px; background: #030508; border-bottom: 1px solid var(--border); text-align: center; }
        header h1 { font-size: 18px; font-weight: 800; color: var(--p); margin: 0; text-shadow: 0 0 10px var(--p); }

        .viewport { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 160px; scroll-behavior: smooth; }
        #chatBox { display: flex; flex-direction: column; gap: 15px; }

        .msg-node { display: flex; flex-direction: column; width: 100%; transition: 0.3s; }
        .msg-node.me { align-items: flex-end; }
        .msg-node.them { align-items: flex-start; }

        .bubble { 
            padding: 12px 16px; border-radius: 20px; font-size: 15px; max-width: 80%; 
            position: relative; box-shadow: 0 4px 15px rgba(0,0,0,0.3); word-wrap: break-word;
            user-select: none; transition: transform 0.1s;
        }
        .bubble:active { transform: scale(0.95); }
        .me .bubble { background: linear-gradient(135deg, var(--p), var(--s)); color: #000; border-bottom-right-radius: 4px; font-weight: 600; }
        .them .bubble { background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        
        .reaction-chip { position: absolute; bottom: -10px; background: #1a212c; border: 1px solid var(--p); border-radius: 12px; padding: 2px 6px; font-size: 11px; }

        .dock { position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%); width: 92%; max-width: 440px; background: #1a212c; padding: 10px 18px; border-radius: 40px; border: 1px solid var(--border); display: flex; align-items: center; gap: 10px; z-index: 2000; }
        .dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 16px; }

        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: #030508; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-link { color: #64748b; font-size: 11px; text-align: center; }
        .nav-link.active { color: var(--p); }
        .nav-link i { font-size: 22px; display: block; margin-bottom: 3px; }
    </style>
</head>
<body>

<div id="loginOverlay">
    <div class="login-box">
        <h2>INFINITY LOGIN</h2>
        <input type="text" id="usernameInp" placeholder="Enter your name...">
        <button onclick="handleLogin()">Start Chatting</button>
    </div>
</div>

<div class="app-shell">
    <header><h1>INFINITY APEX</h1></header>

    <div class="viewport" id="scrollArea">
        <div id="v-home" class="view" style="display:none">
            <h3 style="text-align:center">Active Users</h3>
            <div id="uList"></div>
        </div>
        <div id="v-chat" class="view active">
            <div id="chatBox"></div>
        </div>
    </div>

    <div class="dock" id="dock">
        <i class="fa-solid fa-face-smile" style="color:var(--p)"></i>
        <input type="text" id="mInp" placeholder="Type message..." onfocus="scrollToBottom()">
        <i class="fa-solid fa-paper-plane" onclick="send()" style="color:var(--p); cursor:pointer;"></i>
    </div>

    <nav>
        <div class="nav-link" onclick="location.reload()"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-link active"><i class="fa-solid fa-comments"></i><span>Chats</span></div>
        <div class="nav-link" onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i><span>Logout</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("apex_user_v2");
let room = "Global";

// LOGIN LOGIC
if(user) {
    document.getElementById("loginOverlay").style.display = "none";
    initChat();
}

window.handleLogin = () => {
    const name = document.getElementById("usernameInp").value.trim();
    if(name.length < 3) return alert("Name too short!");
    localStorage.setItem("apex_user_v2", name);
    location.reload();
};

function initChat() {
    set(ref(db, "online_v2/" + user), true);
    syncMsgs();
}

function syncMsgs() {
    onValue(ref(db, "msgs_v2/" + room), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const node = document.createElement("div");
            node.className = "msg-node " + (d.from === user ? "me" : "them");
            
            // Double Tap to Like Logic
            const bubble = document.createElement("div");
            bubble.className = "bubble";
            bubble.innerHTML = `${d.text} ${d.like ? '<div class="reaction-chip">❤️</div>' : ''}`;
            
            let lastTap = 0;
            bubble.addEventListener('touchend', (e) => {
                const now = new Date().getTime();
                if (now - lastTap < 300) {
                    update(ref(db, `msgs_v2/${room}/${m.key}`), { like: true });
                }
                lastTap = now;
            });

            node.appendChild(bubble);
            box.appendChild(node);
        });
        scrollToBottom();
    });
}

window.scrollToBottom = () => {
    const sc = document.getElementById('scrollArea');
    setTimeout(() => { sc.scrollTop = sc.scrollHeight; }, 200);
};

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    push(ref(db, "msgs_v2/" + room), { from: user, text: i.value });
    i.value = "";
    scrollToBottom();
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
