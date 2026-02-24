<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro | Infinite Elite Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        :root { --p: #a855f7; --bg: #020617; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; outline: none; }
        body, html { height: 100%; margin: 0; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; overflow: hidden; }
        
        /* Layout Structure */
        .app-grid { display: grid; grid-template-columns: 320px 1fr; height: 100dvh; }
        @media (max-width: 1024px) { .app-grid { grid-template-columns: 1fr; } }

        /* Sidebar & Navigation */
        .sidebar { background: #070b14; border-right: 1px solid rgba(255,255,255,0.05); transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); z-index: 2000; }
        @media (max-width: 1024px) { .sidebar { position: fixed; inset: 0; transform: translateX(-100%); width: 100%; } .sidebar.active { transform: translateX(0); } }

        /* Chat Layout */
        .chat-container { display: flex; flex-direction: column; height: 100dvh; position: relative; }
        #chat-flow { flex: 1; overflow-y: auto; padding: 25px; display: flex; flex-direction: column; gap: 18px; scroll-behavior: smooth; }
        
        /* Message Bubbles - Modern Look */
        .msg-wrap { position: relative; max-width: 80%; display: flex; flex-direction: column; margin-bottom: 4px; }
        .mine { align-self: flex-end; align-items: flex-end; }
        .other { align-self: flex-start; align-items: flex-start; }
        
        .bubble { padding: 14px 20px; border-radius: 24px; font-size: 14px; position: relative; border: 1px solid rgba(255,255,255,0.05); transition: 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        .mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); border-bottom-right-radius: 4px; box-shadow: 0 8px 25px rgba(168, 85, 247, 0.2); }
        .other .bubble { background: #1e293b; border-bottom-left-radius: 4px; }
        
        /* Action Menus (Hover/Tap) */
        .actions { position: absolute; top: -40px; background: #1e293b; border-radius: 12px; padding: 6px 14px; display: none; gap: 12px; border: 1px solid rgba(255,255,255,0.15); z-index: 50; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        .msg-wrap:hover .actions { display: flex; }
        @media (max-width: 1024px) { .msg-wrap:active .actions { display: flex; } }

        /* Bottom Fixed Input Section */
        .footer-system { background: #070b14; border-top: 1px solid rgba(255,255,255,0.08); padding: 15px 20px 25px 20px; z-index: 100; }
        .input-bar { background: rgba(255,255,255,0.04); border-radius: 22px; border: 1px solid rgba(255,255,255,0.1); padding: 4px 14px; }
        
        .glass { background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(25px); }
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: #a855f7; border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth-layer" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#020617] p-6">
        <div class="glass p-12 rounded-[3.5rem] w-full max-w-sm text-center border border-white/10 shadow-2xl">
            <h1 class="text-3xl font-black mb-10 tracking-tighter text-purple-500 italic">NEON INFINITE</h1>
            <input id="u-in" type="text" placeholder="Enter Nickname" class="w-full bg-white/5 border border-white/10 p-5 rounded-2xl text-white text-center mb-5 font-bold focus:border-purple-500 transition">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-2xl font-black shadow-lg shadow-purple-600/30 active:scale-95 transition-all">START SYNC</button>
        </div>
    </div>

    <div class="app-grid">
        <aside id="sidebar-nav" class="sidebar flex flex-col p-6">
            <div class="flex justify-between items-center mb-10">
                <span class="text-2xl font-black text-purple-400 italic">NEON.OS</span>
                <button onclick="logout()" class="w-10 h-10 rounded-xl bg-red-500/10 text-red-500 flex items-center justify-center border border-red-500/20 hover:bg-red-500/20 transition"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto">
                <div class="space-y-4">
                    <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest px-2">Channels</p>
                    <div onclick="switchChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-2xl cursor-pointer font-bold flex items-center gap-3 hover:bg-purple-600/20 transition">
                        <i class="fa-solid fa-hashtag text-purple-400"></i> global-hq
                    </div>
                </div>
                
                <div class="mt-8 space-y-4">
                    <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest px-2">Direct Messages</p>
                    <div id="u-list" class="space-y-2"></div>
                </div>
            </div>

            <div class="pt-6 border-t border-white/5 flex items-center gap-4">
                <div id="m-avatar" class="w-12 h-12 rounded-2xl bg-purple-600 flex items-center justify-center font-black text-xl shadow-lg">?</div>
                <div><p id="m-name" class="font-bold text-sm">Guest</p><p class="text-[9px] text-green-500 font-bold uppercase tracking-tighter animate-pulse">● System Online</p></div>
            </div>
        </aside>

        <main class="chat-container">
            <header class="h-20 flex items-center justify-between px-6 glass border-b border-white/5 z-50">
                <div class="flex items-center gap-4">
                    <button class="lg:hidden w-10 h-10 bg-white/5 rounded-xl text-purple-400" onclick="toggleNav()"><i class="fa-solid fa-chevron-left"></i></button>
                    <div>
                        <h2 id="chat-h" class="text-lg font-black tracking-tight">Select Workspace</h2>
                        <p id="chat-sub" class="text-[9px] text-slate-500 font-bold uppercase">Encrypted Connection</p>
                    </div>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div id="footer" class="footer-system hidden">
                <div id="reply-tag" class="hidden mb-3 p-3 bg-purple-600/10 rounded-xl border-l-4 border-purple-500 flex justify-between items-center text-[10px] text-purple-200">
                    <span id="reply-msg"></span>
                    <button onclick="clearReply()"><i class="fa-solid fa-circle-xmark"></i></button>
                </div>
                <form id="m-form" class="input-bar flex items-center gap-3">
                    <input type="file" id="f-img" class="hidden" accept="image/*" onchange="uploadMedia(this)">
                    <button type="button" onclick="document.getElementById('f-img').click()" class="text-slate-400 hover:text-purple-400 transition"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="m-in" type="text" placeholder="Type a message..." class="bg-transparent flex-1 outline-none text-sm p-3 font-medium text-white" autocomplete="off">
                    <button class="bg-purple-600 w-12 h-12 rounded-2xl shadow-xl flex items-center justify-center active:scale-90 transition-transform"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove, update } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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

        let user = localStorage.getItem('neonSyncUser'), active = null, reply = null;

        if(user) login(user);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            user = val; localStorage.setItem('neonSyncUser', val);
            document.getElementById('auth-layer').style.display = 'none';
            document.getElementById('m-name').innerText = user;
            document.getElementById('m-avatar').innerText = user[0].toUpperCase();
            set(ref(db, 'online/' + user), true);
            loadUsers();
        }

        window.toggleNav = () => document.getElementById('sidebar-nav').classList.toggle('active');

        window.switchChat = (t) => {
            active = t;
            document.getElementById('footer').classList.remove('hidden');
            document.getElementById('chat-h').innerText = t === 'global' ? '# global-hq' : `@ ${t}`;
            if(window.innerWidth < 1024) toggleNav();
            loadMessages();
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('u-list'); list.innerHTML = "";
                s.forEach(u => {
                    if(u.key !== user) {
                        const d = document.createElement('div');
                        d.className = "p-4 glass rounded-2xl cursor-pointer hover:bg-purple-600/10 transition flex items-center gap-3 mb-2 border border-white/5";
                        d.innerHTML = `<div class="w-2.5 h-2.5 rounded-full bg-green-500 shadow-[0_0_10px_#22c55e]"></div> <span class="font-bold text-sm uppercase">${u.key}</span>`;
                        d.onclick = () => switchChat(u.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        function loadMessages() {
            const p = active === 'global' ? 'msgs/global' : `private/${[user, active].sort().join('_')}`;
            onValue(ref(db, p), (s) => {
                const flow = document.getElementById('chat-flow'); flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isM = d.sender === user;
                    const wrap = document.createElement('div');
                    wrap.className = `msg-wrap ${isM ? 'mine' : 'other'}`;
                    
                    let rx = "";
                    if(d.reactions) Object.entries(d.reactions).forEach(([e, c]) => {
                        rx += `<span onclick="react('${id}','${e}')" class="bg-white/5 px-2 py-1 rounded-lg text-[10px] cursor-pointer border border-white/10 hover:border-purple-500/50">${e} ${c}</span>`;
                    });

                    wrap.innerHTML = `
                        <div class="actions">
                            <button onclick="setReply('${d.sender}','${d.text||"Media"}')" class="text-[10px] font-black hover:text-purple-400">REPLY</button>
                            <button onclick="react('${id}','👍')" class="text-xs">👍</button>
                            <button onclick="react('${id}','❤️')" class="text-xs">❤️</button>
                            ${isM ? `<button onclick="deleteMsg('${id}')" class="text-[10px] text-red-500 font-black ml-2">DEL</button>` : ''}
                        </div>
                        <span class="text-[9px] text-slate-500 font-black mb-1 px-3 uppercase tracking-tighter">${d.sender}</span>
                        ${d.reply ? `<div class="text-[9px] bg-white/5 p-2 rounded-xl mb-1 border-l-2 border-purple-500 italic opacity-70">Reply to ${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble">${d.img ? `<img src="${d.img}" class="rounded-xl max-w-full shadow-lg border border-white/5">` : `<span>${d.text}</span>`}</div>
                        <div class="flex gap-1 mt-2">${rx}</div>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.uploadMedia = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `media/${Date.now()}_${f.name}`);
            const snap = await uploadBytes(r, f);
            const url = await getDownloadURL(snap.ref);
            const p = active === 'global' ? 'msgs/global' : `private/${[user, active].sort().join('_')}`;
            push(ref(db, p), { sender: user, img: url, time: Date.now() });
        };

        window.setReply = (u, m) => {
            reply = { to: u, msg: m };
            document.getElementById('reply-tag').classList.remove('hidden');
            document.getElementById('reply-msg').innerText = `Syncing Reply to @${u}: ${m}`;
            document.getElementById('m-in').focus();
        };

        window.clearReply = () => { reply = null; document.getElementById('reply-tag').classList.add('hidden'); };

        window.react = (mid, e) => {
            const p = (active === 'global' ? 'msgs/global/' : `private/${[user, active].sort().join('_')}/`) + mid + '/reactions/' + e;
            onValue(ref(db, p), (s) => set(ref(db, p), (s.val() || 0) + 1), { onlyOnce: true });
        };

        window.deleteMsg = (id) => {
            if(confirm("Erase from database?")) {
                const p = (active === 'global' ? 'msgs/global/' : `private/${[user, active].sort().join('_')}/`) + id;
                remove(ref(db, p));
            }
        };

        document.getElementById('m-form').onsubmit = (e) => {
            e.preventDefault();
            const i = document.getElementById('m-in');
            if(!i.value.trim() || !active) return;
            const p = active === 'global' ? 'msgs/global' : `private/${[user, active].sort().join('_')}`;
            const m = { sender: user, text: i.value.trim(), time: Date.now() };
            if(reply) { m.reply = reply; clearReply(); }
            push(ref(db, p), m); i.value = "";
        };

        window.logout = () => { if(confirm("Terminate Session?")) { remove(ref(db, 'online/' + user)); localStorage.clear(); location.reload(); }};
        window.login = login;
    </script>
</body>
</html>
