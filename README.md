<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Apex | Stable</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --p: #00e5ff; --s: #0077ff; --bg: #030508;
            --card: rgba(18, 22, 30, 0.95); --border: rgba(255, 255, 255, 0.08);
            --text: #ffffff;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

        /* Header Fixed */
        header { 
            padding: 15px 20px; background: #030508; border-bottom: 1px solid var(--border);
            display: flex; justify-content: space-between; align-items: center; z-index: 1000;
        }
        header h1 { font-size: 18px; font-weight: 800; margin: 0; color: var(--p); }

        /* SCROLL AREA FIX: Ismein padding-bottom zaroori hai */
        .viewport { 
            flex: 1; 
            overflow-y: auto; 
            padding: 15px; 
            display: flex; 
            flex-direction: column;
            /* Typing area aur Nav bar ke liye jagah chhori hai */
            padding-bottom: 160px; 
            scroll-behavior: smooth;
        }

        .view { display: none; width: 100%; }
        .view.active { display: flex; flex-direction: column; }

        /* MESSAGE ALIGNMENT (No Overlapping) */
        .msg-container { 
            display: flex; 
            flex-direction: column; 
            width: 100%; 
            margin-bottom: 20px; /* Messages ke darmiyan gap */
            position: relative;
            clear: both;
        }
        .msg-container.me { align-items: flex-end; }
        .msg-container.them { align-items: flex-start; }

        .bubble { 
            padding: 12px 16px; border-radius: 20px; font-size: 15px; line-height: 1.4; 
            max-width: 80%; word-wrap: break-word; position: relative;
            box-shadow: 0 4px 12px rgba(0,0,0,0.2);
        }
        .me .bubble { background: linear-gradient(135deg, var(--p), var(--s)); color: #000; border-bottom-right-radius: 4px; font-weight: 600; }
        .them .bubble { background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; }

        .msg-info { font-size: 10px; opacity: 0.5; margin-top: 5px; }

        /* INPUT DOCK FIX: Isko fixed rakha hai taake messages iske niche jayein */
        .input-dock {
            position: fixed; 
            bottom: 80px; /* Bottom nav ke upar */
            left: 50%; 
            transform: translateX(-50%);
            width: 92%; 
            max-width: 440px; 
            background: #161b22; 
            padding: 10px 15px; 
            border-radius: 30px; 
            border: 1px solid var(--border);
            display: flex; 
            align-items: center; 
            gap: 10px; 
            z-index: 2000;
            box-shadow: 0 -5px 20px rgba(0,0,0,0.5);
        }
        .input-dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 16px; }
        .icon-btn { color: var(--p); font-size: 20px; cursor: pointer; }
        .send-btn { background: var(--p); color: #000; width: 40px; height: 40px; border-radius: 50%; display: flex; align-items: center; justify-content: center; border: none; }

        /* Navigation */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 70px; background: #030508; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-link { color: #5865f2; font-size: 11px; text-align: center; cursor: pointer; opacity: 0.6; }
        .nav-link.active { color: var(--p); opacity: 1; }
        .nav-link i { font-size: 20px; display: block; margin-bottom: 2px; }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1 id="chatTitle">INFINITY APEX</h1>
        <i class="fa-solid fa-power-off" onclick="logout()" style="color:#ff4444"></i>
    </header>

    <div class="viewport" id="scrollArea">
        <div id="v-home" class="view active">
            <h3 style="margin-left: 10px;">Active Network</h3>
            <div id="uList"></div>
        </div>

        <div id="v-chat" class="view">
            <div id="chatBox"></div>
            <div id="typing" style="font-size:11px; color:var(--p); margin-top:10px; display:none;">typing...</div>
        </div>
    </div>

    <div class="input-dock" id="dock" style="display:none;">
        <i class="fa-solid fa-image icon-btn" onclick="document.getElementById('imgInp').click()"></i>
        <i class="fa-solid fa-microphone icon-btn" id="micBtn"></i>
        <input type="file" id="imgInp" style="display:none" onchange="sendImg(event)">
        <input type="text" id="mInp" placeholder="Type message..." oninput="isTyping()">
        <button class="send-btn" onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>

    <nav id="bNav">
        <div class="nav-link active" id="hBtn" onclick="nav('home', 'hBtn')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-link" id="cBtn" onclick="nav('chat', 'cBtn')"><i class="fa-solid fa-comment"></i><span>Chats</span></div>
        <div class="nav-link" onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i><span>Exit</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("apex_user_v12") || "User_" + Math.floor(Math.random()*1000);
localStorage.setItem("apex_user_v12", user);
let room = "Global";
let isP = false;

// Initial Setup
set(ref(db, "presence/" + user), true);
onDisconnect(ref(db, "presence/" + user)).remove();

window.nav = (vId, bId) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + vId).classList.add("active");
    document.querySelectorAll('.nav-link').forEach(n => n.classList.remove('active'));
    document.getElementById(bId).classList.add('active');
    document.getElementById("dock").style.display = (vId === 'chat' ? 'flex' : 'none');
    if(vId === 'chat') sync();
};

function loadUsers() {
    onValue(ref(db, "presence"), snap => {
        const l = document.getElementById("uList");
        l.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== user) {
                const div = document.createElement("div");
                div.style = "background:var(--card); padding:15px; border-radius:15px; display:flex; align-items:center; gap:12px; margin-bottom:10px; border:1px solid var(--border); cursor:pointer;";
                div.innerHTML = `<div style="width:40px;height:40px;background:var(--p);border-radius:50%;display:flex;align-items:center;justify-content:center;color:#000;font-weight:bold">${u.key[0]}</div><b>${u.key}</b>`;
                div.onclick = () => {
                    room = [user, u.key].sort().join("__");
                    isP = true;
                    document.getElementById("chatTitle").innerText = u.key;
                    nav('chat', 'cBtn');
                };
                l.appendChild(div);
            }
        });
    });
}
loadUsers();

function sync() {
    const path = isP ? "msgs_p/" + room : "msgs_g/main";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const cont = document.createElement("div");
            cont.className = "msg-container " + (d.from === user ? "me" : "them");
            cont.innerHTML = `
                <div class="bubble">${d.text ? d.text : ''} ${d.img ? '<img src="'+d.img+'" style="width:100%; border-radius:10px;">' : ''}</div>
                <div class="msg-info">${d.from} • Now</div>
            `;
            box.appendChild(cont);
        });
        
        // AUTO SCROLL FIX
        scrollToBottom();
    });
}

function scrollToBottom() {
    const sc = document.getElementById('scrollArea');
    setTimeout(() => {
        sc.scrollTop = sc.scrollHeight;
    }, 100);
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    const path = isP ? "msgs_p/" + room : "msgs_g/main";
    push(ref(db, path), { from: user, text: i.value });
    i.value = "";
    scrollToBottom();
};

window.logout = () => { localStorage.clear(); location.reload(); };

// Typing & Image functions (Same as before)
window.isTyping = () => { /* Logic here */ };
window.sendImg = (e) => { /* Logic here */ };

</script>
</body>
</html>
