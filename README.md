<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity X | Super-App</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --accent: #007bff; --bg: #000000; --card: #111111; --border: #222222; --text: #ffffff; --wa: #25D366; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Outfit', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* LUXURY AUTH */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 10000; display: flex; align-items: center; justify-content: center; padding: 30px; }
        .auth-card { width: 100%; max-width: 380px; text-align: center; }
        .auth-card h1 { font-size: 50px; font-weight: 800; letter-spacing: -2px; background: linear-gradient(to bottom, #fff, #444); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .auth-input { width: 100%; padding: 20px; background: #111; border: 1px solid #333; border-radius: 20px; color: #fff; margin: 20px 0; outline: none; font-size: 16px; }
        .btn-join { width: 100%; padding: 20px; background: #fff; border: none; border-radius: 20px; color: #000; font-weight: 800; cursor: pointer; }

        /* APP LAYOUT */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; box-shadow: 0 0 50px rgba(0,0,0,1); }
        header { padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.8); backdrop-filter: blur(20px); border-bottom: 1px solid var(--border); z-index: 100; }
        header h2 { font-weight: 800; font-size: 24px; letter-spacing: -1px; }

        /* VIEWPORT */
        .viewport { flex: 1; overflow-y: auto; scroll-behavior: smooth; -webkit-overflow-scrolling: touch; padding-bottom: 100px; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; width: 100%; }
        .view.active { display: block; animation: zoomIn 0.3s ease; }

        /* POST CARDS */
        .post { background: var(--card); margin-bottom: 15px; border-bottom: 1px solid var(--border); }
        .p-header { padding: 12px 15px; display: flex; align-items: center; gap: 12px; }
        .p-avatar { width: 42px; height: 42px; border-radius: 50%; object-fit: cover; border: 1.5px solid var(--accent); }
        .p-media { width: 100%; min-height: 250px; background: #080808; display: flex; align-items: center; justify-content: center; overflow: hidden; }
        .p-media img, .p-media video { width: 100%; object-fit: cover; }
        .p-actions { padding: 12px 18px; display: flex; gap: 25px; font-size: 24px; color: #ccc; }
        .p-caption { padding: 0 18px 15px; font-size: 14px; color: #eee; }

        /* PRIVATE CHAT SYSTEM */
        #dmOverlay { position: fixed; inset: 0; background: #000; z-index: 5000; display: none; flex-direction: column; }
        .chat-header { padding: 15px; display: flex; align-items: center; gap: 15px; border-bottom: 1px solid #222; }
        #chatBox { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 10px; background: #000; }
        .bubble { padding: 12px 16px; border-radius: 20px; max-width: 75%; font-size: 14px; line-height: 1.4; }
        .bubble.me { background: var(--accent); align-self: flex-end; border-bottom-right-radius: 4px; }
        .bubble.them { background: #222; align-self: flex-start; border-bottom-left-radius: 4px; }

        /* NAV BAR */
        nav { position: absolute; bottom: 0; width: 100%; height: 75px; background: rgba(0,0,0,0.9); border-top: 1px solid #222; display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        .n-btn { font-size: 26px; color: #555; cursor: pointer; position: relative; }
        .n-btn.active { color: #fff; }
        .n-btn.active::after { content: ''; position: absolute; bottom: -12px; left: 50%; transform: translateX(-50%); width: 5px; height: 5px; background: #fff; border-radius: 50%; }

        @keyframes zoomIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>Infinity X</h1>
        <p style="color: #666;">The Future of Social Connection</p>
        <input type="text" id="uInp" class="auth-input" placeholder="Your Username">
        <button onclick="login()" class="btn-join">Get Started</button>
    </div>
</div>

<div id="dmOverlay">
    <div class="chat-header">
        <i class="fa-solid fa-chevron-left" onclick="closeDM()" style="font-size: 20px;"></i>
        <img id="dmPfp" src="" style="width:40px; height:40px; border-radius:50%; object-fit: cover;">
        <b id="dmUser">Username</b>
    </div>
    <div id="chatBox"></div>
    <div style="padding:15px; display:flex; gap:10px; background:#111;">
        <input type="text" id="dmMsg" class="auth-input" style="margin:0; flex:1;" placeholder="Message...">
        <button onclick="sendDM()" style="background:var(--accent); border:none; width:55px; border-radius:20px; color:#fff;"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2 style="font-style: italic;">Infinity X</h2>
        <div style="display:flex; gap:22px; font-size:22px;">
            <label for="postUp"><i class="fa-regular fa-square-plus"></i></label>
            <input type="file" id="postUp" hidden accept="image/*,video/*" onchange="handlePost(event)">
            <i class="fa-regular fa-paper-plane" onclick="tab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="mainPort">
        <div id="v-home" class="view active"><div id="feed"></div></div>
        
        <div id="v-chat" class="view" style="padding: 10px 0;">
            <div style="padding:15px; color:#666; font-weight:800; font-size:12px;">MESSAGES</div>
            <div id="usersList"></div>
        </div>

        <div id="v-profile" class="view" style="text-align:center; padding-top:40px;">
            <label for="pfpUp"><img id="myPfp" src="" style="width:130px; height:130px; border-radius:50%; border:4px solid #fff; object-fit:cover;"></label>
            <input type="file" id="pfpUp" hidden onchange="updatePfp(event)">
            <h2 id="myName" style="margin:20px 0;">User</h2>
            <div style="display:flex; justify-content:center; gap:30px; margin-top:30px;">
                <div><b>502</b><br><small style="color:gray">Posts</small></div>
                <div><b>12k</b><br><small style="color:gray">Followers</small></div>
                <div><b>840</b><br><small style="color:gray">Following</small></div>
            </div>
            <button onclick="localStorage.clear(); location.reload();" style="margin-top:60px; color:#ff4d4d; border:none; background:none; font-weight:800;">LOGOUT</button>
        </div>
    </div>

    <nav>
        <div class="n-btn active" onclick="tab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="n-btn"><i class="fa-solid fa-magnifying-glass"></i></div>
        <div class="n-btn" onclick="tab('chat')"><i class="fa-solid fa-comment"></i></div>
        <div class="n-btn" onclick="tab('profile')"><i class="fa-solid fa-user"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);
let user = localStorage.getItem("inf_x_user");
let targetDM = null;

if(user) { document.getElementById("authBox").style.display="none"; initApp(); }

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(v) { localStorage.setItem("inf_x_user", v); location.reload(); }
};

function initApp() {
    document.getElementById("myName").innerText = user;

    // 1. Sync My PFP
    onValue(ref(db, `users/${user}/pfp`), (s) => {
        document.getElementById("myPfp").src = s.val() || "https://cdn-icons-png.flaticon.com/512/149/149071.png";
    });

    // 2. Load Feed (Realtime)
    onValue(ref(db, 'inf_x_feed'), (snap) => {
        const row = document.getElementById("feed"); row.innerHTML = "";
        snap.forEach(s => {
            const p = s.val();
            row.innerHTML = `
                <div class="post">
                    <div class="p-header" onclick="openDM('${p.user}', '${p.uPfp}')">
                        <img src="${p.uPfp}" class="p-avatar">
                        <b>${p.user}</b>
                    </div>
                    <div class="p-media">
                        ${p.type==='video' ? `<video src="${p.media}" controls loop muted autoplay></video>` : `<img src="${p.media}">`}
                    </div>
                    <div class="p-actions">
                        <i class="fa-regular fa-heart" onclick="like('${s.key}')"></i>
                        <i class="fa-regular fa-comment"></i>
                        <i class="fa-regular fa-paper-plane"></i>
                    </div>
                    <div class="p-caption"><b>${p.user}</b> ${p.text}</div>
                </div>` + row.innerHTML;
        });
    });

    // 3. Sync All Users for Direct Chat
    onValue(ref(db, 'users'), (snap) => {
        const list = document.getElementById("usersList"); list.innerHTML = "";
        snap.forEach(s => {
            if(s.key !== user) {
                list.innerHTML += `
                <div class="p-header" style="padding:15px; cursor:pointer;" onclick="openDM('${s.key}', '${s.val().pfp}')">
                    <img src="${s.val().pfp || 'https://cdn-icons-png.flaticon.com/512/149/149071.png'}" class="p-avatar">
                    <div><b>${s.key}</b><br><small style="color:gray">Click to chat privately</small></div>
                </div>`;
            }
        });
    });
}

// MEDIA UPLOAD
window.handlePost = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    const type = file.type.startsWith('video') ? 'video' : 'image';
    reader.onload = () => {
        const cap = prompt("Caption?");
        const pfp = document.getElementById("myPfp").src;
        push(ref(db, 'inf_x_feed'), { user, text: cap||"", media: reader.result, type, uPfp: pfp });
    };
    reader.readAsDataURL(file);
};

// PRIVATE CHAT LOGIC
window.openDM = (target, pfp) => {
    targetDM = target;
    document.getElementById("dmUser").innerText = target;
    document.getElementById("dmPfp").src = pfp || "https://cdn-icons-png.flaticon.com/512/149/149071.png";
    document.getElementById("dmOverlay").style.display = "flex";
    const chatId = [user, target].sort().join("_");
    onValue(ref(db, `inf_x_dms/${chatId}`), (snap) => {
        const box = document.getElementById("chatBox"); box.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.sender === user;
            box.innerHTML += `<div class="bubble ${isMe?'me':'them'}">${m.text}</div>`;
        });
        box.scrollTop = box.scrollHeight;
    });
};
window.sendDM = () => {
    const i = document.getElementById("dmMsg");
    if(!i.value || !targetDM) return;
    const chatId = [user, targetDM].sort().join("_");
    push(ref(db, `inf_x_dms/${chatId}`), { sender: user, text: i.value });
    i.value = "";
};
window.closeDM = () => document.getElementById("dmOverlay").style.display = "none";

window.updatePfp = (e) => {
    const reader = new FileReader();
    reader.onload = () => set(ref(db, `users/${user}/pfp`), reader.result);
    reader.readAsDataURL(e.target.files[0]);
};

window.tab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.n-btn').forEach(n => n.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    const m = {'home':0, 'chat':2, 'profile':3};
    document.querySelectorAll('.n-btn')[m[t]].classList.add('active');
};
</script>
</body>
</html>
