<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>ChatPro | Ultra Modern Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        
        /* Full Screen & Layout Fix */
        html, body { height: 100%; margin: 0; padding: 0; overflow: hidden; background: #0f172a; font-family: 'Plus Jakarta Sans', sans-serif; }
        .app-container { display: flex; height: 100dvh; width: 100%; overflow: hidden; }

        /* Modern Sidebar */
        .sidebar { 
            width: 280px; background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(20px);
            border-right: 1px solid rgba(255,255,255,0.1); display: flex; flex-direction: column; 
            transition: transform 0.3s ease; z-index: 100;
        }

        /* Chat Flow Fix (Gayab hone wala masla yahan hal hua) */
        #chat-flow { 
            flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px;
            background: linear-gradient(rgba(15, 23, 42, 0.8), rgba(15, 23, 42, 0.8)), url('https://www.transparenttextures.com/patterns/dark-matter.png');
            -webkit-overflow-scrolling: touch;
        }

        /* Mobile View Logic */
        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; }
            .sidebar.active { transform: translateX(100%); }
            .msg { max-width: 90% !important; }
        }

        /* Neon Bubbles */
        .msg { max-width: 75%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; animation: pop 0.3s ease; }
        .msg.mine { align-self: flex-end; background: linear-gradient(135deg, #6366f1, #a855f7); color: white; border-bottom-right-radius: 2px; box-shadow: 0 4px 15px rgba(99, 102, 241, 0.4); }
        .msg.theirs { align-self: flex-start; background: rgba(255, 255, 255, 0.1); color: #e2e8f0; border-bottom-left-radius: 2px; border: 1px solid rgba(255,255,255,0.1); }

        @keyframes pop { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }

        /* Message Box Fix (Ye screen ke bottom se kabhi nahi hilega) */
        .input-area { background: rgba(30, 41, 59, 0.7); backdrop-filter: blur(10px); padding: 12px; border-top: 1px solid rgba(255,255,255,0.1); shrink-0; }
        
        .user-btn { transition: 0.2s; border-radius: 12px; }
        .user-btn:hover { background: rgba(255,255,255,0.05); }
        .user-btn.active { background: rgba(99, 102, 241, 0.2); border-left: 4px solid #6366f1; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[200] bg-[#0f172a] flex items-center justify-center p-6">
        <div class="bg-slate-900 border border-slate-800 p-8 rounded-[2rem] shadow-2xl w-full max-w-sm text-center">
            <div class="w-16 h-16 bg-indigo-600 rounded-2xl flex items-center justify-center mx-auto mb-4 shadow-lg shadow-indigo-500/20">
                <i class="fa-solid fa-comments text-white text-2xl"></i>
            </div>
            <h2 class="text-2xl font-bold text-white mb-6">ChatPro <span class="text-indigo-500">v9.0</span></h2>
            <input id="username-input" type="text" placeholder="Enter Name..." class="w-full p-4 bg-slate-800 border-none rounded-xl mb-4 text-center font-bold text-white outline-none focus:ring-2 focus:ring-indigo-500">
            <button id="join-btn" class="w-full bg-indigo-600 text-white font-bold py-4 rounded-xl shadow-lg active:scale-95 transition">Launch App</button>
        </div>
    </div>

    <div class="app-container">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 border-b border-slate-800 flex justify-between items-center">
                <span class="font-bold text-xl text-white">Workspace</span>
                <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div class="p-4 flex-1 overflow-y-auto">
                <div onclick="switchChat('global')" class="user-btn active p-3 text-indigo-400 font-bold cursor-pointer mb-4"># Global Group</div>
                <p class="text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-2 ml-2">Private Chats</p>
                <div id="users-list" class="space-y-1"></div>
            </div>
            <div class="p-4 border-t border-slate-800 flex items-center gap-3">
                <div id="u-avatar" class="w-10 h-10 rounded-xl bg-indigo-600 text-white flex items-center justify-center font-bold uppercase">?</div>
                <div class="text-white text-xs font-bold" id="u-name">User</div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col min-w-0">
            <header class="h-16 border-b border-slate-800 flex items-center justify-between px-4 shrink-0 bg-slate-900/50 backdrop-blur-md">
                <div class="flex items-center gap-3">
                    <button onclick="toggleSidebar()" class="md:hidden text-slate-400 p-2"><i class="fa-solid fa-bars-staggered"></i></button>
                    <h2 id="current-chat-title" class="font-bold text-white text-sm"># global-group</h2>
                </div>
                <button onclick="sendLoc()" class="text-indigo-400"><i class="fa-solid fa-location-arrow"></i></button>
            </header>

            <div id="chat-flow"></div>

            <div class="input-area">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-2 bg-slate-800/50 p-1.5 rounded-2xl border border-slate-700">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="w-10 h-10 text-slate-500 hover:text-white"><i class="fa-solid fa-plus text-xl"></i></button>
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Write message..." class="flex-1 bg-transparent p-2 outline-none text-sm text-white font-medium">
                    <button class="bg-indigo-600 text-white w-10 h-10 rounded-xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-paper-plane text-xs"></i></button>
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
            document.getElementById('u-avatar').innerText = n[0];
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
                const div = document.createElement('div');
                div.className = `msg ${data.sender === myName ? 'mine' : 'theirs'}`;
                let content = data.text || "";
                if(data.fileUrl) content = `<img src="${data.fileUrl}" class="rounded-xl max-w-[200px] mt-2 border border-white/10">`;
                div.innerHTML = `<p class="text-[9px] font-bold opacity-40 mb-1">${data.sender}</p><div>${content}</div>`;
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
                        d.className = `user-btn p-3 text-slate-300 flex items-center gap-3 cursor-pointer text-sm`;
                        d.innerHTML = `<div class="w-8 h-8 rounded bg-slate-800 flex items-center justify-center text-[10px] font-bold">${c.key[0].toUpperCase()}</div> ${c.key}`;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        window.switchChat = (user) => {
            activeChat = user;
            document.getElementById('current-chat-title').innerText = user === "global" ? "# global-group" : `@ ${user}`;
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
