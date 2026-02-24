<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Empire Ultra | Private & Global</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --p: #8b5cf6; --s: #ec4899; --bg: #030712; --card: rgba(30, 41, 59, 0.7); }
        * { box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { margin: 0; background: var(--bg); color: #f9fafb; height: 100dvh; display: flex; overflow: hidden; }

        /* --- Sidebar --- */
        .sidebar { width: 80px; background: #000; border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; align-items: center; padding: 25px 0; gap: 25px; }
        .nav-btn { width: 50px; height: 50px; border-radius: 15px; display: flex; align-items: center; justify-content: center; color: #4b5563; cursor: pointer; transition: 0.3s; font-size: 1.3rem; }
        .nav-btn.active { background: var(--p); color: white; box-shadow: 0 0 20px rgba(139, 92, 246, 0.4); }

        /* --- Main Content --- */
        .main { flex: 1; display: flex; flex-direction: column; position: relative; }
        header { padding: 15px 25px; display: flex; justify-content: space-between; align-items: center; background: rgba(3, 7, 18, 0.9); backdrop-filter: blur(15px); border-bottom: 1px solid rgba(255,255,255,0.05); }
        .brand { font-weight: 900; font-size: 1.2rem; background: linear-gradient(to right, #a78bfa, #f472b6); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        .content-wrap { flex: 1; display: flex; overflow: hidden; }
        .chat-area { flex: 1; display: flex; flex-direction: column; position: relative; }
        .feed { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; padding-bottom: 100px; }

        /* --- User List (Desktop & Mobile Scroll) --- */
        .user-list { width: 260px; background: rgba(255,255,255,0.02); padding: 20px; border-left: 1px solid rgba(255,255,255,0.05); overflow-y: auto; }
        @media (max-width: 850px) { .user-list { display: none; } }
        .u-item { display: flex; align-items: center; gap: 12px; padding: 10px; border-radius: 12px; cursor: pointer; margin-bottom: 8px; border: 1px solid transparent; }
        .u-item:hover { background: rgba(255,255,255,0.05); border-color: rgba(255,255,255,0.1); }
        .u-avatar { width: 40px; height: 40px; border-radius: 50%; border: 2px solid var(--p); }

        /* --- Bubbles --- */
        .bubble { max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; line-height: 1.5; }
        .me { align-self: flex-end; background: var(--p); color: white; border-bottom-right-radius: 4px; }
        .other { align-self: flex-start; background: var(--card); border-bottom-left-radius: 4px; }
        .bubble img { max-width: 100%; border-radius: 12px; margin-top: 8px; border: 1px solid rgba(255,255,255,0.1); }

        /* --- Input Dock --- */
        .dock { position: absolute; bottom: 20px; left: 15px; right: 15px; background: rgba(30, 41, 59, 0.9); padding: 8px; border-radius: 50px; display: flex; align-items: center; gap: 10px; backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.1); }
        .dock input { flex: 1; background: none; border: none; color: white; outline: none; padding-left: 10px; font-size: 15px; }
        .dock-btn { width: 42px; height: 42px; border-radius: 50%; border: none; display: flex; align-items: center; justify-content: center; cursor: pointer; color: white; }

        /* --- Auth Layer --- */
        #authLayer { position: fixed; inset: 0; z-index: 2000; background: #030712; display: flex; align-items: center; justify-content: center; }
        .auth-card { width: 85%; max-width: 360px; text-align: center; padding: 40px; background: var(--card); border-radius: 35px; border: 1px solid rgba(255,255,255,0.1); }
    </style>
</head>
<body>

<div id="authLayer">
    <div class="auth-card">
        <h1 class="brand" style="font-size: 2rem;">EMPIRE_X</h1>
        <p style="opacity: 0.5; font-size: 0.8rem; margin-bottom: 30px;">Initialize Secure Signal</p>
        <input type="text" id="nodeName" placeholder="Enter Codename" style="width:100%; padding:15px; border-radius:15px; border:none; background:#000; color:#fff; margin-bottom:15px; text-align:center;">
        <button onclick="bootSystem()" style="width:100%; padding:15px; border-radius:15px; border:none; background:var(--p); color:white; font-weight:800; cursor:pointer;">CONNECT</button>
    </div>
</div>

<nav class="sidebar">
    <div class="nav-btn active" id="globalTab" onclick="goGlobal()" title="Global Group"><i class="fa-solid fa-earth-americas"></i></div>
    <div class="nav-btn" onclick="alert('Files Vault: Encrypted')"><i class="fa-solid fa-folder-closed"></i></div>
    <div class="nav-btn" onclick="logout()"><i class="fa-solid fa-power-off"></i></div>
</nav>

<div class="main">
    <div id="adminPanel" onclick="adminWipe()" style="display:none; padding:8px; background:#ef4444; color:white; text-align:center; font-size:10px; font-weight:800; letter-spacing:2px; cursor:pointer;">EMERGENCY DATA WIPE</div>
    <header>
        <div class="brand" id="viewTitle">GLOBAL_CONNECT</div>
        <div id="adminBadge" style="display:none; background:gold; color:black; padding:3px 10px; border-radius:8px; font-weight:900; font-size:10px;">👑 ADMIN</div>
    </header>

    <div class="content-wrap">
        <div class="chat-area">
            <div class="feed" id="chatFeed"></div>
            <div class="dock">
                <label for="imgInp" style="padding:10px; cursor:pointer; opacity:0.6"><i class="fa-solid fa-camera"></i></label>
                <input type="file" id="imgInp" hidden onchange="handleImage(this)">
                <input type="text" id="mInput" placeholder="Type a message..." onkeydown="if(event.key==='Enter') dispatch()">
                <button class="dock-btn" onclick="dispatch()" style="background:var(--p)"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>

        <aside class="user-list">
            <h4 style="margin:0 0 15px 0; opacity:0.5; font-size:12px;">ONLINE ASSETS</h4>
            <div id="onlineNodes"></div>
        </aside>
    </div>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
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

    let myUID, myName, isAdmin = false;
    let currentRoom = "global_v130";

    // Auto-Login Persistence
    onAuthStateChanged(auth, user => {
        if (user) {
            const savedName = localStorage.getItem("empire_user");
            const savedAdmin = localStorage.getItem("empire_admin") === "true";
            if (savedName) {
                myUID = user.uid; myName = savedName; isAdmin = savedAdmin;
                enterEmpire();
            }
        }
    });

    window.bootSystem = async () => {
        const name = document.getElementById("nodeName").value.trim();
        if (!name) return;
        
        let adminMode = false;
        if (name.toLowerCase() === 'nazim') {
            const pin = prompt("Enter Master PIN:");
            if (pin !== "786") { alert("Access Denied"); return; }
            adminMode = true;
        }

        const res = await signInAnonymously(auth);
        myUID = res.user.uid; myName = name; isAdmin = adminMode;
        localStorage.setItem("empire_user", name);
        localStorage.setItem("empire_admin", adminMode);
        enterEmpire();
    };

    function enterEmpire() {
        document.getElementById("authLayer").style.display = "none";
        if (isAdmin) {
            document.getElementById("adminBadge").style.display = "block";
            document.getElementById("adminPanel").style.display = "block";
        }
        set(ref(db, 'nodes/' + myUID), { name: myName, online: true });
        syncMessages();
        syncUsers();
    }

    window.dispatch = (photo = null) => {
        const inp = document.getElementById("mInput");
        if (!inp.value.trim() && !photo) return;
        push(ref(db, currentRoom), {
            uid: myUID, sender: myName, text: inp.value, photo: photo, ts: serverTimestamp()
        });
        inp.value = "";
    };

    window.handleImage = (el) => {
        const r = new FileReader();
        r.onload = (e) => dispatch(e.target.result);
        r.readAsDataURL(el.files[0]);
    };

    window.goGlobal = () => {
        currentRoom = "global_v130";
        document.getElementById("viewTitle").innerText = "GLOBAL_CONNECT";
        document.getElementById("globalTab").classList.add("active");
        syncMessages();
    };

    window.openPrivate = (targetUID, targetName) => {
        if (targetUID === myUID) return;
        const roomID = myUID < targetUID ? `${myUID}_${targetUID}` : `${targetUID}_${myUID}`;
        currentRoom = `private/${roomID}`;
        document.getElementById("viewTitle").innerText = "🔒 " + targetName;
        document.getElementById("globalTab").classList.remove("active");
        syncMessages();
    };

    function syncMessages() {
        onValue(query(ref(db, currentRoom), limitToLast(40)), snap => {
            const feed = document.getElementById("chatFeed");
            feed.innerHTML = "";
            snap.forEach(s => {
                const d = s.val();
                feed.innerHTML += `
                    <div class="bubble ${d.uid === myUID ? 'me' : 'other'}">
                        <small style="opacity:0.5; font-weight:800; font-size:10px;">${d.sender}</small>
                        ${d.text ? `<div>${d.text}</div>` : ''}
                        ${d.photo ? `<img src="${d.photo}">` : ''}
                    </div>
                `;
            });
            feed.scrollTop = feed.scrollHeight;
        });
    }

    function syncUsers() {
        onValue(ref(db, 'nodes'), snap => {
            const list = document.getElementById("onlineNodes");
            list.innerHTML = "";
            snap.forEach(s => {
                const d = s.val();
                if (s.key === myUID) return;
                list.innerHTML += `
                    <div class="u-item" onclick="openPrivate('${s.key}', '${d.name}')">
                        <img src="https://ui-avatars.com/api/?name=${d.name}&background=8b5cf6&color=fff" class="u-avatar">
                        <span style="font-size:0.9rem;">${d.name}</span>
                        <div style="width:8px; height:8px; background:#10b981; border-radius:50%; margin-left:auto;"></div>
                    </div>
                `;
            });
        });
    }

    window.adminWipe = () => { if (isAdmin && confirm("Wipe Global History?")) remove(ref(db, 'global_v130')); };
    window.logout = () => { localStorage.clear(); signOut(auth).then(() => location.reload()); };
</script>
</body>
</html>
