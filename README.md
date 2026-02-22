<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Global</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --fb-blue: #0866FF; --bg: #000; --card: #1c1c1e; --text: #fff; --border: #2c2c2e; --insta-grad: linear-gradient(45deg, #f09433, #e6683c, #dc2743, #cc2366, #bc1888); }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; margin: 0; padding: 0; }
        body, html { height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* LOGIN */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 25px; }
        .auth-card { width: 100%; max-width: 350px; text-align: center; }
        .auth-card h1 { font-size: 40px; font-weight: 800; color: var(--fb-blue); margin-bottom: 30px; }
        .auth-input { width: 100%; padding: 16px; background: #121212; border: 1px solid var(--border); border-radius: 12px; color: #fff; margin-bottom: 15px; outline: none; }
        .btn-join { width: 100%; padding: 16px; background: var(--fb-blue); border: none; border-radius: 12px; color: #fff; font-weight: 800; cursor: pointer; }

        /* APP */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
        header { padding: 12px 15px; display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.9); backdrop-filter: blur(10px); border-bottom: 1px solid var(--border); }
        header h2 { font-weight: 800; font-size: 24px; color: var(--fb-blue); }

        .viewport { flex: 1; overflow-y: auto; padding-bottom: 80px; scrollbar-width: none; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; }
        .view.active { display: block; }

        /* STORIES (INSTA STYLE) */
        .stories { display: flex; gap: 12px; padding: 15px; overflow-x: auto; border-bottom: 1px solid var(--border); }
        .s-ring { min-width: 65px; height: 65px; border-radius: 50%; padding: 2.5px; background: var(--insta-grad); position: relative; }
        .s-img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; border: 2px solid #000; background: #222; }

        /* COMPOSER (FB STYLE) */
        .composer { background: var(--card); margin: 10px; border-radius: 15px; padding: 12px; display: flex; align-items: center; gap: 10px; }
        .comp-btn { background: #2c2c2e; padding: 10px 15px; border-radius: 20px; flex: 1; color: #8e8e93; font-size: 14px; }

        /* POST CARDS */
        .post { background: var(--card); margin-bottom: 10px; border-top: 1px solid var(--border); border-bottom: 1px solid var(--border); }
        .p-head { padding: 12px; display: flex; align-items: center; justify-content: space-between; }
        .u-info { display: flex; align-items: center; gap: 10px; }
        .p-avatar { width: 35px; height: 35px; border-radius: 50%; object-fit: cover; }
        .p-img { width: 100%; max-height: 500px; object-fit: cover; }
        .p-actions { padding: 12px 15px; display: flex; gap: 20px; font-size: 22px; }

        /* CHAT */
        .chat-list { padding: 15px; display: flex; flex-direction: column; gap: 12px; }
        .msg { padding: 10px 15px; border-radius: 18px; max-width: 75%; font-size: 14px; }
        .msg.me { background: var(--fb-blue); align-self: flex-end; border-bottom-right-radius: 4px; }
        .msg.them { background: #2c2c2e; align-self: flex-start; border-bottom-left-radius: 4px; }

        /* NAV */
        nav { position: absolute; bottom: 0; width: 100%; height: 70px; background: rgba(0,0,0,0.95); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--border); }
        .n-btn { font-size: 22px; color: #8e8e93; cursor: pointer; }
        .n-btn.active { color: var(--fb-blue); }

        #chatDock { position: absolute; bottom: 70px; width: 100%; padding: 12px; background: #121212; border-top: 1px solid var(--border); display: none; gap: 10px; }
        #chatDock.active { display: flex; }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>LiveConnect</h1>
        <input type="text" id="uNameInp" class="auth-input" placeholder="Your Username...">
        <button class="btn-join" onclick="joinApp()">Log In</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2>LiveConnect</h2>
        <div style="display:flex; gap:20px; font-size:22px;">
            <label for="postFile"><i class="fa-regular fa-square-plus"></i></label>
            <input type="file" id="postFile" hidden accept="image/*" onchange="uploadPost(event)">
            <i class="fa-brands fa-facebook-messenger" onclick="switchTab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="vPort">
        <div id="v-home" class="view active">
            <div class="stories" id="storyRow"></div>
            <div class="composer">
                <img src="" class="p-avatar" id="compAvatar">
                <div class="comp-btn" onclick="triggerPost()">What's on your mind?</div>
            </div>
            <div id="mainFeed"></div>
        </div>

        <div id="v-chat" class="view">
            <div class="chat-list" id="chatList"></div>
        </div>

        <div id="v-profile" class="view" style="padding:40px 20px; text-align:center;">
            <label for="pfpFile">
                <div class="s-ring" style="width:110px; height:110px; margin:0 auto; padding:4px;">
                    <img src="" id="myPfp" class="s-img">
                </div>
            </label>
            <input type="file" id="pfpFile" hidden accept="image/*" onchange="updatePfp(event)">
            <h2 id="dispName" style="margin:15px 0;">User</h2>
            <p style="color:var(--fb-blue); font-weight:600;">Edit Profile Picture</p>
            <button onclick="logout()" style="margin-top:40px; color:red; background:none; border:none; font-weight:800;">Log Out</button>
        </div>
    </div>

    <div id="chatDock">
        <input type="text" id="mInp" style="flex:1; padding:12px; border-radius:25px; background:#2c2c2e; border:none; color:#fff; outline:none;" placeholder="Message...">
        <i class="fa-solid fa-paper-plane" onclick="sendMsg()" style="color:var(--fb-blue); font-size:22px; padding:5px;"></i>
    </div>

    <nav>
        <div class="n-btn active" onclick="switchTab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="n-btn"><i class="fa-solid fa-magnifying-glass"></i></div>
        <div class="n-btn" onclick="switchTab('chat')"><i class="fa-solid fa-play-circle"></i></div>
        <div class="n-btn" onclick="switchTab('profile')"><i class="fa-solid fa-circle-user"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);
let me = localStorage.getItem("lc_user_final");

if(me) { document.getElementById("authBox").style.display="none"; run(); }

window.joinApp = () => {
    const v = document.getElementById("uNameInp").value.trim();
    if(v) { localStorage.setItem("lc_user_final", v); location.reload(); }
};

function run() {
    document.getElementById("dispName").innerText = me;

    // Get My PFP
    onValue(ref(db, `users/${me}/pfp`), (s) => {
        const url = s.val() || "https://cdn-icons-png.flaticon.com/512/149/149071.png";
        document.getElementById("myPfp").src = url;
        document.getElementById("compAvatar").src = url;
    });

    // Sync Feed
    onValue(ref(db, 'global_feed'), (snap) => {
        const feed = document.getElementById("mainFeed"); feed.innerHTML = "";
        const story = document.getElementById("storyRow"); story.innerHTML = "";
        
        snap.forEach(s => {
            const p = s.val();
            // Stories
            story.innerHTML += `<div class="s-ring"><img src="${p.uPfp}" class="s-img"></div>`;
            
            // Post Card
            feed.innerHTML = `
                <div class="post">
                    <div class="p-head">
                        <div class="u-info"><img src="${p.uPfp}" class="p-avatar"><b>${p.user}</b></div>
                        ${p.user === me ? `<i class="fa-solid fa-trash" onclick="delPost('${s.key}')"></i>` : ''}
                    </div>
                    ${p.img ? `<img src="${p.img}" class="p-img">` : ''}
                    <div style="padding:15px;"><b>${p.user}</b> ${p.text}</div>
                    <div class="p-actions">
                        <i class="fa-regular fa-heart" onclick="like('${s.key}')"></i>
                        <i class="fa-regular fa-comment"></i>
                        <i class="fa-regular fa-paper-plane"></i>
                    </div>
                </div>` + feed.innerHTML;
        });
    });

    // Chat
    onValue(ref(db, 'global_chats'), (snap) => {
        const list = document.getElementById("chatList"); list.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.user === me;
            list.innerHTML += `<div class="msg ${isMe?'me':'them'}">${m.text}</div>`;
        });
        document.getElementById("vPort").scrollTop = document.getElementById("vPort").scrollHeight;
    });
}

window.updatePfp = (e) => {
    const reader = new FileReader();
    reader.onload = () => set(ref(db, `users/${me}/pfp`), reader.result);
    reader.readAsDataURL(e.target.files[0]);
};

window.triggerPost = () => {
    const t = prompt("What's on your mind?");
    if(t) uploadData(t, null);
};

window.uploadPost = (e) => {
    const reader = new FileReader();
    reader.onload = () => {
        const t = prompt("Add a caption...");
        uploadData(t || "", reader.result);
    };
    reader.readAsDataURL(e.target.files[0]);
};

function uploadData(txt, img) {
    const currentPfp = document.getElementById("myPfp").src;
    push(ref(db, 'global_feed'), { user: me, text: txt, img: img, uPfp: currentPfp, likes: 0 });
}

window.sendMsg = () => {
    const i = document.getElementById("mInp");
    if(i.value) { push(ref(db, 'global_chats'), { user: me, text: i.value }); i.value = ""; }
};

window.like = (id) => runTransaction(ref(db, `global_feed/${id}/likes`), (c) => (c || 0) + 1);
window.delPost = (id) => { if(confirm("Delete post?")) remove(ref(db, 'global_feed/'+id)); };

window.switchTab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.n-btn').forEach(n => n.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    document.getElementById('chatDock').classList.toggle('active', t === 'chat');
    const map = {'home':0, 'chat':2, 'profile':3};
    document.querySelectorAll('.n-btn')[map[t]].classList.add('active');
};

window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
