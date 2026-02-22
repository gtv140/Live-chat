<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Super-App</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --bg: #000; --card: #121212; --text: #fff; --border: #262626; --accent: #1c1c1c; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.25s; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* AUTH */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 25px; }
        .auth-card { width: 100%; max-width: 380px; text-align: center; }
        .auth-card h1 { font-size: 48px; font-weight: 800; background: linear-gradient(45deg, #0084FF, #ff007a); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 10px; }
        .auth-input { width: 100%; padding: 18px; background: var(--accent); border: 1px solid var(--border); border-radius: 18px; color: #fff; margin-bottom: 15px; outline: none; }
        .btn-join { width: 100%; padding: 18px; background: var(--p); border: none; border-radius: 18px; color: #fff; font-weight: 800; cursor: pointer; }

        /* LAYOUT */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
        header { padding: 15px; display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.9); backdrop-filter: blur(10px); z-index: 100; }
        header h2 { font-weight: 800; font-size: 24px; color: #fff; }

        .viewport { flex: 1; overflow-y: auto; padding-bottom: 90px; scrollbar-width: none; scroll-snap-type: y proximity; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; height: 100%; }
        .view.active { display: block; }

        /* STORIES (INSTA STYLE) */
        .stories { display: flex; gap: 12px; padding: 10px 15px; overflow-x: auto; background: #000; border-bottom: 1px solid var(--border); }
        .s-circle { min-width: 65px; height: 65px; border-radius: 50%; border: 2px solid #ff007a; padding: 2px; }
        .s-img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; background: var(--accent); }

        /* POSTS (FB STYLE) */
        .post { background: var(--card); margin: 10px; border-radius: 20px; border: 1px solid var(--border); overflow: hidden; }
        .p-head { padding: 12px; display: flex; align-items: center; gap: 10px; }
        .p-pfp { width: 38px; height: 38px; border-radius: 50%; object-fit: cover; border: 1px solid var(--border); }
        .p-img { width: 100%; max-height: 450px; object-fit: cover; }
        .p-btns { padding: 15px; display: flex; justify-content: space-between; font-size: 22px; color: #bbb; }

        /* REELS (TIKTOK STYLE) */
        .reel-card { height: 100%; width: 100%; scroll-snap-align: start; position: relative; background: #000; }
        .reel-video { width: 100%; height: 100%; object-fit: cover; }
        .reel-ui { position: absolute; bottom: 100px; right: 15px; display: flex; flex-direction: column; gap: 20px; align-items: center; text-shadow: 0 2px 4px #000; }

        /* CHAT */
        .chat-box { padding: 20px; display: flex; flex-direction: column; gap: 15px; }
        .bubble { padding: 12px 18px; border-radius: 20px; max-width: 75%; font-size: 14px; position: relative; }
        .bubble.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 4px; }
        .bubble.them { background: var(--accent); align-self: flex-start; border-bottom-left-radius: 4px; }

        /* NAV */
        nav { position: absolute; bottom: 0; width: 100%; height: 75px; background: rgba(0,0,0,0.9); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 200; }
        .n-btn { font-size: 24px; color: #777; cursor: pointer; }
        .n-btn.active { color: #fff; transform: scale(1.1); }

        #chatDock { position: absolute; bottom: 75px; width: 100%; padding: 12px; background: #111; display: none; gap: 10px; border-top: 1px solid var(--border); }
        #chatDock.active { display: flex; }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>Connect</h1>
        <p style="color: #666; margin-bottom: 30px;">The Only App You'll Ever Need.</p>
        <input type="text" id="uInp" class="auth-input" placeholder="Choose a Username">
        <button class="btn-join" onclick="join()">Enter Infinity</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2>Connect</h2>
        <div style="display:flex; gap:20px; font-size:22px;">
            <label for="fileInp"><i class="fa-solid fa-plus-square"></i></label>
            <input type="file" id="fileInp" hidden accept="image/*,video/*" onchange="upload(event)">
            <i class="fa-brands fa-facebook-messenger" onclick="tab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="vPort">
        <div id="v-home" class="view active">
            <div class="stories" id="storyRow"></div>
            <div id="feedRow"></div>
        </div>

        <div id="v-reels" class="view" style="height: 100%; overflow-y: scroll; scroll-snap-type: y mandatory;">
            <div id="reelRow" style="height: 100%;"></div>
        </div>

        <div id="v-chat" class="view">
            <div class="chat-box" id="chatRow"></div>
        </div>

        <div id="v-profile" class="view" style="padding:40px 20px; text-align:center;">
            <label for="pfpInp"><img src="" id="myPfp" class="s-img" style="width:120px; height:120px; border:3px solid var(--p); margin:0 auto; display:block; cursor:pointer;"></label>
            <input type="file" id="pfpInp" hidden accept="image/*" onchange="updatePfp(event)">
            <h2 id="myName" style="margin-top:20px;">User <i class="fa-solid fa-circle-check" style="color:var(--p); font-size:16px;"></i></h2>
            <div style="display:flex; justify-content:space-around; margin:30px 0; background:var(--accent); padding:20px; border-radius:20px;">
                <div><b>10.2k</b><br><small>Followers</small></div>
                <div><b>540</b><br><small>Following</small></div>
                <div><b>Live</b><br><small>Status</small></div>
            </div>
            <button onclick="localStorage.clear(); location.reload();" style="color:red; background:none; border:none; font-weight:800;">Log Out</button>
        </div>
    </div>

    <div id="chatDock">
        <input type="text" id="mInp" style="flex:1; padding:12px; border-radius:25px; background:var(--accent); border:none; color:#fff; outline:none;" placeholder="Message...">
        <i class="fa-solid fa-paper-plane" onclick="send()" style="color:var(--p); font-size:22px; padding:5px;"></i>
    </div>

    <nav>
        <div class="n-btn active" onclick="tab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="n-btn" onclick="tab('reels')"><i class="fa-solid fa-play-circle"></i></div>
        <div class="n-btn" onclick="tab('chat')"><i class="fa-solid fa-comment"></i></div>
        <div class="n-btn" onclick="tab('profile')"><i class="fa-solid fa-user"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);
let user = localStorage.getItem("lc_super_user");

if(user) { document.getElementById("authBox").style.display="none"; init(); }

window.join = () => {
    const n = document.getElementById("uInp").value.trim();
    if(n) { localStorage.setItem("lc_super_user", n); location.reload(); }
};

function init() {
    document.getElementById("myName").innerHTML = `${user} <i class="fa-solid fa-circle-check" style="color:var(--p); font-size:16px;"></i>`;
    
    // Get PFP
    onValue(ref(db, `users/${user}/pfp`), (s) => {
        const url = s.val() || "https://cdn-icons-png.flaticon.com/512/149/149071.png";
        document.getElementById("myPfp").src = url;
    });

    // Sync Everything (Feed + Reels)
    onValue(ref(db, 'content'), (snap) => {
        const feed = document.getElementById("feedRow"); feed.innerHTML = "";
        const reels = document.getElementById("reelRow"); reels.innerHTML = "";
        const stories = document.getElementById("storyRow"); stories.innerHTML = "";
        
        snap.forEach(s => {
            const p = s.val();
            // Stories (Latest 5)
            stories.innerHTML += `<div class="s-circle"><img src="${p.uPfp || 'https://cdn-icons-png.flaticon.com/512/149/149071.png'}" class="s-img"></div>`;
            
            // Feed
            feed.innerHTML = `
                <div class="post">
                    <div class="p-head"><img src="${p.uPfp}" class="p-pfp"><b>${p.user}</b></div>
                    ${p.img ? `<img src="${p.img}" class="p-img">` : ''}
                    <div style="padding:15px;">${p.text}</div>
                    <div class="p-btns"><i class="fa-solid fa-heart" onclick="like('${s.key}')"> ${p.likes||0}</i><i class="fa-solid fa-comment"></i><i class="fa-solid fa-share"></i></div>
                </div>` + feed.innerHTML;

            // Reels (If image/video exists)
            if(p.img) {
                reels.innerHTML += `
                    <div class="reel-card">
                        <img src="${p.img}" class="reel-video">
                        <div class="reel-ui">
                            <img src="${p.uPfp}" style="width:45px; height:45px; border-radius:50%; border:2px solid #fff;">
                            <i class="fa-solid fa-heart" style="font-size:30px;"></i>
                            <i class="fa-solid fa-comment" style="font-size:30px;"></i>
                            <i class="fa-solid fa-share" style="font-size:30px;"></i>
                        </div>
                        <div style="position:absolute; bottom:20px; left:15px; text-shadow:0 2px 4px #000;">
                            <b>@${p.user}</b><p>${p.text}</p>
                        </div>
                    </div>`;
            }
        });
    });

    // Chat Sync
    onValue(ref(db, 'chats'), (snap) => {
        const row = document.getElementById("chatRow"); row.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.user === user;
            row.innerHTML += `<div class="bubble ${isMe?'me':'them'}">${m.text}</div>`;
        });
    });
}

window.updatePfp = (e) => {
    const reader = new FileReader();
    reader.onload = () => set(ref(db, `users/${user}/pfp`), reader.result);
    reader.readAsDataURL(e.target.files[0]);
};

window.upload = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const cap = prompt("Caption?");
        push(ref(db, 'content'), { user, text: cap||"", img: reader.result, uPfp: document.getElementById("myPfp").src, likes: 0 });
    };
    reader.readAsDataURL(e.target.files[0]);
};

window.send = () => {
    const i = document.getElementById("mInp");
    if(i.value) { push(ref(db, 'chats'), { user, text: i.value }); i.value = ""; }
};

window.like = (id) => runTransaction(ref(db, `content/${id}/likes`), (c) => (c || 0) + 1);

window.tab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.n-btn').forEach(n => n.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    document.getElementById('chatDock').classList.toggle('active', t === 'chat');
    const m = {'home':0, 'reels':1, 'chat':2, 'profile':3};
    document.querySelectorAll('.n-btn')[m[t]].classList.add('active');
};
</script>
</body>
</html>
