<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Apex | Stabilized</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --p: #00e5ff; --s: #0077ff; --bg: #05070a;
            --card: rgba(22, 28, 36, 1); --border: rgba(255, 255, 255, 0.1);
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: white; overflow: hidden; }

        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; }

        header { 
            padding: 15px 20px; background: #0a0f18; border-bottom: 1px solid var(--border);
            display: flex; justify-content: space-between; align-items: center; z-index: 100;
        }
        header h1 { font-size: 18px; font-weight: 800; margin: 0; color: var(--p); }

        /* SCROLL AREA FIX */
        .viewport { 
            flex: 1; 
            overflow-y: auto; 
            padding: 15px; 
            display: flex; 
            flex-direction: column; 
            gap: 15px; /* Spacing between messages */
            padding-bottom: 180px; /* Space for dock and nav */
            scroll-behavior: smooth;
        }

        .view { display: none; width: 100%; }
        .view.active { display: flex; flex-direction: column; }

        /* MESSAGE BUBBLE FIX */
        .msg-node { 
            display: flex; 
            flex-direction: column; 
            width: 100%; 
            margin-bottom: 10px; 
            position: relative;
        }
        
        .bubble { 
            padding: 12px 16px; 
            border-radius: 20px; 
            font-size: 15px; 
            line-height: 1.4; 
            max-width: 80%; 
            word-wrap: break-word; 
            position: relative;
            box-shadow: 0 4px 10px rgba(0,0,0,0.3);
        }

        .msg-node.me { align-items: flex-end; }
        .me .bubble { 
            background: linear-gradient(135deg, var(--p), var(--s)); 
            color: #000; 
            border-bottom-right-radius: 4px; 
            font-weight: 600;
        }

        .msg-node.them { align-items: flex-start; }
        .them .bubble { 
            background: var(--card); 
            border: 1px solid var(--border); 
            border-bottom-left-radius: 4px; 
            color: white;
        }

        .meta { font-size: 10px; opacity: 0.6; margin-top: 5px; }
        .me .meta { margin-right: 5px; }
        .them .meta { margin-left: 5px; }

        .bubble img { max-width: 100%; border-radius: 12px; margin-top: 8px; }

        /* DOCK & NAV FIXED */
        .dock {
            position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%);
            width: 92%; max-width: 440px; background: #1a1f2b; 
            padding: 10px 15px; border-radius: 30px; border: 1px solid var(--border);
            display: flex; align-items: center; gap: 10px; z-index: 1000;
        }
        .dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 16px; }
        .send-btn { background: var(--p); color: #000; width: 40px; height: 40px; border-radius: 50%; display: flex; align-items: center; justify-content: center; border: none; cursor: pointer; }

        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: #05070a; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        .nav-link { color: #64748b; font-size: 11px; text-align: center; cursor: pointer; }
        .nav-link.active { color: var(--p); }
        .nav-link i { font-size: 20px; display: block; margin-bottom: 4px; }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1 id="headTitle">INFINITY APEX</h1>
        <i class="fa-solid fa-power-off" onclick="logout()" style="color:#ff4444"></i>
    </header>

    <div class="viewport" id="scrollArea">
        <div id="v-login" class="view active">
            <div style="text-align:center; padding-top:100px;">
                <h1 style="font-size:50px;">💎</h1>
                <h2>Connect Node</h2>
                <input type="text" id="uInp" placeholder="Nickname..." style="width:85%; padding:18px; border-radius:15px; background:var(--card); border:1px solid var(--p); color:white; text-align:center; outline:none; margin-top:20px;">
                <button onclick="login()" style="width:85%; padding:18px; border-radius:15px; background:var(--p); border:none; color:#000; font-weight:bold; margin-top:15px;">INITIALIZE</button>
            </div>
        </div>

        <div id="v-home" class="view">
            <div style="background:var(--card); padding:20px; border-radius:20px; border:1px solid var(--border); margin-bottom:20px;">
                <h3 id="greet" style="margin:0">Hi!</h3>
                <small style="color:var(--p)">System Secure</small>
            </div>
            <div id="uList"></div>
        </div>

        <div id="v-chat" class="view">
            <div id="chatBox"></div>
            <div id="typing" style="font-size:11px; color:var(--p); margin-top:5px; display:none;">Typing...</div>
        </div>
    </div>

    <div class="dock" id="dock" style="display:none;">
        <input type="file" id="fInp" style="display:none" onchange="sendImg(event)">
        <i class="fa-solid fa-camera" onclick="document.getElementById('fInp').click()"></i>
        <input type="text" id="mInp" placeholder="Type message..." oninput="setTyping()">
        <button class="send-btn" onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>

    <nav id="bNav" style="display:none;">
        <div class="nav-link active" id="hBtn" onclick="nav('home', 'hBtn')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-link" id="cBtn" onclick="nav('chat', 'cBtn')"><i class="fa-solid fa-message"></i><span>Chats</span></div>
        <div class="nav-link" onclick="logout()"><i class="fa-solid fa-gear"></i><span>Exit</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("apex_v6_user");
let room = "Global_Lobby";
let isP = false;
let tTimer;

if(user) start();

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(!v) return;
    user = v;
    localStorage.setItem("apex_v6_user", v);
    start();
};

function start() {
    document.getElementById("v-login").classList.remove("active");
    document.getElementById("v-home").classList.add("active");
    document.getElementById("bNav").style.display = "flex";
    document.getElementById("greet").innerText = "Welcome, " + user;
    set(ref(db, "presence/" + user), true);
    onDisconnect(ref(db, "presence/" + user)).remove();
    loadNodes();
}

window.nav = (vId, bId) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + vId).classList.add("active");
    document.querySelectorAll('.nav-link').forEach(n => n.classList.remove('active'));
    document.getElementById(bId).classList.add('active');
    document.getElementById("dock").style.display = (vId === 'chat' ? 'flex' : 'none');
    if(vId === 'chat') sync();
};

function loadNodes() {
    onValue(ref(db, "presence"), snap => {
        const l = document.getElementById("uList");
        l.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== user) {
                const div = document.createElement("div");
                div.style = "background:var(--card); padding:15px; border-radius:15px; display:flex; align-items:center; gap:12px; margin-bottom:10px; border:1px solid var(--border);";
                div.innerHTML = `<div style="width:40px;height:40px;background:var(--p);border-radius:50%;display:flex;align-items:center;justify-content:center;color:#000;font-weight:bold">${u.key[0].toUpperCase()}</div><b>${u.key}</b>`;
                div.onclick = () => {
                    room = [user, u.key].sort().join("__");
                    isP = true;
                    document.getElementById("headTitle").innerText = u.key;
                    nav('chat', 'cBtn');
                };
                l.appendChild(div);
            }
        });
    });
}

window.setTyping = () => {
    set(ref(db, "typing/" + room), user);
    clearTimeout(tTimer);
    tTimer = setTimeout(() => set(ref(db, "typing/" + room), null), 2000);
};

function sync() {
    const path = isP ? "msgs_p/" + room : "msgs_g/main";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const node = document.createElement("div");
            node.className = "msg-node " + (d.from === user ? "me" : "them");
            node.innerHTML = `
                <div class="bubble">${d.text ? d.text : ''} ${d.img ? '<img src="'+d.img+'">' : ''}</div>
                <div class="meta">${d.from}</div>
            `;
            box.appendChild(node);
        });
        
        // RELIABLE AUTO SCROLL
        const sc = document.getElementById('scrollArea');
        sc.scrollTop = sc.scrollHeight;
    });
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    const path = isP ? "msgs_p/" + room : "msgs_g/main";
    push(ref(db, path), { from: user, text: i.value });
    i.value = "";
};

window.sendImg = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const path = isP ? "msgs_p/" + room : "msgs_g/main";
        push(ref(db, path), { from: user, img: reader.result });
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
