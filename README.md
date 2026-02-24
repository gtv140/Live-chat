<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>ChatPro | Future Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        
        :root { --brand: #6366f1; --bg: #0b0f1a; --card: #161b2c; }
        
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: white; height: 100dvh; margin: 0; overflow: hidden; }

        .app-layout { display: flex; height: 100dvh; width: 100%; }

        /* Professional Sidebar */
        .sidebar { 
            width: 280px; background: var(--card); border-right: 1px solid rgba(255,255,255,0.05);
            display: flex; flex-direction: column; transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1); z-index: 100;
        }

        /* Message Area */
        .chat-main { flex: 1; display: flex; flex-direction: column; background: var(--bg); position: relative; }
        
        #chat-flow { 
            flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 12px;
            scroll-behavior: smooth; -webkit-overflow-scrolling: touch;
        }

        /* Modern Bubbles */
        .msg-box { max-width: 85%; padding: 12px 16px; border-radius: 16px; font-size: 14px; position: relative; transition: 0.2s; }
        .msg-box.mine { align-self: flex-end; background: var(--brand); color: white; border-bottom-right-radius: 4px; box-shadow: 0 4px 15px rgba(99, 102, 241, 0.3); }
        .msg-box.theirs { align-self: flex-start; background: #21283d; color: #e2e8f0; border-bottom-left-radius: 4px; }

        /* Typing & Ticks */
        .meta { font-size: 10px; margin-top: 4px; opacity: 0.5; display: flex; align-items: center; gap: 4px; }
        .pulse-online { width: 8px; height: 8px; background: #10b981; border-radius: 50%; animation: pulse 2s infinite; }
        @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.7); } 70% { box-shadow: 0 0 0 10px rgba(16, 185, 129, 0); } 100% { box-shadow: 0 0 0 0 rgba(16, 185, 129, 0); } }

        /* Mobile Optimization */
        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; }
            .sidebar.active { left: 0; }
        }

        /* Easy Input Bar */
        .input-bar { padding: 12px 16px; background: var(--card); border-top: 1px solid rgba(255,255,255,0.05); }
        .input-inner { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.1); border-radius: 14px; display: flex; align-items: center; padding: 4px 8px; }
        
        .user-link { padding: 10px; border-radius: 10px; cursor: pointer; transition: 0.2s; display: flex; align-items: center; gap: 12px; }
        .user-link:hover { background: rgba(255,255,255,0.05); }
        .user-link.active { background: rgba(99, 102, 241, 0.15); color: #818cf8; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[200] bg-[#0b0f1a] flex items-center justify-center p-6">
        <div class="bg-[#161b2c] p-8 rounded-[2rem] shadow-2xl w-full max-w-sm border border-white/5 text-center">
            <div class="w-14 h-14 bg-indigo-600 rounded-2xl flex items-center justify-center mx-auto mb-4 shadow-xl shadow-indigo-500/20">
                <i class="fa-solid fa-bolt text-white text-xl"></i>
            </div>
            <h2 class="text-2xl font-bold mb-1">ChatPro <span class="text-indigo-500">v11</span></h2>
            <p class="text-slate-500 text-xs mb-6">Experience the next generation of chat</p>
            <input id="username-input" type="text" placeholder="Pick a username" class="w-full bg-slate-900 border border-white/10 p-4 rounded-xl mb-4 text-center text-white outline-none focus:border-indigo-500">
            <button id="join-btn" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-4 rounded-xl transition-all active:scale-95">Enter Workspace</button>
        </div>
    </div>

    <div class="app-layout">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 border-b border-white/5 flex justify-between items-center">
                <span class="font-bold text-lg tracking-tight">ChatPro OS</span>
                <button onclick="toggleSidebar()" class="md:hidden text-slate-500"><i class="fa-solid fa-xmark"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto p-3 space-y-4">
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase px-3 mb-2 tracking-widest">Channels</p>
                    <div onclick="switchChat('global')" id="global-tab" class="user-link active font-semibold text-sm">
                        <i class="fa-solid fa-hashtag opacity-50"></i> general-chat
                    </div>
                </div>
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase px-3 mb-2 tracking-widest">Direct Messages</p>
                    <div id="users-list" class="space-y-1"></div>
                </div>
            </div>

            <div class="p-4 border-t border-white/5 bg-black/20 flex items-center gap-3">
                <div class="w-10 h-10 rounded-xl bg-indigo-600 flex items-center justify-center font-bold" id="u-avatar">?</div>
                <div class="flex-1 overflow-hidden">
                    <p class="text-xs font-bold truncate" id="u-name">User</p>
                    <div class="flex items-center gap-1.5"><div class="pulse-online"></div><span class="text-[9px] text-slate-400">Online</span></div>
                </div>
                <button onclick="logout()" class="text-slate-500 hover:text-red-400 p-2"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </aside>

        <main class="chat-main">
            <header class="h-16 border-b border-white/5 flex items-center justify-between px-6 bg-slate-900/30 backdrop-blur-xl">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 id="chat-title" class="font-bold text-sm tracking-tight"># general-chat</h2>
                        <p id="typing-indicator" class="text-[9px] text-indigo-400 font-medium h-3"></p>
                    </div>
                </div>
                <div class="flex gap-4 text-slate-400">
                    <button onclick="sendLoc()"><i class="fa-solid fa-location-arrow text-sm hover:text-indigo-400"></i></button>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-bar">
                <form id="msg-form" class="max-w-4xl mx-auto input-inner">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="p-3 text-slate-500 hover:text-white"><i class="fa-solid fa-circle-plus text-lg"></i></button>
                    <input id="msg-input" type="text" placeholder="Type a message..." class="flex-1 bg-transparent p-3 outline-none text-sm text-white" oninput="handleTyping()">
                    <button class="bg-indigo-600 text-white w-10 h-10 rounded-xl flex items-center justify-center shadow-lg active:scale-90 transition-all m-1">
                        <i class="fa-solid fa-paper-plane text-xs"></i>
                    </button>
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
            onChildAdded(ref(db, path), (snap) => {
                const d = snap.val();
                const isMine = d.sender === myName;
                const div = document.createElement('div');
                div.className = `msg-box ${isMine ? 'mine' : 'theirs'}`;
                
                let body = d.text || "";
                if(d.fileUrl) body = `<img src="${d.fileUrl}" class="rounded-xl max-w-full border border-white/10 mt-1">`;
                if(d.locUrl) body = `<a href="${d.locUrl}" target="_blank" class="text-xs font-bold underline">📍 Shared Location</a>`;

                div.innerHTML = `
                    ${!isMine ? `<p class="text-[9px] font-bold text-indigo-400 mb-1 uppercase">${d.sender}</p>` : ''}
                    <div>${body}</div>
                    <div class="meta ${isMine ? 'justify-end' : ''}">
                        ${new Date(d.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}
                        ${isMine ? '<i class="fa-solid fa-check-double text-[8px] text-white"></i>' : ''}
                    </div>
                `;
                flow.appendChild(div);
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
                        div.className = `user-link text-sm ${activeChat === c.key ? 'active' : ''}`;
                        div.innerHTML = `<div class="pulse-online"></div> ${c.key}`;
                        div.onclick = () => switchChat(c.key);
                        list.appendChild(div);
                    }
                });
            });
        }

        window.switchChat = (user) => {
            activeChat = user;
            document.getElementById('chat-title').innerText = user === "global" ? "# general-chat" : `@ ${user}`;
            document.getElementById('global-tab').classList.toggle('active', user === 'global');
            loadMessages();
            if(window.innerWidth < 768) toggleSidebar();
        };

        window.handleTyping = () => {
            set(ref(db, `typing/${activeChat}/${myName}`), true);
            clearTimeout(typingTimer);
            typingTimer = setTimeout(() => remove(ref(db, `typing/${activeChat}/${myName}`)), 1500);
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
            const r = sRef(storage, `files/${Date.now()}_${file.name}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            push(ref(db, path), { sender: myName, fileUrl: url, time: Date.now() });
        };

        window.sendLoc = () => {
            navigator.geolocation.getCurrentPosition(p => {
                const url = `https://www.google.com/maps?q=${p.coords.latitude},${p.coords.longitude}`;
                const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
                push(ref(db, path), { sender: myName, locUrl: url, time: Date.now() });
            });
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
                push(ref(db, path), { sender: myName, text: i.value, time: Date.now() });
                i.value = ""; 
            }
        };
    </script>
</body>
</html>
