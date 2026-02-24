<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro | Ultra Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap');
        :root { --neon-purple: #a855f7; --neon-cyan: #22d3ee; --bg-dark: #020617; }
        
        body, html { height: 100%; margin: 0; padding: 0; overflow: hidden; background: var(--bg-dark); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; }
        .app-shell { display: flex; height: 100dvh; width: 100%; position: relative; }
        
        /* Glass Sidebar */
        .sidebar { width: 320px; background: rgba(15, 23, 42, 0.95); border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100; }
        
        /* Modern Chat View */
        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); position: relative; }
        
        /* Dash Design */
        #main-dashboard { position: absolute; inset: 0; background: var(--bg-dark); z-index: 10; display: flex; flex-direction: column; padding: 40px; overflow-y: auto; }
        .glass-card { background: rgba(255,255,255,0.02); border: 1px solid rgba(255,255,255,0.05); border-radius: 24px; padding: 25px; backdrop-filter: blur(10px); }

        /* Message Bubbles (Skype Style) */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 250px !important; display: flex; flex-direction: column; gap: 8px; }
        .msg-bubble { max-width: 75%; padding: 12px 16px; border-radius: 20px; position: relative; font-size: 14px; line-height: 1.5; }
        .msg-left { align-self: flex-start; background: rgba(255,255,255,0.05); border-bottom-left-radius: 4px; border: 1px solid rgba(255,255,255,0.1); }
        .msg-right { align-self: flex-end; background: linear-gradient(135deg, #7e22ce, #a855f7); border-bottom-right-radius: 4px; box-shadow: 0 10px 20px rgba(168, 85, 247, 0.2); }

        /* Input Bar (Fixed Slack Style) */
        .input-bar { display: none; position: fixed; bottom: 30px; left: 340px; right: 30px; background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(20px); border-radius: 24px; border: 1px solid rgba(168, 85, 247, 0.4); padding: 8px 15px; z-index: 1000; box-shadow: 0 20px 50px rgba(0,0,0,0.5); }
        @media (max-width: 768px) { .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; transition: 0.3s; } .sidebar.active { left: 0; } .input-bar { left: 15px; right: 15px; } }

        .status-dot { width: 10px; height: 10px; border-radius: 50%; border: 2px solid var(--bg-dark); }
        .online { background: #22c55e; box-shadow: 0 0 10px #22c55e; }
        
        /* Search Bar */
        .search-box { background: rgba(255,255,255,0.05); border-radius: 12px; padding: 8px 15px; margin: 15px; border: 1px solid rgba(255,255,255,0.05); }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[2000] bg-[#020617] flex items-center justify-center p-6">
        <div class="bg-slate-900 border border-purple-500/20 p-12 rounded-[3rem] w-full max-w-md text-center shadow-2xl">
            <div class="w-20 h-20 bg-purple-600 rounded-3xl mx-auto mb-6 flex items-center justify-center text-4xl shadow-glow"><i class="fa-solid fa-bolt text-white"></i></div>
            <h2 class="text-3xl font-extrabold text-white mb-2 tracking-tighter">NeonPro <span class="text-purple-500 text-sm align-top">OS</span></h2>
            <p class="text-slate-500 mb-8 text-sm">Secure Enterprise Communication</p>
            <input id="username-input" type="text" placeholder="Your Name" class="w-full p-4 bg-slate-800 rounded-2xl mb-4 text-white font-semibold outline-none border border-transparent focus:border-purple-500 transition-all text-center">
            <button id="join-btn" class="w-full bg-purple-600 hover:bg-purple-500 text-white font-bold py-4 rounded-2xl transition-transform active:scale-95 shadow-xl">AUTHENTICATE</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 pb-2">
                <div class="flex items-center justify-between mb-6">
                    <span class="text-xl font-bold tracking-tighter">Neon<span class="text-purple-500">Pro</span></span>
                    <i class="fa-solid fa-pen-to-square text-slate-400 cursor-pointer"></i>
                </div>
                <div class="search-box flex items-center gap-3">
                    <i class="fa-solid fa-magnifying-glass text-slate-500 text-xs"></i>
                    <input type="text" placeholder="Search chats..." class="bg-transparent outline-none text-xs text-white w-full">
                </div>
            </div>

            <div class="flex-1 overflow-y-auto px-4">
                <p class="text-[10px] text-slate-500 uppercase font-bold tracking-widest mb-4 ml-2">Channels</p>
                <div onclick="switchChat('global')" class="flex items-center gap-3 p-3 mb-2 rounded-xl bg-purple-500/10 border border-purple-500/20 cursor-pointer text-purple-300">
                    <div class="w-8 h-8 rounded-lg bg-purple-600 flex items-center justify-center text-white"><i class="fa-solid fa-globe text-xs"></i></div>
                    <span class="font-semibold text-sm"># global-hq</span>
                </div>

                <p class="text-[10px] text-slate-500 uppercase font-bold tracking-widest my-6 ml-2">Direct Messages</p>
                <div id="users-list" class="space-y-1"></div>
            </div>

            <div class="p-4 space-y-2 border-t border-white/5">
                <div class="flex items-center gap-4 p-3 hover:bg-white/5 rounded-xl cursor-pointer text-slate-400 text-sm"><i class="fa-solid fa-gear"></i> Settings</div>
                <div class="flex items-center gap-4 p-3 hover:bg-white/5 rounded-xl cursor-pointer text-slate-400 text-sm"><i class="fa-solid fa-address-book"></i> Contacts</div>
                <div class="p-4 bg-white/5 rounded-2xl flex items-center gap-3">
                    <div id="u-avatar" class="w-10 h-10 rounded-xl bg-purple-600 flex items-center justify-center font-bold text-white shadow-lg">?</div>
                    <div class="flex-1 truncate"><p id="u-name" class="text-xs font-bold text-white">User</p><p class="text-[10px] text-green-500">Active Now</p></div>
                    <button onclick="logout()" class="text-slate-500"><i class="fa-solid fa-right-from-bracket"></i></button>
                </div>
            </div>
        </aside>

        <main class="chat-view">
            <div id="main-dashboard">
                <h1 class="text-5xl font-extrabold mb-4 tracking-tighter">Hello, <span id="dash-user" class="text-purple-500">Specialist</span></h1>
                <p class="text-slate-500 text-lg mb-10">Welcome to your secure communications hub.</p>
                
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <div class="glass-card">
                        <i class="fa-solid fa-users text-cyan-400 text-2xl mb-4"></i>
                        <h3 class="text-3xl font-bold" id="stat-online">0</h3>
                        <p class="text-slate-500 text-xs uppercase font-bold">Team Members Online</p>
                    </div>
                    <div class="glass-card">
                        <i class="fa-solid fa-paper-plane text-purple-500 text-2xl mb-4"></i>
                        <h3 class="text-3xl font-bold">Live</h3>
                        <p class="text-slate-500 text-xs uppercase font-bold">Database Stream</p>
                    </div>
                    <div class="glass-card">
                        <i class="fa-solid fa-shield-halved text-green-500 text-2xl mb-4"></i>
                        <h3 class="text-3xl font-bold">E2EE</h3>
                        <p class="text-slate-500 text-xs uppercase font-bold">Encryption Status</p>
                    </div>
                </div>

                <div class="mt-10 glass-card">
                    <h4 class="text-sm font-bold text-slate-400 mb-4 uppercase tracking-widest">Recent Activity</h4>
                    <div class="space-y-4">
                        <div class="flex items-center gap-4 text-sm text-slate-300"><i class="fa-solid fa-circle-check text-green-500"></i> Server synchronization complete.</div>
                        <div class="flex items-center gap-4 text-sm text-slate-300"><i class="fa-solid fa-clock text-yellow-500"></i> No unread private messages.</div>
                    </div>
                </div>
            </div>

            <header class="h-20 border-b border-white/5 flex items-center justify-between px-8 bg-slate-900/40 backdrop-blur-xl">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-white"><i class="fa-solid fa-bars-staggered"></i></button>
                    <h2 id="chat-title" class="text-lg font-bold">Select a Chat</h2>
                </div>
                <div class="flex items-center gap-4 text-slate-400">
                    <i class="fa-solid fa-phone cursor-pointer hover:text-white"></i>
                    <i class="fa-solid fa-video cursor-pointer hover:text-white"></i>
                    <i class="fa-solid fa-circle-info cursor-pointer hover:text-white"></i>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-bar" id="input-container">
                <form id="msg-form" class="flex items-center gap-4">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="w-10 h-10 rounded-full flex items-center justify-center text-slate-400 hover:bg-white/5 hover:text-cyan-400"><i class="fa-solid fa-plus text-xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Type a message here..." class="bg-transparent text-white w-full outline-none font-medium py-2">
                    <button class="bg-purple-600 text-white px-6 py-2 rounded-xl font-bold shadow-glow flex items-center gap-2">Send <i class="fa-solid fa-paper-plane text-[10px]"></i></button>
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
            document.getElementById('dash-user').innerText = n;
            document.getElementById('u-avatar').innerText = n[0].toUpperCase();
            set(ref(db, 'online/' + n), true);
            loadUsers();
        }

        window.switchChat = (u) => { 
            activeChat = u; 
            document.getElementById('main-dashboard').style.display = 'none';
            document.getElementById('input-container').style.display = 'flex';
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
                    const wrap = document.createElement('div');
                    wrap.className = `flex flex-col ${isMine ? 'items-end' : 'items-start'} mb-4`;
                    
                    let content = d.text || "";
                    if(d.file) content = `<img src="${d.file}" class="rounded-2xl max-w-[250px] cursor-pointer shadow-lg">`;
                    
                    wrap.innerHTML = `
                        <div class="flex items-center gap-2 mb-1 px-2">
                            <span class="text-[9px] font-bold uppercase text-slate-500">${d.sender}</span>
                        </div>
                        <div class="msg-bubble ${isMine ? 'msg-right' : 'msg-left'}">
                            ${content}
                        </div>
                        <span class="text-[8px] text-slate-600 mt-1 px-2">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
                    `;
                    flow.appendChild(wrap);
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
                    if(c.key !== myName) {
                        const d = document.createElement('div');
                        d.className = "flex items-center gap-3 p-3 rounded-xl cursor-pointer hover:bg-white/5 transition";
                        d.innerHTML = `
                            <div class="relative">
                                <div class="w-10 h-10 rounded-xl bg-slate-800 flex items-center justify-center font-bold text-xs">${c.key[0].toUpperCase()}</div>
                                <div class="status-dot online absolute -bottom-1 -right-1"></div>
                            </div>
                            <div class="flex-1"><p class="text-sm font-semibold text-white">${c.key}</p><p class="text-[10px] text-slate-500">Available</p></div>
                        `;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
                document.getElementById('stat-online').innerText = count;
            });
        }

        window.uploadFile = async (i) => {
            const file = i.files[0]; if(!file) return;
            const r = sRef(storage, `neon_files/${Date.now()}`);
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
