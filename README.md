<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Ultra Pro</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --primary: #00f2fe; --secondary: #4facfe; --bg: #06080f;
            --card: #121620; --glass: rgba(255, 255, 255, 0.08);
            --text: #ffffff; --muted: #8e9297;
        }
        * { box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
        body, html { margin:0; padding:0; height:100%; background: var(--bg); color: var(--text); overflow: hidden; }

        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

        /* Header Fix */
        header { padding: 15px 20px; background: var(--card); display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--glass); z-index: 100; }
        header h1 { font-size: 18px; font-weight: 800; background: linear-gradient(45deg, var(--primary), var(--secondary)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        /* Scrollable Content */
        .viewport { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 160px; scroll-behavior: smooth; }
        .view { display: none; }
        .view.active { display: block; animation: fadeIn 0.3s ease; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Chat Bubbles */
        .bubble { padding: 12px 16px; border-radius: 18px; max-width: 85%; font-size: 14px; margin-bottom: 10px; position: relative; line-height: 1.4; }
        .bubble.sent { align-self: flex-end; background: #005c4b; margin-left: auto; border-bottom-right-radius: 2px; }
        .bubble.received { align-self: flex-start; background: var(--card); border: 1px solid var(--glass); border-bottom-left-radius: 2px; }
        .bubble b { display: block; font-size: 10px; color: var(--primary); margin-bottom: 4px; }

        /* THE FIX: Floating Input Bar */
        .input-dock { 
            position: fixed; bottom: 80px; left: 50%; transform: translateX(-50%);
            width: 95%; max-width: 480px; background: #1c222d; padding: 10px 15px; 
            border-radius: 30px; display: flex; align-items: center; gap: 10px; 
            border: 1px solid var(--glass); z-index: 999; box-shadow: 0 5px 20px rgba(0,0,0,0.6);
        }
        .input-dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 15px; }
        .input-dock i { color: var(--primary); font-size: 20px; cursor: pointer; transition: 0.2s; }
        .input-dock i:active { transform: scale(0.9); }

        /* Navigation Fix */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: var(--card); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--glass); z-index: 1000; }
        nav button { background: none; border: none; color: var(--muted); display: flex; flex-direction: column; align-items: center; gap: 4px; cursor: pointer; }
        nav button.active { color: var(--primary); }
        nav button i { font-size: 22px; }

        /* Lists */
        .row { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; cursor: pointer; border: 1px solid transparent; }
        .row:hover { border-color: var(--primary); }
        .avatar { width: 45px; height: 45px; border-radius: 50%; background: var(--glass); display: flex; align-items: center; justify-content: center; font-weight: bold; border: 2px solid var(--primary); }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1>INFINITY ULTRA</h1>
        <div style="display:flex; gap:15px;">
            <i class="fa-solid fa-camera" onclick="document.getElementById('fileInp').click()"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:red"></i>
        </div>
    </header>

    <div class="viewport" id="scrollArea">
        <div id="v-login" class="view active">
            <div style="text-align:center; padding-top:100px;">
                <i class="fa-solid fa-user-astronaut" style="font-size:70px; color:var(--primary)"></i>
                <h2>Welcome to Ultra</h2>
                <input type="text" id="uInp" placeholder="Enter Username..." style="width:80%; padding:15px; border-radius:12px; background:var(--card); border:1px solid var(--primary); color:white; outline:none; margin-top:20px;">
                <br>
                <button onclick="login()" style="width:80%; padding:15px; border-radius:12px; background:var(--primary); color:black; font-weight:bold; border:none; margin-top:20px;">SYNC IDENTITY</button>
            </div>
        </div>

        <div id="v-home" class="view">
            <div style="background: linear-gradient(45deg, #121620, #1c222d); padding: 25px; border-radius: 20px; text-align: center; margin-bottom: 20px;">
                <h3 id="welcomeH">Hi Explorer!</h3>
                <p>Status: <span style="color:var(--primary)">Secure & Live</span></p>
            </div>
            <h3>Quick Connect</h3>
            <div id="dashUsers"></div>
        </div>

        <div id="v-chat" class="view">
            <div style="text-align:center; padding-bottom:10px; color:var(--primary); font-weight:bold;" id="chatT">Global Room</div>
            <div id="chatBox" style="display:flex; flex-direction:column;"></div>
        </div>

        <div id="v-users" class="view">
            <h3>Active Network</h3>
            <div id="fullList"></div>
        </div>
    </div>

    <div class="input-dock" id="typeDock" style="display:none;">
        <input type="file" id="fileInp" style="display:none" onchange="alert('Image upload ready!')">
        <i class="fa-solid fa-image" onclick="document.getElementById('fileInp').click()"></i>
        <input type="text" id="mInp" placeholder="Type message here...">
        <i class="fa-solid fa-microphone" onclick="alert('Recording...')"></i>
        <i class="fa-solid fa-paper-plane" onclick="send()"></i>
    </div>

    <nav id="footer" style="display:none;">
        <button onclick="nav('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="nav('chat', this)" id="cTab"><i class="fa-solid fa-comment-dots"></i></button>
        <button onclick="nav('users', this)"><i class="fa-solid fa-user-group"></i></button>
        <button onclick="alert('Profile Coming Soon')"><i class="fa-solid fa-circle-user"></i></button>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("u_name");
let room = "Global_Lobby";
let isP = false;

if(user) startApp();

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(!v) return;
    user = v;
    localStorage.setItem("u_name", v);
    startApp();
};

function startApp() {
    document.getElementById("v-login").classList.remove("active");
    document.getElementById("v-home").classList.add("active");
    document.getElementById("footer").style.display = "flex";
    document.getElementById("welcomeH").innerText = "Hi, " + user + "!";
    
    set(ref(db, "active_users/" + user), { online: true });
    onDisconnect(ref(db, "active_users/" + user)).set({ online: false });
    loadUsers();
}

window.nav = (id, btn) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + id).classList.add("active");
    document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    
    document.getElementById("typeDock").style.display = (id === 'chat') ? 'flex' : 'none';
    if(id === 'chat') sync();
};

function loadUsers() {
    onValue(ref(db, "active_users"), snap => {
        const fl = document.getElementById("fullList");
        const dl = document.getElementById("dashUsers");
        fl.innerHTML = ""; dl.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online && u.key !== user) {
                const html = `<div class="row"><div class="avatar">${u.key[0]}</div><div><b>${u.key}</b><br><small>Online Now</small></div></div>`;
                const div = document.createElement("div");
                div.innerHTML = html;
                div.onclick = () => {
                    room = [user, u.key].sort().join("_");
                    isP = true;
                    document.getElementById("chatT").innerText = "Chat with " + u.key;
                    nav('chat', document.getElementById('cTab'));
                };
                fl.appendChild(div.cloneNode(true));
                dl.appendChild(div);
            }
        });
    });
}

function sync() {
    const path = isP ? "p_chats/" + room : "g_chats/main";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div");
            div.className = "bubble " + (d.from === user ? "sent" : "received");
            div.innerHTML = `<b>${d.from}</b>${d.text}`;
            box.appendChild(div);
        });
        const v = document.getElementById('scrollArea');
        v.scrollTop = v.scrollHeight;
    });
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    const path = isP ? "p_chats/" + room : "g_chats/main";
    push(ref(db, path), { from: user, text: i.value });
    i.value = "";
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
