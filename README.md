<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>WEB-HUB | Sovereign Fixed</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root { --p: #0084ff; --bg: #000; --card: #1c1c1e; --me: #0084ff; --other: #262626; }
* { box-sizing: border-box; font-family: -apple-system, sans-serif; margin: 0; padding: 0; }

/* 🔒 THE GRID SYSTEM (Fixes everything) */
body { 
    height: 100dvh; display: grid; 
    grid-template-rows: auto auto 1fr auto auto; /* Admin, Head, Chat, Input, Nav */
    background: #000; color: white; overflow: hidden;
}

/* ADMIN BAR */
#admBar { 
    display: none; background: rgba(255, 59, 48, 0.2); 
    padding: 10px 15px; border-bottom: 1px solid #ff3b30; 
    justify-content: space-between; align-items: center; z-index: 100;
}
.btn-red { background: #ff3b30; color: white; border: none; padding: 6px 12px; border-radius: 8px; font-weight: 800; font-size: 10px; }

/* HEADER */
header { padding: 15px 20px; border-bottom: 0.5px solid #222; text-align: center; }
.brand { font-size: 20px; font-weight: 800; color: var(--p); }

/* MAIN CONTENT AREA */
.content { overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 8px; }
.page { display: none; flex-direction: column; height: 100%; }
.page.active { display: flex; }

/* CHAT BUBBLES */
.msg { max-width: 80%; padding: 10px 14px; border-radius: 18px; font-size: 15px; line-height: 1.4; margin-bottom: 4px; }
.msg.me { align-self: flex-end; background: var(--me); border-bottom-right-radius: 4px; }
.msg.other { align-self: flex-start; background: var(--other); border-bottom-left-radius: 4px; }
.msg b { font-size: 10px; display: block; opacity: 0.6; margin-bottom: 2px; }

/* INPUT BOX */
.input-area { 
    padding: 10px 15px; background: #000; border-top: 0.5px solid #222;
    display: flex; align-items: center; gap: 10px;
}
.input-box { flex: 1; background: #222; border-radius: 20px; padding: 5px 15px; }
.input-box input { width: 100%; background: none; border: none; color: white; height: 35px; outline: none; font-size: 16px; }

/* NAVIGATION */
nav { 
    height: 65px; border-top: 0.5px solid #222;
    display: flex; justify-content: space-around; align-items: center;
    background: #000; padding-bottom: env(safe-area-inset-bottom);
}
.tab { color: #8e8e93; font-size: 10px; text-align: center; cursor: pointer; }
.tab i { font-size: 20px; display: block; margin-bottom: 4px; }
.tab.active { color: var(--p); }

/* AUTH (OVERLAY) */
#auth { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; }
.auth-box { width: 85%; text-align: center; }
.auth-box input { width: 100%; padding: 16px; background: #1c1c1e; border: 1px solid #333; border-radius: 15px; color: white; margin-bottom: 12px; font-size: 16px; text-align: center; }
.auth-box button { width: 100%; padding: 16px; background: var(--p); border: none; border-radius: 15px; color: white; font-weight: bold; }
</style>
</head>
<body>

<div id="auth">
    <div class="auth-box">
        <h1 style="font-size: 36px; margin-bottom: 10px;">Web-Hub</h1>
        <input type="text" id="uIn" placeholder="Username">
        <button onclick="login()">Connect</button>
    </div>
</div>

<div id="admBar">
    <span style="font-weight: 900; color: #ff3b30;">SYSTEM OVERRIDE</span>
    <div>
        <button class="btn-red" onclick="admMute()">MUTE</button>
        <button class="btn-red" style="margin-left:5px;" onclick="admWipe()">WIPE</button>
    </div>
</div>

<header><div class="brand">Live Chat</div></header>

<div class="content">
    <div class="page active" id="home-pg">
        <div style="background: #1c1c1e; padding: 40px 20px; border-radius: 20px; text-align: center; border: 1px solid #333;">
            <h2 id="helloName">Welcome</h2>
            <p style="color: #8e8e93; font-size: 13px; margin-top: 5px;">Authenticated Secure Session</p>
        </div>
    </div>
    <div class="page" id="chat-pg"><div id="chatFeed" style="display:flex; flex-direction:column; gap:8px;"></div></div>
    <div class="page" id="users-pg"><div id="uList"></div></div>
</div>

<div class="input-area" id="inputContainer" style="display: none;">
    <div class="input-box"><input type="text" id="mIn" placeholder="Aa" onkeydown="if(event.key==='Enter') send()"></div>
    <button onclick="send()" style="color:var(--p); border:none; background:none; font-size:24px;"><i class="fa-solid fa-paper-plane"></i></button>
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

let uID, uName, isAdmin=false, room="global_v7";

window.login = async () => {
    const n = document.getElementById("uIn").value.trim();
    if(!n) return;
    if(n.toLowerCase()==='nazim'){
        if(prompt("Key:")==='786') isAdmin=true; else return;
    }
    const res = await signInAnonymously(auth);
    uID = res.user.uid; uName = n;
    localStorage.setItem("uN", n); localStorage.setItem("uA", isAdmin);
    start();
};

function start(){
    document.getElementById("auth").style.display="none";
    document.getElementById("helloName").innerText = "Hi, " + uName;
    if(isAdmin) document.getElementById("admBar").style.display="flex";

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
    
    document.getElementById("inputContainer").style.display = (id === 'chat-pg') ? 'flex' : 'none';
    if(id==='chat-pg') setTimeout(()=>{ const c = document.querySelector(".content"); c.scrollTop = c.scrollHeight; }, 100);
};

function syncChat(){
    onValue(query(ref(db, room), limitToLast(50)), s => {
        const f = document.getElementById("chatFeed"); f.innerHTML="";
        s.forEach(m => {
            const d = m.val();
            f.innerHTML += `<div class="msg ${d.uid===uID?'me':'other'}"><b>${d.name}</b>${d.text}</div>`;
        });
        const c = document.querySelector(".content"); c.scrollTop = c.scrollHeight;
    });
}

function syncUsers(){
    onValue(ref(db, 'nodes'), s => {
        const l = document.getElementById("uList"); l.innerHTML="<p style='color:#8e8e93; font-size:12px; margin-bottom:10px;'>ACTIVE</p>";
        s.forEach(u => {
            if(u.key===uID) return;
            const d = u.val();
            l.innerHTML += `<div style="background:#1c1c1e; padding:12px; border-radius:12px; margin-bottom:8px; display:flex; justify-content:space-between; align-items:center;">
                <span style="font-weight:bold;">${d.name}</span>
                <button onclick="room='${uID<u.key?uID+'_'+u.key:u.key+'_'+uID}'; go('chat-pg', document.querySelectorAll('.tab')[1])" style="background:var(--p); border:none; color:white; border-radius:8px; padding:6px 12px; font-size:12px;">Chat</button>
            </div>`;
        });
    });
}

window.admMute = () => { onValue(ref(db, 'system/mute'), s=>{ set(ref(db, 'system/mute'), !s.val()); }, {onlyOnce:true}); };
window.admWipe = () => { if(isAdmin && confirm("Wipe?")) remove(ref(db, room)); };
window.exit = () => { localStorage.clear(); signOut(auth).then(()=>location.reload()); };

onAuthStateChanged(auth, user => {
    if(user && localStorage.getItem("uN")){ 
        uID=user.uid; uName=localStorage.getItem("uN"); isAdmin=localStorage.getItem("uA")==='true'; 
        start();
    }
});
</script>
</body>
</html>
