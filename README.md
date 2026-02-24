<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonChat Pro | Fixed UI</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        
        :root { --neon-purple: #a855f7; --neon-cyan: #22d3ee; }
        
        body, html { 
            height: 100%; margin: 0; padding: 0; overflow: hidden;
            background: #020617; font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc;
        }

        .app-shell { display: flex; height: 100dvh; width: 100%; position: relative; }

        /* Sidebar */
        .sidebar { 
            width: 280px; background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(10px);
            border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100;
        }

        /* Chat Body */
        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); position: relative; }
        
        #chat-flow { 
            flex: 1; overflow-y: auto; padding: 20px; 
            /* YE HAI FIX: Message box ke liye extra jagah niche */
            padding-bottom: 160px; 
            display: flex; flex-direction: column; gap: 15px;
            scroll-behavior: smooth;
        }

        /* Message Card - Neon Style */
        .msg-card { 
            background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05);
            padding: 12px; border-radius: 18px; position: relative; transition: 0.3s;
            max-width: 85%; align-self: flex-start;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
        }
        .msg-card.mine { align-self: flex-end; border-color: var(--neon-purple); background: rgba(168, 85, 247, 0.12); }
        
        .reaction-bar { display: flex; gap: 6px; margin-top: 8px; }
        .reaction-pill { 
            background: rgba(255,255,255,0.08); padding: 3px 10px; border-radius: 20px; 
            font-size: 12px; cursor: pointer; border: 1px solid transparent; transition: 0.2s;
        }
        .reaction-pill:hover { border-color: var(--neon-cyan); background: rgba(34, 211, 238, 0.15); transform: scale(1.1); }

        /* NEON INPUT AREA - Floating Fix */
        .input-area {
            position: absolute; bottom: 25px; left: 15px; right: 15px; z-index: 150;
            background: rgba(15, 23, 42, 0.85); backdrop-filter: blur(25px);
            border-radius: 24px; padding: 8px 15px; border: 1.5px solid var(--neon-purple);
            box-shadow: 0 0 25px rgba(168, 85, 247, 0.4);
        }
        @media (min-width: 769px) { .input-area { left: 300px; right: 40px; } }

        input#msg-input { background: transparent; border: none; color: white; flex: 1; outline: none; padding: 10px; font-size: 15px; }

        /* Mobile Adjust */
        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; transition: 0.3s; }
            .sidebar.active { left: 0; }
            #chat-flow { padding-bottom: 140px; }
        }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[300] bg-[#020617] flex items-center justify-center p-6 text-center">
        <div class="bg-slate-900/50 border border-purple-500/30 p-10 rounded-[2.5rem] w-full max-w-sm backdrop-blur-xl">
            <h2 class="text-3xl font-bold mb-6 text-white tracking-tighter">Neon<span class="text-purple-500">Pro</span></h2>
            <input id="username-input" type="text" placeholder="Workspace Name..." class="w-full p-4 bg-slate-800 rounded-2xl mb-4 text-white text-center font-bold focus:border-cyan-400 outline-none border border-transparent transition">
            <button id="join-btn" class="w-full bg-purple-600 text-white font-bold py-4 rounded-2xl shadow-lg active:scale-95 transition">Enter Workspace</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-8 border-b border-white/5 font-bold text-xl text-white">NEON OS</div>
            <div class="flex-1 overflow-y-auto p-4 space-y-6">
                <div onclick="switchChat('global')" class="p-3 bg-purple-500/10 border border-purple-500/20 text-purple-300 rounded-xl cursor-pointer font-bold text-sm"># global-hq</div>
                <p class="text-[10px] text-slate-500 uppercase font-bold ml-2">Direct Messages</p>
                <div id="users-list" class="space-y-2"></div>
            </div>
            <div class="p-6 bg-black/40 flex items-center gap-4">
                <div id="u-avatar" class="w-10 h-10 rounded-xl bg-purple-600 flex items-center justify-center font-bold text-white text-sm">?</div>
                <div class="flex-1 overflow-hidden"><p id="u-name" class="text-sm font-bold truncate">User</p></div>
                <button onclick="logout()" class="text-slate-500 hover:text-red-400"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </aside>

        <main class="chat-view">
            <header class="h-20 border-b border-white/5 flex items-center justify-between px-6">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-white"><i class="fa-solid fa-bars-staggered"></i></button>
                    <h2 id="chat-title" class="text-xl font-bold tracking-tight text-white"># global-hq</h2>
                </div>
                <div id="typing-box" class="text-[10px] text-cyan-400 font-bold"></div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-area">
                <form id="msg-form" class="flex items-center gap-3">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="text-slate-400 hover:text-cyan-400 transition"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Type message..." class="text-white" autocomplete="off" oninput="handleTyping()">
                    <button class="w-10 h-10 bg-purple-600 rounded-xl flex items-center justify-center shadow-lg active:scale-90 transition"><i class="fa-solid fa-paper-plane text-xs text-white"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
            onValue(ref(db, path), (snap) => {
                flow.innerHTML = "";
                snap.forEach(msg => {
                    const d = msg.val();
                    const isMine = d.sender === myName;
                    const div = document.createElement('div');
                    div.className = `msg-card ${isMine ? 'mine' : ''}`;
                    
                    let body = d.text || "";
                    if(d.fileUrl) body = `<img src="${d.fileUrl}" class="rounded-xl max-w-full md:max-w-xs mt-1">`;
                    
                    div.innerHTML = `
                        <div class="flex justify-between items-center gap-4 mb-1">
                            <span class="text-[10px] font-bold ${isMine ? 'text-purple-300' : 'text-cyan-400'}">${d.sender}</span>
                            <span class="text-[9px] text-slate-500">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
                        </div>
                        <div class="text-slate-200 text-sm">${body}</div>
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
                        d.className = `flex items-center gap-3 p-3 rounded-xl cursor-pointer hover:bg-white/5 transition ${activeChat === c.key ? 'bg-white/10 border-l-2 border-cyan-400' : ''}`;
                        d.innerHTML = `<div class="w-8 h-8 rounded-lg bg-slate-800 flex items-center justify-center font-bold text-xs">${c.key[0]}</div>
                                       <div class="flex-1"><p class="text-xs font-bold text-white">${c.key}</p></div>`;
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
                document.getElementById('typing-box').innerText = typers.length > 0 ? `${typers[0]} is typing...` : "";
            });
        }

        window.uploadFile = async (i) => {
            const file = i.files[0]; if(!file) return;
            const r = sRef(storage, `files/${Date.now()}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            push(ref(db, activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, fileUrl: url, time: Date.now() });
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
