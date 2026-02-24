<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonChat Pro | Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        
        :root { --neon-purple: #a855f7; --neon-cyan: #22d3ee; --neon-pink: #f472b6; }
        
        body, html { 
            height: 100%; margin: 0; padding: 0; overflow: hidden;
            background: #020617; font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc;
        }

        .neon-border { border: 1px solid rgba(168, 85, 247, 0.3); box-shadow: 0 0 15px rgba(168, 85, 247, 0.1); }
        .neon-text-glow { text-shadow: 0 0 10px rgba(34, 211, 238, 0.5); }

        /* App Layout */
        .app-shell { display: flex; height: 100dvh; width: 100%; position: relative; }

        /* Sidebar - Slack Style */
        .sidebar { 
            width: 280px; background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(10px);
            border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100;
        }

        /* Chat Body */
        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); }
        
        #chat-flow { 
            flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 140px; 
            display: flex; flex-direction: column; gap: 15px;
        }

        /* Modern Message Bubble - Neon Style */
        .msg-card { 
            background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05);
            padding: 12px; border-radius: 16px; position: relative; transition: 0.3s;
            max-width: 85%; align-self: flex-start;
        }
        .msg-card.mine { align-self: flex-end; border-color: var(--neon-purple); background: rgba(168, 85, 247, 0.1); }
        
        /* Reactions Bar */
        .reaction-bar { display: flex; gap: 5px; margin-top: 8px; }
        .reaction-pill { 
            background: rgba(255,255,255,0.1); padding: 2px 8px; border-radius: 20px; 
            font-size: 12px; cursor: pointer; border: 1px solid transparent;
        }
        .reaction-pill:hover { border-color: var(--neon-cyan); background: rgba(34, 211, 238, 0.1); }

        /* Floating Neon Input */
        .input-area {
            position: fixed; bottom: 25px; left: 20px; right: 20px; z-index: 150;
            background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(20px);
            border-radius: 24px; padding: 10px 20px; border: 1px solid var(--neon-purple);
            box-shadow: 0 0 20px rgba(168, 85, 247, 0.3);
        }
        @media (min-width: 769px) { .input-area { left: 300px; right: 40px; } }

        .input-glow:focus { outline: none; box-shadow: 0 0 15px rgba(34, 211, 238, 0.3); }

        /* Status Indicators */
        .status-circle { width: 12px; height: 12px; border-radius: 50%; border: 2px solid #020617; }
        .online { background: #22c55e; box-shadow: 0 0 8px #22c55e; }
        .busy { background: #ef4444; }

        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; transition: 0.3s; }
            .sidebar.active { left: 0; }
        }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[300] bg-[#020617] flex items-center justify-center p-6">
        <div class="bg-slate-900/50 border border-purple-500/30 p-10 rounded-[2.5rem] w-full max-w-sm text-center backdrop-blur-xl">
            <div class="w-20 h-20 bg-gradient-to-tr from-purple-600 to-cyan-400 rounded-3xl flex items-center justify-center mx-auto mb-6 rotate-12 shadow-2xl shadow-purple-500/20">
                <i class="fa-solid fa-bolt-lightning text-white text-3xl"></i>
            </div>
            <h2 class="text-4xl font-bold mb-2 neon-text-glow">Neon<span class="text-purple-500">Pro</span></h2>
            <p class="text-slate-400 text-sm mb-8 italic">Future of Workspace Messaging</p>
            <input id="username-input" type="text" placeholder="Enter Nickname" class="w-full p-4 bg-slate-800/50 border border-slate-700 rounded-2xl mb-4 text-white text-center font-bold focus:border-cyan-400 outline-none transition">
            <button id="join-btn" class="w-full bg-gradient-to-r from-purple-600 to-indigo-600 text-white font-bold py-4 rounded-2xl shadow-lg hover:brightness-110 transition active:scale-95">Enter System</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-8 border-b border-white/5">
                <h1 class="text-xl font-bold bg-gradient-to-r from-purple-400 to-cyan-400 bg-clip-text text-transparent">NEON OS</h1>
            </div>
            <div class="flex-1 overflow-y-auto p-4 space-y-8">
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-4 ml-2">Channels</p>
                    <div onclick="switchChat('global')" class="flex items-center gap-3 p-3 bg-purple-500/10 border border-purple-500/20 text-purple-300 rounded-xl cursor-pointer">
                        <i class="fa-solid fa-hashtag text-xs"></i> <span class="font-bold text-sm">global-hq</span>
                    </div>
                </div>
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-4 ml-2">Direct Messages</p>
                    <div id="users-list" class="space-y-2"></div>
                </div>
            </div>
            <div class="p-6 bg-black/40 flex items-center gap-4">
                <div class="relative">
                    <div id="u-avatar" class="w-12 h-12 rounded-2xl bg-gradient-to-br from-purple-600 to-pink-500 flex items-center justify-center font-bold text-white">?</div>
                    <div class="status-circle online absolute -bottom-1 -right-1"></div>
                </div>
                <div class="flex-1 overflow-hidden">
                    <p id="u-name" class="text-sm font-bold truncate text-white">User</p>
                    <p class="text-[10px] text-cyan-400 font-medium tracking-wide uppercase">Active</p>
                </div>
                <button onclick="logout()" class="text-slate-500 hover:text-red-400 transition"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </aside>

        <main class="chat-view">
            <header class="h-20 border-b border-white/5 flex items-center justify-between px-8 backdrop-blur-md">
                <div class="flex items-center gap-5">
                    <button onclick="toggleSidebar()" class="md:hidden text-white/70"><i class="fa-solid fa-bars-staggered text-xl"></i></button>
                    <div>
                        <h2 id="chat-title" class="text-xl font-bold tracking-tight"># global-hq</h2>
                        <div id="typing-box" class="text-[10px] text-cyan-400 font-bold h-4"></div>
                    </div>
                </div>
                <div class="flex gap-6 text-slate-400">
                    <button onclick="sendLoc()" class="hover:text-cyan-400 transition"><i class="fa-solid fa-map-location-dot text-lg"></i></button>
                    <i class="fa-solid fa-video cursor-pointer hover:text-white transition"></i>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-area">
                <form id="msg-form" class="flex items-center gap-4">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="text-slate-400 hover:text-cyan-400 transition"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Type a message to workspace..." class="flex-1 bg-transparent text-white placeholder:text-slate-600 outline-none py-2 text-md" autocomplete="off" oninput="handleTyping()">
                    <button class="w-12 h-12 bg-purple-600 rounded-full flex items-center justify-center shadow-lg shadow-purple-500/40 hover:scale-105 active:scale-95 transition">
                        <i class="fa-solid fa-paper-plane text-xs text-white"></i>
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
            set(ref(db, 'online/' + n), {status: 'online', lastSeen: Date.now()});
            loadMessages();
            loadUsers();
            listenTyping();
        }

        function loadMessages() {
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            const flow = document.getElementById('chat-flow');
            onValue(ref(db, path), (snap) => {
                flow.innerHTML = "";
                snap.forEach(msg => {
                    const d = msg.val();
                    const isMine = d.sender === myName;
                    const div = document.createElement('div');
                    div.className = `msg-card ${isMine ? 'mine' : ''}`;
                    
                    let body = d.text || "";
                    if(d.fileUrl) body = `<img src="${d.fileUrl}" class="rounded-xl max-w-full md:max-w-xs border border-white/10 mt-1 hover:scale-105 transition cursor-pointer">`;
                    if(d.locUrl) body = `<a href="${d.locUrl}" target="_blank" class="text-cyan-400 font-bold text-xs underline">📍 Shared Location</a>`;
                    
                    div.innerHTML = `
                        <div class="flex justify-between items-center gap-4 mb-1">
                            <span class="text-[10px] font-bold ${isMine ? 'text-purple-300' : 'text-cyan-400'} uppercase tracking-tighter">${d.sender}</span>
                            <span class="text-[9px] text-slate-500">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
                        </div>
                        <div class="text-slate-200 text-sm leading-relaxed">${body}</div>
                        <div class="reaction-bar">
                            <span class="reaction-pill">🔥</span><span class="reaction-pill">👍</span><span class="reaction-pill">😂</span>
                        </div>
                    `;
                    flow.appendChild(div);
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
                        d.className = `flex items-center gap-3 p-3 rounded-xl cursor-pointer hover:bg-white/5 transition ${activeChat === c.key ? 'bg-white/5 border-l-2 border-cyan-400' : ''}`;
                        d.innerHTML = `<div class="w-8 h-8 rounded-lg bg-slate-800 flex items-center justify-center font-bold text-xs">${c.key[0]}</div>
                                       <div class="flex-1"><p class="text-xs font-bold text-white">${c.key}</p></div>
                                       <div class="status-circle online scale-75"></div>`;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        window.switchChat = (u) => {
            activeChat = u;
            document.getElementById('chat-title').innerText = u === 'global' ? '# global-hq' : `@ ${u}`;
            loadMessages();
            if(window.innerWidth < 768) toggleSidebar();
        };

        window.handleTyping = () => {
            set(ref(db, `typing/${activeChat}/${myName}`), true);
            clearTimeout(typingTimer);
            typingTimer = setTimeout(() => remove(ref(db, `typing/${activeChat}/${myName}`)), 2000);
        };

        function listenTyping() {
            onValue(ref(db, `typing/${activeChat}`), (snap) => {
                const typers = snap.val() ? Object.keys(snap.val()).filter(u => u !== myName) : [];
                document.getElementById('typing-box').innerText = typers.length > 0 ? `${typers[0]} is writing...` : "";
            });
        }

        window.uploadFile = async (i) => {
            const file = i.files[0]; if(!file) return;
            const r = sRef(storage, `files/${Date.now()}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            push(ref(db, activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, fileUrl: url, time: Date.now() });
        };

        window.sendLoc = () => {
            navigator.geolocation.getCurrentPosition(p => {
                const url = `http://maps.google.com/?q=${p.coords.latitude},${p.coords.longitude}`;
                push(ref(db, activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, locUrl: url, time: Date.now() });
            });
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                push(ref(db, activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, text: i.value, time: Date.now() });
                i.value = ""; 
            }
        };
    </script>
</body>
</html>
