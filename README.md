<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro | Private & Global Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        :root { --neon-purple: #a855f7; --neon-cyan: #22d3ee; }
        body, html { height: 100%; margin: 0; padding: 0; overflow: hidden; background: #020617; font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; }
        
        .sidebar { width: 280px; background: rgba(15, 23, 42, 0.98); border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100; transition: 0.3s; }
        @media (max-width: 768px) { .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; } .sidebar.active { left: 0; } }

        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); position: relative; }
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 240px !important; display: flex; flex-direction: column; gap: 15px; scroll-behavior: smooth; }

        .msg-card { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); padding: 14px; border-radius: 20px; max-width: 85%; align-self: flex-start; }
        .msg-card.mine { align-self: flex-end; border-color: var(--neon-purple); background: rgba(168, 85, 247, 0.12); }

        .input-container { position: fixed !important; bottom: 30px !important; left: 15px !important; right: 15px !important; z-index: 999 !important; background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(20px); border-radius: 26px; padding: 12px 20px; border: 2px solid var(--neon-purple); }
        @media (min-width: 769px) { .input-container { left: 300px; right: 30px; } }

        .user-item { cursor: pointer; transition: 0.2s; border: 1px solid transparent; }
        .user-item:hover { background: rgba(255,255,255,0.05); }
        .user-item.active-chat { border-color: var(--neon-cyan); background: rgba(34, 211, 238, 0.1); }
        .status-dot { width: 8px; height: 8px; background: #22c55e; border-radius: 50%; box-shadow: 0 0 8px #22c55e; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[1000] bg-[#020617] flex items-center justify-center p-6">
        <div class="bg-slate-900/80 border border-purple-500/30 p-10 rounded-[2.5rem] w-full max-w-sm backdrop-blur-3xl text-center">
            <h2 class="text-3xl font-bold mb-6 text-white">Neon<span class="text-purple-500">Pro</span></h2>
            <input id="username-input" type="text" placeholder="Username" class="w-full p-4 bg-slate-800 rounded-2xl mb-4 text-white text-center outline-none">
            <input id="admin-key" type="password" placeholder="Admin Key (Optional)" class="w-full p-4 bg-slate-800/40 rounded-2xl mb-6 text-white text-center outline-none">
            <button id="join-btn" class="w-full bg-purple-600 text-white font-bold py-4 rounded-2xl shadow-lg">Login</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-8 border-b border-white/5 font-bold text-xl text-white">WORKSPACE</div>
            <div class="flex-1 overflow-y-auto p-4 space-y-4">
                <div onclick="switchChat('global')" id="global-btn" class="p-4 bg-purple-600/20 text-purple-300 rounded-xl font-bold text-sm cursor-pointer border border-purple-500/30">
                    <i class="fa-solid fa-earth-americas mr-2"></i> # Global Chat
                </div>
                <p class="text-[10px] text-slate-500 font-bold uppercase ml-2 tracking-widest">Direct Messages</p>
                <div id="users-list" class="space-y-2"></div>
            </div>
            <div id="admin-panel" class="hidden p-4 bg-red-900/10 border-t border-red-500/20">
                <button onclick="clearChat()" class="w-full py-2 bg-red-600 text-white rounded-lg text-xs font-bold">WIPE CURRENT CHAT</button>
            </div>
            <div class="p-4 border-t border-white/5 flex items-center justify-between">
                <span id="my-display-name" class="text-xs font-bold text-slate-400"></span>
                <button onclick="logout()" class="text-red-400 text-xs font-bold"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </aside>

        <main class="chat-view">
            <header class="h-20 border-b border-white/5 flex items-center px-6 bg-slate-900/40 backdrop-blur-md">
                <button onclick="toggleSidebar()" class="md:hidden text-white mr-4"><i class="fa-solid fa-bars-staggered"></i></button>
                <h2 id="chat-title" class="text-lg font-bold text-white tracking-tight"># Global Chat</h2>
            </header>

            <div id="chat-flow"></div>

            <div class="input-container">
                <form id="msg-form" class="flex items-center gap-4">
                    <input type="file" id="file-upload" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-upload').click()" class="text-slate-400"><i class="fa-solid fa-paperclip text-2xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Write something..." class="bg-transparent text-white flex-1 outline-none">
                    <button class="w-11 h-11 bg-purple-600 rounded-xl flex items-center justify-center text-white"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove, update, increment, onDisconnect } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        
        let myName = localStorage.getItem('neonUser') || "";
        let isAdmin = localStorage.getItem('neonAdmin') === 'true';
        let activeChat = localStorage.getItem('lastChat') || "global";
        const ADMIN_PASS = "SECRET_BOSS";

        if(myName) {
            document.getElementById('login-screen').style.display = 'none';
            initSession();
        }

        document.getElementById('join-btn').onclick = () => {
            const n = document.getElementById('username-input').value.trim();
            const p = document.getElementById('admin-key').value.trim();
            if(n){
                myName = n;
                localStorage.setItem('neonUser', n);
                if(p === ADMIN_PASS) { isAdmin = true; localStorage.setItem('neonAdmin', 'true'); }
                document.getElementById('login-screen').style.display = 'none';
                initSession();
            }
        };

        function initSession() {
            document.getElementById('my-display-name').innerText = myName;
            if(isAdmin) document.getElementById('admin-panel').classList.remove('hidden');
            const userRef = ref(db, 'online/' + myName);
            set(userRef, true);
            onDisconnect(userRef).remove();
            switchChat(activeChat);
            loadUsers();
        }

        window.switchChat = (target) => {
            activeChat = target;
            localStorage.setItem('lastChat', target);
            document.getElementById('chat-title').innerText = target === 'global' ? '# Global Chat' : '@ ' + target;
            
            // UI Toggle
            document.querySelectorAll('.user-item').forEach(el => el.classList.remove('active-chat'));
            if(target !== 'global') document.getElementById('user-' + target)?.classList.add('active-chat');
            
            loadMessages();
            if(window.innerWidth < 768) toggleSidebar();
        };

        function loadMessages() {
            const path = activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            onValue(ref(db, path), (snap) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                snap.forEach(msg => {
                    const d = msg.val();
                    const mid = msg.key;
                    const div = document.createElement('div');
                    div.className = `msg-card ${d.sender === myName ? 'mine' : ''}`;
                    div.innerHTML = `
                        <p class="text-[10px] font-bold ${d.admin ? 'text-yellow-400' : 'text-purple-400'}">${d.sender}</p>
                        <div class="text-sm text-slate-200">${d.text || (d.img ? `<img src="${d.img}" class="rounded-xl">` : '')}</div>
                        ${isAdmin ? `<button onclick="deleteMsg('${mid}')" class="text-[9px] text-red-500 font-bold mt-1 underline">Delete</button>` : ''}
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
                        d.id = 'user-' + c.key;
                        d.className = `user-item flex items-center justify-between p-3 rounded-xl ${activeChat === c.key ? 'active-chat' : ''}`;
                        d.innerHTML = `<span class="text-xs font-bold text-white">${c.key}</span><div class="status-dot"></div>`;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        window.deleteMsg = (id) => {
            const path = activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            remove(ref(db, `${path}/${id}`));
        };

        window.clearChat = () => {
            const path = activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            if(confirm("Wipe this chat?")) remove(ref(db, path));
        };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            const path = activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            if(i.value.trim()){ 
                push(ref(db, path), { sender: myName, text: i.value, time: Date.now(), admin: isAdmin });
                i.value = ""; 
            }
        };

        window.uploadFile = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(storage, `files/${Date.now()}`);
            const path = activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            await uploadBytes(r, f);
            const url = await getDownloadURL(r);
            push(ref(db, path), { sender: myName, img: url, time: Date.now(), admin: isAdmin });
        };

        window.logout = () => { localStorage.clear(); location.reload(); };
        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
    </script>
</body>
</html>
