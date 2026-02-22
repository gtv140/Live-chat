<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Meta Infinity | Live Connect</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --bg: #000; --card: #121212; --text: #fff; --border: #262626; --input: #1c1c1c; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.3s; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* LOGIN SCREEN - FULL FIX */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 25px; }
        .auth-card { width: 100%; max-width: 350px; text-align: center; }
        .auth-card h1 { font-size: 42px; font-weight: 800; background: linear-gradient(to right, #0084FF, #00C6FF); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 40px; }
        .auth-input { width: 100%; padding: 16px; background: var(--input); border: 1px solid var(--border); border-radius: 14px; color: #fff; font-size: 16px; margin-bottom: 20px; outline: none; }
        .btn-join { width: 100%; padding: 16px; background: var(--p); border: none; border-radius: 14px; color: #fff; font-weight: 800; font-size: 16px; cursor: pointer; }

        /* MAIN APP LAYOUT */
        .app-container { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; background: var(--bg); position: relative; }
        header { padding: 15px; border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; background: var(--bg); z-index: 100; }
        header h2 { font-style: italic; font-weight: 800; margin: 0; font-size: 22px; color: var(--p); }

        .viewport { flex: 1; overflow-y: auto; padding-bottom: 100px; scrollbar-width: none; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; }
        .view.active { display: block; animation: fadeIn 0.4s ease; }

        /* POSTS */
        .post-card { background: var(--card); border-bottom: 6px solid #000; }
        .post-top { padding: 12px; display: flex; align-items: center; justify-content: space-between; }
        .avatar { width: 34px; height: 34px; border-radius: 50%; background: var(--p); display: flex; align-items: center; justify-content: center; font-size: 14px; font-weight: 800; }
        .post-actions { padding: 12px 15px; display: flex; gap: 20px; font-size: 22px; }

        /* CHAT */
        .chat-list { padding: 15px; display: flex; flex-direction: column; gap: 12px; }
        .msg-bubble { padding: 12px 16px; border-radius: 20px; max-width: 80%; font-size: 14px; }
        .msg-bubble.me { background: var(--p); align-self: flex-end; color: #fff; border-bottom-right-radius: 4px; }
        .msg-bubble.them { background: var(--input); align-self: flex-start; border-bottom-left-radius: 4px; }

        /* NAVIGATION */
        nav { position: absolute; bottom: 0; width: 100%; height: 65px; background: var(--bg); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; }
        .nav-item { font-size: 24px; color: var(--text); opacity: 0.4; cursor: pointer; }
        .nav-item.active { opacity: 1; color: var(--p); }

        #chatDock { position: absolute; bottom: 65px; width: 100%; padding: 12px; background: var(--bg); border-top: 1px solid var(--border); display: none; gap: 10px; }
        #chatDock.active { display: flex; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>Meta Infinity</h1>
        <input type="text" id="usernameInput" class="auth-input" placeholder="Your Name...">
        <button class="btn-join" id="joinBtn">Join Network</button>
    </div>
</div>

<div class="app-container">
    <header>
        <h2>LiveConnect</h2>
        <div style="display:flex; gap:18px; font-size:20px;">
            <i class="fa-regular fa-square-plus" onclick="triggerPost()"></i>
            <i class="fa-brands fa-facebook-messenger" onclick="switchTab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="mainScroll">
        <div id="view-home" class="view active"><div id="feedList"></div></div>
        <div id="view-chat" class="view"><div class="chat-list" id="chatList"></div></div>
        <div id="view-profile" class="view" style="padding:40px 20px; text-align:center;">
            <div class="avatar" style="width:100px; height:100px; margin:0 auto; font-size:40px;" id="profAvatar">?</div>
            <h2 id="profName" style="margin-top:15px;">User</h2>
            <button onclick="logout()" style="margin-top:30px; color:#ff4d4d; border:none; background:none; font-weight:800; cursor:pointer;">Logout</button>
        </div>
    </div>

    <div id="chatDock">
        <input type="text" id="msgInput" style="flex:1; padding:12px; border-radius:25px; background:var(--input); border:none; color:#fff; outline:none;" placeholder="Message...">
        <i class="fa-solid fa-paper-plane" onclick="pushMsg()" style="color:var(--p); font-size:20px;"></i>
    </div>

    <nav>
        <div class="nav-item active" onclick="switchTab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="nav-item" onclick="switchTab('chat')"><i class="fa-solid fa-comment-dots"></i></div>
        <div class="nav-item" onclick="switchTab('profile')"><i class="fa-solid fa-circle-user"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

// AapkI REAL CONFIGURATION
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

let currentUser = localStorage.getItem("meta_user_session");

// Check Session
if(currentUser) {
    document.getElementById("authBox").style.display = "none";
    initApp();
}

// Fixed Login Logic
document.getElementById("joinBtn").addEventListener("click", () => {
    const name = document.getElementById("usernameInput").value.trim();
    if(name) {
        localStorage.setItem("meta_user_session", name);
        location.reload();
    } else {
        alert("Please enter a name!");
    }
});

function initApp() {
    document.getElementById("profName").innerText = currentUser;
    document.getElementById("profAvatar").innerText = currentUser.charAt(0).toUpperCase();

    // Load Posts
    onValue(ref(db, 'infinity_posts'), (snap) => {
        const feed = document.getElementById("feedList");
        feed.innerHTML = "";
        let posts = [];
        snap.forEach(s => posts.unshift({id: s.key, ...s.val()}));
        posts.forEach(p => {
            feed.innerHTML += `
                <div class="post-card">
                    <div class="post-top">
                        <div style="display:flex;align-items:center;gap:10px;">
                            <div class="avatar">${p.user.charAt(0)}</div><b>${p.user}</b>
                        </div>
                        ${p.user === currentUser ? `<i class="fa-solid fa-trash" onclick="deleteData('infinity_posts/${p.id}')"></i>` : ''}
                    </div>
                    <div style="padding:0 15px 15px;">${p.text}</div>
                    <div class="post-actions">
                        <i class="fa-regular fa-heart" onclick="likePost('${p.id}')"></i> <span>${p.likes || 0}</span>
                        <i class="fa-regular fa-comment" onclick="switchTab('chat')"></i>
                    </div>
                </div>`;
        });
    });

    // Load Chat
    onValue(ref(db, 'infinity_chat'), (snap) => {
        const list = document.getElementById("chatList");
        list.innerHTML = "";
        snap.forEach(s => {
            const m = s.val();
            const isMe = m.user === currentUser;
            list.innerHTML += `<div class="msg-bubble ${isMe ? 'me' : 'them'}"><small style="display:block;font-size:9px;opacity:0.6">${m.user}</small>${m.text}</div>`;
        });
    });
}

window.triggerPost = () => {
    const t = prompt("What's happening?");
    if(t) push(ref(db, 'infinity_posts'), { user: currentUser, text: t, likes: 0 });
};

window.pushMsg = () => {
    const inp = document.getElementById("msgInput");
    if(inp.value.trim()) {
        push(ref(db, 'infinity_chat'), { user: currentUser, text: inp.value });
        inp.value = "";
    }
};

window.likePost = (id) => runTransaction(ref(db, `infinity_posts/${id}/likes`), (c) => (c || 0) + 1);
window.deleteData = (path) => { if(confirm("Delete?")) remove(ref(db, path)); };

window.switchTab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
    document.getElementById('view-'+t).classList.add('active');
    document.getElementById('chatDock').classList.toggle('active', t === 'chat');
    
    const tabs = {'home':0, 'chat':1, 'profile':2};
    document.querySelectorAll('.nav-item')[tabs[t]].classList.add('active');
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
