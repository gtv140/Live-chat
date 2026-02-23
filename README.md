<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Ultimate God Mode | V3.0</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root { --p: #6b5bff; --bg: #000; --text: #fff; --border: #222; }
* { box-sizing: border-box; font-family: 'Segoe UI', sans-serif; transition: 0.3s; }
body { margin: 0; background: var(--bg); color: var(--text); overflow: hidden; }

/* HACKING OVERLAY */
#hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:20000; color:#0f0; padding:20px; font-family:monospace; font-size:12px; overflow:hidden; }

#auth { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; text-align: center; }
#auth img { width: 200px; height: 200px; border-radius: 50%; margin-bottom: 25px; border: 3px solid var(--p); box-shadow: 0 0 50px var(--p); object-fit: cover; }
#auth input { width: 100%; max-width: 320px; padding: 20px; border-radius: 25px; background: #111; border: 1px solid var(--border); color: #fff; text-align: center; margin-bottom: 15px; }
#auth button { width: 100%; max-width: 320px; padding: 20px; border-radius: 25px; background: var(--p); border: none; color: #fff; font-weight: 800; cursor: pointer; }

.app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; }
.page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 100px; }
.page.active { display: block; }
.dash-card { background: #111; border-radius: 20px; padding: 20px; margin-bottom: 15px; border: 1px solid var(--border); }

nav { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); width: 90%; height: 70px; background: #0a0a0a; border-radius: 25px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; }
.nav-btn { color: #555; font-size: 20px; }
.nav-btn.active { color: var(--p); }
</style>
</head>
<body>

<div id="hackOverlay"></div>

<div id="auth">
    <img src="http://googleusercontent.com/generated_image_content/0.png" alt="God Mode">
    <h1>Ultimate God Mode</h1>
    <p style="color:#555">Accessing Mainframe...</p>
    <input type="text" id="userInput" placeholder="Identity Name">
    <button onclick="startHack()">INITIATE ACCESS</button>
</div>

<div class="app">
    <header style="padding:40px 20px 10px; border-bottom:1px solid #222;">
        <h2 style="margin:0">Live Connect <small style="color:gold; font-size:10px;">GOD MODE</small></h2>
    </header>

    <div id="home" class="page active">
        <div class="dash-card" id="adminPanel" style="display:none; border:1px solid gold;">
            <h4 style="color:gold; margin:0 0 10px;">ADMIN CONSOLE</h4>
            <button onclick="wipeData()" style="background:red; color:white; width:100%; padding:10px; border-radius:10px; border:none;">SELF DESTRUCT (WIPE)</button>
        </div>
        <div class="dash-card">Welcome, <span id="uName">...</span></div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox" style="height:60vh; overflow-y:auto; padding:10px; display:flex; flex-direction:column; gap:10px;"></div>
        <div style="display:flex; gap:10px; margin-top:10px;">
            <input id="msgInput" style="flex:1; background:#111; border:1px solid #222; color:white; padding:15px; border-radius:15px;" placeholder="Message..."/>
            <button onclick="sendMsg()" style="background:var(--p); border:none; padding:15px; border-radius:15px; color:white;"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <nav>
        <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-house"></i></div>
        <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-message"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

window.startHack = () => {
    const name = document.getElementById("userInput").value;
    if(!name) return;
    document.getElementById("hackOverlay").style.display = "block";
    const overlay = document.getElementById("hackOverlay");
    const lines = ["INITIALIZING EXPLOIT...", "BYPASSING FIREWALL...", "INJECTING PAYLOAD...", "EXTRACTING DATA...", "ENCRYPTING CONNECTION...", "ACCESS GRANTED!"];
    let i = 0;
    const interval = setInterval(() => {
        overlay.innerHTML += `<p>> ${lines[i]}</p>`;
        i++;
        if(i >= lines.length) {
            clearInterval(interval);
            setTimeout(() => {
                localStorage.setItem("user", name);
                location.reload();
            }, 1000);
        }
    }, 400);
};

let user = localStorage.getItem("user");
if(user) {
    document.getElementById("auth").style.display = "none";
    document.getElementById("uName").innerText = user;
    if(user === "Admin786") document.getElementById("adminPanel").style.display = "block";
    loadChat();
}

function loadChat() {
    onValue(ref(db, "globalChat"), snap => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(m => {
            const v = m.val();
            box.innerHTML += `<div style="padding:10px; background:${v.from===user?var(--p):'#222'}; border-radius:10px; align-self:${v.from===user?'flex-end':'flex-start'}"><b>${v.from}:</b> ${v.text}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
}

window.sendMsg = () => {
    const i = document.getElementById("msgInput");
    if(!i.value) return;
    push(ref(db, "globalChat"), { from: user, text: i.value });
    
    // AUTO REPLY BOT
    setTimeout(() => {
        push(ref(db, "globalChat"), { from: "Ghost_Bot", text: "Admin is busy hacking the world. Stay tuned! 💀" });
    }, 2000);
    
    i.value = "";
};

window.wipeData = () => { if(confirm("Destroy all data?")) remove(ref(db, "globalChat")); };
window.openPage = (id, btn) => {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    document.querySelectorAll('.nav-btn').forEach(n => n.classList.remove('active'));
    btn.classList.add('active');
};
</script>
</body>
</html>
