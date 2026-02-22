<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Ultimate</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
    <style>
        :root { --fb-blue: #1877F2; --bg: #18191A; --card: #242526; --text: #E4E6EB; --border: #3E4042; --input: #3A3B3C; }
        .light-mode { --bg: #F0F2F5; --card: #FFFFFF; --text: #050505; --border: #CED0D4; --input: #F0F2F5; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Inter', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
        header { padding: 12px 15px; background: var(--card); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; z-index: 1000; }
        header h1 { color: var(--fb-blue); font-size: 24px; font-weight: 800; margin: 0; letter-spacing: -1px; }

        .viewport { flex: 1; overflow-y: auto; padding: 10px; padding-bottom: 90px; }
        .post-card { background: var(--card); border-radius: 12px; padding: 15px; margin-bottom: 12px; border: 1px solid var(--border); }
        .pfp { width: 40px; height: 40px; border-radius: 50%; object-fit: cover; background: #555; }
        
        /* POST ACTIONS */
        .post-stats { display: flex; justify-content: space-between; padding: 8px 0; font-size: 13px; color: #b0b3b8; border-bottom: 1px solid var(--border); }
        .post-actions { display: flex; justify-content: space-around; padding-top: 10px; }
        .btn-action { background: none; border: none; color: #b0b3b8; font-weight: 600; font-size: 14px; cursor: pointer; display: flex; align-items: center; gap: 5px; }
        .btn-action.active { color: var(--fb-blue); }

        /* COMMENTS SECTION */
        .comment-box { margin-top: 10px; background: var(--input); border-radius: 15px; padding: 8px 12px; font-size: 13px; }
        .comment-input-area { display: flex; gap: 8px; margin-top: 10px; }
        .comment-input { flex: 1; background: var(--input); border: none; border-radius: 18px; padding: 8px 12px; color: var(--text); outline: none; font-size: 13px; }

        /* NAVIGATION */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 65px; background: var(--card); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-link { color: #8a8d91; text-align: center; cursor: pointer; font-size: 22px; }
        .nav-link.active { color: var(--fb-blue); }

        /* OVERLAYS */
        #loginOverlay { position: fixed; inset: 0; background: var(--bg); z-index: 5000; display: flex; align-items: center; justify-content: center; padding: 20px; }
    </style>
</head>
<body>

<div id="loginOverlay">
    <div style="background:var(--card); padding:30px; border-radius:20px; text-align:center; width:100%; max-width:350px;">
        <h1 style="color:var(--fb-blue)">liveconnect</h1>
        <input type="text" id="loginName" placeholder="Your Name" style="width:100%; padding:12px; border-radius:10px; border:1px solid var(--border); background:var(--input); color:var(--text); margin-top:20px;">
        <button onclick="doLogin()" style="width:100%; padding:12px; border-radius:10px; border:none; background:var(--fb-blue); color:white; font-weight:700; margin-top:15px; cursor:pointer;">Join Now</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h1>liveconnect</h1>
        <div style="display:flex; gap:15px;">
            <i class="fa-solid fa-moon" onclick="toggleTheme()" style="cursor:pointer"></i>
            <i class="fa-solid fa-right-from-bracket" onclick="logout()" style="cursor:pointer"></i>
        </div>
    </header>

    <div class="viewport" id="feedScroll">
        <div class="post-card">
            <div style="display:flex; gap:10px; align-items:center;">
                <div class="pfp" style="display:flex; align-items:center; justify-content:center; background:var(--fb-blue); color:white;"><i class="fa-solid fa-user"></i></div>
                <div onclick="createPost()" style="flex:1; background:var(--input); padding:10px 15px; border-radius:20px; color:#b0b3b8; cursor:pointer;">What's on your mind?</div>
            </div>
        </div>
        <div id="postsArea"></div>
    </div>

    <nav>
        <div class="nav-link active"><i class="fa-solid fa-house"></i></div>
        <div class="nav-link"><i class="fa-solid fa-user-group"></i></div>
        <div class="nav-link"><i class="fa-solid fa-message"></i></div>
        <div class="nav-link"><i class="fa-solid fa-bell"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, push, onValue, set, update, runTransaction } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("lc_user_v8");

if(user) {
    document.getElementById("loginOverlay").style.display = "none";
    initFeed();
}

window.doLogin = () => {
    const n = document.getElementById("loginName").value.trim();
    if(n) { localStorage.setItem("lc_user_v8", n); location.reload(); }
};

window.createPost = () => {
    const txt = prompt("Write your post...");
    if(txt) push(ref(db, 'posts_v8'), { user, text: txt, likes: 0, time: Date.now() });
};

function initFeed() {
    onValue(ref(db, 'posts_v8'), (snap) => {
        const area = document.getElementById("postsArea");
        area.innerHTML = "";
        let posts = [];
        snap.forEach(s => posts.unshift({id: s.key, ...s.val()}));
        
        posts.forEach(p => {
            const card = document.createElement("div");
            card.className = "post-card";
            card.innerHTML = `
                <div style="display:flex; gap:10px; align-items:center; margin-bottom:10px;">
                    <div class="pfp" style="background:#555"></div>
                    <div>
                        <div style="font-weight:700; font-size:14px">${p.user}</div>
                        <div style="font-size:11px; color:#b0b3b8">${new Date(p.time).toLocaleTimeString()}</div>
                    </div>
                </div>
                <div style="font-size:15px; margin-bottom:10px;">${p.text}</div>
                <div class="post-stats">
                    <span>👍 ${p.likes || 0} Likes</span>
                    <span>0 Comments</span>
                </div>
                <div class="post-actions">
                    <button class="btn-action" onclick="likePost('${p.id}')"><i class="fa-regular fa-thumbs-up"></i> Like</button>
                    <button class="btn-action" onclick="focusComment('${p.id}')"><i class="fa-regular fa-comment"></i> Comment</button>
                    <button class="btn-action"><i class="fa-solid fa-share"></i> Share</button>
                </div>
                <div class="comment-input-area">
                    <input type="text" class="comment-input" placeholder="Write a comment..." id="inp-${p.id}" onkeypress="addComment(event, '${p.id}')">
                </div>
                <div id="comments-${p.id}"></div>
            `;
            area.appendChild(card);
            syncComments(p.id);
        });
    });
}

window.likePost = (id) => {
    const postRef = ref(db, `posts_v8/${id}/likes`);
    runTransaction(postRef, (currentLikes) => (currentLikes || 0) + 1);
};

window.addComment = (e, postId) => {
    if(e.key === 'Enter') {
        const val = e.target.value.trim();
        if(val) {
            push(ref(db, `comments_v8/${postId}`), { user, text: val });
            e.target.value = "";
        }
    }
};

function syncComments(postId) {
    onValue(ref(db, `comments_v8/${postId}`), (snap) => {
        const cArea = document.getElementById(`comments-${postId}`);
        cArea.innerHTML = "";
        snap.forEach(s => {
            const c = s.val();
            cArea.innerHTML += `
                <div class="comment-box">
                    <b style="font-size:12px; color:var(--fb-blue)">${c.user}</b>: ${c.text}
                </div>`;
        });
    });
}

window.toggleTheme = () => document.body.classList.toggle('light-mode');
window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
