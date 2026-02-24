<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>ChatPro OS | Enterprise</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        :root { --primary: #4f46e5; --sidebar: #1e1b4b; }
        
        html, body { height: 100%; margin: 0; background: #0f172a; font-family: 'Inter', sans-serif; color: white; overflow: hidden; }
        .app-shell { display: flex; height: 100dvh; width: 100%; }

        /* Slack Style Sidebar */
        .sidebar { 
            width: 260px; background: var(--sidebar); display: flex; flex-direction: column; 
            transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1); z-index: 50; border-right: 1px solid rgba(255,255,255,0.05);
        }

        /* Message Area */
        .main-content { flex: 1; display: flex; flex-direction: column; background: #0f172a; position: relative; }
        #chat-flow { 
            flex: 1; overflow-y: auto; padding: 24px; display: flex; flex-direction: column; gap: 8px;
            background-image: radial-gradient(circle at 2px 2px, rgba(255,255,255,0.02) 1px, transparent 0);
            background-size: 32px 32px;
        }

        /* Message Bubbles */
        .msg-row { display: flex; flex-direction: column; margin-bottom: 4px; }
        .msg { max-width: 80%; padding: 10px 14px; border-radius: 12px; font-size: 14px; position: relative; }
        .msg.mine { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 2px; }
        .msg.theirs { align-self: flex-start; background: #1e293b; color: #f1f5f9; border-bottom-left-radius: 2px; }
        
        .status-tag { font-size: 10px; margin-top: 2px; opacity: 0.6; display: flex; align-items: center; gap: 4px; }
        .blue-tick { color: #38bdf8; }

        /* Typing Animation */
        .typing { font-size: 11px; color: #818cf8; padding: 10px; font-style: italic; }

        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 80%; }
            .sidebar.active { left: 0; box-shadow: 20px 0 50px rgba(0,0,0,0.5); }
        }

        /* Input Area Fix */
        .input-container { padding: 16px; background: #0f172a; border-top: 1px solid rgba(255,255,255,0.05); }
        .input-pill { background: #1e293b; border: 1px solid rgba(255,255,255,0.1); border-radius: 12px; display: flex; align-items: center; padding: 4px 12px; }
        
        .user-item { padding: 10px; border-radius: 8px; cursor: pointer; display: flex; align-items: center; gap: 10px; transition: 0.2s; }
        .user-item:hover { background: rgba(255,255,255,0.05); }
        .user-item.active { background: rgba(79, 70, 229, 0.2); color: white; }
        .online-dot { width: 8px; height: 8px; background: #22c55e; border-radius: 50%; box-shadow: 0 0 8px #22c55e; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[100] bg-[#0f172a] flex items-center justify-center p-6">
        <div class="bg-[#1e1b4b] border border-white/10 p-10 rounded-3xl shadow-2xl w-full max-w-sm text-center">
            <div class="w-16 h-16 bg-indigo-600 rounded-2xl flex items-center justify-center mx-auto mb-6">
                <i class="fa-solid fa-layer-group text-white text-2xl"></i>
            </div>
            <h2 class="text-3xl font-bold mb-2">ChatPro <span class="text-indigo-500">v10</span></h2>
            <p class="text-slate-400 text-sm mb-8">Ready to sync with your team?</p>
            <input id="username-input" type="text" placeholder="Your Workspace Name" class="w-full bg-slate-900 border border-white/5 p-4 rounded-xl mb-4 text-center text-white outline-none focus:border-indigo-500">
            <button id="join-btn" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-4 rounded-xl transition-all">Connect</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 border-b border-white/5">
                <h1 class="font-black text-xl tracking-tighter italic">TEAM SYNC</h1>
            </div>
            <div class="flex-1 overflow-y-auto p-4 space-y-6">
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-3">Channels</p>
                    <div onclick="switchChat('global')" id="global-tab" class="user-item active font-bold text-sm">
                        <i class="fa-solid fa-hashtag text-indigo-400"></i> global-general
                    </div>
                </div>
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-3">Direct Messages</p>
                    <div id="users-list" class="space-y-1"></div>
                </div>
            </div>
            <div class="p-4 bg-black/20 flex items-center gap-3">
                <div class="w-10 h-10 rounded-lg bg-indigo-600 flex items-center justify-center font-bold" id="u-avatar">?</div>
                <div>
                    <p class="text-xs font-bold" id="u-name">User</p>
                    <div class="flex items-center gap-1"><div class="online-dot"></div><span class="text-[9px] text-slate-400">Active Now</span></div>
                </div>
            </div>
        </aside>

        <main class="main-content">
            <header class="h-16 border-b border-white/5 flex items-center justify-between px-6 bg-slate-950/20 backdrop-blur-md">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-bars-staggered"></i></button>
                    <h2 id="chat-title" class="font-bold text-sm"># global-general</h2>
                </div>
                <div class="flex gap-4 text-slate-400 text-sm">
                    <i class="fa-solid fa-phone hover:text-white cursor-pointer"></i>
                    <i class="fa-solid fa-video hover:text-white cursor-pointer"></i>
                </div>
            </header>

            <div id="chat-flow"></div>
            <div id="typing-area" class="typing px-6"></div>

            <div class="input-container">
                <form id="msg-form" class="max-w-5xl mx-auto input-pill">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="p-2 text-slate-400 hover:text-white"><i class="fa-solid fa-paperclip"></i></button>
                    <input id="msg-input" type="text" placeholder="Message #global-general" class="flex-1 bg-transparent p-3 outline-none text-sm" oninput="handleTyping()">
                    <button class="bg-indigo-600 text-white px-4 py-2 rounded-lg font-bold text-xs">SEND</button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        const firebaseConfig = {
            apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
            authDomain: "live-chat-b810c.firebaseapp.com",
            databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
            projectId: "live-chat-b810c",
            storageBucket: "live-chat-b810c.firebasestorage.app",
            messagingSenderId: "555058795334",
            appId: "1:555058795334:web:f668887409800c32970b47"
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const storage = getStorage(app);
        let myName = "", activeChat = "global", typingTimeout;

        window.onload = () => { if(localStorage.getItem('chatProUser')) init(localStorage.getItem('chatProUser')); };
        document.getElementById('join-btn').onclick = () => { 
            const n = document.getElementById('username-input').value.trim(); 
            if(n){ localStorage.setItem('chatProUser', n); init(n); }
        };

        function init(n) {
            myName = n;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('u-name').innerText = n;
            document.getElementById('u-avatar').innerText = n[0].toUpperCase();
            set(ref(db, 'online/' + n), { status: 'online', lastSeen: serverTimestamp() });
            loadMessages();
            loadUsers();
            listenToTyping();
        }

        function loadMessages() {
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, path), (snap) => {
                const d = snap.val();
                const isMine = d.sender === myName;
                const row = document.createElement('div');
                row.className = 'msg-row';
                let body = d.text || "";
                if(d.fileUrl) body = `<img src="${d.fileUrl}" class="rounded-lg max-w-xs border border-white/5 mt-1">`;
                
                row.innerHTML = `
                    <div class="msg ${isMine ? 'mine' : 'theirs'}">
                        ${!isMine ? `<p class="text-[9px] font-bold text-indigo-400 mb-1 uppercase">${d.sender}</p>` : ''}
                        ${body}
                    </div>
                    <div class="status-tag ${isMine ? 'self-end' : 'self-start'}">
                        ${new Date(d.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}
                        ${isMine ? '<i class="fa-solid fa-check-double blue-tick"></i>' : ''}
                    </div>
                `;
                flow.appendChild(row);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        function loadUsers() {
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    if(c.key !== myName) {
                        const div = document.createElement('div');
                        div.className = `user-item text-slate-300 text-sm ${activeChat === c.key ? 'active' : ''}`;
                        div.innerHTML = `<div class="online-dot"></div> ${c.key}`;
                        div.onclick = () => switchChat(c.key);
                        list.appendChild(div);
                    }
                });
            });
        }

        window.switchChat = (user) => {
            activeChat = user;
            document.getElementById('chat-title').innerText = user === "global" ? "# global-general" : `@ ${user}`;
            document.getElementById('global-tab').classList.toggle('active', user === 'global');
            loadMessages();
            if(window.innerWidth < 768) toggleSidebar();
        };

        window.handleTyping = () => {
            set(ref(db, `typing/${activeChat}/${myName}`), true);
            clearTimeout(typingTimeout);
            typingTimeout = setTimeout(() => set(ref(db, `typing/${activeChat}/${myName}`), null), 2000);
        };

        function listenToTyping() {
            onValue(ref(db, `typing/${myName}`), (snap) => {
                const area = document.getElementById('typing-area');
                const typers = snap.val() ? Object.keys(snap.val()) : [];
                area.innerText = typers.length > 0 ? `${typers[0]} is typing...` : "";
            });
        }

        window.uploadFile = async (i) => {
            const file = i.files[0]; if(!file) return;
            const r = sRef(storage, `files/${Date.now()}_${file.name}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            sendMessage(null, url);
        };

        function sendMessage(text, fileUrl = null) {
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            push(ref(db, path), { sender: myName, text, fileUrl, time: Date.now() });
        }

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ sendMessage(i.value); i.value = ""; }
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
    </script>
</body>
</html>
