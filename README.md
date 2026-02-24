<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro v3 | Ultimate Hybrid</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap');
        :root { --neon-p: #a855f7; --bg: #020617; }
        
        body, html { height: 100%; margin: 0; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; overflow: hidden; }
        
        /* Sidebar for Desktop */
        .sidebar { width: 280px; background: #0f172a; border-right: 1px solid rgba(255,255,255,0.05); transition: 0.3s; }
        @media (max-width: 1024px) { .sidebar { display: none; } .sidebar.active { display: flex; position: fixed; inset: 0; width: 100%; z-index: 2000; } }

        /* Main Chat Area */
        .chat-container { flex: 1; display: flex; flex-direction: column; position: relative; height: 100dvh; }
        #chat-flow { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 100px; display: flex; flex-direction: column; gap: 10px; }

        /* Modern Bubbles */
        .msg-wrap { display: flex; flex-direction: column; max-width: 85%; position: relative; margin-bottom: 4px; }
        .msg-mine { align-self: flex-end; align-items: flex-end; }
        .msg-other { align-self: flex-start; align-items: flex-start; }
        .bubble { padding: 10px 14px; border-radius: 18px; font-size: 14px; position: relative; word-break: break-word; }
        .msg-mine .bubble { background: #7e22ce; border-bottom-right-radius: 4px; }
        .msg-other .bubble { background: #1e293b; border-bottom-left-radius: 4px; }

        /* Floating Input Bar (Skype/Discord Style) */
        .input-area { position: absolute; bottom: 0; left: 0; right: 0; background: rgba(15, 23, 42, 0.9); backdrop-filter: blur(15px); padding: 12px 15px; border-top: 1px solid rgba(255,255,255,0.05); z-index: 100; }
        
        /* Mobile Bottom Nav (Skype Style) */
        .mobile-nav { display: none; position: fixed; bottom: 0; width: 100%; background: #0f172a; border-top: 1px solid rgba(255,255,255,0.1); padding: 10px 0; z-index: 1500; }
        @media (max-width: 1024px) { .mobile-nav { display: flex; justify-content: space-around; } #chat-flow { padding-bottom: 140px; } }

        /* Actions Overlay */
        .msg-actions { opacity: 0; transition: 0.2s; position: absolute; top: -20px; right: 0; background: #334155; border-radius: 6px; padding: 2px 8px; font-size: 10px; display: flex; gap: 8px; z-index: 50; }
        .msg-wrap:hover .msg-actions { opacity: 1; }
        
        #login-screen { background: radial-gradient(circle at center, #1e1b4b 0%, #020617 100%); }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[3000] flex items-center justify-center p-6">
        <div class="w-full max-w-sm text-center">
            <div class="mb-6 inline-block p-4 bg-purple-600 rounded-3xl shadow-2xl shadow-purple-500/20"><i class="fa-solid fa-ghost text-4xl text-white"></i></div>
            <h1 class="text-4xl font-extrabold mb-2 tracking-tighter">NeonPro</h1>
            <p class="text-slate-400 text-sm mb-8 font-medium">Modern Workspace 2026</p>
            <div class="space-y-4">
                <input id="u-input" type="text" placeholder="Enter Nickname" class="w-full bg-slate-800/50 border border-slate-700 p-4 rounded-2xl text-white outline-none focus:border-purple-500 transition-all text-center font-bold">
                <button id="join-btn" class="w-full bg-purple-600 hover:bg-purple-500 py-4 rounded-2xl font-bold shadow-xl">AUTHENTICATE</button>
            </div>
        </div>
    </div>

    <div class="flex h-screen overflow-hidden">
        <aside id="sidebar" class="sidebar flex flex-col">
            <div class="p-6">
                <div class="flex items-center justify-between mb-8">
                    <span class="font-black text-xl text-purple-400">NEON.OS</span>
                    <button onclick="window.logout()" class="text-slate-500"><i class="fa-solid fa-right-from-bracket"></i></button>
                </div>
                <div class="bg-slate-800/50 rounded-xl flex items-center px-3 py-2 border border-slate-700 mb-6">
                    <i class="fa-solid fa-search text-xs text-slate-500 mr-2"></i>
                    <input type="text" id="user-search" oninput="window.filterUsers()" placeholder="Search..." class="bg-transparent text-xs text-white outline-none w-full">
                </div>
            </div>
            <div class="flex-1 overflow-y-auto px-4 space-y-6">
                <div onclick="window.switchChat('global')" class="p-4 bg-purple-600/20 border border-purple-500/30 rounded-2xl cursor-pointer font-bold text-purple-200">
                    <i class="fa-solid fa-globe mr-2"></i> Global HQ
                </div>
                <div>
                    <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest px-2 mb-3">Direct Messages</p>
                    <div id="users-list" class="space-y-1"></div>
                </div>
            </div>
            <div class="p-4 md:hidden"><button onclick="window.toggleSidebar()" class="w-full bg-slate-800 py-3 rounded-xl font-bold">CLOSE MENU</button></div>
        </aside>

        <main class="chat-container">
            <header class="h-16 border-b border-white/5 flex items-center justify-between px-6 bg-slate-900/40 backdrop-blur-md">
                <div class="flex items-center gap-3">
                    <button onclick="window.toggleSidebar()" class="lg:hidden text-white"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div><h2 id="chat-title" class="font-bold text-sm">Welcome</h2><p id="chat-status" class="text-[9px] text-green-500 font-bold uppercase">System Online</p></div>
                </div>
                <div class="flex items-center gap-4 text-slate-400">
                    <i class="fa-solid fa-phone-flip text-sm hover:text-white cursor-pointer"></i>
                    <i class="fa-solid fa-video text-sm hover:text-white cursor-pointer"></i>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-area" id="input-container" style="display: none;">
                <div id="reply-box" class="hidden bg-slate-800 p-2 rounded-lg mb-2 border-l-4 border-purple-500 flex justify-between items-center">
                    <span id="reply-text" class="text-[10px] truncate">Replying...</span>
                    <button onclick="window.cancelReply()" class="text-slate-500"><i class="fa-solid fa-circle-xmark"></i></button>
                </div>
                <form id="msg-form" class="flex items-center gap-3">
                    <input type="file" id="f-input" class="hidden" onchange="window.upFile(this)">
                    <button type="button" onclick="document.getElementById('f-input').click()" class="text-slate-500 hover:text-purple-400"><i class="fa-solid fa-paperclip text-lg"></i></button>
                    <input id="msg-input" type="text" placeholder="Type a message..." class="bg-slate-800/80 text-white w-full outline-none rounded-xl px-4 py-2 text-sm border border-slate-700">
                    <button class="bg-purple-600 text-white w-10 h-10 rounded-full flex items-center justify-center shadow-lg active:scale-90 transition-transform"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <nav class="mobile-nav">
        <button onclick="window.toggleSidebar()" class="flex flex-col items-center gap-1 text-slate-400"><i class="fa-solid fa-message"></i><span class="text-[9px] font-bold">CHATS</span></button>
        <button onclick="window.switchChat('global')" class="flex flex-col items-center gap-1 text-slate-400"><i class="fa-solid fa-hashtag"></i><span class="text-[9px] font-bold">GLOBAL</span></button>
        <button onclick="window.logout()" class="flex flex-col items-center gap-1 text-red-500/70"><i class="fa-solid fa-power-off"></i><span class="text-[9px] font-bold">EXIT</span></button>
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
            set(ref(db, 'online/' + n), true);
            loadUsers();
        }

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');

        window.switchChat = (u) => {
            activeC = u;
            document.getElementById('input-container').style.display = 'block';
            document.getElementById('chat-title').innerText = u === 'global' ? '# global-hq' : `@ ${u}`;
            loadMsgs();
            if(window.innerWidth < 1024) document.getElementById('sidebar').classList.remove('active');
        };

        function loadMsgs() {
            const p = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
            onValue(ref(db, p), (s) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isM = d.sender === myN;
                    const wrap = document.createElement('div');
                    wrap.className = `msg-wrap ${isM ? 'msg-mine' : 'msg-other'}`;
                    
                    let content = d.text || "";
                    if(d.img) content = `<img src="${d.img}" class="rounded-xl max-w-full">`;

                    wrap.innerHTML = `
                        <div class="msg-actions">
                            <button onclick="window.setReply('${d.sender}','${d.text||"Image"}')"><i class="fa-solid fa-reply"></i></button>
                            <button onclick="window.delMsg('${id}')"><i class="fa-solid fa-trash text-red-400"></i></button>
                        </div>
                        <span class="text-[8px] text-slate-500 font-bold px-2 uppercase">${d.sender}</span>
                        ${d.reply ? `<div class="text-[10px] bg-black/20 p-2 rounded-lg mb-1 border-l-2 border-purple-500 italic opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble">${content}</div>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setReply = (u, m) => {
            replyTo = { to: u, msg: m };
            document.getElementById('reply-box').classList.remove('hidden');
            document.getElementById('reply-text').innerText = `@${u}: ${m}`;
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
            const r = sRef(st, `media/${Date.now()}`);
            await uploadBytes(r, f);
            const url = await getDownloadURL(r);
            const p = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
            push(ref(db, p), { sender: myN, img: url, time: Date.now() });
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('users-list');
                let count = 0; list.innerHTML = "";
                s.forEach(c => {
                    count++;
                    if(c.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "user-item flex items-center gap-3 p-3 bg-slate-800/30 rounded-xl cursor-pointer hover:bg-purple-600/10 transition";
                        d.innerHTML = `<div class="w-8 h-8 rounded-lg bg-slate-700 flex items-center justify-center text-[10px] font-bold">${c.key[0].toUpperCase()}</div>
                                       <div><p class="text-xs font-bold text-white">${c.key}</p><p class="text-[9px] text-green-500">Active</p></div>`;
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
            if(confirm("Logout?")) {
                remove(ref(db, 'online/' + myN));
                localStorage.clear();
                location.reload();
            }
        };
    </script>
</body>
</html>
