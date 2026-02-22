<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Infinity OS | Premium</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { 
            --p: #007AFF; --bg: #000000; --card: #121212; --border: #262626; 
            --text: #ffffff; --sub: #8e8e93; --insta: linear-gradient(45deg, #f09433, #e6683c, #dc2743, #cc2366, #bc1888);
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* --- MODERN LOGIN --- */
        #authBox { position: fixed; inset: 0; background: var(--bg); z-index: 10000; display: flex; align-items: center; justify-content: center; padding: 40px; }
        .auth-card { width: 100%; max-width: 400px; text-align: center; }
        .auth-logo { width: 80px; height: 80px; background: var(--p); border-radius: 22px; margin: 0 auto 20px; display: flex; align-items: center; justify-content: center; font-size: 40px; box-shadow: 0 20px 40px rgba(0,122,255,0.3); }
        .auth-input { width: 100%; padding: 20px; background: #1c1c1e; border: 1px solid var(--border); border-radius: 18px; color: #fff; margin-bottom: 20px; outline: none; font-size: 16px; transition: 0.3s; }
        .auth-input:focus { border-color: var(--p); background: #2c2c2e; }
        .btn-join { width: 100%; padding: 20px; background: var(--p); border: none; border-radius: 18px; color: #fff; font-weight: 800; cursor: pointer; font-size: 16px; }

        /* --- APP SHELL --- */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
        header { padding: 50px 20px 15px; display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.85); backdrop-filter: blur(20px); z-index: 1000; position: sticky; top: 0; }
        header h1 { font-size: 26px; font-weight: 800; letter-spacing: -1.5px; background: var(--insta); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* --- VIEWPORT (SMOOTH SCROLL) --- */
        .viewport { flex: 1; overflow-y: scroll; scroll-behavior: smooth; -webkit-overflow-scrolling: touch; padding-bottom: 110px; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; width: 100%; }
        .view.active { display: block; animation: slideIn 0.35s cubic-bezier(0.4, 0, 0.2, 1); }

        /* --- INSTA STORIES --- */
        .stories { display: flex; gap: 15px; padding: 15px 20px; overflow-x: auto; border-bottom: 0.5px solid var(--border); }
        .s-ring { min-width: 66px; height: 66px; border-radius: 50%; padding: 2.5px; background: var(--insta); cursor: pointer; }
        .s-ring.seen { background: var(--border); }
        .s-img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; border: 2px solid #000; background: #111; }

        /* --- FEED CARDS --- */
        .post { background: #000; margin-bottom: 25px; border-bottom: 0.5px solid var(--border); }
        .p-user { padding: 12px 15px; display: flex; align-items: center; gap: 10px; font-weight: 600; font-size: 14px; }
        .p-user img { width: 34px; height: 34px; border-radius: 50%; object-fit: cover; }
        .p-media { width: 100%; min-height: 300px; background: #050505; position: relative; border-radius: 4px; overflow: hidden; }
        .p-media img, .p-media video { width: 100%; display: block; }
        .p-actions { padding: 15px; display: flex; gap: 20px; font-size: 24px; color: #fff; }
        .p-actions i { cursor: pointer; transition: 0.2s; }
        .p-actions i:active { transform: scale(1.3); color: #ff3b30; }

        /* --- DM OVERLAY --- */
        #dmBox { position: fixed; inset: 0; background: #000; z-index: 5000; display: none; flex-direction: column; }
        .dm-head { padding: 50px 20px 15px; display: flex; align-items: center; gap: 15px; border-bottom: 0.5px solid var(--border); }
        .dm-chats { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 12px; }
        .msg { padding: 12px 18px; border-radius: 22px; max-width: 75%; font-size: 15px; line-height: 1.4; }
        .msg.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 4px; }
        .msg.them { background: #26262e; align-self: flex-start; border-bottom-left-radius: 4px; }

        /* --- TAB NAV --- */
        nav { position: absolute; bottom: 0; width: 100%; height: 85px; background: rgba(10,10,10,0.95); backdrop-filter: blur(30px); border-top: 0.5px solid var(--border); display: flex; justify-content: space-around; padding-top: 10px; z-index: 2000; }
        .n-btn { display: flex; flex-direction: column; align-items: center; color: var(--sub); cursor: pointer; gap: 5px; }
        .n-btn i { font-size: 24px; }
        .n-btn span { font-size: 10px; font-weight: 600; }
        .n-btn.active { color: #fff; }
        .n-btn.active i { transform: scale(1.1); transition: 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); }

        /* --- ANIMATIONS --- */
        @keyframes slideIn { from { opacity: 0; transform: translateX(10px); } to { opacity: 1; transform: translateX(0); } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <div class="auth-logo"><i class="fa-solid fa-infinity" style="color:white;"></i></div>
        <h1>Infinity OS</h1>
        <p style="color: #666; margin-bottom: 30px;">Premium Social Experience</p>
        <input type="text" id="uInp" class="auth-input" placeholder="Username">
        <button onclick="login()" class="btn-join">Continue</button>
    </div>
</div>

<div id="dmBox">
    <div class="dm-head">
        <i class="fa-solid fa-chevron-left" onclick="closeDM()" style="font-size: 22px;"></i>
        <img id="dmPfp" src="" style="width:38px; height:38px; border-radius:50%; object-fit:cover;">
        <b id="dmUser" style="font-size:18px;">Chat</b>
    </div>
    <div class="dm-chats" id="chatFlow"></div>
    <div style="padding:15px 20px 40px; display:flex; gap:12px; background:#000;">
        <input type="text" id="dmMsg" class="auth-input" style="margin:0; border-radius:30px; padding:15px 20px;" placeholder="Message...">
        <button onclick="sendDM()" style="background:var(--p); border:none; width:50px; height:50px; border-radius:50%; color:#fff;"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h1>Infinity</h1>
        <div style="display:flex; gap:22px; font-size:22px;">
            <label for="mediaPost"><i class="fa-regular fa-square-plus"></i></label>
            <input type="file" id="mediaPost" hidden onchange="handlePost(event)">
            <i class="fa-regular fa-heart"></i>
            <i class="fa-regular fa-paper-plane" onclick="tab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="vPort">
        <div id="v-home" class="view active">
            <div class="stories" id="storyRow"></div>
            <div id="feed"></div>
        </div>

        <div id="v-chat" class="view">
            <div style="padding:20px; font-weight:800; font-size:20px;">Messages</div>
            <div id="usersList"></div>
        </div>

        <div id="v-profile" class="view" style="text-align:center; padding:40px 20px;">
            <label for="pfpUp">
                <div class="s-ring" style="width:110px; height:110px; margin:0 auto; padding:4px;">
                    <img id="myPfp" src="" class="s-img">
                </div>
            </label>
            <input type="file" id="pfpUp" hidden onchange="updatePfp(event)">
            <h2 id="myName" style="margin-top:20px; font-size:24px;">Username</h2>
            <div style="display:flex; justify-content:space-around; margin:40px 0;">
                <div><b>152</b><p style="color:var(--sub); font-size:12px;">Posts</p></div>
                <div><b>8.4k</b><p style="color:var(--sub); font-size:12px;">Followers</p></div>
                <div><b>490</b><p style="color:var(--sub); font-size:12px;">Following</p></div>
            </div>
            <button onclick="localStorage.clear(); location.reload();" style="color:#ff3b30; font-weight:800; border:none; background:none;">Logout Account</button>
        </div>
    </div>

    <nav>
        <div class="n-btn active" onclick="tab('home')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="n-btn"><i class="fa-solid fa-magnifying-glass"></i><span>Explore</span></div>
        <div class="n-btn" onclick="tab('chat')"><i class="fa-solid fa-comment-dots"></i><span>Messages</span></div>
        <div class="n-btn" onclick="tab('profile')"><i class="fa-solid fa-circle-user"></i><span>Profile</span></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);
let me = localStorage.getItem("inf_os_user");
let targetDM = null;

if(me) { document.getElementById("authBox").style.display="none"; run(); }

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(v) { localStorage.setItem("inf_os_user", v); location.reload(); }
};

function run() {
    document.getElementById("myName").innerText = me;
    onValue(ref(db, `users/${me}/pfp`), s => {
        document.getElementById("myPfp").src = s.val() || "https://cdn-icons-png.flaticon.com/512/149/149071.png";
    });

    // Sync Feed
    onValue(ref(db, 'inf_os_feed'), (snap) => {
        const row = document.getElementById("feed"); row.innerHTML = "";
        const story = document.getElementById("storyRow"); story.innerHTML = "";
        snap.forEach(s => {
            const p = s.val();
            story.innerHTML += `<div class="s-ring"><img src="${p.uPfp}" class="s-img"></div>`;
            row.innerHTML = `
                <div class="post">
                    <div class="p-user" onclick="openDM('${p.user}', '${p.uPfp}')">
                        <img src="${p.uPfp}"> <span>${p.user}</span>
                    </div>
                    <div class="p-media">
                        ${p.type==='video' ? `<video src="${p.media}" controls loop muted autoplay></video>` : `<img src="${p.media}">`}
                    </div>
                    <div class="p-actions">
                        <i class="fa-regular fa-heart"></i>
                        <i class="fa-regular fa-comment"></i>
                        <i class="fa-regular fa-paper-plane"></i>
                    </div>
                    <div style="padding:0 15px 15px; font-size:14px;"><b>${p.user}</b> ${p.text}</div>
                </div>` + row.innerHTML;
        });
    });

    // Sync Users for Chat
    onValue(ref(db, 'users'), (snap) => {
        const list = document.getElementById("usersList"); list.innerHTML = "";
        snap.forEach(s => {
            if(s.key !== me) {
                list.innerHTML += `
                <div class="p-user" style="padding:15px 20px; border-bottom:0.5px solid #1a1a1a;" onclick="openDM('${s.key}', '${s.val().pfp}')">
                    <img src="${s.val().pfp || 'https://cdn-icons-png.flaticon.com/512/149/149071.png'}" style="width:50px; height:50px;">
                    <div><b>${s.key}</b><br><small style="color:var(--sub)">Active now</small></div>
                </div>`;
            }
        });
    });
}

window.handlePost = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    const type = file.type.startsWith('video') ? 'video' : 'image';
    reader.onload = () => {
        const cap = prompt("Add a caption...");
        const pfp = document.getElementById("myPfp").src;
        push(ref(db, 'inf_os_feed'), { user: me, text: cap||"", media: reader.result, type, uPfp: pfp });
    };
    reader.readAsDataURL(file);
};

window.openDM = (target, pfp) => {
    targetDM = target;
    document.getElementById("dmUser").innerText = target;
    document.getElementById("dmPfp").src = pfp;
    document.getElementById("dmBox").style.display = "flex";
    const cid = [me, target].sort().join("_");
    onValue(ref(db, `inf_os_dms/${cid}`), (snap) => {
        const flow = document.getElementById("chatFlow"); flow.innerHTML = "";
        snap.forEach(s => {
            const m = s.val();
            flow.innerHTML += `<div class="msg ${m.sender===me?'me':'them'}">${m.text}</div>`;
        });
        flow.scrollTop = flow.scrollHeight;
    });
};

window.sendDM = () => {
    const i = document.getElementById("dmMsg");
    if(!i.value || !targetDM) return;
    const cid = [me, targetDM].sort().join("_");
    push(ref(db, `inf_os_dms/${cid}`), { sender: me, text: i.value });
    i.value = "";
};

window.closeDM = () => document.getElementById("dmBox").style.display = "none";

window.updatePfp = (e) => {
    const reader = new FileReader();
    reader.onload = () => set(ref(db, `users/${me}/pfp`), reader.result);
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
