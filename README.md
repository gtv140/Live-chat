<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Live Connect | Nazim Edition</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --main: #0084ff; --bg: #f0f2f5; --white: #ffffff; --text: #1c1e21; --gray: #65676b; }
        
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; margin: 0; padding: 0; }
        
        /* Full Screen Fix */
        html, body { height: 100%; width: 100%; overflow: hidden; position: fixed; background: var(--bg); font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; }
        body { display: flex; flex-direction: column; color: var(--text); }

        /* Header */
        header { 
            background: var(--white); padding: 15px 20px; 
            display: flex; justify-content: space-between; align-items: center; 
            border-bottom: 1px solid #ddd; flex-shrink: 0;
        }
        .logo { font-size: 24px; font-weight: 800; color: var(--main); letter-spacing: -1px; }

        /* Admin Bar */
        #admBar { display: none; background: #fff0f0; padding: 8px; border-bottom: 1px solid #ffdada; justify-content: center; gap: 10px; flex-shrink: 0; }
        .adm-btn { background: #fa3e3e; color: white; border: none; padding: 6px 12px; border-radius: 6px; font-weight: bold; font-size: 11px; }

        /* Main Viewport */
        main { flex: 1; overflow-y: auto; background: var(--bg); display: flex; flex-direction: column; -webkit-overflow-scrolling: touch; }
        .page { display: none; width: 100%; min-height: 100%; flex-direction: column; padding: 10px; }
        .page.active { display: flex; }

        /* Chat Bubbles */
        #chatFeed { display: flex; flex-direction: column; gap: 8px; padding-bottom: 20px; }
        .bubble { max-width: 85%; padding: 10px 14px; border-radius: 18px; font-size: 15px; line-height: 1.4; position: relative; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }
        .bubble.me { align-self: flex-end; background: var(--main); color: white; border-bottom-right-radius: 4px; }
        .bubble.other { align-self: flex-start; background: var(--white); color: var(--text); border-bottom-left-radius: 4px; }
        .bubble b { display: block; font-size: 10px; opacity: 0.7; margin-bottom: 2px; }

        /* User List */
        .user-card { background: var(--white); padding: 15px; border-radius: 12px; margin-bottom: 8px; display: flex; justify-content: space-between; align-items: center; box-shadow: 0 1px 2px rgba(0,0,0,0.05); }
        .user-name { font-weight: 600; font-size: 16px; }

        /* Input Area */
        #inputStrip { background: var(--white); padding: 10px 15px; border-top: 1px solid #ddd; display: none; align-items: center; gap: 10px; flex-shrink: 0; }
        .input-box { flex: 1; background: #f0f2f7; border-radius: 20px; padding: 0 15px; }
        .input-box input { width: 100%; border: none; height: 40px; background: none; outline: none; font-size: 16px; }
        .send-btn { color: var(--main); border: none; background: none; font-size: 22px; cursor: pointer; }

        /* Navbar */
        nav { height: 70px; background: var(--white); border-top: 1px solid #ddd; display: flex; align-items: center; flex-shrink: 0; padding-bottom: env(safe-area-inset-bottom); }
        .tab { flex: 1; text-align: center; color: var(--gray); font-size: 11px; font-weight: 500; cursor: pointer; }
        .tab i { font-size: 22px; display: block; margin-bottom: 3px; }
        .tab.active { color: var(--main); }

        /* Login Screen */
        #authScreen { position: fixed; inset: 0; background: var(--white); z-index: 1000; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 30px; }
        .login-form { width: 100%; max-width: 350px; text-align: center; }
        .login-form input { width: 100%; padding: 16px; border: 1px solid #ddd; border-radius: 12px; margin: 20px 0 10px; font-size: 16px; outline-color: var(--main); }
        .login-form button { width: 100%; padding: 16px; background: var(--main); color: white; border: none; border-radius: 12px; font-weight: bold; font-size: 16px; box-shadow: 0 4px 12px rgba(0,132,255,0.3); }
    </style>
</head>
<body>

<div id="authScreen">
    <div class="login-form">
        <h1 class="logo" style="font-size: 40px;">Live Connect</h1>
        <p style="color: var(--gray); margin-top: 5px;">Join the secure network</p>
        <input type="text" id="nameIn" placeholder="Enter Your Name">
        <button onclick="startApp()">Get Started</button>
    </div>
</div>

<header>
    <div class="logo">Live Connect</div>
    <div id="onlineTag" style="color: #31a24c; font-size: 12px; font-weight: 700;"><i class="fa-solid fa-circle"></i> LIVE</div>
</header>

<div id="admBar">
    <button class="adm-btn" onclick="wipe()">WIPE ALL DATA</button>
</div>

<main id="mainScroll">
    <div class="page active" id="pg-home">
        <div style="background: white; padding: 25px; border-radius: 15px; text-align: center; margin-top: 10px;">
            <h3 id="hiName">Welcome!</h3>
            <p style="color: var(--gray); font-size: 14px; margin-top: 8px;">Network connection established.</p>
        </div>
    </div>
    <div class="page" id="pg-chat"><div id="chatFeed"></div></div>
    <div class="page" id="pg-users"><div id="uList"></div></div>
</main>

<div id="inputStrip">
    <div class="input-box"><input type="text" id="mIn" placeholder="Type a message..." onkeydown="if(event.key==='Enter') send()"></div>
    <button class="send-btn" onclick="send()"><i class="fa-solid fa-paper-plane"></i></button>
</div>

<nav>
    <div class="tab active" onclick="go('pg-home',this)"><i class="fa-solid fa-house"></i>Home</div>
    <div class="tab" onclick="go('pg-chat',this)"><i class="fa-solid fa-comment"></i>Messages</div>
    <div class="tab" onclick="go('pg-users',this)"><i class="fa-solid fa-users"></i>People</div>
    <div class="tab" onclick="exit()"><i class="fa-solid fa-right-from-bracket"></i>Exit</div>
</nav>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp, query, limitToLast, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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

let myID, myName, isNazim = false, chatRoom = "live_connect_v1";

window.startApp = async () => {
    const n = document.getElementById("nameIn").value.trim();
    if(!n) return;
    if(n.toLowerCase() === 'nazim'){ if(prompt("Pass:")==='786') isNazim = true; else return; }
    const res = await signInAnonymously(auth);
    myID = res.user.uid; myName = n;
    localStorage.setItem("lc_n", n); localStorage.setItem("lc_a", isNazim);
    init();
};

function init(){
    document.getElementById("authScreen").style.display = "none";
    document.getElementById("hiName").innerText = "Hi, " + myName;
    if(isNazim) document.getElementById("admBar").style.display = "flex";

    const presenceRef = ref(db, 'nodes/' + myID);
    onValue(ref(db, '.info/connected'), (s) => {
        if(s.val()){ onDisconnect(presenceRef).remove(); set(presenceRef, {name: myName, online: true}); }
    });

    syncM(); syncU();
}

window.send = () => {
    const i = document.getElementById("mIn");
    if(!i.value.trim()) return;
    push(ref(db, chatRoom), { uid: myID, name: myName, text: i.value, ts: serverTimestamp() });
    i.value = "";
};

window.go = (id, el) => {
    document.querySelectorAll(".page").forEach(p => p.classList.remove("active"));
    document.getElementById(id).classList.add("active");
    document.querySelectorAll(".tab").forEach(t => t.classList.remove("active"));
    el.classList.add("active");
    document.getElementById("inputStrip").style.display = (id === 'pg-chat') ? 'flex' : 'none';
    if(id === 'pg-chat') scroll();
};

function scroll(){ const m = document.getElementById("mainScroll"); m.scrollTop = m.scrollHeight; }

function syncM(){
    onValue(query(ref(db, chatRoom), limitToLast(50)), (s) => {
        const f = document.getElementById("chatFeed"); f.innerHTML = "";
        s.forEach((m) => {
            const d = m.val();
            f.innerHTML += `<div class="bubble ${d.uid === myID ? 'me' : 'other'}"><b>${d.name}</b>${d.text}</div>`;
        });
        scroll();
    });
}

function syncU(){
    onValue(ref(db, 'nodes'), (s) => {
        const l = document.getElementById("uList"); l.innerHTML = "<p style='color:gray; font-size:12px; margin-bottom:10px;'>ONLINE USERS</p>";
        s.forEach((u) => {
            if(u.key === myID) return;
            l.innerHTML += `<div class="user-card">
                <span class="user-name">${u.val().name}</span>
                <button onclick="chatRoom='${myID<u.key?myID+'_'+u.key:u.key+'_'+myID}'; go('pg-chat', document.querySelectorAll('.tab')[1])" style="background:var(--main); color:white; border:none; padding:6px 12px; border-radius:8px; font-weight:bold;">Chat</button>
            </div>`;
        });
    });
}

window.wipe = () => { if(isNazim && confirm("Delete Chats?")) remove(ref(db, chatRoom)); };
window.exit = () => { localStorage.clear(); signOut(auth).then(() => location.reload()); };

onAuthStateChanged(auth, user => {
    if(user && localStorage.getItem("lc_n")){ 
        myID = user.uid; myName = localStorage.getItem("lc_n"); isNazim = localStorage.getItem("lc_a") === 'true'; 
        init();
    }
});
</script>
</body>
</html>
