<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect Pro Max</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --primary: #0084ff; --bg: #f0f2f5; --white: #ffffff; --text: #1c1e21; --gray: #65676b;
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Segoe UI', sans-serif; }
body { margin: 0; background: var(--bg); height: 100vh; overflow: hidden; }

/* Main App Layout */
.app { width: 100%; max-width: 500px; margin: 0 auto; height: 100vh; background: var(--white); display: flex; flex-direction: column; position: relative; }

/* Header Pro */
header { padding: 15px; background: var(--white); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #ddd; }
header h1 { margin: 0; font-size: 22px; color: var(--primary); font-weight: 800; }
.header-icons { display: flex; gap: 20px; color: var(--gray); font-size: 18px; }

/* Status Tray */
.status-tray { display: flex; gap: 12px; padding: 15px; overflow-x: auto; border-bottom: 1px solid #eee; }
.status-item { min-width: 65px; text-align: center; }
.status-circle { width: 60px; height: 60px; border-radius: 50%; border: 3px solid var(--primary); padding: 2px; margin-bottom: 5px; }
.status-circle img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
.status-item span { font-size: 11px; color: var(--text); }

/* Chat Container */
#chatBox { flex: 1; overflow-y: auto; padding: 15px; background: #e5ddd5; display: flex; flex-direction: column; gap: 10px; }
.msg { max-width: 75%; padding: 10px 14px; border-radius: 18px; font-size: 15px; position: relative; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }
.msg.me { align-self: flex-end; background: #dcf8c6; border-bottom-right-radius: 2px; }
.msg.other { align-self: flex-start; background: var(--white); border-bottom-left-radius: 2px; }
.msg-info { font-size: 10px; color: var(--gray); text-align: right; margin-top: 4px; display: flex; justify-content: flex-end; gap: 3px; }

/* Typing Indicator */
#typing { font-size: 12px; color: var(--gray); padding: 5px 15px; display: none; }

/* Action Bar (Input) */
.action-bar { padding: 10px; background: #f0f0f0; display: flex; align-items: center; gap: 10px; }
.input-pill { flex: 1; background: white; border-radius: 25px; padding: 5px 15px; display: flex; align-items: center; gap: 10px; }
.input-pill input { flex: 1; border: none; outline: none; padding: 8px 0; font-size: 16px; }
.btn-round { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--primary); color: white; display: flex; align-items: center; justify-content: center; font-size: 18px; cursor: pointer; }

/* Bottom Nav */
nav { display: flex; justify-content: space-around; padding: 10px 0; border-top: 1px solid #ddd; background: white; }
nav button { background: none; border: none; color: var(--gray); display: flex; flex-direction: column; align-items: center; gap: 4px; font-size: 20px; }
nav button.active { color: var(--primary); }
nav button span { font-size: 10px; font-weight: 600; }

/* Login Page Overlay */
#loginPage { position: fixed; inset: 0; background: white; z-index: 1000; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 30px; }
#loginPage input { width: 100%; padding: 15px; border-radius: 10px; border: 1px solid #ddd; margin-bottom: 15px; }

</style>
</head>
<body>

<div id="loginPage">
    <i class="fa-solid fa-bolt-lightning" style="font-size: 50px; color: var(--primary); margin-bottom: 20px;"></i>
    <h2>Live Connect Pro</h2>
    <input type="text" id="uNameInp" placeholder="Enter Codename">
    <button onclick="doLogin()" style="width:100%; padding:15px; border:none; background:var(--primary); color:white; border-radius:10px; font-weight:bold;">GET STARTED</button>
</div>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div class="header-icons">
            <i class="fa-solid fa-camera"></i>
            <i class="fa-solid fa-magnifying-glass"></i>
            <i class="fa-solid fa-ellipsis-vertical"></i>
        </div>
    </header>

    <div class="status-tray">
        <div class="status-item">
            <div class="status-circle"><img src="https://i.pravatar.cc/100?img=12"></div>
            <span>My Status</span>
        </div>
        <div class="status-item">
            <div class="status-circle" style="border-color: #ccc;"><img src="https://i.pravatar.cc/100?img=5"></div>
            <span>Wasif</span>
        </div>
        <div class="status-item">
            <div class="status-circle"><img src="https://i.pravatar.cc/100?img=8"></div>
            <span>Sheru</span>
        </div>
    </div>

    <div id="chatBox"></div>
    <div id="typing">Nazim is typing...</div>

    <div class="action-bar">
        <i class="fa-solid fa-plus" style="color:var(--primary); font-size:20px;"></i>
        <div class="input-pill">
            <input type="text" id="mInp" placeholder="Message..." oninput="showTyping()">
            <i class="fa-solid fa-face-smile" style="color:var(--gray);"></i>
        </div>
        <button id="sendBtn" class="btn-round" onclick="pushMsg()">
            <i class="fa-solid fa-microphone" id="micIcon"></i>
        </button>
    </div>

    <nav>
        <button class="active"><i class="fa-solid fa-message"></i><span>Chats</span></button>
        <button><i class="fa-solid fa-circle-notch"></i><span>Status</span></button>
        <button><i class="fa-solid fa-phone"></i><span>Calls</span></button>
        <button onclick="location.reload()"><i class="fa-solid fa-user"></i><span>Profile</span></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const fbConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(fbConfig);
const db = getDatabase(app);
let user = null;

window.doLogin = () => {
    const val = document.getElementById("uNameInp").value.trim();
    if(!val) return;
    user = val;
    document.getElementById("loginPage").style.display = "none";
};

// Typing Logic
window.showTyping = () => {
    const btn = document.getElementById("sendBtn");
    const icon = document.getElementById("micIcon");
    if(document.getElementById("mInp").value.length > 0) {
        icon.className = "fa-solid fa-paper-plane";
    } else {
        icon.className = "fa-solid fa-microphone";
    }
};

window.pushMsg = () => {
    const inp = document.getElementById("mInp");
    if(!inp.value) return;
    push(ref(db, "pro_chat"), {
        from: user,
        text: inp.value,
        time: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})
    });
    inp.value = "";
    showTyping();
};

onValue(ref(db, "pro_chat"), snap => {
    const box = document.getElementById("chatBox");
    box.innerHTML = "";
    snap.forEach(m => {
        const d = m.val();
        const div = document.createElement("div");
        div.className = "msg " + (d.from === user ? "me" : "other");
        div.innerHTML = `
            <small style="color:var(--primary); font-weight:bold; font-size:10px;">${d.from}</small>
            <div>${d.text}</div>
            <div class="msg-info">${d.time} ${d.from === user ? '<i class="fa-solid fa-check-double" style="color:#34b7f1"></i>' : ''}</div>
        `;
        box.appendChild(div);
    });
    box.scrollTop = box.scrollHeight;
});
</script>
</body>
</html>
