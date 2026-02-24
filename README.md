<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ChatPro Ultra | Future Suite</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap');
        
        :root { --p: #6366f1; --s: #a855f7; --t: #ec4899; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: linear-gradient(135deg, #0f172a 0%, #1e1b4b 100%); height: 100dvh; overflow: hidden; margin: 0; }
        
        .glass-container { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.1); height: 100%; display: flex; width: 100%; }
        
        /* Sidebar Glass */
        .sidebar { width: 300px; background: rgba(15, 23, 42, 0.8); border-right: 1px solid rgba(255,255,255,0.1); transition: 0.3s; z-index: 50; }
        
        /* Chat Flow Custom Scroll */
        #chat-flow { flex: 1; overflow-y: auto; padding: 24px; display: flex; flex-direction: column; gap: 16px; scroll-behavior: smooth; }
        #chat-flow::-webkit-scrollbar { width: 4px; }
        #chat-flow::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.2); border-radius: 10px; }

        /* Modern Bubbles */
        .msg { max-width: 80%; padding: 14px 18px; border-radius: 22px; font-size: 14px; position: relative; animation: fadeIn 0.3s ease; line-height: 1.6; }
        .msg.mine { align-self: flex-end; background: linear-gradient(135deg, var(--p), var(--s)); color: white; border-bottom-right-radius: 4px; box-shadow: 0 8px 20px rgba(99, 102, 241, 0.3); }
        .msg.theirs { align-self: flex-start; background: rgba(255,255,255,0.1); color: white; border-bottom-left-radius: 4px; border: 1px solid rgba(255,255,255,0.1); }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* Mobile Adjustments */
        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; }
            .sidebar.active { left: 0; }
            .msg { max-width: 90%; }
        }

        .user-card:hover { background: rgba(255,255,255,0.05); }
        .user-card.active { background: linear-gradient(90deg, rgba(99, 102, 241, 0.2), transparent); border-left: 3px solid var(--p); }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[100] bg-[#0f172a] flex items-center justify-center p-6">
        <div class="bg-white/5 border border-white/10 backdrop-blur-xl p-10 rounded-[2.5rem] shadow-2xl w-full max-w-sm text-center">
            <div class="w-16 h-16 bg-indigo-600 rounded-2xl flex items-center justify-center mx-auto mb-6 shadow-xl shadow-indigo-500/50">
                <i class="fa-solid fa-bolt-lightning text-white text-2xl"></i>
            </div>
            <h2 class="text-2xl font-bold text-white mb-2">ChatPro <span class="text-indigo-400 text-sm font-normal">Next-Gen</span></h2>
            <p class="text-slate-400 text-xs mb-8">Personal & Business Communication</p>
            <input id="username-input" type="text" placeholder="Enter Nickname" class="w-full bg-white/5 border border-white/10 p-4 rounded-xl mb-4 text-center font-bold text-white outline-none focus:border-indigo-500 transition">
            <button id="join-btn" class="w-full bg-indigo-600 text-white font-bold py-4 rounded-xl shadow-lg hover:bg-indigo-700 transition-all active:scale-95">Get Started</button>
        </div>
    </div>

    <div class="glass-container">
        <aside id="sidebar" class="sidebar flex flex-col">
            <div class="p-8 border-b border-white/10 flex justify-between items-center">
                <span class="font-bold text-xl text-white tracking-tighter">Workspace</span>
                <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-xmark"></i></button>
            </div>
            
            <div class="p-4 flex-1 overflow-y-auto space-y-6">
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-4 ml-2">Channels</p>
                    <div onclick="switchChat('global')" id="global-btn" class="user-card active p-4 rounded-2xl flex items-center gap-4 cursor-pointer text-white">
                        <i class="fa-solid fa-hashtag text-indigo-400"></i>
                        <span class="text-sm font-bold">global-hq</span>
                    </div>
                </div>
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-4 ml-2">Direct Messages</p>
                    <div id="users-list" class="space-y-1"></div>
                </div>
            </div>

            <div class="p-6 border-t border-white/10 flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div id="u-avatar" class="w-10 h-10 rounded-xl bg-indigo-600 text-white flex items-center justify-center font-bold shadow-lg">?</div>
                    <div>
                        <p id="u-name" class="text-xs font-bold text-white truncate max-w-[100px]">User</p>
                        <p class="text-[10px] text-green-400 font-medium">Online</p>
                    </div>
                </div>
                <button onclick="logout()" class="text-slate-500 hover:text-red-400"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </aside>

        <main class="flex-1 flex flex-col relative min-w-0">
            <header class="h-20 border-b border-white/10 flex items-center justify-between px-6 md:px-10 shrink-0">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden p-2 text-white/50"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 id="current-chat-title" class="font-bold text-white text-lg tracking-tight"># global-hq</h2>
                        <p class="text-[10px] text-slate-500 font-medium">End-to-end encrypted</p>
                    </div>
                </div>
                <div class="flex gap-4">
                    <button onclick="sendLoc()" class="w-10 h-10 rounded-full border border-white/10 flex items-center justify-center text-white/50 hover:text-indigo-400 transition"><i class="fa-solid fa-location-arrow text-sm"></i></button>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="p-4 md:p-8 shrink-0">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-4 bg-white/5 border border-white/10 p-2 rounded-[1.8rem] shadow-2xl backdrop-blur-xl">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="w-12 h-12 text-slate-400 hover:text-white transition"><i class="fa-solid fa-circle-plus text-xl"></i></button>
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Write a message..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium text-white placeholder:text-slate-500">
                    <button class="bg-indigo-600 text-white px-6 py-3 rounded-2xl font-bold shadow-lg shadow-indigo-500/30 hover:scale-105 active:scale-95 transition-all">Send</button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        // Firebase Config
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
            const path = activeChat === "global" ? 'msgs/global' : `private/${getChatId(myName, activeChat)}`;
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, path), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const div = document.createElement('div');
                div.className = `msg ${isMine ? 'mine' : 'theirs'}`;
                
                let content = data.text || "";
                if(data.fileUrl) content = `<img src="${data.fileUrl}" class="rounded-xl max-w-full md:max-w-xs shadow-lg">`;
                if(data.locUrl) content = `<a href="${data.locUrl}" target="_blank" class="text-xs font-bold underline"><i class="fa-solid fa-map-location-dot mr-2"></i>My Live Location</a>`;

                div.innerHTML = `<p class="text-[9px] font-bold uppercase ${isMine?'text-indigo-200':'text-slate-400'} mb-1">${data.sender}</p><div>${content}</div>`;
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
                        const isActive = activeChat === c.key;
                        d.className = `user-card ${isActive?'active':''} p-4 rounded-2xl flex items-center gap-4 cursor-pointer text-white transition`;
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
            const path = activeChat === "global" ? 'msgs/global' : `private/${getChatId(myName, activeChat)}`;
            push(ref(db, path), { sender: myName, fileUrl: url, time: Date.now() });
        };

        function getChatId(u1, u2) { return [u1, u2].sort().join('_'); }
        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };
        window.sendLoc = () => {
            navigator.geolocation.getCurrentPosition(p => {
                const url = `https://www.google.com/maps?q=${p.coords.latitude},${p.coords.longitude}`;
                const path = activeChat === "global" ? 'msgs/global' : `private/${getChatId(myName, activeChat)}`;
                push(ref(db, path), { sender: myName, locUrl: url, time: Date.now() });
            });
        };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                const path = activeChat === "global" ? 'msgs/global' : `private/${getChatId(myName, activeChat)}`;
                push(ref(db, path), { sender: myName, text: i.value, time: Date.now() });
                i.value = ""; 
            }
        };
    </script>
</body>
</html>
