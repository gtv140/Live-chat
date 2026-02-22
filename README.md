<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Apex | Pro Max</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --p: #00e5ff; --s: #0077ff; --bg: #030508;
            --card: rgba(15, 20, 28, 0.98); --border: rgba(255, 255, 255, 0.08);
            --text: #ffffff; --text-dim: #94a3b8;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; background: var(--bg); position: relative; }

        /* Modern Header */
        header { 
            padding: 15px 20px; background: rgba(3, 5, 8, 0.8); backdrop-filter: blur(25px);
            border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 1000;
        }
        header h1 { font-size: 20px; font-weight: 800; margin: 0; background: linear-gradient(90deg, var(--p), var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* Status Tray */
        .status-tray { display: flex; gap: 15px; padding: 12px 20px; overflow-x: auto; scrollbar-width: none; background: rgba(255,255,255,0.01); border-bottom: 1px solid var(--border); }
        .status-node { min-width: 60px; height: 60px; border-radius: 50%; border: 2px solid var(--p); padding: 2px; position: relative; }
        .status-node img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
        .status-node .add { position: absolute; bottom: 0; right: 0; background: var(--p); color: #000; width: 18px; height: 18px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 10px; border: 2px solid var(--bg); }

        /* Viewport & Scrolling (Fixed Overlap) */
        .viewport { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; scroll-behavior: smooth; padding-bottom: 180px; }
        .view { display: none; width: 100%; }
        .view.active { display: flex; flex-direction: column; }

        /* Message Logic */
        .msg-container { display: flex; flex-direction: column; width: 100%; margin-bottom: 15px; }
        .msg-container.me { align-items: flex-end; }
        .msg-container.them { align-items: flex-start; }

        .bubble { 
            padding: 12px 16px; border-radius: 22px; font-size: 15px; line-height: 1.5; max-width: 85%;
            word-wrap: break-word; position: relative; box-shadow: 0 4px 15px rgba(0,0,0,0.3);
        }
        .me .bubble { background: linear-gradient(135deg, var(--p), var(--s)); color: #000; border-bottom-right-radius: 4px; font-weight: 600; }
        .them .bubble { background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        
        .bubble img { max-width: 100%; border-radius: 15px; margin-top: 5px; }
        .bubble audio { width: 200px; height: 35px; margin-top: 5px; filter: invert(1); }

        .msg-info { font-size: 10px; opacity: 0.5; margin-top: 4px; display: flex; align-items: center; gap: 5px; }
        .me .msg-info { flex-direction: row-reverse; }

        /* Floating Input Bar */
        .input-dock {
            position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%);
            width: 92%; max-width: 440px; background: rgba(20, 25, 35, 0.98); backdrop-filter: blur(20px);
            padding: 8px 12px; border-radius: 40px; border: 1px solid var(--border);
            display: flex; align-items: center; gap: 8px; z-index: 2000; box-shadow: 0 10px 40px rgba(0,0,0,0.6);
        }
        .input-dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 15px; padding: 10px 5px; }
        .input-dock .icon-btn { color: var(--p); font-size: 18px; padding: 8px; cursor: pointer; }
        .send-pill { background: var(--p); color: #000; width: 40px; height: 40px; border-radius: 50%; display: flex; align-items: center; justify-content: center; border: none; cursor: pointer; }

        /* Bottom Nav */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: #030508; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-link { text-align: center; color: var(--text-dim); font-size: 11px; cursor: pointer; transition: 0.3s; }
        .nav-link.active { color: var(--p); }
        .nav-link i { font-size: 20px; display: block; margin-bottom: 4px; }

        /* Delete Button */
        .del-btn { color: #ff4444; font-size: 12px; cursor: pointer; display: none; margin-left: 8px; }
        .msg-container.me:hover .del-btn { display: inline; }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1>INFINITY APEX</h1>
        <div style="display:flex; gap:15px;">
            <i class="fa-solid fa-camera"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:#ff4444"></i>
        </div>
    </header>

    <div class="status-tray" id="stTray" style="display:none;">
        <div class="status-node" onclick="document.getElementById('stInp').click()">
            <img src="https://api.dicebear.com/7.x/bottts/svg?seed=Me">
            <div class="add"><i class="fa-solid fa-plus"></i></div>
            <input type="file" id="stInp" style="display:none" onchange="upStatus(event)">
        </div>
        <div id="stList" style="display:flex; gap:15px;"></div>
    </div>

    <div class="viewport" id="scrollArea">
        <div id="v-login" class="view active">
            <div style="text-align:center; padding-top:15vh;">
                <div style="width:90px; height:90px; background:linear-gradient(135deg, var(--p), var(--s)); border-radius:30px; margin:0 auto 20px; display:flex; align-items:center; justify-content:center; transform:rotate(-10deg);">
                    <i class="fa-solid fa-bolt" style="font-size:45px; color:#000;"></i>
                </div>
                <h2>Quantum Connect</h2>
                <input type="text" id="uInp" placeholder="Nickname" style="width:85%; padding:18px; border-radius:20px; background:var(--card); border:1px solid var(--p); color:white; text-align:center; outline:none; margin-top:20px; font-size:18px;">
                <button onclick="login()" style="width:85%; padding:18px; border-radius:20px; background:var(--p); border:none; color:#000; font-weight:800; font-size:18px; margin-top:15px; cursor:pointer;">START SYNC</button>
            </div>
        </div>

        <div id="v-home" class="view">
            <div style="background:var(--card); padding:20px; border-radius:25px; border:1px solid var(--border); margin-bottom:20px;">
                <h3 id="greet" style="margin:0">Hi!</h3>
                <small style="color:var(--p)">Connection: Encrypted</small>
            </div>
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
        <button class="send-pill" onclick="send()">
            <i class="fa-solid fa-paper-plane"></i>
        </button>
    </div>

    <nav id="bNav" style="display:none;">
        <div class="nav-link active" id="hBtn" onclick="nav('home', 'hBtn')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-link" id="cBtn" onclick="nav('chat', 'cBtn')"><i class="fa-solid fa-message"></i><span>Chats</span></div>
        <div class="nav-link" onclick="alert('Profile Locked')"><i class="fa-solid fa-user-astronaut"></i><span>Profile</span></div>
        <div class="nav-link" onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i><span>Exit</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("apex_god_user");
let room = "Global_Base";
let isP = false;
let tTimer;

if(user) start();

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(!v) return;
    user = v;
    localStorage.setItem("apex_god_user", v);
    start();
};

function start() {
    document.getElementById("v-login").classList.remove("active");
    document.getElementById("v-home").classList.add("active");
    document.getElementById("bNav").style.display = "flex";
    document.getElementById("stTray").style.display = "flex";
    document.getElementById("greet").innerText = "Hi, " + user;
    set(ref(db, "presence/" + user), true);
    onDisconnect(ref(db, "presence/" + user)).remove();
    loadUsers();
}

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
                div.style = "background:var(--card); padding:16px; border-radius:18px; display:flex; align-items:center; gap:12px; margin-bottom:12px; border:1px solid var(--border);";
                div.innerHTML = `<div style="width:42px;height:42px;background:var(--p);border-radius:50%;display:flex;align-items:center;justify-content:center;color:#000;font-weight:bold">${u.key[0].toUpperCase()}</div><b>${u.key}</b>`;
                div.onclick = () => {
                    room = [user, u.key].sort().join("__");
                    isP = true;
                    nav('chat', 'cBtn');
                };
                l.appendChild(div);
            }
        });
    });
}

window.isTyping = () => {
    set(ref(db, "typing/" + room), user);
    clearTimeout(tTimer);
    tTimer = setTimeout(() => set(ref(db, "typing/" + room), null), 2000);
};

function sync() {
    const path = isP ? "god_msgs_p/" + room : "god_msgs_g/main";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const cont = document.createElement("div");
            cont.className = "msg-container " + (d.from === user ? "me" : "them");
            cont.innerHTML = `
                <div class="bubble">
                    ${d.text ? d.text : ''}
                    ${d.img ? `<img src="${d.img}">` : ''}
                    ${d.audio ? `<audio controls src="${d.audio}"></audio>` : ''}
                </div>
                <div class="msg-info">${d.from} • Now ${d.from === user ? '<i class="fa-solid fa-check-double" style="color:var(--p)"></i><i class="fa-solid fa-trash del-btn" onclick="delMsg(\''+m.key+'\')"></i>' : ''}</div>
            `;
            box.appendChild(cont);
        });
        const sc = document.getElementById('scrollArea');
        sc.scrollTop = sc.scrollHeight;
    });
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    const path = isP ? "god_msgs_p/" + room : "god_msgs_g/main";
    push(ref(db, path), { from: user, text: i.value });
    i.value = "";
};

window.sendImg = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const path = isP ? "god_msgs_p/" + room : "god_msgs_g/main";
        push(ref(db, path), { from: user, img: reader.result });
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.delMsg = (key) => {
    const path = isP ? "god_msgs_p/" + room : "god_msgs_g/main";
    remove(ref(db, path + "/" + key));
};

window.logout = () => { localStorage.clear(); location.reload(); };

// --- VOICE RECORDING LOGIC ---
let recorder, chunks = [];
document.getElementById('micBtn').addEventListener('mousedown', async () => {
    const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
    recorder = new MediaRecorder(stream);
    recorder.ondataavailable = e => chunks.push(e.data);
    recorder.onstop = () => {
        const blob = new Blob(chunks, { type: 'audio/ogg; codecs=opus' });
        const reader = new FileReader();
        reader.onload = () => {
            const path = isP ? "god_msgs_p/" + room : "god_msgs_g/main";
            push(ref(db, path), { from: user, audio: reader.result });
        };
        reader.readAsDataURL(blob);
        chunks = [];
    };
    recorder.start();
    document.getElementById('micBtn').style.color = 'red';
});

document.getElementById('micBtn').addEventListener('mouseup', () => {
    recorder.stop();
    document.getElementById('micBtn').style.color = 'var(--p)';
});

</script>
</body>
</html>
