<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect Ultra 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #050505;
            --card: rgba(20, 20, 25, 0.9);
            --primary: #7c3aed;
            --accent: #db2777;
            --text: #ffffff;
            --glass: rgba(255, 255, 255, 0.08);
            --success: #22c55e;
        }

        body {
            margin: 0; font-family: 'Segoe UI', sans-serif;
            background: #000; color: var(--text); height: 100vh; overflow: hidden;
        }

        .app { max-width: 450px; margin: auto; height: 100vh; display: flex; flex-direction: column; background: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png'); background-attachment: fixed; }

        header {
            padding: 15px 20px; background: var(--card); backdrop-filter: blur(15px);
            display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--glass);
        }

        .page { display: none; flex: 1; flex-direction: column; overflow-y: auto; padding: 15px; animation: fadeIn 0.4s ease; }
        .active { display: flex; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* ADVANCED CHAT BUBBLES */
        .chat-box { flex: 1; display: flex; flex-direction: column; gap: 10px; overflow-y: auto; padding-bottom: 20px; }
        .msg-wrap { display: flex; flex-direction: column; max-width: 85%; position: relative; }
        .msg { padding: 10px 15px; border-radius: 18px; font-size: 14px; position: relative; transition: 0.3s; cursor: pointer; }
        .sent { align-self: flex-end; background: var(--primary); border-bottom-right-radius: 2px; }
        .received { align-self: flex-start; background: var(--card); border-bottom-left-radius: 2px; border: 1px solid var(--glass); }
        
        .reactions { position: absolute; bottom: -12px; right: 10px; display: flex; gap: 3px; background: #222; border-radius: 10px; padding: 2px 5px; font-size: 12px; border: 1px solid var(--glass); }
        .msg-info { font-size: 9px; opacity: 0.6; margin-top: 4px; display: flex; justify-content: space-between; }

        /* REACTION PICKER */
        .reaction-picker { display: none; position: fixed; background: #1a1a1a; padding: 10px; border-radius: 30px; gap: 10px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); z-index: 1000; border: 1px solid var(--glass); }
        .reaction-picker span { cursor: pointer; transition: 0.2s; font-size: 20px; }
        .reaction-picker span:hover { transform: scale(1.4); }

        /* INPUT AREA */
        .input-area { background: var(--card); padding: 12px; display: flex; gap: 10px; align-items: center; border-top: 1px solid var(--glass); }
        .input-area input { flex: 1; padding: 12px 18px; border-radius: 25px; border: none; background: #222; color: white; outline: none; }
        .btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--primary); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; }

        /* USER LIST */
        .user-card { background: var(--card); padding: 12px; border-radius: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 12px; border: 1px solid var(--glass); }
        .avatar { width: 45px; height: 45px; border-radius: 50%; background: linear-gradient(45deg, var(--primary), var(--accent)); display: flex; align-items: center; justify-content: center; font-weight: bold; }
        .status-text { font-size: 11px; color: var(--success); }

        nav { display: flex; justify-content: space-around; background: var(--card); padding: 10px; border-top: 1px solid var(--glass); }
        nav button { background: none; border: none; color: #888; font-size: 20px; cursor: pointer; }
        nav button.active { color: var(--primary); }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h2 style="margin:0; font-size:18px;">Live Connect <span style="color:var(--primary)">Ultra</span></h2>
        <div id="userPresence" style="font-size:11px; opacity:0.7;"></div>
    </header>

    <div id="reactionPicker" class="reaction-picker">
        <span onclick="addReaction('❤️')">❤️</span>
        <span onclick="addReaction('😂')">😂</span>
        <span onclick="addReaction('😮')">😮</span>
        <span onclick="addReaction('👍')">👍</span>
        <span onclick="addReaction('🔥')">🔥</span>
    </div>

    <div id="loginPage" class="page active">
        <div style="text-align:center; margin-top:100px;">
            <i class="fa-solid fa-shield-halved" style="font-size:60px; color:var(--primary); margin-bottom:20px;"></i>
            <h3>Enter Username</h3>
            <input type="text" id="usernameInput" placeholder="Ex: Rahul_123" style="width:80%; padding:15px; border-radius:15px; background:#111; color:white; border:1px solid var(--glass); margin-bottom:20px;">
            <button onclick="login()" style="width:80%; padding:15px; border-radius:15px; background:var(--primary); color:white; border:none; font-weight:bold;">Start Chatting</button>
        </div>
    </div>

    <div id="chatPage" class="page">
        <div class="chat-box" id="chatBox"></div>
        <div id="typingStatus" style="font-size:11px; color:var(--primary); height:20px;"></div>
        <div class="input-area">
            <label for="imgFile" style="cursor:pointer; color:#888;"><i class="fa-solid fa-paperclip"></i></label>
            <input type="file" id="imgFile" hidden onchange="uploadFile(this)">
            <input type="text" id="msgInput" placeholder="Message..." oninput="isTyping()">
            <button onclick="sendMsg()" class="btn"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <div id="usersPage" class="page">
        <h3>Active Explorers</h3>
        <div id="userList"></div>
    </div>

    <nav id="navbar" style="display:none;">
        <button onclick="openPage('chatPage', this)" class="active"><i class="fa-solid fa-message"></i></button>
        <button onclick="openPage('usersPage', this)"><i class="fa-solid fa-users"></i></button>
    </nav>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
    import { getDatabase, ref, set, push, onValue, remove, onDisconnect, serverTimestamp, update } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

    const firebaseConfig = {
        apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
        authDomain: "live-chat-b810c.firebaseapp.com",
        databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
        projectId: "live-chat-b810c"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    let user = localStorage.getItem("ultra_user");
    let activeChat = "Global_Group";
    let selectedMsgId = null;

    if(user) startApp();

    window.login = () => {
        let val = document.getElementById("usernameInput").value.trim();
        if(!val) return;
        user = val;
        localStorage.setItem("ultra_user", user);
        startApp();
    };

    function startApp() {
        document.getElementById("loginPage").classList.remove("active");
        document.getElementById("chatPage").classList.add("active");
        document.getElementById("navbar").style.display = "flex";

        const presenceRef = ref(db, 'users/' + user);
        set(presenceRef, { online: true, lastSeen: serverTimestamp() });
        onDisconnect(presenceRef).set({ online: false, lastSeen: serverTimestamp() });

        loadMessages();
        loadUsers();
    }

    function loadMessages() {
        onValue(ref(db, 'chats/' + activeChat), (snap) => {
            const box = document.getElementById("chatBox");
            box.innerHTML = "";
            snap.forEach(child => {
                const data = child.val();
                const isMe = data.from === user;
                const div = document.createElement("div");
                div.className = "msg-wrap " + (isMe ? "sent-wrap" : "");
                div.style.alignSelf = isMe ? "flex-end" : "flex-start";
                
                let reactionsHTML = "";
                if(data.reactions) {
                    reactionsHTML = `<div class="reactions">` + Object.values(data.reactions).join("") + `</div>`;
                }

                div.innerHTML = `
                    <div class="msg ${isMe ? 'sent' : 'received'}" onclick="showMenu('${child.key}', event)">
                        <b style="font-size:10px; display:block; margin-bottom:4px;">${data.from}</b>
                        ${data.text}
                        <div class="msg-info">
                            <span>${data.time}</span>
                            ${isMe ? '<i class="fa-solid fa-check-double"></i>' : ''}
                        </div>
                        ${reactionsHTML}
                    </div>
                `;
                box.appendChild(div);
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    window.showMenu = (id, e) => {
        selectedMsgId = id;
        const picker = document.getElementById("reactionPicker");
        picker.style.display = "flex";
        picker.style.top = (e.clientY - 50) + "px";
        picker.style.left = "50px";
        
        // Hide after 3 seconds if no reaction
        setTimeout(() => picker.style.display = "none", 3000);
    };

    window.addReaction = (emoji) => {
        if(!selectedMsgId) return;
        set(ref(db, `chats/${activeChat}/${selectedMsgId}/reactions/${user}`), emoji);
        document.getElementById("reactionPicker").style.display = "none";
    };

    window.sendMsg = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value) return;
        push(ref(db, 'chats/' + activeChat), {
            from: user,
            text: inp.value,
            time: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})
        });
        inp.value = "";
    };

    function loadUsers() {
        onValue(ref(db, 'users'), snap => {
            const list = document.getElementById("userList");
            list.innerHTML = "";
            snap.forEach(u => {
                const data = u.val();
                const card = document.createElement("div");
                card.className = "user-card";
                card.innerHTML = `
                    <div class="avatar">${u.key.charAt(0)}</div>
                    <div>
                        <div style="font-weight:bold;">${u.key}</div>
                        <div class="status-text">${data.online ? 'Online Now' : 'Last seen some time ago'}</div>
                    </div>
                `;
                list.appendChild(card);
            });
        });
    }

    window.openPage = (id, btn) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    };

    window.isTyping = () => {
        // Typing logic here
    };
</script>
</body>
</html>
