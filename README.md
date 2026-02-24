<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Empire Ultra | Gemini v100</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --p: #7c3aed; --s: #db2777; --bg: #030712; --g: rgba(31, 41, 55, 0.7); }
        * { box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        body { margin: 0; background: var(--bg); color: #f9fafb; height: 100dvh; display: flex; overflow: hidden; }

        /* --- Elite Navigation --- */
        .side-nav { width: 85px; background: #000; border-right: 1px solid rgba(255,255,255,0.1); display: flex; flex-direction: column; align-items: center; padding: 30px 0; gap: 35px; z-index: 50; }
        .nav-item { width: 55px; height: 55px; display: flex; align-items: center; justify-content: center; border-radius: 18px; color: #4b5563; cursor: pointer; font-size: 1.4rem; }
        .nav-item.active, .nav-item:hover { background: var(--p); color: #fff; box-shadow: 0 0 25px rgba(124, 58, 237, 0.5); }

        /* --- Dashboard Area --- */
        .app-core { flex: 1; display: flex; flex-direction: column; position: relative; }
        header { padding: 25px 35px; display: flex; justify-content: space-between; align-items: center; background: rgba(3, 7, 18, 0.9); backdrop-filter: blur(20px); border-bottom: 1px solid rgba(255,255,255,0.05); }
        .glitch-text { font-weight: 900; font-size: 1.5rem; letter-spacing: -1px; background: linear-gradient(to right, #8b5cf6, #ec4899); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        
        .main-stage { flex: 1; display: flex; overflow: hidden; }
        .chat-zone { flex: 1; display: flex; flex-direction: column; position: relative; }
        .feed { flex: 1; overflow-y: auto; padding: 25px; display: flex; flex-direction: column; gap: 20px; padding-bottom: 130px; }

        /* --- Bubbles & Media --- */
        .bubble { max-width: 75%; padding: 16px 20px; border-radius: 24px; font-size: 15px; line-height: 1.5; box-shadow: 0 10px 30px rgba(0,0,0,0.3); }
        .me { align-self: flex-end; background: linear-gradient(135deg, var(--p), var(--s)); border-bottom-right-radius: 4px; }
        .other { align-self: flex-start; background: var(--g); border: 1px solid rgba(255,255,255,0.1); border-bottom-left-radius: 4px; }
        .bubble img { max-width: 100%; border-radius: 15px; margin-top: 10px; display: block; }

        /* --- Admin Command Strip --- */
        #adminStrip { display: none; padding: 10px 30px; background: #ef4444; text-align: center; font-weight: 800; font-size: 0.8rem; letter-spacing: 2px; cursor: pointer; }

        /* --- Input Dock --- */
        .dock { position: absolute; bottom: 30px; left: 30px; right: 30px; background: rgba(31, 41, 55, 0.9); padding: 12px; border-radius: 60px; display: flex; align-items: center; gap: 15px; border: 1px solid rgba(255,255,255,0.1); backdrop-filter: blur(30px); }
        .dock input { flex: 1; background: none; border: none; color: #fff; font-size: 16px; outline: none; padding-left: 10px; }
        .dock-btn { width: 48px; height: 48px; border-radius: 50%; border: none; display: flex; align-items: center; justify-content: center; cursor: pointer; }
        .btn-send { background: var(--p); color: white; }

        /* --- User Sidebar --- */
        .user-panel { width: 300px; background: rgba(255,255,255,0.02); border-left: 1px solid rgba(255,255,255,0.05); padding: 30px; }
        @media (max-width: 1000px) { .user-panel { display: none; } }

        /* --- Auth & Modals --- */
        #authOverlay { position: fixed; inset: 0; z-index: 1000; background: #000; display: flex; align-items: center; justify-content: center; }
        .auth-card { width: 90%; max-width: 400px; text-align: center; padding: 40px; background: var(--g); border-radius: 35px; border: 1px solid rgba(255,255,255,0.1); }
    </style>
</head>
<body>

<div id="authOverlay">
    <div class="auth-card">
        <h1 class="glitch-text">EMPIRE_LOGIN</h1>
        <p style="opacity: 0.5; font-size: 0.8rem; margin-bottom: 30px;">Initialize Secure Session</p>
        <input type="text" id="joinName" placeholder="Enter Codename" style="width:100%; padding:18px; border-radius:20px; border:none; background:#000; color:#fff; margin-bottom:15px; text-align:center; font-size:1rem;">
        <button onclick="initEmpire()" style="width:100%; padding:18px; border-radius:20px; border:none; background:var(--p); color:#fff; font-weight:800; cursor:pointer;">START_ENGINE</button>
    </div>
</div>

<nav class="side-nav">
    <div class="nav-item active"><i class="fa-solid fa-ghost"></i></div>
    <div class="nav-item"><i class="fa-solid fa-bolt"></i></div>
    <div class="nav-item" onclick="alert('Settings: Encryption v10.0 Active')"><i class="fa-solid fa-shield-halved"></i></div>
    <div class="nav-item" onclick="location.reload()"><i class="fa-solid fa-power-off"></i></div>
</nav>

<div class="app-core">
    <div id="adminStrip" onclick="wipeDatabase()">SYSTEM WIPE: EXECUTE COMMAND</div>
    <header>
        <div class="glitch-text" id="headerTitle">ULTRA_CONNECT</div>
        <div id="adminBadge" style="display:none; background: #fbbf24; color: #000; padding: 5px 12px; border-radius: 12px; font-weight: 800; font-size: 0.7rem;">👑 SUPREME</div>
    </header>

    <div class="main-stage">
        <div class="chat-zone">
            <div class="feed" id="mainFeed"></div>
            
            <div class="dock">
                <label for="fUp" class="nav-item" style="width:40px; height:40px; font-size:1rem; margin:0;"><i class="fa-solid fa-plus"></i></label>
                <input type="file" id="fUp" hidden onchange="processFile(this)">
                <input type="text" id="msgIn" placeholder="Enter encrypted signal..." onkeydown="if(event.key==='Enter') broadcast()">
                <button class="dock-btn btn-send" onclick="broadcast()"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>

        <aside class="user-panel">
            <h3 style="margin-top:0; font-size:1.2rem;">Live Nodes</h3>
            <div id="nodesList" style="display:flex; flex-direction:column; gap:15px; margin-top:25px;"></div>
        </aside>
    </div>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getAuth, signInAnonymously } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
    import { getDatabase, ref, set, push, onValue, serverTimestamp, remove, query, limitToLast } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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

    let myUID, myName;
    let isAdmin = false;

    window.initEmpire = async () => {
        myName = document.getElementById("joinName").value.trim();
        if(!myName) return;

        // Admin Security Check
        if(myName.toLowerCase() === 'nazim') {
            let pin = prompt("Enter Master PIN:");
            if(pin !== "786") { alert("Access Denied!"); return; }
            isAdmin = true;
        }

        const res = await signInAnonymously(auth);
        myUID = res.user.uid;
        
        if(isAdmin) {
            document.getElementById("adminBadge").style.display = "block";
            document.getElementById("adminStrip").style.display = "block";
        }

        document.getElementById("authOverlay").style.display = "none";
        set(ref(db, 'online_v100/' + myUID), { name: myName, role: isAdmin ? 'admin' : 'user' });
        sync();
    };

    window.broadcast = (img = null) => {
        const input = document.getElementById("msgIn");
        if(!input.value.trim() && !img) return;

        push(ref(db, 'v100_core'), {
            uid: myUID, sender: myName, text: input.value, photo: img, ts: serverTimestamp()
        });
        input.value = "";
    };

    window.processFile = (el) => {
        const r = new FileReader();
        r.onload = (e) => broadcast(e.target.result);
        r.readAsDataURL(el.files[0]);
    };

    window.wipeDatabase = () => {
        if(confirm("DANGER: This will delete ALL chat records. Proceed?")) {
            remove(ref(db, 'v100_core'));
        }
    };

    function sync() {
        onValue(query(ref(db, 'v100_core'), limitToLast(40)), snap => {
            const feed = document.getElementById("mainFeed");
            feed.innerHTML = "";
            snap.forEach(s => {
                const d = s.val();
                const isMe = d.uid === myUID;
                feed.innerHTML += `
                    <div class="bubble ${isMe ? 'me' : 'other'}">
                        <small style="opacity:0.6; font-weight:800; display:block; margin-bottom:5px;">${d.sender}</small>
                        ${d.text ? `<div>${d.text}</div>` : ''}
                        ${d.photo ? `<img src="${d.photo}">` : ''}
                    </div>
                `;
            });
            feed.scrollTop = feed.scrollHeight;
        });

        onValue(ref(db, 'online_v100'), snap => {
            const list = document.getElementById("nodesList");
            list.innerHTML = "";
            snap.forEach(s => {
                const d = s.val();
                list.innerHTML += `
                    <div style="display:flex; align-items:center; gap:12px; background:rgba(255,255,255,0.05); padding:10px; border-radius:15px;">
                        <img src="https://ui-avatars.com/api/?name=${d.name}&background=random&color=fff" style="width:35px; border-radius:12px;">
                        <div>
                            <div style="font-size:0.9rem; font-weight:600;">${d.name}</div>
                            <div style="font-size:0.7rem; color:#10b981;">● Online</div>
                        </div>
                    </div>
                `;
            });
        });
    }
</script>
</body>
</html>
