<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect - Ultra Pro Max 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg: #020617;
            --card: rgba(15, 23, 42, 0.8);
            --primary: #6366f1;
            --accent: #f43f5e;
            --text: #f8fafc;
            --glass: rgba(255, 255, 255, 0.03);
            --success: #10b981;
        }

        body {
            margin: 0; font-family: 'Plus Jakarta Sans', sans-serif;
            background: radial-gradient(circle at 0% 0%, #1e1b4b 0%, #020617 100%);
            color: var(--text); height: 100vh; overflow: hidden;
        }

        .app-container { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; background: var(--glass); backdrop-filter: blur(25px); border: 1px solid var(--glass); position: relative; }

        /* Modern Header */
        header {
            padding: 20px; display: flex; justify-content: space-between; align-items: center;
            background: var(--card); border-bottom: 1px solid var(--glass); z-index: 100;
        }
        header h1 { font-size: 1.2rem; font-weight: 800; background: linear-gradient(to right, #818cf8, #f472b6); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        /* Dashboard Stats */
        .page { display: none; flex: 1; flex-direction: column; overflow-y: auto; padding: 20px; animation: slideIn 0.3s ease-out; }
        .page.active { display: flex; }
        @keyframes slideIn { from { transform: translateY(20px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }

        .stat-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 25px; }
        .stat-card { background: var(--card); padding: 15px; border-radius: 20px; border: 1px solid var(--glass); text-align: center; }
        .stat-card i { font-size: 24px; color: var(--primary); margin-bottom: 10px; }

        /* Chat UI */
        .chat-box { flex: 1; display: flex; flex-direction: column; gap: 12px; overflow-y: auto; padding: 10px; scrollbar-width: none; }
        .msg { max-width: 75%; padding: 12px 16px; border-radius: 22px; position: relative; font-size: 14px; line-height: 1.5; cursor: pointer; transition: 0.2s; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        .msg.sent { align-self: flex-end; background: linear-gradient(135deg, var(--primary), #4338ca); color: white; border-bottom-right-radius: 4px; }
        .msg.received { align-self: flex-start; background: var(--card); border: 1px solid var(--glass); border-bottom-left-radius: 4px; }
        .msg b { display: block; font-size: 10px; margin-bottom: 4px; opacity: 0.8; text-transform: uppercase; }
        .msg img { max-width: 100%; border-radius: 12px; margin-top: 5px; }
        .reaction-badge { position: absolute; bottom: -10px; right: 10px; background: #1e1b4b; padding: 2px 6px; border-radius: 10px; font-size: 12px; border: 1px solid var(--glass); }

        /* Input Area */
        .input-bar { background: var(--card); padding: 15px; border-top: 1px solid var(--glass); display: flex; align-items: center; gap: 12px; }
        .input-bar input { flex: 1; padding: 12px 20px; border-radius: 30px; border: 1px solid var(--glass); background: rgba(0,0,0,0.3); color: white; outline: none; }
        .icon-btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--glass); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 18px; transition: 0.3s; }
        .icon-btn.send { background: var(--primary); box-shadow: 0 4px 15px rgba(99, 102, 241, 0.4); }
        .icon-btn:hover { transform: scale(1.1); }

        /* Navigation */
        nav { display: flex; justify-content: space-around; background: var(--card); padding: 15px; border-top: 1px solid var(--glass); }
        nav button { background: none; border: none; font-size: 22px; color: #64748b; cursor: pointer; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: translateY(-5px); }

        /* List Items */
        .user-item { background: var(--card); padding: 15px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; border: 1px solid var(--glass); cursor: pointer; }
        .avatar { width: 40px; height: 40px; border-radius: 50%; background: var(--primary); display: flex; align-items: center; justify-content: center; font-weight: bold; }
        .online-dot { width: 10px; height: 10px; background: var(--success); border-radius: 50%; box-shadow: 0 0 10px var(--success); }
    </style>
</head>
<body>

<div class="app-container">
    <header>
        <h1>LIVE CONNECT 🚀</h1>
        <button onclick="logout()" class="icon-btn" style="width:35px; height:35px;"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; padding-top: 50px;">
            <i class="fa-solid fa-comments" style="font-size: 80px; color: var(--primary); text-shadow: 0 0 30px var(--primary);"></i>
            <h2 style="margin: 30px 0;">Welcome to Future</h2>
            <input type="text" id="usernameInput" placeholder="Enter Your Cool Name..." style="width:100%; padding:15px; border-radius:15px; background:var(--card); color:white; border:1px solid var(--glass); margin-bottom:20px;">
            <button onclick="login()" style="width:100%; padding:15px; border-radius:15px; background:var(--primary); color:white; border:none; font-weight:bold; cursor:pointer;">Launch App</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <h3>Network Activity</h3>
        <div class="stat-grid">
            <div class="stat-card"><i class="fa-solid fa-globe"></i><h2 id="onlineCounter">0</h2><small>Users Live</small></div>
            <div class="stat-card"><i class="fa-solid fa-layer-group"></i><h2 id="groupCounter">0</h2><small>Channels</small></div>
        </div>
        <h3>Create New Channel</h3>
        <div style="display:flex; gap:10px; margin-bottom: 25px;">
            <input type="text" id="groupNameInp" placeholder="Channel Name..." style="flex:1; padding:12px; border-radius:12px; background:var(--card); border:1px solid var(--glass); color:white;">
            <button onclick="createGroup()" class="icon-btn send"><i class="fa-solid fa-plus"></i></button>
        </div>
        <div id="groupList"></div>
    </div>

    <div id="usersPage" class="page">
        <h3>Live Explorers</h3>
        <div id="userList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="chatTitle" style="text-align:center; font-size:12px; color:var(--primary); margin-bottom:10px; font-weight:bold;"></div>
        <div class="chat-box" id="chatBox"></div>
        <div class="input-bar">
            <label for="imgInp" class="icon-btn"><i class="fa-solid fa-image"></i></label>
            <input type="file" id="imgInp" hidden onchange="sendImage(this)">
            <input type="text" id="msgInput" placeholder="Say something...">
            <button onclick="sendMsg()" class="icon-btn send"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="switchPage('homePage', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="switchPage('usersPage', this)"><i class="fa-solid fa-user-astronaut"></i></button>
        <button onclick="switchPage('chatPage', this)" id="chatBtn"><i class="fa-solid fa-message"></i></button>
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

    let user = localStorage.getItem("chat_user");
    let activeChat = null;
    let isGroupChat = false;

    if(user) startApp();

    window.login = () => {
        let val = document.getElementById("usernameInput").value.trim();
        if(!val) return;
        user = val;
        localStorage.setItem("chat_user", user);
        startApp();
    };

    function startApp() {
        document.getElementById("loginPage").classList.remove("active");
        document.getElementById("homePage").classList.add("active");
        document.getElementById("navbar").style.display = "flex";
        
        const presenceRef = ref(db, 'users/' + user);
        set(presenceRef, { online: true, lastSeen: serverTimestamp() });
        onDisconnect(presenceRef).set({ online: false, lastSeen: serverTimestamp() });

        loadNetworkData();
    }

    function loadNetworkData() {
        // Online Users
        onValue(ref(db, 'users'), snap => {
            const list = document.getElementById("userList");
            let count = 0; list.innerHTML = "";
            snap.forEach(u => {
                if(u.val().online) {
                    count++;
                    if(u.key !== user) {
                        const item = document.createElement("div");
                        item.className = "user-item";
                        item.innerHTML = `<div class="avatar">${u.key[0]}</div> <div style="flex:1"><b>${u.key}</b><br><small style="color:var(--success)">Available</small></div> <div class="online-dot"></div>`;
                        item.onclick = () => startChat(u.key, false);
                        list.appendChild(item);
                    }
                }
            });
            document.getElementById("onlineCounter").innerText = count;
        });

        // Groups
        onValue(ref(db, 'groups'), snap => {
            const list = document.getElementById("groupList");
            list.innerHTML = "";
            document.getElementById("groupCounter").innerText = snap.size;
            snap.forEach(g => {
                const item = document.createElement("div");
                item.className = "user-item";
                item.innerHTML = `<i class="fa-solid fa-hashtag" style="font-size:20px; color:var(--primary)"></i> <b>${g.key}</b>`;
                item.onclick = () => startChat(g.key, true);
                list.appendChild(item);
            });
        });
    }

    window.startChat = (id, isGroup) => {
        activeChat = isGroup ? id : [user, id].sort().join("_");
        isGroupChat = isGroup;
        document.getElementById("chatTitle").innerText = "CONNECTED TO: " + id;
        switchPage('chatPage', document.getElementById('chatBtn'));
        loadMessages();
    };

    function loadMessages() {
        const path = (isGroupChat ? 'groupChats/' : 'privateChats/') + activeChat;
        onValue(ref(db, path), snap => {
            const box = document.getElementById("chatBox");
            box.innerHTML = "";
            snap.forEach(m => {
                const data = m.val();
                const isMe = data.sender === user;
                const div = document.createElement("div");
                div.className = `msg ${isMe ? 'sent' : 'received'}`;
                let content = data.image ? `<img src="${data.image}">` : data.text;
                div.innerHTML = `<b>${data.sender}</b>${content}`;
                if(isMe) div.onclick = () => { if(confirm("Unsend message?")) remove(ref(db, path + '/' + m.key)); };
                box.appendChild(div);
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    window.sendMsg = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value) return;
        const path = (isGroupChat ? 'groupChats/' : 'privateChats/') + activeChat;
        push(ref(db, path), { sender: user, text: inp.value, time: serverTimestamp() });
        inp.value = "";
    };

    window.sendImage = (inp) => {
        const file = inp.files[0];
        const reader = new FileReader();
        reader.onload = (e) => {
            const path = (isGroupChat ? 'groupChats/' : 'privateChats/') + activeChat;
            push(ref(db, path), { sender: user, image: e.target.result });
        };
        reader.readAsDataURL(file);
    };

    window.createGroup = () => {
        const name = document.getElementById("groupNameInp").value.trim();
        if(name) set(ref(db, 'groups/' + name), { createdBy: user });
        document.getElementById("groupNameInp").value = "";
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
