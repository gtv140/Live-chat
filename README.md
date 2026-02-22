<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Pro</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --bg: #050505; --card: #18191a; --text: #e4e6eb; --border: #3e4042; --accent: #242526; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.2s; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* NEON LOGIN */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 25px; }
        .auth-card { width: 100%; max-width: 380px; text-align: center; }
        .auth-card h1 { font-size: 45px; font-weight: 800; background: linear-gradient(to right, #0084FF, #00dfd8); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 30px; }
        .auth-input { width: 100%; padding: 18px; background: var(--accent); border: 1px solid var(--border); border-radius: 16px; color: #fff; margin-bottom: 20px; outline: none; font-size: 16px; }
        .btn-join { width: 100%; padding: 18px; background: var(--p); border: none; border-radius: 16px; color: #fff; font-weight: 800; cursor: pointer; }

        /* APP LAYOUT */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; background: var(--bg); position: relative; }
        header { padding: 15px; display: flex; justify-content: space-between; align-items: center; background: rgba(5,5,5,0.8); backdrop-filter: blur(10px); z-index: 100; }
        header h2 { font-weight: 800; font-size: 26px; color: var(--p); letter-spacing: -1px; }

        .viewport { flex: 1; overflow-y: auto; padding-bottom: 90px; scrollbar-width: none; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; }
        .view.active { display: block; animation: fadeIn 0.3s ease; }

        /* POSTS */
        .post-card { background: var(--card); margin: 10px; border-radius: 16px; border: 1px solid var(--border); }
        .post-top { padding: 12px; display: flex; align-items: center; justify-content: space-between; }
        .user-tag { display: flex; align-items: center; gap: 10px; }
        .avatar { width: 40px; height: 40px; border-radius: 50%; object-fit: cover; background: var(--p); display: flex; align-items: center; justify-content: center; font-weight: 800; border: 2px solid var(--border); }
        .post-img { width: 100%; max-height: 400px; object-fit: cover; }
        .post-actions { padding: 12px 20px; display: flex; justify-content: space-between; border-top: 1px solid var(--border); color: #b0b3b8; }

        /* CHAT */
        .chat-container { padding: 20px; display: flex; flex-direction: column; gap: 15px; }
        .msg-row { display: flex; gap: 10px; align-items: flex-end; }
        .msg-row.me { flex-direction: row-reverse; }
        .msg { padding: 12px 18px; border-radius: 20px; max-width: 70%; font-size: 14px; }
        .msg.me { background: var(--p); color: white; border-bottom-right-radius: 4px; }
        .msg.them { background: var(--accent); border-bottom-left-radius: 4px; }

        /* PROFILE UI */
        .pfp-main { width: 110px; height: 110px; border-radius: 50%; border: 4px solid var(--p); object-fit: cover; margin: 0 auto; display: block; cursor: pointer; }

        /* NAV */
        nav { position: absolute; bottom: 0; width: 100%; height: 75px; background: var(--accent); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--border); z-index: 100; }
        .nav-btn { font-size: 24px; color: #b0b3b8; cursor: pointer; }
        .nav-btn.active { color: var(--p); }

        #chatDock { position: absolute; bottom: 75px; width: 100%; padding: 15px; background: var(--card); display: none; gap: 10px; border-top: 1px solid var(--border); }
        #chatDock.active { display: flex; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>Live Connect</h1>
        <input type="text" id="uInput" class="auth-input" placeholder="Your Name...">
        <button class="btn-join" onclick="joinNet()">Start Connecting</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2>Live Connect</h2>
        <div style="display:flex; gap:20px; font-size:22px;">
            <label for="postImg"><i class="fa-solid fa-square-plus"></i></label>
            <input type="file" id="postImg" hidden accept="image/*" onchange="uploadPost(event)">
            <i class="fa-brands fa-facebook-messenger" onclick="tab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="vPort">
        <div id="v-home" class="view active"><div id="feed"></div></div>
        
        <div id="v-chat" class="view">
            <div class="chat-container" id="chats"></div>
        </div>

        <div id="v-profile" class="view" style="padding:40px 20px; text-align:center;">
            <label for="pfpEdit">
                <img src="" class="pfp-main" id="myPfp" alt="?">
            </label>
            <input type="file" id="pfpEdit" hidden accept="image/*" onchange="updatePfp(event)">
            <h2 id="myName" style="margin:15px 0;">User</h2>
            <p style="opacity:0.5; font-size:14px; margin-bottom:20px;">Tap photo to change Profile Picture</p>
            <button onclick="logout()" style="color:red; background:none; border:none; font-weight:800;">Log Out</button>
        </div>
    </div>

    <div id="chatDock">
        <input type="text" id="mInput" style="flex:1; padding:12px; border-radius:25px; background:var(--accent); border:none; color:#fff; outline:none;" placeholder="Message...">
        <i class="fa-solid fa-paper-plane" onclick="sendMsg()" style="color:var(--p); font-size:22px;"></i>
    </div>

    <nav>
        <div class="nav-btn active" onclick="tab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="nav-btn" onclick="tab('chat')"><i class="fa-solid fa-clapperboard"></i></div>
        <div class="nav-btn" onclick="tab('profile')"><i class="fa-solid fa-circle-user"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const firebaseConfig = {
    apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
    databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
let user = localStorage.getItem("lc_user_v4");

if(user) { document.getElementById("authBox").style.display="none"; init(); }

window.joinNet = () => {
    const n = document.getElementById("uInput").value.trim();
    if(n) { localStorage.setItem("lc_user_v4", n); location.reload(); }
};

function init() {
    document.getElementById("myName").innerText = user;
    
    // Get PFP
    onValue(ref(db, `users/${user}/pfp`), (s) => {
        const url = s.val() || "https://cdn-icons-png.flaticon.com/512/149/149071.png";
        document.getElementById("myPfp").src = url;
    });

    // Sync Posts
    onValue(ref(db, 'lc_v4_posts'), (snap) => {
        const feed = document.getElementById("feed"); feed.innerHTML = "";
        snap.forEach(s => {
            const p = s.val();
            feed.innerHTML = `
                <div class="post-card">
                    <div class="post-top">
                        <div class="user-tag">
                            <img src="${p.uPfp || 'https://cdn-icons-png.flaticon.com/512/149/149071.png'}" class="avatar">
                            <b>${p.user}</b>
                        </div>
                    </div>
                    ${p.img ? `<img src="${p.img}" class="post-img">` : ''}
                    <div style="padding:15px;">${p.text}</div>
                    <div class="post-actions">
                        <div onclick="like('${s.key}')"><i class="fa-solid fa-heart"></i> ${p.likes || 0}</div>
                        <i class="fa-solid fa-comment"></i>
                        <i class="fa-solid fa-share"></i>
                    </div>
                </div>` + feed.innerHTML;
        });
    });

    // Sync Chat
    onValue(ref(db, 'lc_v4_chat'), (snap) => {
        const chats = document.getElementById("chats"); chats.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.user === user;
            chats.innerHTML += `
                <div class="msg-row ${isMe?'me':''}">
                    <img src="${m.uPfp || 'https://cdn-icons-png.flaticon.com/512/149/149071.png'}" class="avatar" style="width:25px; height:25px;">
                    <div class="msg ${isMe?'me':'them'}">${m.text}</div>
                </div>`;
        });
        document.getElementById("vPort").scrollTop = document.getElementById("vPort").scrollHeight;
    });
}

window.updatePfp = (e) => {
    const reader = new FileReader();
    reader.onload = () => set(ref(db, `users/${user}/pfp`), reader.result);
    reader.readAsDataURL(e.target.files[0]);
};

window.uploadPost = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const cap = prompt("Caption?");
        const currentPfp = document.getElementById("myPfp").src;
        push(ref(db, 'lc_v4_posts'), { user, text: cap||"", img: reader.result, uPfp: currentPfp, likes: 0 });
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.sendMsg = () => {
    const i = document.getElementById("mInput");
    const currentPfp = document.getElementById("myPfp").src;
    if(i.value) { push(ref(db, 'lc_v4_chat'), { user, text: i.value, uPfp: currentPfp }); i.value = ""; }
};

window.like = (id) => runTransaction(ref(db, `lc_v4_posts/${id}/likes`), (c) => (c || 0) + 1);

window.tab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-btn').forEach(n => n.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    document.getElementById('chatDock').classList.toggle('active', t === 'chat');
    const m = {'home':0, 'chat':1, 'profile':2};
    document.querySelectorAll('.nav-btn')[m[t]].classList.add('active');
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
