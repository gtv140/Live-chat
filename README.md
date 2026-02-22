<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Meta Edition</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --bg: #000000; --card: #121212; --text: #FFFFFF; --border: #262626; --input: #262626; }
        .light-mode { --bg: #FFFFFF; --card: #FFFFFF; --text: #000000; --border: #DBDBDB; --input: #EFEFEF; --p: #0095F6; }
        
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; transition: 0.3s; }

        /* AUTH OVERLAY */
        #authBox { position: fixed; inset: 0; background: var(--bg); z-index: 5000; display: flex; align-items: center; justify-content: center; padding: 25px; }
        .auth-card { background: var(--card); padding: 40px 20px; border-radius: 25px; text-align: center; width: 100%; max-width: 380px; border: 1px solid var(--border); box-shadow: 0 10px 40px rgba(0,0,0,0.5); }
        .auth-card h1 { font-size: 32px; font-weight: 800; background: linear-gradient(45deg, #f09433 0%, #e6683c 25%, #dc2743 50%, #cc2366 75%, #bc1888 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 30px; }

        /* HEADER */
        header { padding: 12px 15px; background: var(--bg); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 1000; }
        header h1 { font-size: 24px; font-weight: 800; color: var(--text); margin: 0; font-style: italic; }

        /* APP CONTENT */
        .main-container { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; }
        .viewport { flex: 1; overflow-y: auto; scrollbar-width: none; padding-bottom: 80px; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; }
        .view.active { display: block; animation: slideUp 0.3s ease; }

        /* INSTA STORIES */
        .story-tray { display: flex; gap: 12px; padding: 15px; overflow-x: auto; border-bottom: 1px solid var(--border); }
        .story { min-width: 65px; text-align: center; font-size: 11px; }
        .story-ring { width: 65px; height: 65px; border-radius: 50%; padding: 3px; background: linear-gradient(45deg, #f09433, #e6683c, #dc2743, #cc2366, #bc1888); margin-bottom: 5px; }
        .story-img { width: 100%; height: 100%; border-radius: 50%; border: 2px solid var(--bg); background: #333; }

        /* FB POSTS */
        .post { margin-bottom: 10px; background: var(--card); border-bottom: 1px solid var(--border); }
        .post-header { padding: 12px; display: flex; align-items: center; gap: 10px; }
        .avatar { width: 35px; height: 35px; border-radius: 50%; background: var(--p); display: flex; align-items: center; justify-content: center; font-weight: 800; font-size: 14px; color: #fff; }
        .post-text { padding: 0 15px 15px 15px; font-size: 14px; line-height: 1.5; }
        .post-stats { padding: 10px 15px; display: flex; justify-content: space-between; border-top: 1px solid var(--border); font-size: 12px; color: #8e8e8e; }
        .post-btns { display: flex; justify-content: space-around; padding: 5px 0; border-top: 1px solid var(--border); }
        .action-btn { background: none; border: none; color: var(--text); padding: 10px; font-size: 18px; cursor: pointer; }

        /* NAVIGATION BAR */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 60px; background: var(--bg); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-icon { font-size: 22px; color: var(--text); cursor: pointer; opacity: 0.6; }
        .nav-icon.active { opacity: 1; }

        /* PROFILE */
        .prof-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 2px; padding: 10px; }
        .prof-item { aspect-ratio: 1/1; background: var(--border); border-radius: 4px; display: flex; align-items: center; justify-content: center; font-size: 10px; color: #8e8e8e; }

        @keyframes slideUp { from { transform: translateY(20px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>LiveConnect</h1>
        <input type="text" id="username" placeholder="Phone, email or username" style="width:100%; padding:14px; background:var(--input); border:1px solid var(--border); border-radius:8px; color:var(--text); outline:none; margin-bottom:15px;">
        <button onclick="login()" style="width:100%; padding:14px; background:var(--p); color:white; border:none; border-radius:8px; font-weight:700; cursor:pointer;">Log In</button>
    </div>
</div>

<div class="main-container">
    <header>
        <h1>LiveConnect</h1>
        <div style="display:flex; gap:20px; font-size:20px;">
            <i class="fa-regular fa-heart" onclick="toggleTheme()"></i>
            <i class="fa-brands fa-facebook-messenger" onclick="tab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="appScroll">
        <div id="v-home" class="view active">
            <div class="story-tray">
                <div class="story"><div class="story-ring"><div class="story-img"></div></div><span>Your Story</span></div>
                <div class="story"><div class="story-ring"><div class="story-img"></div></div><span>Admin</span></div>
                <div class="story"><div class="story-ring"><div class="story-img"></div></div><span>Mverick</span></div>
            </div>
            <div id="feedList"></div>
        </div>

        <div id="v-profile" class="view">
            <div style="padding:20px; text-align:center;">
                <div class="avatar" style="width:90px; height:90px; font-size:32px; margin:0 auto 15px auto;" id="pfpLg">?</div>
                <h3 id="uDisplay">Username</h3>
                <p id="uBio" style="font-size:14px; opacity:0.8;">Live Connect User • Node v9.0</p>
                <button onclick="editBio()" style="padding:8px 30px; background:var(--input); color:var(--text); border:1px solid var(--border); border-radius:8px; font-weight:600;">Edit Profile</button>
            </div>
            <div class="prof-grid" id="myTimeline"></div>
        </div>
    </div>

    <div style="position:fixed; bottom:80px; right:20px;">
        <button onclick="newPost()" style="width:55px; height:55px; border-radius:50%; background:var(--p); border:none; color:white; font-size:24px; box-shadow: 0 4px 15px rgba(0,0,0,0.3);"><i class="fa-solid fa-plus"></i></button>
    </div>

    <nav>
        <div class="nav-icon active" onclick="tab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="nav-icon"><i class="fa-solid fa-magnifying-glass"></i></div>
        <div class="nav-icon"><i class="fa-solid fa-clapperboard"></i></div>
        <div class="nav-icon"><i class="fa-solid fa-bag-shopping"></i></div>
        <div class="nav-icon" onclick="tab('profile')"><i class="fa-solid fa-circle-user"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, push, onValue, set, runTransaction } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const fb = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(fb);
const db = getDatabase(app);

let user = localStorage.getItem("meta_user");

if(user) {
    document.getElementById("authBox").style.display = "none";
    initApp();
}

window.login = () => {
    const val = document.getElementById("username").value.trim();
    if(val.length > 2) { localStorage.setItem("meta_user", val); location.reload(); }
};

function initApp() {
    document.getElementById("uDisplay").innerText = user;
    document.getElementById("pfpLg").innerText = user.charAt(0).toUpperCase();

    // Feed Sync
    onValue(ref(db, 'meta_posts'), (snap) => {
        const list = document.getElementById("feedList");
        const mine = document.getElementById("myTimeline");
        list.innerHTML = "";
        mine.innerHTML = "";
        let posts = [];
        snap.forEach(s => posts.unshift({id: s.key, ...s.val()}));

        posts.forEach(p => {
            const card = document.createElement("div");
            card.className = "post";
            card.innerHTML = `
                <div class="post-header">
                    <div class="avatar">${p.user.charAt(0)}</div>
                    <div style="font-weight:700; font-size:14px">${p.user}</div>
                </div>
                <div class="post-text">${p.text}</div>
                <div class="post-stats">
                    <span id="l-${p.id}">❤️ ${p.likes || 0} Likes</span>
                    <span>0 Comments</span>
                </div>
                <div class="post-btns">
                    <button class="action-btn" onclick="react('${p.id}')"><i class="fa-regular fa-heart"></i></button>
                    <button class="action-btn"><i class="fa-regular fa-comment"></i></button>
                    <button class="action-btn"><i class="fa-regular fa-paper-plane"></i></button>
                </div>`;
            list.appendChild(card);
            
            if(p.user === user) {
                mine.innerHTML += `<div class="prof-item">${p.text.substring(0,20)}...</div>`;
            }
        });
    });
}

window.newPost = () => {
    const t = prompt("What's on your mind?");
    if(t) push(ref(db, 'meta_posts'), { user, text: t, likes: 0, time: Date.now() });
};

window.react = (id) => {
    runTransaction(ref(db, `meta_posts/${id}/likes`), (cur) => (cur || 0) + 1);
};

window.tab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-icon').forEach(i => i.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    event.currentTarget.classList.add('active');
};

window.toggleTheme = () => document.body.classList.toggle('light-mode');
window.editBio = () => {
    const b = prompt("Update your bio:");
    if(b) document.getElementById("uBio").innerText = b;
};
</script>
</body>
</html>
