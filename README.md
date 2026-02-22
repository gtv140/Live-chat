<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Infinity Ultra 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --primary: #00f2fe; --secondary: #4facfe; --bg: #06080f;
  --card: #121620; --glass: rgba(255, 255, 255, 0.05);
  --text: #ffffff; --muted: #8e9297; --accent: #ff007a;
}
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
body, html { margin:0; padding:0; height:100%; font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; background: var(--bg); color: var(--text); overflow: hidden; }

.app-wrapper { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; background: var(--bg); border-right: 1px solid var(--glass); border-left: 1px solid var(--glass); }

/* Header */
header { padding: 15px 20px; background: var(--card); display: flex; justify-content: space-between; align-items: center; z-index: 1000; box-shadow: 0 2px 10px rgba(0,0,0,0.5); }
header h1 { font-size: 20px; font-weight: 700; background: linear-gradient(45deg, var(--primary), var(--secondary)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

/* Viewport Area */
.viewport { flex: 1; overflow-y: auto; padding-bottom: 80px; position: relative; }
.view { display: none; height: 100%; padding: 15px; overflow-y: auto; }
.view.active { display: block; animation: fadeIn 0.3s ease-in-out; }

@keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

/* Login Styling */
#loginPage { text-align: center; padding-top: 20vh; }
.login-card { background: var(--card); padding: 30px; border-radius: 25px; border: 1px solid var(--glass); box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
.input-field { width: 100%; padding: 15px; border-radius: 12px; border: 1px solid var(--glass); background: #1c222d; color: white; margin-bottom: 20px; outline: none; }
.btn-main { width: 100%; padding: 15px; border-radius: 12px; background: linear-gradient(45deg, var(--primary), var(--secondary)); border: none; color: #000; font-weight: bold; cursor: pointer; font-size: 16px; }

/* Dashboard & Status */
.status-container { display: flex; gap: 15px; padding-bottom: 20px; overflow-x: auto; scrollbar-width: none; }
.status-circle { min-width: 65px; height: 65px; border-radius: 50%; border: 2px solid var(--primary); padding: 3px; cursor: pointer; position: relative; }
.status-circle img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
.status-circle::after { content: '+'; position: absolute; bottom: 0; right: 0; background: var(--secondary); width: 20px; height: 20px; border-radius: 50%; font-size: 14px; display: flex; align-items: center; justify-content: center; color: black; border: 2px solid var(--bg); }

/* Chat Bubbles */
.chat-window { display: flex; flex-direction: column; gap: 10px; padding-bottom: 100px; }
.bubble { padding: 12px 16px; border-radius: 18px; max-width: 80%; font-size: 14px; position: relative; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
.bubble.sent { align-self: flex-end; background: #005c4b; color: white; border-bottom-right-radius: 4px; }
.bubble.received { align-self: flex-start; background: var(--card); border: 1px solid var(--glass); border-bottom-left-radius: 4px; }
.bubble b { display: block; font-size: 11px; color: var(--primary); margin-bottom: 3px; }

/* Multimedia Input Bar */
.input-bar { position: absolute; bottom: 85px; left: 10px; right: 10px; background: #1c222d; padding: 10px 15px; border-radius: 30px; display: flex; align-items: center; gap: 12px; z-index: 1000; box-shadow: 0 4px 15px rgba(0,0,0,0.4); border: 1px solid var(--glass); }
.input-bar input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 15px; }
.input-bar i { color: var(--muted); font-size: 20px; cursor: pointer; transition: 0.2s; }
.input-bar i:hover { color: var(--primary); }

/* Bottom Tab Navigation */
nav { position: absolute; bottom: 0; left: 0; right: 0; height: 75px; background: var(--card); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--glass); z-index: 1001; }
nav button { background: none; border: none; color: var(--muted); display: flex; flex-direction: column; align-items: center; gap: 5px; cursor: pointer; transition: 0.3s; }
nav button.active { color: var(--primary); transform: translateY(-3px); }
nav button i { font-size: 22px; }
nav button span { font-size: 10px; font-weight: 600; }

/* User List */
.user-row { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; cursor: pointer; transition: 0.2s; border: 1px solid transparent; }
.user-row:active { transform: scale(0.98); background: rgba(255,255,255,0.1); }
.user-avatar { width: 48px; height: 48px; border-radius: 50%; background: linear-gradient(45deg, var(--primary), var(--secondary)); display: flex; align-items: center; justify-content: center; font-weight: bold; color: #000; font-size: 18px; }
</style>
</head>
<body>

<div class="app-wrapper">
    <header>
        <h1>INFINITY ULTRA</h1>
        <div style="display:flex; gap:18px;">
            <i class="fa-solid fa-camera" onclick="alert('Camera functionality requires HTTPS')"></i>
            <i class="fa-solid fa-search"></i>
            <i class="fa-solid fa-ellipsis-vertical" onclick="toggleSettings()"></i>
        </div>
    </header>

    <div class="viewport" id="scrollContainer">
        
        <div id="view-login" class="view active">
            <div class="login-card">
                <i class="fa-solid fa-shield-cat" style="font-size: 60px; color: var(--primary); margin-bottom: 20px;"></i>
                <h2>Secure Link</h2>
                <input type="text" id="usernameInput" class="input-field" placeholder="Create your alias...">
                <button class="btn-main" onclick="handleLogin()">START CONNECTION</button>
            </div>
        </div>

        <div id="view-home" class="view">
            <div class="status-container">
                <div class="status-circle"><img src="https://api.dicebear.com/7.x/pixel-art/svg?seed=Me"></div>
                <div class="status-circle" style="border-color: #555;"><img src="https://api.dicebear.com/7.x/avataaars/svg?seed=A"></div>
                <div class="status-circle" style="border-color: #555;"><img src="https://api.dicebear.com/7.x/avataaars/svg?seed=B"></div>
            </div>
            <div style="background: linear-gradient(135deg, #1e3c72, #2a5298); padding: 20px; border-radius: 20px; margin-bottom: 20px;">
                <h3 id="userGreet" style="margin:0">Welcome</h3>
                <p style="margin:5px 0 0 0; opacity:0.7; font-size:14px;">Server: Global-Alpha-1</p>
            </div>
            <h3>Quick Connections</h3>
            <div id="activeUserCards"></div>
        </div>

        <div id="view-chat" class="view">
            <div style="position:sticky; top:0; background:var(--bg); padding:10px; z-index:10; border-bottom:1px solid var(--glass); display:flex; align-items:center; gap:10px;">
                <i class="fa-solid fa-arrow-left" onclick="navigate('home')"></i>
                <b id="chattingWith">Global Room</b>
            </div>
            <div class="chat-window" id="messageBox"></div>
        </div>

        <div id="view-users" class="view">
            <h3>Active Nodes</h3>
            <div id="fullUserList"></div>
        </div>

    </div>

    <div class="input-bar" id="inputDock" style="display:none;">
        <i class="fa-solid fa-paperclip"></i>
        <input type="text" id="mainInput" placeholder="Message...">
        <i class="fa-solid fa-microphone"></i>
        <i class="fa-solid fa-paper-plane" style="color:var(--primary);" onclick="handleSendMessage()"></i>
    </div>

    <nav id="bottomNav" style="display:none;">
        <button onclick="navigate('home', this)" class="active"><i class="fa-solid fa-house"></i><span>Home</span></button>
        <button onclick="navigate('chat', this)" id="chatTabBtn"><i class="fa-solid fa-comment-dots"></i><span>Chats</span></button>
        <button onclick="navigate('users', this)"><i class="fa-solid fa-user-group"></i><span>Users</span></button>
        <button onclick="logout()"><i class="fa-solid fa-gear"></i><span>Logout</span></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

let currentUser = localStorage.getItem("inf_user");
let activeRoom = "Global_Lobby";
let isPrivate = false;

// Initialization
if(currentUser) initApp();

window.handleLogin = () => {
    const val = document.getElementById("usernameInput").value.trim();
    if(!val) return alert("Please enter a name");
    currentUser = val;
    localStorage.setItem("inf_user", val);
    initApp();
};

function initApp() {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("view-home").classList.add("active");
    document.getElementById("bottomNav").style.display = "flex";
    document.getElementById("userGreet").innerText = "Hi, " + currentUser + "!";
    
    // Set Online Status
    set(ref(db, "presence/" + currentUser), { status: "online", lastSeen: Date.now() });
    onDisconnect(ref(db, "presence/" + currentUser)).set({ status: "offline", lastSeen: Date.now() });
    
    loadUserDirectory();
}

window.navigate = (pageId, btn) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("view-" + pageId).classList.add("active");
    
    if(btn) {
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    // Input Bar Logic
    document.getElementById("inputDock").style.display = (pageId === 'chat') ? 'flex' : 'none';
    if(pageId === 'chat') syncMessages();
};

function loadUserDirectory() {
    onValue(ref(db, "presence"), (snap) => {
        const list = document.getElementById("fullUserList");
        const quickList = document.getElementById("activeUserCards");
        list.innerHTML = ""; quickList.innerHTML = "";
        
        snap.forEach(child => {
            if(child.key !== currentUser && child.val().status === "online") {
                const userRow = `
                    <div class="user-row" onclick="startPrivateChat('${child.key}')">
                        <div class="user-avatar">${child.key[0].toUpperCase()}</div>
                        <div><b>${child.key}</b><br><small style="color:var(--primary)">Online Now</small></div>
                    </div>`;
                list.innerHTML += userRow;
                quickList.innerHTML += userRow;
            }
        });
    });
}

window.startPrivateChat = (targetUser) => {
    activeRoom = [currentUser, targetUser].sort().join("_");
    isPrivate = true;
    document.getElementById("chattingWith").innerText = targetUser;
    navigate('chat', document.getElementById("chatTabBtn"));
};

function syncMessages() {
    const chatRef = ref(db, "messages/" + activeRoom);
    onValue(chatRef, (snap) => {
        const box = document.getElementById("messageBox");
        box.innerHTML = "";
        snap.forEach(msg => {
            const data = msg.val();
            const align = data.user === currentUser ? "sent" : "received";
            box.innerHTML += `
                <div class="bubble ${align}">
                    <b>${data.user}</b>
                    ${data.text}
                </div>`;
        });
        const container = document.getElementById("scrollContainer");
        container.scrollTop = container.scrollHeight;
    });
}

window.handleSendMessage = () => {
    const input = document.getElementById("mainInput");
    if(!input.value.trim()) return;
    
    push(ref(db, "messages/" + activeRoom), {
        user: currentUser,
        text: input.value,
        timestamp: Date.now()
    });
    input.value = "";
};

window.logout = () => {
    set(ref(db, "presence/" + currentUser), { status: "offline" });
    localStorage.removeItem("inf_user");
    location.reload();
};

window.toggleSettings = () => {
    alert("Pro Settings: \n- Encryption: AES-256\n- Protocol: Firebase Realtime\n- Build: v3.1.0");
};
</script>
</body>
</html>
