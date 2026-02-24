<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Architect v1.0</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --primary: #6366f1; --bg: #0f172a; --surface: #1e293b; }
        body { margin: 0; font-family: 'Inter', sans-serif; background: var(--bg); color: white; height: 100vh; overflow: hidden; }
        .app { display: flex; flex-direction: column; height: 100vh; max-width: 500px; margin: 0 auto; background: #0b0f1a; position: relative; }
        
        /* Auth Overlay */
        #authLayer { position: fixed; inset: 0; background: #0f172a; z-index: 9999; display: flex; align-items: center; justify-content: center; transition: 0.5s; }
        .auth-card { width: 80%; text-align: center; }
        .auth-card input { width: 100%; padding: 15px; border-radius: 12px; border: 1px solid #334155; background: #1e293b; color: white; margin-bottom: 20px; }
        
        header { padding: 20px; border-bottom: 1px solid #1e293b; display: flex; justify-content: space-between; }
        .chat-area { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; }
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 18px; font-size: 14px; position: relative; line-height: 1.4; }
        .msg.me { align-self: flex-end; background: var(--primary); }
        .msg.other { align-self: flex-start; background: #334155; }
        
        .input-box { padding: 15px; background: #1e293b; display: flex; gap: 10px; }
        .input-box input { flex: 1; background: #0f172a; border: none; padding: 12px; border-radius: 10px; color: white; }
        
        #adminTools { display: none; position: absolute; top: 70px; right: 20px; background: red; padding: 10px; border-radius: 8px; font-weight: bold; cursor: pointer; }
    </style>
</head>
<body>

<div id="authLayer">
    <div class="auth-card">
        <h2>SECURE_LOGIN</h2>
        <input type="text" id="nick" placeholder="Codename...">
        <button onclick="secureLogin()" style="width:100%; padding:15px; background:var(--primary); border:none; color:white; border-radius:12px; font-weight:bold;">INITIALIZE AUTH</button>
    </div>
</div>

<div class="app">
    <header>
        <div style="font-weight:900; letter-spacing:1px;">CORE_SIGNAL</div>
        <div id="status">Connecting...</div>
    </header>
    
    <div id="adminTools" onclick="triggerServerLock()">SERVER_OVERRIDE</div>

    <div class="chat-area" id="feed"></div>

    <div class="input-box">
        <input type="text" id="mInp" placeholder="Encrypted message...">
        <button onclick="send()" style="background:var(--primary); border:none; color:white; padding:10px 20px; border-radius:8px;"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getAuth, signInAnonymously } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-auth.js";
import { getDatabase, ref, set, push, onValue, serverTimestamp, query, limitToLast } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getDatabase(app);

let userUID = "";
let userName = "";

window.secureLogin = async () => {
    userName = document.getElementById("nick").value.trim();
    if(!userName) return;

    try {
        const userCred = await signInAnonymously(auth);
        userUID = userCred.user.uid;
        
        // Register Session
        set(ref(db, `users/${userUID}`), { name: userName, lastActive: serverTimestamp() });
        
        if(userName === "Nazim") document.getElementById("adminTools").style.display = "block";
        
        document.getElementById("authLayer").style.opacity = "0";
        setTimeout(() => document.getElementById("authLayer").style.display = "none", 500);
    } catch (e) { alert("Auth Failed: " + e.message); }
};

window.send = () => {
    const txt = document.getElementById("mInp").value.trim();
    if(!txt || !userUID) return;

    push(ref(db, 'messages'), {
        uid: userUID,
        sender: userName,
        text: txt,
        timestamp: serverTimestamp()
    });
    document.getElementById("mInp").value = "";
};

// Optimized Stream (Limit to last 50 for performance)
const chatQuery = query(ref(db, 'messages'), limitToLast(50));
onValue(chatQuery, snap => {
    const feed = document.getElementById("feed");
    feed.innerHTML = "";
    snap.forEach(s => {
        const d = s.val();
        const div = document.createElement("div");
        div.className = `msg ${d.uid === userUID ? 'me' : 'other'}`;
        div.innerHTML = `<small style="display:block;font-size:10px;opacity:0.6">${d.sender}</small>${d.text}`;
        feed.appendChild(div);
    });
    feed.scrollTop = feed.scrollHeight;
});

// Server Side Override Trigger
window.triggerServerLock = () => {
    set(ref(db, 'system/lockdown'), true);
    alert("Server-side lockdown signal sent.");
};
</script>
</body>
</html>
