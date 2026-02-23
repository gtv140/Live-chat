<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Omega</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;500;800&display=swap');
        
        :root {
            --primary: #8b5cf6; --secondary: #ec4899;
            --bg: #030303; --surface: #0f1015; --border: #1f212a;
            --text: #ffffff; --dim: #94a3b8;
        }

        * { box-sizing: border-box; font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { margin: 0; background: var(--bg); color: var(--text); height: 100dvh; display: flex; flex-direction: column; overflow: hidden; }

        /* Animated Background */
        body::before {
            content: ''; position: absolute; width: 300px; height: 300px; background: var(--primary);
            filter: blur(150px); opacity: 0.15; top: -50px; left: -50px; z-index: -1;
        }

        /* Top Navigation */
        header { 
            padding: 20px; display: flex; justify-content: space-between; align-items: center; 
            background: rgba(15, 16, 21, 0.8); backdrop-filter: blur(20px); border-bottom: 1px solid var(--border);
        }
        header h1 { margin: 0; font-size: 22px; font-weight: 800; background: linear-gradient(to right, #a78bfa, #f472b6); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* Main Viewport */
        .viewport { flex: 1; position: relative; width: 100%; max-width: 500px; margin: auto; overflow: hidden; display: flex; flex-direction: column; }
        .page { display: none; flex: 1; padding: 20px; overflow-y: auto; height: 100%; padding-bottom: 120px; }
        .page.active { display: block; animation: slideIn 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        @keyframes slideIn { from { transform: translateX(20px); opacity: 0; } to { transform: translateX(0); opacity: 1; } }

        /* Cards & Components */
        .glass-card { background: var(--surface); border-radius: 28px; padding: 25px; border: 1px solid var(--border); margin-bottom: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        .status-pill { display: flex; align-items: center; justify-content: space-between; background: #1a1b23; padding: 15px 20px; border-radius: 20px; margin-top: 10px; border: 1px solid var(--border); }
        .status-pill b { color: var(--primary); font-size: 18px; }

        /* Chat System */
        #chatBox { display: flex; flex-direction: column; gap: 12px; }
        .bubble { max-width: 85%; padding: 14px 18px; border-radius: 22px; font-size: 14px; line-height: 1.4; position: relative; border: 1px solid var(--border); }
        .bubble.me { background: var(--primary); align-self: flex-end; border-bottom-right-radius: 4px; border: none; box-shadow: 0 8px 20px rgba(139, 92, 246, 0.3); }
        .bubble.them { background: var(--surface); align-self: flex-start; border-bottom-left-radius: 4px; }
        .bubble small { display: block; font-size: 10px; margin-bottom: 5px; opacity: 0.6; font-weight: bold; }
        .bubble .del-btn { position: absolute; top: -10px; right: -10px; background: red; color: white; width: 20px; height: 20px; border-radius: 50%; display: none; place-items: center; font-size: 10px; cursor: pointer; }
        .bubble:hover .del-btn { display: grid; }

        /* Floating Controls */
        .input-bar { 
            position: absolute; bottom: 25px; left: 15px; right: 15px; background: rgba(26, 27, 35, 0.95);
            backdrop-filter: blur(15px); padding: 10px; border-radius: 35px; border: 1px solid var(--border);
            display: flex; align-items: center; gap: 10px; box-shadow: 0 15px 40px rgba(0,0,0,0.6);
        }
        .input-bar input { flex: 1; background: transparent; border: none; color: white; padding: 10px; outline: none; font-size: 15px; }
        .action-btn { width: 45px; height: 45px; border-radius: 50%; display: grid; place-items: center; cursor: pointer; transition: 0.3s; }
        .btn-send { background: linear-gradient(135deg, var(--primary), var(--secondary)); color: white; }

        /* Bottom Nav */
        nav { 
            position: fixed; bottom: 0; width: 100%; max-width: 500px; background: #000;
            padding: 15px 0; display: flex; justify-content: space-around; border-top: 1px solid var(--border);
        }
        nav i { color: var(--dim); font-size: 22px; cursor: pointer; transition: 0.3s; }
        nav i.active { color: var(--primary); transform: translateY(-5px); }

        /* Admin HUD */
        .admin-hud { border: 2px solid var(--secondary); padding: 20px; border-radius: 30px; background: rgba(236, 72, 153, 0.05); margin-top: 25px; }
        .ban-row { display: flex; justify-content: space-between; padding: 12px; background: #000; border-radius: 15px; margin-bottom: 8px; }
    </style>
</head>
<body>

<header>
    <h1>Ω MEGA<span>.</span></h1>
    <div style="display:flex; gap:15px;">
        <i class="fa-solid fa-satellite-dish" style="color:var(--primary)"></i>
        <i class="fa-solid fa-sign-out-alt" onclick="logout()"></i>
    </div>
</header>

<div class="viewport">
    <div id="home" class="page active">
        <div class="glass-card">
            <h2 style="margin:0; font-size:26px;">Operator: <span id="uName" style="color:var(--primary)">...</span></h2>
            <p style="color:var(--dim); margin-top:5px;">System Security: Level 10 Encrypted</p>
        </div>
        
        <div class="status-pill"><span>Online Nodes</span><b id="nodeCount">0</b></div>
        <div class="status-pill"><span>Active Hubs</span><b id="hubCount">0</b></div>

        <div id="adminPanel" style="display:none;" class="admin-hud">
            <h3 style="margin:0; color:var(--secondary); font-size:16px;"><i class="fa-solid fa-shield"></i> ROOT ACCESS GRANTED</h3>
            <input id="bcInput" placeholder="Send Global Broadcast..." style="width:100%; background:#000; border:1px solid #333; padding:15px; border-radius:15px; color:white; margin:15px 0; outline:none;">
            <button onclick="sendBroadcast()" style="width:100%; background:var(--secondary); color:white; border:none; padding:12px; border-radius:15px; font-weight:800; cursor:pointer;">FIRE GLOBAL SIGNAL</button>
            <div id="adminUserList" style="margin-top:20px;"></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatHeader" style="text-align:center; color:var(--dim); font-size:11px; margin-bottom:20px; letter-spacing:3px;">IDLE MODE</div>
        <div id="chatBox"></div>
        <div class="input-bar">
            <label for="imgFile" class="action-btn"><i class="fa-solid fa-paperclip"></i></label>
            <input type="file" id="imgFile" hidden accept="image/*" onchange="uploadImage(this)">
            <input id="msgInput" placeholder="Enter secure message...">
            <div class="action-btn btn-send" onclick="sendMessage()"><i class="fa-solid fa-paper-plane"></i></div>
        </div>
    </div>

    <div id="users" class="page">
        <h4 style="color:var(--dim); font-size:11px; letter-spacing:2px; margin-bottom:15px;">AUTHORIZED PERSONNEL</h4>
        <div id="nodeList"></div>
        <h4 style="color:var(--dim); font-size:11px; letter-spacing:2px; margin-top:30px; margin-bottom:15px;">ACTIVE GROUPS</h4>
        <div id="hubList"></div>
        <div class="input-bar" style="position:relative; bottom:0; left:0; right:0;">
            <input id="hubInput" placeholder="Create new group identifier...">
            <div class="action-btn btn-send" onclick="createHub()"><i class="fa-solid fa-plus"></i></div>
        </div>
    </div>

    <nav>
        <i class="fa-solid fa-ghost active" onclick="navigate('home',this)"></i>
        <i class="fa-solid fa-comments" onclick="navigate('chat',this)"></i>
        <i class="fa-solid fa-user-astronaut" onclick="navigate('users',this)"></i>
    </nav>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
    import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

    const firebaseConfig = {
        apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
        databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
        projectId: "live-chat-b810c",
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    // --- SECURE ADMIN KEY ---
    const ADMIN_PASSKEY = "X_ADMIN_786"; // Is naam se login karne par Admin Panel khulega

    let me = localStorage.getItem("u_name") || prompt("Identity Verification Required (Username):");
    if(!me) location.reload();
    localStorage.setItem("u_name", me);
    document.getElementById("uName").textContent = me;

    // Admin Auth
    const isRoot = me === ADMIN_PASSKEY;
    if(isRoot) document.getElementById("adminPanel").style.display = "block";

    // Online Handling
    const myRef = ref(db, "users/" + me);
    set(myRef, {name: me, online: true});
    onDisconnect(myRef).update({online: false});

    window.navigate = (id, el) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
        el.classList.add('active');
    };

    let activeTarget = "";
    let hubMode = false;

    // Node & Admin Listeners
    onValue(ref(db, "users"), snap => {
        let count = 0; nodeList.innerHTML = "";
        if(isRoot) adminUserList.innerHTML = "<h4>Ban Protocols:</h4>";
        snap.forEach(u => {
            if(u.val().online) {
                count++;
                if(u.key !== me) {
                    nodeList.innerHTML += `<div class="glass-card" onclick="connect('${u.key}', false)" style="padding:18px; margin-bottom:12px;">🛰️ ${u.key}</div>`;
                }
            }
            if(isRoot && u.key !== me) {
                adminUserList.innerHTML += `<div class="ban-row"><span>${u.key}</span><i class="fa-solid fa-user-slash" style="color:red" onclick="banUser('${u.key}')"></i></div>`;
            }
        });
        nodeCount.textContent = count;
    });

    window.connect = (t, h) => {
        activeTarget = t; hubMode = h;
        chatHeader.textContent = "SECURE LINK: " + t;
        navigate('chat', document.querySelectorAll('nav i')[1]);
        syncMessages();
    };

    function syncMessages() {
        const path = (hubMode ? "hubs/" : "direct/") + [me, activeTarget].sort().join("_");
        onValue(ref(db, path), snap => {
            chatBox.innerHTML = "";
            snap.forEach(m => {
                const d = m.val();
                const div = document.createElement("div");
                div.className = `bubble ${d.f === me ? 'me' : 'them'}`;
                div.innerHTML = `
                    <div class="del-btn" onclick="delMsg('${path}','${m.key}')">×</div>
                    <small>${d.f}</small>
                    ${d.t === 'img' ? `<img src="${d.v}" style="width:100%; border-radius:12px;">` : d.v}
                `;
                chatBox.appendChild(div);
            });
            chatBox.scrollTop = chatBox.scrollHeight;
        });
    }

    window.sendMessage = () => {
        const v = msgInput.value;
        if(!v || !activeTarget) return;
        const path = (hubMode ? "hubs/" : "direct/") + [me, activeTarget].sort().join("_");
        push(ref(db, path), {f: me, v: v, t: 'txt'});
        msgInput.value = "";
    };

    window.uploadImage = (e) => {
        const r = new FileReader();
        r.onload = (f) => {
            const path = (hubMode ? "hubs/" : "direct/") + [me, activeTarget].sort().join("_");
            push(ref(db, path), {f: me, v: f.target.result, t: 'img'});
        };
        r.readAsDataURL(e.files[0]);
    };

    window.sendBroadcast = () => {
        const m = bcInput.value;
        if(m) set(ref(db, "global_alert"), {m: m, ts: Date.now()});
        bcInput.value = "";
    };
    onValue(ref(db, "global_alert"), s => { if(s.exists()) alert("📡 GLOBAL SIGNAL:\n" + s.val().m); });

    window.createHub = () => {
        const h = hubInput.value;
        if(h) set(ref(db, "activeHubs/" + h), {creator: me});
        hubInput.value = "";
    };

    onValue(ref(db, "activeHubs"), snap => {
        hubList.innerHTML = ""; hubCount.textContent = snap.size || 0;
        snap.forEach(g => {
            hubList.innerHTML += `<div class="glass-card" onclick="connect('${g.key}', true)" style="padding:18px;">💠 HUB: ${g.key}</div>`;
        });
    });

    window.banUser = (u) => { if(confirm("Terminate Access for " + u + "?")) remove(ref(db, "users/" + u)); };
    window.delMsg = (p, k) => { if(confirm("Erase data point?")) remove(ref(db, p + "/" + k)); };
    window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
