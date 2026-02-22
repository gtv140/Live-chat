<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Next-Gen</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --bg: #050505; --card: #18191a; --text: #e4e6eb; --border: #3e4042; --accent: #242526; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; transition: all 0.2s ease; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* NEON LOGIN */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 25px; }
        .auth-card { width: 100%; max-width: 380px; text-align: center; }
        .auth-card h1 { font-size: 45px; font-weight: 800; background: linear-gradient(to right, #0084FF, #00dfd8); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 30px; }
        .auth-input { width: 100%; padding: 18px; background: var(--accent); border: 1px solid var(--border); border-radius: 16px; color: #fff; margin-bottom: 20px; outline: none; font-size: 16px; }
        .btn-join { width: 100%; padding: 18px; background: var(--p); border: none; border-radius: 16px; color: #fff; font-weight: 800; cursor: pointer; box-shadow: 0 10px 20px rgba(0,132,255,0.3); }

        /* APP SHELL */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; background: var(--bg); position: relative; }
        header { padding: 15px; display: flex; justify-content: space-between; align-items: center; background: rgba(5,5,5,0.8); backdrop-filter: blur(10px); z-index: 100; position: sticky; top:0; }
        header h2 { font-weight: 800; font-size: 26px; color: var(--p); letter-spacing: -1px; }

        /* STORIES */
        .stories { display: flex; gap: 12px; padding: 10px 15px; overflow-x: auto; scrollbar-width: none; background: var(--bg); }
        .story-ring { min-width: 65px; height: 65px; border-radius: 50%; padding: 3px; border: 2px solid var(--p); position: relative; }
        .story-pfp { width: 100%; height: 100%; border-radius: 50%; background: #333; display: flex; align-items: center; justify-content: center; font-weight: 800; }

        /* VIEWPORT */
        .viewport { flex: 1; overflow-y: auto; padding-bottom: 90px; scrollbar-width: none; }
        .view { display: none; }
        .view.active { display: block; animation: slideUp 0.4s ease-out; }

        /* MODERN POSTS */
        .post-card { background: var(--card); margin: 10px; border-radius: 16px; overflow: hidden; border: 1px solid var(--border); }
        .post-top { padding: 12px; display: flex; align-items: center; justify-content: space-between; }
        .user-tag { display: flex; align-items: center; gap: 10px; }
        .avatar { width: 40px; height: 40px; border-radius: 50%; background: var(--p); display: flex; align-items: center; justify-content: center; font-weight: 800; border: 2px solid var(--bg); }
        .post-img { width: 100%; max-height: 400px; object-fit: cover; }
        .post-actions { padding: 12px 20px; display: flex; justify-content: space-between; border-top: 1px solid var(--border); }
        .action-item { display: flex; align-items: center; gap: 8px; font-weight: 600; color: #b0b3b8; font-size: 15px; }
        .action-item.liked { color: var(--p); }

        /* CHAT BUBBLES */
        .chat-container { padding: 20px; display: flex; flex-direction: column; gap: 15px; }
        .msg { padding: 12px 18px; border-radius: 22px; max-width: 80%; font-size: 14px; box-shadow: 0 4px 10px rgba(0,0,0,0.2); }
        .msg.me { background: var(--p); color: white; align-self: flex-end; border-bottom-right-radius: 4px; }
        .msg.them { background: var(--accent); align-self: flex-start; border-bottom-left-radius: 4px; }

        /* NAV BAR */
        nav { position: absolute; bottom: 0; width: 100%; height: 75px; background: rgba(24,25,26,0.95); backdrop-filter: blur(15px); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--border); }
        .nav-btn { font-size: 24px; color: #b0b3b8; cursor: pointer; position: relative; }
        .nav-btn.active { color: var(--p); transform: translateY(-5px); }
        .nav-btn.active::after { content: ''; position: absolute; bottom: -10px; left: 50%; transform: translateX(-50%); width: 5px; height: 5px; background: var(--p); border-radius: 50%; }

        #chatDock { position: absolute; bottom: 75px; width: 100%; padding: 15px; background: var(--card); display: none; gap: 10px; align-items: center; border-top: 1px solid var(--border); }
        #chatDock.active { display: flex; }

        @keyframes slideUp { from { transform: translateY(20px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>Live Connect</h1>
        <input type="text" id="uInput" class="auth-input" placeholder="Your Global Username...">
        <button class="btn-join" onclick="joinNet()">Initialize Connection</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2>Live Connect</h2>
        <div style="display:flex; gap:20px; font-size:22px;">
            <label for="pImg"><i class="fa-solid fa-plus-circle" style="color: var(--p);"></i></label>
            <input type="file" id="pImg" hidden accept="image/*" onchange="uploadPost(event)">
            <i class="fa-brands fa-facebook-messenger" onclick="tab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="scroll">
        <div id="v-home" class="view active">
            <div class="stories">
                <div class="story-ring"><div class="story-pfp">Me</div></div>
                <div class="story-ring"><div class="story-pfp" style="background:var(--accent)">AD</div></div>
                <div class="story-ring"><div class="story-pfp" style="background:var(--accent)">JD</div></div>
                <div class="story-ring"><div class="story-pfp" style="background:var(--accent)">RK</div></div>
            </div>
            <div id="feedList"></div>
        </div>

        <div id="v-chat" class="view">
            <div class="chat-container" id="chatList"></div>
        </div>

        <div id="v-profile" class="view" style="padding:30px 20px; text-align:center;">
            <div style="position:relative; width:110px; height:110px; margin:0 auto;">
                <div class="avatar" style="width:110px; height:110px; font-size:45px; background:linear-gradient(45deg, #0084FF, #00dfd8);" id="myAvatar">?</div>
                <div style="position:absolute; bottom:5px; right:5px; width:20px; height:20px; background:#45bd62; border:3px solid var(--bg); border-radius:50%;"></div>
            </div>
            <h2 id="myName" style="margin-top:15px; font-size:28px;">Username</h2>
            <div style="display:flex; justify-content:space-around; margin:30px 0; background:var(--card); padding:20px; border-radius:20px;">
                <div><b id="postCount">0</b><br><small>Posts</small></div>
                <div><b>12.5k</b><br><small>Reach</small></div>
                <div><b>100%</b><br><small>Active</small></div>
            </div>
            <button onclick="logout()" style="width:100%; padding:15px; border-radius:15px; background:var(--accent); border:1px solid var(--border); color:#ff4d4d; font-weight:800;">Terminate Session</button>
        </div>
    </div>

    <div id="chatDock">
        <input type="text" id="mInput" style="flex:1; padding:15px; border-radius:30px; background:var(--accent); border:none; color:#fff; outline:none;" placeholder="Type a message...">
        <div onclick="pushMsg()" style="background:var(--p); width:50px; height:50px; border-radius:50%; display:flex; align-items:center; justify-content:center; cursor:pointer;"><i class="fa-solid fa-paper-plane"></i></div>
    </div>

    <nav>
        <div class="nav-btn active" onclick="tab('home')"><i class="fa-solid fa-house-chimney"></i></div>
        <div class="nav-btn"><i class="fa-solid fa-compass"></i></div>
        <div class="nav-btn" onclick="tab('chat')"><i class="fa-solid fa-message"></i></div>
        <div class="nav-btn" onclick="tab('profile')"><i class="fa-solid fa-user-ninja"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.firebasestorage.app",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
let me = localStorage.getItem("live_connect_session");

if(me) { document.getElementById("authBox").style.display="none"; start(); }

window.joinNet = () => {
    const v = document.getElementById("uInput").value.trim();
    if(v) { localStorage.setItem("live_connect_session", v); location.reload(); }
};

function start() {
    document.getElementById("myName").innerText = me;
    document.getElementById("myAvatar").innerText = me.charAt(0).toUpperCase();

    // POSTS SYNC
    onValue(ref(db, 'lc_posts'), (snap) => {
        const feed = document.getElementById("feedList"); feed.innerHTML = "";
        let count = 0;
        snap.forEach(s => {
            const p = s.val(); if(p.user === me) count++;
            feed.innerHTML = `
                <div class="post-card">
                    <div class="post-top">
                        <div class="user-tag"><div class="avatar">${p.user.charAt(0)}</div><b>${p.user}</b></div>
                        ${p.user === me ? `<i class="fa-solid fa-trash-can" onclick="delPost('${s.key}')" style="opacity:0.5"></i>` : ''}
                    </div>
                    ${p.img ? `<img src="${p.img}" class="post-img">` : ''}
                    <div style="padding:15px; font-weight:300;">${p.text}</div>
                    <div class="post-actions">
                        <div class="action-item" onclick="like('${s.key}')"><i class="fa-solid fa-heart"></i> ${p.likes || 0}</div>
                        <div class="action-item"><i class="fa-solid fa-comment-dots"></i> Reply</div>
                        <div class="action-item"><i class="fa-solid fa-paper-plane-top"></i></div>
                    </div>
                </div>` + feed.innerHTML;
        });
        document.getElementById("postCount").innerText = count;
    });

    // CHAT SYNC
    onValue(ref(db, 'lc_chat'), (snap) => {
        const chat = document.getElementById("chatList"); chat.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.user === me;
            chat.innerHTML += `<div class="msg ${isMe?'me':'them'}"><small style="display:block;font-size:10px;opacity:0.6;margin-bottom:4px;">${m.user}</small>${m.text}</div>`;
        });
        const port = document.getElementById("scroll"); port.scrollTop = port.scrollHeight;
    });
}

window.uploadPost = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const cap = prompt("Express your thoughts...");
        push(ref(db, 'lc_posts'), { user: me, text: cap || "", img: reader.result, likes: 0 });
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.pushMsg = () => {
    const i = document.getElementById("mInput");
    if(i.value.trim()) { push(ref(db, 'lc_chat'), { user: me, text: i.value }); i.value = ""; }
};

window.like = (id) => runTransaction(ref(db, `lc_posts/${id}/likes`), (c) => (c || 0) + 1);
window.delPost = (id) => { if(confirm("Discard this post?")) remove(ref(db, 'lc_posts/'+id)); };

window.tab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-btn').forEach(n => n.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    document.getElementById('chatDock').classList.toggle('active', t === 'chat');
    
    const map = {'home':0, 'chat':2, 'profile':3};
    document.querySelectorAll('.nav-btn')[map[t]].classList.add('active');
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
