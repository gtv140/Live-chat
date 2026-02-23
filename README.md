<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Infinity 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        
        :root {
            --accent: #6366f1; --accent-glow: rgba(99, 102, 241, 0.4);
            --bg: #050505; --surface: #121214; --border: #27272a;
            --text: #ffffff; --text-dim: #a1a1aa;
        }

        * { box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { margin: 0; background: var(--bg); color: var(--text); height: 100dvh; display: flex; flex-direction: column; overflow: hidden; }

        header { 
            padding: 20px; display: flex; justify-content: space-between; align-items: center; 
            background: rgba(18, 18, 20, 0.8); backdrop-filter: blur(10px); 
            border-bottom: 1px solid var(--border); z-index: 100;
        }
        header h1 { margin: 0; font-size: 20px; font-weight: 800; letter-spacing: -1px; }
        header span { color: var(--accent); }

        .main-container { flex: 1; position: relative; width: 100%; max-width: 500px; margin: auto; overflow: hidden; display: flex; flex-direction: column; }
        .page { display: none; flex: 1; padding: 20px; overflow-y: auto; height: 100%; padding-bottom: 100px; }
        .page.active { display: block; }

        .premium-card { 
            background: linear-gradient(145deg, #1e1e22, #121214); 
            border-radius: 24px; padding: 25px; border: 1px solid var(--border);
            margin-bottom: 20px; position: relative; overflow: hidden;
        }

        .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .stat-pill { background: var(--surface); padding: 15px; border-radius: 18px; border: 1px solid var(--border); text-align: center; }

        #chatBox { display: flex; flex-direction: column; gap: 15px; padding: 10px 0; }
        .message { max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; animation: pop 0.2s ease; }
        .msg-left { background: var(--surface); align-self: flex-start; border-bottom-left-radius: 4px; border: 1px solid var(--border); }
        .msg-right { background: var(--accent); align-self: flex-end; border-bottom-right-radius: 4px; box-shadow: 0 4px 15px var(--accent-glow); }
        
        .input-wrapper { 
            position: absolute; bottom: 20px; left: 15px; right: 15px; 
            background: rgba(30, 30, 34, 0.9); backdrop-filter: blur(15px);
            padding: 8px; border-radius: 30px; border: 1px solid var(--border);
            display: flex; align-items: center; gap: 10px;
        }
        .input-wrapper input { flex: 1; background: transparent; border: none; color: white; padding: 10px 5px; outline: none; }
        .btn-icon { width: 40px; height: 40px; border-radius: 50%; display: grid; place-items: center; cursor: pointer; }

        .nav-bar { 
            position: fixed; bottom: 0; width: 100%; max-width: 500px;
            background: rgba(10, 10, 12, 0.9); padding: 15px 0; 
            display: flex; justify-content: space-around; border-top: 1px solid var(--border);
        }
        .nav-item { color: var(--text-dim); font-size: 20px; cursor: pointer; }
        .nav-item.active { color: var(--accent); }

        .admin-term { border: 1px solid #ef4444; padding: 15px; border-radius: 20px; background: rgba(239, 68, 68, 0.05); margin-top: 20px; }
    </style>
</head>
<body>

<header>
    <h1>LIVE <span>CONNECT</span></h1>
    <div style="display:flex; gap:15px;">
        <i class="fa-solid fa-bolt" style="color:var(--accent)"></i>
        <i class="fa-solid fa-power-off" onclick="logout()"></i>
    </div>
</header>

<div class="main-container">
    <div id="home" class="page active">
        <div class="premium-card">
            <h2 style="margin:0; font-size:24px;">Node: <span id="uName">...</span></h2>
            <p style="color:var(--text-dim); margin:5px 0 0; font-size:14px;">Status: Encrypted & Verified</p>
        </div>
        
        <div class="stats-grid">
            <div class="stat-pill"><small>Online</small><div id="onCount">0</div></div>
            <div class="stat-pill"><small>Hubs</small><div id="grCount">0</div></div>
        </div>

        <div id="admBox" style="display:none;" class="admin-term">
            <h4 style="margin:0; color:#ef4444;">🛡️ ROOT ACCESS GRANTED</h4>
            <input id="bcData" placeholder="Global Broadcast Signal..." style="width:100%; background:#000; border:1px solid #333; padding:12px; border-radius:12px; color:white; margin-top:10px; outline:none;">
            <button onclick="fireBC()" style="background:#ef4444; color:white; border:none; width:100%; padding:10px; margin-top:10px; border-radius:10px; font-weight:bold;">FIRE BROADCAST</button>
            <div id="banList" style="margin-top:15px;"></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatWith" style="text-align:center; font-size:12px; color:var(--text-dim); margin-bottom:15px;">Idle</div>
        <div id="chatBox"></div>
        <div class="input-wrapper">
            <label for="fInp" class="btn-icon"><i class="fa-solid fa-plus"></i></label>
            <input type="file" id="fInp" hidden accept="image/*" onchange="upImg(this)">
            <input id="mInp" placeholder="Type signal...">
            <div class="btn-icon" style="background:var(--accent)" onclick="pushMsg()"><i class="fa-solid fa-paper-plane"></i></div>
        </div>
    </div>

    <div id="dir" class="page">
        <h4 style="color:var(--text-dim); font-size:11px;">NODES</h4>
        <div id="uList"></div>
        <h4 style="color:var(--text-dim); font-size:11px; margin-top:20px;">HUBS</h4>
        <div id="gList"></div>
    </div>

    <div class="nav-bar">
        <i class="fa-solid fa-house nav-item active" onclick="go('home',this)"></i>
        <i class="fa-solid fa-message nav-item" onclick="go('chat',this)"></i>
        <i class="fa-solid fa-shield-halved nav-item" onclick="go('dir',this)"></i>
    </div>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
    import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

    const cfg = {
        apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
        databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
        projectId: "live-chat-b810c",
    };

    const app = initializeApp(cfg);
    const db = getDatabase(app);

    // --- ADMIN SECURITY ---
    // Muhammad Nazim ki jagah ab ye secret name admin panel kholay ga:
    const SECRET_ADMIN_ID = "Nazim_X_99"; 
    
    let me = localStorage.getItem("u") || prompt("Enter Username:");
    if(!me) location.reload();
    localStorage.setItem("u", me);
    document.getElementById("uName").textContent = me;

    // Admin Check
    if(me === SECRET_ADMIN_ID) {
        document.getElementById("admBox").style.display = "block";
    }

    const sRef = ref(db, "users/" + me);
    set(sRef, {name: me, online: true});
    onDisconnect(sRef).update({online: false});

    window.go = (id, el) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('active'));
        el.classList.add('active');
    };

    let target = "";

    onValue(ref(db, "users"), snap => {
        let count = 0; uList.innerHTML = "";
        if(me === SECRET_ADMIN_ID) banList.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online) {
                count++;
                if(u.key !== me) {
                    uList.innerHTML += `<div class="premium-card" onclick="start('${u.key}')" style="padding:15px; margin-bottom:10px;">👤 ${u.key}</div>`;
                }
            }
            if(me === SECRET_ADMIN_ID && u.key !== me) {
                banList.innerHTML += `<div style="display:flex; justify-content:space-between; margin-bottom:10px;"><span>${u.key}</span><button onclick="ban('${u.key}')" style="color:red; background:none; border:none;">BAN</button></div>`;
            }
        });
        onCount.textContent = count;
    });

    window.start = (t) => {
        target = t;
        chatWith.textContent = "PRIVATE LINK: " + t;
        go('chat', document.querySelectorAll('.nav-item')[1]);
        load();
    };

    function load() {
        const path = "chats/" + [me, target].sort().join("_");
        onValue(ref(db, path), snap => {
            chatBox.innerHTML = "";
            snap.forEach(m => {
                const d = m.val();
                const div = document.createElement("div");
                div.className = `message ${d.f === me ? 'msg-right' : 'msg-left'}`;
                div.innerHTML = d.t === 'img' ? `<img src="${d.v}" style="width:100%; border-radius:10px;">` : d.v;
                chatBox.appendChild(div);
            });
            chatBox.scrollTop = chatBox.scrollHeight;
        });
    }

    window.pushMsg = () => {
        const v = mInp.value;
        if(!v || !target) return;
        const path = "chats/" + [me, target].sort().join("_");
        push(ref(db, path), {f: me, v: v, t: 'txt'});
        mInp.value = "";
    };

    window.fireBC = () => {
        const m = bcData.value;
        if(m) set(ref(db, "broadcast"), {m: m});
        bcData.value = "";
    };

    onValue(ref(db, "broadcast"), s => { if(s.exists()) alert("📢 BROADCAST: " + s.val().m); });

    window.ban = (u) => { if(confirm("Ban " + u + "?")) remove(ref(db, "users/" + u)); };
    window.logout = () => { localStorage.clear(); location.reload(); };
</script>
</body>
</html>
