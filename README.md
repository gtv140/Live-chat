<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | God Mode Private</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --p: #7d5fff; --s: #ff4dff; --bg: #050505; --glass: rgba(255,255,255,0.05); --border: rgba(255,255,255,0.1); --acc: #ff4d4d; }
        * { box-sizing: border-box; font-family: 'Segoe UI', sans-serif; transition: 0.3s; }
        body { margin: 0; background: var(--bg); color: #fff; overflow: hidden; height: 100dvh; }

        /* MATRIX HACKING OVERLAY */
        #hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:20000; color:#0f0; padding:20px; font-family:monospace; font-size:11px; overflow:hidden; }

        /* LOGIN SCREEN */
        #auth { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 30px; text-align: center; }
        #auth img { width: 160px; height: 160px; border-radius: 50%; border: 3px solid var(--p); box-shadow: 0 0 60px var(--p); object-fit: cover; margin-bottom: 20px; }
        #auth h1 { font-size: 28px; margin: 0; letter-spacing: -1px; text-transform: uppercase; background: linear-gradient(to right, #fff, var(--p)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        #auth input { width: 100%; max-width: 300px; padding: 18px; border-radius: 20px; background: var(--glass); border: 1px solid var(--border); color: #fff; margin: 25px 0 15px; text-align: center; font-size: 16px; outline: none; }
        #auth button { width: 100%; max-width: 300px; padding: 18px; border-radius: 20px; background: var(--p); border: none; color: #fff; font-weight: 800; cursor: pointer; text-transform: uppercase; box-shadow: 0 10px 25px rgba(125,95,255,0.4); }

        /* MAIN APP */
        .app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; border-left: 1px solid var(--border); border-right: 1px solid var(--border); }
        header { padding: 50px 20px 15px; border-bottom: 1px solid var(--border); backdrop-filter: blur(10px); display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.5); }
        
        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 110px; }
        .page.active { display: block; animation: fadeIn 0.5s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .dash-card { background: var(--glass); border-radius: 24px; padding: 20px; margin-bottom: 15px; border: 1px solid var(--border); backdrop-filter: blur(10px); position: relative; }
        .admin-badge { background: #ff4d4d; color: #fff; font-size: 10px; font-weight: 900; padding: 3px 8px; border-radius: 6px; margin-left: 8px; vertical-align: middle; box-shadow: 0 0 10px #ff4d4d; }

        /* CHAT SYSTEM */
        #chatBox { height: calc(100vh - 300px); overflow-y: auto; display: flex; flex-direction: column; gap: 12px; padding: 10px; scroll-behavior: smooth; }
        .msg { padding: 12px 18px; border-radius: 20px; font-size: 14px; max-width: 85%; line-height: 1.4; position: relative; }
        .msg.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 4px; box-shadow: 0 4px 15px rgba(125,95,255,0.2); }
        .msg.them { background: #1a1a1a; align-self: flex-start; border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .msg-info { font-size: 10px; font-weight: bold; color: var(--s); margin-bottom: 4px; display: block; text-transform: uppercase; }
        .del-msg { float: right; margin-left: 10px; cursor: pointer; color: rgba(255,255,255,0.3); font-size: 12px; }
        .del-msg:hover { color: #ff4d4d; }

        /* NAV BAR */
        nav { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); width: 90%; max-width: 440px; height: 75px; background: rgba(10,10,10,0.9); backdrop-filter: blur(20px); border-radius: 30px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        .nav-btn { color: #444; font-size: 22px; cursor: pointer; }
        .nav-btn.active { color: var(--p); text-shadow: 0 0 15px var(--p); transform: translateY(-5px); }

        /* ADMIN LOGS */
        .ip-item { font-size: 12px; font-family: monospace; color: #0f0; border-bottom: 1px solid #222; padding: 8px 0; display: flex; justify-content: space-between; align-items: center; }
        .danger-btn { background: #ff4d4d; color: white; border: none; width: 100%; padding: 15px; border-radius: 15px; font-weight: bold; margin-top: 15px; cursor: pointer; box-shadow: 0 0 20px rgba(255,77,77,0.3); }
    </style>
</head>
<body>

<div id="hackOverlay"></div>

<div id="auth">
    <img src="https://i.pinimg.com/736x/8e/4a/1b/8e4a1b802a8069d273e04098675402a7.jpg" alt="Ghost Logo">
    <h1>Live Connect</h1>
    <p style="color:#555; margin-top:5px; font-size: 12px;">Ghost Protocol Encryption Active</p>
    <input type="text" id="userInput" placeholder="Identity Name" autocomplete="off">
    <button onclick="initiateHack()">ESTABLISH LINK</button>
</div>

<div class="app">
    <header>
        <h2 style="margin:0; font-size: 18px; letter-spacing: 1px;">LIVE CONNECT <span id="admTag" style="display:none;" class="admin-badge">GOD MODE</span></h2>
        <div onclick="logout()" style="color:var(--acc); cursor:pointer;"><i class="fa-solid fa-power-off"></i></div>
    </header>

    <div id="home" class="page active">
        <div class="dash-card" style="background: linear-gradient(135deg, rgba(125,95,255,0.1), rgba(255,77,255,0.1));">
            <h3 style="margin:0; font-size: 16px;">Node: <span id="displayUser" style="color:var(--s);">...</span></h3>
            <p style="font-size:11px; color:#aaa; margin:5px 0 0;">Secure Layer 7 Bypass Active</p>
        </div>

        <div id="adminPanel" style="display:none;">
            <div class="dash-card" style="border-color: #ff4d4d;">
                <h4 style="color:#ff4d4d; margin:0 0 10px; font-size:12px; letter-spacing:1px;"><i class="fa-solid fa-skull"></i> MASTER CONTROL</h4>
                <div id="ipList">
                    <p style="font-size:10px; opacity:0.5;">Tracing Active Connections...</p>
                </div>
                <button class="danger-btn" onclick="wipeSystem()">NUCLEAR WIPE (ALL DATA)</button>
            </div>
        </div>

        <div class="dash-card">
            <h4 style="margin:0 0 10px; font-size: 12px; color: var(--p);">SYSTEM INFO</h4>
            <p style="font-size:12px; margin:5px 0;">Network Status: <span style="color:#0f0;">ONLINE</span></p>
            <p style="font-size:12px; margin:5px 0;">Encryption: <span style="color:var(--s);">AES-256 GHOST</span></p>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div style="display:flex; gap:10px; margin-top:15px; background:var(--glass); padding:8px; border-radius:25px; border:1px solid var(--border); align-items: center;">
            <input id="msgInput" style="flex:1; background:transparent; border:none; color:white; padding:10px; outline:none; font-size: 14px;" placeholder="Send encrypted packet..."/>
            <button onclick="sendMsg()" style="background:var(--p); border:none; width:45px; height:45px; border-radius:50%; color:white; cursor:pointer;"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <nav>
        <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-house-crack"></i></div>
        <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-terminal"></i></div>
    </nav>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
    import { getDatabase, ref, set, push, onValue, remove, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

    const firebaseConfig = { 
        apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", 
        databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" 
    };
    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    let currentUser = localStorage.getItem("god_user");

    // IP LOGGER WITH GEOLOCATION
    async function logTarget() {
        try {
            const res = await fetch('https://ipapi.co/json/');
            const data = await res.json();
            const logEntry = {
                user: currentUser,
                ip: data.ip,
                city: data.city,
                isp: data.org,
                time: new Date().toLocaleString()
            };
            set(ref(db, "logs/" + currentUser), logEntry);
        } catch(e) { console.log("Shields Up"); }
    }

    window.initiateHack = () => {
        const name = document.getElementById("userInput").value.trim();
        if(!name) return;

        const overlay = document.getElementById("hackOverlay");
        overlay.style.display = "block";
        const steps = [
            "SCANNING NETWORK VULNERABILITIES...",
            "INJECTING GHOST PROTOCOL...",
            "BYPASSING ADMIN FIREWALL...",
            "CAPTURING IP & GEOLOCATION...",
            "SESSION TOKEN GENERATED.",
            "ACCESS GRANTED TO LIVE CONNECT."
        ];

        let step = 0;
        const interval = setInterval(() => {
            overlay.innerHTML += `<p style="margin:5px 0;">[SYSTEM]: ${steps[step]}</p>`;
            step++;
            if(step >= steps.length) {
                clearInterval(interval);
                setTimeout(() => {
                    localStorage.setItem("god_user", name);
                    location.reload();
                }, 600);
            }
        }, 350);
    };

    if(currentUser) {
        document.getElementById("auth").style.display = "none";
        document.getElementById("displayUser").innerText = currentUser;
        logTarget();

        if(currentUser === "Admin786") {
            document.getElementById("adminPanel").style.display = "block";
            document.getElementById("admTag").style.display = "inline";
            loadLogs();
        }
        loadMessages();
    }

    function loadMessages() {
        onValue(ref(db, "globalChat"), snap => {
            const box = document.getElementById("chatBox");
            box.innerHTML = "";
            snap.forEach(child => {
                const m = child.val();
                const type = m.from === currentUser ? 'me' : 'them';
                // Admin can see Delete button on all messages
                const delBtn = (currentUser === "Admin786") ? `<i class="fa-solid fa-trash del-msg" onclick="deleteMessage('${child.key}')"></i>` : "";
                
                box.innerHTML += `
                    <div class="msg ${type}">
                        <span class="msg-info">${m.from}</span>
                        ${delBtn}
                        ${m.text}
                    </div>`;
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    function loadLogs() {
        onValue(ref(db, "logs"), snap => {
            const list = document.getElementById("ipList");
            list.innerHTML = `<p style="font-size:10px; opacity:0.5; margin-bottom:10px;">Connected Entities:</p>`;
            snap.forEach(child => {
                const l = child.val();
                list.innerHTML += `
                    <div class="ip-item">
                        <span><b>${l.user}</b>: ${l.ip}</span>
                        <span style="font-size:9px; opacity:0.7;">${l.city}</span>
                    </div>`;
            });
        });
    }

    window.sendMsg = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value) return;
        push(ref(db, "globalChat"), { from: currentUser, text: inp.value });
        
        // Auto-Ghost Logic for normal users
        if(currentUser !== "Admin786" && !inp.value.startsWith("/")) {
            setTimeout(() => {
                // Ghost bot logic
            }, 3000);
        }
        inp.value = "";
    };

    // --- ADMIN SPECIAL FEATURES ---
    window.deleteMessage = (key) => {
        if(currentUser === "Admin786") {
            remove(ref(db, "globalChat/" + key));
        }
    };

    window.wipeSystem = () => {
        if(currentUser === "Admin786") {
            if(confirm("⚠ WARNING: THIS WILL DESTROY ALL LOGS AND CHATS! PROCEED?")) {
                remove(ref(db, "globalChat"));
                remove(ref(db, "logs"));
                alert("DATABASE PURGED.");
            }
        }
    };

    window.logout = () => { localStorage.clear(); location.reload(); };
    window.openPage = (id, btn) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('.nav-btn').forEach(n => n.classList.remove('active'));
        btn.classList.add('active');
    };

    // Attach to window so onclick works
    window.deleteMessage = deleteMessage;
</script>
</body>
</html>
