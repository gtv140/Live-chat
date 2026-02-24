<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>WEB-HUB | Screen Fixed</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root { --p: #0084ff; --bg: #000; --card: #1c1c1e; --me: #0084ff; --other: #262626; }
* { box-sizing: border-box; font-family: -apple-system, sans-serif; touch-action: manipulation; }

/* 🔒 SCREEN LOCK LOGIC */
html, body { 
    margin: 0; padding: 0; width: 100%; height: 100%; 
    overflow: hidden; position: fixed; background: #000;
}

body { display: flex; flex-direction: column; }

/* 👑 ADMIN BAR */
#admBar { 
    display: none; background: rgba(255, 59, 48, 0.2); 
    padding: 8px 15px; border-bottom: 1px solid #ff3b30; 
    justify-content: space-between; align-items: center; z-index: 100;
}
.btn-red { background: #ff3b30; color: white; border: none; padding: 5px 12px; border-radius: 8px; font-weight: 800; font-size: 10px; }

/* 📱 HEADER */
header { 
    padding: 15px 20px; background: #000; border-bottom: 0.5px solid #222; 
    flex-shrink: 0; z-index: 10;
}
.brand { font-size: 20px; font-weight: 800; letter-spacing: -0.5px; }

/* 💬 SCROLLABLE AREA (ONLY CHAT SCROLLS) */
.main-content { 
    flex: 1; overflow: hidden; position: relative; display: flex; flex-direction: column;
}

.page { 
    position: absolute; inset: 0; display: none; flex-direction: column; background: #000;
}
.page.active { display: flex; }

/* 🚀 INTERNAL SCROLL ONLY */
.feed { 
    flex: 1; overflow-y: auto; padding: 15px; 
    display: flex; flex-direction: column; gap: 6px;
    -webkit-overflow-scrolling: touch; /* Smooth scroll for iOS */
}

/* MESSAGE BUBBLES */
.msg { max-width: 80%; padding: 10px 14px; border-radius: 18px; font-size: 15px; line-height: 1.3; }
.msg.me { align-self: flex-end; background: var(--me); color: white; border-bottom-right-radius: 4px; }
.msg.other { align-self: flex-start; background: var(--other); color: white; border-bottom-left-radius: 4px; }
.msg b { font-size: 10px; display: block; opacity: 0.6; margin-bottom: 2px; text-transform: uppercase; }

/* ⌨️ FIXED INPUT BOX */
.input-area { 
    padding: 10px 15px 15px 15px; background: #000;
    border-top: 0.5px solid #222; display: flex; align-items: center; gap: 10px;
    flex-shrink: 0;
}
.input-box { 
    flex: 1; background: #222; border-radius: 20px; 
    padding: 2px 15px; display: flex; align-items: center;
}
.input-box input { 
    flex: 1; background: none; border: none; color: white; 
    outline: none; font-size: 16px; height: 40px;
}
.send-btn { color: var(--p); font-size: 24px; border: none; background: none; padding: 0; }

/* BOTTOM NAV */
nav { 
    height: 65px; background: #000; border-top: 0.5px solid #222;
    display: flex; justify-content: space-around; align-items: center; flex-shrink: 0;
    padding-bottom: env(safe-area-inset-bottom);
}
.tab { color: #8e8e93; text-align: center; font-size: 10px; cursor: pointer; opacity: 0.8; }
.tab i { font-size: 22px; display: block; margin-bottom: 3px; }
.tab.active { color: var(--p); opacity: 1; }

/* AUTH */
#auth { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; }
.auth-box { width: 85%; text-align: center; }
.auth-box input { width: 100%; padding: 16px; background: #1c1c1e; border: 1px solid #333; border-radius: 15px; color: white; margin-bottom: 12px; font-size: 16px; text-align: center; outline: none; }
.auth-box button { width: 100%; padding: 16px; background: var(--p); border: none; border-radius: 15px; color: white; font-weight: bold; font-size: 16px; }
</style>
</head>
<body>

<div id="auth">
    <div class="auth-box">
        <h1 style="font-size: 42px; letter-spacing: -2px; margin-bottom: 10px;">Web-Hub</h1>
        <p style="color:#8e8e93; margin-bottom: 30px;">Enter your codename</p>
        <input type="text" id="uIn" placeholder="Username">
        <button onclick="login()">Enter System</button>
    </div>
</div>

<div id="admBar">
    <span style="font-weight: 900; color: #ff3b30;">SYSTEM OVERRIDE</span>
    <div>
        <button class="btn-red" onclick="admMute()">MUTE</button>
        <button class="btn-red" style="margin-left:5px;" onclick="admWipe()">WIPE</button>
    </div>
</div>

<header id="head">
    <div class="brand">Chats</div>
</header>

<div class="main-content">
    <div class="page active" id="home-pg">
        <div style="padding: 20px; text-align: center; margin-top: 20px;">
            <div style="background: #1c1c1e; padding: 40px 20px; border-radius: 25px; border: 0.5px solid #333;">
                <h2 id="helloName" style="margin:0;">Hello!</h2>
                <p style="color:#8e8e93; font-size: 14px; margin-top: 10px;" id="rankText">Connecting...</p>
            </div>
        </div>
    </div>

    <div class="page" id="chat-pg">
        <div class="feed" id="chatFeed"></div>
        <div class="input-area">
            <div class="input-box">
                <input type="text" id="mIn" placeholder="Aa" onkeydown="if(event.key==='Enter') send()">
            </div>
            <button class="send-btn" onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div class="page" id="users-pg">
        <div style="padding: 15px; overflow-y: auto;" id="uList"></div>
    </div>
</div>

<nav>
    <div class="tab active" onclick="go('home-pg',this)"><i class="fa-solid fa-house"></i>Home</div>
    <div class="tab" onclick="go('chat-pg',this)"><i class="fa-solid fa-comment"></i>Chats</div>
    <div class="tab" onclick="go('users-pg',this)"><i class="fa-solid fa-users"></i>People</div>
    <div class="tab" onclick="exit()"><i class="fa-solid fa-right-from-bracket"></i>Exit</div>
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

let uID, uName, isAdmin=false, room="global_v6";

window.login = async () => {
    const n = document.getElementById("uIn").value.trim();
    if(!n) return;
    if(n.toLowerCase()==='nazim'){
        if(prompt("Admin Key:")==='786') isAdmin=true; else return;
    }
    const res = await signInAnonymously(auth);
    uID = res.user.uid; uName = n;
    localStorage.setItem("uN", n); localStorage.setItem("uA", isAdmin);
    start();
};

function start(){
    document.getElementById("auth").style.display="none";
    document.getElementById("helloName").innerText = "Hi, " + uName;
    if(isAdmin){
        document.getElementById("admBar").style.display="flex";
        document.getElementById("rankText").innerText = "System Sovereign";
    }

    const myRef = ref(db, 'nodes/'+uID);
    onValue(ref(db, '.info/connected'), (s) => {
        if(s.val()){ onDisconnect(myRef).remove(); set(myRef, {name:uName, online:true}); }
    });

    syncChat(); syncUsers();
    onValue(ref(db, 'system/mute'), s => { 
        document.getElementById("mIn").disabled = (s.val() && !isAdmin);
        document.getElementById("mIn").placeholder = (s.val() && !isAdmin) ? "Muted" : "Aa";
    });
}

window.send = () => {
    const i = document.getElementById("mIn");
    if(!i.value.trim()) return;
    push(ref(db, room), { uid:uID, name:uName, text:i.value, ts:serverTimestamp() });
    i.value="";
};

window.go = (id, el) => {
    document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));
    document.getElementById(id).classList.add("active");
    document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));
    el.classList.add("active");
    if(id==='chat-pg') setTimeout(()=>{ const f = document.getElementById("chatFeed"); f.scrollTop = f.scrollHeight; }, 100);
};

function syncChat(){
    onValue(query(ref(db, room), limitToLast(50)), s => {
        const f = document.getElementById("chatFeed"); f.innerHTML="";
        s.forEach(m => {
            const d = m.val();
            f.innerHTML += `<div class="msg ${d.uid===uID?'me':'other'}"><b>${d.name}</b>${d.text}</div>`;
        });
        f.scrollTop = f.scrollHeight;
    });
}

function syncUsers(){
    onValue(ref(db, 'nodes'), s => {
        const l = document.getElementById("uList"); l.innerHTML="<p style='color:#8e8e93; font-size:12px; margin-left:10px;'>ACTIVE NOW</p>";
        s.forEach(u => {
            if(u.key===uID) return;
            const d = u.val();
            l.innerHTML += `<div style="background:#1c1c1e; padding:12px 15px; border-radius:15px; margin-bottom:10px; display:flex; justify-content:space-between; align-items:center; border:0.5px solid #333;">
                <span style="font-weight:bold; font-size:15px;">${d.name}</span>
                <div style="display:flex; gap:8px;">
                    <button onclick="room='${uID<u.key?uID+'_'+u.key:u.key+'_'+uID}'; go('chat-pg', document.querySelectorAll('.tab')[1])" style="background:var(--p); border:none; color:white; border-radius:10px; padding:6px 12px; font-size:12px; font-weight:bold;">Chat</button>
                    ${isAdmin?`<button onclick="ban('${u.key}')" style="background:#ff3b30; border:none; color:white; border-radius:10px; padding:6px 12px; font-size:12px; font-weight:bold;">BAN</button>`:''}
                </div>
            </div>`;
        });
    });
}

window.ban = (uid) => { if(isAdmin && confirm("Ban?")) set(ref(db, 'banned/'+uid), true); };
window.admMute = () => { onValue(ref(db, 'system/mute'), s=>{ set(ref(db, 'system/mute'), !s.val()); }, {onlyOnce:true}); };
window.admWipe = () => { if(isAdmin && confirm("Wipe?")) remove(ref(db, room)); };
window.exit = () => { localStorage.clear(); signOut(auth).then(()=>location.reload()); };

onAuthStateChanged(auth, user => {
    if(user && localStorage.getItem("uN")){ 
        uID=user.uid; uName=localStorage.getItem("uN"); isAdmin=localStorage.getItem("uA")==='true'; 
        onValue(ref(db, 'banned/'+uID), s => {
            if(s.val()){ document.body.innerHTML="<h1 style='color:red; text-align:center; padding-top:100px;'>BANNED</h1>"; localStorage.clear(); }
            else start();
        });
    }
});
</script>
</body>
</html>
