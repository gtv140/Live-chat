<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>WEB-HUB | Sovereign v3</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{ --p:#8b5cf6; --s:#ec4899; --bg:#020617; --card:#1e293b; --danger:#ef4444; --me-grad:linear-gradient(135deg, #8b5cf6, #6d28d9); }
*{box-sizing:border-box; font-family:'Plus Jakarta Sans',sans-serif; -webkit-tap-highlight-color: transparent;}

body { 
    margin: 0; background: var(--bg); color: #f9fafb; 
    height: 100dvh; display: flex; flex-direction: column; overflow: hidden; 
    position: fixed; width: 100%;
}

/* Admin Bar */
#admBar{ display:none; background:rgba(239,68,68,0.2); border-bottom:1px solid var(--danger); padding:8px; justify-content:space-around; align-items:center; z-index:100; font-size:11px;}
.adm-btn{ background:var(--danger); color:white; border:none; padding:5px 10px; border-radius:8px; font-weight:800; cursor:pointer; }

/* Auth Layer */
#auth{ position:fixed; inset:0; background:#000; display:flex; align-items:center; justify-content:center; z-index:2000; }
.auth-card{ background:rgba(30,41,59,0.7); padding:40px; border-radius:35px; border:1px solid rgba(255,255,255,0.1); text-align:center; width:90%; max-width:380px; backdrop-filter:blur(20px); }
.auth-card input{ width:100%; padding:15px; margin-bottom:15px; border-radius:18px; border:none; background:#000; color:#fff; text-align:center; outline:none; font-size:16px;}
.auth-card button{ width:100%; padding:15px; border-radius:18px; border:none; background:var(--p); color:white; font-weight:800; cursor:pointer; }

header{ padding:15px 20px; background:rgba(2,6,23,0.9); display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid rgba(255,255,255,0.05); }
.brand{ font-weight:900; font-size:1.3rem; background:linear-gradient(to right, #a78bfa, #f472b6); -webkit-background-clip:text; -webkit-text-fill-color:transparent; }

.page{ display:none; flex:1; overflow:hidden; position:relative; }
.page.active{ display:flex; flex-direction:column; }

/* 🚀 BOXED CHAT SYSTEM */
.feed{ 
    flex:1; overflow-y:auto; padding:15px; display:flex; flex-direction:column; gap:15px; 
    scroll-behavior: smooth; padding-bottom: 100px;
}

.msg-box { display: flex; flex-direction: column; max-width: 80%; }
.msg-box.me { align-self: flex-end; align-items: flex-end; }
.msg-box.other { align-self: flex-start; align-items: flex-start; }

.bubble { 
    padding: 12px 16px; border-radius: 22px; font-size: 14px; line-height: 1.4;
    box-shadow: 0 4px 12px rgba(0,0,0,0.1); word-wrap: break-word; position: relative;
}
.me .bubble { background: var(--me-grad); color: white; border-bottom-right-radius: 4px; }
.other .bubble { background: var(--card); color: #e2e8f0; border-bottom-left-radius: 4px; }

.sender-name { font-size: 10px; font-weight: 700; opacity: 0.6; margin-bottom: 4px; margin-left: 8px; margin-right: 8px; }
.bubble img { max-width: 100%; border-radius: 15px; margin-top: 8px; display: block; }

/* ⌨️ APP INPUT SYSTEM */
.dock-container { 
    padding: 10px 15px 25px 15px; background: transparent; 
    display: flex; align-items: center; gap: 10px; 
}
.input-box { 
    flex: 1; background: var(--card); border-radius: 25px; padding: 5px 15px;
    display: flex; align-items: center; border: 1px solid rgba(255,255,255,0.1);
}
.input-box input { flex:1; background:none; border:none; color:white; outline:none; height:45px; font-size:16px; }
.send-box { 
    width: 50px; height: 50px; background: var(--p); border-radius: 50%; 
    display: flex; align-items: center; justify-content: center; 
    box-shadow: 0 4px 15px rgba(139, 92, 246, 0.4); border: none; color: white; cursor: pointer;
}
.mute-lock { position: absolute; inset: 0; background: rgba(0,0,0,0.9); color: var(--danger); display: none; align-items: center; justify-content: center; border-radius: 30px; font-weight: 800; font-size: 12px; z-index: 20; }

/* Bottom Nav */
.nav-bar{ height:70px; background:#000; display:flex; justify-content:space-around; align-items:center; border-top:1px solid rgba(255,255,255,0.05); }
.n-btn{ color:#4b5563; font-size:1.3rem; cursor:pointer; display:flex; flex-direction:column; align-items:center; gap:4px;}
.n-btn.active{ color:var(--p); }
.n-btn span { font-size: 9px; font-weight: 600; text-transform: uppercase; }

.u-card{ background:var(--card); padding:12px 15px; border-radius:20px; margin-bottom:10px; display:flex; align-items:center; gap:12px; border:1px solid rgba(255,255,255,0.05); }
.ban-tag{ border:1px solid var(--danger); color:var(--danger); padding:3px 8px; border-radius:8px; font-size:10px; font-weight:800; margin-left:auto; }
</style>
</head>
<body>

<div id="auth">
    <div class="auth-card">
        <h1 class="brand" style="font-size:2rem;">WEB-HUB</h1>
        <input type="text" id="nodeUser" placeholder="Username">
        <button onclick="bootSystem()">ENTER SYSTEM</button>
    </div>
</div>

<div id="admBar">
    <span style="font-weight:900; color:var(--danger);">GOD MODE ENABLED</span>
    <button class="adm-btn" onclick="admMute()">MUTE</button>
    <button class="adm-btn" onclick="admClear()">WIPE</button>
</div>

<header>
    <div class="brand">WEB-HUB v3</div>
    <div id="king" style="display:none; color:#fbbf24;"><i class="fa-solid fa-crown"></i></div>
</header>

<div class="page active" id="pg-home">
    <div style="padding:30px; text-align:center;">
        <div style="background:var(--me-grad); padding:40px 20px; border-radius:30px; box-shadow:0 10px 30px rgba(0,0,0,0.3);">
            <h2 id="greet" style="margin:0;">Welcome!</h2>
            <p id="rank" style="opacity:0.8; font-size:12px; margin-top:10px;">Establishing secure connection...</p>
        </div>
        <div style="margin-top:30px; opacity:0.5; font-size:13px; font-style:italic;" id="qBox">"Code is power."</div>
    </div>
</div>

<div class="page" id="pg-chat">
    <div class="feed" id="chatFeed"></div>
    <div class="dock-container">
        <div class="input-box">
            <label for="fUp" style="padding-right:10px; color:var(--p);"><i class="fa-solid fa-circle-plus"></i></label>
            <input type="file" id="fUp" hidden onchange="hMedia(this)">
            <input type="text" id="msgIn" placeholder="Type message..." onkeydown="if(event.key==='Enter') dispatch()">
            <div class="mute-lock" id="lock">🔇 CHAT DISABLED BY NAZIM</div>
        </div>
        <button class="send-box" onclick="dispatch()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<div class="page" id="pg-users">
    <div style="padding:20px;">
        <h4 style="margin:0 0 15px 10px; opacity:0.5;">LIVE ASSETS</h4>
        <div id="assetList"></div>
    </div>
</div>

<nav class="nav-bar">
    <div class="n-btn active" onclick="nav('pg-home',this)"><i class="fa-solid fa-house-chimney"></i><span>Home</span></div>
    <div class="n-btn" onclick="nav('pg-chat',this)"><i class="fa-solid fa-message"></i><span>Chat</span></div>
    <div class="n-btn" onclick="nav('pg-users',this)"><i class="fa-solid fa-bolt"></i><span>Live</span></div>
    <div class="n-btn" onclick="terminate()"><i class="fa-solid fa-power-off"></i><span>Exit</span></div>
</nav>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp, query, limitToLast, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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

let myID, myName, isBoss=false, curRoom="global_v3";

const scroll = () => { const f = document.getElementById("chatFeed"); f.scrollTop = f.scrollHeight; };

window.bootSystem = async () => {
    const n = document.getElementById("nodeUser").value.trim();
    if(!n) return;
    if(n.toLowerCase()==='nazim'){
        if(prompt("Master Key:")==='786') isBoss=true;
        else return;
    }
    const res = await signInAnonymously(auth);
    myID = res.user.uid; myName = n;
    localStorage.setItem("uN",n); localStorage.setItem("uB",isBoss);
    initApp();
};

function initApp(){
    document.getElementById("auth").style.display="none";
    document.getElementById("greet").innerText = "Hello, " + myName;
    if(isBoss){
        document.getElementById("admBar").style.display="flex";
        document.getElementById("king").style.display="block";
        document.getElementById("rank").innerText = "SUPREME SYSTEM CONTROLLER";
    } else { document.getElementById("rank").innerText = "AUTHENTICATED ASSET"; }

    const statusRef = ref(db, 'nodes/' + myID);
    onValue(ref(db, '.info/connected'), (snap) => {
        if (snap.val() === true) {
            onDisconnect(statusRef).remove();
            set(statusRef, { name: myName, online: true });
        }
    });

    syncM(); syncU();
    onValue(ref(db, 'system/mute'), s => { document.getElementById("lock").style.display = (s.val() && !isBoss) ? "flex" : "none"; });
}

window.dispatch = (media=null) => {
    const i = document.getElementById("msgIn");
    if(!i.value.trim() && !media) return;
    push(ref(db, curRoom), { uid:myID, sender:myName, text:i.value, img:media, ts:serverTimestamp() });
    i.value="";
};

window.hMedia = (el) => {
    const r = new FileReader();
    r.onload = e => dispatch(e.target.result);
    r.readAsDataURL(el.files[0]);
};

window.nav = (id, el) => {
    document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));
    document.getElementById(id).classList.add("active");
    document.querySelectorAll(".n-btn").forEach(b=>b.classList.remove("active"));
    el.classList.add("active");
    if(id==='pg-chat') setTimeout(scroll, 200);
};

function syncM(){
    onValue(query(ref(db, curRoom), limitToLast(40)), s => {
        const f = document.getElementById("chatFeed"); f.innerHTML="";
        s.forEach(m => {
            const d = m.val();
            f.innerHTML += `
                <div class="msg-box ${d.uid===myID?'me':'other'}">
                    <span class="sender-name">${d.sender}</span>
                    <div class="bubble">
                        ${d.text?`<div>${d.text}</div>`:''}
                        ${d.img?`<img src="${d.img}">`:''}
                    </div>
                </div>`;
        });
        scroll();
    });
}

function syncU(){
    onValue(ref(db, 'nodes'), s => {
        const l = document.getElementById("assetList"); l.innerHTML="";
        s.forEach(u => {
            const d = u.val(); if(u.key===myID) return;
            const card = document.createElement('div'); card.className='u-card';
            card.innerHTML = `<img src="https://ui-avatars.com/api/?name=${d.name}&background=random&color=fff&rounded=true" style="width:40px;"> 
                              <span style="font-weight:700;">${d.name}</span>
                              <div style="width:8px; height:8px; background:#10b981; border-radius:50%;"></div>
                              ${isBoss?`<span class="ban-tag" onclick="ban('${u.key}')">BAN</span>`:''}`;
            card.onclick = (e) => { if(e.target.className!=='ban-tag') { openP(u.key, d.name); } };
            l.appendChild(card);
        });
    });
}

window.openP = (tid, tn) => {
    curRoom = myID < tid ? `${myID}_${tid}` : `${tid}_${myID}`;
    nav('pg-chat', document.querySelectorAll(".n-btn")[1]);
};

window.ban = (uid) => { if(isBoss && confirm("Ban Asset?")) set(ref(db, 'banned/'+uid), true); };
window.admMute = () => { onValue(ref(db, 'system/mute'), s=>{ set(ref(db, 'system/mute'), !s.val()); }, {onlyOnce:true}); };
window.admClear = () => { if(isBoss && confirm("Clear History?")) remove(ref(db, 'global_v3')); };
window.terminate = () => { localStorage.clear(); signOut(auth).then(()=>location.reload()); };

onAuthStateChanged(auth, user => {
    if(user && localStorage.getItem("uN")){ 
        myID=user.uid; myName=localStorage.getItem("uN"); isBoss=localStorage.getItem("uB")==='true'; 
        onValue(ref(db, 'banned/'+myID), s => {
            if(s.val()){ document.body.innerHTML="<h1 style='color:red; text-align:center; margin-top:100px;'>SYSTEM ACCESS REVOKED</h1>"; localStorage.clear(); }
            else initApp();
        });
    }
});
</script>
</body>
</html>
