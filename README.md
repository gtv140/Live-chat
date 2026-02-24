<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Aether UI | Gemini Special Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        
        :root { --accent: #8b5cf6; --accent-glow: rgba(139, 92, 246, 0.3); --bg: #09090b; --card: #18181b; --text: #fafafa; }
        .light-theme { --bg: #f8fafc; --card: #ffffff; --text: #0f172a; --accent: #6366f1; }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: var(--bg); color: var(--text); height: 100dvh; overflow: hidden; }

        /* Layout Architecture */
        .app-wrapper { display: flex; height: 100dvh; width: 100vw; }
        
        .sidebar { 
            width: 300px; flex-shrink: 0; background: var(--card); border-right: 1px solid rgba(255,255,255,0.05);
            display: flex; flex-direction: column; transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
        }

        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; z-index: 2000; border-right: none; }
            .sidebar.active { left: 0; box-shadow: 20px 0 50px rgba(0,0,0,0.5); }
        }

        /* Message Esthetics */
        #chat-flow { flex: 1; overflow-y: auto; padding: 24px; display: flex; flex-direction: column; gap: 20px; scroll-behavior: smooth; }
        .msg-group { position: relative; max-width: 85%; display: flex; flex-direction: column; gap: 4px; }
        
        .card-ui { 
            background: var(--card); border: 1px solid rgba(255,255,255,0.08); padding: 14px 18px; 
            border-radius: 20px; box-shadow: 0 4px 20px rgba(0,0,0,0.1); position: relative;
        }
        
        .mine { align-self: flex-end; }
        .mine .card-ui { background: var(--accent); color: white; border: none; border-bottom-right-radius: 4px; }
        
        .other { align-self: flex-start; }
        .other .card-ui { border-bottom-left-radius: 4px; }

        /* Action Pill */
        .action-pill { 
            position: absolute; top: -18px; right: 0; display: none; background: var(--card); 
            border: 1px solid var(--accent); border-radius: 30px; padding: 4px 10px; gap: 12px; z-index: 100;
        }
        .msg-group:hover .action-pill, .msg-group:active .action-pill { display: flex; }

        /* Modern Input */
        .input-bar { padding: 20px; background: linear-gradient(to top, var(--bg) 80%, transparent); }
        .pill { background: var(--card); border: 1px solid rgba(255,255,255,0.1); border-radius: 24px; padding: 8px 12px; display: flex; align-items: center; gap: 12px; }
        .pill:focus-within { border-color: var(--accent); box-shadow: 0 0 15px var(--accent-glow); }
        .pill input { background: transparent; border: none; outline: none; color: inherit; flex: 1; font-size: 15px; }

        /* Global Classes */
        .overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.6); backdrop-filter: blur(4px); z-index: 1500; }
        .overlay.active { display: block; }
        .btn-circle { width: 44px; height: 44px; border-radius: 14px; display: flex; align-items: center; justify-content: center; transition: 0.2s; }
        .btn-circle:active { scale: 0.9; }
    </style>
</head>
<body>

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#09090b] p-6">
        <div class="w-full max-w-sm text-center">
            <div class="w-20 h-20 bg-indigo-600 rounded-[2rem] flex items-center justify-center mx-auto mb-8 shadow-2xl rotate-12"><i class="fa-solid fa-feather-pointed text-4xl text-white"></i></div>
            <h1 class="text-3xl font-extrabold mb-2 tracking-tighter">AETHER<span class="text-indigo-500">UI</span></h1>
            <p class="text-slate-500 text-sm mb-10 font-medium">Next-gen communication portal</p>
            <input id="u-in" type="text" placeholder="Identity Label" class="w-full bg-[#18181b] border border-white/5 p-5 rounded-2xl text-white text-center mb-4 outline-none focus:border-indigo-500 transition-all font-semibold">
            <button onclick="login()" class="w-full bg-indigo-600 text-white py-5 rounded-2xl font-bold shadow-2xl shadow-indigo-500/20 active:scale-95 transition-all">INITIALIZE</button>
        </div>
    </div>

    <div id="overlay" class="overlay" onclick="toggleSide()"></div>

    <div class="app-wrapper">
        <aside id="side" class="sidebar">
            <div class="p-8 flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div class="w-8 h-8 bg-indigo-500 rounded-lg"></div>
                    <span class="font-black text-xl tracking-tighter">AETHER</span>
                </div>
                <button onclick="toggleTheme()" class="text-slate-500 hover:text-indigo-400"><i id="t-icon" class="fa-solid fa-moon"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto px-4 space-y-6">
                <div>
                    <label class="px-4 text-[10px] font-black text-slate-500 uppercase tracking-[0.2em]">Workspaces</label>
                    <div onclick="openChat('global')" class="mt-2 p-4 bg-indigo-500/10 text-indigo-400 rounded-2xl cursor-pointer font-bold flex items-center gap-3 border border-indigo-500/20">
                        <i class="fa-solid fa-hashtag"></i> global-lounge
                    </div>
                </div>
                
                <div>
                    <label class="px-4 text-[10px] font-black text-slate-500 uppercase tracking-[0.2em]">Live Nodes</p>
                    <div id="u-list" class="mt-2 space-y-1"></div>
                </div>
            </div>

            <div class="p-6 bg-black/10 flex items-center gap-4">
                <div id="my-av" class="w-10 h-10 rounded-xl bg-indigo-600 flex items-center justify-center font-bold text-white">?</div>
                <div class="flex-1 overflow-hidden">
                    <p id="my-n" class="text-sm font-bold truncate">User</p>
                    <p class="text-[9px] text-indigo-400 font-bold tracking-widest uppercase">Connected</p>
                </div>
                <button onclick="logout()" class="text-slate-500 hover:text-red-500"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </aside>

        <main class="flex-1 flex flex-col min-w-0">
            <header class="h-20 flex items-center px-6 justify-between backdrop-blur-md sticky top-0 z-40">
                <div class="flex items-center gap-4">
                    <button class="lg:hidden btn-circle bg-white/5" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 id="chat-h" class="font-extrabold text-sm uppercase tracking-tight">Main Terminal</h2>
                        <span class="text-[9px] font-bold text-green-500 uppercase">● System Nominal</span>
                    </div>
                </div>
                <div class="flex items-center gap-3">
                    <div class="bg-indigo-500/10 px-4 py-2 rounded-xl text-[10px] font-black text-indigo-400">ACTIVE: <span id="stat-count">0</span></div>
                </div>
            </header>

            <div id="chat-flow">
                <div class="m-auto text-center opacity-10">
                    <i class="fa-solid fa-terminal text-6xl mb-4"></i>
                    <p class="text-xs font-black uppercase tracking-[0.3em]">Awaiting Data</p>
                </div>
            </div>

            <div id="input-area" class="input-bar hidden">
                <div id="reply-tag" class="hidden mb-3 p-4 bg-indigo-500/5 rounded-2xl border-l-4 border-indigo-500 flex justify-between items-center text-xs">
                    <span id="reply-txt" class="truncate opacity-60 font-medium italic"></span>
                    <button onclick="cancelReply()"><i class="fa-solid fa-xmark"></i></button>
                </div>
                <form id="m-form" class="pill">
                    <input type="file" id="f-in" class="hidden" accept="image/*" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('f-in').click()" class="text-slate-500 hover:text-indigo-400 transition"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="m-in" type="text" placeholder="Type a secure message..." autocomplete="off">
                    <button class="bg-indigo-600 text-white w-11 h-11 rounded-xl flex items-center justify-center shadow-lg active:scale-90 transition-all"><i class="fa-solid fa-paper-plane text-xs"></i></button>
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

        let user = localStorage.getItem('ae_user'), active = null, reply = null;
        if(user) login(user);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            user = val; localStorage.setItem('ae_user', val);
            document.getElementById('auth').style.display = 'none';
            document.getElementById('my-n').innerText = user;
            document.getElementById('my-av').innerText = user[0].toUpperCase();
            set(ref(db, 'online/' + user), true);
            loadUsers();
        }

        window.toggleTheme = () => {
            document.body.classList.toggle('light-theme');
            const isL = document.body.classList.contains('light-theme');
            document.getElementById('t-icon').className = isL ? "fa-solid fa-sun" : "fa-solid fa-moon";
        };

        window.toggleSide = () => {
            document.getElementById('side').classList.toggle('active');
            document.getElementById('overlay').classList.toggle('active');
        };

        window.openChat = (t) => {
            active = t;
            document.getElementById('input-area').classList.remove('hidden');
            document.getElementById('chat-h').innerText = t === 'global' ? 'Global Lounge' : `@${t}`;
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
                        d.className = "p-4 rounded-2xl cursor-pointer hover:bg-indigo-500/5 transition flex items-center gap-3";
                        d.innerHTML = `<div class="w-2 h-2 rounded-full bg-indigo-500 shadow-lg shadow-indigo-500/50"></div> <span class="text-xs font-bold uppercase tracking-tight">${u.key}</span>`;
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
                    wrap.className = `msg-group ${isM ? 'mine' : 'other'}`;
                    wrap.innerHTML = `
                        <div class="action-pill shadow-xl text-white">
                            <button onclick="setRep('${d.sender}','${d.text||"Image"}')" class="text-[9px] font-black px-1">REPLY</button>
                            <button onclick="react('${id}','❤️')" class="text-xs">❤️</button>
                            ${isM ? `<button onclick="del('${id}')" class="text-[9px] font-black px-1">DEL</button>` : ''}
                        </div>
                        <p class="text-[9px] font-bold opacity-30 uppercase ml-2">${d.sender}</p>
                        <div class="card-ui">
                            ${d.reply ? `<div class="text-[10px] bg-black/10 p-3 rounded-xl mb-3 border-l-2 border-indigo-400 italic opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                            ${d.img ? `<img src="${d.img}" class="rounded-xl max-w-full">` : `<span class="text-sm font-medium">${d.text}</span>`}
                            <div id="rx-${id}" class="flex gap-1 mt-2"></div>
                        </div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="bg-indigo-500/10 px-2 py-0.5 rounded-lg text-[8px] font-bold border border-indigo-500/10">${e} ${c}</span>`;
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
            push(ref(db, p), { sender: user, img: url, time: Date.now() });
        };

        window.react = (mid, e) => {
            const p = (active === 'global' ? 'msgs/global/' : `private/${[user, active].sort().join('_')}/`) + mid + '/reactions/' + e;
            onValue(ref(db, p), (s) => set(ref(db, p), (s.val() || 0) + 1), { onlyOnce: true });
        };

        window.del = (id) => {
            if(confirm("Delete signal?")) {
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
            if(reply) { m.reply = reply; cancelReply(); }
            push(ref(db, p), m); i.value = "";
        };

        window.logout = () => { remove(ref(db, 'online/' + user)); localStorage.clear(); location.reload(); };
        window.login = login;
    </script>
</body>
</html>
