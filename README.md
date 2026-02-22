<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Meta Infinity | Final</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --bg: #000; --card: #121212; --text: #fff; --border: #262626; --insta: linear-gradient(45deg, #f09433, #e6683c, #dc2743, #cc2366, #bc1888); }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* AUTH UI */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 25px; }
        .auth-card { width: 100%; max-width: 350px; text-align: center; }
        .auth-card h1 { font-size: 40px; font-weight: 800; color: var(--p); margin-bottom: 30px; }
        .auth-input { width: 100%; padding: 16px; background: #1a1a1a; border: 1px solid var(--border); border-radius: 12px; color: #fff; margin-bottom: 15px; outline: none; }
        .btn-join { width: 100%; padding: 16px; background: var(--p); border: none; border-radius: 12px; color: #fff; font-weight: 800; cursor: pointer; }

        /* HEADER */
        header { padding: 12px 15px; border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; background: #000; position: sticky; top: 0; z-index: 100; }
        header h2 { font-style: italic; font-weight: 800; margin: 0; font-size: 22px; background: var(--insta); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* POSTS */
        .viewport { height: calc(100vh - 65px); overflow-y: auto; padding-bottom: 100px; scrollbar-width: none; }
        .view { display: none; }
        .view.active { display: block; animation: fadeIn 0.3s ease; }
        .post-card { background: var(--card); border-bottom: 6px solid #000; }
        .post-top { padding: 12px; display: flex; align-items: center; justify-content: space-between; }
        .avatar { width: 35px; height: 35px; border-radius: 50%; background: var(--p); display: flex; align-items: center; justify-content: center; font-weight: 800; }
        .post-img { width: 100%; max-height: 400px; object-fit: cover; }
        .v-badge { color: #0095f6; font-size: 13px; margin-left: 4px; }

        /* STATS */
        .profile-header { padding: 30px 15px; text-align: center; }
        .stats-row { display: flex; justify-content: space-around; margin: 20px 0; border-top: 1px solid var(--border); padding-top: 20px; }
        .stat-item b { display: block; font-size: 18px; }
        .stat-item span { font-size: 12px; opacity: 0.6; }

        /* NAV */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 65px; background: #000; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; }
        .nav-item { font-size: 24px; color: #fff; opacity: 0.4; cursor: pointer; }
        .nav-item.active { opacity: 1; color: var(--p); }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>Meta</h1>
        <input type="text" id="userInput" class="auth-input" placeholder="Username">
        <button class="btn-join" onclick="login()">Join Network</button>
    </div>
</div>

<div class="app-shell" style="max-width:500px; margin:0 auto; position:relative; height:100vh;">
    <header>
        <h2>Meta Infinity</h2>
        <div style="display:flex; gap:18px; font-size:22px;">
            <label for="imgInp"><i class="fa-regular fa-square-plus"></i></label>
            <input type="file" id="imgInp" hidden accept="image/*" onchange="uploadImage(event)">
            <i class="fa-brands fa-facebook-messenger" onclick="tab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="vPort">
        <div id="v-home" class="view active"><div id="feed"></div></div>
        
        <div id="v-chat" class="view">
            <div id="chats" style="padding:15px; display:flex; flex-direction:column; gap:10px;"></div>
            <div style="position:fixed; bottom:70px; width:100%; max-width:500px; padding:10px; display:flex; gap:8px; background:#000;">
                <input type="text" id="mInp" placeholder="Message..." style="flex:1; padding:12px; border-radius:25px; background:#1a1a1a; border:none; color:#fff; outline:none;">
                <button onclick="sendMsg()" style="background:var(--p); border:none; border-radius:50%; width:45px; height:45px; color:#fff;"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>

        <div id="v-profile" class="view">
            <div class="profile-header">
                <div class="avatar" style="width:90px; height:90px; font-size:35px; margin:0 auto; background:var(--insta);" id="pfp">?</div>
                <h2 id="pName" style="margin-top:15px;">User</h2>
                <div class="stats-row">
                    <div class="stat-item"><b id="postCount">0</b><span>Posts</span></div>
                    <div class="stat-item"><b>4.8k</b><span>Followers</span></div>
                    <div class="stat-item"><b>1.2k</b><span>Following</span></div>
                </div>
                <button onclick="localStorage.clear(); location.reload();" style="background:none; border:none; color:red; font-weight:800;">Log Out</button>
            </div>
        </div>
    </div>

    <nav>
        <div class="nav-item active" onclick="tab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="nav-item"><i class="fa-solid fa-magnifying-glass"></i></div>
        <div class="nav-item" onclick="tab('chat')"><i class="fa-solid fa-play"></i></div>
        <div class="nav-item" onclick="tab('profile')"><i class="fa-solid fa-circle-user"></i></div>
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
let user = localStorage.getItem("meta_final_user");

if(user) { document.getElementById("authBox").style.display="none"; init(); }

window.login = () => {
    const n = document.getElementById("userInput").value.trim();
    if(n) { localStorage.setItem("meta_final_user", n); location.reload(); }
};

function init() {
    document.getElementById("pName").innerText = user;
    document.getElementById("pfp").innerText = user.charAt(0).toUpperCase();

    onValue(ref(db, 'meta_final_posts'), (snap) => {
        const feed = document.getElementById("feed"); feed.innerHTML = "";
        let count = 0;
        snap.forEach(s => {
            const p = s.val(); if(p.user === user) count++;
            const isV = p.user.toLowerCase() === 'admin' ? '<i class="fa-solid fa-circle-check v-badge"></i>' : '';
            feed.innerHTML = `
                <div class="post-card">
                    <div class="post-top">
                        <div style="display:flex;align-items:center;gap:10px;"><div class="avatar">${p.user.charAt(0)}</div><b>${p.user}${isV}</b></div>
                        ${p.user === user ? `<i class="fa-solid fa-trash" onclick="del('meta_final_posts/${s.key}')"></i>` : ''}
                    </div>
                    ${p.img ? `<img src="${p.img}" class="post-img">` : ''}
                    <div style="padding:12px 15px;">${p.text}</div>
                    <div style="padding:10px 15px; display:flex; gap:20px; font-size:22px; border-top:1px solid var(--border);">
                        <i class="fa-regular fa-heart" onclick="like('${s.key}')"></i>
                        <i class="fa-regular fa-comment"></i>
                    </div>
                </div>` + feed.innerHTML;
        });
        document.getElementById("postCount").innerText = count;
    });

    onValue(ref(db, 'meta_final_chat'), (snap) => {
        const c = document.getElementById("chats"); c.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.user === user;
            c.innerHTML += `<div style="align-self:${isMe?'flex-end':'flex-start'}; background:${isMe?var(--p):'#222'}; padding:10px 15px; border-radius:18px; max-width:80%; font-size:14px;">
                <small style="display:block; font-size:9px; opacity:0.5;">${m.user}</small>${m.text}</div>`;
        });
    });
}

window.uploadImage = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const t = prompt("Add a caption:");
        push(ref(db, 'meta_final_posts'), { user, text: t || "", img: reader.result, likes: 0 });
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.sendMsg = () => {
    const i = document.getElementById("mInp");
    if(i.value) { push(ref(db, 'meta_final_chat'), { user, text: i.value }); i.value = ""; }
};

window.like = (id) => runTransaction(ref(db, `meta_final_posts/${id}/likes`), (c) => (c || 0) + 1);
window.del = (p) => { if(confirm("Delete?")) remove(ref(db, p)); };

window.tab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    const tabs = {'home':0, 'chat':2, 'profile':3};
    document.querySelectorAll('.nav-item')[tabs[t]].classList.add('active');
};
</script>
</body>
</html>
