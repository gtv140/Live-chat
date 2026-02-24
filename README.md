<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>WEB-HUB | Messenger Pro</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root { 
    --p: #0084ff; /* Messenger Blue */
    --s: #a033ff; /* Messenger Purple */
    --bg: #000000; 
    --card: #1c1c1e; 
    --me: linear-gradient(135deg, #0084ff, #00a2ff);
    --other: #262626;
    --text: #ffffff;
}

* { box-sizing: border-box; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; -webkit-font-smoothing: antialiased; }

body { 
    margin: 0; background: var(--bg); color: var(--text); 
    height: 100dvh; display: flex; flex-direction: column; overflow: hidden;
}

/* Admin Controls (Floating) */
#admTerminal { 
    display: none; position: fixed; top: 70px; left: 10px; right: 10px; 
    background: rgba(255, 59, 48, 0.2); backdrop-filter: blur(10px);
    border: 1px solid #ff3b30; padding: 10px; border-radius: 15px; 
    z-index: 1000; justify-content: space-between; align-items: center;
}

/* Header */
header { 
    padding: 15px 20px; display: flex; justify-content: space-between; align-items: center;
    background: rgba(0,0,0,0.8); backdrop-filter: blur(20px); border-bottom: 0.5px solid #333;
}
.brand { font-weight: 800; font-size: 1.5rem; letter-spacing: -0.5px; }

/* Pages */
.page { display: none; flex: 1; overflow: hidden; position: relative; }
.page.active { display: flex; flex-direction: column; }

/* 💬 REAL MESSENGER CHAT BOXES */
.feed { 
    flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 4px; 
    scroll-behavior: smooth; padding-bottom: 100px;
}

.msg-wrapper { display: flex; flex-direction: column; margin-bottom: 8px; }
.msg-wrapper.me { align-items: flex-end; }
.msg-wrapper.other { align-items: flex-start; }

.bubble { 
    padding: 10px 16px; border-radius: 20px; font-size: 15px; max-width: 75%;
    line-height: 1.4; word-wrap: break-word;
}
.me .bubble { background: var(--me); color: white; border-bottom-right-radius: 4px; }
.other .bubble { background: var(--other); color: white; border-bottom-left-radius: 4px; }

.sender { font-size: 10px; opacity: 0.5; margin: 0 10px 4px 10px; font-weight: 600; }

/* ⌨️ STYLISH INPUT BOX (MODERN) */
.dock-area { 
    position: absolute; bottom: 0; left: 0; right: 0;
    padding: 10px 15px 30px 15px; background: linear-gradient(to top, black, transparent);
}
.input-container { 
    background: #262626; border-radius: 25px; padding: 5px 5px 5px 15px;
    display: flex; align-items: center; gap: 10px;
}
.input-container input { 
    flex: 1; background: none; border: none; color: white; height: 40px; 
    outline: none; font-size: 16px; 
}
.send-btn { 
    width: 38px; height: 38px; background: var(--p); border-radius: 50%;
    display: flex; align-items: center; justify-content: center; border: none; color: white;
}

/* Bottom Tabs */
.tabs { 
    height: 75px; background: #000; border-top: 0.5px solid #333;
    display: flex; justify-content: space-around; align-items: center; padding-bottom: 15px;
}
.tab { color: #8e8e93; font-size: 1.2rem; cursor: pointer; text-align: center; }
.tab.active { color: var(--p); }
.tab span { display: block; font-size: 10px; margin-top: 4px; font-weight: 500; }

/* Auth */
#auth { position: fixed; inset: 0; background: #000; z-index: 5000; display: flex; align-items: center; justify-content: center; }
.auth-inner { width: 85%; text-align: center; }
.auth-inner input { width: 100%; padding: 16px; background: #1c1c1e; border: none; border-radius: 12px; color: white; margin-bottom: 15px; font-size: 16px; text-align: center; }
.auth-inner button { width: 100%; padding: 16px; background: var(--p); border: none; border-radius: 12px; color: white; font-weight: 700; font-size: 16px; }

/* User Card */
.user-row { background: #1c1c1e; padding: 12px 15px; border-radius: 15px; margin-bottom: 8px; display: flex; align-items: center; gap: 12px; }
.status-dot { width: 10px; height: 10px; background: #34c759; border-radius: 50%; }
.ban-link { color: #ff3b30; font-size: 11px; font-weight: 700; margin-left: auto; text-decoration: none; }
</style>
</head>
<body>

<div id="auth">
    <div class="auth-inner">
        <h1 style="font-size: 2.5rem; letter-spacing: -1px; margin-bottom: 5px;">Web-Hub</h1>
        <p style="color: #8e8e93; margin-bottom: 30px;">Enter your asset codename</p>
        <input type="text" id="username" placeholder="Codename">
        <button onclick="login()">Get Started</button>
    </div>
</div>

<div id="admTerminal">
    <span style="font-weight: 800; color: #ff3b30;">ADMIN COMMANDS</span>
    <div>
        <button onclick="admMute()" style="background:white; border:none; padding:5px 10px; border-radius:5px; font-size:10px; font-weight:800;">MUTE</button>
        <button onclick="admWipe()" style="background:#ff3b30; border:none; color:white; padding:5px 10px; border-radius:5px; font-size:10px; font-weight:800; margin-left:5px;">WIPE</button>
    </div>
</div>

<header>
    <div class="brand">Chats</div>
    <div id="adminCrown" style="display:none; color:#ffcc00;"><i class="fa-solid fa-crown"></i></div>
</header>

<div class="page active" id="home-pg">
    <div style="padding: 20px;">
        <div style="background: #1c1c1e; padding: 30px; border-radius: 25px; text-align:center;">
            <h2 id="hello" style="margin:0">Hi there!</h2>
            <p id="sub" style="color:#8e8e93; font-size: 14px;">Welcome to your secure empire.</p>
        </div>
    </div>
</div>

<div class="page" id="chat-pg">
    <div class="feed" id="chatFeed"></div>
    <div class="dock-area">
        <div class="input-container">
            <input type="text" id="mInput" placeholder="Aa" onkeydown="if(event.key==='Enter') sendMsg()">
            <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-arrow-up"></i></button>
        </div>
        <div id="muteLabel" style="display:none; text-align:center; color:#ff3b30; font-size:11px; font-weight:800; margin-top:5px;">🔇 CHAT DISABLED BY ADMIN</div>
    </div>
</div>

<div class="page" id="users-pg">
    <div style="padding: 20px;">
        <h3 style="color:#8e8e93; font-size: 13px; text-transform: uppercase;">Active Now</h3>
        <div id="liveList"></div>
    </div>
</div>

<nav class="tabs">
    <div class="tab active" onclick="go('home-pg',this)"><i class="fa-solid fa-house"></i><span>Home</span></div>
    <div class="tab" onclick="go('chat-pg',this)"><i class="fa-solid fa-comment"></i><span>Chats</span></div>
    <div class="tab" onclick="go('users-pg',this)"><i class="fa-solid fa-users"></i><span>People</span></div>
    <div class="tab" onclick="out()"><i class="fa-solid fa-right-from-bracket"></i><span>Exit</span></div>
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

let uID, uName, isAdmin=false, room="global_v4";

const fixScroll = () => { const f = document.getElementById("chatFeed"); f.scrollTop = f.scrollHeight; };

window.login = async () => {
    const val = document.getElementById("username").value.trim();
    if(!val) return;
    if(val.toLowerCase()==='nazim'){
        if(prompt("Admin Key:")==='786') isAdmin=true; else return;
    }
    const res = await signInAnonymously(auth);
    uID = res.user.uid; uName = val;
    localStorage.setItem("uN", val); localStorage.setItem("uA", isAdmin);
    init();
};

function init(){
    document.getElementById("auth").style.display="none";
    document.getElementById("hello").innerText = "Hi, " + uName;
    if(isAdmin){
        document.getElementById("admTerminal").style.display="flex";
        document.getElementById("adminCrown").style.display="block";
        document.getElementById("sub").innerText = "System Administrator (God Mode)";
    }

    const myRef = ref(db, 'nodes/'+uID);
    onValue(ref(db, '.info/connected'), (s) => {
        if(s.val()===true){ onDisconnect(myRef).remove(); set(myRef, {name:uName, online:true}); }
    });

    syncChat(); syncPeople();
    onValue(ref(db, 'system/mute'), s => { 
        document.getElementById("mInput").disabled = (s.val() && !isAdmin);
        document.getElementById("muteLabel").style.display = (s.val() && !isAdmin) ? "block" : "none";
    });
}

window.sendMsg = () => {
    const i = document.getElementById("mInput");
    if(!i.value.trim()) return;
    push(ref(db, room), { uid:uID, name:uName, text:i.value, ts:serverTimestamp() });
    i.value="";
};

window.go = (id, el) => {
    document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));
    document.getElementById(id).classList.add("active");
    document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));
    el.classList.add("active");
    if(id==='chat-pg') setTimeout(fixScroll, 100);
};

function syncChat(){
    onValue(query(ref(db, room), limitToLast(50)), s => {
        const feed = document.getElementById("chatFeed"); feed.innerHTML="";
        s.forEach(m => {
            const d = m.val();
            feed.innerHTML += `
                <div class="msg-wrapper ${d.uid===uID?'me':'other'}">
                    <span class="sender">${d.name}</span>
                    <div class="bubble">${d.text}</div>
                </div>`;
        });
        fixScroll();
    });
}

function syncPeople(){
    onValue(ref(db, 'nodes'), s => {
        const list = document.getElementById("liveList"); list.innerHTML="";
        s.forEach(u => {
            if(u.key===uID) return;
            const d = u.val();
            const row = document.createElement('div'); row.className='user-row';
            row.innerHTML = `
                <img src="https://ui-avatars.com/api/?name=${d.name}&background=random&color=fff" style="width:40px; height:40px; border-radius:50%;">
                <div style="flex:1">
                    <div style="font-weight:600; font-size:15px;">${d.name}</div>
                    <div style="font-size:11px; color:#34c759;">Online Now</div>
                </div>
                ${isAdmin?`<span class="ban-link" onclick="ban('${u.key}')">BAN</span>`:''}
            `;
            row.onclick = (e) => { if(e.target.className!=='ban-link') startPriv(u.key, d.name); };
            list.appendChild(row);
        });
    });
}

window.startPriv = (tid, tname) => {
    room = uID < tid ? `${uID}_${tid}` : `${tid}_${uID}`;
    go('chat-pg', document.querySelectorAll(".tab")[1]);
};

window.ban = (uid) => { if(isAdmin && confirm("Ban?")) set(ref(db, 'banned/'+uid), true); };
window.admMute = () => { onValue(ref(db, 'system/mute'), s=>{ set(ref(db, 'system/mute'), !s.val()); }, {onlyOnce:true}); };
window.admWipe = () => { if(isAdmin && confirm("Wipe?")) remove(ref(db, 'global_v4')); };
window.out = () => { localStorage.clear(); signOut(auth).then(()=>location.reload()); };

onAuthStateChanged(auth, user => {
    if(user && localStorage.getItem("uN")){ 
        uID=user.uid; uName=localStorage.getItem("uN"); isAdmin=localStorage.getItem("uA")==='true'; 
        onValue(ref(db, 'banned/'+uID), s => {
            if(s.val()){ document.body.innerHTML="<h1 style='color:red; text-align:center; padding-top:100px;'>ACCESS DENIED</h1>"; localStorage.clear(); }
            else init();
        });
    }
});
</script>
</body>
</html>
