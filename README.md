<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Infinity Pro 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #05050a; --card: #11111e;
            --primary: #8b5cf6; --accent: #f43f5e;
            --text: #ffffff; --glass: rgba(255, 255, 255, 0.05);
            --success: #22c55e;
        }

        body, html {
            margin: 0; padding: 0; height: 100%; width: 100%;
            font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text);
            overflow: hidden;
        }

        .app-shell {
            display: flex; flex-direction: column; height: 100vh;
            max-width: 500px; margin: 0 auto; background: var(--bg); position: relative;
        }

        /* HEADER */
        header {
            padding: 15px; background: var(--card); border-bottom: 1px solid var(--glass);
            display: flex; justify-content: space-between; align-items: center; z-index: 10;
        }
        header h1 { font-size: 1.1rem; margin: 0; background: linear-gradient(90deg, #a78bfa, #f472b6); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: 800; }

        /* CONTENT AREA */
        .content { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 140px; }
        .page { display: none; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* DASHBOARD */
        .welcome-hero {
            background: linear-gradient(135deg, #6d28d9, #db2777);
            padding: 30px 20px; border-radius: 25px; text-align: center; margin-bottom: 25px;
            box-shadow: 0 10px 20px rgba(0,0,0,0.3);
        }
        .user-icon { width: 60px; height: 60px; background: white; color: var(--primary); border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 28px; font-weight: bold; margin: 0 auto 10px; }

        /* CHAT BUBBLES */
        .chat-container { display: flex; flex-direction: column; gap: 12px; }
        .msg { padding: 12px 16px; border-radius: 18px; max-width: 80%; font-size: 14px; position: relative; word-wrap: break-word; }
        .sent { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 2px; }
        .received { align-self: flex-start; background: var(--card); border: 1px solid var(--glass); border-bottom-left-radius: 2px; }
        .msg b { display: block; font-size: 10px; opacity: 0.7; margin-bottom: 4px; }
        .msg img { max-width: 100%; border-radius: 10px; margin-top: 5px; }

        /* FIXED INPUT AREA */
        .input-area {
            position: absolute; bottom: 75px; left: 0; right: 0;
            padding: 12px; background: var(--card); display: flex; gap: 10px;
            border-top: 1px solid var(--glass); z-index: 100;
        }
        .input-area input {
            flex: 1; padding: 12px 18px; border-radius: 25px; border: 1px solid var(--glass);
            background: #000; color: white; outline: none;
        }
        .action-btn { width: 45px; height: 45px; border-radius: 50%; border: none; background: var(--primary); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 18px; }

        /* BOTTOM NAVIGATION */
        nav {
            position: absolute; bottom: 0; left: 0; right: 0;
            height: 75px; background: var(--card); border-top: 1px solid var(--glass);
            display: flex; justify-content: space-around; align-items: center; z-index: 100;
        }
        nav button {
            background: none; border: none; color: #555; font-size: 22px;
            display: flex; flex-direction: column; align-items: center; gap: 4px; cursor: pointer; transition: 0.3s;
        }
        nav button.active { color: var(--primary); transform: translateY(-5px); }
        nav button span { font-size: 10px; font-weight: bold; text-transform: uppercase; }

        /* USER LIST */
        .user-row {
            background: var(--card); padding: 15px; border-radius: 15px;
            margin-bottom: 10px; display: flex; align-items: center; gap: 15px;
            border: 1px solid var(--glass); cursor: pointer;
        }
        .status-dot { width: 10px; height: 10px; background: var(--success); border-radius: 50%; box-shadow: 0 0 10px var(--success); }
    </style>
</head>
<body>

<div class="app-shell">
    <header>
        <h1>INFINITY CONNECT <i class="fa-solid fa-bolt"></i></h1>
        <i class="fa-solid fa-right-from-bracket" onclick="logout()" style="color:var(--accent); cursor:pointer;"></i>
    </header>

    <div class="content" id="scrollArea">
        <div id="home" class="page active-page">
            <div class="welcome-hero">
                <div class="user-icon" id="myAvatar">?</div>
                <h2 style="margin:0;">Welcome,</h2>
                <h1 id="userNameDisplay" style="margin:5px 0; -webkit-text-fill-color:white;">Explorer</h1>
            </div>
            <h3>Quick Stats</h3>
            <div class="user-row" onclick="openPage('users')">
                <i class="fa-solid fa-users" style="color:var(--success)"></i>
                <div style="flex:1"><b>Live Network</b><br><small id="onlineCount">Detecting...</small></div>
                <i class="fa-solid fa-chevron-right"></i>
            </div>
            <div class="user-row" onclick="startGlobal()">
                <i class="fa-solid fa-earth-asia" style="color:var(--primary)"></i>
                <div style="flex:1"><b>Global Room</b><br><small>Chat with everyone</small></div>
                <i class="fa-solid fa-chevron-right"></i>
            </div>
        </div>

        <div id="users" class="page">
            <h3><i class="fa-solid fa-tower-broadcast"></i> Active Explorers</h3>
            <div id="userList"></div>
        </div>

        <div id="chat" class="page">
            <h4 id="chatTitle" style="text-align:center; color:var(--primary); margin-top:0;">Global Room</h4>
            <div class="chat-container" id="chatBox"></div>
        </div>
    </div>

    <div class="input-area" id="inputBar" style="display:none;">
        <label for="imgInp" class="action-btn" style="background:var(--glass)"><i class="fa-solid fa-image"></i></label>
        <input type="file" id="imgInp" hidden onchange="sendImg(this)">
        <input type="text" id="msgInput" placeholder="Write a message...">
        <button onclick="sendMsg()" class="action-btn"><i class="fa-solid fa-paper-plane"></i></button>
    </div>

    <nav>
        <button onclick="openPage('home')" id="homeBtn" class="active"><i class="fa-solid fa-house"></i><span>Home</span></button>
        <button onclick="openPage('chat')" id="chatBtn"><i class="fa-solid fa-comments"></i><span>Chat</span></button>
        <button onclick="openPage('users')" id="usersBtn"><i class="fa-solid fa-user-astronaut"></i><span>Users</span></button>
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
    let activeChatID = "Global_Group";
    let isPrivate = false;

    if(!user) {
        user = prompt("Enter your username:");
        if(user) localStorage.setItem("chat_user", user);
        else location.reload();
    }

    // Initialize App
    document.getElementById('userNameDisplay').innerText = user;
    document.getElementById('myAvatar').innerText = user[0].toUpperCase();
    set(ref(db, 'users/' + user), { online: true });
    onDisconnect(ref(db, 'users/' + user)).set({ online: false });

    // Page Switching Logic
    window.openPage = (pageId) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
        document.getElementById(pageId).classList.add('active-page');
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        document.getElementById(pageId + 'Btn').classList.add('active');
        document.getElementById('inputBar').style.display = (pageId === 'chat') ? 'flex' : 'none';
        if(pageId === 'chat') loadMessages();
    };

    // Load Users
    onValue(ref(db, 'users'), snap => {
        const list = document.getElementById("userList");
        let count = 0; list.innerHTML = "";
        snap.forEach(u => {
            if(u.val().online) {
                count++;
                if(u.key !== user) {
                    const div = document.createElement("div");
                    div.className = "user-row";
                    div.innerHTML = `<b>${u.key}</b> <div class="status-dot"></div>`;
                    div.onclick = () => startPrivate(u.key);
                    list.appendChild(div);
                }
            }
        });
        document.getElementById("onlineCount").innerText = count + " Explorers Live";
    });

    window.startGlobal = () => {
        activeChatID = "Global_Group";
        isPrivate = false;
        document.getElementById('chatTitle').innerText = "GLOBAL ROOM";
        openPage('chat');
    };

    window.startPrivate = (target) => {
        activeChatID = [user, target].sort().join("_");
        isPrivate = true;
        document.getElementById('chatTitle').innerText = "PRIVATE: " + target;
        openPage('chat');
    };

    // Messaging Logic
    function loadMessages() {
        const path = isPrivate ? 'private/' + activeChatID : 'global/' + activeChatID;
        onValue(ref(db, path), snap => {
            const box = document.getElementById("chatBox");
            box.innerHTML = "";
            snap.forEach(m => {
                const data = m.val();
                const isMe = data.from === user;
                const div = document.createElement("div");
                div.className = `msg ${isMe ? 'sent' : 'received'}`;
                let content = data.img ? `<img src="${data.img}">` : data.text;
                div.innerHTML = `<b>${data.from}</b>${content}`;
                if(isMe) div.onclick = () => { if(confirm("Delete message?")) remove(ref(db, path + '/' + m.key)); };
                box.appendChild(div);
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    window.sendMsg = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value) return;
        const path = isPrivate ? 'private/' + activeChatID : 'global/' + activeChatID;
        push(ref(db, path), { from: user, text: inp.value });
        inp.value = "";
    };

    window.sendImg = (inp) => {
        const reader = new FileReader();
        reader.onload = (e) => {
            const path = isPrivate ? 'private/' + activeChatID : 'global/' + activeChatID;
            push(ref(db, path), { from: user, img: e.target.result });
        };
        reader.readAsDataURL(inp.files[0]);
    };

    window.logout = () => {
        set(ref(db, 'users/' + user), { online: false });
        localStorage.removeItem("chat_user");
        location.reload();
    };
</script>

</body>
</html>
