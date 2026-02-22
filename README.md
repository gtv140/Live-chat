<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Ultra Max 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #020617;
            --card: rgba(15, 23, 42, 0.8);
            --primary: #8b5cf6;
            --accent: #ec4899;
            --text: #f8fafc;
            --glass: rgba(255, 255, 255, 0.03);
            --success: #10b981;
        }

        body {
            margin: 0; font-family: 'Inter', sans-serif;
            background: radial-gradient(circle at 0% 0%, #1e1b4b 0%, #020617 100%);
            color: var(--text); height: 100vh; overflow: hidden;
        }

        .app-container { max-width: 480px; margin: auto; height: 100vh; display: flex; flex-direction: column; background: var(--glass); backdrop-filter: blur(20px); border: 1px solid var(--glass); position: relative; }

        /* Header */
        header {
            padding: 15px 20px; display: flex; justify-content: space-between; align-items: center;
            background: var(--card); border-bottom: 1px solid var(--glass); z-index: 10;
        }
        header h1 { font-size: 20px; font-weight: 800; background: linear-gradient(to right, #a78bfa, #f472b6); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin: 0; }

        /* Pages */
        .page { display: none; flex: 1; flex-direction: column; overflow-y: auto; padding: 20px; scrollbar-width: none; }
        .page.active { display: flex; }
        .page::-webkit-scrollbar { display: none; }

        /* Dashboard Cards */
        .stat-card { background: var(--card); padding: 20px; border-radius: 20px; margin-bottom: 15px; border: 1px solid var(--glass); display: flex; align-items: center; gap: 15px; transition: 0.3s; }
        .stat-card:hover { transform: translateY(-5px); border-color: var(--primary); }
        .icon-box { width: 50px; height: 50px; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 20px; }

        /* Chat UI */
        .chat-box { flex: 1; display: flex; flex-direction: column; gap: 12px; overflow-y: auto; padding-bottom: 20px; }
        .msg { max-width: 80%; padding: 10px 14px; border-radius: 18px; position: relative; font-size: 14px; line-height: 1.4; transition: 0.2s; cursor: pointer; }
        .msg.sent { align-self: flex-end; background: linear-gradient(135deg, var(--primary), #6d28d9); color: white; border-bottom-right-radius: 2px; }
        .msg.received { align-self: flex-start; background: var(--card); border-bottom-left-radius: 2px; border: 1px solid var(--glass); }
        .msg img { max-width: 100%; border-radius: 10px; margin-top: 5px; }
        .msg-time { font-size: 9px; opacity: 0.6; text-align: right; margin-top: 4px; }
        
        /* Reaction Bar */
        .reactions { position: absolute; bottom: -15px; right: 10px; display: flex; gap: 4px; background: #1e1b4b; padding: 2px 6px; border-radius: 10px; font-size: 12px; border: 1px solid var(--glass); }

        /* Input Bar */
        .input-bar { background: var(--card); padding: 15px; border-top: 1px solid var(--glass); display: flex; align-items: center; gap: 10px; }
        .input-bar input { flex: 1; padding: 12px 18px; border-radius: 25px; border: 1px solid var(--glass); background: #00000044; color: white; outline: none; }
        .action-btn { width: 40px; height: 40px; border-radius: 50%; border: none; background: var(--glass); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: 0.2s; }
        .send-btn { background: var(--primary); box-shadow: 0 4px 12px rgba(139, 92, 246, 0.3); }

        /* Bottom Nav */
        nav { display: flex; justify-content: space-around; background: var(--card); padding: 12px; border-top: 1px solid var(--glass); }
        nav button { background: none; border: none; font-size: 20px; color: #64748b; cursor: pointer; transition: 0.3s; }
        nav button.active { color: var(--primary); transform: translateY(-3px); }

        /* Reaction Picker */
        #emojiPicker { display: none; position: absolute; bottom: 80px; left: 50%; transform: translateX(-50%); background: #1e293b; padding: 10px; border-radius: 20px; gap: 10px; border: 1px solid var(--primary); z-index: 100; }
        #emojiPicker span { cursor: pointer; font-size: 20px; }
    </style>
</head>
<body>

<div class="app-container">
    <header>
        <h1>Live Connect <span style="font-size: 10px; opacity: 0.5;">ULTRA</span></h1>
        <button onclick="logout()" style="background:none; border:none; color:white;"><i class="fa-solid fa-right-from-bracket"></i></button>
    </header>

    <div id="emojiPicker">
        <span onclick="react('❤️')">❤️</span> <span onclick="react('😂')">😂</span> <span onclick="react('🔥')">🔥</span> <span onclick="react('👍')">👍</span>
    </div>

    <div id="loginPage" class="page active">
        <div style="text-align:center; padding-top: 80px;">
            <div style="font-size: 60px; margin-bottom: 20px;">⚡</div>
            <h2>Universal Access</h2>
            <input type="text" id="usernameInput" placeholder="Enter Username..." style="width:100%; padding:15px; border-radius:15px; background:var(--card); color:white; border:1px solid var(--glass); margin-bottom:20px;">
            <button onclick="login()" style="width:100%; padding:15px; border-radius:15px; background:var(--primary); color:white; border:none; font-weight:bold; cursor:pointer;">Initialize Link</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <h2 style="margin-bottom: 20px;">Dashboard</h2>
        <div class="stat-card">
            <div class="icon-box" style="background: rgba(139, 92, 246, 0.2); color: var(--primary);"><i class="fa-solid fa-user-group"></i></div>
            <div><div id="onlineCount" style="font-size: 20px; font-weight: bold;">0</div><div style="font-size: 12px; opacity: 0.6;">Explorers Online</div></div>
        </div>
        <div class="stat-card">
            <div class="icon-box" style="background: rgba(236, 72, 153, 0.2); color: var(--accent);"><i class="fa-solid fa-bolt"></i></div>
            <div><div style="font-size: 20px; font-weight: bold;">Stable</div><div style="font-size: 12px; opacity: 0.6;">Link Status</div></div>
        </div>
        <button onclick="openPage('chatPage', document.getElementById('chatBtn'))" style="padding:15px; border-radius:15px; border:none; background:var(--primary); color:white; font-weight:bold; cursor:pointer;">Launch Messenger</button>
    </div>

    <div id="chatPage" class="page">
        <div id="chatWith" style="font-size:12px; color:var(--primary); margin-bottom:10px; text-align:center;">Select a user to chat</div>
        <div class="chat-box" id="chatBox"></div>
        <div id="typingArea" style="height: 15px; font-size: 10px; color: var(--primary); font-style: italic;"></div>
        <div class="input-bar">
            <label for="imgInp" class="action-btn"><i class="fa-solid fa-image"></i></label>
            <input type="file" id="imgInp" hidden accept="image/*" onchange="sendImage(this)">
            <input type="text" id="msgInput" placeholder="Type a message..." oninput="handleTyping()">
            <button onclick="startVoice()" class="action-btn" id="micBtn"><i class="fa-solid fa-microphone"></i></button>
            <button onclick="sendMsg()" class="action-btn send-btn"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="usersPage" class="page">
        <h3>Active Explorers</h3>
        <div id="userList"></div>
    </div>

    <nav id="navBar" style="display:none;">
        <button onclick="openPage('homePage', this)" class="active"><i class="fa-solid fa-chart-pie"></i></button>
        <button onclick="openPage('chatPage', this)" id="chatBtn"><i class="fa-solid fa-comments"></i></button>
        <button onclick="openPage('usersPage', this)"><i class="fa-solid fa-users-rays"></i></button>
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
    let targetChat = "Global_Station";
    let selectedMsg = null;

    if(user) initializeAppUser();

    window.login = () => {
        let val = document.getElementById("usernameInput").value.trim();
        if(!val) return alert("Username required");
        user = val;
        localStorage.setItem("chat_user", user);
        initializeAppUser();
    };

    function initializeAppUser() {
        document.getElementById("loginPage").classList.remove("active");
        document.getElementById("homePage").classList.add("active");
        document.getElementById("navBar").style.display = "flex";

        const uRef = ref(db, 'users/' + user);
        set(uRef, { online: true, lastSeen: serverTimestamp() });
        onDisconnect(uRef).set({ online: false, lastSeen: serverTimestamp() });

        loadRealtimeData();
    }

    window.logout = () => {
        set(ref(db, 'users/' + user), { online: false });
        localStorage.removeItem("chat_user");
        location.reload();
    };

    function loadRealtimeData() {
        onValue(ref(db, 'users'), snap => {
            let count = 0;
            const list = document.getElementById("userList");
            list.innerHTML = "";
            snap.forEach(u => {
                if(u.val().online) {
                    count++;
                    if(u.key !== user) {
                        const div = document.createElement("div");
                        div.className = "stat-card";
                        div.innerHTML = `<div class="icon-box" style="background:var(--glass)">${u.key[0]}</div> <div><b>${u.key}</b><br><span style="color:var(--success); font-size:10px;">Connected</span></div>`;
                        div.onclick = () => { targetChat = u.key; openPage('chatPage', document.getElementById('chatBtn')); loadMsgs(); };
                        list.appendChild(div);
                    }
                }
            });
            document.getElementById("onlineCount").innerText = count;
        });
    }

    function loadMsgs() {
        const chatID = [user, targetChat].sort().join("_");
        document.getElementById("chatWith").innerText = "COMMUNICATION WITH: " + targetChat;
        onValue(ref(db, 'chats/' + chatID), snap => {
            const box = document.getElementById("chatBox");
            box.innerHTML = "";
            snap.forEach(m => {
                const data = m.val();
                const isMe = data.from === user;
                const div = document.createElement("div");
                div.className = `msg ${isMe ? 'sent' : 'received'}`;
                let content = data.image ? `<img src="${data.image}">` : data.text;
                div.innerHTML = `<b>${data.from}</b>${content}<div class="msg-time">${data.time}</div>`;
                div.onclick = (e) => { selectedMsg = m.key; document.getElementById("emojiPicker").style.display = "flex"; };
                box.appendChild(div);
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    window.sendMsg = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value) return;
        const chatID = [user, targetChat].sort().join("_");
        push(ref(db, 'chats/' + chatID), {
            from: user,
            text: inp.value,
            time: new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})
        });
        inp.value = "";
    };

    window.sendImage = (inp) => {
        const file = inp.files[0];
        const reader = new FileReader();
        reader.onload = (e) => {
            const chatID = [user, targetChat].sort().join("_");
            push(ref(db, 'chats/' + chatID), { from: user, image: e.target.result, time: 'Now' });
        };
        reader.readAsDataURL(file);
    };

    window.react = (emoji) => {
        const chatID = [user, targetChat].sort().join("_");
        set(ref(db, `chats/${chatID}/${selectedMsg}/reaction`), emoji);
        document.getElementById("emojiPicker").style.display = "none";
    };

    window.openPage = (id, btn) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        if(id === 'chatPage') loadMsgs();
    };

    // Typing logic & Voice logic can be expanded here
</script>
</body>
</html>
