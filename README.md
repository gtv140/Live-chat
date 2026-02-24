<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Empire Sovereign | God Mode</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root { --p: #a855f7; --s: #ec4899; --bg: #030712; --glass: rgba(30, 41, 59, 0.7); }
* { box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.3s; }
body { margin: 0; background: var(--bg); color: #f9fafb; height: 100dvh; display: flex; overflow: hidden; }

/* Navigation Sidebar */
.sidebar { width: 60px; background: #000; border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; align-items: center; padding: 20px 0; gap: 20px; transition: all 0.3s; position: relative; z-index: 100; }
.nav-btn { width: 45px; height: 45px; border-radius: 15px; display: flex; align-items: center; justify-content: center; color: #4b5563; cursor: pointer; font-size: 1.2rem; }
.nav-btn.active { background: var(--p); color: white; box-shadow: 0 0 20px rgba(168, 85, 247, 0.4); }

/* Sidebar toggle button for mobile */
#menuToggle { display:none; position: fixed; top: 15px; left: 15px; z-index: 2000; width:40px; height:40px; border-radius:50%; background: var(--p); color:#fff; border:none; font-size:1.2rem; cursor:pointer; }

/* Main UI */
.main { flex: 1; display: flex; flex-direction: column; position: relative; }
header { padding: 12px 20px; display: flex; justify-content: space-between; align-items: center; background: rgba(3, 7, 18, 0.9); backdrop-filter: blur(15px); border-bottom: 1px solid rgba(255,255,255,0.05); font-size: 0.95rem; }
.brand { font-weight: 900; font-size: 1.2rem; background: linear-gradient(to right, #c084fc, #fb7185); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

#adminTools { display: none; background: #ef4444; padding: 6px; text-align: center; gap: 15px; justify-content: center; align-items: center; font-size: 0.8rem; flex-wrap: wrap; }
.tool-btn { background: white; color: black; border: none; padding: 2px 8px; border-radius: 5px; font-size: 10px; font-weight: 800; cursor: pointer; }

.content { flex: 1; display: flex; overflow: hidden; transition: all 0.3s; }
.chat-view { flex: 1; display: flex; flex-direction: column; position: relative; }
.feed { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px; padding-bottom: 100px; }

/* User List */
.user-list { width: 220px; background: rgba(255,255,255,0.02); padding: 15px; border-left: 1px solid rgba(255,255,255,0.05); transition: all 0.3s; }
@media (max-width: 850px) { 
    .user-list { display: none; }
    #menuToggle { display:block; }
    .sidebar { position: fixed; left: -80px; top:0; height:100%; flex-direction: column; }
    .sidebar.show { left:0; }
}
.u-card { display: flex; align-items: center; gap: 10px; padding: 8px; border-radius: 12px; cursor: pointer; margin-bottom: 6px; background: rgba(255,255,255,0.03); }
.u-card:hover { background: rgba(255,255,255,0.08); }
.ban-hammer { color: #ef4444; margin-left: auto; font-size: 11px; font-weight: 800; border: 1px solid #ef4444; padding: 2px 5px; border-radius: 4px; display: none; }

/* Message Bubbles */
.msg { max-width: 80%; padding: 10px 14px; border-radius: 18px; font-size: 13px; position: relative; word-wrap: break-word; }
.me { align-self: flex-end; background: var(--p); border-bottom-right-radius: 4px; }
.other { align-self: flex-start; background: var(--glass); border-bottom-left-radius: 4px; }
.msg img { max-width: 100%; border-radius: 10px; margin-top: 6px; }

/* Input Dock */
.dock { position: absolute; bottom: 15px; left: 10px; right: 10px; background: rgba(30, 41, 59, 0.95); padding: 6px; border-radius: 50px; display: flex; align-items: center; gap: 8px; border: 1px solid rgba(255,255,255,0.1); }
.dock input { flex: 1; background: none; border: none; color: white; outline: none; padding-left: 8px; font-size: 0.95rem; }
.dock button { width: 36px; height: 36px; border-radius: 50%; display: flex; align-items: center; justify-content: center; }
.mute-msg { position: absolute; inset: 0; background: rgba(0,0,0,0.85); color: #ef4444; display: none; align-items: center; justify-content: center; border-radius: 50px; font-weight: 800; font-size: 12px; text-align:center; padding:10px; }

/* Auth Screen */
#auth { position: fixed; inset: 0; z-index: 2000; background: #030712; display: flex; align-items: center; justify-content: center; }
.auth-box { width: 90%; max-width: 350px; text-align: center; padding: 35px 25px; background: var(--glass); border-radius: 30px; border: 1px solid rgba(255,255,255,0.1); }
.auth-box input { font-size: 1rem; }
.auth-box button { font-size: 1rem; }
</style>
</head>
<body>

<button id="menuToggle"><i class="fa-solid fa-bars"></i></button>

<div id="auth">
    <div class="auth-box">
        <h1 class="brand" style="font-size: 2rem;">EMPIRE_v2</h1>
        <input type="text" id="loginName" placeholder="Codename" style="width:100%; padding:15px; border-radius:15px; border:none; background:#000; color:#fff; margin-bottom:15px; text-align:center;">
        <button onclick="boot()" style="width:100%; padding:15px; border-radius:15px; border:none; background:var(--p); color:white; font-weight:800; cursor:pointer;">INITIALIZE</button>
    </div>
</div>

<nav class="sidebar">
    <div class="nav-btn active" onclick="goGlobal()" id="glob"><i class="fa-solid fa-globe"></i></div>
    <div class="nav-btn" onclick="logout()"><i class="fa-solid fa-power-off"></i></div>
</nav>

<div class="main">
    <div id="adminTools">
        <span style="font-size: 10px; font-weight: 800;">GOD MODE ACTIVE</span>
        <button class="tool-btn" onclick="toggleMute()">TOGGLE MUTE</button>
        <button class="tool-btn" onclick="wipeData()" style="background:#ef4444; color:white;">WIPE CHAT</button>
    </div>
    <header>
        <div class="brand" id="title">GLOBAL_FEED</div>
        <div id="badge" style="display:none; color:gold;"><i class="fa-solid fa-crown"></i></div>
    </header>

    <div class="content">
        <div class="chat-view">
            <div class="feed" id="feed"></div>
            <div class="dock" id="inputDock">
                <label for="pUp" style="padding:10px; cursor:pointer; opacity:0.6"><i class="fa-solid fa-image"></i></label>
                <input type="file" id="pUp" hidden onchange="upImg(this)">
                <input type="text" id="mIn" placeholder="Enter signal..." onkeydown="if(event.key==='Enter') send()">
                <button onclick="send()" style="background:var(--p); border:none; color:white; width:40px; height:40px; border-radius:50%; cursor:pointer;"><i class="fa-solid fa-paper-plane"></i></button>
                <div class="mute-msg" id="muteScreen">🔇 GLOBAL CHAT IS LOCKED</div>
            </div>
        </div>
        <aside class="user-list">
            <h5 style="opacity:0.5; margin-bottom:15px;">ASSETS</h5>
            <div id="uList"></div>
        </aside>
    </div>
</div>

<script type="module">
// Firebase imports & config (same as original)
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp, remove, query, limitToLast } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
const auth = getAuth(app);
const db = getDatabase(app);

let uUID, uName, isAdmin = false, currentRoom = "global_v2";

onAuthStateChanged(auth, user => {
    if (user) {
        const savedName = localStorage.getItem("uN");
        const savedAdmin = localStorage.getItem("uA") === "true";
        if (savedName) {
            uUID = user.uid; uName = savedName; isAdmin = savedAdmin;
            checkBan(user.uid);
        }
    }
});

window.boot = async () => {
    const n = document.getElementById("loginName").value.trim();
    if(!n) return;
    let admin = false;
    if(n.toLowerCase() === 'nazim') {
        if(prompt("Master PIN:") === "786") admin = true;
        else { alert("Wrong PIN"); return; }
    }
    const res = await signInAnonymously(auth);
    uUID = res.user.uid; uName = n; isAdmin = admin;
    localStorage.setItem("uN", n); localStorage.setItem("uA", admin);
    checkBan(uUID);
};

function checkBan(uid) {
    onValue(ref(db, 'banned/' + uid), s => {
        if(s.val()) { 
            document.body.innerHTML = "<h1 style='color:red; text-align:center; padding:50px;'>🚫 PERMANENTLY BANNED</h1>";
            localStorage.clear();
        } else { start(); }
    });
}

function start() {
    document.getElementById("auth").style.display = "none";
    if(isAdmin) {
        document.getElementById("adminTools").style.display = "flex";
        document.getElementById("badge").style.display = "block";
    }
    set(ref(db, 'nodes/' + uUID), { name: uName, online: true });
    syncMsgs(); syncUsers();
    onValue(ref(db, 'system/mute'), s => {
        document.getElementById("muteScreen").style.display = (s.val() && !isAdmin) ? "flex" : "none";
    });
}

window.send = (p = null) => {
    const i = document.getElementById("mIn");
    if(!i.value.trim() && !p) return;
    push(ref(db, currentRoom), { uid: uUID, sender: uName, text: i.value, img: p, ts: serverTimestamp() });
    i.value = "";
};

window.upImg = (el) => {
    const r = new FileReader();
    r.onload = (e) => send(e.target.result);
    r.readAsDataURL(el.files[0]);
};

window.goGlobal = () => {
    currentRoom = "global_v2";
    document.getElementById("title").innerText = "GLOBAL_FEED";
    document.getElementById("glob").classList.add("active");
    syncMsgs();
};

window.openPriv = (tid, tname) => {
    if(tid === uUID) return;
    const rid = uUID < tid ? `${uUID}_${tid}` : `${tid}_${uUID}`;
    currentRoom = `private/${rid}`;
    document.getElementById("title").innerText = "🔒 " + tname;
    document.getElementById("glob").classList.remove("active");
    syncMsgs();
};

function syncMsgs() {
    onValue(query(ref(db, currentRoom), limitToLast(40)), s => {
        const f = document.getElementById("feed");
        f.innerHTML = "";
        s.forEach(m => {
            const d = m.val();
            f.innerHTML += `<div class="msg ${d.uid === uUID ? 'me' : 'other'}">
                <small style="opacity:0.5; font-size:10px; font-weight:800;">${d.sender}</small>
                ${d.text ? `<div>${d.text}</div>` : ''}
                ${d.img ? `<img src="${d.img}">` : ''}
            </div>`;
        });
        f.scrollTop = f.scrollHeight;
    });
}

function syncUsers() {
    onValue(ref(db, 'nodes'), s => {
        const l = document.getElementById("uList");
        l.innerHTML = "";
        s.forEach(u => {
            if(u.key === uUID) return;
            const d = u.val();
            const node = document.createElement('div');
            node.className = 'u-card';
            node.innerHTML = `
                <img src="https://ui-avatars.com/api/?name=${d.name}&background=random&color=fff" style="width:30px; border-radius:10px;">
                <span style="font-size:0.8rem;">${d.name}</span>
                ${isAdmin ? `<span class="ban-hammer" id="bh-${u.key}" style="display:block;">BAN</span>` : ''}
            `;
            node.onclick = (e) => {
                if(e.target.className === 'ban-hammer') {
                    if(confirm("Ban " + d.name + "?")) set(ref(db, 'banned/' + u.key), true);
                } else { openPriv(u.key, d.name); }
            };
            l.appendChild(node);
        });
    });
}

window.toggleMute = () => {
    onValue(ref(db, 'system/mute'), s => {
        set(ref(db, 'system/mute'), !s.val());
    }, { onlyOnce: true });
};

window.wipeData = () => { if(confirm("Wipe Global?")) remove(ref(db, 'global_v2')); };
window.logout = () => { localStorage.clear(); signOut(auth).then(() => location.reload()); };

// Sidebar toggle logic
document.getElementById("menuToggle").addEventListener("click", () => {
    document.querySelector(".sidebar").classList.toggle("show");
});
</script>
</body>
</html>
