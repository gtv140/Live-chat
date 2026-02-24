<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Empire v10 | Next-Gen Chat</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { 
            --accent: #6366f1; 
            --bg: #0f172a; 
            --glass: rgba(30, 41, 59, 0.7);
            --text: #f8fafc;
        }

        /* 🔒 Screen Lock Logic */
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        html, body { 
            margin: 0; padding: 0; width: 100%; height: 100%; 
            overflow: hidden; position: fixed; 
            background: var(--bg); color: var(--text);
            font-family: 'Inter', -apple-system, sans-serif;
        }

        body { display: flex; flex-direction: column; }

        /* 💎 Modern Header */
        header { 
            height: 70px; background: var(--glass); backdrop-filter: blur(15px);
            display: flex; align-items: center; justify-content: space-between;
            padding: 0 20px; border-bottom: 1px solid rgba(255,255,255,0.1);
            z-index: 100; flex-shrink: 0;
        }
        .logo { font-size: 22px; font-weight: 800; background: linear-gradient(to right, #818cf8, #c084fc); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* 🚀 Main Viewport */
        main { 
            flex: 1; overflow-y: auto; display: flex; flex-direction: column;
            background: radial-gradient(circle at top right, #1e1b4b, #0f172a);
            -webkit-overflow-scrolling: touch;
        }

        .page { display: none; padding: 20px; flex-direction: column; min-height: 100%; }
        .page.active { display: flex; }

        /* 💬 Modern Chat Bubbles */
        #chatFeed { display: flex; flex-direction: column; gap: 12px; }
        .bubble { 
            max-width: 85%; padding: 12px 18px; border-radius: 22px; 
            font-size: 15px; position: relative; box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            animation: fadeIn 0.3s ease;
        }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        .bubble.me { align-self: flex-end; background: linear-gradient(135deg, #6366f1, #a855f7); color: white; border-bottom-right-radius: 4px; }
        .bubble.other { align-self: flex-start; background: var(--glass); border: 1px solid rgba(255,255,255,0.1); border-bottom-left-radius: 4px; }
        .bubble .sender { font-size: 10px; font-weight: 700; text-transform: uppercase; margin-bottom: 4px; display: block; opacity: 0.8; }

        /* ⌨️ Modern Floating Input */
        #inputArea { 
            padding: 15px; background: var(--glass); backdrop-filter: blur(15px);
            border-top: 1px solid rgba(255,255,255,0.1); display: none; align-items: center; gap: 10px;
        }
        .input-pill { 
            flex: 1; background: rgba(0,0,0,0.3); border-radius: 30px; 
            padding: 5px 20px; border: 1px solid rgba(255,255,255,0.05);
        }
        .input-pill input { width: 100%; background: none; border: none; color: white; height: 45px; outline: none; font-size: 16px; }
        .btn-send { width: 45px; height: 45px; background: var(--accent); border-radius: 50%; border: none; color: white; font-size: 18px; box-shadow: 0 0 15px rgba(99, 102, 241, 0.4); }

        /* 📱 Bottom Tabs */
        nav { 
            height: 75px; background: var(--glass); backdrop-filter: blur(15px);
            display: flex; justify-content: space-around; align-items: center;
            border-top: 1px solid rgba(255,255,255,0.1); padding-bottom: 10px;
        }
        .tab { color: #64748b; font-size: 10px; font-weight: 600; text-align: center; cursor: pointer; transition: 0.3s; }
        .tab i { font-size: 24px; display: block; margin-bottom: 4px; }
        .tab.active { color: var(--accent); transform: translateY(-5px); }

        /* 🔓 Auth Overlay */
        #authOverlay { position: fixed; inset: 0; background: var(--bg); z-index: 1000; display: flex; align-items: center; justify-content: center; }
        .auth-box { width: 85%; text-align: center; }
        .auth-box h1 { font-size: 40px; margin-bottom: 10px; letter-spacing: -2px; }
        .auth-input { width: 100%; padding: 18px; background: var(--glass); border: 1px solid rgba(255,255,255,0.1); border-radius: 18px; color: white; text-align: center; font-size: 18px; margin-bottom: 15px; }
        .auth-btn { width: 100%; padding: 18px; background: var(--accent); border: none; border-radius: 18px; color: white; font-weight: 800; font-size: 16px; }
    </style>
</head>
<body>

<div id="authOverlay">
    <div class="auth-box">
        <h1>Empire</h1>
        <p style="color: #94a3b8; margin-bottom: 30px;">Enter the digital realm</p>
        <input type="text" id="userName" class="auth-input" placeholder="Codename">
        <button class="auth-btn" onclick="initialize()">INITIALIZE</button>
    </div>
</div>

<header>
    <div class="logo">EMPIRE v10</div>
    <div id="adminCrown" style="display:none; color: #fbbf24;"><i class="fa-solid fa-crown fa-beat-fade"></i></div>
</header>

<main id="viewport">
    <div class="page active" id="p-home">
        <div style="background: var(--glass); padding: 40px 20px; border-radius: 30px; border: 1px solid rgba(255,255,255,0.05); text-align: center; margin-top: 20px;">
            <h2 id="helloTxt">Synchronizing...</h2>
            <p style="color: #94a3b8; font-size: 14px; margin-top: 10px;">Security protocols active. Encryption 100%.</p>
        </div>
    </div>
    <div class="page" id="p-chat"><div id="chatFeed"></div></div>
    <div class="page" id="p-users"><div id="onlineList"></div></div>
</main>

<div id="inputArea">
    <div class="input-pill"><input type="text" id="msgInput" placeholder="Write a message..." onkeydown="if(event.key==='Enter') pushMsg()"></div>
    <button class="btn-send" onclick="pushMsg()"><i class="fa-solid fa-paper-plane"></i></button>
</div>

<nav>
    <div class="tab active" onclick="navigate('p-home',this)"><i class="fa-solid fa-compass"></i>Hub</div>
    <div class="tab" onclick="navigate('p-chat',this)"><i class="fa-solid fa-ghost"></i>Shadow</div>
    <div class="tab" onclick="navigate('p-users',this)"><i class="fa-solid fa-user-astronaut"></i>Aliens</div>
    <div class="tab" onclick="logout()"><i class="fa-solid fa-power-off"></i>End</div>
</nav>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp, query, limitToLast, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

const fbConfig = {
  apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain:"live-chat-b810c.firebaseapp.com",
  databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId:"live-chat-b810c",
  storageBucket:"live-chat-b810c.firebasestorage.app",
  messagingSenderId:"555058795334",
  appId:"1:555058795334:web:f668887409800c32970b47"
};

const app = initializeApp(fbConfig);
const auth = getAuth(app);
const db = getDatabase(app);

let uid, name, isAdmin=false, room="v10_main";

window.initialize = async () => {
    const n = document.getElementById("userName").value.trim();
    if(!n) return;
    if(n.toLowerCase()==='nazim'){ if(prompt("Auth Code:")==='786') isAdmin=true; else return; }
    const res = await signInAnonymously(auth);
    uid = res.user.uid; name = n;
    localStorage.setItem("un", n); localStorage.setItem("ia", isAdmin);
    startHub();
};

function startHub(){
    document.getElementById("authOverlay").style.display="none";
    document.getElementById("helloTxt").innerText = "Welcome, " + name;
    if(isAdmin) document.getElementById("adminCrown").style.display="block";

    const presenceRef = ref(db, 'presence/'+uid);
    onValue(ref(db, '.info/connected'), s => { if(s.val()){ onDisconnect(presenceRef).remove(); set(presenceRef, {name:name, ts:serverTimestamp()}); }});

    syncM(); syncU();
}

window.pushMsg = () => {
    const i = document.getElementById("msgInput");
    if(!i.value.trim()) return;
    push(ref(db, room), { uid:uid, name:name, text:i.value, ts:serverTimestamp() });
    i.value="";
};

window.navigate = (id, el) => {
    document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));
    document.getElementById(id).classList.add("active");
    document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));
    el.classList.add("active");
    document.getElementById("inputArea").style.display = (id==='p-chat') ? 'flex' : 'none';
    if(id==='p-chat') { const v = document.getElementById("viewport"); v.scrollTop = v.scrollHeight; }
};

function syncM(){
    onValue(query(ref(db, room), limitToLast(50)), s => {
        const feed = document.getElementById("chatFeed"); feed.innerHTML="";
        s.forEach(m => {
            const d = m.val();
            feed.innerHTML += `<div class="bubble ${d.uid===uid?'me':'other'}"><span class="sender">${d.name}</span>${d.text}</div>`;
        });
        document.getElementById("viewport").scrollTop = document.getElementById("viewport").scrollHeight;
    });
}

function syncU(){
    onValue(ref(db, 'presence'), s => {
        const list = document.getElementById("onlineList"); list.innerHTML="";
        s.forEach(u => {
            if(u.key===uid) return;
            list.innerHTML += `<div style="background:var(--glass); padding:15px; border-radius:20px; margin-bottom:10px; display:flex; align-items:center; gap:15px; border:1px solid rgba(255,255,255,0.05);">
                <div style="width:10px; height:10px; background:#4ade80; border-radius:50%; box-shadow: 0 0 10px #4ade80;"></div>
                <b style="flex:1">${u.val().name}</b>
                <button onclick="room='${uid<u.key?uid+'_'+u.key:u.key+'_'+uid}'; navigate('p-chat', document.querySelectorAll('.tab')[1])" style="background:var(--accent); border:none; color:white; padding:8px 15px; border-radius:12px; font-weight:700;">CHIP IN</button>
            </div>`;
        });
    });
}

window.logout = () => { localStorage.clear(); signOut(auth).then(()=>location.reload()); };

onAuthStateChanged(auth, user => {
    if(user && localStorage.getItem("un")){ 
        uid=user.uid; name=localStorage.getItem("un"); isAdmin=localStorage.getItem("ia")==='true'; 
        startHub();
    }
});
</script>
</body>
</html>
