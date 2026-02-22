<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Facebook Infinity</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Segoe+UI:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        :root { --fb-blue: #0866FF; --fb-bg: #F0F2F5; --fb-white: #FFFFFF; --fb-text: #050505; --fb-gray: #65676B; --fb-border: #ced0d4; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Segoe UI', Helvetica, Arial, sans-serif; margin: 0; padding: 0; }
        body, html { height: 100%; background: var(--fb-bg); color: var(--fb-text); overflow: hidden; }

        /* AUTH SCREEN */
        #authBox { position: fixed; inset: 0; background: var(--fb-bg); z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 20px; }
        .auth-card { width: 100%; max-width: 400px; text-align: center; background: var(--fb-white); padding: 30px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .auth-card h1 { color: var(--fb-blue); font-size: 40px; margin-bottom: 20px; font-weight: 800; }
        .auth-input { width: 100%; padding: 14px; border: 1px solid var(--fb-border); border-radius: 6px; font-size: 16px; margin-bottom: 15px; outline: none; }
        .btn-join { width: 100%; padding: 14px; background: var(--fb-blue); border: none; border-radius: 6px; color: #fff; font-weight: 700; font-size: 18px; cursor: pointer; }

        /* APP LAYOUT */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; background: var(--fb-bg); position: relative; }
        
        header { background: var(--fb-white); padding: 10px 15px; display: flex; justify-content: space-between; align-items: center; box-shadow: 0 2px 4px rgba(0,0,0,0.05); }
        header h2 { color: var(--fb-blue); font-size: 28px; font-weight: 800; letter-spacing: -1px; }

        nav { background: var(--fb-white); display: flex; border-bottom: 1px solid var(--fb-border); }
        .nav-item { flex: 1; text-align: center; padding: 12px; font-size: 22px; color: var(--fb-gray); cursor: pointer; position: relative; }
        .nav-item.active { color: var(--fb-blue); }
        .nav-item.active::after { content: ''; position: absolute; bottom: 0; left: 0; width: 100%; height: 3px; background: var(--fb-blue); }

        .viewport { flex: 1; overflow-y: auto; padding-bottom: 20px; scrollbar-width: none; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; }
        .view.active { display: block; }

        /* FB FEED STYLE */
        .fb-composer { background: var(--fb-white); padding: 15px; margin-bottom: 10px; display: flex; gap: 10px; align-items: center; }
        .composer-btn { background: #E4E6EB; padding: 10px 15px; border-radius: 20px; flex: 1; color: var(--fb-gray); cursor: pointer; text-align: left; }
        
        .post-card { background: var(--fb-white); margin-bottom: 10px; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }
        .post-header { padding: 12px; display: flex; align-items: center; justify-content: space-between; }
        .u-info { display: flex; align-items: center; gap: 10px; }
        .pfp { width: 40px; height: 40px; border-radius: 50%; background: #ddd; display: flex; align-items: center; justify-content: center; font-weight: 700; color: #fff; background-color: var(--fb-blue); }
        .post-text { padding: 0 15px 12px; font-size: 15px; }
        .post-img { width: 100%; max-height: 400px; object-fit: cover; border-top: 1px solid #f0f0f0; }
        .post-stats { padding: 10px 15px; display: flex; justify-content: space-between; color: var(--fb-gray); font-size: 13px; border-bottom: 1px solid #f0f2f5; }
        .post-actions { display: flex; padding: 5px; }
        .act-btn { flex: 1; text-align: center; padding: 8px; color: var(--fb-gray); font-weight: 600; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 5px; }
        .act-btn:active { background: #f0f2f5; }

        /* MESSENGER STYLE */
        .chat-view { padding: 10px; display: flex; flex-direction: column; gap: 10px; }
        .bubble { padding: 10px 15px; border-radius: 18px; max-width: 75%; font-size: 14px; position: relative; }
        .bubble.me { background: var(--fb-blue); color: white; align-self: flex-end; }
        .bubble.them { background: #E4E6EB; color: black; align-self: flex-start; }

        #msgDock { background: var(--fb-white); padding: 10px; display: none; border-top: 1px solid var(--fb-border); gap: 10px; }
        #msgDock.active { display: flex; }
        .msg-input { flex: 1; padding: 10px 15px; border-radius: 20px; background: #F0F2F5; border: none; outline: none; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>facebook</h1>
        <input type="text" id="userInput" class="auth-input" placeholder="Your name...">
        <button class="btn-join" onclick="handleJoin()">Log In</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2>facebook</h2>
        <div style="display:flex; gap:15px; font-size:20px; color: var(--fb-text);">
            <i class="fa-solid fa-circle-plus" onclick="triggerPost()"></i>
            <i class="fa-solid fa-magnifying-glass"></i>
            <i class="fa-brands fa-facebook-messenger" onclick="switchTab('chat')"></i>
        </div>
    </header>

    <nav>
        <div class="nav-item active" onclick="switchTab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="nav-item"><i class="fa-solid fa-user-group"></i></div>
        <div class="nav-item"><i class="fa-solid fa-tv"></i></div>
        <div class="nav-item" onclick="switchTab('profile')"><i class="fa-solid fa-circle-user"></i></div>
        <div class="nav-item"><i class="fa-solid fa-bell"></i></div>
        <div class="nav-item"><i class="fa-solid fa-bars"></i></div>
    </nav>

    <div class="viewport" id="vPort">
        <div id="v-home" class="view active">
            <div class="fb-composer">
                <div class="pfp" id="cAvatar">?</div>
                <div class="composer-btn" onclick="triggerPost()">What's on your mind?</div>
                <label for="imgInp"><i class="fa-regular fa-image" style="color:#45bd62; font-size:22px;"></i></label>
                <input type="file" id="imgInp" hidden accept="image/*" onchange="uploadPhoto(event)">
            </div>
            <div id="postFeed"></div>
        </div>

        <div id="v-chat" class="view">
            <div class="chat-view" id="chatList"></div>
        </div>

        <div id="v-profile" class="view" style="background:var(--fb-white); min-height:100%;">
            <div style="height:150px; background:#ddd; position:relative;">
                <div class="pfp" style="width:120px; height:120px; font-size:40px; border:4px solid white; position:absolute; bottom:-40px; left:20px;" id="profAvatar">?</div>
            </div>
            <div style="padding:50px 20px 20px;">
                <h2 id="profName" style="font-size:24px;">Username</h2>
                <div style="display:flex; gap:10px; margin-top:15px;">
                    <button style="flex:1; background:var(--fb-blue); color:white; border:none; padding:10px; border-radius:6px; font-weight:600;">Add to Story</button>
                    <button style="background:#E4E6EB; border:none; padding:10px 15px; border-radius:6px;" onclick="logout()"><i class="fa-solid fa-right-from-bracket"></i></button>
                </div>
            </div>
        </div>
    </div>

    <div id="msgDock">
        <i class="fa-solid fa-circle-plus" style="color:var(--fb-blue); font-size:22px;"></i>
        <input type="text" id="mInp" class="msg-input" placeholder="Aa">
        <i class="fa-solid fa-paper-plane" onclick="sendMsg()" style="color:var(--fb-blue); font-size:22px;"></i>
    </div>
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
let user = localStorage.getItem("fb_infinity_user");

if(user) { document.getElementById("authBox").style.display="none"; init(); }

window.handleJoin = () => {
    const n = document.getElementById("userInput").value.trim();
    if(n) { localStorage.setItem("fb_infinity_user", n); location.reload(); }
};

function init() {
    document.getElementById("profName").innerText = user;
    document.getElementById("profAvatar").innerText = user.charAt(0).toUpperCase();
    document.getElementById("cAvatar").innerText = user.charAt(0).toUpperCase();

    onValue(ref(db, 'fb_posts'), (snap) => {
        const feed = document.getElementById("postFeed"); feed.innerHTML = "";
        snap.forEach(s => {
            const p = s.val();
            feed.innerHTML = `
                <div class="post-card">
                    <div class="post-header">
                        <div class="u-info"><div class="pfp">${p.user.charAt(0)}</div><div><b>${p.user}</b><br><small style="color:gray">Just now • <i class="fa-solid fa-earth-americas"></i></small></div></div>
                        ${p.user === user ? `<i class="fa-solid fa-ellipsis-h" onclick="del('fb_posts/${s.key}')"></i>` : ''}
                    </div>
                    <div class="post-text">${p.text}</div>
                    ${p.img ? `<img src="${p.img}" class="post-img">` : ''}
                    <div class="post-stats"><span>👍 ${p.likes || 0}</span><span>0 Comments</span></div>
                    <div class="post-actions">
                        <div class="act-btn" onclick="like('${s.key}')"><i class="fa-regular fa-thumbs-up"></i> Like</div>
                        <div class="act-btn"><i class="fa-regular fa-comment"></i> Comment</div>
                        <div class="act-btn"><i class="fa-solid fa-share"></i> Share</div>
                    </div>
                </div>` + feed.innerHTML;
        });
    });

    onValue(ref(db, 'fb_chat'), (snap) => {
        const c = document.getElementById("chatList"); c.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.user === user;
            c.innerHTML += `<div class="bubble ${isMe?'me':'them'}">${m.text}</div>`;
        });
    });
}

window.triggerPost = () => {
    const t = prompt("What's on your mind?");
    if(t) push(ref(db, 'fb_posts'), { user, text: t, likes: 0 });
};

window.uploadPhoto = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const cap = prompt("Say something about this photo...");
        push(ref(db, 'fb_posts'), { user, text: cap || "", img: reader.result, likes: 0 });
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.sendMsg = () => {
    const i = document.getElementById("mInp");
    if(i.value) { push(ref(db, 'fb_chat'), { user, text: i.value }); i.value = ""; }
};

window.like = (id) => runTransaction(ref(db, `fb_posts/${id}/likes`), (c) => (c || 0) + 1);
window.del = (p) => { if(confirm("Delete this post?")) remove(ref(db, p)); };

window.switchTab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    document.getElementById('msgDock').classList.toggle('active', t === 'chat');
    
    const tabs = {'home':0, 'chat':1, 'profile':3};
    if(tabs[t] !== undefined) document.querySelectorAll('.nav-item')[tabs[t]].classList.add('active');
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
