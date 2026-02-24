<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Connect | God Mode</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #6366f1; --accent: #a855f7; --bg: #020617;
            --surface: rgba(30, 41, 59, 0.7); --text: #f8fafc;
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { 
            margin: 0; background: var(--bg); color: var(--text);
            font-family: 'Plus Jakarta Sans', sans-serif; height: 100dvh; 
            display: flex; justify-content: center; overflow: hidden;
        }

        /* App Container */
        .app-wrap {
            width: 100%; max-width: 500px; height: 100dvh;
            display: flex; flex-direction: column; position: relative;
            background: radial-gradient(circle at top right, #1e1b4b, #020617);
        }

        /* Modern Header */
        header {
            padding: 1.5rem; display: flex; justify-content: space-between; align-items: center;
            backdrop-filter: blur(15px); border-bottom: 1px solid rgba(255,255,255,0.05);
        }
        .brand { font-weight: 800; font-size: 1.4rem; background: linear-gradient(to right, #818cf8, #c084fc); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* User Status Bar */
        .status-bar {
            display: flex; gap: 15px; padding: 10px 1.5rem; 
            overflow-x: auto; scrollbar-width: none; border-bottom: 1px solid rgba(255,255,255,0.05);
        }
        .status-bar::-webkit-scrollbar { display: none; }
        .u-node { display: flex; flex-direction: column; align-items: center; min-width: 60px; }
        .u-avatar { width: 50px; height: 50px; border-radius: 50%; border: 2px solid var(--primary); padding: 2px; }
        .u-avatar img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
        .u-node span { font-size: 10px; margin-top: 5px; opacity: 0.7; }

        /* Chat Canvas */
        .chat-canvas {
            flex: 1; overflow-y: auto; padding: 1.5rem;
            display: flex; flex-direction: column; gap: 1.2rem;
            scroll-behavior: smooth; padding-bottom: 100px;
        }

        .msg-row { display: flex; flex-direction: column; max-width: 80%; animation: pop 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        @keyframes pop { from { opacity: 0; transform: scale(0.8); } to { opacity: 1; transform: scale(1); } }
        
        .msg-row.me { align-self: flex-end; align-items: flex-end; }
        .msg-row.other { align-self: flex-start; align-items: flex-start; }

        .bubble {
            padding: 12px 16px; border-radius: 20px; font-size: 15px; line-height: 1.5;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2); position: relative;
        }
        .me .bubble { background: linear-gradient(135deg, var(--primary), var(--accent)); border-bottom-right-radius: 4px; }
        .other .bubble { background: var(--surface); border-bottom-left-radius: 4px; border: 1px solid rgba(255,255,255,0.05); }

        .meta { font-size: 10px; margin-top: 5px; opacity: 0.5; display: flex; gap: 5px; align-items: center; }
        .crown { color: #fbbf24; font-size: 12px; margin-right: 4px; }

        /* Bottom Command Center */
        .cmd-center {
            position: fixed; bottom: 0; width: 100%; max-width: 500px;
            padding: 15px; background: rgba(2, 6, 23, 0.9); backdrop-filter: blur(20px);
            border-top: 1px solid rgba(255,255,255,0.1); z-index: 100;
        }
        .typing-pill { position: absolute; top: -30px; left: 20px; font-size: 12px; color: var(--primary); display: none; }
        
        .input-group {
            display: flex; align-items: center; gap: 10px; 
            background: rgba(255,255,255,0.05); padding: 8px 15px; border-radius: 30px;
            border: 1px solid rgba(255,255,255,0.1);
        }
        .input-group input {
            flex: 1; background: none; border: none; color: white; padding: 10px 0;
            font-size: 16px; outline: none;
        }
        .btn-action {
            width: 42px; height: 42px; border-radius: 50%; border: none;
            background: var(--primary); color: white; cursor: pointer;
            transition: 0.2s; display: flex; align-items: center; justify-content: center;
        }
        .btn-action:active { transform: scale(0.9); }

        /* Auth Screen */
        #authScreen {
            position: fixed; inset: 0; z-index: 9999; background: var(--bg);
            display: flex; flex-direction: column; align-items: center; justify-content: center;
        }
        .login-box {
            width: 85%; max-width: 350px; padding: 2.5rem; text-align: center;
            background: var(--surface); border-radius: 30px; border: 1px solid rgba(255,255,255,0.1);
        }
    </style>
</head>
<body>

<div id="authScreen">
    <div class="login-box">
        <h1 style="margin-bottom:0.5rem">Welcome_</h1>
        <p style="font-size: 0.8rem; opacity: 0.5; margin-bottom: 2rem;">Secure Biometric Simulation</p>
        <input type="text" id="nodeName" placeholder="Enter Codename" style="width:100%; padding:1rem; border-radius:15px; border:none; background:#020617; color:white; margin-bottom:1rem; text-align:center;">
        <button onclick="bootApp()" class="btn-auth" style="width:100%; padding:1rem; border-radius:15px; border:none; background:var(--primary); color:white; font-weight:800; cursor:pointer;">INITIALIZE</button>
    </div>
</div>

<div class="app-wrap">
    <header>
        <div class="brand">INFINITY_CONNECT</div>
        <i class="fa-solid fa-ellipsis-vertical" style="opacity:0.6"></i>
    </header>

    <div class="status-bar" id="activeUsers">
        </div>

    <div class="chat-canvas" id="canvas"></div>

    <div class="cmd-center">
        <div id="typingInfo" class="typing-pill">Someone is typing...</div>
        <div class="input-group">
            <i class="fa-regular fa-face-smile" style="opacity:0.5"></i>
            <input type="text" id="mainInp" placeholder="Send an encrypted signal..." oninput="handleTyping()">
            <button class="btn-action" onclick="sendSignal()">
                <i class="fa-solid fa-paper-plane"></i>
            </button>
        </div>
    </div>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getAuth, signInAnonymously } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
    import { getDatabase, ref, set, push, onValue, serverTimestamp, query, limitToLast, onDisconnect } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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

    let myUID = "";
    let myName = "";
    let typingTimer;

    window.bootApp = async () => {
        const input = document.getElementById("nodeName");
        myName = input.value.trim();
        if(!myName) return;

        try {
            const cred = await signInAnonymously(auth);
            myUID = cred.user.uid;

            // Presence System
            const myStatusRef = ref(db, 'status/' + myUID);
            set(myStatusRef, { name: myName, online: true, typing: false });
            onDisconnect(myStatusRef).remove();

            document.getElementById("authScreen").style.display = "none";
        } catch (e) { alert(e.message); }
    };

    // Typing Logic
    window.handleTyping = () => {
        set(ref(db, `status/${myUID}/typing`), true);
        clearTimeout(typingTimer);
        typingTimer = setTimeout(() => {
            set(ref(db, `status/${myUID}/typing`), false);
        }, 2000);
    };

    // Active Users & Typing Sync
    onValue(ref(db, 'status'), snap => {
        const bar = document.getElementById("activeUsers");
        const typ = document.getElementById("typingInfo");
        bar.innerHTML = "";
        let typingUsers = [];

        snap.forEach(s => {
            const d = s.val();
            if(s.key !== myUID && d.typing) typingUsers.push(d.name);
            
            bar.innerHTML += `
                <div class="u-node">
                    <div class="u-avatar">
                        <img src="https://ui-avatars.com/api/?name=${d.name}&background=random&color=fff">
                    </div>
                    <span>${d.name}</span>
                </div>
            `;
        });

        typ.style.display = typingUsers.length > 0 ? "block" : "none";
        typ.innerText = typingUsers.join(", ") + " is typing...";
    });

    // Messaging
    window.sendSignal = () => {
        const inp = document.getElementById("mainInp");
        const val = inp.value.trim();
        if(!val) return;

        push(ref(db, 'v60_messages'), {
            uid: myUID,
            sender: myName,
            text: val,
            ts: serverTimestamp()
        });
        inp.value = "";
        set(ref(db, `status/${myUID}/typing`), false);
    };

    const q = query(ref(db, 'v60_messages'), limitToLast(40));
    onValue(q, snap => {
        const canvas = document.getElementById("canvas");
        canvas.innerHTML = "";
        snap.forEach(s => {
            const d = s.val();
            const isMe = d.uid === myUID;
            const isAdmin = d.sender.toLowerCase() === "nazim";

            canvas.innerHTML += `
                <div class="msg-row ${isMe ? 'me' : 'other'}">
                    <div class="bubble">${d.text}</div>
                    <div class="meta">
                        ${isAdmin ? '<i class="fa-solid fa-crown crown"></i>' : ''}
                        <b>${d.sender}</b> • Now
                        ${isMe ? '<i class="fa-solid fa-check-double" style="color:var(--primary)"></i>' : ''}
                    </div>
                </div>
            `;
        });
        canvas.scrollTop = canvas.scrollHeight;
    });
</script>
</body>
</html>
