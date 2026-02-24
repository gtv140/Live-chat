<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>ChatPro | Enterprise Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        :root { --brand: #4f46e5; --sidebar-bg: #1e1b4b; --main-bg: #0f172a; }
        
        body, html { 
            font-family: 'Inter', sans-serif; 
            background: var(--main-bg); 
            height: 100%; margin: 0; overflow: hidden; color: #e2e8f0;
        }

        .workspace-root { display: flex; height: 100dvh; width: 100%; }

        /* Slack-Style Sidebar */
        .sidebar { 
            width: 280px; background: var(--sidebar-bg); border-right: 1px solid rgba(255,255,255,0.05);
            display: flex; flex-direction: column; transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1); z-index: 50;
        }

        /* Main Chat Section */
        .chat-view { flex: 1; display: flex; flex-direction: column; background: var(--main-bg); position: relative; min-width: 0; }

        #chat-flow { 
            flex: 1; overflow-y: auto; padding: 24px; display: flex; flex-direction: column; gap: 6px;
            scroll-behavior: smooth;
        }

        /* Professional Slack Bubbles */
        .msg-container { display: flex; gap: 12px; padding: 8px 16px; transition: 0.2s; border-radius: 8px; }
        .msg-container:hover { background: rgba(255,255,255,0.03); }
        .avatar-sm { width: 36px; height: 36px; border-radius: 8px; background: #3730a3; display: flex; align-items: center; justify-content: center; font-weight: bold; font-size: 14px; shrink-0; }
        
        .msg-content { flex: 1; }
        .msg-header { display: flex; align-items: baseline; gap: 8px; margin-bottom: 2px; }
        .sender-name { font-weight: 700; font-size: 14px; color: white; }
        .msg-time { font-size: 10px; color: #64748b; }
        .msg-text { font-size: 14px; color: #cbd5e1; line-height: 1.5; word-break: break-word; }

        /* Floating Input Bar (Skype/Slack Mix) */
        .input-zone { padding: 12px 20px; background: var(--main-bg); }
        .input-container { 
            background: #1e293b; border: 1px solid rgba(255,255,255,0.1); border-radius: 12px; 
            display: flex; flex-direction: column; transition: 0.2s;
        }
        .input-container:focus-within { border-color: var(--brand); box-shadow: 0 0 0 2px rgba(79, 70, 229, 0.2); }
        
        .toolbar { display: flex; gap: 10px; padding: 8px 12px; border-bottom: 1px solid rgba(255,255,255,0.05); }
        .toolbar button { color: #94a3b8; font-size: 14px; transition: 0.2s; }
        .toolbar button:hover { color: white; }

        input#msg-input { background: transparent; border: none; color: white; padding: 12px; width: 100%; outline: none; font-size: 15px; }

        /* Responsive */
        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 80%; }
            .sidebar.active { left: 0; box-shadow: 10px 0 30px rgba(0,0,0,0.5); }
        }

        .status-dot { width: 10px; height: 10px; background: #22c55e; border-radius: 50%; border: 2px solid var(--sidebar-bg); }
        .online-user { display: flex; align-items: center; gap: 10px; padding: 8px 12px; border-radius: 6px; cursor: pointer; transition: 0.2s; }
        .online-user:hover { background: rgba(255,255,255,0.05); }
        .online-user.active { background: #4338ca; color: white; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[200] bg-[#0f172a] flex items-center justify-center p-6">
        <div class="bg-[#1e1b4b] border border-white/10 p-10 rounded-3xl shadow-2xl w-full max-w-sm">
            <div class="flex items-center gap-3 mb-8 justify-center">
                <div class="w-10 h-10 bg-indigo-600 rounded-lg flex items-center justify-center"><i class="fa-solid fa-layer-group text-white"></i></div>
                <h2 class="text-2xl font-bold text-white">ChatPro <span class="text-indigo-500">v14</span></h2>
            </div>
            <input id="username-input" type="text" placeholder="Work Name (e.g. Ali)" class="w-full bg-slate-900 border border-slate-700 p-4 rounded-xl mb-4 text-white font-medium outline-none focus:border-indigo-500 transition">
            <button id="join-btn" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-4 rounded-xl transition-all">Launch Workspace</button>
        </div>
    </div>

    <div class="workspace-root">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 border-b border-white/5">
                <h1 class="font-bold text-lg text-white">Main Office</h1>
            </div>
            
            <div class="flex-1 overflow-y-auto p-4 space-y-6">
                <div>
                    <p class="text-[11px] font-bold text-slate-500 uppercase mb-3 px-2">Channels</p>
                    <div onclick="switchChat('global')" id="global-btn" class="online-user active font-semibold text-sm">
                        <i class="fa-solid fa-hashtag opacity-50"></i> general-hq
                    </div>
                </div>
                <div>
                    <p class="text-[11px] font-bold text-slate-500 uppercase mb-3 px-2">Direct Messages</p>
                    <div id="users-list" class="space-y-1"></div>
                </div>
            </div>

            <div class="p-4 bg-black/20 flex items-center gap-3">
                <div id="u-avatar" class="avatar-sm">?</div>
                <div class="flex-1">
                    <p id="u-name" class="text-sm font-bold text-white truncate">User</p>
                    <p class="text-[10px] text-green-500">Active Now</p>
                </div>
                <button onclick="logout()" class="text-slate-500 hover:text-red-400 p-2"><i class="fa-solid fa-gear"></i></button>
            </div>
        </aside>

        <main class="chat-view">
            <header class="h-16 border-b border-white/5 flex items-center justify-between px-6 bg-slate-900/50 backdrop-blur-md">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-bars"></i></button>
                    <div>
                        <h2 id="current-chat-title" class="font-bold text-white text-md tracking-tight"># general-hq</h2>
                        <p id="typing-indicator" class="text-[10px] text-indigo-400 h-3 font-medium"></p>
                    </div>
                </div>
                <div class="flex gap-5 text-slate-400">
                    <i class="fa-solid fa-phone cursor-pointer hover:text-white"></i>
                    <i class="fa-solid fa-video cursor-pointer hover:text-white"></i>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-zone">
                <form id="msg-form" class="input-container">
                    <div class="toolbar">
                        <button type="button" onclick="document.getElementById('file-input').click()"><i class="fa-solid fa-plus"></i></button>
                        <button type="button" onclick="sendLoc()"><i class="fa-solid fa-location-dot"></i></button>
                        <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    </div>
                    <div class="flex items-center">
                        <input id="msg-input" type="text" autocomplete="off" placeholder="Send a message to workspace..." oninput="handleTyping()">
                        <button class="px-5 text-indigo-500 font-bold hover:text-indigo-400"><i class="fa-solid fa-paper-plane"></i></button>
                    </div>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let myName = "", activeChat = "global", typingTimer;

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
            set(ref(db, 'online/' + n), true);
            loadMessages();
            loadUsers();
            listenTyping();
        }

        function loadMessages() {
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onValue(ref(db, path), (snap) => {
                flow.innerHTML = "";
                snap.forEach(msg => {
                    const data = msg.val();
                    const isMine = data.sender === myName;
                    const row = document.createElement('div');
                    row.className = "msg-container";
                    
                    let content = data.text || "";
                    if(data.fileUrl) content = `<img src="${data.fileUrl}" class="rounded-lg max-w-full md:max-w-xs border border-white/5 shadow-md">`;
                    if(data.locUrl) content = `<a href="${data.locUrl}" target="_blank" class="text-indigo-400 underline font-bold text-xs">📍 Shared Live Location</a>`;
                    
                    row.innerHTML = `
                        <div class="avatar-sm" style="background:${isMine ? '#4f46e5' : '#334155'}">${data.sender[0].toUpperCase()}</div>
                        <div class="msg-content">
                            <div class="msg-header">
                                <span class="sender-name">${data.sender}</span>
                                <span class="msg-time">${new Date(data.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}</span>
                            </div>
                            <div class="msg-text">${content}</div>
                        </div>
                    `;
                    flow.appendChild(row);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        function loadUsers() {
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    if(c.key !== myName) {
                        const d = document.createElement('div');
                        d.className = `online-user text-sm ${activeChat === c.key ? 'active' : 'text-slate-400'}`;
                        d.innerHTML = `<div class="status-dot"></div> <span>${c.key}</span>`;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        window.switchChat = (user) => {
            activeChat = user;
            document.getElementById('current-chat-title').innerText = user === "global" ? "# general-hq" : `@ ${user}`;
            document.getElementById('global-btn').classList.toggle('active', user === "global");
            loadMessages();
            if(window.innerWidth < 768) toggleSidebar();
        };

        window.handleTyping = () => {
            set(ref(db, `typing/${activeChat}/${myName}`), true);
            clearTimeout(typingTimer);
            typingTimer = setTimeout(() => remove(ref(db, `typing/${activeChat}/${myName}`)), 2000);
        };

        function listenTyping() {
            onValue(ref(db, `typing/${myName}`), (snap) => {
                const indicator = document.getElementById('typing-indicator');
                const typers = snap.val() ? Object.keys(snap.val()) : [];
                indicator.innerText = typers.length > 0 ? `${typers[0]} is typing...` : "";
            });
        }

        window.uploadFile = async (i) => {
            const file = i.files[0]; if(!file) return;
            const r = sRef(storage, `files/${Date.now()}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            push(ref(db, activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, fileUrl: url, time: Date.now() });
        };

        window.sendLoc = () => {
            navigator.geolocation.getCurrentPosition(p => {
                const url = `https://www.google.com/maps?q=${p.coords.latitude},${p.coords.longitude}`;
                push(ref(db, activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, locUrl: url, time: Date.now() });
            });
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                push(ref(db, activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, text: i.value, time: Date.now() });
                i.value = ""; 
            }
        };
    </script>
</body>
</html>
