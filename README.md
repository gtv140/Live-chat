<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Apex | Final</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --p: #00e5ff; --s: #0077ff; --bg: #05070a;
            --card: rgba(22, 28, 36, 0.98); --border: rgba(255, 255, 255, 0.1);
            --text: #ffffff; --text-dim: #94a3b8;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }

        /* Modern Glass Header */
        header { 
            padding: 15px 20px; background: rgba(5, 7, 10, 0.85); backdrop-filter: blur(25px);
            border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 1000;
        }
        header h1 { font-size: 20px; font-weight: 800; margin: 0; letter-spacing: -0.5px; background: linear-gradient(90deg, var(--p), var(--s)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* Stories Section */
        .story-bar { display: flex; gap: 15px; padding: 12px 20px; overflow-x: auto; scrollbar-width: none; background: rgba(255,255,255,0.02); border-bottom: 1px solid var(--border); }
        .story { min-width: 62px; height: 62px; border-radius: 50%; border: 2px solid var(--p); padding: 3px; cursor: pointer; position: relative; }
        .story img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
        .story .add { position: absolute; bottom: 0; right: 0; background: var(--p); color: #000; width: 18px; height: 18px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 10px; border: 2px solid var(--bg); }

        /* Fixed Viewport & Chat Layout */
        .viewport { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; scroll-behavior: smooth; padding-bottom: 180px; }
        .view { display: none; width: 100%; }
        .view.active { display: flex; flex-direction: column; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* Perfect Bubble Alignment */
        .msg-node { display: flex; flex-direction: column; max-width: 82%; margin-bottom: 15px; position: relative; }
        .msg-node.me { align-self: flex-end; }
        .msg-node.them { align-self: flex-start; }

        .bubble { 
            padding: 12px 16px; border-radius: 22px; font-size: 15px; line-height: 1.5; word-wrap: break-word; 
            box-shadow: 0 4px 15px rgba(0,0,0,0.2); position: relative;
        }
        .me .bubble { background: linear-gradient(135deg, var(--p), var(--s)); color: #000; border-bottom-right-radius: 4px; font-weight: 500; }
        .them .bubble { background: var(--card); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .bubble img { max-width: 100%; border-radius: 15px; margin-top: 8px; display: block; }

        .meta { font-size: 10px; margin-top: 5px; opacity: 0.6; display: flex; align-items: center; gap: 4px; }
        .me .meta { justify-content: flex-end; }
        .del-icon { color: #ff4444; font-size: 12px; margin-left: 8px; cursor: pointer; visibility: hidden; }
        .msg-node.me:hover .del-icon { visibility: visible; }

        /* Floating Input Dock */
        .dock {
            position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%);
            width: 92%; max-width: 440px; background: rgba(25, 30, 45, 0.95); backdrop-filter: blur(20px);
            padding: 8px 15px; border-radius: 40px; border: 1px solid var(--border);
            display: flex; align-items: center; gap: 10px; z-index: 2000; box-shadow: 0 10px 30px rgba(0,0,0,0.5);
        }
        .dock input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 16px; padding: 10px 5px; }
        .dock i { font-size: 20px; color: var(--p); cursor: pointer; padding: 5px; }
        .send-btn { background: var(--p); color: #000; width: 42px; height: 42px; border-radius: 50%; display: flex; align-items: center; justify-content: center; border: none; cursor: pointer; }

        /* Bottom Nav (iOS Style) */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 75px; background: #050710; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-link { text-align: center; color: var(--text-dim); font-size: 11px; font-weight: 600; cursor: pointer; transition: 0.3s; }
        .nav-link.active { color: var(--p); }
        .nav-link i { font-size: 22px; display: block; margin-bottom: 3px; }

        /* Login Screen */
        .login-card { text-align: center; padding: 40px 20px; }
        .login-card input { width: 100%; padding: 18px; border-radius: 20px; background: var(--card); border: 1px solid var(--p); color: white; text-align: center; outline: none; font-size: 18px; margin-bottom: 20px; }
        .login-card button { width: 100%; padding: 18px; border-radius: 20px; background: var(--p); border: none; color: #000; font-weight: 800; font-size: 18px; cursor: pointer; box-shadow: 0 10px 20px rgba(0,229,255,0.2); }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1 id="headTitle">INFINITY APEX</h1>
        <div style="display:flex; gap:15px; align-items:center;">
            <i class="fa-solid fa-circle-user" style="font-size:22px; color:var(--p)"></i>
            <i class="fa-solid fa-power-off" onclick="logout()" style="color:#ff4444"></i>
        </div>
    </header>

    <div class="story-bar" id="storyBar" style="display:none;">
        <div class="story" onclick="document.getElementById('stInp').click()">
            <img src="https://api.dicebear.com/7.x/identicon/svg?seed=Me">
            <div class="add"><i class="fa-solid fa-plus"></i></div>
            <input type="file" id="stInp" style="display:none" onchange="upStatus(event)">
        </div>
        <div id="stList" style="display:flex; gap:15px;"></div>
    </div>

    <div class="viewport" id="scrollArea">
        <div id="v-login" class="view active">
            <div class="login-card">
                <div style="font-size:80px; margin-bottom:10px;">💎</div>
                <h2>Quantum Login</h2>
                <p style="color:var(--text-dim); margin-bottom:30px;">Initialize secure node connection.</p>
                <input type="text" id="uInp" placeholder="Enter Nickname">
                <button onclick="login()">CONNECT NOW</button>
            </div>
        </div>

        <div id="v-home" class="view">
            <div style="background:var(--card); padding:20px; border-radius:22px; border:1px solid var(--border); margin-bottom:20px;">
                <h3 id="greet" style="margin:0">Hi, Networker</h3>
                <span style="font-size:12px; color:var(--p)">● Connection Encrypted</span>
            </div>
            <h4 style="margin-bottom:15px; color:var(--text-dim);">Online Directory</h4>
            <div id="uList"></div>
        </div>

        <div id="v-chat" class="view">
            <div id="chatBox"></div>
            <div id="typing" style="font-size:11px; color:var(--p); margin-top:5px; display:none;">Typing...</div>
        </div>
    </div>

    <div class="dock" id="dock" style="display:none;">
        <input type="file" id="fInp" style="display:none" onchange="sendImg(event)">
        <i class="fa-solid fa-image" onclick="document.getElementById('fInp').click()"></i>
        <input type="text" id="mInp" placeholder="Type message..." oninput="isTyping()">
        <button class="send-btn" onclick="send()">
            <i class="fa-solid fa-paper-plane"></i>
        </button>
    </div>

    <nav id="bNav" style="display:none;">
        <div class="nav-link active" id="hBtn" onclick="nav('home', 'hBtn')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-link" id="cBtn" onclick="nav('chat', 'cBtn')"><i class="fa-solid fa-comment-dots"></i><span>Chats</span></div>
        <div class="nav-link" onclick="alert('Profile Coming Soon')"><i class="fa-solid fa-bolt"></i><span>Activity</span></div>
        <div class="nav-link" onclick="logout()"><i class="fa-solid fa-gear"></i><span>Settings</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, onDisconnect, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("apex_final_user");
let room = "Global_Lobby";
let isP = false;
let tTimer;

if(user) startApp();

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(!v) return;
    user = v;
    localStorage.setItem("apex_final_user", v);
    startApp();
};

function startApp() {
    document.getElementById("v-login").classList.remove("active");
    document.getElementById("v-home").classList.add("active");
    document.getElementById("bNav").style.display = "flex";
    document.getElementById("storyBar").style.display = "flex";
    document.getElementById("greet").innerText = "Welcome, " + user;
    
    set(ref(db, "active_nodes/" + user), true);
    onDisconnect(ref(db, "active_nodes/" + user)).remove();
    loadDirectory();
}

window.nav = (vId, bId) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.getElementById("v-" + vId).classList.add("active");
    document.querySelectorAll('.nav-link').forEach(n => n.classList.remove('active'));
    document.getElementById(bId).classList.add('active');
    document.getElementById("dock").style.display = (vId === 'chat' ? 'flex' : 'none');
    if(vId === 'chat') syncMsgs();
};

function loadDirectory() {
    onValue(ref(db, "active_nodes"), snap => {
        const l = document.getElementById("uList");
        l.innerHTML = "";
        snap.forEach(u => {
            if(u.key !== user) {
                const div = document.createElement("div");
                div.style = "background:var(--card); padding:16px; border-radius:18px; display:flex; align-items:center; gap:12px; margin-bottom:10px; border:1px solid var(--border);";
                div.innerHTML = `<div style="width:42px;height:42px;background:var(--p);border-radius:50%;display:flex;align-items:center;justify-content:center;color:#000;font-weight:bold">${u.key[0].toUpperCase()}</div>
                                 <div style="flex:1"><b>${u.key}</b><br><small style="color:var(--p)">● Active Now</small></div>`;
                div.onclick = () => {
                    room = [user, u.key].sort().join("::");
                    isP = true;
                    document.getElementById("headTitle").innerText = u.key;
                    nav('chat', 'cBtn');
                };
                l.appendChild(div);
            }
        });
    });

    onValue(ref(db, "typing_nodes/" + room), snap => {
        const t = document.getElementById("typing");
        if(snap.val() && snap.val() !== user) t.style.display = "block";
        else t.style.display = "none";
    });
}

window.isTyping = () => {
    set(ref(db, "typing_nodes/" + room), user);
    clearTimeout(tTimer);
    tTimer = setTimeout(() => set(ref(db, "typing_nodes/" + room), null), 2000);
};

function syncMsgs() {
    const path = isP ? "messages_final_p/" + room : "messages_final_g/main";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("chatBox");
        box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const node = document.createElement("div");
            node.className = "msg-node " + (d.from === user ? "me" : "them");
            node.innerHTML = `
                <div class="bubble">
                    ${d.text ? d.text : ''}
                    ${d.img ? `<img src="${d.img}">` : ''}
                </div>
                <div class="meta">${d.from} • Now ${d.from === user ? '<i class="fa-solid fa-check-double" style="color:var(--p)"></i><i class="fa-solid fa-trash del-icon" onclick="deleteMsg(\''+m.key+'\')"></i>' : ''}</div>
            `;
            box.appendChild(node);
        });
        setTimeout(() => {
            const sc = document.getElementById('scrollArea');
            sc.scrollTop = sc.scrollHeight;
        }, 100);
    });
}

window.send = () => {
    const i = document.getElementById("mInp");
    if(!i.value.trim()) return;
    const path = isP ? "messages_final_p/" + room : "messages_final_g/main";
    push(ref(db, path), { from: user, text: i.value });
    i.value = "";
};

window.sendImg = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const path = isP ? "messages_final_p/" + room : "messages_final_g/main";
        push(ref(db, path), { from: user, img: reader.result });
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.deleteMsg = (key) => {
    const path = isP ? "messages_final_p/" + room : "messages_final_g/main";
    remove(ref(db, path + "/" + key));
};

window.upStatus = (e) => {
    const reader = new FileReader();
    reader.onload = () => { 
        set(ref(db, "status_final/" + user), { img: reader.result }); 
        alert("Status Broadcasted!");
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
