<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Meta Infinity</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --bg: #000; --card: #121212; --text: #fff; --border: #262626; --insta: linear-gradient(45deg, #f09433, #e6683c, #dc2743, #cc2366, #bc1888); }
        .light-mode { --bg: #fff; --card: #fff; --text: #000; --border: #dbdbdb; }
        
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* HEADER */
        header { padding: 12px 15px; background: var(--bg); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 1000; }
        header h1 { font-size: 24px; font-weight: 800; color: var(--text); font-style: italic; letter-spacing: -1px; }

        /* VIEWPORT */
        .viewport { height: calc(100vh - 65px); overflow-y: auto; scrollbar-width: none; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; padding-bottom: 100px; }
        .view.active { display: block; animation: fadeIn 0.3s ease; }

        /* STORIES */
        .story-tray { display: flex; gap: 12px; padding: 15px; overflow-x: auto; border-bottom: 1px solid var(--border); }
        .story-circle { min-width: 65px; height: 65px; border-radius: 50%; padding: 3px; background: var(--insta); }
        .inner-circle { width: 100%; height: 100%; border-radius: 50%; border: 2px solid var(--bg); background: #333; display: flex; align-items: center; justify-content: center; font-size: 10px; }

        /* POSTS */
        .post { background: var(--card); border-bottom: 5px solid var(--border); }
        .post-header { padding: 12px; display: flex; align-items: center; justify-content: space-between; }
        .post-user { display: flex; align-items: center; gap: 10px; font-weight: 700; font-size: 14px; }
        .post-img { width: 100%; background: #222; min-height: 250px; object-fit: cover; }
        .post-actions { padding: 12px; display: flex; gap: 18px; font-size: 22px; }
        
        /* COMMENTS */
        .comment-sec { padding: 0 12px 12px; font-size: 13px; }
        .comment-item { margin-top: 5px; }

        /* REELS */
        .reels-scroll { height: 100vh; overflow-y: scroll; snap-type: y mandatory; margin-top: -60px; }
        .reel { height: 100vh; snap-align: start; background: #000; position: relative; }
        .reel-ui { position: absolute; bottom: 100px; right: 15px; display: flex; flex-direction: column; gap: 20px; align-items: center; }

        /* NAV */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 65px; background: var(--bg); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-btn { font-size: 24px; color: var(--text); opacity: 0.5; }
        .nav-btn.active { opacity: 1; color: var(--p); }

        /* MODALS */
        #auth { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 30px; }
        .btn-p { width: 100%; padding: 15px; background: var(--p); color: #fff; border: none; border-radius: 12px; font-weight: 800; cursor: pointer; }
        
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

<div id="auth">
    <div style="text-align:center; width: 100%; max-width: 320px;">
        <h1 style="font-size: 38px; margin-bottom: 10px; color: var(--p);">Meta</h1>
        <p style="color: #666; margin-bottom: 30px;">Connect with the world</p>
        <input type="text" id="uInp" placeholder="Username" style="width:100%; padding:15px; background:#111; border:1px solid #333; color:#fff; border-radius:12px; margin-bottom:15px; outline:none;">
        <button class="btn-p" onclick="login()">Log In</button>
    </div>
</div>

<div class="app-shell" style="max-width:500px; margin:0 auto;">
    <header id="mainHead">
        <h1>LiveConnect</h1>
        <div style="display:flex; gap:20px; font-size:22px;">
            <i class="fa-regular fa-square-plus" onclick="createNew()"></i>
            <i class="fa-brands fa-facebook-messenger" onclick="tab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="vPort">
        <div id="v-home" class="view active">
            <div class="story-tray">
                <div class="story-circle"><div class="inner-circle">You</div></div>
                <div class="story-circle"><div class="inner-circle">Admin</div></div>
                <div class="story-circle"><div class="inner-circle">Live</div></div>
            </div>
            <div id="feedArea"></div>
        </div>

        <div id="v-reels" class="view">
            <div class="reels-scroll">
                <div class="reel" style="display:flex; align-items:center; justify-content:center;">
                    <p style="color:#555">Video Reel 1 (Swipe Up)</p>
                    <div class="reel-ui"><i class="fa-solid fa-heart"></i><i class="fa-solid fa-comment"></i><i class="fa-solid fa-share"></i></div>
                </div>
                <div class="reel" style="background:#111; display:flex; align-items:center; justify-content:center;">
                    <p style="color:#555">Video Reel 2</p>
                    <div class="reel-ui"><i class="fa-solid fa-heart"></i><i class="fa-solid fa-comment"></i><i class="fa-solid fa-share"></i></div>
                </div>
            </div>
        </div>

        <div id="v-chat" class="view">
            <div id="chatArea" style="padding:15px; display:flex; flex-direction:column; gap:10px;"></div>
            <div style="position:fixed; bottom:70px; width:100%; max-width:500px; padding:10px; display:flex; gap:10px; background:var(--bg);">
                <input type="text" id="mInp" placeholder="Message..." style="flex:1; padding:12px; border-radius:20px; background:var(--card); color:#fff; border:1px solid var(--border);">
                <button onclick="sendMsg()" style="background:var(--p); border:none; color:#fff; border-radius:50%; width:40px; height:40px;"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>

        <div id="v-profile" class="view" style="padding:20px; text-align:center;">
            <div style="width:100px; height:100px; border-radius:50%; background:var(--insta); margin:0 auto; padding:4px;">
                <div style="width:100%; height:100%; background:#222; border-radius:50%; display:flex; align-items:center; justify-content:center; font-size:40px;" id="uAvatar">?</div>
            </div>
            <h2 id="uName">Username</h2>
            <div style="display:flex; justify-content:space-around; margin:20px 0; border-top:1px solid var(--border); padding-top:20px;">
                <div><b>12</b><br><small>Posts</small></div>
                <div><b>450</b><br><small>Followers</small></div>
                <div><b>120</b><br><small>Following</small></div>
            </div>
            <button onclick="localStorage.clear(); location.reload();" style="color:red; background:none; border:none; font-weight:800;">Log Out</button>
        </div>
    </div>

    <nav>
        <div class="nav-btn active" onclick="tab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="nav-btn"><i class="fa-solid fa-search"></i></div>
        <div class="nav-btn" onclick="tab('reels')"><i class="fa-solid fa-clapperboard"></i></div>
        <div class="nav-btn"><i class="fa-solid fa-bag-shopping"></i></div>
        <div class="nav-btn" onclick="tab('profile')"><i class="fa-solid fa-circle-user"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("meta_inf_user");
if(user) { document.getElementById("auth").style.display = "none"; init(); }

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(v) { localStorage.setItem("meta_inf_user", v); location.reload(); }
};

function init() {
    document.getElementById("uName").innerText = user;
    document.getElementById("uAvatar").innerText = user.charAt(0).toUpperCase();

    // Feed Sync
    onValue(ref(db, 'meta_v20_posts'), (snap) => {
        const area = document.getElementById("feedArea"); area.innerHTML = "";
        snap.forEach(s => {
            const p = s.val();
            area.innerHTML = `
                <div class="post">
                    <div class="post-header">
                        <div class="post-user"><div style="width:30px;height:30px;background:var(--p);border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:12px;">${p.user.charAt(0)}</div> ${p.user}</div>
                        ${p.user === user ? `<i class="fa-solid fa-ellipsis-v" onclick="del('meta_v20_posts/${s.key}')"></i>` : ''}
                    </div>
                    ${p.img ? `<img src="${p.img}" class="post-img">` : ''}
                    <div style="padding:10px 15px;">${p.text}</div>
                    <div class="post-actions">
                        <i class="fa-regular fa-heart" onclick="like('${s.key}')"></i>
                        <i class="fa-regular fa-comment" onclick="addComment('${s.key}')"></i>
                        <i class="fa-regular fa-paper-plane"></i>
                    </div>
                    <div class="comment-sec" id="comm-${s.key}"></div>
                </div>` + area.innerHTML;
            syncComments(s.key);
        });
    });

    // Chat Sync
    onValue(ref(db, 'meta_v20_chat'), (snap) => {
        const chat = document.getElementById("chatArea"); chat.innerHTML = "";
        snap.forEach(s => {
            const m = s.val();
            const isMe = m.user === user;
            chat.innerHTML += `<div style="align-self:${isMe?'flex-end':'flex-start'}; background:${isMe?var(--p):'#222'}; padding:10px; border-radius:15px; max-width:80%; font-size:14px;">
                <small style="font-size:9px; display:block; opacity:0.6">${m.user}</small>${m.text}</div>`;
        });
    });
}

window.createNew = () => {
    const t = prompt("What's on your mind?");
    if(t) push(ref(db, 'meta_v20_posts'), { user, text: t, likes: 0, time: Date.now() });
};

window.addComment = (id) => {
    const c = prompt("Write a comment:");
    if(c) push(ref(db, `meta_v20_comments/${id}`), { user, text: c });
};

function syncComments(id) {
    onValue(ref(db, `meta_v20_comments/${id}`), (snap) => {
        const cArea = document.getElementById(`comm-${id}`);
        if(!cArea) return;
        cArea.innerHTML = "";
        snap.forEach(s => {
            const c = s.val();
            cArea.innerHTML += `<div class="comment-item"><b>${c.user}</b> ${c.text}</div>`;
        });
    });
}

window.sendMsg = () => {
    const i = document.getElementById("mInp");
    if(i.value) { push(ref(db, 'meta_v20_chat'), { user, text: i.value }); i.value = ""; }
};

window.like = (id) => runTransaction(ref(db, `meta_v20_posts/${id}/likes`), (c) => (c || 0) + 1);
window.del = (path) => { if(confirm("Delete this?")) remove(ref(db, path)); };

window.tab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    event.currentTarget.classList.add('active');
    document.getElementById("mainHead").style.display = (t === 'reels') ? 'none' : 'flex';
};
</script>
</body>
</html>
