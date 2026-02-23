<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Omega Fix</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;500;800&display=swap');
        :root { --primary: #8b5cf6; --bg: #030303; --surface: #0f1015; --border: #1f212a; --text: #ffffff; }
        
        * { box-sizing: border-box; font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; }
        
        /* Fixed Height Fix for Mobile */
        html, body { height: 100%; margin: 0; background: var(--bg); color: var(--text); overflow: hidden; }
        
        body { display: flex; flex-direction: column; }

        header { padding: 15px 20px; background: var(--surface); border-bottom: 1px solid var(--border); flex-shrink: 0; }
        header h1 { margin: 0; font-size: 20px; font-weight: 800; background: linear-gradient(to right, #a78bfa, #f472b6); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        .viewport { flex: 1; position: relative; overflow: hidden; display: flex; flex-direction: column; width: 100%; max-width: 500px; margin: auto; }

        /* Scrollable Area Fix */
        .page { display: none; flex: 1; flex-direction: column; overflow-y: auto; padding: 15px; -webkit-overflow-scrolling: touch; }
        .page.active { display: flex; }

        #chatBox { flex: 1; display: flex; flex-direction: column; gap: 12px; padding-bottom: 80px; }
        
        .bubble { max-width: 85%; padding: 12px 16px; border-radius: 18px; font-size: 14px; position: relative; border: 1px solid var(--border); word-wrap: break-word; }
        .bubble.me { background: var(--primary); align-self: flex-end; border-bottom-right-radius: 4px; border: none; }
        .bubble.them { background: var(--surface); align-self: flex-start; border-bottom-left-radius: 4px; }

        /* Input Bar - Always Visible Fix */
        .input-bar { 
            position: absolute; bottom: 10px; left: 10px; right: 10px; 
            background: #1a1b23; padding: 8px; border-radius: 30px; 
            border: 1px solid var(--border); display: flex; align-items: center; gap: 8px;
            box-shadow: 0 -5px 20px rgba(0,0,0,0.4); z-index: 10;
        }
        .input-bar input { flex: 1; background: transparent; border: none; color: white; padding: 10px; outline: none; font-size: 16px; }
        .action-btn { width: 42px; height: 42px; border-radius: 50%; display: grid; place-items: center; cursor: pointer; background: var(--primary); color: white; border: none; }

        nav { background: #000; padding: 12px 0; display: flex; justify-content: space-around; border-top: 1px solid var(--border); flex-shrink: 0; }
        nav i { color: #555; font-size: 20px; cursor: pointer; }
        nav i.active { color: var(--primary); }

        /* Admin UI Fix */
        .admin-hud { border: 1px solid var(--primary); padding: 15px; border-radius: 20px; background: rgba(139, 92, 246, 0.05); margin-top: 20px; }
    </style>
</head>
<body>

<header>
    <h1>Ω MEGA<span>.</span></h1>
</header>

<div class="viewport">
    <div id="home" class="page active">
        <div style="background:var(--surface); padding:20px; border-radius:20px; border:1px solid var(--border);">
            <h2 style="margin:0;">Node: <span id="uName">...</span></h2>
        </div>
        <div id="adminPanel" style="display:none;" class="admin-hud">
            <h4 style="color:var(--primary); margin:0;">Admin Console</h4>
            <input id="bcInput" placeholder="System Alert..." style="width:100%; background:#000; border:1px solid #333; padding:12px; border-radius:12px; color:white; margin:10px 0; outline:none;">
            <button onclick="sendBC()" style="width:100%; background:var(--primary); color:white; border:none; padding:10px; border-radius:10px; font-weight:bold;">BROADCAST</button>
            <div id="adminUserList" style="margin-top:10px; max-height: 200px; overflow-y: auto;"></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatBox"></div>
        <div class="input-bar">
            <label for="imgF" style="padding:0 10px; color:var(--primary); cursor:pointer;"><i class="fa-solid fa-paperclip"></i></label>
            <input type="file" id="imgF" hidden accept="image/*" onchange="upImg(this)">
            <input id="msgInp" placeholder="Type message...">
            <button class="action-btn" onclick="sendM()"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="users" class="page">
        <div id="nodeList"></div>
    </div>
</div>

<nav>
    <i class="fa-solid fa-house active" onclick="nav('home',this)"></i>
    <i class="fa-solid fa-message" onclick="nav('chat',this)"></i>
    <i class="fa-solid fa-users" onclick="nav('users',this)"></i>
</nav>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
    import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

    const cfg = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com", projectId: "live-chat-b810c" };
    const app = initializeApp(cfg);
    const db = getDatabase(app);

    const ADMIN_KEY = "X_ADMIN_786";
    let me = localStorage.getItem("un") || prompt("Username:");
    if(!me) location.reload();
    localStorage.setItem("un", me);
    document.getElementById("uName").textContent = me;

    if(me === ADMIN_KEY) document.getElementById("adminPanel").style.display = "block";

    const myS = ref(db, "users/" + me);
    set(myS, {name: me, online: true});
    onDisconnect(myS).update({online: false});

    window.nav = (id, el) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
        el.classList.add('active');
    };

    let tgt = "";
    onValue(ref(db, "users"), snap => {
        nodeList.innerHTML = "";
        if(me === ADMIN_KEY) adminUserList.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online && u.key !== me) {
                nodeList.innerHTML += `<div style="background:var(--surface); padding:15px; border-radius:15px; margin-bottom:10px; border:1px solid var(--border);" onclick="start('${u.key}')">👤 ${u.key}</div>`;
            }
            if(me === ADMIN_KEY && u.key !== me) {
                adminUserList.innerHTML += `<div style="display:flex; justify-content:space-between; margin-bottom:5px;"><span>${u.key}</span><button onclick="ban('${u.key}')" style="color:red; background:none; border:none;">BAN</button></div>`;
            }
        });
    });

    window.start = (t) => {
        tgt = t;
        nav('chat', document.querySelectorAll('nav i')[1]);
        const path = "direct/" + [me, tgt].sort().join("_");
        onValue(ref(db, path), snap => {
            chatBox.innerHTML = "";
            snap.forEach(m => {
                const d = m.val();
                const div = document.createElement("div");
                div.className = `bubble ${d.f === me ? 'me' : 'them'}`;
                div.innerHTML = d.t === 'img' ? `<img src="${d.v}" style="width:100%; border-radius:10px;">` : d.v;
                chatBox.appendChild(div);
            });
            chatBox.scrollTop = chatBox.scrollHeight;
        });
    };

    window.sendM = () => {
        const v = msgInp.value;
        if(!v || !tgt) return;
        push(ref(db, "direct/" + [me, tgt].sort().join("_")), {f: me, v: v, t: 'txt'});
        msgInp.value = "";
    };

    window.upImg = (e) => {
        const r = new FileReader();
        r.onload = (f) => push(ref(db, "direct/" + [me, tgt].sort().join("_")), {f: me, v: f.target.result, t: 'img'});
        r.readAsDataURL(e.files[0]);
    };

    window.sendBC = () => {
        const m = bcInput.value;
        if(m) set(ref(db, "alert"), {m: m, ts: Date.now()});
        bcInput.value = "";
    };
    onValue(ref(db, "alert"), s => { if(s.exists()) alert("ADMIN: " + s.val().m); });

    window.ban = (u) => { if(confirm("Ban " + u + "?")) remove(ref(db, "users/" + u)); };
</script>
</body>
</html>
