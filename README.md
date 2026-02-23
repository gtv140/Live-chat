<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Pro 🚀</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #09090b; --card: #18181b; --text: #fafafa;
            --primary: #6366f1; --accent: #ec4899; --success: #22c55e;
            --glass: rgba(255, 255, 255, 0.03);
        }
        * { box-sizing: border-box; font-family: 'Segoe UI', Roboto, sans-serif; -webkit-tap-highlight-color: transparent; }
        body { margin: 0; background: var(--bg); color: var(--text); height: 100dvh; display: flex; flex-direction: column; overflow: hidden; }

        /* Header */
        header { padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; background: var(--card); border-bottom: 1px solid #27272a; flex-shrink: 0; }
        header h1 { margin: 0; font-size: 20px; font-weight: 800; background: linear-gradient(to right, var(--primary), var(--accent)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .top-icons i { font-size: 18px; margin-left: 15px; color: #a1a1aa; cursor: pointer; }

        /* App Layout */
        .app { flex: 1; display: flex; flex-direction: column; max-width: 500px; margin: auto; width: 100%; overflow: hidden; position: relative; }
        .page { display: none; padding: 15px; flex: 1; overflow-y: auto; height: 100%; }
        .page.active { display: block; }

        /* Modern Cards */
        .card { background: var(--card); border-radius: 20px; padding: 20px; margin-bottom: 15px; border: 1px solid #27272a; }
        .hero { background: linear-gradient(135deg, var(--primary), #4f46e5); color: white; border: none; box-shadow: 0 10px 20px rgba(0,0,0,0.3); }
        .stat-box { display: flex; justify-content: space-between; align-items: center; background: #000; padding: 12px; border-radius: 12px; margin-top: 8px; }

        /* Chat System */
        .chat-container { display: flex; flex-direction: column; height: 100%; }
        #chatBox { flex: 1; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; padding: 10px 0; }
        .msg { max-width: 85%; padding: 10px 14px; border-radius: 18px; position: relative; font-size: 14px; background: #27272a; align-self: flex-start; border-bottom-left-radius: 4px; }
        .msg.me { align-self: flex-end; background: var(--primary); border-bottom-left-radius: 18px; border-bottom-right-radius: 4px; }
        .msg img { width: 100%; border-radius: 12px; margin-top: 5px; }
        .msg .sender { font-size: 10px; opacity: 0.6; font-weight: bold; margin-bottom: 3px; display: block; }
        
        /* Reactions & Reply */
        .reply-box { background: rgba(0,0,0,0.2); border-left: 3px solid var(--accent); padding: 5px 8px; border-radius: 5px; font-size: 11px; margin-bottom: 5px; }
        .reactions { font-size: 12px; margin-top: 5px; background: rgba(0,0,0,0.1); padding: 2px 6px; border-radius: 10px; width: fit-content; }
        .msg-tools { margin-top: 5px; display: flex; gap: 10px; opacity: 0.5; font-size: 11px; }

        /* Input Area */
        .input-area { background: var(--card); padding: 10px 15px; display: flex; gap: 10px; align-items: center; border-top: 1px solid #27272a; flex-shrink: 0; }
        .input-area input { flex: 1; background: #09090b; border: 1px solid #3f3f46; padding: 12px 18px; border-radius: 25px; color: white; outline: none; }
        .input-area i { font-size: 20px; color: var(--primary); cursor: pointer; }

        /* Nav */
        nav { display: flex; justify-content: space-around; background: var(--card); padding: 12px 0; border-top: 1px solid #27272a; flex-shrink: 0; }
        nav i { font-size: 20px; color: #52525b; cursor: pointer; transition: 0.2s; }
        nav i.active { color: var(--primary); filter: drop-shadow(0 0 5px var(--primary)); }

        /* Admin UI */
        .admin-badge { background: #facc15; color: black; font-size: 9px; padding: 2px 5px; border-radius: 4px; font-weight: bold; margin-left: 5px; }
        .admin-panel { border: 1px dashed var(--accent); padding: 15px; border-radius: 15px; margin-top: 15px; background: rgba(236, 72, 153, 0.05); }
    </style>
</head>
<body>

<div class="app">
    <header>
        <h1>Live Connect</h1>
        <div class="top-icons">
            <i class="fa-solid fa-moon" onclick="document.body.classList.toggle('light')"></i>
            <i class="fa-solid fa-power-off" onclick="logout()"></i>
        </div>
    </header>

    <div id="home" class="page active">
        <div class="card hero">
            <h2 style="margin:0;">Welcome, <span id="dashUser">...</span>!</h2>
            <p style="font-size:14px; opacity:0.9;">System Online | Connection Secure</p>
        </div>
        
        <div class="stat-box"><span>Online Nodes</span><b id="onlineCount" style="color:var(--success)">0</b></div>
        <div class="stat-box"><span>Active Channels</span><b id="groupCount" style="color:var(--primary)">0</b></div>

        <div id="adminControls" style="display:none;" class="admin-panel">
            <h4 style="color:var(--accent); margin-top:0;">🛡️ Admin Terminal</h4>
            <input id="bcInput" placeholder="System Announcement..." style="width:100%; padding:10px; border-radius:10px; background:#000; border:1px solid #333; color:white; margin-bottom:10px;">
            <button onclick="sendBC()" style="width:100%; background:var(--primary); color:white; border:none; padding:10px; border-radius:10px; font-weight:bold;">BROADCAST</button>
            <div id="adminUserList" style="margin-top:15px;"></div>
        </div>
    </div>

    <div id="chat" class="page">
        <div id="chatHeader" style="font-weight:bold; color:var(--primary); padding-bottom:10px; border-bottom:1px solid #27272a;">Select a user</div>
        <div id="chatBox"></div>
        <div id="replyBar" style="display:none; padding:8px; background:var(--glass); border-left:3px solid var(--accent); font-size:12px;">
            <span id="replyPreview"></span> <i class="fa-solid fa-xmark" style="float:right" onclick="setReply(null)"></i>
        </div>
        <div class="input-area">
            <label for="imgInp"><i class="fa-solid fa-paperclip"></i></label>
            <input type="file" id="imgInp" hidden accept="image/*" onchange="sendImage(this)">
            <input id="msgInput" placeholder="Message...">
            <i class="fa-solid fa-paper-plane" onclick="sendMsg()"></i>
        </div>
    </div>

    <div id="users" class="page">
        <h4 style="color:#71717a; font-size:11px; letter-spacing:1px;">ACTIVE USERS</h4>
        <div id="userList"></div>
        <h4 style="color:#71717a; font-size:11px; letter-spacing:1px; margin-top:20px;">GROUPS</h4>
        <div id="groupList"></div>
        <div class="input-area" style="margin-top:10px; border-radius:15px;">
            <input id="newGroup" placeholder="Group name...">
            <i class="fa-solid fa-plus" onclick="createGroup()"></i>
        </div>
    </div>

    <nav>
        <i class="fa-solid fa-house active" onclick="openPage('home',this)"></i>
        <i class="fa-solid fa-comment-dots" onclick="openPage('chat',this)"></i>
        <i class="fa-solid fa-user-group" onclick="openPage('users',this)"></i>
    </nav>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
    import { getDatabase, ref, set, push, onValue, remove, update, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

    const firebaseConfig = {
        apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
        databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
        projectId: "live-chat-b810c",
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    let user = localStorage.getItem("chat_user") || prompt("Enter Username:");
    if(!user) location.reload();
    localStorage.setItem("chat_user", user);

    const admins = ["Muhammad Nazim", "Admin786"];
    const isAdmin = admins.includes(user);

    document.getElementById("dashUser").textContent = user;
    if(isAdmin) document.getElementById("adminControls").style.display = "block";

    const myStatus = ref(db, "users/" + user);
    set(myStatus, {name: user, online: true});
    onDisconnect(myStatus).update({online: false});

    window.openPage = (id, el) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('nav i').forEach(i => i.classList.remove('active'));
        el.classList.add('active');
    };

    let curChat = "";
    let isGroup = false;
    let replyMsg = null;

    // Load Data
    onValue(ref(db, "users"), snap => {
        let count = 0;
        userList.innerHTML = "";
        if(isAdmin) adminUserList.innerHTML = "<h5>Manage Users:</h5>";
        snap.forEach(u => {
            if(u.val().online) {
                count++;
                if(u.key !== user) {
                    userList.innerHTML += `<div class="card" onclick="startChat('${u.key}', false)" style="padding:15px;">🟢 ${u.key} ${admins.includes(u.key)?'<span class="admin-badge">ADMIN</span>':''}</div>`;
                }
            }
            if(isAdmin && u.key !== user) {
                adminUserList.innerHTML += `<div class="stat-box">${u.key} <button onclick="ban('${u.key}')" style="background:red; border:none; color:white; padding:4px 8px; border-radius:5px;">Ban</button></div>`;
            }
        });
        document.getElementById("onlineCount").textContent = count;
    });

    window.startChat = (target, gFlag) => {
        curChat = target; isGroup = gFlag;
        document.getElementById("chatHeader").textContent = target;
        openPage('chat', document.querySelectorAll('nav i')[1]);
        loadMessages();
    };

    function loadMessages() {
        const path = (isGroup ? "groupChats/" : "chats/") + [user, curChat].sort().join("_");
        onValue(ref(db, path), snap => {
            const box = document.getElementById("chatBox");
            box.innerHTML = "";
            snap.forEach(m => {
                const d = m.val();
                const div = document.createElement("div");
                div.className = `msg ${d.from === user ? 'me' : ''}`;
                div.innerHTML = `
                    <span class="sender">${d.from}</span>
                    ${d.reply ? `<div class="reply-box">🔙 ${d.reply}</div>` : ''}
                    ${d.type === 'img' ? `<img src="${d.text}">` : `<div>${d.text}</div>`}
                    <div class="reactions">${d.reaction || ''}</div>
                    <div class="msg-tools">
                        <span onclick="setReply('${d.text}')">Reply</span>
                        <span onclick="react('${path}','${m.key}','❤️')">❤️</span>
                        <span onclick="react('${path}','${m.key}','👍')">👍</span>
                        ${isAdmin || d.from === user ? `<span onclick="delMsg('${path}','${m.key}')">🗑️</span>` : ''}
                    </div>
                `;
                box.appendChild(div);
            });
            box.scrollTop = box.scrollHeight;
        });
    }

    window.sendMsg = () => {
        const inp = document.getElementById("msgInput");
        if(!inp.value || !curChat) return;
        const path = (isGroup ? "groupChats/" : "chats/") + [user, curChat].sort().join("_");
        push(ref(db, path), {from: user, text: inp.value, type: 'text', reply: replyMsg});
        inp.value = ""; setReply(null);
    };

    window.setReply = (txt) => {
        replyMsg = txt;
        const bar = document.getElementById("replyBar");
        if(txt) { bar.style.display="block"; document.getElementById("replyPreview").textContent = "Replying to: " + txt; }
        else { bar.style.display="none"; }
    };

    window.react = (p, k, e) => { update(ref(db, p + "/" + k), {reaction: e}); };
    window.delMsg = (p, k) => { if(confirm("Delete?")) remove(ref(db, p + "/" + k)); };
    window.sendBC = () => {
        const m = document.getElementById("bcInput").value;
        if(m) set(ref(db, "broadcast"), {msg: m});
    };
    onValue(ref(db, "broadcast"), snap => { if(snap.exists()) alert("📢 ADMIN: " + snap.val().msg); });

    window.sendImage = (e) => {
        const reader = new FileReader();
        reader.onload = (f) => {
            const path = (isGroup ? "groupChats/" : "chats/") + [user, curChat].sort().join("_");
            push(ref(db, path), {from: user, text: f.target.result, type: 'img'});
        };
        reader.readAsDataURL(e.files[0]);
    };

    window.createGroup = () => {
        const g = document.getElementById("newGroup").value;
        if(g) set(ref(db, "groups/" + g), {created: user});
    };

    onValue(ref(db, "groups"), snap => {
        groupList.innerHTML = "";
        document.getElementById("groupCount").textContent = snap.size || 0;
        snap.forEach(g => {
            groupList.innerHTML += `<div class="card" onclick="startChat('${g.key}', true)"># ${g.key}</div>`;
        });
    });

    window.ban = (t) => { if(confirm("Ban " + t + "?")) remove(ref(db, "users/" + t)); };
    window.logout = () => { localStorage.removeItem("chat_user"); location.reload(); };
</script>
</body>
</html>
