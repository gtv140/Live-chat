<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Enterprise | Final</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        :root { --neon-purple: #a855f7; --neon-cyan: #22d3ee; }
        body, html { height: 100%; margin: 0; padding: 0; overflow: hidden; background: #020617; font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; }
        .app-shell { display: flex; height: 100dvh; width: 100%; position: relative; }
        .sidebar { width: 280px; background: rgba(15, 23, 42, 0.98); border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100; transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); position: relative; height: 100%; }
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 220px !important; display: flex; flex-direction: column; gap: 12px; scroll-behavior: smooth; }
        
        /* Dashboard Overlay */
        #main-dashboard { position: absolute; inset: 0; display: flex; flex-direction: column; items-center; justify-content: center; background: #020617; z-index: 10; text-align: center; padding: 20px; }
        .stat-card { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); padding: 15px; border-radius: 20px; width: 140px; }

        .msg-card { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); padding: 12px; border-radius: 20px; max-width: 85%; align-self: flex-start; position: relative; }
        .msg-card.mine { align-self: flex-end; border-color: var(--neon-purple); background: rgba(168, 85, 247, 0.12); }
        
        /* Input Bar Hidden by Default */
        .input-bar { display: none; position: fixed !important; bottom: 25px !important; left: 15px !important; right: 15px !important; z-index: 999 !important; background: rgba(15, 23, 42, 0.9) !important; backdrop-filter: blur(25px); border-radius: 26px; padding: 12px 20px; border: 2px solid var(--neon-purple); box-shadow: 0 0 35px rgba(168, 85, 247, 0.4); }
        @media (min-width: 769px) { .input-bar { left: 300px; right: 30px; } }
        
        .status-dot { width: 10px; height: 10px; background: #22c55e; border-radius: 50%; box-shadow: 0 0 8px #22c55e; }
        .side-opt { display: flex; align-items: center; gap: 12px; padding: 10px 15px; border-radius: 12px; font-size: 13px; color: #94a3b8; transition: 0.2s; cursor: pointer; }
        .side-opt:hover { background: rgba(255,255,255,0.05); color: white; }
        @media (max-width: 768px) { .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; } .sidebar.active { left: 0; } }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[1000] bg-[#020617] flex items-center justify-center p-6 text-center">
        <div class="bg-slate-900/80 border border-purple-500/30 p-10 rounded-[2.5rem] w-full max-w-sm backdrop-blur-2xl">
            <h2 class="text-3xl font-bold mb-6 text-white tracking-tighter">Neon<span class="text-purple-500">Pro</span></h2>
            <input id="username-input" type="text" placeholder="Enter Your Name..." class="w-full p-4 bg-slate-800 rounded-2xl mb-4 text-white text-center font-bold outline-none border border-transparent">
            <button id="join-btn" class="w-full bg-purple-600 text-white font-bold py-4 rounded-2xl shadow-lg">Launch System</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-8 border-b border-white/5 font-bold text-xl text-white">WORKSPACE</div>
            <div class="flex-1 overflow-y-auto p-4 space-y-4">
                <div onclick="switchChat('global')" class="p-3 bg-purple-500/10 border border-purple-500/20 text-purple-300 rounded-xl cursor-pointer font-bold text-sm"># global-hq</div>
                <p class="text-[10px] text-slate-500 uppercase font-bold ml-2">Team Members</p>
                <div id="users-list" class="space-y-1"></div>
                
                <hr class="border-white/5 my-4">
                <div class="side-opt"><i class="fa-solid fa-gear"></i> Settings</div>
                <div class="side-opt"><i class="fa-solid fa-circle-info"></i> About</div>
                <div class="side-opt"><i class="fa-solid fa-envelope"></i> Contact Us</div>
            </div>
            <div class="p-6 bg-black/40 flex items-center gap-4">
                <div id="u-avatar" class="w-10 h-10 rounded-xl bg-purple-600 flex items-center justify-center font-bold text-white uppercase">?</div>
                <div class="flex-1 truncate text-sm font-bold" id="u-name">User</div>
                <button onclick="logout()" class="text-slate-500 hover:text-red-400"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </aside>

        <main class="chat-view">
            <div id="main-dashboard">
                <h1 class="text-4xl font-bold mb-2">Welcome to NeonPro</h1>
                <p class="text-slate-500 mb-8">Select a conversation to start messaging</p>
                <div class="flex gap-4">
                    <div class="stat-card">
                        <div class="text-2xl font-bold text-cyan-400" id="stat-online">0</div>
                        <div class="text-[10px] uppercase font-bold text-slate-500">Users Online</div>
                    </div>
                    <div class="stat-card">
                        <div class="text-2xl font-bold text-purple-400">Live</div>
                        <div class="text-[10px] uppercase font-bold text-slate-500">System Status</div>
                    </div>
                </div>
            </div>

            <header class="h-20 border-b border-white/5 flex items-center justify-between px-6 bg-slate-900/20 backdrop-blur-md">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-white"><i class="fa-solid fa-bars-staggered text-xl"></i></button>
                    <h2 id="chat-title" class="text-xl font-bold text-white tracking-tight">Main Dashboard</h2>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-bar" id="input-container">
                <form id="msg-form" class="flex items-center gap-4">
                    <input type="file" id="file-input" class="hidden" accept="image/*" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="text-slate-400 hover:text-cyan-400"><i class="fa-solid fa-image text-2xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Type a message..." class="bg-transparent text-white w-full outline-none">
                    <button class="w-11 h-11 bg-purple-600 rounded-xl flex items-center justify-center text-white shadow-xl"><i class="fa-solid fa-paper-plane text-xs"></i></button>
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
        let myName = "", activeChat = null;

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
            loadUsers();
        }

        window.switchChat = (u) => { 
            activeChat = u; 
            document.getElementById('main-dashboard').style.display = 'none';
            document.getElementById('input-container').style.display = 'block';
            document.getElementById('chat-title').innerText = u === 'global' ? '# global-hq' : `@ ${u}`; 
            loadMessages(); 
            if(window.innerWidth < 768) toggleSidebar(); 
        };

        function loadMessages() {
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            onValue(ref(db, path), (snap) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                snap.forEach(msg => {
                    const d = msg.val();
                    const isMine = d.sender === myName;
                    const div = document.createElement('div');
                    div.className = `msg-card ${isMine ? 'mine' : ''}`;
                    let content = d.text || "";
                    if(d.file) content = `<img src="${d.file}" class="rounded-xl max-w-full">`;
                    div.innerHTML = `<span class="text-[10px] font-bold ${isMine ? 'text-purple-300' : 'text-cyan-400'} uppercase">${d.sender}</span><div class="text-white text-sm mt-1">${content}</div>`;
                    flow.appendChild(div);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        function loadUsers() {
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                let count = 0;
                list.innerHTML = "";
                snap.forEach(c => {
                    count++;
                    const d = document.createElement('div');
                    d.className = `flex items-center gap-3 p-3 rounded-xl cursor-pointer hover:bg-white/5 transition`;
                    d.innerHTML = `<div class="w-8 h-8 rounded-lg bg-slate-800 flex items-center justify-center font-bold text-xs">${c.key[0]}</div><div class="flex-1 text-xs font-bold">${c.key}</div><div class="status-dot"></div>`;
                    if(c.key !== myName) d.onclick = () => switchChat(c.key);
                    list.appendChild(d);
                });
                document.getElementById('stat-online').innerText = count;
            });
        }

        window.uploadFile = async (i) => {
            const file = i.files[0]; if(!file) return;
            const r = sRef(storage, `uploads/${Date.now()}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            push(ref(db, activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, file: url, time: Date.now() });
        };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault(); const i = document.getElementById('msg-input');
            if(i.value.trim()){ push(ref(db, activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, text: i.value, time: Date.now() }); i.value = ""; }
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
        window.logout = () => { localStorage.clear(); location.reload(); };
    </script>
</body>
</html>
