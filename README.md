<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>NeonPro | Premium Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        
        :root { --p: #bc13fe; --bg: #0f1115; --card: #161920; --text: #f8fafc; }
        .light-mode { --bg: #f3f4f6; --card: #ffffff; --text: #1e293b; }

        * { transition: background 0.3s, color 0.3s; font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: var(--bg); color: var(--text); height: 100dvh; overflow: hidden; margin: 0; }

        /* Modern Sidebar */
        .sidebar { 
            width: 280px; flex-shrink: 0; background: var(--card); border-right: 1px solid rgba(0,0,0,0.05);
            display: flex; flex-direction: column; transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1);
        }

        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; z-index: 2000; }
            .sidebar.active { left: 0; box-shadow: 15px 0 40px rgba(0,0,0,0.2); }
        }

        /* Premium Message Cards */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; }
        .msg-card { 
            background: var(--card); border-radius: 20px; padding: 15px; 
            box-shadow: 0 4px 15px rgba(0,0,0,0.03); max-width: 85%;
            position: relative; border: 1px solid rgba(188, 19, 254, 0.1);
            animation: slideUp 0.3s ease;
        }
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .mine { align-self: flex-end; border-right: 4px solid var(--p); }
        .other { align-self: flex-start; border-left: 4px solid #00d2ff; }

        /* Floating Action Menu */
        .action-bar { 
            display: none; position: absolute; top: -20px; right: 10px; 
            background: var(--p); border-radius: 30px; padding: 4px 12px; gap: 10px; z-index: 50;
        }
        .msg-card:hover .action-bar, .msg-card:active .action-bar { display: flex; }

        /* Clean Input */
        .footer { padding: 15px 20px 30px 20px; background: var(--bg); }
        .input-box { background: var(--card); border-radius: 18px; padding: 6px 15px; display: flex; align-items: center; gap: 12px; border: 1px solid rgba(0,0,0,0.05); }

        .overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.4); z-index: 1500; backdrop-filter: blur(4px); }
        .overlay.active { display: block; }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: var(--p); border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#0f1115] p-6 text-white">
        <div class="w-full max-w-sm text-center">
            <div class="text-5xl mb-6">✨</div>
            <h1 class="text-3xl font-black mb-10 tracking-tighter">NEON<span class="text-purple-500">PRO</span></h1>
            <input id="u-in" type="text" placeholder="Your Name" class="w-full bg-white/5 border border-white/10 p-5 rounded-3xl text-center mb-5 outline-none focus:border-purple-500 font-bold">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-3xl font-black shadow-xl active:scale-95 transition-all">ENTER WORKSPACE</button>
        </div>
    </div>

    <div id="overlay" class="overlay" onclick="toggleSide()"></div>

    <div class="flex h-screen w-screen">
        <aside id="side" class="sidebar">
            <div class="p-8 flex items-center justify-between">
                <span class="font-black text-xl italic tracking-tighter">N<span class="text-purple-500">P</span>.</span>
                <button onclick="toggleTheme()" class="w-10 h-10 rounded-full bg-black/5 flex items-center justify-center"><i id="t-icon" class="fa-solid fa-moon"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto px-6">
                <div onclick="openChat('global')" class="p-4 bg-purple-600 text-white rounded-2xl cursor-pointer font-bold flex items-center gap-3 shadow-lg shadow-purple-500/20">
                    <i class="fa-solid fa-layer-group"></i> Global Hub
                </div>
                
                <p class="text-[11px] text-slate-400 font-bold uppercase tracking-widest mt-10 mb-4">Direct Messages</p>
                <div id="u-list" class="space-y-2"></div>
            </div>

            <div class="p-6 border-t border-black/5">
                <div class="flex items-center gap-4">
                    <div id="my-av" class="w-12 h-12 rounded-2xl bg-purple-600 flex items-center justify-center font-black text-white">?</div>
                    <div class="overflow-hidden">
                        <p id="my-n" class="font-bold text-sm truncate">User</p>
                        <p class="text-[9px] text-green-500 font-black">ONLINE NOW</p>
                    </div>
                    <button onclick="logout()" class="ml-auto text-red-400"><i class="fa-solid fa-power-off"></i></button>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col min-w-0 bg-[var(--bg)]">
            <header class="h-20 flex items-center px-8 justify-between border-b border-black/5 bg-[var(--card)]/50 backdrop-blur-md">
                <div class="flex items-center gap-4">
                    <button class="lg:hidden text-2xl" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                    <h2 id="chat-h" class="font-black text-lg">Dashboard</h2>
                </div>
                <div class="flex gap-4">
                    <div class="bg-black/5 px-4 py-2 rounded-full text-[10px] font-bold">NODES: <span id="stat-count" class="text-purple-500">0</span></div>
                </div>
            </header>

            <div id="chat-flow">
                <div class="m-auto text-center opacity-20">
                    <i class="fa-solid fa-wand-magic-sparkles text-6xl mb-4"></i>
                    <p class="font-bold text-xs uppercase">Premium Signal Ready</p>
                </div>
            </div>

            <div id="input-area" class="footer hidden">
                <div id="reply-tag" class="hidden mb-3 p-3 bg-purple-500/10 rounded-2xl border-l-4 border-purple-500 flex justify-between items-center text-xs animate-pulse">
                    <span id="reply-txt" class="truncate font-semibold italic"></span>
                    <button onclick="cancelReply()"><i class="fa-solid fa-circle-xmark"></i></button>
                </div>
                <form id="m-form" class="input-box">
                    <input type="file" id="f-in" class="hidden" accept="image/*" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('f-in').click()" class="text-slate-400 hover:text-purple-500"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="m-in" type="text" placeholder="Write something amazing..." autocomplete="off" class="bg-transparent outline-none flex-1 text-sm py-3">
                    <button class="bg-purple-600 text-white w-12 h-10 rounded-2xl flex items-center justify-center shadow-lg active:scale-90 transition-all"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

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

        let user = localStorage.getItem('np_user'), active = null, reply = null;
        if(user) login(user);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            user = val; localStorage.setItem('np_user', val);
            document.getElementById('auth').style.display = 'none';
            document.getElementById('my-n').innerText = user;
            document.getElementById('my-av').innerText = user[0].toUpperCase();
            set(ref(db, 'online/' + user), true);
            loadUsers();
        }

        window.toggleTheme = () => {
            const b = document.body;
            b.classList.toggle('light-mode');
            const isL = b.classList.contains('light-mode');
            document.getElementById('t-icon').className = isL ? "fa-solid fa-sun" : "fa-solid fa-moon";
        };

        window.toggleSide = () => {
            document.getElementById('side').classList.toggle('active');
            document.getElementById('overlay').classList.toggle('active');
        };

        window.openChat = (t) => {
            active = t;
            document.getElementById('input-area').classList.remove('hidden');
            document.getElementById('chat-h').innerText = t === 'global' ? 'Global Hub' : `@${t}`;
            if(window.innerWidth < 1024) toggleSide();
            loadMsgs();
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('u-list'); list.innerHTML = "";
                let count = 0;
                s.forEach(u => {
                    count++;
                    if(u.key !== user) {
                        const d = document.createElement('div');
                        d.className = "p-4 bg-[var(--card)] rounded-2xl cursor-pointer hover:scale-[1.02] transition flex items-center gap-3 border border-black/5";
                        d.innerHTML = `<div class="w-3 h-3 rounded-full bg-green-500 shadow-lg shadow-green-500/30"></div> <span class="text-xs font-bold uppercase">${u.key}</span>`;
                        d.onclick = () => openChat(u.key);
                        list.appendChild(d);
                    }
                });
                document.getElementById('stat-count').innerText = count;
            });
        }

        function loadMsgs() {
            const p = active === 'global' ? 'msgs/global' : `private/${[user, active].sort().join('_')}`;
            onValue(ref(db, p), (s) => {
                const flow = document.getElementById('chat-flow'); flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isM = d.sender === user;
                    const wrap = document.createElement('div');
                    wrap.className = `msg-card ${isM ? 'mine' : 'other'}`;
                    wrap.innerHTML = `
                        <div class="action-bar text-white flex items-center">
                            <button onclick="setRep('${d.sender}','${d.text||"Media"}')" class="text-[9px] font-bold px-2">REPLY</button>
                            <button onclick="react('${id}','❤️')" class="text-xs">❤️</button>
                            <button onclick="react('${id}','👍')" class="text-xs">👍</button>
                            ${isM ? `<button onclick="del('${id}')" class="text-[9px] font-bold px-2 text-white/70">DEL</button>` : ''}
                        </div>
                        <p class="text-[8px] font-black opacity-30 uppercase tracking-tighter mb-2">${d.sender}</p>
                        ${d.reply ? `<div class="text-[9px] bg-black/5 p-3 rounded-xl mb-3 border-l-2 border-purple-500 italic opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        ${d.img ? `<img src="${d.img}" class="rounded-xl max-w-full shadow-lg">` : `<span class="text-sm font-medium leading-relaxed">${d.text}</span>`}
                        <div id="rx-${id}" class="flex gap-1 mt-3"></div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="bg-black/5 px-2 py-1 rounded-lg text-[9px] font-bold border border-black/5">${e} ${c}</span>`;
                        });
                    }
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setRep = (u, m) => {
            reply = { to: u, msg: m };
            document.getElementById('reply-tag').classList.remove('hidden');
            document.getElementById('reply-txt').innerText = `Reply to @${u}: ${m}`;
            document.getElementById('m-in').focus();
        };

        window.cancelReply = () => { reply = null; document.getElementById('reply-tag').classList.add('hidden'); };

        window.upImg = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `media/${Date.now()}`);
            const snap = await uploadBytes(r, f);
            const url = await getDownloadURL(snap.ref);
            const p = active === 'global' ? 'msgs/global' : `private/${[user, active].sort().join('_')}`;
            push(ref(db, p), { sender: user, img: url });
        };

        window.react = (mid, e) => {
            const p = (active === 'global' ? 'msgs/global/' : `private/${[user, active].sort().join('_')}/`) + mid + '/reactions/' + e;
            onValue(ref(db, p), (s) => set(ref(db, p), (s.val() || 0) + 1), { onlyOnce: true });
        };

        window.del = (id) => {
            if(confirm("Permanently erase?")) {
                const p = (active === 'global' ? 'msgs/global/' : `private/${[user, active].sort().join('_')}/`) + id;
                remove(ref(db, p));
            }
        };

        document.getElementById('m-form').onsubmit = (e) => {
            e.preventDefault();
            const i = document.getElementById('m-in');
            if(!i.value.trim() || !active) return;
            const p = active === 'global' ? 'msgs/global' : `private/${[user, active].sort().join('_')}`;
            const m = { sender: user, text: i.value.trim() };
            if(reply) { m.reply = reply; cancelReply(); }
            push(ref(db, p), m); i.value = "";
        };

        window.logout = () => { remove(ref(db, 'online/' + user)); localStorage.clear(); location.reload(); };
        window.login = login;
