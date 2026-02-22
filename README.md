<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Infinity</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --primary: #0084FF; --wa-green: #25D366; --bg: #0b0b0b; --card: #1c1c1e; --text: #ffffff; --border: #2c2c2e; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; margin: 0; padding: 0; }
        body, html { height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* AUTH SCREEN */
        #authBox { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; padding: 25px; }
        .auth-card { width: 100%; max-width: 360px; text-align: center; animation: fadeIn 0.8s ease; }
        .auth-card h1 { font-size: 42px; font-weight: 800; background: linear-gradient(45deg, var(--primary), #a2d2ff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 10px; }
        .auth-input { width: 100%; padding: 18px; background: #1a1a1a; border: 1px solid var(--border); border-radius: 15px; color: #fff; margin-bottom: 15px; outline: none; text-align: center; }
        .btn-join { width: 100%; padding: 18px; background: var(--primary); border: none; border-radius: 15px; color: #fff; font-weight: 800; cursor: pointer; box-shadow: 0 10px 20px rgba(0,132,255,0.3); }

        /* APP LAYOUT */
        .app-shell { max-width: 500px; margin: 0 auto; height: 100vh; display: flex; flex-direction: column; position: relative; border-left: 1px solid var(--border); border-right: 1px solid var(--border); }
        header { padding: 15px; display: flex; justify-content: space-between; align-items: center; background: rgba(11,11,11,0.9); backdrop-filter: blur(15px); border-bottom: 1px solid var(--border); z-index: 100; }
        header h2 { font-weight: 800; font-size: 22px; }

        .viewport { flex: 1; overflow-y: auto; padding-bottom: 90px; scrollbar-width: none; }
        .viewport::-webkit-scrollbar { display: none; }
        .view { display: none; height: 100%; }
        .view.active { display: block; animation: slideUp 0.4s ease; }

        /* STORIES (INSTA STYLE) */
        .story-bar { display: flex; gap: 12px; padding: 15px; overflow-x: auto; scrollbar-width: none; }
        .story-node { min-width: 68px; height: 68px; border-radius: 50%; padding: 3px; background: linear-gradient(45deg, #f09433, #dc2743, #cc2366); }
        .story-node img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; border: 2px solid #000; }

        /* FEED (FB STYLE) */
        .post { background: var(--card); margin-bottom: 12px; border-bottom: 1px solid var(--border); }
        .p-info { padding: 12px; display: flex; align-items: center; gap: 10px; justify-content: space-between; }
        .p-user { display: flex; align-items: center; gap: 10px; font-weight: 600; }
        .p-user img { width: 38px; height: 38px; border-radius: 50%; object-fit: cover; }
        .media-container { width: 100%; background: #000; display: flex; justify-content: center; }
        .media-content { width: 100%; max-height: 500px; object-fit: contain; }
        .p-text { padding: 12px 15px; font-size: 15px; line-height: 1.4; }
        .p-actions { padding: 10px 20px; display: flex; gap: 25px; font-size: 20px; color: #bbb; border-top: 1px solid #2c2c2e; }

        /* CHAT (WHATSAPP STYLE) */
        .chat-view { display: flex; flex-direction: column; gap: 8px; padding: 15px; background: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png'); background-attachment: fixed; min-height: 100%; }
        .wa-msg { padding: 8px 12px; border-radius: 12px; max-width: 80%; font-size: 14px; position: relative; box-shadow: 0 1px 2px rgba(0,0,0,0.3); }
        .wa-msg.me { background: #056162; color: #fff; align-self: flex-end; border-top-right-radius: 0; }
        .wa-msg.them { background: #262d31; color: #fff; align-self: flex-start; border-top-left-radius: 0; }
        .wa-time { font-size: 9px; opacity: 0.6; display: block; text-align: right; margin-top: 4px; }

        /* FOOTER NAV */
        nav { position: absolute; bottom: 0; width: 100%; height: 75px; background: rgba(20,20,20,0.95); display: flex; justify-content: space-around; align-items: center; border-top: 1px solid var(--border); z-index: 1000; }
        .n-btn { font-size: 24px; color: #777; cursor: pointer; position: relative; }
        .n-btn.active { color: var(--primary); }
        .n-btn.active::after { content: ''; position: absolute; top: -10px; left: 50%; transform: translateX(-50%); width: 4px; height: 4px; background: var(--primary); border-radius: 50%; }

        #chatDock { position: absolute; bottom: 75px; width: 100%; padding: 12px; background: #1c1c1e; display: none; gap: 10px; align-items: center; }
        #chatDock.active { display: flex; }
        .m-input { flex: 1; padding: 12px 18px; border-radius: 25px; background: #2c2c2e; border: none; color: #fff; outline: none; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes slideUp { from { transform: translateY(20px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
    </style>
</head>
<body>

<div id="authBox">
    <div class="auth-card">
        <h1>Infinity</h1>
        <p style="color: #888; margin-bottom: 25px;">Connect Smarter, Faster, Better.</p>
        <input type="text" id="uInp" class="auth-input" placeholder="What should we call you?">
        <button class="btn-join" onclick="login()">Start Experience</button>
    </div>
</div>

<div class="app-shell">
    <header>
        <h2 style="color: var(--primary);">LiveConnect</h2>
        <div style="display:flex; gap:22px; font-size:20px;">
            <label for="mediaInp"><i class="fa-solid fa-camera"></i></label>
            <input type="file" id="mediaInp" hidden accept="image/*,video/*" onchange="handleUpload(event)">
            <i class="fa-solid fa-magnifying-glass"></i>
            <i class="fa-brands fa-whatsapp" style="color:var(--wa-green)" onclick="tab('chat')"></i>
        </div>
    </header>

    <div class="viewport" id="vPort">
        <div id="v-home" class="view active">
            <div class="story-bar" id="storyRow"></div>
            <div id="feedRow"></div>
        </div>

        <div id="v-chat" class="view">
            <div class="chat-view" id="chatRow"></div>
        </div>

        <div id="v-profile" class="view" style="padding:30px 20px; text-align:center;">
            <div style="position:relative; width:120px; height:120px; margin:0 auto;">
                <label for="pfpInp"><img src="" id="myPfp" style="width:120px; height:120px; border-radius:50%; border:3px solid var(--primary); object-fit:cover; cursor:pointer;"></label>
                <input type="file" id="pfpInp" hidden accept="image/*" onchange="updatePfp(event)">
            </div>
            <h2 id="myName" style="margin-top:20px;">User</h2>
            <div style="display:flex; justify-content:space-around; margin:30px 0; background:var(--card); padding:20px; border-radius:20px; border:1px solid var(--border);">
                <div><b>1.2k</b><br><small>Following</small></div>
                <div><b>50.4k</b><br><small>Followers</small></div>
                <div><b>230</b><br><small>Posts</small></div>
            </div>
            <button onclick="localStorage.clear(); location.reload();" style="width:100%; padding:15px; border-radius:15px; background:rgba(255,0,0,0.1); border:1px solid rgba(255,0,0,0.2); color:#ff4d4d; font-weight:800;">Log Out</button>
        </div>
    </div>

    <div id="chatDock">
        <i class="fa-solid fa-paperclip" style="color:#888;"></i>
        <input type="text" id="mInp" class="m-input" placeholder="Message">
        <div onclick="sendChat()" style="background:var(--wa-green); width:45px; height:45px; border-radius:50%; display:flex; align-items:center; justify-content:center; cursor:pointer;"><i class="fa-solid fa-paper-plane" style="color:#fff;"></i></div>
    </div>

    <nav>
        <div class="n-btn active" onclick="tab('home')"><i class="fa-solid fa-house-variant"></i></div>
        <div class="n-btn"><i class="fa-solid fa-compass"></i></div>
        <div class="n-btn" onclick="tab('chat')"><i class="fa-solid fa-comment-dots"></i></div>
        <div class="n-btn" onclick="tab('profile')"><i class="fa-solid fa-circle-user"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction, set } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

const fbCfg = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(fbCfg);
const db = getDatabase(app);
let user = localStorage.getItem("lc_inf_user");

if(user) { document.getElementById("authBox").style.display="none"; startApp(); }

window.login = () => {
    const n = document.getElementById("uInp").value.trim();
    if(n) { localStorage.setItem("lc_inf_user", n); location.reload(); }
};

function startApp() {
    document.getElementById("myName").innerText = user;
    
    // Profile Photo Sync
    onValue(ref(db, `users/${user}/pfp`), (s) => {
        const url = s.val() || "https://cdn-icons-png.flaticon.com/512/149/149071.png";
        document.getElementById("myPfp").src = url;
    });

    // Real-time Global Feed (Facebook/Instagram Mix)
    onValue(ref(db, 'infinity_feed'), (snap) => {
        const feed = document.getElementById("feedRow"); feed.innerHTML = "";
        const story = document.getElementById("storyRow"); story.innerHTML = "";
        snap.forEach(s => {
            const p = s.val();
            story.innerHTML += `<div class="story-node"><img src="${p.uPfp}"></div>`;
            
            let mediaHtml = "";
            if(p.type === "video") {
                mediaHtml = `<video src="${p.media}" class="media-content" controls loop muted autoplay></video>`;
            } else if(p.media) {
                mediaHtml = `<img src="${p.media}" class="media-content">`;
            }

            feed.innerHTML = `
                <div class="post">
                    <div class="p-info">
                        <div class="p-user"><img src="${p.uPfp}"> ${p.user}</div>
                        ${p.user === user ? `<i class="fa-solid fa-ellipsis-v" onclick="delPost('${s.key}')"></i>` : ''}
                    </div>
                    <div class="media-container">${mediaHtml}</div>
                    <div class="p-text"><b>${p.user}</b> ${p.text}</div>
                    <div class="p-actions">
                        <i class="fa-regular fa-heart" onclick="like('${s.key}')"> ${p.likes||0}</i>
                        <i class="fa-regular fa-comment"></i>
                        <i class="fa-regular fa-paper-plane"></i>
                    </div>
                </div>` + feed.innerHTML;
        });
    });

    // Real-time Global WhatsApp-style Chat
    onValue(ref(db, 'infinity_chats'), (snap) => {
        const chat = document.getElementById("chatRow"); chat.innerHTML = "";
        snap.forEach(s => {
            const m = s.val(); const isMe = m.user === user;
            chat.innerHTML += `
                <div class="wa-msg ${isMe?'me':'them'}">
                    <small style="font-size:10px; opacity:0.7; font-weight:800;">${m.user}</small><br>
                    ${m.text}
                    <span class="wa-time">${new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})} ${isMe?'✓✓':''}</span>
                </div>`;
        });
        document.getElementById("vPort").scrollTop = document.getElementById("vPort").scrollHeight;
    });
}

window.handleUpload = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    const type = file.type.startsWith('video') ? 'video' : 'image';
    reader.onload = () => {
        const cap = prompt("Write a caption...");
        const pfp = document.getElementById("myPfp").src;
        push(ref(db, 'infinity_feed'), { user, text: cap||"", media: reader.result, type, uPfp: pfp, likes: 0 });
    };
    reader.readAsDataURL(file);
};

window.updatePfp = (e) => {
    const reader = new FileReader();
    reader.onload = () => set(ref(db, `users/${user}/pfp`), reader.result);
    reader.readAsDataURL(e.target.files[0]);
};

window.sendChat = () => {
    const i = document.getElementById("mInp");
    if(i.value.trim()) { push(ref(db, 'infinity_chats'), { user, text: i.value }); i.value = ""; }
};

window.like = (id) => runTransaction(ref(db, `infinity_feed/${id}/likes`), (c) => (c || 0) + 1);
window.delPost = (id) => { if(confirm("Delete post?")) remove(ref(db, 'infinity_feed/'+id)); };

window.tab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.n-btn').forEach(n => n.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    document.getElementById('chatDock').classList.toggle('active', t === 'chat');
    const m = {'home':0, 'chat':2, 'profile':3};
    document.querySelectorAll('.n-btn')[m[t]].classList.add('active');
};
</script>
</body>
</html>
