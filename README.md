<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Max | The Future of Work</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        
        :root { --p: #a855f7; --bg: #020617; --card: #0f172a; }
        
        * { scroll-behavior: smooth; -webkit-tap-highlight-color: transparent; }
        body, html { height: 100%; margin: 0; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; overflow: hidden; }

        /* Modern Scrollbar Layout */
        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: rgba(168, 85, 247, 0.2); border-radius: 20px; }
        ::-webkit-scrollbar-thumb:hover { background: var(--p); }

        /* Glassmorphism Classes */
        .glass { background: rgba(15, 23, 42, 0.7); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.05); }
        
        /* Layout Engine */
        .app-grid { display: grid; grid-template-columns: 280px 1fr; height: 100dvh; width: 100%; }
        @media (max-width: 1024px) { .app-grid { grid-template-columns: 1fr; } }

        /* Sidebar Design */
        .sidebar { background: #070b14; border-right: 1px solid rgba(255,255,255,0.03); display: flex; flex-direction: column; overflow: hidden; transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        @media (max-width: 1024px) { .sidebar { position: fixed; left: -100%; z-index: 2000; width: 85%; height: 100%; } .sidebar.active { left: 0; box-shadow: 20px 0 80px rgba(0,0,0,0.9); } }

        /* Message Bubbles */
        .msg-wrap { display: flex; flex-direction: column; max-width: 82%; position: relative; margin-bottom: 8px; transition: 0.2s; }
        .msg-mine { align-self: flex-end; align-items: flex-end; }
        .msg-other { align-self: flex-start; align-items: flex-start; }
        .bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; line-height: 1.5; position: relative; border: 1px solid rgba(255,255,255,0.05); }
        .msg-mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); color: white; border-bottom-right-radius: 4px; box-shadow: 0 4px 20px rgba(168, 85, 247, 0.25); }
        .msg-other .bubble { background: #1e293b; border-bottom-left-radius: 4px; }

        /* Hover Actions (Slack/Discord Style) */
        .actions-overlay { position: absolute; top: -15px; background: #1e293b; border: 1px solid rgba(255,255,255,0.1); border-radius: 10px; padding: 4px 10px; display: none; gap: 12px; z-index: 10; font-size: 12px; box-shadow: 0 10px 25px rgba(0,0,0,0.5); }
        .msg-wrap:hover .actions-overlay { display: flex; }

        /* Bottom Nav for Mobile */
        .mobile-dock { display: none; position: fixed; bottom: 0; width: 100%; background: rgba(7, 11, 20, 0.95); backdrop-filter: blur(20px); border-top: 1px solid rgba(255,255,255,0.05); padding: 12px 0; z-index: 1000; }
        @media (max-width: 1024px) { .mobile-dock { display: flex; justify-content: space-around; } }

        /* Floating Input */
        .input-box { position: absolute; bottom: 20px; left: 20px; right: 20px; border-radius: 24px; padding: 10px 20px; z-index: 100; transition: 0.3s; }
        @media (max-width: 1024px) { .input-box { bottom: 85px; left: 15px; right: 15px; } }

        #login-screen { background: radial-gradient(circle at top right, #1e1b4b, #020617); }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[3000] flex items-center justify-center p-6">
        <div class="glass p-10 rounded-[3rem] w-full max-w-sm text-center shadow-2xl">
            <div class="w-20 h-20 bg-gradient-to-tr from-purple-600 to-indigo-600 rounded-3xl mx-auto mb-6 flex items-center justify-center text-white text-4xl shadow-glow"><i class="fa-solid fa-crown"></i></div>
            <h1 class="text-4xl font-extrabold mb-2 tracking-tighter">NeonPro</h1>
            <p class="text-slate-500 text-sm mb-10 font-medium">Business & Personal Workspace</p>
            <input id="u-input" type="text" placeholder="Your Username" class="w-full bg-white/5 border border-white/10 p-4 rounded-2xl text-white text-center outline-none focus:border-purple-500 transition-all font-bold mb-4">
            <button id="join-btn" class="w-full bg-purple-600 hover:bg-purple-500 text-white font-bold py-4 rounded-2xl shadow-xl transition-transform active:scale-95">START CHATTING</button>
        </div>
    </div>

    <div class="app-grid">
        <aside id="sidebar" class="sidebar">
            <div class="p-6">
                <div class="flex items-center justify-between mb-8">
                    <span class="text-2xl font-black tracking-tighter text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-cyan-400">NEON.OS</span>
                    <button onclick="window.logout()" class="w-10 h-10 rounded-xl hover:bg-red-500/20 text-red-500 transition-all"><i class="fa-solid fa-power-off"></i></button>
                </div>
                <div class="glass rounded-2xl flex items-center px-4 py-3 mb-6">
                    <i class="fa-solid fa-magnifying-glass text-slate-500 mr-3 text-sm"></i>
                    <input type="text" id="user-search" oninput="window.filterUsers()" placeholder="Find team member..." class="bg-transparent text-sm text-white outline-none w-full">
                </div>
            </div>

            <div class="flex-1 overflow-y-auto px-4 space-y-6">
                <div onclick="window.switchChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-2xl cursor-pointer font-bold flex items-center gap-3 hover:bg-purple-600/20 transition group">
                    <div class="w-8 h-8 bg-purple-600 rounded-lg flex items-center justify-center text-white group-hover:rotate-12 transition-transform"><i class="fa-solid fa-hashtag text-xs"></i></div>
                    <span class="text-purple-100">Global Headquarters</span>
                </div>
                
                <div>
                    <p class="text-[10px] text-slate-500 font-extrabold uppercase tracking-[0.2em] ml-2 mb-4">Direct Messages</p>
                    <div id="users-list" class="space-y-1"></div>
                </div>
            </div>

            <div class="p-6 bg-black/40 border-t border-white/5 flex items-center gap-4">
                <div id="u-avatar" class="w-12 h-12 rounded-2xl bg-gradient-to-br from-purple-500 to-indigo-600 flex items-center justify-center font-bold text-white text-xl">?</div>
                <div class="flex-1 overflow-hidden">
                    <p id="u-name" class="text-sm font-bold text-white truncate">Username</p>
                    <p class="text-[10px] text-green-500 font-bold uppercase tracking-wider">● Online</p>
                </div>
            </div>
        </aside>

        <main class="chat-container">
            <header class="h-20 border-b border-white/5 flex items-center justify-between px-8 glass sticky top-0 z-50">
                <div class="flex items-center gap-4">
                    <button onclick="window.toggleSidebar()" class="lg:hidden text-white w-10 h-10 flex items-center justify-center glass rounded-xl"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 id="chat-title" class="text-lg font-bold tracking-tight">Select Workspace</h2>
                        <div id="chat-sub" class="text-[10px] text-slate-500 font-bold uppercase tracking-widest">Awaiting selection</div>
                    </div>
                </div>
                <div class="flex items-center gap-5 text-slate-400">
                    <i class="fa-solid fa-phone-volume text-sm hover:text-white cursor-pointer transition"></i>
                    <i class="fa-solid fa-video text-sm hover:text-white cursor-pointer transition"></i>
                    <i class="fa-solid fa-ellipsis-vertical text-sm hover:text-white cursor-pointer transition"></i>
                </div>
            </header>

            <div id="chat-flow" class="overflow-y-auto"></div>

            <div class="input-box glass" id="input-container" style="display: none;">
                <div id="reply-box" class="hidden bg-purple-500/10 p-3 rounded-xl mb-3 border-l-4 border-purple-500 flex justify-between items-center">
                    <div class="text-[11px] truncate pr-4 text-purple-200">Replying to <b id="reply-to">User</b>: <span id="reply-msg" class="opacity-60">Message...</span></div>
                    <button onclick="window.cancelReply()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-circle-xmark"></i></button>
                </div>
                <form id="msg-form" class="flex items-center gap-4">
                    <input type="file" id="f-input" class="hidden" onchange="window.upFile(this)">
                    <button type="button" onclick="document.getElementById('f-input').click()" class="text-slate-500 hover:text-cyan-400 transition-colors"><i class="fa-solid fa-plus-circle text-2xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Type your message..." autocomplete="off" class="bg-transparent text-white w-full outline-none text-sm font-medium">
                    <button class="bg-purple-600 hover:bg-purple-500 text-white px-6 py-2.5 rounded-xl font-bold text-xs tracking-widest transition-all active:scale-95 shadow-lg shadow-purple-600/20">SEND</button>
                </form>
            </div>
        </main>
    </div>

    <nav class="mobile-dock">
        <button onclick="window.toggleSidebar()" class="flex flex-col items-center gap-1 text-slate-500"><i class="fa-solid fa-users text-lg"></i><span class="text-[8px] font-bold uppercase">Team</span></button>
        <button onclick="window.switchChat('global')" class="flex flex-col items-center gap-1 text-purple-500"><i class="fa-solid fa-hashtag text-lg"></i><span class="text-[8px] font-bold uppercase">Global</span></button>
        <button onclick="window.logout()" class="flex flex-col items-center gap-1 text-slate-500"><i class="fa-solid fa-power-off text-lg"></i><span class="text-[8px] font-bold uppercase">Exit</span></button>
    </nav>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        const cfg = {
            apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
            authDomain: "live-chat-b810c.firebaseapp.com",
            databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
            projectId: "live-chat-b810c",
            storageBucket: "live-chat-b810c.firebasestorage.app",
            messagingSenderId: "555058795334",
            appId: "1:555058795334:web:f668887409800c32970b47"
        };

        const app = initializeApp(cfg);
        const db = getDatabase(app);
        const st = getStorage(app);
        
        let myN = "", activeC = null, replyTo = null;

        window.onload = () => { const s = localStorage.getItem('neonUser'); if(s) init(s); };
        document.getElementById('join-btn').onclick = () => {
            const v = document.getElementById('u-input').value.trim();
            if(v) { localStorage.setItem('neonUser', v); init(v); }
        };

        function init(n) {
            myN = n;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('u-name').innerText = n;
            document.getElementById('u-avatar').innerText = n[0].toUpperCase();
            set(ref(db, 'online/' + n), true);
            loadUsers();
        }

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');

        window.switchChat = (u) => {
            activeC = u;
            document.getElementById('input-container').style.display = 'block';
            document.getElementById('chat-title').innerText = u === 'global' ? '# global-hq' : `@ ${u}`;
            document.getElementById('chat-sub').innerText = u === 'global' ? 'Main Team Channel' : 'Secure Private Conversation';
            loadMsgs();
            if(window.innerWidth < 1024) document.getElementById('sidebar').classList.remove('active');
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
                    if(d.img) content = `<img src="${d.img}" class="rounded-2xl max-w-full hover:scale-[1.02] transition-transform">`;

                    wrap.innerHTML = `
                        <div class="actions-overlay">
                            <button onclick="window.setReply('${d.sender}','${d.text||"Image"}')"><i class="fa-solid fa-reply"></i></button>
                            <button onclick="window.delMsg('${id}')"><i class="fa-solid fa-trash text-red-400"></i></button>
                        </div>
                        <span class="text-[9px] text-slate-500 font-bold px-3 mb-1 uppercase tracking-tighter">${d.sender}</span>
                        ${d.reply ? `<div class="text-[10px] bg-white/5 p-2 rounded-xl mb-1 border-l-2 border-purple-500 italic opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble">${content}</div>
                        <span class="text-[8px] text-slate-700 font-bold px-3 mt-1">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setReply = (u, m) => {
            replyTo = { to: u, msg: m };
            document.getElementById('reply-box').classList.remove('hidden');
            document.getElementById('reply-to').innerText = u;
            document.getElementById('reply-msg').innerText = m;
            document.getElementById('msg-input').focus();
        };

        window.cancelReply = () => {
            replyTo = null;
            document.getElementById('reply-box').classList.add('hidden');
        };

        window.delMsg = (id) => {
            if(confirm("Delete this message?")) {
                const p = (activeC === 'global' ? 'msgs/global/' : `private/${[myN, activeC].sort().join('_')}/`) + id;
                remove(ref(db, p));
            }
        };

        window.upFile = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `business_media/${Date.now()}`);
            await uploadBytes(r, f);
            const url = await getDownloadURL(r);
            const p = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
            push(ref(db, p), { sender: myN, img: url, time: Date.now() });
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                s.forEach(c => {
                    if(c.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "user-item flex items-center gap-3 p-3 bg-white/5 rounded-2xl cursor-pointer hover:bg-purple-600/10 transition";
                        d.innerHTML = `<div class="w-10 h-10 rounded-xl bg-slate-800 flex items-center justify-center text-xs font-bold border border-white/5">${c.key[0].toUpperCase()}</div>
                                       <div class="flex-1 overflow-hidden"><p class="text-xs font-bold text-white truncate">${c.key}</p><p class="text-[9px] text-green-500 font-bold">ACTIVE</p></div>`;
                        d.onclick = () => window.switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        window.filterUsers = () => {
            const q = document.getElementById('user-search').value.toLowerCase();
            document.querySelectorAll('.user-item').forEach(u => {
                u.style.display = u.innerText.toLowerCase().includes(q) ? 'flex' : 'none';
            });
        };

        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim() && activeC) {
                const p = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
                const m = { sender: myN, text: i.value.trim(), time: Date.now() };
                if(replyTo) { m.reply = replyTo; window.cancelReply(); }
                push(ref(db, p), m);
                i.value = "";
            }
        };

        window.logout = () => {
            if(confirm("Sign out of NeonPro?")) {
                remove(ref(db, 'online/' + myN));
                localStorage.clear();
                location.reload();
            }
        };
    </script>
</body>
</html>
