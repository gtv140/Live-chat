<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Apex | Next-Gen</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --accent: #00e5ff;
            --gradient: linear-gradient(135deg, #00e5ff 0%, #0077ff 100%);
            --bg: #0b0e14;
            --card: rgba(22, 28, 36, 0.7);
            --glass: rgba(255, 255, 255, 0.03);
            --border: rgba(255, 255, 255, 0.08);
            --text-main: #ffffff;
            --text-dim: #94a3b8;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        
        body, html { 
            margin: 0; padding: 0; height: 100%; 
            background-color: var(--bg); 
            background-image: radial-gradient(circle at 50% -20%, #1e293b 0%, #0b0e14 100%);
            color: var(--text-main); overflow: hidden; 
        }

        /* Main Container */
        .app-container {
            max-width: 480px; margin: 0 auto; height: 100vh;
            display: flex; flex-direction: column; position: relative;
            box-shadow: 0 0 100px rgba(0,0,0,0.5);
        }

        /* Modern Glass Header */
        header {
            padding: 20px; background: rgba(11, 14, 20, 0.8);
            backdrop-filter: blur(20px); border-bottom: 1px solid var(--border);
            display: flex; justify-content: space-between; align-items: center; z-index: 100;
        }
        header h1 { 
            font-size: 22px; font-weight: 800; margin: 0;
            background: var(--gradient); -webkit-background-clip: text; -webkit-text-fill-color: transparent;
        }
        .header-icons { display: flex; gap: 15px; font-size: 18px; color: var(--text-dim); }

        /* Stories - Instagram Style */
        .stories-tray {
            display: flex; gap: 14px; padding: 15px 20px; 
            overflow-x: auto; scrollbar-width: none;
        }
        .story-circle {
            min-width: 66px; height: 66px; border-radius: 50%;
            background: var(--gradient); padding: 2px; position: relative;
            transition: transform 0.2s;
        }
        .story-circle:active { transform: scale(0.9); }
        .story-circle img { 
            width: 100%; height: 100%; border-radius: 50%; 
            object-fit: cover; border: 3px solid var(--bg);
        }
        .add-btn {
            position: absolute; bottom: 2px; right: 2px;
            background: var(--accent); width: 20px; height: 20px;
            border-radius: 50%; border: 2px solid var(--bg);
            display: flex; align-items: center; justify-content: center; font-size: 10px; color: #000;
        }

        /* Content Area */
        .content { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 180px; }
        .view { display: none; }
        .view.active { display: block; animation: view-in 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        @keyframes view-in { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }

        /* Modern Chat Bubbles */
        .msg-row { display: flex; flex-direction: column; margin-bottom: 18px; }
        .bubble {
            padding: 12px 16px; border-radius: 20px; max-width: 85%;
            line-height: 1.5; font-size: 14.5px; position: relative;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }
        .bubble.me {
            align-self: flex-end; background: var(--gradient);
            color: #000; font-weight: 500; border-bottom-right-radius: 4px;
        }
        .bubble.them {
            align-self: flex-start; background: var(--card);
            border: 1px solid var(--border); border-bottom-left-radius: 4px;
        }
        .msg-info { font-size: 10px; margin-top: 5px; opacity: 0.6; display: flex; justify-content: flex-end; }

        /* Floating Input Dock */
        .input-dock {
            position: fixed; bottom: 90px; left: 50%; transform: translateX(-50%);
            width: 90%; max-width: 430px; 
            background: rgba(30, 41, 59, 0.7); backdrop-filter: blur(20px);
            border-radius: 30px; padding: 8px 15px; border: 1px solid var(--border);
            display: flex; align-items: center; gap: 12px; z-index: 1000;
            box-shadow: 0 20px 40px rgba(0,0,0,0.4);
        }
        .input-dock input {
            flex: 1; background: transparent; border: none; color: white;
            padding: 10px 5px; outline: none; font-size: 15px;
        }
        .input-dock .icon-btn {
            width: 40px; height: 40px; border-radius: 50%;
            display: flex; align-items: center; justify-content: center;
            color: var(--accent); cursor: pointer; transition: 0.2s;
        }
        .input-dock .send-btn { background: var(--gradient); color: #000; }

        /* Bottom Nav - iPhone Style */
        nav {
            position: fixed; bottom: 0; width: 100%; max-width: 480px;
            height: 80px; background: rgba(11, 14, 20, 0.95);
            backdrop-filter: blur(20px); border-top: 1px solid var(--border);
            display: flex; justify-content: space-around; align-items: center; z-index: 1000;
        }
        .nav-item {
            display: flex; flex-direction: column; align-items: center;
            color: var(--text-dim); gap: 4px; cursor: pointer; transition: 0.3s;
        }
        .nav-item.active { color: var(--accent); }
        .nav-item i { font-size: 20px; }
        .nav-item span { font-size: 11px; font-weight: 600; }

        /* User List Card */
        .user-card {
            background: var(--card); border: 1px solid var(--border);
            padding: 15px; border-radius: 20px; margin-bottom: 12px;
            display: flex; align-items: center; gap: 15px; transition: 0.2s;
        }
        .user-card:active { transform: scale(0.97); background: rgba(255,255,255,0.05); }
        .avatar-box {
            width: 50px; height: 50px; border-radius: 50%;
            background: #1e293b; border: 2px solid var(--border);
            display: flex; align-items: center; justify-content: center; font-weight: bold;
        }

        /* Typing & Indicators */
        #typing-status { font-size: 12px; color: var(--accent); margin-bottom: 10px; display: none; }
    </style>
</head>
<body>

<div class="app-container">
    <header>
        <h1>INFINITY</h1>
        <div class="header-icons">
            <i class="fa-solid fa-camera"></i>
            <i class="fa-solid fa-bell"></i>
        </div>
    </header>

    <div class="stories-tray" id="storyTray" style="display:none;">
        <div class="story-circle" onclick="document.getElementById('stInp').click()">
            <img src="https://api.dicebear.com/7.x/bottts-neutral/svg?seed=Me">
            <div class="add-btn"><i class="fa-solid fa-plus"></i></div>
            <input type="file" id="stInp" style="display:none" onchange="uploadStory(event)">
        </div>
        <div id="othersStories" style="display:flex; gap:14px;"></div>
    </div>

    <div class="content" id="scrollArea">
        
        <div id="v-login" class="view active">
            <div style="text-align:center; padding-top:20vh;">
                <div style="width:100px; height:100px; background:var(--gradient); border-radius:30px; margin:0 auto 30px; display:flex; align-items:center; justify-content:center; transform: rotate(-10deg);">
                    <i class="fa-solid fa-bolt" style="font-size:50px; color:black;"></i>
                </div>
                <h2 style="font-size:32px; margin-bottom:10px;">Welcome Back</h2>
                <p style="color:var(--text-dim); margin-bottom:40px;">Connect to the secure network.</p>
                
                <input type="text" id="uInp" placeholder="Enter Username" style="width:100%; padding:20px; border-radius:20px; background:var(--card); border:1px solid var(--border); color:white; outline:none; text-align:center; font-size:18px;">
                
                <button onclick="login()" style="width:100%; padding:20px; border-radius:20px; background:var(--gradient); border:none; color:black; font-weight:800; font-size:18px; margin-top:20px; cursor:pointer; box-shadow: 0 10px 30px rgba(0,229,255,0.3);">Get Started</button>
            </div>
        </div>

        <div id="v-home" class="view">
            <div style="background:var(--card); padding:25px; border-radius:25px; border:1px solid var(--border); margin-bottom:25px; position:relative; overflow:hidden;">
                <h2 id="greet" style="margin:0">Hello, User</h2>
                <p style="color:var(--text-dim); font-size:14px; margin:5px 0 0 0;">Everything is secure and up to date.</p>
                <i class="fa-solid fa-shield-check" style="position:absolute; right:-10px; bottom:-10px; font-size:80px; opacity:0.05;"></i>
            </div>
            
            <h3 style="font-size:18px; color:var(--text-dim); margin-bottom:15px;">Active Nodes</h3>
            <div id="activeList"></div>
        </div>

        <div id="v-chat" class="view">
            <div style="display:flex; align-items:center; gap:15px; margin-bottom:25px;">
                <i class="fa-solid fa-chevron-left" onclick="nav('home', 'hBtn')" style="font-size:20px;"></i>
                <div>
                    <b id="activeChatName" style="font-size:20px;">Global Core</b><br>
                    <span id="typing-status">typing...</span>
                </div>
            </div>
            <div id="chatBox" style="display:flex; flex-direction:column;"></div>
        </div>

    </div>

    <div class="input-dock" id="inputDock" style="display:none;">
        <div class="icon-btn" onclick="document.getElementById('fInp').click()">
            <i class="fa-solid fa-plus"></i>
        </div>
        <input type="file" id="fInp" style="display:none" onchange="alert('File selected')">
        <input type="text" id="mInp" placeholder="Type a message..." oninput="isTyping()">
        <div class="icon-btn send-btn" onclick="send()">
            <i class="fa-solid fa-paper-plane"></i>
        </div>
    </div>

    <nav id="bottomNav" style="display:none;">
        <div class="nav-item active" id="hBtn" onclick="nav('home', 'hBtn')">
            <i class="fa-solid fa-house-simple"></i>
            <span>Home</span>
        </div>
        <div class="nav-item" id="cBtn" onclick="nav('chat', 'cBtn')">
            <i class="fa-solid fa-comment-dots"></i>
            <span>Chats</span>
        </div>
        <div class="nav-item" onclick="alert('Profile Locked')">
            <i class="fa-solid fa-user-circle"></i>
            <span>Profile</span>
        </div>
        <div class="nav-item" onclick="logout()">
            <i class="fa-solid fa-arrow-right-from-bracket"></i>
            <span>Exit</span>
        </div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("apex_modern_user");
let room = "Global_Base";
let isPriv = false;
let tTimer;

if(user) startApp();

window.login = () => {
    const val = document.getElementById("uInp").value.trim();
    if(!val) return;
    user = val;
    localStorage.setItem("apex_modern_user", val);
    startApp();
};

function startApp() {
    document.getElementById("v-login").classList.remove("active");
    document.getElementById("v-home").classList.add("active");
    document.getElementById("bottomNav").style.display = "flex";
    document.getElementById("storyTray").style.display = "flex";
    document.getElementById("greet").innerText = "Hey, " + user + "!";
    
    set(ref(db, "live/" + user), { online: true });
    onDisconnect(ref(db, "live/" + user)).remove();
    loadNodes();
}

window.nav = (vId, btnId) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + vId).classList.add("active");
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
    document.getElementById(btnId).classList.add('active');
    
    document.getElementById("inputDock").style.display = (vId === 'chat') ? 'flex' : 'none';
    if(vId === 'chat') syncChat();
};

function loadNodes() {
    onValue(ref(db, "live"), snap => {
        const list = document.getElementById("activeList");
        list.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== user) {
                const div = document.createElement("div");
                div.className = "user-card";
                div.innerHTML = `<div class="avatar-box">${u.key[0].toUpperCase()}</div>
                                 <div><b style="font-size:16px;">${u.key}</b><br><span style="color:var(--accent); font-size:12px;">● Online</span></div>`;
                div.onclick = () => {
                    room = [user, u.key].sort().join("::");
                    isPriv = true;
                    document.getElementById("activeChatName").innerText = u.key;
                    nav('chat', 'cBtn');
                };
                list.appendChild(div);
            }
        });
    });

    onValue(ref(db, "typing/" + room), snap => {
        const t = document.getElementById("typing-status");
        if(snap.val() && snap.val() !== user) t.style.display = "inline";
        else t.style.display = "none";
    });
}

window.isTyping = () => {
    set(ref(db, "typing/" + room), user);
    clearTimeout(tTimer);
    tTimer = setTimeout(() => set(ref(db, "typing/" + room), null), 2000);
};

function syncChat() {
    const path = isPriv ? "modern_priv/" + room : "modern_pub/main";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div");
            div.className = "msg-row";
            div.innerHTML = `<div class="bubble ${d.from === user ? 'me' : 'them'}">${d.text}</div>
                             <div class="msg-info">${d.from}</div>`;
            box.appendChild(div);
        });
        const sc = document.getElementById('scrollArea');
        sc.scrollTop = sc.scrollHeight;
    });
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    const path = isPriv ? "modern_priv/" + room : "modern_pub/main";
    push(ref(db, path), { from: user, text: i.value });
    i.value = "";
};

window.logout = () => { localStorage.clear(); location.reload(); };

window.uploadStory = (e) => {
    alert("Story Uploading..."); // Simulating upload
};
</script>
</body>
</html>
