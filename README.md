<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Web-Hub Sovereign | Nazim God Mode</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#8b5cf6; --s:#ec4899; --bg:#020617; --g:rgba(30,41,59,0.7); --danger:#ef4444; }
*{box-sizing:border-box; font-family:'Plus Jakarta Sans',sans-serif; transition:0.3s; }
body{margin:0; background:var(--bg); color:#f9fafb; height:100dvh; display:flex; flex-direction:column; overflow:hidden;}

/* Admin Special UI */
#adminTerminal{display:none; background:rgba(239,68,68,0.15); border:1px solid var(--danger); margin:10px; padding:10px; border-radius:15px; display:none; justify-content:space-around; align-items:center; z-index:100;}
.adm-btn{background:var(--danger); color:white; border:none; padding:6px 12px; border-radius:8px; font-size:11px; font-weight:800; cursor:pointer; text-transform:uppercase;}

/* Standard UI */
#loginLayer{position:fixed; inset:0; background:#000; display:flex; align-items:center; justify-content:center; z-index:2000;}
.auth-card{background:var(--g); padding:40px; border-radius:30px; border:1px solid rgba(255,255,255,0.1); text-align:center; width:90%; max-width:380px; backdrop-filter:blur(20px);}
.auth-card input{width:100%; padding:15px; margin-bottom:15px; border-radius:15px; border:none; background:#000; color:#fff; text-align:center; outline:none;}
.auth-card button{width:100%; padding:15px; border-radius:15px; border:none; background:var(--p); color:white; font-weight:800; cursor:pointer;}

header{padding:18px 25px; background:rgba(2,6,23,0.9); display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid rgba(255,255,255,0.05);}
.brand{font-weight:900; font-size:1.4rem; background:linear-gradient(to right, #a78bfa, #f472b6); -webkit-background-clip:text; -webkit-text-fill-color:transparent;}

.page{display:none; flex:1; overflow-y:auto; padding:20px; padding-bottom:120px;}
.page.active{display:flex; flex-direction:column;}

.feed{flex:1; display:flex; flex-direction:column; gap:12px;}
.msg{max-width:80%; padding:12px 16px; border-radius:20px; font-size:14px; position:relative;}
.me{align-self:flex-end; background:var(--p); color:white; border-bottom-right-radius:4px;}
.other{align-self:flex-start; background:var(--g); border-bottom-left-radius:4px;}
.msg img{max-width:100%; border-radius:12px; margin-top:8px;}

.dock{position:fixed; bottom:75px; left:15px; right:15px; background:rgba(30,41,59,0.95); padding:10px; border-radius:50px; display:flex; align-items:center; gap:10px; border:1px solid rgba(255,255,255,0.1); backdrop-filter:blur(20px);}
.dock input{flex:1; background:none; border:none; color:white; outline:none; padding-left:10px;}
.mute-overlay{position:absolute; inset:0; background:rgba(0,0,0,0.85); color:var(--danger); display:none; align-items:center; justify-content:center; border-radius:50px; font-weight:900; font-size:11px;}

.bottom-nav{position:fixed; bottom:0; left:0; right:0; height:65px; background:#000; display:flex; justify-content:space-around; align-items:center; border-top:1px solid rgba(255,255,255,0.05);}
.nav-btn{color:#4b5563; font-size:1.4rem; cursor:pointer;}
.nav-btn.active{color:var(--p); filter:drop-shadow(0 0 8px var(--p));}

.u-card{background:rgba(255,255,255,0.03); padding:15px; border-radius:15px; margin-bottom:10px; display:flex; align-items:center; gap:12px;}
.ban-hammer{background:none; border:1px solid var(--danger); color:var(--danger); padding:4px 8px; border-radius:6px; font-size:10px; font-weight:800; margin-left:auto; display:none; cursor:pointer;}
</style>
</head>
<body>

<div id="loginLayer">
    <div class="auth-card">
        <h1 class="brand">WEB-HUB</h1>
        <input type="text" id="nName" placeholder="Enter Username">
        <button onclick="boot()">INITIALIZE</button>
    </div>
</div>

<div id="adminTerminal">
    <span style="font-size:10px; font-weight:900; color:var(--danger);">GOD_MODE</span>
    <button class="adm-btn" onclick="admToggleMute()">Toggle Mute</button>
    <button class="adm-btn" onclick="admWipe()">Wipe History</button>
</div>

<header>
    <div class="brand">WEB-HUB PRO</div>
    <div id="admBadge" style="display:none; color:#fbbf24;"><i class="fa-solid fa-crown"></i></div>
</header>

<div class="page active" id="p-home">
    <div style="background:linear-gradient(135deg,var(--p),var(--s)); padding:30px; border-radius:25px; text-align:center;">
        <h2 id="welcomeText" style="margin:0;">Connecting...</h2>
        <small id="roleText">Initializing Assets</small>
    </div>
    <p id="quoteBox" style="text-align:center; font-style:italic; opacity:0.6; margin-top:20px;"></p>
</div>

<div class="page" id="p-chat">
    <div class="feed" id="chatFeed"></div>
    <div class="dock">
        <label for="imgUp"><i class="fa-solid fa-camera"></i></label>
        <input type="file" id="imgUp" hidden onchange="up(this)">
        <input type="text" id="mIn" placeholder="Type signal..." onkeydown="if(event.key==='Enter') send()">
        <button onclick="send()" style="background:var(--p); border:none; color:white; width:40px; height:40px; border-radius:50%;"><i class="fa-solid fa-paper-plane"></i></button>
        <div class="mute-overlay" id="muteScreen">🔇 GLOBAL CHAT IS LOCKED BY ADMIN</div>
    </div>
</div>

<div class="page" id="p-users">
    <h3>Online Assets</h3>
    <div id="uList"></div>
</div>

<nav class="bottom-nav">
    <i class="fa-solid fa-house nav-btn active" onclick="nav('p-home',this)"></i>
    <i class="fa-solid fa-comment-dots nav-btn" onclick="nav('p-chat',this)"></i>
    <i class="fa-solid fa-user-shield nav-btn" onclick="nav('p-users',this)"></i>
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

let uUID, uName, isAdmin=false, curRoom="global_v2";

window.boot = async () => {
    const n = document.getElementById("nName").value.trim();
    if(!n) return;
    if(n.toLowerCase()==='nazim'){
        if(prompt("Admin PIN:")==='786') isAdmin=true;
        else return;
    }
    const res = await signInAnonymously(auth);
    uUID = res.user.uid; uName = n;
    localStorage.setItem("uN",n); localStorage.setItem("uA",isAdmin);
    checkBan(uUID);
};

function checkBan(uid) {
    onValue(ref(db, 'banned/'+uid), s => {
        if(s.val()) { document.body.innerHTML = "<h1 style='color:red; text-align:center; padding-top:100px;'>🚫 YOU ARE BANNED</h1>"; localStorage.clear(); }
        else startApp();
    });
}

function startApp(){
    document.getElementById("loginLayer").style.display="none";
    document.getElementById("welcomeText").innerText = "Welcome, "+uName;
    if(isAdmin) {
        document.getElementById("adminTerminal").style.display="flex";
        document.getElementById("admBadge").style.display="block";
        document.getElementById("roleText").innerText = "Supreme Sovereign Administrator";
    }
    set(ref(db, 'nodes/'+uUID), {name:uName, online:true});
    syncMsgs(); syncUsers();
    onValue(ref(db, 'system/mute'), s => { document.getElementById("muteScreen").style.display = (s.val() && !isAdmin) ? "flex" : "none"; });
}

window.send = (img=null) => {
    const i = document.getElementById("mIn");
    if(!i.value.trim() && !img) return;
    push(ref(db, curRoom), { uid:uUID, sender:uName, text:i.value, img:img, ts:serverTimestamp() });
    i.value="";
};

window.up = (el) => {
    const r = new FileReader();
    r.onload = e => send(e.target.result);
    r.readAsDataURL(el.files[0]);
};

window.nav = (id, el) => {
    document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));
    document.getElementById(id).classList.add("active");
    document.querySelectorAll(".nav-btn").forEach(b=>b.classList.remove("active"));
    el.classList.add("active");
};

function syncMsgs(){
    onValue(query(ref(db, curRoom), limitToLast(40)), s => {
        const f = document.getElementById("chatFeed"); f.innerHTML="";
        s.forEach(m => {
            const d = m.val();
            f.innerHTML += `<div class="msg ${d.uid===uUID?'me':'other'}"><small style="opacity:0.5; font-size:10px; display:block;">${d.sender}</small>${d.text?`<div>${d.text}</div>`:''}${d.img?`<img src="${d.img}">`:''}</div>`;
        });
        f.scrollTop = f.scrollHeight;
    });
}

function syncUsers(){
    onValue(ref(db, 'nodes'), s => {
        const l = document.getElementById("uList"); l.innerHTML="";
        s.forEach(u => {
            const d = u.val(); if(!d.online) return;
            const card = document.createElement('div'); card.className='u-card';
            card.innerHTML = `<img src="https://ui-avatars.com/api/?name=${d.name}&background=random&color=fff" style="width:35px; border-radius:10px;"> <span>${d.name}</span> <button class="ban-hammer" style="display:${isAdmin?'block':'none'}" onclick="ban('${u.key}')">BAN</button>`;
            card.onclick = (e) => { if(e.target.tagName!=='BUTTON') { openPriv(u.key, d.name); } };
            l.appendChild(card);
        });
    });
}

window.openPriv = (tid, tn) => {
    if(tid===uUID) return;
    curRoom = uUID < tid ? `${uUID}_${tid}` : `${tid}_${uUID}`;
    nav('p-chat', document.querySelectorAll(".nav-btn")[1]);
    syncMsgs();
};

window.ban = (uid) => { if(isAdmin && confirm("Ban User?")) set(ref(db, 'banned/'+uid), true); };
window.admToggleMute = () => { onValue(ref(db, 'system/mute'), s=>{ set(ref(db, 'system/mute'), !s.val()); }, {onlyOnce:true}); };
window.admWipe = () => { if(isAdmin && confirm("Wipe History?")) remove(ref(db, 'global_v2')); };
window.exit = () => { localStorage.clear(); signOut(auth).then(()=>location.reload()); };

onAuthStateChanged(auth, user => {
    if(user && localStorage.getItem("uN")){ uUID=user.uid; uName=localStorage.getItem("uN"); isAdmin=localStorage.getItem("uA")==='true'; checkBan(uUID); }
});
</script>
</body>
</html>
