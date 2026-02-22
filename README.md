<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Meta Infinity | Final Edition</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --bg: #000; --card: #121212; --text: #fff; --border: #262626; --insta: linear-gradient(45deg, #f09433, #e6683c, #dc2743, #cc2366, #bc1888); }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; margin: 0; padding: 0; }
        body, html { height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* AUTH SCREEN */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 25px; }
        .auth-card { width: 100%; max-width: 350px; text-align: center; }
        .auth-card h1 { font-size: 45px; font-weight: 800; color: var(--p); margin-bottom: 30px; letter-spacing: -2px; }
        .auth-input { width: 100%; padding: 18px; background: #1a1a1a; border: 1px solid var(--border); border-radius: 15px; color: #fff; margin-bottom: 15px; outline: none; font-size: 16px; }
        .btn-join { width: 100%; padding: 18px; background: var(--p); border: none; border-radius: 15px; color: #fff; font-weight: 800; cursor: pointer; font-size: 16px; }

        /* MAIN APP */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; background: #000; }
        header { padding: 15px; border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; background: #000; }
        header h2 { font-style: italic; font-weight: 800; font-size: 24px; background: var(--insta); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        .viewport { flex: 1; overflow-y: auto; padding-bottom: 80px; scrollbar-width: none; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; }
        .view.active { display: block; }

        /* POSTS STYLE */
        .post-card { background: var(--card); border-bottom: 8px solid #000; }
        .post-top { padding: 12px; display: flex; align-items: center; justify-content: space-between; }
        .avatar { width: 35px; height: 35px; border-radius: 50%; background: var(--p); display: flex; align-items: center; justify-content: center; font-weight: 800; font-size: 14px; }
        .post-img { width: 100%; max-height: 450px; object-fit: cover; background: #1a1a1a; }
        .post-actions { padding: 15px; display: flex; gap: 20px; font-size: 24px; }

        /* CHAT STYLE */
        .chat-list { padding: 15px; display: flex; flex-direction: column; gap: 10px; }
        .msg { padding: 12px 16px; border-radius: 20px; max-width: 80%; font-size: 14px; line-height: 1.4; }
        .msg.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 4px; }
        .msg.them { background: #262626; align-self: flex-start; border-bottom-left-radius: 4px; }

        /* NAV */
        nav { position: absolute; bottom: 0; width: 100%; height: 70px; background: #000; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 100; }
        .nav-item { font-size: 26px; color: #fff; opacity: 0.4; cursor: pointer; }
        .nav-item.active { opacity: 1; color: var(--p); }

        #chatDock { position: absolute; bottom: 70px; width: 100%; padding: 12px; background: #000; border-top: 1px solid var(--border); display: none; gap: 10px; }
        #chatDock.active { display: flex; }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>Meta</h1>
        <input type="text" id="userInput" class="auth-input" placeholder="Enter Username...">
        <button class="btn-join" id="joinBtn">Join Network</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2>Meta</h2>
        <div style="display:flex; gap:20px; font-size:22px;">
            <label for="imgFile"><i class="fa-regular fa-square-plus"></i></label>
            <input type="file" id="imgFile" hidden accept="image/*">
            <i class="fa-brands fa-facebook-messenger" onclick="switchTab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="mainScroll">
        <div id="v-home" class="view active">
            <div id="postFeed"></div>
        </div>

        <div id="v-chat" class="view">
            <div class="chat-list" id="chatList"></div>
        </div>

        <div id="v-profile" class="view" style="padding:40px 20px; text-align:center;">
            <div class="avatar" style="width:100px; height:100px; margin:0 auto; font-size:40px; background:var(--insta);" id="myPfp">?</div>
            <h2 id="myName" style="margin:15px 0;">User</h2>
            <div style="display:flex; justify-content:space-around; margin:20px 0; border-top:1px solid #222; padding-top:20px;">
                <div><b id="cPost">0</b><br><small>Posts</small></div>
                <div><b>5.2k</b><br><small>Followers</small></div>
                <div><b>940</b><br><small>Following</small></div>
            </div>
            <button onclick="logout()" style="color:red; background:none; border:none; font-weight:800; margin-top:20px;">Logout</button>
        </div>
    </div>

    <div id="chatDock">
        <input type="text" id="msgInp" style="flex:1; padding:12px; border-radius:25px; background:#1a1a1a; border:none; color:#fff; outline:none;" placeholder="Message...">
        <i class="fa-solid fa-paper-plane" id="sendBtn" style="color:var(--p); font-size:22px;"></i>
    </div>

    <nav>
        <div class="nav-item active" onclick="switchTab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="nav-item"><i class="fa-solid fa-magnifying-glass"></i></div>
        <div class="nav-item" onclick="switchTab('chat')"><i class="fa-solid fa-clapperboard"></i></div>
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
let user = localStorage.getItem("meta_final_v3");

if(user) {
    document.getElementById("authBox").style.display = "none";
    runApp();
}

// JOIN LOGIC
document.getElementById("joinBtn").onclick = () => {
    const val = document.getElementById("userInput").value.trim();
    if(val) {
        localStorage.setItem("meta_final_v3", val);
        window.location.reload();
    }
};

function runApp() {
    document.getElementById("myName").innerText = user;
    document.getElementById("myPfp").innerText = user.charAt(0).toUpperCase();

    // Sync Posts
    onValue(ref(db, 'posts_final'), (snap) => {
        const feed = document.getElementById("postFeed");
        feed.innerHTML = "";
        let count = 0;
        snap.forEach(s => {
            const p = s.val();
            if(p.user === user) count++;
            feed.innerHTML = `
                <div class="post-card">
                    <div class="post-top">
                        <div style="display:flex;align-items:center;gap:10px;">
                            <div class="avatar">${p.user.charAt(0)}</div><b>${p.user}</b>
                        </div>
                        ${p.user === user ? `<i class="fa-solid fa-trash" onclick="window.delPost('${s.key}')"></i>` : ''}
                    </div>
                    ${p.img ? `<img src="${p.img}" class="post-img">` : ''}
                    <div style="padding:15px;">${p.text}</div>
                    <div class="post-actions">
                        <i class="fa-regular fa-heart" onclick="window.likePost('${s.key}')"></i>
                        <span style="font-size:14px; margin-left:-12px; font-weight:700;">${p.likes || 0}</span>
                        <i class="fa-regular fa-comment"></i>
                    </div>
                </div>` + feed.innerHTML;
        });
        document.getElementById("cPost").innerText = count;
    });

    // Sync Chat
    onValue(ref(db, 'chat_final'), (snap) => {
        const list = document.getElementById("chatList");
        list.innerHTML = "";
        snap.forEach(s => {
            const m = s.val();
            const isMe = m.user === user;
            list.innerHTML += `<div class="msg ${isMe ? 'me' : 'them'}"><small style="display:block;font-size:9px;opacity:0.6">${m.user}</small>${m.text}</div>`;
        });
    });
}

// Global Actions
window.delPost = (id) => { if(confirm("Delete post?")) remove(ref(db, 'posts_final/'+id)); };
window.likePost = (id) => runTransaction(ref(db, `posts_final/${id}/likes`), (c) => (c || 0) + 1);

document.getElementById("imgFile").onchange = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    reader.onload = () => {
        const caption = prompt("Write a caption...");
        push(ref(db, 'posts_final'), { user, text: caption || "", img: reader.result, likes: 0 });
    };
    reader.readAsDataURL(file);
};

document.getElementById("sendBtn").onclick = () => {
    const inp = document.getElementById("msgInp");
    if(inp.value.trim()) {
        push(ref(db, 'chat_final'), { user, text: inp.value });
        inp.value = "";
    }
};

window.switchTab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    document.getElementById('chatDock').classList.toggle('active', t === 'chat');
    
    const map = {'home':0, 'chat':2, 'profile':3};
    document.querySelectorAll('.nav-item')[map[t]].classList.add('active');
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
