<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Web-Hub Pro | Nazim Edition</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --primary:#7c3aed; --secondary:#db2777; --bg:#030712; --glass:rgba(30,41,59,0.7); --accent:#fbbf24; }
*{box-sizing:border-box; font-family:'Plus Jakarta Sans',sans-serif; transition:0.3s; }
body{margin:0; background:var(--bg); color:#f9fafb; height:100dvh; display:flex; flex-direction:column; overflow:hidden;}

/* Login Layer */
#loginLayer{position:fixed; inset:0; background:var(--bg); display:flex; align-items:center; justify-content:center; z-index:2000;}
.auth-card{background:var(--glass); padding:40px; border-radius:30px; border:1px solid rgba(255,255,255,0.1); text-align:center; width:90%; max-width:380px; backdrop-filter:blur(20px);}
.auth-card h1{background:linear-gradient(to right,var(--primary),var(--secondary)); -webkit-background-clip:text; -webkit-text-fill-color:transparent; font-size:2.5rem; margin-bottom:10px; font-weight:900;}
.auth-card input{width:100%; padding:15px; margin-bottom:15px; border-radius:15px; border:none; background:#000; color:#fff; text-align:center; outline:none; font-size:1rem;}
.auth-card button{width:100%; padding:15px; border-radius:15px; border:none; background:var(--primary); color:white; font-weight:800; cursor:pointer; font-size:1rem; box-shadow:0 10px 20px rgba(124,58,237,0.3);}

/* Layout */
header{padding:18px 25px; background:rgba(3,7,18,0.9); display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid rgba(255,255,255,0.05); z-index:100;}
.brand{font-weight:900; font-size:1.4rem; letter-spacing:-1px;}
.admin-badge{background:var(--accent); color:#000; padding:2px 8px; border-radius:8px; font-size:10px; font-weight:900; margin-left:10px;}

.page{display:none; flex:1; overflow-y:auto; padding:20px; padding-bottom:100px;}
.page.active{display:flex; flex-direction:column; animation:fadeIn 0.4s ease;}
@keyframes fadeIn{from{opacity:0;} to{opacity:1;}}

/* Chat UI */
.feed{flex:1; display:flex; flex-direction:column; gap:12px;}
.msg{max-width:80%; padding:12px 16px; border-radius:20px; font-size:14px; line-height:1.5; position:relative;}
.msg.me{align-self:flex-end; background:var(--primary); color:white; border-bottom-right-radius:4px;}
.msg.other{align-self:flex-start; background:var(--glass); border-bottom-left-radius:4px; border:1px solid rgba(255,255,255,0.05);}
.msg img{max-width:100%; border-radius:12px; margin-top:8px; display:block;}

.dock{position:fixed; bottom:75px; left:15px; right:15px; background:rgba(30,41,59,0.9); backdrop-filter:blur(20px); padding:10px; border-radius:50px; display:flex; align-items:center; gap:10px; border:1px solid rgba(255,255,255,0.1);}
.dock input{flex:1; background:none; border:none; color:white; outline:none; padding-left:10px; font-size:15px;}
.dock button{background:var(--primary); color:white; border:none; width:42px; height:42px; border-radius:50%; cursor:pointer;}

.bottom-nav{position:fixed; bottom:0; left:0; right:0; height:65px; background:rgba(0,0,0,0.9); display:flex; justify-content:space-around; align-items:center; border-top:1px solid rgba(255,255,255,0.05);}
.nav-btn{color:#4b5563; font-size:1.4rem; cursor:pointer;}
.nav-btn.active{color:var(--primary); filter:drop-shadow(0 0 8px var(--primary));}

/* Extras */
.welcome-card{background:linear-gradient(135deg,var(--primary),var(--secondary)); padding:30px; border-radius:25px; text-align:center; margin-bottom:20px;}
.quote-box{background:rgba(255,255,255,0.05); padding:15px; border-radius:15px; font-style:italic; text-align:center; font-size:0.9rem; color:#94a3b8;}
.user-card{background:rgba(255,255,255,0.03); padding:15px; border-radius:15px; margin-bottom:10px; display:flex; align-items:center; gap:15px; cursor:pointer;}
.user-card:hover{background:rgba(255,255,255,0.08);}
.typing-tag{color:var(--accent); font-size:12px; margin:5px 0 0 15px; font-weight:600;}
</style>
</head>
<body>

<div id="loginLayer">
    <div class="auth-card">
        <h1>WEB-HUB</h1>
        <p style="opacity:0.5; font-size:0.8rem; margin-bottom:20px;">Secure Real-time Portal</p>
        <input type="text" id="nodeName" placeholder="Enter Username">
        <button onclick="boot()">INITIALIZE</button>
    </div>
</div>

<header>
    <div style="display:flex; align-items:center;">
        <span class="brand">WEB-HUB</span>
        <span id="admTag" class="admin-badge" style="display:none;">ADMIN</span>
    </div>
    <div id="statusDot" style="width:10px; height:10px; background:#10b981; border-radius:50%;"></div>
</header>

<div class="page active" id="pg-home">
    <div class="welcome-card">
        <h2 id="greet" style="margin:0;">Welcome!</h2>
        <small id="userRole">Standard Asset</small>
    </div>
    <div class="quote-box" id="quoteFeed">"Design is not just what it looks like, it's how it works."</div>
    <div style="margin-top:20px; text-align:center; font-size:0.8rem; color:var(--accent);" id="clock"></div>
</div>

<div class="page" id="pg-chat">
    <div id="chatHeader" style="padding-bottom:10px; border-bottom:1px solid rgba(255,255,255,0.05); margin-bottom:15px; font-weight:800; font-size:0.9rem; color:var(--primary);">GLOBAL_CHANNEL</div>
    <div class="feed" id="chatFeed"></div>
    <div id="typeInfo" class="typing-tag"></div>
    <div class="dock">
        <label for="imgUp"><i class="fa-solid fa-paperclip"></i></label>
        <input type="file" id="imgUp" hidden onchange="upMedia(this)">
        <input type="text" id="msgIn" placeholder="Type a message..." oninput="pushType()" onkeydown="if(event.key==='Enter') sendMsg()">
        <button onclick="sendMsg()"><i class="fa-solid fa-location-arrow"></i></button>
    </div>
</div>

<div class="page" id="pg-users">
    <h3 style="margin-top:0;">Active Nodes</h3>
    <div id="nodesList"></div>
    <div style="margin-top:20px; padding:15px; background:var(--glass); border-radius:15px;">
        <small style="display:block; margin-bottom:10px; opacity:0.6;">CREATE GROUP</small>
        <div style="display:flex; gap:10px;">
            <input type="text" id="gName" placeholder="Group Name" style="flex:1; padding:10px; border-radius:10px; border:none; background:#000; color:#fff;">
            <button onclick="makeGroup()" style="background:var(--primary); border:none; color:#fff; padding:0 15px; border-radius:10px; cursor:pointer;">+</button>
        </div>
    </div>
</div>

<nav class="bottom-nav">
    <i class="fa-solid fa-house nav-btn active" onclick="nav('pg-home',this)"></i>
    <i class="fa-solid fa-comment-dots nav-btn" onclick="nav('pg-chat',this)"></i>
    <i class="fa-solid fa-user-group nav-btn" onclick="nav('pg-users',this)"></i>
    <i class="fa-solid fa-power-off nav-btn" onclick="exit()"></i>
</nav>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp, query, limitToLast, update, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

const firebaseConfig = {
  apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain:"live-chat-b810c.firebaseapp.com",
  databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId:"live-chat-b810c",
  storageBucket:"live-chat-b810c.firebasestorage.app",
  messagingSenderId:"555058795334",
  appId:"1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getDatabase(app);

let uUID, uName, isAdmin=false;
let currentRoom = "global_v2";

const quotes = [
    "Empire is built on code.", "Work hard in silence.", "Stay focused, stay humble.",
    "Nazim's System is Online.", "The best way to predict future is to create it."
];

// Navigation
window.nav = (id, el) => {
    document.querySelectorAll(".page").forEach(p => p.classList.remove("active"));
    document.getElementById(id).classList.add("active");
    document.querySelectorAll(".nav-btn").forEach(i => i.classList.remove("active"));
    el.classList.add("active");
    if(id === 'pg-chat') syncMsgs();
};

window.boot = async () => {
    const n = document.getElementById("nodeName").value.trim();
    if(!n) return;
    if(n.toLowerCase() === 'nazim'){
        if(prompt("Enter Admin PIN:") === '786') isAdmin=true;
        else return;
    }
    const res = await signInAnonymously(auth);
    uUID = res.user.uid; uName = n;
    localStorage.setItem("uN", n); localStorage.setItem("uA", isAdmin);
    setupApp();
};

function setupApp(){
    document.getElementById("loginLayer").style.display = "none";
    document.getElementById("greet").innerText = "Welcome, " + uName;
    if(isAdmin) {
        document.getElementById("admTag").style.display = "inline";
        document.getElementById("userRole").innerText = "Supreme System Admin";
    }
    set(ref(db, 'nodes/'+uUID), {name: uName, online: true, typing: false});
    syncUsers();
    syncMsgs();
    trackTyping();
    setInterval(() => {
        document.getElementById("clock").innerText = new Date().toLocaleString();
        document.getElementById("quoteFeed").innerText = quotes[Math.floor(Math.random()*quotes.length)];
    }, 10000);
}

window.sendMsg = (img = null) => {
    const i = document.getElementById("msgIn");
    if(!i.value.trim() && !img) return;
    push(ref(db, currentRoom), {
        uid: uUID, sender: uName, text: i.value, img: img, ts: serverTimestamp()
    });
    i.value = "";
    update(ref(db, 'nodes/'+uUID), {typing: false});
};

window.upMedia = (el) => {
    const r = new FileReader();
    r.onload = e => sendMsg(e.target.result);
    r.readAsDataURL(el.files[0]);
};

function syncMsgs() {
    onValue(query(ref(db, currentRoom), limitToLast(40)), s => {
        const feed = document.getElementById("chatFeed");
        feed.innerHTML = "";
        s.forEach(m => {
            const d = m.val();
            feed.innerHTML += `
                <div class="msg ${d.uid === uUID ? 'me' : 'other'}">
                    <small style="opacity:0.5; font-weight:800; font-size:10px; display:block; margin-bottom:4px;">${d.sender}</small>
                    ${d.text ? `<div>${d.text}</div>` : ''}
                    ${d.img ? `<img src="${d.img}">` : ''}
                </div>`;
        });
        feed.scrollTop = feed.scrollHeight;
    });
}

window.pushType = () => {
    update(ref(db, 'nodes/'+uUID), {typing: true});
    setTimeout(() => update(ref(db, 'nodes/'+uUID), {typing: false}), 2000);
};

function trackTyping() {
    onValue(ref(db, 'nodes'), s => {
        let typs = [];
        s.forEach(u => {
            if(u.val().typing && u.key !== uUID) typs.push(u.val().name);
        });
        document.getElementById("typeInfo").innerText = typs.length ? typs.join(", ") + " is typing..." : "";
    });
}

function syncUsers() {
    onValue(ref(db, 'nodes'), s => {
        const l = document.getElementById("nodesList");
        l.innerHTML = "";
        s.forEach(u => {
            const d = u.val();
            if(!d.online) return;
            const card = document.createElement('div');
            card.className = 'user-card';
            card.innerHTML = `<img src="https://ui-avatars.com/api/?name=${d.name}&background=random&color=fff" style="width:35px; border-radius:10px;"> <span>${d.name}</span>`;
            card.onclick = () => {
                if(u.key === uUID) { currentRoom = "global_v2"; document.getElementById("chatHeader").innerText = "GLOBAL_CHANNEL"; }
                else {
                    currentRoom = uUID < u.key ? `${uUID}_${u.key}` : `${u.key}_${uUID}`;
                    document.getElementById("chatHeader").innerText = "PRIVATE: " + d.name;
                }
                nav('pg-chat', document.querySelectorAll(".nav-btn")[1]);
            };
            l.appendChild(card);
        });
    });
}

window.makeGroup = () => {
    const g = document.getElementById("gName").value.trim();
    if(!g) return;
    currentRoom = "group_" + g;
    document.getElementById("chatHeader").innerText = "GROUP: " + g;
    nav('pg-chat', document.querySelectorAll(".nav-btn")[1]);
    document.getElementById("gName").value = "";
};

window.exit = () => { localStorage.clear(); signOut(auth).then(()=>location.reload()); };

onAuthStateChanged(auth, user => {
    if(user){
        const sn = localStorage.getItem("uN");
        if(sn){ uUID=user.uid; uName=sn; isAdmin=localStorage.getItem("uA")==="true"; setupApp(); }
    }
});
</script>
</body>
</html>
