<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>WEB-HUB | Final Fixed</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root { --p: #0084ff; --bg: #000; --card: #1c1c1e; --me: #0084ff; --other: #262626; }
* { box-sizing: border-box; font-family: -apple-system, sans-serif; }

body { 
    margin: 0; background: var(--bg); color: white; 
    height: 100dvh; display: flex; flex-direction: column; overflow: hidden;
}

/* 👑 ADMIN BAR - FIXED TOP */
#admBar { 
    display: none; background: rgba(255, 59, 48, 0.2); 
    padding: 10px; border-bottom: 1px solid #ff3b30; 
    justify-content: space-between; align-items: center; font-size: 12px;
}
.btn-red { background: #ff3b30; color: white; border: none; padding: 5px 10px; border-radius: 6px; font-weight: bold; }

/* 📱 HEADER */
header { padding: 15px 20px; border-bottom: 1px solid #222; background: #000; }
.brand { font-size: 22px; font-weight: 800; }

/* 💬 CHAT FEED - SCROLLABLE */
.page { display: none; flex: 1; overflow: hidden; }
.page.active { display: flex; flex-direction: column; }

#chat-pg { display: none; flex-direction: column; height: 100%; }
#chat-pg.active { display: flex; }

.feed { 
    flex: 1; overflow-y: auto; padding: 15px; 
    display: flex; flex-direction: column; gap: 8px;
}

/* MESSAGES */
.msg { max-width: 75%; padding: 10px 15px; border-radius: 18px; font-size: 15px; position: relative; }
.msg.me { align-self: flex-end; background: var(--me); border-bottom-right-radius: 4px; }
.msg.other { align-self: flex-start; background: var(--other); border-bottom-left-radius: 4px; }
.msg span { font-size: 10px; opacity: 0.5; display: block; margin-bottom: 3px; }

/* ⌨️ STICKY INPUT BOX (FIXED) */
.input-area { 
    background: #000; padding: 10px 15px 10px 15px;
    border-top: 1px solid #222; display: flex; align-items: center; gap: 10px;
}
.input-box { 
    flex: 1; background: #262626; border-radius: 20px; 
    padding: 8px 15px; display: flex; align-items: center;
}
.input-box input { 
    flex: 1; background: none; border: none; color: white; 
    outline: none; font-size: 16px; height: 35px;
}
.send-btn { 
    color: var(--p); font-size: 22px; border: none; background: none; 
}

/* BOTTOM NAVIGATION */
nav { 
    height: 65px; background: #000; border-top: 1px solid #222;
    display: flex; justify-content: space-around; align-items: center;
}
.tab { color: #8e8e93; text-align: center; font-size: 11px; text-decoration: none; cursor: pointer; }
.tab i { font-size: 20px; display: block; margin-bottom: 4px; }
.tab.active { color: var(--p); }

/* AUTH */
#auth { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; align-items: center; justify-content: center; }
.auth-box { width: 80%; text-align: center; }
.auth-box input { width: 100%; padding: 15px; background: #1c1c1e; border: none; border-radius: 10px; color: white; margin-bottom: 10px; font-size: 16px; }
.auth-box button { width: 100%; padding: 15px; background: var(--p); border: none; border-radius: 10px; color: white; font-weight: bold; }
</style>
</head>
<body>

<div id="auth">
    <div class="auth-box">
        <h1 style="font-size: 40px; margin-bottom: 30px;">Web-Hub</h1>
        <input type="text" id="uIn" placeholder="Enter Username">
        <button onclick="login()">Connect</button>
    </div>
</div>

<div id="admBar">
    <span style="font-weight: 900; color: #ff3b30;">GOD MODE</span>
    <div>
        <button class="btn-red" onclick="admMute()">MUTE</button>
        <button class="btn-red" style="margin-left:5px;" onclick="admWipe()">WIPE</button>
    </div>
</div>

<header id="head">
    <div class="brand">Chats</div>
</header>

<div class="page active" id="home-pg">
    <div style="padding: 20px; text-align: center;">
        <div style="background: #1c1c1e; padding: 40px 20px; border-radius: 20px;">
            <h2 id="helloName">Welcome</h2>
            <p style="color:#8e8e93" id="rankText">Connecting to secure server...</p>
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
    <div style="padding: 20px;" id="uList"></div>
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

let uID, uName, isAdmin=false, room="global_v5";

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
        document.getElementById("rankText").innerText = "Sovereign Administrator";
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
            f.innerHTML += `<div class="msg ${d.uid===uID?'me':'other'}"><span>${d.name}</span>${d.text}</div>`;
        });
        f.scrollTop = f.scrollHeight;
    });
}

function syncUsers(){
    onValue(ref(db, 'nodes'), s => {
        const l = document.getElementById("uList"); l.innerHTML="<h3 style='font-size:14px; color:#8e8e93;'>ONLINE</h3>";
        s.forEach(u => {
            if(u.key===uID) return;
            const d = u.val();
            l.innerHTML += `<div style="background:#1c1c1e; padding:15px; border-radius:12px; margin-bottom:10px; display:flex; justify-content:space-between; align-items:center;">
                <span style="font-weight:bold;">${d.name}</span>
                <div style="display:flex; gap:10px;">
                    <button onclick="room='${uID<u.key?uID+'_'+u.key:u.key+'_'+uID}'; go('chat-pg', document.querySelectorAll('.tab')[1])" style="background:var(--p); border:none; color:white; border-radius:5px; padding:5px 10px;">Chat</button>
                    ${isAdmin?`<button onclick="ban('${u.key}')" style="background:#ff3b30; border:none; color:white; border-radius:5px; padding:5px 10px;">BAN</button>`:''}
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
