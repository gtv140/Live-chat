<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Production v45</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --brand: #6366f1; --bg: #0f172a; --surface: #1e293b; }
        body { margin: 0; background: var(--bg); color: #f8fafc; font-family: 'Plus Jakarta Sans', sans-serif; height: 100vh; overflow: hidden; }
        
        /* Auth Overlay */
        #authLayer { position: fixed; inset: 0; z-index: 9999; background: var(--bg); display: flex; align-items: center; justify-content: center; transition: 0.5s; }
        .auth-card { width: 85%; max-width: 400px; text-align: center; padding: 2rem; background: rgba(255,255,255,0.03); border-radius: 24px; border: 1px solid rgba(255,255,255,0.1); backdrop-filter: blur(10px); }
        .auth-card input { width: 100%; padding: 1rem; border-radius: 12px; border: 1px solid #334155; background: #0f172a; color: white; margin-bottom: 1rem; font-size: 1rem; }
        .btn-auth { width: 100%; padding: 1rem; border: none; border-radius: 12px; background: var(--brand); color: white; font-weight: 700; cursor: pointer; box-shadow: 0 10px 15px -3px rgba(99, 102, 241, 0.4); }

        /* Main App */
        .app-shell { width: 100%; max-width: 500px; height: 100vh; margin: 0 auto; display: flex; flex-direction: column; background: #0b0f1a; position: relative; }
        header { padding: 1.5rem; border-bottom: 1px solid rgba(255,255,255,0.05); display: flex; justify-content: space-between; align-items: center; }
        .chat-view { flex: 1; overflow-y: auto; padding: 1rem; display: flex; flex-direction: column; gap: 1rem; padding-bottom: 140px; }
        
        .bubble { max-width: 80%; padding: 1rem; border-radius: 18px; font-size: 0.95rem; line-height: 1.4; animation: slideIn 0.3s ease; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .bubble.me { align-self: flex-end; background: var(--brand); border-bottom-right-radius: 4px; }
        .bubble.other { align-self: flex-start; background: var(--surface); border-bottom-left-radius: 4px; }

        .input-area { position: absolute; bottom: 20px; left: 15px; right: 15px; background: rgba(30, 41, 59, 0.8); backdrop-filter: blur(10px); padding: 10px; border-radius: 50px; display: flex; gap: 10px; border: 1px solid rgba(255,255,255,0.1); }
        .input-area input { flex: 1; background: none; border: none; color: white; padding: 10px 15px; outline: none; }
        .btn-send { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--brand); color: white; cursor: pointer; }
    </style>
</head>
<body>

<div id="authLayer">
    <div class="auth-card">
        <h2 style="margin-top:0">System Access</h2>
        <input type="text" id="nick" placeholder="Enter Codename">
        <button class="btn-auth" id="loginBtn" onclick="initSession()">INITIALIZE AUTH</button>
        <p id="errorLog" style="color: #ef4444; font-size: 0.8rem; margin-top: 1rem; display: none;"></p>
    </div>
</div>

<div class="app-shell">
    <header>
        <div style="font-weight:800; color:var(--brand)">CORE_SIGNAL v45</div>
        <div id="connectionStatus" style="font-size:0.7rem; opacity:0.6;">● Synchronizing...</div>
    </header>

    <div class="chat-view" id="feed"></div>

    <div class="input-area">
        <input type="text" id="msgInp" placeholder="Type a message..." onkeydown="if(event.key==='Enter') dispatch()">
        <button class="btn-send" onclick="dispatch()"><i class="fa-solid fa-bolt"></i></button>
    </div>
</div>

<script type="module">
    // Modern Firebase SDK Imports
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
    import { getDatabase, ref, set, push, onValue, serverTimestamp, query, limitToLast } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

    // ✅ Aapki Configuration
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

    let userUID = "";
    let userName = "";

    // Session Initialization
    window.initSession = async () => {
        userName = document.getElementById("nick").value.trim();
        const errBox = document.getElementById("errorLog");
        const btn = document.getElementById("loginBtn");

        if(!userName) { alert("Please enter a name"); return; }
        
        btn.innerText = "AUTHENTICATING...";
        btn.disabled = true;

        try {
            const userCred = await signInAnonymously(auth);
            userUID = userCred.user.uid;
            
            // Register User Presence
            await set(ref(db, `active_nodes/${userUID}`), {
                name: userName,
                online: true,
                lastSeen: serverTimestamp()
            });

            document.getElementById("authLayer").style.opacity = "0";
            setTimeout(() => document.getElementById("authLayer").style.display = "none", 500);
            document.getElementById("connectionStatus").innerText = "● Secure Link Active";
            
        } catch (error) {
            console.error(error);
            errBox.style.display = "block";
            errBox.innerText = "Error: " + error.message;
            btn.innerText = "RETRY AUTH";
            btn.disabled = false;
        }
    };

    // Message Dispatcher
    window.dispatch = () => {
        const inp = document.getElementById("msgInp");
        const txt = inp.value.trim();
        if(!txt || !userUID) return;

        push(ref(db, 'v45_messages'), {
            uid: userUID,
            sender: userName,
            text: txt,
            ts: serverTimestamp()
        });
        inp.value = "";
    };

    // Real-time Stream
    const q = query(ref(db, 'v45_messages'), limitToLast(50));
    onValue(q, snap => {
        const feed = document.getElementById("feed");
        feed.innerHTML = "";
        snap.forEach(s => {
            const d = s.val();
            const div = document.createElement("div");
            div.className = `bubble ${d.uid === userUID ? 'me' : 'other'}`;
            div.innerHTML = `<small style="display:block; font-size:10px; opacity:0.6; margin-bottom:4px;">${d.sender}</small>${d.text}`;
            feed.appendChild(div);
        });
        feed.scrollTop = feed.scrollHeight;
    });

</script>
</body>
</html>
