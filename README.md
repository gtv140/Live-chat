<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>WEB-HUB | Live Empire</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --p: #0084ff; --bg: #000; --card: #1c1c1e; --me: #0084ff; --other: #262626; --head: #0a0a0a; }
        
        /* 🔒 Layout Fix: No Scroll on Body */
        * { box-sizing: border-box; font-family: -apple-system, system-ui, sans-serif; margin: 0; padding: 0; }
        html, body { height: 100%; width: 100%; overflow: hidden; background: #000; color: #fff; position: fixed; }

        body { display: flex; flex-direction: column; }

        /* 👑 Header Section */
        header { height: 60px; background: var(--head); display: flex; align-items: center; padding: 0 20px; border-bottom: 1px solid #222; flex-shrink: 0; justify-content: space-between; }
        .logo { font-size: 20px; font-weight: 800; color: var(--p); }

        /* 🛠️ Admin Bar (Only for Nazim) */
        #admBar { display: none; height: 50px; background: rgba(255, 50, 50, 0.15); border-bottom: 1px solid #ff3b30; align-items: center; justify-content: space-around; padding: 0 10px; flex-shrink: 0; }
        .adm-btn { background: #ff3b30; color: white; border: none; padding: 6px 12px; border-radius: 8px; font-size: 11px; font-weight: bold; }

        /* 📜 Main Viewport (The only scrollable part) */
        main { flex: 1; overflow-y: auto; display: flex; flex-direction: column; background: #000; -webkit-overflow-scrolling: touch; position: relative; }
        .page { display: none; padding: 15px; flex-direction: column; min-height: 100%; }
        .page.active { display: flex; }

        /* 💬 Chat System v9 */
        #chatFeed { display: flex; flex-direction: column; gap: 10px; padding-bottom: 20px; }
        .bubble { max-width: 80%; padding: 10px 15px; border-radius: 18px; font-size: 15px; line-height: 1.4; position: relative; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        .bubble.me { align-self: flex-end; background: var(--me); border-bottom-right-radius: 4px; }
        .bubble.other { align-self: flex-start; background: var(--other); border-bottom-left-radius: 4px; }
        .bubble span { font-size: 10px; display: block; opacity: 0.6; margin-bottom: 3px; font-weight: bold; }

        /* ⌨️ Fixed Chat Input Area */
        #inputStrip { height: 70px; background: var(--head); display: none; align-items: center; padding: 0 15px; gap: 10px; border-top: 1px solid #222; flex-shrink: 0; }
        .msg-box { flex: 1; background: #222; border-radius: 25px; padding: 0 15px; display: flex; align-items: center; }
        .msg-box input { flex: 1; background: none; border: none; color: white; height: 40px; outline: none; font-size: 16px; }
        .send-btn { background: var(--p); color: white; border: none; width: 42px; height: 42px; border-radius: 50%; font-size: 18px; cursor: pointer; }

        /* 📱 Bottom Navigation */
        nav { height: 70px; background: var(--head); border-top: 1px solid #222; display: flex; justify-content: space-around; align-items: center; flex-shrink: 0; padding-bottom: env(safe-area-inset-bottom); }
        .nav-tab { color: #8e8e93; font-size: 11px; text-align: center; cursor: pointer; flex: 1; }
        .nav-tab i { font-size: 22px; display: block; margin-bottom: 4px; }
        .nav-tab.active { color: var(--p); }

        /* 🚪 Login Overlay */
        #authLayer { position: fixed; inset: 0; background: #000; z-index: 2000; display: flex; align-items: center; justify-content: center; }
        .auth-card { width: 85%; text-align: center; }
        .auth-card input { width: 100%; padding: 16px; background: #1c1c1e; border: 1px solid #333; border-radius: 12px; color: white; margin-bottom: 12px; font-size: 16px; text-align: center; }
        .auth-card button { width: 100%; padding: 16px; background: var(--p); border: none; border-radius: 12px; color: white; font-weight: bold; font-size: 16px; }
    </style>
</head>
<body>

<div id="authLayer">
    <div class="auth-card">
        <h1 style="font-size: 38px; margin-bottom: 10px;">Web-Hub</h1>
        <p style="color:#8e8e93; margin-bottom: 30px;">Secure Mobile Connection</p>
        <input type="text" id="nodeName" placeholder="Your Name">
        <button onclick="boot()">Connect to Hub</button>
    </div>
</div>

<div id="admBar">
    <span style="font-weight: 900; color: #ff3b30; font-size: 12px;">SYSTEM ROOT</span>
    <button class="adm-btn" onclick="muteAll()">MUTE ALL</button>
    <button class="adm-btn" onclick="wipeAll()">WIPE</button>
</div>

<header>
    <div class="logo">Empire Hub</div>
    <div id="crown" style="display:none; color:gold;"><i class="fa-solid fa-crown"></i></div>
</header>

<main id="scrollZone">
    <div class="page active" id="pg-home">
        <div style="background: var(--card); padding: 30px; border-radius: 20px; text-align:center; border: 1px solid #333; margin-top: 20px;">
            <h2 id="welcome">Welcome</h2>
            <p style="color:#8e8e93; margin-top: 10px; font-size: 14px;">Everything is fixed and ready.</p>
        </div>
    </div>
    
    <div class="page" id="pg-chat">
        <div id="chatFeed"></div>
    </div>

    <div class="page" id="pg-live">
        <div id="userList"></div>
    </div>
</main>

<div id="inputStrip">
    <div class="msg-box"><input type="text" id="mIn" placeholder="Type here..." onkeydown="if(event.key==='Enter') send()"></div>
    <button class="send-btn" onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
</div>

<nav>
    <div class="nav-tab active" onclick="go('pg-home',this)"><i class="fa-solid fa-house"></i>Home</div>
    <div class="nav-tab" onclick="go('pg-chat',this)"><i class="fa-solid fa-message"></i>Chats</div>
    <div class="nav-tab" onclick="go('pg-live',this)"><i class="fa-solid fa-users"></i>People</div>
    <div class="nav-tab" onclick="quit()"><i class="fa-solid fa-power-off"></i>Exit</div>
</nav>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp, query, limitToLast, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

const config = {
  apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain:"live-chat-b810c.firebaseapp.com",
  databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId:"live-chat-b810c",
  storageBucket:"live-chat-b810c.firebasestorage.app",
  messagingSenderId:"555058795334",
  appId:"1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(config);
const auth = getAuth(app);
const db = getDatabase(app);

let uID, uName, boss=false, room="v9_global";

window.boot = async () => {
    const val = document.getElementById("nodeName").value.trim();
    if(!val) return;
    if(val.toLowerCase()==='nazim'){ if(prompt("Pass:")==='786') boss=true; else return; }
    const res = await signInAnonymously(auth);
    uID = res.user.uid; uName = val;
    localStorage.setItem("n", val); localStorage.setItem("b", boss);
    setup();
};

function setup(){
    document.getElementById("authLayer").style.display="none";
    document.getElementById("welcome").innerText = "Hi, " + uName;
    if(boss){ document.getElementById("admBar").style.display="flex"; document.getElementById("crown").style.display="block"; }

    const nodeRef = ref(db, 'nodes/'+uID);
    onValue(ref(db, '.info/connected'), s => { if(s.val()){ onDisconnect(nodeRef).remove(); set(nodeRef, {name:uName, live:true}); }});

    syncM(); syncU();
    onValue(ref(db, 'system/mute'), s => { 
        document.getElementById("mIn").disabled = (s.val() && !boss);
        document.getElementById("mIn").placeholder = (s.val() && !boss) ? "System Muted" : "Type here...";
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
    document.querySelectorAll(".nav-tab").forEach(t=>t.classList.remove("active"));
    el.classList.add("active");
    document.getElementById("inputStrip").style.display = (id==='pg-chat') ? 'flex' : 'none';
    if(id==='pg-chat') scroll();
};

function scroll(){ const z = document.getElementById("scrollZone"); z.scrollTop = z.scrollHeight; }

function syncM(){
    onValue(query(ref(db, room), limitToLast(40)), s => {
        const f = document.getElementById("chatFeed"); f.innerHTML="";
        s.forEach(m => {
            const d = m.val();
            f.innerHTML += `<div class="bubble ${d.uid===uID?'me':'other'}"><span>${d.name}</span>${d.text}</div>`;
        });
        scroll();
    });
}

function syncU(){
    onValue(ref(db, 'nodes'), s => {
        const l = document.getElementById("userList"); l.innerHTML="<p style='color:#555; font-size:12px; margin-bottom:10px;'>ACTIVE CONNECTIONS</p>";
        s.forEach(u => {
            if(u.key===uID) return;
            l.innerHTML += `<div style="background:var(--card); padding:15px; border-radius:15px; margin-bottom:8px; display:flex; justify-content:space-between; align-items:center;">
                <b>${u.val().name}</b>
                <button onclick="room='${uID<u.key?uID+'_'+u.key:u.key+'_'+uID}'; go('pg-chat', document.querySelectorAll('.nav-tab')[1])" style="background:var(--p); border:none; color:white; padding:6px 15px; border-radius:10px; font-weight:bold;">Message</button>
            </div>`;
        });
    });
}

window.muteAll = () => { onValue(ref(db, 'system/mute'), s=>set(ref(db, 'system/mute'), !s.val()), {onlyOnce:true}); };
window.wipeAll = () => { if(boss && confirm("Wipe Hub?")) remove(ref(db, room)); };
window.quit = () => { localStorage.clear(); signOut(auth).then(()=>location.reload()); };

onAuthStateChanged(auth, user => {
    if(user && localStorage.getItem("n")){ 
        uID=user.uid; uName=localStorage.getItem("n"); boss=localStorage.getItem("b")==='true'; 
        setup();
    }
});
</script>
</body>
</html>
