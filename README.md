<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect 🚀 | Next-Gen Chat</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #0f172a;
            --glass: rgba(30, 41, 59, 0.7);
            --card-border: rgba(255, 255, 255, 0.1);
            --text: #f8fafc;
            --primary: #8b5cf6;
            --accent: #ec4899;
            --success: #10b981;
            --msg-sent: #4f46e5;
            --msg-received: #334155;
        }

        body.light {
            --bg: #f1f5f9;
            --glass: rgba(255, 255, 255, 0.8);
            --card-border: rgba(0, 0, 0, 0.05);
            --text: #1e293b;
            --msg-received: #e2e8f0;
        }

        body {
            margin: 0;
            font-family: 'Inter', system-ui, -apple-system, sans-serif;
            background: var(--bg);
            color: var(--text);
            transition: all 0.3s ease;
            overflow-x: hidden;
        }

        .app {
            max-width: 500px;
            margin: auto;
            height: 100vh;
            display: flex;
            flex-direction: column;
            background: radial-gradient(circle at top right, rgba(139, 92, 246, 0.1), transparent);
        }

        /* --- Header --- */
        header {
            padding: 18px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: var(--glass);
            backdrop-filter: blur(12px);
            border-bottom: 1px solid var(--card-border);
            z-index: 10;
        }

        header h1 {
            margin: 0;
            font-size: 20px;
            font-weight: 800;
            background: linear-gradient(to right, var(--primary), var(--accent));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        /* --- Pages --- */
        .page {
            display: none;
            padding: 20px;
            flex: 1;
            overflow-y: auto;
            animation: fadeIn 0.4s ease-out;
        }

        .page.active { display: flex; flex-direction: column; }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* --- Hero Card --- */
        .hero {
            background: linear-gradient(135deg, var(--primary), var(--accent));
            padding: 30px;
            border-radius: 24px;
            text-align: center;
            box-shadow: 0 10px 25px -5px rgba(139, 92, 246, 0.4);
            margin-bottom: 25px;
        }

        /* --- Chat UI --- */
        .chat-container {
            flex: 1;
            display: flex;
            flex-direction: column;
            gap: 12px;
            padding-bottom: 20px;
        }

        .chat-box {
            flex: 1;
            overflow-y: auto;
            padding: 5px;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .msg {
            max-width: 80%;
            padding: 12px 16px;
            border-radius: 18px;
            font-size: 14px;
            position: relative;
            line-height: 1.5;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        .msg.sent {
            align-self: flex-end;
            background: var(--msg-sent);
            color: white;
            border-bottom-right-radius: 4px;
        }

        .msg.received {
            align-self: flex-start;
            background: var(--msg-received);
            border-bottom-left-radius: 4px;
        }

        .msg-info { font-size: 11px; opacity: 0.7; margin-bottom: 4px; display: block; }

        /* --- Input Area --- */
        .input-wrapper {
            padding: 15px;
            background: var(--glass);
            backdrop-filter: blur(10px);
            border-top: 1px solid var(--card-border);
        }

        .input-row {
            display: flex;
            gap: 10px;
            background: var(--bg);
            padding: 5px 5px 5px 15px;
            border-radius: 30px;
            border: 1px solid var(--card-border);
        }

        .input-row input {
            flex: 1;
            border: none;
            background: transparent;
            color: var(--text);
            outline: none;
            font-size: 15px;
        }

        .input-row button {
            width: 45px;
            height: 45px;
            border-radius: 50%;
            border: none;
            background: var(--primary);
            color: white;
            cursor: pointer;
            transition: 0.2s;
        }

        /* --- Navigation --- */
        nav {
            display: flex;
            justify-content: space-around;
            padding: 12px 0 25px;
            background: var(--glass);
            backdrop-filter: blur(15px);
            border-top: 1px solid var(--card-border);
        }

        nav button {
            background: none;
            border: none;
            font-size: 20px;
            color: #64748b;
            cursor: pointer;
            position: relative;
        }

        nav button.active { color: var(--primary); }
        nav button.active::after {
            content: '';
            position: absolute;
            bottom: -8px;
            left: 50%;
            transform: translateX(-50%);
            width: 5px;
            height: 5px;
            background: var(--primary);
            border-radius: 50%;
        }

        /* Lists */
        .list-item {
            background: var(--glass);
            padding: 15px;
            border-radius: 16px;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            gap: 12px;
            border: 1px solid var(--card-border);
            cursor: pointer;
        }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div>
            <button onclick="toggleTheme()" style="background:none; border:none; color:var(--text); font-size: 18px; margin-right: 15px;"><i class="fa-solid fa-circle-half-stroke"></i></button>
            <button onclick="logout()" style="background:none; border:none; color:var(--accent); font-size: 18px;"><i class="fa-solid fa-power-off"></i></button>
        </div>
    </header>

    <div id="loginPage" class="page active">
        <div style="margin-top: 40px; text-align: center;">
            <div class="hero"><h2>Join the Wave 🌊</h2><p>Connect instantly, chat freely.</p></div>
            <div class="input-row" style="margin-top: 20px;">
                <input type="text" id="usernameInput" placeholder="Enter your handle...">
                <button onclick="login()"><i class="fa-solid fa-arrow-right"></i></button>
            </div>
        </div>
    </div>

    <div id="home" class="page">
        <div class="hero"><h2>Hey <span id="dashUser"></span>! 👋</h2><p>The world is online right now.</p></div>
        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;">
            <div class="list-item" style="flex-direction: column; text-align: center;">
                <small>Online</small><b id="onlineCount" style="font-size: 24px; color: var(--success);">0</b>
            </div>
            <div class="list-item" style="flex-direction: column; text-align: center;">
                <small>Groups</small><b id="groupCount" style="font-size: 24px; color: var(--primary);">0</b>
            </div>
        </div>
    </div>

    <div id="chat" class="page">
        <div class="chat-container">
            <div id="chatHeader" style="padding-bottom: 10px; border-bottom: 1px solid var(--card-border); font-weight: bold;">Select a contact</div>
            <div class="chat-box" id="chatBox"></div>
        </div>
        <div class="input-wrapper">
            <div class="input-row">
                <input id="msgInput" placeholder="Type a message..."/>
                <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <div id="users" class="page">
        <h4 style="margin-bottom: 15px;">People Nearby</h4>
        <div id="userList"></div>
        <h4 style="margin: 20px 0 15px;">Communities</h4>
        <div id="groupList"></div>
        <div class="input-row">
            <input id="groupInput" placeholder="New group name..."/>
            <button onclick="createGroup()"><i class="fa-solid fa-plus"></i></button>
        </div>
    </div>

    <nav id="navbar" style="display: none;">
        <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house-chimney"></i></button>
        <button onclick="openPage('chat',this)"><i class="fa-solid fa-message"></i></button>
        <button onclick="openPage('users',this)"><i class="fa-solid fa-users"></i></button>
        <button onclick="openPage('contact',this)"><i class="fa-solid fa-address-book"></i></button>
    </nav>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
    import { getDatabase, ref, set, push, onValue, remove, onDisconnect, serverTimestamp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

    const firebaseConfig = {
        apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
        authDomain: "live-chat-b810c.firebaseapp.com",
        databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
        projectId: "live-chat-b810c",
        storageBucket: "live-chat-b810c.appspot.com",
        messagingSenderId: "555058795334",
        appId: "1:555058795334:web:f668887409800c32970b47"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    let currentUser = localStorage.getItem('chatUser') || null;
    let curChat = "";
    let isGroup = false;

    // UI Elements
    const chatBox = document.getElementById("chatBox");
    const userList = document.getElementById("userList");
    const groupList = document.getElementById("groupList");
    const navbar = document.getElementById("navbar");

    window.login = () => {
        const uname = document.getElementById("usernameInput").value.trim();
        if(!uname) return;
        currentUser = uname;
        localStorage.setItem('chatUser', uname);
        setupUser();
    };

    function setupUser() {
        document.getElementById("dashUser").textContent = currentUser;
        const userRef = ref(db, "users/" + currentUser);
        set(userRef, { name: currentUser, online: true, lastSeen: serverTimestamp() });
        onDisconnect(userRef).update({ online: false, lastSeen: serverTimestamp() });
        
        document.getElementById("loginPage").classList.remove("active");
        document.getElementById("home").classList.add("active");
        navbar.style.display = "flex";
    }

    if(currentUser) setupUser();

    window.logout = () => {
        localStorage.removeItem('chatUser');
        location.reload();
    };

    window.openPage = (id, btn) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    };

    window.toggleTheme = () => document.body.classList.toggle('light');

    // Load Users
    onValue(ref(db, "users"), snap => {
        let online = 0;
        userList.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online) online++;
            if(u.key !== currentUser){
                const d = document.createElement("div");
                d.className = "list-item";
                d.innerHTML = `<div style="width:10px;height:10px;border-radius:50%;background:var(--success)"></div> ${u.val().name}`;
                d.onclick = () => startChat(u.key, u.val().name, false);
                userList.appendChild(d);
            }
        });
        document.getElementById("onlineCount").textContent = online;
    });

    // Load Groups
    onValue(ref(db, "groups"), snap => {
        groupList.innerHTML = "";
        let count = 0;
        snap.forEach(g => {
            count++;
            const d = document.createElement("div");
            d.className = "list-item";
            d.innerHTML = `<i class="fa-solid fa-hashtag"></i> ${g.key}`;
            d.onclick = () => startChat(g.key, g.key, true);
            groupList.appendChild(d);
        });
        document.getElementById("groupCount").textContent = count;
    });

    function startChat(id, name, groupStatus) {
        curChat = id;
        isGroup = groupStatus;
        document.getElementById("chatHeader").textContent = isGroup ? "# " + name : "User: " + name;
        loadChat();
        openPage('chat', document.querySelector('nav button:nth-child(2)'));
    }

    function loadChat() {
        if(!curChat) return;
        const path = isGroup ? `groupChats/${curChat}` : `chats/${[currentUser, curChat].sort().join("_")}`;
        onValue(ref(db, path), snap => {
            chatBox.innerHTML = "";
            snap.forEach(m => {
                const isMe = m.val().from === currentUser;
                const div = document.createElement("div");
                div.className = `msg ${isMe ? 'sent' : 'received'}`;
                div.innerHTML = `
                    <span class="msg-info">${isMe ? 'You' : m.val().from}</span>
                    <div>${m.val().text}</div>
                `;
                chatBox.appendChild(div);
            });
            chatBox.scrollTop = chatBox.scrollHeight;
        });
    }

    window.sendMsg = () => {
        const input = document.getElementById('msgInput');
        if(!input.value || !curChat) return;
        const path = isGroup ? `groupChats/${curChat}` : `chats/${[currentUser, curChat].sort().join("_")}`;
        push(ref(db, path), {
            from: currentUser,
            text: input.value,
            time: serverTimestamp()
        });
        input.value = '';
    };

    window.createGroup = () => {
        const g = document.getElementById("groupInput").value.trim();
        if(!g) return;
        set(ref(db, "groups/" + g), { createdBy: currentUser, createdAt: serverTimestamp() });
        document.getElementById("groupInput").value = '';
    };
</script>
</body>
</html>
