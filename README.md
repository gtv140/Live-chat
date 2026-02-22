<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Pro Max 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #0b0e14;
            --card: rgba(22, 27, 34, 0.9);
            --primary: #a855f7;
            --accent: #f43f5e;
            --text: #e6edf3;
            --glass: rgba(255, 255, 255, 0.05);
            --success: #238636;
        }

        body {
            margin: 0; font-family: 'Inter', system-ui, sans-serif;
            background: #0d1117; color: var(--text); height: 100vh; overflow: hidden;
        }

        .app { max-width: 500px; margin: auto; height: 100vh; display: flex; flex-direction: column; background: var(--bg); border-left: 1px solid var(--glass); border-right: 1px solid var(--glass); position: relative; }

        /* HEADER */
        header {
            padding: 15px 20px; display: flex; justify-content: space-between; align-items: center;
            background: var(--card); backdrop-filter: blur(10px); border-bottom: 1px solid var(--glass);
        }
        header h1 { font-size: 18px; margin: 0; background: linear-gradient(90deg, #d8b4fe, #f472b6); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* PAGES */
        .page { display: none; flex: 1; flex-direction: column; overflow-y: auto; padding: 15px; animation: fadeIn 0.3s ease; }
        .active { display: flex; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }

        /* DASHBOARD CARDS */
        .stat-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 20px; }
        .stat-card { background: var(--card); padding: 15px; border-radius: 12px; border: 1px solid var(--glass); text-align: center; }

        /* USER & GROUP LISTS */
        .list-item { background: var(--card); padding: 12px 15px; border-radius: 12px; margin-bottom: 8px; display: flex; align-items: center; justify-content: space-between; cursor: pointer; border: 1px solid var(--glass); transition: 0.2s; }
        .list-item:hover { border-color: var(--primary); background: rgba(168, 85, 247, 0.1); }
        .online-dot { width: 10px; height: 10px; background: var(--success); border-radius: 50%; box-shadow: 0 0 8px var(--success); }

        /* CHAT BUBBLES */
        .chat-box { flex: 1; display: flex; flex-direction: column; gap: 10px; overflow-y: auto; padding: 10px; }
        .msg { max-width: 75%; padding: 10px 14px; border-radius: 16px; font-size: 14px; position: relative; cursor: pointer; }
        .msg.sent { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 2px; }
        .msg.received { align-self: flex-start; background: #21262d; border-bottom-left-radius: 2px; border: 1px solid var(--glass); }
        .msg .sender { font-size: 10px; font-weight: bold; display: block; margin-bottom: 4px; opacity: 0.8; }
        .msg .delete-hint { font-size: 8px; position: absolute; top: -15px; right: 0; color: var(--accent); display: none; }
        .msg:hover .delete-hint { display: block; }

        /* INPUT BAR */
        .input-bar { padding: 15px; background: var(--card); display: flex; gap: 10px; align-items: center; border-top: 1px solid var(--glass); }
        .input-bar input { flex: 1; padding: 12px 18px; border-radius: 25px; border: 1px solid var(--glass); background: #000; color: white; outline: none; }
        .circle-btn { width: 40px; height: 40px; border-radius: 50%; border: none; background: var(--primary); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 18px; }

        /* NAV BAR */
        nav { display: flex; justify-content: space-around; background: var(--card); padding: 12px; border-top: 1px solid var(--glass); }
        nav button { background: none; border: none; color: #8b949e; font-size: 20px; cursor: pointer; transition: 0.2s; }
        nav button.active { color: var(--primary); transform: translateY(-3px); }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>LIVE CONNECT PRO</h1>
        <div id="me" style="font-size: 12px; color: var(--primary);"></div>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; padding-top:100px;">
            <i class="fa-solid fa-bolt-lightning" style="font-size:50px; color:var(--primary); margin-bottom:20px;"></i>
            <h2>Enter the Nexus</h2>
            <input type="text" id="usernameInput" placeholder="Create Username" style="width:85%; padding:15px; border-radius:12px; background:#000; color:white; border:1px solid var(--glass); margin-bottom:15px;">
            <button onclick="login()" style="width:85%; padding:15px; border-radius:12px; background:var(--primary); color:white; border:none; font-weight:bold; cursor:pointer;">Initialize Link</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <h3>System Status</h3>
        <div class="stat-grid">
            <div class="stat-card"><h2 id="onlineCounter">0</h2><small>Users Live</small></div>
            <div class="stat-card"><h2 id="groupCounter">0</h2><small>Groups Active</small></div>
        </div>
        
        <div style="display:flex; justify-content: space-between; align-items: center;">
            <h3>New Group</h3>
            <button onclick="createGroup()" class="circle-btn" style="width:30px; height:30px; font-size:12px;"><i class="fa-solid fa-plus"></i></button>
        </div>
        <input type="text" id="groupNameInp" placeholder="Enter group name..." style="width:90%; padding:10px; border-radius:8px; background:#000; color:white; border:1px solid var(--glass); margin-bottom:20px;">
        
        <h3>Community Groups</h3>
        <div id="groupList"></div>
    </div>

    <div id="usersPage" class="page">
        <h3>Live Explorers</h3>
        <div id="userList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="chatHeader" style="padding:10px; text-align:center; background:rgba(168, 85, 247, 0.1); border-radius:10px; font-size:12px; font-weight:bold; color:var(--primary); margin-bottom:10px;">Select a user to chat</div>
        <div class="chat-box" id="chatBox"></div>
        <div class="input-bar">
            <input type="text" id="msgInput" placeholder="Message...">
            <button onclick="sendMessage()" class="circle-btn"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="switchPage('homePage', this)" class="active"><i class="fa-solid fa-house"></i></button>
        <button onclick="switchPage('usersPage', this)"><i class="fa-solid fa-user-group"></i></button>
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

    let user = localStorage.getItem("app_user");
    let activeChat = null;
    let chatType = 'private'; // 'private' or 'group'

    if(user) startSession();

    window.login = () => {
        const val = document.getElementById("usernameInput").value.trim();
        if(!val) return;
        user = val;
        localStorage.setItem("app_user", user);
        startSession();
    };

    function startSession() {
        document.getElementById("loginPage").classList.remove("active");
        document.getElementById("homePage").classList.add("active");
        document.getElementById("navbar").style.display = "flex";
        document.getElementById("me").innerText = "ID: " + user;

        const uRef = ref(db, 'users/' + user);
        set(uRef, { online: true, lastSeen: serverTimestamp() });
        onDisconnect(uRef).set({ online: false, lastSeen: serverTimestamp() });

        initListeners();
    }

    function initListeners() {
        // Listen for Online Users
        onValue(ref(db, 'users'), snap => {
            const list = document.getElementById("userList");
            let count = 0;
            list.innerHTML = "";
            snap.forEach(u => {
                if(u.val().online) {
                    count++;
                    if(u.key !== user) {
                        const div = document.createElement("div");
                        div.className = "list-item";
                        div.innerHTML = `<span>${u.key}</span> <div class="online-dot"></div>`;
                        div.onclick = () => startPrivateChat(u.key);
                        list.appendChild(div);
                    }
                }
            });
            document.getElementById("onlineCounter").innerText = count;
        });

        // Listen for Groups
        onValue(ref(db, 'groups'), snap => {
            const list = document.getElementById("groupList");
            list.innerHTML = "";
            document.getElementById("groupCounter").innerText = snap.size;
            snap.forEach(g => {
                const div = document.createElement("div");
                div.className = "list-item";
                div.innerHTML = `<span># ${g.key}</span> <i class="fa-solid fa-chevron-right" style="opacity:0.5"></i>`;
                div.onclick = () => startGroupChat(g.key);
                list.appendChild(div);
            });
        });
    }

    window.createGroup = () => {
        const name = document.getElementById("groupNameInp").value.trim();
        if(name) set(ref(db, 'groups/' + name), { createdBy: user });
        document.getElementById("groupNameInp").value = "";
    };

    function startPrivateChat(target) {
        activeChat = [user, target].sort().join("_");
        chatType = 'private';
        document.getElementById("chatHeader").innerText = "PRIVATE LINK: " + target;
        switchPage('chatPage', document.getElementById('chatBtn'));
        loadMessages();
    }

    function startGroupChat(groupName) {
        activeChat = groupName;
        chatType = 'group';
        document.getElementById("chatHeader").innerText = "GROUP CHANNEL: " + groupName;
        switchPage('chatPage', document.getElementById('chatBtn'));
        loadMessages();
    }

    function loadMessages() {
        const path = (chatType === 'private' ? 'privateChats/' : 'groupChats/') + activeChat;
        onValue(ref(db, path), snap => {
            const box = document.getElementById("chatBox");
            box.innerHTML = "";
            snap.forEach(m => {
                const data = m.val();
                const isMe = data.from === user;
                const div = document.createElement("div");
                div.className = `msg ${isMe ? 'sent' : 'received'}`;
                div.innerHTML = `
                    ${isMe ? '<span class="delete-hint">Click to Delete</span>' : ''}
                    <span class="sender">${data.from}</span>
                    ${data.text}
                `;
                if(isMe) div.onclick = () => { if(confirm("Unsend message?")) remove(ref(db, path + '/' + m.key)); };
                box.appendChild(div);
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    window.sendMessage = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value || !activeChat) return;
        const path = (chatType === 'private' ? 'privateChats/' : 'groupChats/') + activeChat;
        push(ref(db, path), {
            from: user,
            text: inp.value,
            timestamp: serverTimestamp()
        });
        inp.value = "";
    };

    window.switchPage = (id, btn) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    };

    window.logout = () => {
        localStorage.removeItem("app_user");
        location.reload();
    };
</script>
</body>
</html>
