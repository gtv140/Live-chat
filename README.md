<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Meta OS</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        :root { --p: #0084FF; --bg: #000; --card: #121212; --text: #fff; --border: #262626; --notify: #FF3B30; }
        .light-mode { --bg: #fff; --card: #fff; --text: #000; --border: #dbdbdb; }
        
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body, html { margin: 0; padding: 0; height: 100%; background: var(--bg); color: var(--text); overflow: hidden; }

        /* HEADER & NOTIFICATION DOT */
        header { padding: 12px 15px; background: var(--bg); border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 1000; }
        header h1 { font-size: 22px; font-weight: 800; color: var(--text); font-style: italic; letter-spacing: -1px; }
        .notify-dot { position: absolute; top: -5px; right: -5px; width: 10px; height: 10px; background: var(--notify); border-radius: 50%; display: none; }

        /* VIEWPORT */
        .viewport { height: calc(100vh - 65px); overflow-y: auto; scrollbar-width: none; }
        .view { display: none; padding-bottom: 100px; }
        .view.active { display: block; animation: slideIn 0.3s ease; }

        /* POSTS & FOLLOW */
        .post { background: var(--card); border-bottom: 5px solid var(--border); }
        .post-header { padding: 12px; display: flex; align-items: center; justify-content: space-between; }
        .follow-btn { color: var(--p); font-size: 13px; font-weight: 700; cursor: pointer; }
        .follow-btn.following { color: #888; }

        /* NOTIFICATIONS VIEW */
        .notif-item { padding: 15px; border-bottom: 1px solid var(--border); display: flex; align-items: center; gap: 10px; font-size: 14px; }
        .notif-item .dot { width: 8px; height: 8px; background: var(--p); border-radius: 50%; }

        /* PROFILE STATS */
        .stat-box { text-align: center; flex: 1; }
        .stat-num { font-size: 18px; font-weight: 800; }
        .stat-label { font-size: 12px; opacity: 0.6; }

        /* NAV */
        nav { position: fixed; bottom: 0; width: 100%; max-width: 500px; height: 65px; background: var(--bg); border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 2000; }
        .nav-btn { font-size: 22px; color: var(--text); opacity: 0.5; position: relative; cursor: pointer; }
        .nav-btn.active { opacity: 1; color: var(--p); }

        @keyframes slideIn { from { transform: translateX(20px); opacity: 0; } to { transform: translateX(0); opacity: 1; } }
    </style>
</head>
<body>

<div id="auth" style="position:fixed; inset:0; background:#000; z-index:9999; display:flex; align-items:center; justify-content:center; padding:30px;">
    <div style="text-align:center; width: 100%; max-width: 320px;">
        <h1 style="font-size: 38px; color: var(--p); margin-bottom: 10px;">Meta Infinity</h1>
        <input type="text" id="uInp" placeholder="Choose a Username" style="width:100%; padding:15px; background:#111; border:1px solid #333; color:#fff; border-radius:12px; margin-bottom:15px; outline:none;">
        <button onclick="login()" style="width:100%; padding:15px; background:var(--p); border:none; border-radius:12px; color:#fff; font-weight:800;">Join Network</button>
    </div>
</div>

<div class="app-shell" style="max-width:500px; margin:0 auto; position:relative;">
    <header>
        <h1>LiveConnect</h1>
        <div style="display:flex; gap:20px;">
            <i class="fa-regular fa-heart" onclick="tab('notifs')"><div id="nDot" class="notify-dot"></div></i>
            <i class="fa-brands fa-facebook-messenger" onclick="tab('chat')"></i>
        </div>
    </header>

    <div class="viewport">
        <div id="v-home" class="view active">
            <div id="feedArea"></div>
        </div>

        <div id="v-notifs" class="view">
            <h3 style="padding:15px;">Notifications</h3>
            <div id="notifList"></div>
        </div>

        <div id="v-chat" class="view">
            <div id="chatArea" style="padding:15px; display:flex; flex-direction:column; gap:10px;"></div>
            <div style="position:fixed; bottom:70px; width:100%; max-width:500px; padding:10px; display:flex; gap:10px; background:var(--bg);">
                <input type="text" id="mInp" placeholder="Type message..." style="flex:1; padding:12px; border-radius:25px; background:var(--card); color:#fff; border:1px solid var(--border);">
                <button onclick="sendMsg()" style="background:var(--p); border:none; color:#fff; border-radius:50%; width:45px; height:45px;"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>

        <div id="v-profile" class="view" style="padding:20px; text-align:center;">
            <div style="width:90px; height:90px; border-radius:50%; background:var(--p); margin:0 auto; display:flex; align-items:center; justify-content:center; font-size:35px;" id="uAvatar">?</div>
            <h2 id="uName">User</h2>
            <div style="display:flex; margin:25px 0;">
                <div class="stat-box"><div class="stat-num" id="pCount">0</div><div class="stat-label">Posts</div></div>
                <div class="stat-box"><div class="stat-num">4.2k</div><div class="stat-label">Followers</div></div>
                <div class="stat-box"><div class="stat-num">890</div><div class="stat-label">Following</div></div>
            </div>
            <button onclick="localStorage.clear(); location.reload();" style="width:100%; padding:12px; border-radius:10px; background:var(--card); border:1px solid var(--border); color:#FF3B30; font-weight:700;">Logout</button>
        </div>
    </div>

    <nav>
        <div class="nav-btn active" onclick="tab('home')"><i class="fa-solid fa-house"></i></div>
        <div class="nav-btn"><i class="fa-solid fa-magnifying-glass"></i></div>
        <div class="nav-btn" onclick="addNewPost()"><i class="fa-regular fa-square-plus"></i></div>
        <div class="nav-btn" onclick="tab('notifs')"><i class="fa-regular fa-heart"></i></div>
        <div class="nav-btn" onclick="tab('profile')"><i class="fa-solid fa-circle-user"></i></div>
    </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, push, onValue, remove, runTransaction } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const conf = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(conf);
const db = getDatabase(app);

let user = localStorage.getItem("meta_os_user");
if(user) { document.getElementById("auth").style.display = "none"; init(); }

window.login = () => {
    const v = document.getElementById("uInp").value.trim();
    if(v) { localStorage.setItem("meta_os_user", v); location.reload(); }
};

function init() {
    document.getElementById("uName").innerText = user;
    document.getElementById("uAvatar").innerText = user.charAt(0).toUpperCase();

    // Feed with Follow Logic
    onValue(ref(db, 'meta_os_posts'), (snap) => {
        const area = document.getElementById("feedArea"); area.innerHTML = "";
        let count = 0;
        snap.forEach(s => {
            const p = s.val();
            if(p.user === user) count++;
            area.innerHTML = `
                <div class="post">
                    <div class="post-header">
                        <div style="display:flex; align-items:center; gap:10px;">
                            <div style="width:32px; height:32px; background:var(--p); border-radius:50%; display:flex; align-items:center; justify-content:center; font-size:12px;">${p.user.charAt(0)}</div>
                            <b>${p.user}</b>
                            ${p.user !== user ? `<span class="follow-btn" id="f-${s.key}" onclick="toggleFollow('${s.key}', '${p.user}')"> • Follow</span>` : ''}
                        </div>
                        <i class="fa-solid fa-ellipsis-h" style="opacity:0.5"></i>
                    </div>
                    <div style="padding:0 15px 15px; font-size:15px;">${p.text}</div>
                    <div style="padding:12px; display:flex; gap:20px; border-top:1px solid var(--border); font-size:20px;">
                        <i class="fa-regular fa-heart" onclick="like('${s.key}', '${p.user}')"></i>
                        <i class="fa-regular fa-comment"></i>
                        <i class="fa-regular fa-paper-plane"></i>
                    </div>
                </div>` + area.innerHTML;
        });
        document.getElementById("pCount").innerText = count;
    });

    // Notifications Logic
    onValue(ref(db, `meta_os_notifs/${user}`), (snap) => {
        const list = document.getElementById("notifList"); list.innerHTML = "";
        const dot = document.getElementById("nDot");
        let hasNew = false;
        snap.forEach(s => {
            hasNew = true;
            const n = s.val();
            list.innerHTML = `<div class="notif-item"><div class="dot"></div> <b>${n.from}</b> ${n.action}</div>` + list.innerHTML;
        });
        dot.style.display = hasNew ? "block" : "none";
    });

    // Chat Sync
    onValue(ref(db, 'meta_os_chat'), (snap) => {
        const chat = document.getElementById("chatArea"); chat.innerHTML = "";
        snap.forEach(s => {
            const m = s.val();
            const isMe = m.user === user;
            chat.innerHTML += `<div style="align-self:${isMe?'flex-end':'flex-start'}; background:${isMe?var(--p):'#222'}; padding:10px 15px; border-radius:18px; max-width:80%; font-size:14px; position:relative;">
                <small style="font-size:9px; display:block; opacity:0.5">${m.user}</small>${m.text}</div>`;
        });
    });
}

window.addNewPost = () => {
    const t = prompt("Write something...");
    if(t) push(ref(db, 'meta_os_posts'), { user, text: t, likes: 0 });
};

window.toggleFollow = (id, targetUser) => {
    const btn = document.getElementById(`f-${id}`);
    btn.innerText = " • Following";
    btn.classList.add("following");
    push(ref(db, `meta_os_notifs/${targetUser}`), { from: user, action: "started following you" });
};

window.like = (id, targetUser) => {
    runTransaction(ref(db, `meta_os_posts/${id}/likes`), (c) => (c || 0) + 1);
    if(targetUser !== user) {
        push(ref(db, `meta_os_notifs/${targetUser}`), { from: user, action: "liked your post" });
    }
};

window.sendMsg = () => {
    const i = document.getElementById("mInp");
    if(i.value) { push(ref(db, 'meta_os_chat'), { user, text: i.value }); i.value = ""; }
};

window.tab = (t) => {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
    document.getElementById('v-'+t).classList.add('active');
    if(t === 'notifs') document.getElementById("nDot").style.display = "none";
    // Find nav button for this tab
    const btns = document.querySelectorAll('.nav-btn');
    if(t === 'home') btns[0].classList.add('active');
    if(t === 'notifs') btns[3].classList.add('active');
    if(t === 'profile') btns[4].classList.add('active');
};
</script>
</body>
</html>
