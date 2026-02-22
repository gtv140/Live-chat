<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Social</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Segoe+UI:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        :root { --fb-blue: #1877F2; --fb-bg: #18191A; --fb-card: #242526; --fb-text: #E4E6EB; --border: #3E4042; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--fb-bg); color: var(--fb-text); overflow: hidden; }

        /* HEADER */
        header { padding: 10px 15px; background: var(--fb-card); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; position: fixed; top: 0; width: 100%; z-index: 1000; }
        header h1 { color: var(--fb-blue); font-size: 24px; font-weight: 800; margin: 0; letter-spacing: -1px; }

        .viewport { height: 100vh; overflow-y: auto; padding: 65px 10px 100px 10px; display: none; }
        .viewport.active { display: block; }

        /* STORIES */
        .story-container { display: flex; gap: 10px; overflow-x: auto; padding: 10px 0; scrollbar-width: none; }
        .story-card { min-width: 100px; height: 160px; background: var(--fb-card); border-radius: 12px; position: relative; overflow: hidden; border: 1px solid var(--border); }
        .story-card img { width: 100%; height: 100%; object-fit: cover; opacity: 0.8; }
        .story-card b { position: absolute; bottom: 10px; left: 10px; font-size: 12px; }

        /* POST CREATION */
        .create-post { background: var(--fb-card); padding: 15px; border-radius: 12px; margin-bottom: 15px; }
        .post-input-row { display: flex; gap: 10px; align-items: center; }
        .user-pfp { width: 40px; height: 40px; border-radius: 50%; background: #555; }
        .fake-input { flex: 1; background: #3A3B3C; padding: 10px 15px; border-radius: 20px; color: #B0B3B8; cursor: pointer; }

        /* FEED POSTS */
        .post-card { background: var(--fb-card); border-radius: 12px; padding: 15px; margin-bottom: 15px; border: 1px solid var(--border); }
        .post-header { display: flex; gap: 10px; align-items: center; margin-bottom: 12px; }
        .post-content { font-size: 15px; margin-bottom: 12px; line-height: 1.4; }
        .post-img { width: calc(100% + 30px); margin-left: -15px; max-height: 300px; object-fit: cover; margin-bottom: 12px; }
        .post-actions { display: flex; border-top: 1px solid var(--border); padding-top: 10px; justify-content: space-around; }
        .action-btn { color: #B0B3B8; font-size: 14px; font-weight: 600; cursor: pointer; }
        .action-btn i { margin-right: 5px; }

        /* NAVIGATION BOTTOM */
        nav { position: fixed; bottom: 0; width: 100%; height: 60px; background: var(--fb-card); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        .nav-link { color: #B0B3B8; font-size: 22px; cursor: pointer; position: relative; }
        .nav-link.active { color: var(--fb-blue); }
        .nav-link.active::after { content: ''; position: absolute; bottom: -18px; left: 0; width: 100%; height: 3px; background: var(--fb-blue); }

        /* MODAL FOR POSTING */
        #postModal { position: fixed; inset: 0; background: var(--fb-bg); z-index: 2000; display: none; flex-direction: column; padding: 20px; }
    </style>
</head>
<body>

<header>
    <h1>liveconnect</h1>
    <div style="display:flex; gap:15px;">
        <i class="fa-solid fa-circle-plus"></i>
        <i class="fa-solid fa-magnifying-glass"></i>
        <i class="fa-solid fa-bars"></i>
    </div>
</header>

<div id="v-home" class="viewport active">
    <div class="story-container">
        <div class="story-card"><img src="https://picsum.photos/200/300?1"><b>Add Story</b></div>
        <div class="story-card"><img src="https://picsum.photos/200/300?2"><b>John Doe</b></div>
        <div class="story-card"><img src="https://picsum.photos/200/300?3"><b>Sarah Ali</b></div>
    </div>

    <div class="create-post">
        <div class="post-input-row" onclick="openPostModal()">
            <div class="user-pfp"></div>
            <div class="fake-input">What's on your mind?</div>
        </div>
    </div>

    <div id="feedArea"></div>
</div>

<div id="v-chat" class="viewport">
    <h2 style="padding-left:10px">Messages</h2>
    <div id="chatList"></div>
</div>

<div id="postModal">
    <div style="display:flex; justify-content:space-between; margin-bottom:20px">
        <span onclick="closePostModal()">Cancel</span>
        <button onclick="publishPost()" style="background:var(--fb-blue); color:white; border:none; padding:5px 15px; border-radius:5px">Post</button>
    </div>
    <textarea id="postText" style="flex:1; background:transparent; border:none; color:white; font-size:18px; outline:none" placeholder="What's on your mind?"></textarea>
</div>

<nav>
    <div class="nav-link active" onclick="tab('home')"><i class="fa-solid fa-house"></i></div>
    <div class="nav-link" onclick="tab('video')"><i class="fa-solid fa-tv"></i></div>
    <div class="nav-link" onclick="tab('chat')"><i class="fa-solid fa-message"></i></div>
    <div class="nav-link" onclick="tab('notify')"><i class="fa-solid fa-bell"></i></div>
    <div class="nav-link" onclick="tab('profile')"><i class="fa-solid fa-circle-user"></i></div>
</nav>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, push, onValue, set } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);

let user = "Guest_" + Math.floor(Math.random()*999);

window.tab = (t) => {
    document.querySelectorAll('.viewport').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-link').forEach(l => l.classList.remove('active'));
    if(document.getElementById('v-'+t)) document.getElementById('v-'+t).classList.add('active');
    event.currentTarget.classList.add('active');
};

window.openPostModal = () => document.getElementById('postModal').style.display = 'flex';
window.closePostModal = () => document.getElementById('postModal').style.display = 'none';

window.publishPost = () => {
    const text = document.getElementById('postText').value;
    if(!text.trim()) return;
    push(ref(db, 'social_posts'), {
        user: user,
        content: text,
        time: Date.now(),
        likes: 0
    });
    document.getElementById('postText').value = "";
    closePostModal();
};

// Sync Feed
onValue(ref(db, 'social_posts'), (snap) => {
    const area = document.getElementById('feedArea');
    area.innerHTML = "";
    let posts = [];
    snap.forEach(p => posts.unshift({id: p.key, ...p.val()}));
    
    posts.forEach(p => {
        const card = document.createElement('div');
        card.className = "post-card";
        card.innerHTML = `
            <div class="post-header">
                <div class="user-pfp" style="width:35px; height:35px"></div>
                <div>
                    <div style="font-weight:700; font-size:14px">${p.user}</div>
                    <div style="font-size:11px; color:#B0B3B8">Just now</div>
                </div>
            </div>
            <div class="post-content">${p.content}</div>
            <div class="post-actions">
                <div class="action-btn"><i class="fa-regular fa-thumbs-up"></i> Like</div>
                <div class="action-btn"><i class="fa-regular fa-comment"></i> Comment</div>
                <div class="action-btn"><i class="fa-solid fa-share"></i> Share</div>
            </div>
        `;
        area.appendChild(card);
    });
});
</script>
</body>
</html>
