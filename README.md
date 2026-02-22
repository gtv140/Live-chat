<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Elite 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #05070a;
            --card: rgba(17, 25, 40, 0.75);
            --primary: #7c3aed;
            --accent: #f43f5e;
            --text: #f3f4f6;
            --glass: rgba(255, 255, 255, 0.05);
            --success: #10b981;
        }

        body {
            margin: 0; font-family: 'Segoe UI', Roboto, sans-serif;
            background: radial-gradient(circle at top left, #1e1b4b, #05070a);
            color: var(--text); height: 100vh; overflow: hidden;
        }

        .app { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; background: var(--glass); backdrop-filter: blur(20px); border: 1px solid var(--glass); position: relative; }

        /* Header */
        header {
            padding: 15px 20px; display: flex; justify-content: space-between; align-items: center;
            background: var(--card); border-bottom: 1px solid var(--glass);
        }
        header h1 { font-size: 18px; margin: 0; letter-spacing: 1px; color: var(--primary); font-weight: 800; }

        /* Dashboard Welcome Section */
        .welcome-box {
            background: linear-gradient(135deg, var(--primary), var(--accent));
            padding: 25px; border-radius: 24px; margin: 10px 0 20px 0;
            box-shadow: 0 10px 20px rgba(0,0,0,0.3); text-align: center;
        }
        .user-avatar-lg {
            width: 70px; height: 70px; background: white; color: var(--primary);
            border-radius: 50%; display: flex; align-items: center; justify-content: center;
            font-size: 30px; font-weight: bold; margin: 0 auto 10px;
        }

        /* Navigation */
        nav {
            display: flex; justify-content: space-around; background: var(--card);
            padding: 15px; border-top: 1px solid var(--glass);
        }
        nav button { background: none; border: none; color: #6b7280; font-size: 20px; cursor: pointer; transition: 0.3s; display: flex; flex-direction: column; align-items: center; gap: 5px; }
        nav button span { font-size: 10px; font-weight: 600; text-transform: uppercase; }
        nav button.active { color: var(--primary); transform: translateY(-3px); }

        /* Pages */
        .page { display: none; flex: 1; flex-direction: column; overflow-y: auto; padding: 15px; animation: fadeIn 0.4s ease; }
        .active { display: flex; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* List Items */
        .list-item {
            background: var(--card); padding: 15px; border-radius: 18px;
            margin-bottom: 10px; display: flex; align-items: center; gap: 15px;
            border: 1px solid var(--glass); transition: 0.3s; cursor: pointer;
        }
        .list-item:hover { transform: translateX(5px); border-color: var(--primary); background: rgba(124, 58, 237, 0.1); }
        .icon-circle { width: 45px; height: 45px; border-radius: 50%; background: var(--glass); display: flex; align-items: center; justify-content: center; font-size: 18px; }

        /* Chat UI */
        .chat-box { flex: 1; display: flex; flex-direction: column; gap: 10px; overflow-y: auto; padding: 10px; }
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; }
        .msg.sent { align-self: flex-end; background: var(--primary); border-bottom-right-radius: 2px; }
        .msg.received { align-self: flex-start; background: #1f2937; border-bottom-left-radius: 2px; border: 1px solid var(--glass); }

        /* Input Area */
        .input-bar { padding: 15px; display: flex; gap: 10px; align-items: center; background: var(--card); border-top: 1px solid var(--glass); }
        .input-bar input { flex: 1; padding: 12px 18px; border-radius: 25px; border: 1px solid var(--glass); background: #000; color: white; outline: none; }
        .action-btn { width: 42px; height: 42px; border-radius: 50%; border: none; background: var(--primary); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; }

    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>LIVE CONNECT <i class="fa-solid fa-satellite-dish"></i></h1>
        <button onclick="logout()" style="background:none; border:none; color:var(--accent); font-size:18px;"><i class="fa-solid fa-power-off"></i></button>
    </header>

    <div id="loginPage" class="page active">
        <div style="text-align:center; padding-top:80px;">
            <div style="font-size: 70px; color: var(--primary); margin-bottom: 20px;"><i class="fa-solid fa-user-shield"></i></div>
            <h2>Secure Authentication</h2>
            <input type="text" id="usernameInput" placeholder="Enter Username..." style="width:85%; padding:15px; border-radius:15px; background:var(--card); color:white; border:1px solid var(--glass); margin-bottom:15px;">
            <button onclick="login()" style="width:85%; padding:15px; border-radius:15px; background:var(--primary); color:white; border:none; font-weight:bold; cursor:pointer;">Join Server</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div class="welcome-box">
            <div class="user-avatar-lg" id="userInitial">U</div>
            <h2 style="margin:0;">Welcome Back,</h2>
            <h1 id="welcomeUser" style="margin:5px 0; color:white; -webkit-text-fill-color: white;">Guest</h1>
            <p style="font-size:12px; opacity:0.9;">System Online | Encryption Active</p>
        </div>

        <h3 style="display:flex; align-items:center; gap:10px;"><i class="fa-solid fa-chart-line"></i> Activity Feed</h3>
        <div class="list-item" onclick="switchPage('usersPage', document.getElementById('userBtn'))">
            <div class="icon-circle" style="color:var(--success);"><i class="fa-solid fa-users"></i></div>
            <div style="flex:1;"><b>Online Users</b><br><small id="onlineCount">0 Users Live</small></div>
            <i class="fa-solid fa-chevron-right"></i>
        </div>
        <div class="list-item">
            <div class="icon-circle" style="color:var(--primary);"><i class="fa-solid fa-hashtag"></i></div>
            <div style="flex:1;"><b>Active Groups</b><br><small id="groupCount">0 Groups</small></div>
            <i class="fa-solid fa-chevron-right"></i>
        </div>
    </div>

    <div id="usersPage" class="page">
        <h3 style="display:flex; align-items:center; gap:10px;"><i class="fa-solid fa-user-astronaut"></i> Live Explorers</h3>
        <div id="userList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="chatHeader" style="text-align:center; font-size:12px; color:var(--primary); font-weight:bold; margin-bottom:10px;"></div>
        <div class="chat-box" id="chatBox"></div>
        <div class="input-bar">
            <button class="action-btn" style="background:var(--glass);"><i class="fa-solid fa-plus"></i></button>
            <input type="text" id="msgInput" placeholder="Secure message...">
            <button onclick="sendMessage()" class="action-btn"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="switchPage('homePage', this)" class="active">
            <i class="fa-solid fa-house-chimney"></i>
            <span>Home</span>
        </button>
        <button onclick="switchPage('chatPage', this)" id="chatBtn">
            <i class="fa-solid fa-comment-dots"></i>
            <span>Chat</span>
        </button>
        <button onclick="switchPage('usersPage', this)" id="userBtn">
            <i class="fa-solid fa-users-viewfinder"></i>
            <span>Users</span>
        </button>
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

    if(user) startApp();

    window.login = () => {
        const val = document.getElementById("usernameInput").value.trim();
        if(!val) return;
        user = val;
        localStorage.setItem("app_user", user);
        startApp();
    };

    function startApp() {
        document.getElementById("loginPage").style.display = "none";
        document.getElementById("homePage").classList.add("active");
        document.getElementById("navbar").style.display = "flex";
        
        // Welcome Dashboard Update
        document.getElementById("welcomeUser").innerText = user;
        document.getElementById("userInitial").innerText = user.charAt(0).toUpperCase();

        const uRef = ref(db, 'users/' + user);
        set(uRef, { online: true, lastSeen: serverTimestamp() });
        onDisconnect(uRef).set({ online: false });

        loadRealtime();
    }

    function loadRealtime() {
        onValue(ref(db, 'users'), snap => {
            const list = document.getElementById("userList");
            let count = 0; list.innerHTML = "";
            snap.forEach(u => {
                if(u.val().online) {
                    count++;
                    if(u.key !== user) {
                        const div = document.createElement("div");
                        div.className = "list-item";
                        div.innerHTML = `
                            <div class="icon-circle"><i class="fa-solid fa-user"></i></div>
                            <div style="flex:1;"><b>${u.key}</b><br><small style="color:var(--success);">• Online Now</small></div>
                            <i class="fa-solid fa-message" style="color:var(--primary);"></i>
                        `;
                        div.onclick = () => startChat(u.key);
                        list.appendChild(div);
                    }
                }
            });
            document.getElementById("onlineCount").innerText = count + " Users Live";
        });
    }

    window.startChat = (target) => {
        activeChat = [user, target].sort().join("_");
        document.getElementById("chatHeader").innerText = "CONNECTED TO: " + target;
        switchPage('chatPage', document.getElementById('chatBtn'));
        loadMsgs();
    }

    function loadMsgs() {
        onValue(ref(db, 'privateChats/' + activeChat), snap => {
            const box = document.getElementById("chatBox");
            box.innerHTML = "";
            snap.forEach(m => {
                const data = m.val();
                const isMe = data.from === user;
                const div = document.createElement("div");
                div.className = `msg ${isMe ? 'sent' : 'received'}`;
                div.innerHTML = data.text;
                if(isMe) div.onclick = () => { if(confirm("Unsend message?")) remove(ref(db, `privateChats/${activeChat}/${m.key}`)); };
                box.appendChild(div);
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    window.sendMessage = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value || !activeChat) return;
        push(ref(db, 'privateChats/' + activeChat), { from: user, text: inp.value });
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
