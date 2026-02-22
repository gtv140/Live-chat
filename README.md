<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity | Ultimate Social</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --wa: #25D366; --bg: #000; --card: #121212; --border: #262626; --text: #fff; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* AUTH */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 25px; }
        .auth-card { width: 100%; max-width: 360px; text-align: center; }
        .auth-input { width: 100%; padding: 18px; background: #1a1a1a; border: 1px solid var(--border); border-radius: 15px; color: #fff; margin-bottom: 15px; outline: none; }

        /* SHELL */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; background: var(--bg); }
        header { padding: 15px; display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.8); backdrop-filter: blur(15px); border-bottom: 1px solid var(--border); z-index: 100; }
        
        /* SCROLLABLE VIEWPORT */
        .viewport { flex: 1; overflow-y: auto; overflow-x: hidden; -webkit-overflow-scrolling: touch; scroll-behavior: smooth; padding-bottom: 100px; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; padding: 0; width: 100%; }
        .view.active { display: block; animation: fadeIn 0.3s ease; }

        /* FEED & POSTS */
        .post { background: var(--card); margin-bottom: 10px; border-bottom: 1px solid var(--border); }
        .p-info { padding: 12px; display: flex; align-items: center; gap: 10px; cursor: pointer; }
        .p-info img { width: 40px; height: 40px; border-radius: 50%; object-fit: cover; border: 2px solid var(--p); }
        .media-box { width: 100%; min-height: 200px; background: #0a0a0a; }
        .media-box img, .media-box video { width: 100%; display: block; }
        .p-actions { padding: 12px 15px; display: flex; gap: 20px; font-size: 22px; color: #888; }
        .react-btn:active { transform: scale(1.4); color: #ff0055; }
        .comment-sec { padding: 0 15px 15px; font-size: 13px; border-top: 1px solid #1a1a1a; }

        /* CHAT SYSTEMS */
        .chat-list-item { padding: 15px; display: flex; align-items: center; gap: 15px; border-bottom: 1px solid var(--border); cursor: pointer; }
        .chat-list-item:active { background: #1a1a1a; }
        .chat-bubble { padding: 10px 15px; border-radius: 18px; max-width: 80%; font-size: 14px; margin: 4px 15px; position: relative; }
        .chat-bubble.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 2px; }
        .chat-bubble.them { background: #262626; align-self: flex-start; border-bottom-left-radius: 2px; }

        /* NAV */
        nav { position: absolute; bottom: 0; width: 100%; height: 75px; background: rgba(10,10,10,0.95); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        .n-btn { font-size: 24px; color: #666; cursor: pointer; transition: 0.3s; }
        .n-btn.active { color: var(--p); transform: translateY(-5px); }

        #chatDock { position: fixed; bottom: 75px; width: 100%; max-width: 500px; padding: 12px; background: #121212; display: none; gap: 10px; border-top: 1px solid var(--border); z-index: 2000; }
        .msg-inp { flex: 1; padding: 12px 18px; border-radius: 25px; background: #222; border: none; color: #fff; outline: none; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1 style="color: var(--p); font-size: 40px; margin-bottom: 20px;">Infinity</h1>
        <input type="text" id="uInp" class="auth-input" placeholder="Your Name">
        <button onclick="login()" style="width: 100%; padding: 18px; border-radius: 15px; background: var(--p); border: none; color: #fff; font-weight: 800;">Get Started</button>
    </div>
</div>

<div id="directChat" style="display:none; position:fixed; inset:0; background:black; z-index:5000; flex-direction:column;">
    <header>
        <div style="display:flex; align-items:center; gap:10px;">
            <i class="fa-solid fa-arrow-left" onclick="closeDM()"></i>
            <img id="dmPfp" src="" style="width:35px; height:35px; border-radius:50%;">
            <b id="dmName">Chat</b>
        </div>
    </header>
    <div id="dmBox" style="flex:1; overflow-y:auto; display:flex; flex-direction:column; padding:10px 0;"></div>
    <div style="padding:15px; display:flex; gap:10px; background:#111;">
        <input type="text" id="dmInp" class="msg-inp" placeholder="Type message...">
        <button onclick="sendDM()" style="background:var(--p); border:none; border-radius:50%; width:45px; height:45px; color:white;"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2 style="font-weight:900; letter-spacing:-1px;">INFINITY</h2>
        <div style="display:flex; gap:20px; font-size:22px;">
            <label for="fInp"><i class="fa-solid fa-plus-square"></i></label>
            <input type="file" id="fInp" hidden onchange="upload(event)">
            <i class="fa-solid fa-bell"></i>
        </div>
    </header>

    <div class="viewport" id="vPort">
        <div id="v-home" class="view active"><div id="feedRow"></div></div>

        <div id="v-chat" class="view">
            <div onclick="openGroup()" class="chat-list-item" style="background: #1a1a1a; margin: 10px; border-radius: 15px; border: none;">
                <div style="width:50px; height:50px; border-radius:50%; background:var(--wa); display:flex; align-items:center; justify-content:center; font-size:24px;"><i class="fa-solid fa-users"></i></div>
                <div><b>Public Square</b><br><small style="color:var(--wa)">Join global discussion</small></div>
            </div>
            <div style="padding: 15px 20px; color: #666; font-size: 12px; font-weight: 800;">DIRECT MESSAGES</div>
            <div id="usersList"></div>
        </div>

        <div id="v-profile" class="view" style="text-align:center; padding-top:40px;">
            <label for="pfpInp"><img id="myPfp" src="" style="width:120px; height:120px; border-radius:50%; border:3px solid var(--p); object-fit:cover;"></label>
            <input type="file" id="pfpInp" hidden onchange="updatePfp(event)">
            <h2 id="myName" style="margin-top:15px;">User</h2>
            <button onclick="localStorage.clear(); location.reload();" style="margin-top:50px; color:red; background:none; border:none; font-weight:800;">Log Out</button>
        </div>
    </div>

    <div id="chatDock">
        <input type="text" id="mInp" class="msg-inp" placeholder="Message to group...">
        <i class="fa-solid fa-paper-plane" onclick="sendGroup()" style="color:var(--p); font-size:22px; padding:10px;"></i>
    </div>

    <nav>
        <div class="n-btn active" onclick="tab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="n-btn" onclick="tab('chat')"><i class="fa-solid fa-message"></i></div>
        <div class="n-btn" onclick="tab('profile')"><i class="fa-solid fa-user-circle"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);
let user = localStorage.getItem("inf_v6_user");
let activeDM = null;

if(user) { document.getElementById("authBox").style.display="none"; start(); }

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(v) { localStorage.setItem("inf_v6_user", v); location.reload(); }
};

function start() {
    document.getElementById("myName").innerText = user;
    
    // 1. Sync PFP
    onValue(ref(db, `users/${user}/pfp`), (s) => {
        document.getElementById("myPfp").src = s.val() || "https://cdn-icons-png.flaticon.com/512/149/149071.png";
    });

    // 2. Sync Global Feed
    onValue(ref(db, 'v6_feed'), (snap) => {
        const row = document.getElementById("feedRow"); row.innerHTML = "";
        snap.forEach(s => {
            const p = s.val();
            row.innerHTML = `
                <div class="post">
                    <div class="p-info" onclick="openPrivate('${p.user}')">
                        <img src="${p.uPfp}">
                        <div><b>${p.user}</b><br><small style="color:gray">Just now</small></div>
                    </div>
                    <div class="media-box">
                        ${p.type==='video' ? `<video src="${p.media}" controls></video>` : `<img src="${p.media}">`}
                    </div>
                    <div class="p-actions">
                        <i class="fa-solid fa-heart react-btn" onclick="like('${s.key}')"> <small>${p.likes||0}</small></i>
                        <i class="fa-solid fa-comment" onclick="addComment('${s.key}')"></i>
                    </div>
                    <div class="p-text" style="padding:0 15px 10px;">${p.text}</div>
                    <div class="comment-sec" id="comm-${s.key}">${renderComments(p.comments)}</div>
                </div>` + row.innerHTML;
        });
    });

    // 3. User List for DMs
    onValue(ref(db, 'users'), (snap) => {
        const list = document.getElementById("usersList"); list.innerHTML = "";
        snap.forEach(s => {
            if(s.key !== user) {
                list.innerHTML += `
                <div class="chat-list-item" onclick="openPrivate('${s.key}')">
                    <img src="${s.val().pfp || 'https://cdn-icons-png.flaticon.com/512/149/149071.png'}" style="width:45px; height:45px; border-radius:50%;">
                    <div><b>${s.key}</b><br><small style="color:gray">Online</small></div>
                </div>`;
            }
        });
    });
}

// FEED ACTIONS
window.upload = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    const type = file.type.startsWith('video') ? 'video' : 'image';
    reader.onload = () => {
        const t = prompt("Caption?");
        const pfp = document.getElementById("myPfp").src;
        push(ref(db, 'v6_feed'), { user, text: t||"", media: reader.result, type, uPfp: pfp, likes: 0 });
    };
    reader.readAsDataURL(file);
};
window.like = (id) => runTransaction(ref(db, `v6_feed/${id}/likes`), (c) => (c || 0) + 1);
window.addComment = (id) => {
    const c = prompt("Write comment...");
    if(c) push(ref(db, `v6_feed/${id}/comments`), { user, text: c });
};
function renderComments(obj) {
    if(!obj) return "";
    return Object.values(obj).map(v => `<div><b>${v.user}:</b> ${v.text}</div>`).join("");
}

// PRIVATE MESSAGING SYSTEM
window.openPrivate = (target) => {
    activeDM = target;
    document.getElementById("dmName").innerText = target;
    document.getElementById("directChat").style.display = "flex";
    const chatID = [user, target].sort().join("_");
    onValue(ref(db, `v6_dms/${chatID}`), (snap) => {
        const box = document.getElementById("dmBox"); box.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.sender === user;
            box.innerHTML += `<div class="chat-bubble ${isMe?'me':'them'}">${m.text}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
};
window.sendDM = () => {
    const i = document.getElementById("dmInp");
    if(!i.value || !activeDM) return;
    const chatID = [user, activeDM].sort().join("_");
    push(ref(db, `v6_dms/${chatID}`), { sender: user, text: i.value });
    i.value = "";
};
window.closeDM = () => document.getElementById("directChat").style.display = "none";

// PUBLIC GROUP SYSTEM
window.openGroup = () => {
    tab('chat');
    document.getElementById("chatDock").style.display = "flex";
    onValue(ref(db, 'v6_public'), (snap) => {
        const v = document.getElementById("vPort"); 
        // Showing group messages in chat view when group is active
    });
};
window.sendGroup = () => {
    const i = document.getElementById("mInp");
    if(i.value) { push(ref(db, 'v6_public'), { user, text: i.value }); i.value = ""; }
};

window.updatePfp = (e) => {
    const reader = new FileReader();
    reader.onload = () => set(ref(db, `users/${user}/pfp`), reader.result);
    reader.readAsDataURL(e.target.files[0]);
};

window.tab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.n-btn').forEach(n => n.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    document.getElementById('chatDock').style.display = (t === 'chat' ? 'flex' : 'none');
    const m = {'home':0, 'chat':1, 'profile':2};
    document.querySelectorAll('.n-btn')[m[t]].classList.add('active');
};
</script>
</body>
</html>
