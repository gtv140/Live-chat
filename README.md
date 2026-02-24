<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ChatPro | The Final Evolution</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap');
        
        :root { --neon: #8b5cf6; --pink: #ec4899; }
        * { -webkit-tap-highlight-color: transparent; }
        
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background: #0f172a; 
            height: 100dvh; 
            margin: 0; 
            overflow: hidden; 
        }

        /* Glass App Container */
        .app-container {
            display: flex;
            height: 100dvh;
            width: 100%;
            background: linear-gradient(135deg, rgba(15,23,42,1) 0%, rgba(30,27,75,1) 100%);
        }

        /* Responsive Sidebar */
        .sidebar {
            width: 300px;
            background: rgba(15, 23, 42, 0.7);
            backdrop-filter: blur(20px);
            border-right: 1px solid rgba(255,255,255,0.05);
            display: flex;
            flex-direction: column;
            transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            z-index: 100;
        }

        /* Chat Area */
        .chat-area { flex: 1; display: flex; flex-direction: column; background: rgba(255,255,255,0.02); position: relative; }

        #chat-flow {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 12px;
            scroll-behavior: smooth;
        }

        /* Modern Bubbles */
        .msg { max-width: 80%; padding: 14px 18px; border-radius: 20px; font-size: 14px; position: relative; animation: slideIn 0.3s ease; line-height: 1.5; }
        .msg.mine { align-self: flex-end; background: linear-gradient(135deg, var(--neon), var(--pink)); color: white; border-bottom-right-radius: 4px; box-shadow: 0 10px 15px -3px rgba(139, 92, 246, 0.3); }
        .msg.theirs { align-self: flex-start; background: rgba(255,255,255,0.1); color: white; border-bottom-left-radius: 4px; border: 1px solid rgba(255,255,255,0.05); }

        @keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; }
            .sidebar.active { left: 0; }
            .msg { max-width: 90%; }
        }

        /* Custom Scrollbar */
        #chat-flow::-webkit-scrollbar { width: 4px; }
        #chat-flow::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.1); border-radius: 10px; }

        /* User Card Hover */
        .user-card:hover { background: rgba(255,255,255,0.05); }
        .user-card.active { background: rgba(139, 92, 246, 0.2); border-left: 4px solid var(--neon); }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[200] bg-slate-950 flex items-center justify-center p-6">
        <div class="bg-white/5 border border-white/10 backdrop-blur-2xl p-10 rounded-[2.5rem] shadow-2xl w-full max-w-sm text-center">
            <div class="w-16 h-16 bg-gradient-to-tr from-purple-600 to-pink-500 rounded-2xl flex items-center justify-center mx-auto mb-6 shadow-xl rotate-12">
                <i class="fa-solid fa-rocket text-white text-2xl"></i>
            </div>
            <h2 class="text-3xl font-bold text-white mb-2">ChatPro <span class="text-purple-400">OS</span></h2>
            <p class="text-slate-400 text-sm mb-8">Personal & Business Workspace</p>
            <input id="username-input" type="text" placeholder="Apna Name Likhein" class="w-full bg-white/5 border border-white/10 p-4 rounded-xl mb-4 text-center font-bold text-white outline-none focus:border-purple-500 transition">
            <button id="join-btn" class="w-full bg-purple-600 text-white font-bold py-4 rounded-xl shadow-lg hover:scale-105 active:scale-95 transition">Launch Workspace</button>
        </div>
    </div>

    <div class="app-container">
        <aside id="sidebar" class="sidebar">
            <div class="p-8 border-b border-white/5 flex justify-between items-center">
                <span class="font-bold text-xl text-white tracking-tighter">Workspace</span>
                <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-xmark"></i></button>
            </div>
            
            <div class="p-4 flex-1 overflow-y-auto space-y-6">
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-4 ml-2">Public Channels</p>
                    <div onclick="switchChat('global')" id="global-btn" class="user-card active p-4 rounded-xl flex items-center gap-4 cursor-pointer text-white">
                        <i class="fa-solid fa-hashtag text-purple-400"></i>
                        <span class="text-sm font-bold">global-hq</span>
                    </div>
                </div>
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-4 ml-2">Direct Messages</p>
                    <div id="users-list" class="space-y-1 text-white/70"></div>
                </div>
            </div>

            <div class="p-6 border-t border-white/5 flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div id="u-avatar" class="w-10 h-10 rounded-xl bg-purple-600 text-white flex items-center justify-center font-bold">?</div>
                    <div>
                        <p id="u-name" class="text-xs font-bold text-white">User</p>
                        <p class="text-[10px] text-green-400 font-medium">Available</p>
                    </div>
                </div>
                <button onclick="logout()" class="text-slate-500 hover:text-red-400"><i class="fa-solid fa-right-from-bracket"></i></button>
            </div>
        </aside>

        <main class="chat-area">
            <header class="h-20 border-b border-white/5 flex items-center justify-between px-6 md:px-10">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden p-2 text-white/50"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 id="current-chat-title" class="font-bold text-white text-lg tracking-tight"># global-hq</h2>
                        <p class="text-[10px] text-slate-500">Live Communication Hub</p>
                    </div>
                </div>
                <div class="flex gap-4">
                    <button onclick="sendLoc()" class="text-white/30 hover:text-purple-400"><i class="fa-solid fa-location-dot"></i></button>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="p-4 md:p-8">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-3 bg-white/5 border border-white/10 p-2 rounded-[1.8rem] backdrop-blur-xl">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="w-12 h-12 text-slate-400 hover:text-white transition"><i class="fa-solid fa-plus-circle text-xl"></i></button>
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Write a message..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium text-white placeholder:text-slate-500">
                    <button class="bg-purple-600 text-white px-8 py-3 rounded-2xl font-bold shadow-lg shadow-purple-500/20 hover:scale-105 active:scale-95 transition-all">Send</button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let myName = "", activeChat = "global";

        window.onload = () => { const s = localStorage.getItem('chatProUser'); if(s) init(s); };
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
        }

        function loadMessages() {
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, path), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const div = document.createElement('div');
                div.className = `msg ${isMine ? 'mine' : 'theirs'}`;
                let content = data.text || "";
                if(data.fileUrl) content = `<img src="${data.fileUrl}" class="rounded-xl max-w-full md:max-w-xs shadow-lg mt-2">`;
                if(data.locUrl) content = `<a href="${data.locUrl}" target="_blank" class="text-xs underline font-bold">📍 View Live Location</a>`;
                div.innerHTML = `<p class="text-[9px] font-bold uppercase opacity-50 mb-1">${data.sender}</p><div>${content}</div>`;
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
                        const d = document.createElement('div');
                        d.className = `user-card ${activeChat === c.key ? 'active' : ''} p-4 rounded-xl flex items-center gap-4 cursor-pointer transition`;
                        d.innerHTML = `<div class="w-8 h-8 rounded-lg bg-white/10 flex items-center justify-center font-bold text-xs uppercase">${c.key[0]}</div> <span class="text-sm font-medium">${c.key}</span>`;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        window.switchChat = (user) => {
            activeChat = user;
            document.getElementById('current-chat-title').innerText = user === "global" ? "# global-hq" : `@ ${user}`;
            document.getElementById('global-btn').classList.toggle('active', user === "global");
            loadMessages();
            if(window.innerWidth < 768) toggleSidebar();
        };

        window.uploadFile = async (input) => {
            const file = input.files[0];
            if(!file) return;
            const r = sRef(storage, `files/${Date.now()}_${file.name}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            push(ref(db, path), { sender: myName, fileUrl: url, time: Date.now() });
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };
        window.sendLoc = () => {
            navigator.geolocation.getCurrentPosition(p => {
                const url = `https://www.google.com/maps?q=${p.coords.latitude},${p.coords.longitude}`;
                const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
                push(ref(db, path), { sender: myName, locUrl: url, time: Date.now() });
            });
        };

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
