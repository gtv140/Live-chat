<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>WEB-HUB | WhatsApp Clone Fix</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root { --wa-green: #075e54; --wa-light: #128c7e; --wa-bg: #ece5dd; --wa-chat-bg: #e5ddd5; }
* { box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; margin: 0; padding: 0; }

/* 🔒 PURE MOBILE LOCK */
html, body { 
    height: 100%; width: 100%; overflow: hidden; 
    position: fixed; background: #fff; display: flex; flex-direction: column;
}

/* 🟢 WHATSAPP HEADER */
header { 
    background: var(--wa-green); color: white; padding: 15px 20px; 
    display: flex; justify-content: space-between; align-items: center; flex-shrink: 0;
}
.brand { font-size: 20px; font-weight: 700; }

/* 🛠️ ADMIN BAR (Sits below header) */
#admBar { 
    display: none; background: #ffebee; padding: 8px 15px; 
    border-bottom: 1px solid #ffcdd2; justify-content: space-between; align-items: center;
}

/* 📜 SCROLLABLE CONTENT (MIRRORING WHATSAPP) */
.main-viewport { 
    flex: 1; overflow-y: auto; background: var(--wa-bg);
    display: flex; flex-direction: column; -webkit-overflow-scrolling: touch;
}

.page { display: none; flex-direction: column; height: 100%; width: 100%; }
.page.active { display: flex; }

/* 💬 CHAT BUBBLES (EXACT WHATSAPP STYLE) */
#chatFeed { padding: 10px; display: flex; flex-direction: column; gap: 5px; }
.msg { 
    max-width: 85%; padding: 8px 12px; border-radius: 8px; font-size: 15px; 
    position: relative; box-shadow: 0 1px 1px rgba(0,0,0,0.1); line-height: 1.4;
}
.msg.me { align-self: flex-end; background: #dcf8c6; color: #303030; }
.msg.other { align-self: flex-start; background: #ffffff; color: #303030; }
.msg-info { font-size: 10px; opacity: 0.5; font-weight: bold; margin-bottom: 2px; }

/* ⌨️ STICKY INPUT (FOOTER) */
.input-area { 
    background: #f0f0f0; padding: 10px; display: none; 
    align-items: center; gap: 8px; flex-shrink: 0;
}
.input-box { 
    flex: 1; background: white; border-radius: 25px; 
    padding: 5px 15px; display: flex; align-items: center;
}
.input-box input { flex: 1; border: none; height: 35px; outline: none; font-size: 16px; }

/* 📱 BOTTOM NAVIGATION */
nav { 
    height: 60px; background: #f7f7f7; border-top: 1px solid #ddd;
    display: flex; justify-content: space-around; align-items: center; flex-shrink: 0;
    padding-bottom: env(safe-area-inset-bottom);
}
.tab { color: #54656f; font-size: 11px; text-align: center; cursor: pointer; flex: 1; }
.tab i { font-size: 20px; display: block; margin-bottom: 2px; }
.tab.active { color: var(--wa-light); }

/* AUTH OVERLAY */
#auth { position: fixed; inset: 0; background: white; z-index: 1000; display: flex; align-items: center; justify-content: center; }
.auth-card { width: 85%; text-align: center; }
.auth-card input { width: 100%; padding: 15px; border: 1px solid #ddd; border-radius: 8px; margin-bottom: 10px; font-size: 16px; text-align: center; }
.auth-card button { width: 100%; padding: 15px; background: var(--wa-light); color: white; border: none; border-radius: 8px; font-weight: bold; }
</style>
</head>
<body>

<div id="auth">
    <div class="auth-card">
        <h1 style="color: var(--wa-light); font-size: 32px; margin-bottom: 20px;">Web-Hub</h1>
        <input type="text" id="uName" placeholder="Enter Codename">
        <button onclick="login()">Agree & Continue</button>
    </div>
</div>

<header>
    <div class="brand">WhatsApp Pro</div>
    <div style="font-size: 18px;"><i class="fa-solid fa-camera"></i> <i class="fa-solid fa-ellipsis-vertical" style="margin-left:15px;"></i></div>
</header>

<div id="admBar">
    <span style="color:#d32f2f; font-weight:bold; font-size:12px;">ADMIN CONTROL</span>
    <button onclick="admMute()" style="background:#d32f2f; color:white; border:none; padding:4px 10px; border-radius:4px;">MUTE</button>
</div>

<div class="main-viewport">
    <div class="page active" id="home-pg">
        <div style="padding: 20px; text-align: center;">
            <div style="background: white; padding: 30px; border-radius: 15px; box-shadow: 0 2px 5px rgba(0,0,0,0.05);">
                <h3 id="greetUser">Welcome</h3>
                <p style="color:#667781; font-size: 14px; margin-top: 5px;">Your chats are end-to-end encrypted.</p>
            </div>
        </div>
    </div>
    <div class="page" id="chat-pg"><div id="chatFeed"></div></div>
    <div class="page" id="users-pg"><div id="uList" style="padding: 10px;"></div></div>
</div>

<div class="input-area" id="inputContainer">
    <div class="input-box"><input type="text" id="mIn" placeholder="Message" onkeydown="if(event.key==='Enter') send()"></div>
    <button onclick="send()" style="background:var(--wa-light); color:white; border:none; width:45px; height:45px; border-radius:50%; font-size:18px;"><i class="fa-solid fa-paper-plane"></i></button>
</div>

<nav>
    <div class="tab active" onclick="go('home-pg',this)"><i class="fa-solid fa-house"></i>Home</div>
    <div class="tab" onclick="go('chat-pg',this)"><i class="fa-solid fa-message"></i>Chats</div>
    <div class="tab" onclick="go('users-pg',this)"><i class="fa-solid fa-users"></i>People</div>
    <div class="tab" onclick="exitApp()"><i class="fa-solid fa-power-off"></i>Exit</div>
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

let myID, myName, isBoss=false, currentRoom="global_v8";

window.login = async () => {
    const val = document.getElementById("uName").value.trim();
    if(!val) return;
    if(val.toLowerCase()==='nazim'){
        if(prompt("Admin Key:")==='786') isBoss=true; else return;
    }
    const res = await signInAnonymously(auth);
    myID = res.user.uid; myName = val;
    localStorage.setItem("un", val); localStorage.setItem("ib", isBoss);
    start();
};

function start(){
    document.getElementById("auth").style.display="none";
    document.getElementById("greetUser").innerText = "Hi, " + myName;
    if(isBoss) document.getElementById("admBar").style.display="flex";

    const statusRef = ref(db, 'nodes/'+myID);
    onValue(ref(db, '.info/connected'), (s) => {
        if(s.val()){ onDisconnect(statusRef).remove(); set(statusRef, {name:myName, online:true}); }
    });

    syncChat(); syncUsers();
    onValue(ref(db, 'system/mute'), s => { 
        document.getElementById("mIn").disabled = (s.val() && !isBoss);
        document.getElementById("mIn").placeholder = (s.val() && !isBoss) ? "Muted by Admin" : "Message";
    });
}

window.send = () => {
    const i = document.getElementById("mIn");
    if(!i.value.trim()) return;
    push(ref(db, currentRoom), { uid:myID, name:myName, text:i.value, ts:serverTimestamp() });
    i.value="";
};

window.go = (id, el) => {
    document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));
    document.getElementById(id).classList.add("active");
    document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));
    el.classList.add("active");
    
    document.getElementById("inputContainer").style.display = (id === 'chat-pg') ? 'flex' : 'none';
    if(id==='chat-pg') scrollDown();
};

function scrollDown(){ const v = document.querySelector(".main-viewport"); v.scrollTop = v.scrollHeight; }

function syncChat(){
    onValue(query(ref(db, currentRoom), limitToLast(50)), s => {
        const feed = document.getElementById("chatFeed"); feed.innerHTML="";
        s.forEach(m => {
            const d = m.val();
            feed.innerHTML += `<div class="msg ${d.uid===myID?'me':'other'}"><div class="msg-info">${d.name}</div>${d.text}</div>`;
        });
        scrollDown();
    });
}

function syncUsers(){
    onValue(ref(db, 'nodes'), s => {
        const l = document.getElementById("uList"); l.innerHTML="";
        s.forEach(u => {
            if(u.key===myID) return;
            const d = u.val();
            l.innerHTML += `<div style="background:white; padding:15px; border-radius:10px; margin-bottom:8px; display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid #eee;">
                <span style="font-weight:600;">${d.name}</span>
                <button onclick="currentRoom='${myID<u.key?myID+'_'+u.key:u.key+'_'+myID}'; go('chat-pg', document.querySelectorAll('.tab')[1])" style="background:var(--wa-light); color:white; border:none; padding:5px 12px; border-radius:5px;">Chat</button>
            </div>`;
        });
    });
}

window.admMute = () => { onValue(ref(db, 'system/mute'), s=>{ set(ref(db, 'system/mute'), !s.val()); }, {onlyOnce:true}); };
window.exitApp = () => { localStorage.clear(); signOut(auth).then(()=>location.reload()); };

onAuthStateChanged(auth, user => {
    if(user && localStorage.getItem("un")){ 
        myID=user.uid; myName=localStorage.getItem("un"); isBoss=localStorage.getItem("ib")==='true'; 
        start();
    }
});
</script>
</body>
</html>
