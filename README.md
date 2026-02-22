<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Infinity Connect Pro 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --bg: #05050a; --card: #11111e; --primary: #8b5cf6; --accent: #f43f5e; --text: #ffffff; --glass: rgba(255,255,255,0.05); --success: #22c55e; }
        body, html { margin:0; padding:0; height:100%; width:100%; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); overflow: hidden; }
        .app-shell { display: flex; flex-direction: column; height: 100vh; max-width: 500px; margin: 0 auto; background: var(--bg); position: relative; }
        
        header { padding: 15px; background: var(--card); border-bottom: 1px solid var(--glass); display: flex; justify-content: space-between; align-items: center; z-index: 10; }
        header h1 { font-size: 1.1rem; margin: 0; background: linear-gradient(90deg, #a78bfa, #f472b6); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }
        
        .content { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 160px; }
        .page { display: none; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* Dashboard */
        .welcome-hero { background: linear-gradient(135deg, #6d28d9, #db2777); padding: 25px; border-radius: 20px; text-align: center; margin-bottom: 20px; box-shadow: 0 10px 20px rgba(0,0,0,0.3); }
        .user-icon { width: 55px; height: 55px; background: white; color: var(--primary); border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 24px; font-weight: bold; margin: 0 auto 10px; }
        
        /* Chat UI */
        .chat-container { display: flex; flex-direction: column; gap: 12px; }
        .msg { padding: 12px; border-radius: 15px; max-width: 85%; font-size: 14px; position: relative; background: var(--card); border: 1px solid var(--glass); }
        .sent { align-self: flex-end; border-color: var(--primary); background: rgba(139, 92, 246, 0.15); }
        .msg b { color: var(--primary); font-size: 11px; display: block; margin-bottom: 4px; }
        .actions { position: absolute; top: 8px; right: 10px; color: var(--accent); cursor: pointer; font-size: 12px; }

        /* Navigation & Input */
        .input-area { position: absolute; bottom: 75px; left: 0; right: 0; padding: 12px; background: var(--card); display: flex; gap: 10px; border-top: 1px solid var(--glass); z-index: 100; }
        .input-area input { flex: 1; padding: 12px 18px; border-radius: 25px; border: 1px solid var(--glass); background: #000; color: white; outline: none; }
        .btn-send { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--primary); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; }

        nav { position: absolute; bottom: 0; left: 0; right: 0; height: 75px; background: var(--card); border-top: 1px solid var(--glass); display: flex; justify-content: space-around; align-items: center; z-index: 100; }
        nav button { background: none; border: none; color: #555; font-size: 22px; display: flex; flex-direction: column; align-items: center; gap: 4px; cursor: pointer; }
        nav button.active { color: var(--primary); }
        nav button span { font-size: 10px; font-weight: bold; }

        .item-row { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; border: 1px solid var(--glass); cursor: pointer; }
        .dot { width: 10px; height: 10px; background: var(--success); border-radius: 50%; }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1>INFINITY CONNECT <i class="fa-solid fa-bolt"></i></h1>
        <i class="fa-solid fa-sync" onclick="location.reload()" style="color:var(--primary); cursor:pointer;"></i>
    </header>

    <div class="content" id="scrollArea">
        <div id="loginPage" class="page active-page">
            <div style="text-align:center; padding-top:60px;">
                <i class="fa-solid fa-user-shield" style="font-size:50px; color:var(--primary); margin-bottom:20px;"></i>
                <h2>Access Portal</h2>
                <input type="text" id="usernameInp" placeholder="Your Name..." style="width:80%; padding:15px; border-radius:15px; background:var(--card); color:white; border:1px solid var(--glass); margin-bottom:15px;">
                <button onclick="login()" style="width:80%; padding:15px; border-radius:15px; background:var(--primary); color:white; border:none; font-weight:bold;">Login</button>
            </div>
        </div>

        <div id="home" class="page">
            <div class="welcome-hero">
                <div class="user-icon" id="userInitial">?</div>
                <h2 style="margin:0;">Greetings,</h2>
                <h1 id="welcomeUser" style="margin:5px 0;">User</h1>
            </div>
            <div class="item-row" onclick="openPage('users', document.getElementById('uBtn'))">
                <i class="fa-solid fa-users" style="color:var(--success)"></i>
                <div style="flex:1"><b>Active Network</b><br><small id="onlineCount">0 Users Online</small></div>
            </div>
        </div>

        <div id="users" class="page">
            <h3><i class="fa-solid fa-wifi"></i> Live Explorers</h3>
            <div id="userList"></div>
        </div>

        <div id="chat" class="page">
            <h4 id="chatTitle" style="text-align:center; color:var(--primary); margin:0 0 10px;">Chat Room</h4>
            <div class="chat-container" id="chatBox"></div>
        </div>
    </div>

    <div class="input-area" id="inputBar" style="display:none;">
        <input type="text" id="msgInput" placeholder="Write something...">
        <button onclick="sendMsg()" class="btn-send"><i class="fa-solid fa-paper-plane"></i></button>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="openPage('home', this)" id="hBtn" class="active"><i class="fa-solid fa-house"></i><span>Home</span></button>
        <button onclick="openPage('chat', this)" id="cBtn"><i class="fa-solid fa-comments"></i><span>Chat</span></button>
        <button onclick="openPage('users', this)" id="uBtn"><i class="fa-solid fa-user-astronaut"></i><span>Users</span></button>
    </nav>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
    import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

    const firebaseConfig = {
        apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
        authDomain: "live-chat-b810c.firebaseapp.com",
        databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
        projectId: "live-chat-b810c"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    let user = localStorage.getItem("chat_user");
    let targetChat = "Global_Station";

    if(user) autoLogin();

    window.login = () => {
        const val = document.getElementById("usernameInp").value.trim();
        if(!val) return;
        user = val;
        localStorage.setItem("chat_user", user);
        autoLogin();
    };

    function autoLogin() {
        document.getElementById("loginPage").classList.remove("active-page");
        document.getElementById("home").classList.add("active-page");
        document.getElementById("navbar").style.display = "flex";
        document.getElementById("welcomeUser").innerText = user;
        document.getElementById("userInitial").innerText = user[0].toUpperCase();
        
        set(ref(db, 'users/' + user), { online: true });
        onDisconnect(ref(db, 'users/' + user)).set({ online: false });
        loadCoreData();
    }

    window.openPage = (id, btn) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
        document.getElementById(id).classList.add('active-page');
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        document.getElementById('inputBar').style.display = (id === 'chat') ? 'flex' : 'none';
        if(id === 'chat') loadMessages();
    };

    function loadCoreData() {
        onValue(ref(db, 'users'), snap => {
            const list = document.getElementById("userList");
            let count = 0; list.innerHTML = "";
            snap.forEach(u => {
                if(u.val().online) {
                    count++;
                    if(u.key !== user) {
                        const div = document.createElement("div");
                        div.className = "item-row";
                        div.innerHTML = `<div class="dot"></div><b>${u.key}</b>`;
                        div.onclick = () => { targetChat = [user, u.key].sort().join("_"); document.getElementById('chatTitle').innerText = "DM: " + u.key; openPage('chat', document.getElementById('cBtn')); };
                        list.appendChild(div);
                    }
                }
            });
            document.getElementById("onlineCount").innerText = count + " Online";
        });
    }

    function loadMessages() {
        onValue(ref(db, 'chats/' + targetChat), snap => {
            const box = document.getElementById("chatBox");
            box.innerHTML = "";
            snap.forEach(m => {
                const data = m.val();
                const div = document.createElement("div");
                div.className = "msg" + (data.from === user ? " sent" : "");
                div.innerHTML = `<b>${data.from}</b>${data.text} <div class="actions" onclick="window.del('${m.key}')">🗑️</div>`;
                box.appendChild(div);
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    window.sendMsg = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value) return;
        push(ref(db, 'chats/' + targetChat), { from: user, text: inp.value });
        inp.value = "";
    };

    window.del = (id) => { if(confirm("Delete?")) remove(ref(db, `chats/${targetChat}/${id}`)); };
</script>
</body>
</html>
