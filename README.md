<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Infinity 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #050505; --card: rgba(22, 22, 28, 0.9);
            --primary: #8b5cf6; --accent: #ec4899;
            --text: #f8fafc; --glass: rgba(255, 255, 255, 0.05);
            --success: #22c55e;
        }

        body {
            margin: 0; font-family: 'Inter', sans-serif;
            background: radial-gradient(circle at 0% 0%, #1e1b4b 0%, #050505 100%);
            color: var(--text); height: 100vh; overflow: hidden;
        }

        .app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; background: var(--glass); backdrop-filter: blur(25px); border: 1px solid var(--glass); }

        /* Dashboard & Header */
        header { padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; background: var(--card); border-bottom: 1px solid var(--glass); }
        header h1 { font-size: 1.1rem; font-weight: 800; background: linear-gradient(to right, #a78bfa, #f472b6); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        .welcome-card { background: linear-gradient(135deg, var(--primary), #4c1d95); padding: 25px; border-radius: 25px; margin: 15px; text-align: center; box-shadow: 0 10px 30px rgba(139, 92, 246, 0.3); }
        .avatar-main { width: 65px; height: 65px; background: white; color: var(--primary); border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 28px; font-weight: bold; margin: 0 auto 10px; }

        /* Pages */
        .page { display: none; flex: 1; flex-direction: column; overflow-y: auto; padding: 15px; animation: fadeIn 0.3s ease; }
        .active { display: flex; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Chat UI */
        .chat-box { flex: 1; display: flex; flex-direction: column; gap: 10px; overflow-y: auto; padding: 10px; }
        .msg { max-width: 75%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; cursor: pointer; transition: 0.2s; }
        .sent { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 2px; }
        .received { align-self: flex-start; background: var(--card); border: 1px solid var(--glass); border-bottom-left-radius: 2px; }
        .msg b { display: block; font-size: 10px; opacity: 0.7; margin-bottom: 3px; }

        /* Input Area */
        .input-bar { padding: 15px; background: var(--card); display: flex; gap: 10px; align-items: center; border-top: 1px solid var(--glass); }
        .input-bar input { flex: 1; padding: 12px 20px; border-radius: 30px; border: 1px solid var(--glass); background: #000; color: white; outline: none; }
        .btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--primary); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 18px; }

        /* Navigation */
        nav { display: flex; justify-content: space-around; background: var(--card); padding: 12px; border-top: 1px solid var(--glass); }
        nav button { background: none; border: none; color: #64748b; font-size: 20px; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 4px; }
        nav button span { font-size: 10px; font-weight: bold; }
        nav button.active { color: var(--primary); transform: translateY(-3px); }

        .user-row { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; border: 1px solid var(--glass); cursor: pointer; }
        .dot { width: 10px; height: 10px; background: var(--success); border-radius: 50%; box-shadow: 0 0 10px var(--success); }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>INFINITY CONNECT <i class="fa-solid fa-cloud"></i></h1>
        <button onclick="logout()" style="background:none; border:none; color:var(--accent);"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; padding-top:100px;">
            <i class="fa-solid fa-fingerprint" style="font-size:60px; color:var(--primary);"></i>
            <h2>Personal Identity</h2>
            <input type="text" id="usernameInput" placeholder="Enter Username" style="width:85%; padding:15px; border-radius:15px; background:var(--card); color:white; border:1px solid var(--glass); margin-bottom:20px;">
            <button onclick="login()" style="width:85%; padding:15px; border-radius:15px; background:var(--primary); color:white; font-weight:bold;">Initialize</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div class="welcome-card">
            <div class="avatar-main" id="myIcon">U</div>
            <h2 id="welcomeName">Welcome Explorer</h2>
            <p style="font-size:12px; opacity:0.8;">Status: Securely Connected</p>
        </div>
        <h3>System Overview</h3>
        <div class="user-row" onclick="switchPage('usersPage', document.getElementById('uBtn'))">
            <i class="fa-solid fa-users" style="color:var(--success)"></i>
            <div style="flex:1"><b>Live Network</b><br><small id="onlineStat">Updating...</small></div>
            <i class="fa-solid fa-chevron-right"></i>
        </div>
        <div class="user-row" onclick="startChat('Global_Chat', true)">
            <i class="fa-solid fa-earth-americas" style="color:var(--primary)"></i>
            <div style="flex:1"><b>Global Station</b><br><small>Public Chat Room</small></div>
            <i class="fa-solid fa-chevron-right"></i>
        </div>
    </div>

    <div id="usersPage" class="page">
        <h3><i class="fa-solid fa-wifi"></i> Live Users</h3>
        <div id="userList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="chatHeader" style="text-align:center; font-size:12px; color:var(--primary); font-weight:bold; margin-bottom:10px;"></div>
        <div class="chat-box" id="chatBox"></div>
        <div class="input-bar">
            <label for="imgFile" class="btn" style="background:var(--glass)"><i class="fa-solid fa-image"></i></label>
            <input type="file" id="imgFile" hidden onchange="uploadImage(this)">
            <input type="text" id="msgInput" placeholder="Message...">
            <button onclick="sendMessage()" class="btn"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="switchPage('homePage', this)" class="active"><i class="fa-solid fa-house"></i><span>Home</span></button>
        <button onclick="switchPage('chatPage', this)" id="cBtn"><i class="fa-solid fa-comments"></i><span>Chat</span></button>
        <button onclick="switchPage('usersPage', this)" id="uBtn"><i class="fa-solid fa-user-astronaut"></i><span>Users</span></button>
    </nav>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
    import { getDatabase, ref, set, push, onValue, remove, onDisconnect, serverTimestamp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

    const firebaseConfig = {
        apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
        authDomain: "live-chat-b810c.firebaseapp.com",
        databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
        projectId: "live-chat-b810c"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    let currentUser = localStorage.getItem("chat_user");
    let activeChat = "Global_Chat";
    let isGlobal = true;

    if(currentUser) initApp();

    window.login = () => {
        let val = document.getElementById("usernameInput").value.trim();
        if(!val) return;
        currentUser = val;
        localStorage.setItem("chat_user", val);
        initApp();
    };

    function initApp() {
        document.getElementById("loginPage").style.display = "none";
        document.getElementById("homePage").classList.add("active");
        document.getElementById("navbar").style.display = "flex";
        document.getElementById("welcomeName").innerText = "Welcome, " + currentUser;
        document.getElementById("myIcon").innerText = currentUser[0].toUpperCase();

        set(ref(db, 'users/' + currentUser), { online: true });
        onDisconnect(ref(db, 'users/' + currentUser)).set({ online: false });

        loadUsers();
        loadMessages();
    }

    function loadUsers() {
        onValue(ref(db, 'users'), snap => {
            const list = document.getElementById("userList");
            let count = 0; list.innerHTML = "";
            snap.forEach(u => {
                if(u.val().online) {
                    count++;
                    if(u.key !== currentUser) {
                        const div = document.createElement("div");
                        div.className = "user-row";
                        div.innerHTML = `<div class="avatar-main" style="width:40px; height:40px; font-size:16px; margin:0;">${u.key[0]}</div> <div style="flex:1"><b>${u.key}</b></div> <div class="dot"></div>`;
                        div.onclick = () => startPrivateChat(u.key);
                        list.appendChild(div);
                    }
                }
            });
            document.getElementById("onlineStat").innerText = count + " Users Online";
        });
    }

    window.startPrivateChat = (target) => {
        activeChat = [currentUser, target].sort().join("_");
        isGlobal = false;
        document.getElementById("chatHeader").innerText = "PRIVATE: " + target;
        switchPage('chatPage', document.getElementById('cBtn'));
        loadMessages();
    };

    window.startChat = (target, global) => {
        activeChat = target; isGlobal = global;
        document.getElementById("chatHeader").innerText = "GLOBAL STATION";
        switchPage('chatPage', document.getElementById('cBtn'));
        loadMessages();
    }

    function loadMessages() {
        const path = isGlobal ? 'global/' + activeChat : 'private/' + activeChat;
        onValue(ref(db, path), snap => {
            const box = document.getElementById("chatBox");
            box.innerHTML = "";
            snap.forEach(m => {
                const data = m.val();
                const isMe = data.from === currentUser;
                const div = document.createElement("div");
                div.className = `msg ${isMe ? 'sent' : 'received'}`;
                let content = data.img ? `<img src="${data.img}" style="max-width:100%; border-radius:10px;">` : data.text;
                div.innerHTML = `<b>${data.from}</b>${content}`;
                if(isMe) div.onclick = () => { if(confirm("Delete message?")) remove(ref(db, path + '/' + m.key)); };
                box.appendChild(div);
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    window.sendMessage = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value) return;
        const path = isGlobal ? 'global/' + activeChat : 'private/' + activeChat;
        push(ref(db, path), { from: currentUser, text: inp.value });
        inp.value = "";
    };

    window.uploadImage = (inp) => {
        const reader = new FileReader();
        reader.onload = (e) => {
            const path = isGlobal ? 'global/' + activeChat : 'private/' + activeChat;
            push(ref(db, path), { from: currentUser, img: e.target.result });
        };
        reader.readAsDataURL(inp.files[0]);
    };

    window.switchPage = (id, btn) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    };

    window.logout = () => {
        localStorage.removeItem("chat_user");
        location.reload();
    };
</script>
</body>
</html>
