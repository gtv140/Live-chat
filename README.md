<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Empire Legend | Nazim Edition</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --wa-dark: #075e54; --wa-light: #128c7e; --wa-bg: #fff; --text: #111b21; --gray: #667781; }
        
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; margin: 0; padding: 0; }
        
        /* 🔒 Poori Screen Lock */
        html, body { height: 100%; width: 100%; overflow: hidden; position: fixed; background: var(--wa-bg); font-family: sans-serif; }
        body { display: flex; flex-direction: column; }

        /* 🟢 WhatsApp Style Header (Full Width) */
        header { 
            background: var(--wa-dark); color: white; padding: 15px 20px; 
            display: flex; justify-content: space-between; align-items: center; 
            flex-shrink: 0; width: 100%; z-index: 100;
        }
        .brand { font-size: 22px; font-weight: bold; }

        /* 🛠️ Admin Override (Below Header) */
        #admBar { display: none; background: #ffebee; padding: 10px; border-bottom: 1px solid #ddd; justify-content: space-around; flex-shrink: 0; }
        .adm-btn { background: #d32f2f; color: white; border: none; padding: 5px 15px; border-radius: 5px; font-weight: bold; font-size: 12px; }

        /* 📜 Main Content Area (Full Screen Scrolling) */
        main { 
            flex: 1; overflow-y: auto; background: var(--wa-bg); 
            -webkit-overflow-scrolling: touch; display: flex; flex-direction: column;
        }
        .page { display: none; width: 100%; height: 100%; flex-direction: column; }
        .page.active { display: flex; }

        /* 💬 Chat Bubbles (Fixed UI) */
        #chatFeed { padding: 15px; display: flex; flex-direction: column; gap: 8px; background: #e5ddd5; min-height: 100%; }
        .bubble { 
            max-width: 80%; padding: 8px 12px; border-radius: 10px; font-size: 15px; 
            box-shadow: 0 1px 1px rgba(0,0,0,0.15); line-height: 1.4;
        }
        .bubble.me { align-self: flex-end; background: #dcf8c6; border-bottom-right-radius: 2px; }
        .bubble.other { align-self: flex-start; background: #fff; border-bottom-left-radius: 2px; }
        .bubble b { display: block; font-size: 10px; color: var(--wa-light); margin-bottom: 2px; text-transform: uppercase; }

        /* 👥 User List (Clean & Full) */
        .user-item { 
            padding: 15px 20px; border-bottom: 1px solid #f0f2f5; 
            display: flex; align-items: center; gap: 15px; cursor: pointer;
        }
        .avatar { width: 50px; height: 50px; background: #dfe5e7; border-radius: 50%; display: flex; align-items: center; justify-content: center; color: #fff; font-size: 20px; }
        .user-info { flex: 1; }
        .user-info h4 { font-size: 16px; color: var(--text); }
        .user-info p { font-size: 13px; color: var(--gray); margin-top: 2px; }

        /* ⌨️ Sticky Input Area */
        #inputArea { 
            background: #f0f2f5; padding: 10px 15px; display: none; 
            align-items: center; gap: 10px; flex-shrink: 0;
        }
        .input-wrap { flex: 1; background: white; border-radius: 25px; padding: 2px 15px; }
        .input-wrap input { width: 100%; border: none; height: 40px; outline: none; font-size: 16px; }
        .send-btn { background: var(--wa-light); color: white; border: none; width: 45px; height: 45px; border-radius: 50%; font-size: 20px; }

        /* 📱 Bottom Navigation (WhatsApp Style) */
        nav { 
            height: 65px; background: #fff; border-top: 1px solid #f0f2f5; 
            display: flex; justify-content: space-around; align-items: center; flex-shrink: 0;
            padding-bottom: env(safe-area-inset-bottom);
        }
        .tab { color: var(--gray); font-size: 12px; text-align: center; flex: 1; cursor: pointer; }
        .tab i { font-size: 22px; display: block; margin-bottom: 4px; }
        .tab.active { color: var(--wa-light); }

        /* 🚪 Login Page (Full Screen) */
        #loginOverlay { position: fixed; inset: 0; background: white; z-index: 1000; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 30px; text-align: center; }
        .login-box input { width: 100%; padding: 15px; border: 1px solid #ddd; border-radius: 10px; margin: 20px 0 10px; font-size: 18px; text-align: center; }
        .login-box button { width: 100%; padding: 15px; background: var(--wa-light); color: white; border: none; border-radius: 10px; font-weight: bold; font-size: 16px; }
    </style>
</head>
<body>

<div id="loginOverlay">
    <div class="login-box" style="width: 100%;">
        <h1 style="color: var(--wa-light); font-size: 32px;">Empire Hub</h1>
        <p style="color: var(--gray); margin-top: 10px;">Connect to your secure network</p>
        <input type="text" id="joinName" placeholder="Enter Your Name">
        <button onclick="joinHub()">AGREE & CONTINUE</button>
    </div>
</div>

<header>
    <div class="brand">WhatsApp Legend</div>
    <div style="font-size: 18px;"><i class="fa-solid fa-camera"></i> <i class="fa-solid fa-search" style="margin-left:20px;"></i></div>
</header>

<div id="admBar">
    <button class="adm-btn" onclick="muteSys()">MUTE SYSTEM</button>
    <button class="adm-btn" onclick="wipeSys()">WIPE CHATS</button>
</div>

<main id="scroller">
    <div class="page active" id="pg-home">
        <div id="activeUsers"></div>
    </div>

    <div class="page" id="pg-chat">
        <div id="chatFeed"></div>
    </div>
</main>

<div id="inputArea">
    <div class="input-wrap"><input type="text" id="mIn" placeholder="Message" onkeydown="if(event.key==='Enter') sendMsg()"></div>
    <button class="send-btn" onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
</div>

<nav>
    <div class="tab active" onclick="go('pg-home',this)"><i class="fa-solid fa-message"></i>Chats</div>
    <div class="tab" onclick="go('pg-chat',this)"><i class="fa-solid fa-ghost"></i>Ghost Chat</div>
    <div class="tab" onclick="quit()"><i class="fa-solid fa-power-off"></i>Exit</div>
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
const db = getDatabase(config.databaseURL ? app : initializeApp(config)); // Safe re-init

let uid, name, boss=false, room="legend_v11";

window.joinHub = async () => {
    const val = document.getElementById("joinName").value.trim();
    if(!val) return;
    if(val.toLowerCase()==='nazim'){ if(prompt("Admin Key:")==='786') boss=true; else return; }
    const res = await signInAnonymously(auth);
    uid = res.user.uid; name = val;
    localStorage.setItem("n_name", val); localStorage.setItem("n_boss", boss);
    run();
};

function run(){
    document.getElementById("loginOverlay").style.display="none";
    if(boss) document.getElementById("admBar").style.display="flex";

    const nodeRef = ref(getDatabase(), 'nodes/'+uid);
    onValue(ref(getDatabase(), '.info/connected'), s => { if(s.val()){ onDisconnect(nodeRef).remove(); set(nodeRef, {name:name, online:true}); }});

    syncChats(); syncPeople();
}

window.sendMsg = () => {
    const i = document.getElementById("mIn");
    if(!i.value.trim()) return;
    push(ref(getDatabase(), room), { uid:uid, name:name, text:i.value, ts:serverTimestamp() });
    i.value="";
};

window.go = (id, el) => {
    document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));
    document.getElementById(id).classList.add("active");
    document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));
    el.classList.add("active");
    document.getElementById("inputArea").style.display = (id==='pg-chat') ? 'flex' : 'none';
    if(id==='pg-chat') scroll();
};

function scroll(){ const s = document.getElementById("scroller"); s.scrollTop = s.scrollHeight; }

function syncChats(){
    onValue(query(ref(getDatabase(), room), limitToLast(50)), s => {
        const f = document.getElementById("chatFeed"); f.innerHTML="";
        s.forEach(m => {
            const d = m.val();
            f.innerHTML += `<div class="bubble ${d.uid===uid?'me':'other'}"><b>${d.name}</b>${d.text}</div>`;
        });
        scroll();
    });
}

function syncPeople(){
    onValue(ref(getDatabase(), 'nodes'), s => {
        const list = document.getElementById("activeUsers"); list.innerHTML="";
        s.forEach(u => {
            if(u.key===uid) return;
            list.innerHTML += `<div class="user-item" onclick="room='${uid<u.key?uid+'_'+u.key:u.key+'_'+uid}'; go('pg-chat', document.querySelectorAll('.tab')[1])">
                <div class="avatar"><i class="fa-solid fa-user"></i></div>
                <div class="user-info">
                    <h4>${u.val().name}</h4>
                    <p>Online - Tap to message</p>
                </div>
            </div>`;
        });
    });
}

window.muteSys = () => { /* Logic here */ };
window.wipeSys = () => { if(boss && confirm("Wipe?")) remove(ref(getDatabase(), room)); };
window.quit = () => { localStorage.clear(); signOut(auth).then(()=>location.reload()); };

onAuthStateChanged(auth, user => {
    if(user && localStorage.getItem("n_name")){ 
        uid=user.uid; name=localStorage.getItem("n_name"); boss=localStorage.getItem("n_boss")==='true'; 
        run();
    }
});
</script>
</body>
</html>
