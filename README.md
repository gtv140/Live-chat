<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Ultra | Workspace 2026</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap');
        :root { --neon-p: #a855f7; --neon-c: #22d3ee; --bg: #020617; }
        
        body, html { height: 100%; margin: 0; padding: 0; overflow: hidden; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; }
        .app-shell { display: flex; height: 100dvh; width: 100%; overflow: hidden; }
        
        /* Sidebar Responsive */
        .sidebar { width: 300px; background: rgba(15, 23, 42, 0.98); border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        @media (max-width: 768px) { .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; } .sidebar.active { left: 0; box-shadow: 20px 0 50px rgba(0,0,0,0.8); } }

        /* Chat View Area */
        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); position: relative; width: 100%; }
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 180px !important; display: flex; flex-direction: column; gap: 12px; scroll-behavior: smooth; }

        /* Modern Bubbles */
        .msg-wrap { display: flex; flex-direction: column; max-width: 85%; position: relative; }
        .msg-mine { align-self: flex-end; align-items: flex-end; }
        .msg-other { align-self: flex-start; align-items: flex-start; }
        .bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; border: 1px solid rgba(255,255,255,0.05); position: relative; }
        .msg-mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); color: white; border-bottom-right-radius: 4px; box-shadow: 0 4px 15px rgba(168, 85, 247, 0.2); }
        .msg-other .bubble { background: rgba(255,255,255,0.08); border-bottom-left-radius: 4px; backdrop-filter: blur(10px); }

        /* Slack Actions Menu */
        .actions { position: absolute; top: -25px; background: #1e293b; border-radius: 8px; padding: 4px 8px; display: none; border: 1px solid rgba(255,255,255,0.1); gap: 12px; z-index: 10; box-shadow: 0 4px 10px rgba(0,0,0,0.3); }
        .msg-wrap:hover .actions { display: flex; }
        .msg-mine .actions { right: 0; }
        .msg-other .actions { left: 0; }

        /* Dashboard Design */
        #dash { position: absolute; inset: 0; background: var(--bg); z-index: 50; padding: 30px; display: flex; flex-direction: column; overflow-y: auto; }
        .stat-card { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); border-radius: 24px; padding: 20px; text-align: center; }

        /* Universal Input Bar */
        .input-bar { display: none; position: fixed; bottom: 20px; left: 320px; right: 20px; background: rgba(15, 23, 42, 0.9); backdrop-filter: blur(20px); border-radius: 22px; border: 1px solid rgba(168, 85, 247, 0.5); padding: 8px 16px; z-index: 1000; transition: 0.3s; }
        @media (max-width: 768px) { .input-bar { left: 15px; right: 15px; bottom: 15px; } }

        .status-dot { width: 10px; height: 10px; border-radius: 50%; border: 2px solid #020617; }
        .online { background: #22c55e; box-shadow: 0 0 8px #22c55e; }
        .reply-preview { display: none; background: rgba(168, 85, 247, 0.15); padding: 8px 15px; border-radius: 12px; font-size: 11px; margin-bottom: 8px; border-left: 3px solid var(--neon-p); align-items: center; justify-content: space-between; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[2000] bg-black flex items-center justify-center p-6 text-center">
        <div class="bg-slate-900/50 border border-purple-500/20 p-10 rounded-[3rem] w-full max-w-sm backdrop-blur-3xl shadow-2xl">
            <div class="w-16 h-16 bg-purple-600 rounded-2xl mx-auto mb-6 flex items-center justify-center text-white text-3xl shadow-glow"><i class="fa-solid fa-bolt"></i></div>
            <h2 class="text-3xl font-bold text-white mb-2 tracking-tighter">NeonPro <span class="text-purple-500">OS</span></h2>
            <p class="text-slate-500 text-sm mb-8">Next-Gen Team Collaboration</p>
            <input id="u-input" type="text" placeholder="Enter Username" class="w-full p-4 bg-slate-800/50 rounded-2xl mb-4 text-white text-center outline-none border border-transparent focus:border-purple-500 transition-all font-semibold">
            <button id="join-btn" class="w-full bg-purple-600 hover:bg-purple-500 text-white font-bold py-4 rounded-2xl shadow-xl transition-all active:scale-95">AUTHENTICATE</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-6">
                <div class="flex items-center justify-between mb-8">
                    <span class="text-xl font-extrabold tracking-tighter">WORKSPACE</span>
                    <button onclick="logout()" class="text-slate-500 hover:text-red-500 transition-colors"><i class="fa-solid fa-power-off text-lg"></i></button>
                </div>
                <div class="bg-white/5 rounded-2xl flex items-center px-4 py-3 border border-white/5 mb-6">
                    <i class="fa-solid fa-magnifying-glass text-slate-500 mr-3 text-sm"></i>
                    <input type="text" id="user-search" oninput="filterUsers()" placeholder="Search team members..." class="bg-transparent text-sm text-white outline-none w-full">
                </div>
            </div>

            <div class="flex-1 overflow-y-auto px-4 space-y-6">
                <div onclick="switchChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/30 text-purple-300 rounded-2xl cursor-pointer font-bold text-sm flex items-center gap-3 hover:bg-purple-600/20 transition">
                    <div class="w-8 h-8 bg-purple-600 rounded-lg flex items-center justify-center text-white"><i class="fa-solid fa-hashtag text-xs"></i></div> global-hq
                </div>
                
                <div>
                    <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest ml-2 mb-4">Direct Messages</p>
                    <div id="users-list" class="space-y-1"></div>
                </div>
            </div>

            <div class="p-6 border-t border-white/5 bg-black/40">
                <div class="flex items-center gap-4">
                    <div id="u-avatar" class="w-12 h-12 rounded-2xl bg-gradient-to-br from-purple-500 to-indigo-600 flex items-center justify-center font-bold text-white text-xl shadow-lg">?</div>
                    <div class="flex-1 overflow-hidden">
                        <p id="u-name" class="text-sm font-bold text-white truncate">User</p>
                        <p class="text-[10px] text-green-500 font-bold flex items-center gap-1">● Online Now</p>
                    </div>
                </div>
            </div>
        </aside>

        <main class="chat-view">
            <div id="dash">
                <div class="flex justify-between items-center mb-10">
                    <h1 class="text-4xl font-extrabold tracking-tighter">Neon<span class="text-purple-500">Pro</span> Console</h1>
                    <button onclick="logout()" class="bg-red-500/10 hover:bg-red-500 text-red-500 hover:text-white px-5 py-2 rounded-xl text-xs font-bold transition-all">SIGN OUT</button>
                </div>
                
                <div class="grid grid-cols-1 sm:grid-cols-3 gap-6 mb-10">
                    <div class="stat-card">
                        <p class="text-slate-500 text-[10px] font-bold uppercase mb-2 tracking-widest">Active Personnel</p>
                        <h2 id="stat-online" class="text-4xl font-bold text-cyan-400">0</h2>
                    </div>
                    <div class="stat-card">
                        <p class="text-slate-500 text-[10px] font-bold uppercase mb-2 tracking-widest">Latency</p>
                        <h2 class="text-4xl font-bold text-green-500">12ms</h2>
                    </div>
                    <div class="stat-card">
                        <p class="text-slate-500 text-[10px] font-bold uppercase mb-2 tracking-widest">Security</p>
                        <h2 class="text-4xl font-bold text-purple-500">AES-256</h2>
                    </div>
                </div>

                <div class="stat-card flex-1 text-left">
                    <h3 class="font-bold text-lg mb-4 flex items-center gap-2"><i class="fa-solid fa-compass text-purple-500"></i> Workspace Navigation</h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="p-4 bg-white/5 rounded-2xl hover:bg-white/10 cursor-pointer transition"><p class="font-bold text-sm mb-1">General Chat</p><p class="text-xs text-slate-500">Connect with everyone in the company.</p></div>
                        <div class="p-4 bg-white/5 rounded-2xl hover:bg-white/10 cursor-pointer transition"><p class="font-bold text-sm mb-1">Direct Messages</p><p class="text-xs text-slate-500">Secure 1-on-1 private conversations.</p></div>
                        <div class="p-4 bg-white/5 rounded-2xl hover:bg-white/10 cursor-pointer transition"><p class="font-bold text-sm mb-1">Workspace Settings</p><p class="text-xs text-slate-500">Modify your profile and preferences.</p></div>
                        <div class="p-4 bg-white/5 rounded-2xl hover:bg-white/10 cursor-pointer transition"><p class="font-bold text-sm mb-1">Media Vault</p><p class="text-xs text-slate-500">View all shared photos and documents.</p></div>
                    </div>
                </div>
            </div>

            <header class="h-20 border-b border-white/5 flex items-center justify-between px-6 bg-slate-900/40 backdrop-blur-2xl">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-white w-10 h-10 flex items-center justify-center bg-white/5 rounded-xl"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 id="chat-title" class="text-lg font-bold">Main Dashboard</h2>
                        <p id="chat-subtitle" class="text-[10px] text-slate-500 font-bold uppercase tracking-widest">Select a channel to begin</p>
                    </div>
                </div>
                <div class="flex items-center gap-4">
                    <i class="fa-solid fa-phone text-slate-500 hover:text-white cursor-pointer transition"></i>
                    <i class="fa-solid fa-video text-slate-500 hover:text-white cursor-pointer transition"></i>
                    <button onclick="logout()" class="md:flex hidden items-center gap-2 bg-white/5 hover:bg-red-500/20 text-slate-400 hover:text-red-400 px-4 py-2 rounded-xl text-[10px] font-bold transition-all border border-white/5">
                        <i class="fa-solid fa-power-off"></i> EXIT
                    </button>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-bar" id="input-container">
                <div id="reply-box" class="reply-preview">
                    <p class="truncate mr-4 text-slate-300">Replying to <b id="reply-to-user">User</b></p>
                    <button onclick="cancelReply()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-circle-xmark"></i></button>
                </div>
                <form id="msg-form" class="flex items-center gap-3">
                    <input type="file" id="f-input" class="hidden" accept="image/*" onchange="upFile(this)">
                    <button type="button" onclick="document.getElementById('f-input').click()" class="w-10 h-10 flex items-center justify-center text-slate-400 hover:bg-white/10 hover:text-cyan-400 rounded-xl transition-all">
                        <i class="fa-solid fa-circle-plus text-2xl"></i>
                    </button>
                    <input id="msg-input" type="text" placeholder="Type your message..." class="bg-transparent text-white w-full outline-none font-medium text-sm py-2" autocomplete="off">
                    <button class="bg-purple-600 hover:bg-purple-500 text-white px-6 py-2 rounded-xl font-bold shadow-glow-purple transition-all flex items-center gap-2">
                        <span>SEND</span> <i class="fa-solid fa-paper-plane text-[10px]"></i>
                    </button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        const fbCfg = {
            apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
            authDomain: "live-chat-b810c.firebaseapp.com",
            databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
            projectId: "live-chat-b810c",
            storageBucket: "live-chat-b810c.firebasestorage.app",
            messagingSenderId: "555058795334",
            appId: "1:555058795334:web:f668887409800c32970b47"
        };

        const app = initializeApp(fbCfg);
        const db = getDatabase(app);
        const st = getStorage(app);
        let myN = "", activeC = null, replyTo = null;

        window.onload = () => { if(localStorage.getItem('neonUser')) init(localStorage.getItem('neonUser')); };
        document.getElementById('join-btn').onclick = () => { 
            const n = document.getElementById('u-input').value.trim(); 
            if(n){ localStorage.setItem('neonUser', n); init(n); }
        };

        function init(n) {
            myN = n;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('u-name').innerText = n;
            document.getElementById('u-avatar').innerText = n[0].toUpperCase();
            set(ref(db, 'online/' + n), true);
            loadUsers();
        }

        window.switchChat = (u) => { 
            activeC = u; 
            document.getElementById('dash').style.display = 'none';
            document.getElementById('input-container').style.display = 'block';
            document.getElementById('chat-title').innerText = u === 'global' ? '# global-hq' : `@ ${u}`; 
            document.getElementById('chat-subtitle').innerText = u === 'global' ? 'Main Team Channel' : 'Secure Private Chat';
            loadMsgs(); 
            if(window.innerWidth < 768) toggleSidebar(); 
        };

        function loadMsgs() {
            const p = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
            onValue(ref(db, p), (s) => {
                const flow = document.getElementById('chat-flow'); flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isM = d.sender === myN;
                    const wrap = document.createElement('div');
                    wrap.className = `msg-wrap ${isM ? 'msg-mine' : 'msg-other'}`;
                    
                    let content = d.text || "";
                    if(d.img) content = `<img src="${d.img}" class="rounded-2xl max-w-full hover:scale-105 transition-transform duration-300">`;
                    
                    wrap.innerHTML = `
                        <div class="actions">
                            <button onclick="setReply('${d.sender}', '${d.text || "Image"}')" class="text-white hover:text-cyan-400 transition"><i class="fa-solid fa-reply"></i></button>
                            <button onclick="delMsg('${id}')" class="text-red-500 hover:text-red-400 transition"><i class="fa-solid fa-trash-can"></i></button>
                        </div>
                        <span class="text-[9px] font-bold text-slate-600 uppercase mb-1 px-3">${d.sender}</span>
                        ${d.reply ? `<div class="text-[10px] bg-white/5 p-2 rounded-xl mb-1 border-l-2 border-purple-500 text-slate-400 italic">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble">${content}</div>
                        <span class="text-[8px] text-slate-700 mt-1 px-3">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setReply = (to, msg) => { 
            replyTo = {to, msg}; 
            document.getElementById('reply-box').style.display = 'flex'; 
            document.getElementById('reply-to-user').innerText = to;
            document.getElementById('msg-input').focus();
        };
        window.cancelReply = () => { replyTo = null; document.getElementById('reply-box').style.display = 'none'; };
        window.delMsg = (id) => { if(confirm("Delete this message?")) remove(ref(db, (activeC === 'global' ? 'msgs/global/' : `private/${[myN, activeC].sort().join('_')}/`) + id)); };

        window.upFile = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `neon_media/${Date.now()}`);
            await uploadBytes(r, f);
            const url = await getDownloadURL(r);
            push(ref(db, activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`), { sender: myN, img: url, time: Date.now() });
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                let count = 0; list.innerHTML = "";
                snap.forEach(c => {
                    count++;
                    if(c.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "user-item flex items-center gap-3 p-3 rounded-2xl cursor-pointer hover:bg-white/5 transition";
                        d.innerHTML = `
                            <div class="relative">
                                <div class="w-10 h-10 rounded-xl bg-slate-800 flex items-center justify-center font-bold text-xs">${c.key[0].toUpperCase()}</div>
                                <div class="status-dot online absolute -bottom-1 -right-1"></div>
                            </div>
                            <div class="flex-1"><p class="text-sm font-bold text-white">${c.key}</p><p class="text-[9px] text-green-500 font-bold uppercase">Active</p></div>
                        `;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
                document.getElementById('stat-online').innerText = count;
            });
        }

        window.filterUsers = () => {
            const val = document.getElementById('user-search').value.toLowerCase();
            document.querySelectorAll('.user-item').forEach(item => {
                item.style.display = item.innerText.toLowerCase().includes(val) ? 'flex' : 'none';
            });
        };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault(); const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                const d = { sender: myN, text: i.value        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault(); 
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                // Message Data Object
                const d = { 
                    sender: myN, 
                    text: i.value, 
                    time: Date.now() 
                };
                
                // Agar Reply mode on hai to reply details add karein
                if(replyTo) { 
                    d.reply = replyTo; 
                    cancelReply(); // Reply box band karein
                }

                // Firebase path: Global ya Private
                const path = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
                push(ref(db, path), d);
                
                i.value = ""; // Input clear karein
                i.focus();
            }
        };

        // Mobile Sidebar Toggle
        window.toggleSidebar = () => {
            document.getElementById('sidebar').classList.toggle('active');
        };

        // Logout function with Firebase clean-up
        window.logout = () => { 
            if(confirm("Are you sure you want to exit NeonPro?")) { 
                if(myN) {
                    remove(ref(db, 'online/' + myN)); // Online status khatam karein
                }
                localStorage.clear(); 
                location.reload(); 
            } 
        };

        // Press Enter to Send (Mobile optimization)
        document.getElementById('msg-input').addEventListener('keypress', (e) => {
            if (e.key === 'Enter' && !e.shiftKey && window.innerWidth > 768) {
                e.preventDefault();
                document.getElementById('msg-form').dispatchEvent(new Event('submit'));
            }
        });

    </script>
</body>
</html>
