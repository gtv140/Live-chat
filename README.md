<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Aether Neon Pro | Gemini Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        
        :root { --p: #a855f7; --bg: #030712; --card: #111827; --text: #f9fafb; }
        .light { --bg: #f8fafc; --card: #ffffff; --text: #0f172a; }

        * { font-family: 'Plus Jakarta Sans', sans-serif; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); -webkit-tap-highlight-color: transparent; }
        body { background: var(--bg); color: var(--text); height: 100dvh; overflow: hidden; margin: 0; }

        /* Premium Glassmorphism */
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(12px); border: 1px solid rgba(255, 255, 256, 0.08); }
        .sidebar { width: 300px; background: var(--card); border-right: 1px solid rgba(0,0,0,0.1); }
        
        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; z-index: 2000; height: 100%; width: 85%; }
            .sidebar.active { left: 0; box-shadow: 20px 0 50px rgba(0,0,0,0.4); }
        }

        /* Message Logic */
        #chat-flow { flex: 1; overflow-y: auto; padding: 25px; display: flex; flex-direction: column; gap: 20px; }
        .msg-unit { max-width: 85%; position: relative; }
        .card-ui { padding: 15px 20px; border-radius: 24px; font-size: 15px; position: relative; box-shadow: 0 4px 15px rgba(0,0,0,0.05); }
        
        .mine { align-self: flex-end; }
        .mine .card-ui { background: var(--p); color: white; border-bottom-right-radius: 4px; }
        
        .other { align-self: flex-start; }
        .other .card-ui { background: var(--card); border: 1px solid rgba(255,255,255,0.05); border-bottom-left-radius: 4px; }

        /* Floating Skype Menu */
        .msg-menu { 
            display: none; position: absolute; top: -40px; right: 0; background: var(--card); 
            border-radius: 30px; padding: 5px 15px; gap: 12px; border: 1px solid var(--p); z-index: 100;
        }
        .msg-unit:hover .msg-menu, .msg-unit:active .msg-menu { display: flex; animation: slideIn 0.2s ease; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* Modern Input */
        .input-area { padding: 20px; background: var(--bg); border-top: 1px solid rgba(255,255,255,0.05); }
        .pill { background: var(--card); border-radius: 30px; padding: 8px 15px; display: flex; align-items: center; gap: 12px; border: 1px solid rgba(255,255,255,0.1); }
        .pill:focus-within { border-color: var(--p); box-shadow: 0 0 15px rgba(168, 85, 247, 0.2); }
        .pill input { background: transparent; border: none; outline: none; color: inherit; flex: 1; font-size: 15px; }

        .overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.5); backdrop-filter: blur(4px); z-index: 1500; }
        .overlay.active { display: block; }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: var(--p); border-radius: 10px; }
    </style>
</head>
<body class="flex">

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#030712] p-8">
        <div class="max-w-sm w-full text-center">
            <div class="w-20 h-20 bg-purple-600 rounded-[2.5rem] flex items-center justify-center mx-auto mb-8 shadow-2xl rotate-12 animate-pulse"><i class="fa-solid fa-bolt text-4xl text-white"></i></div>
            <h1 class="text-4xl font-black mb-2 tracking-tighter italic">AETHER<span class="text-purple-500">PRO</span></h1>
            <p class="text-slate-500 text-sm mb-12 font-medium">Experience the Future of Chat</p>
            <input id="u-in" type="text" placeholder="Access Identity" class="w-full bg-gray-900 border border-gray-800 p-5 rounded-3xl text-center mb-5 outline-none focus:border-purple-500 font-bold text-white">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-3xl font-black shadow-xl active:scale-95 text-white transition-all">ESTABLISH CONNECTION</button>
        </div>
    </div>

    <aside id="side" class="sidebar flex flex-col">
        <div class="p-8 flex items-center justify-between">
            <span class="text-2xl font-black italic tracking-tighter">AE<span class="text-purple-500">.</span></span>
            <button onclick="toggleTheme()" class="w-10 h-10 rounded-2xl glass flex items-center justify-center"><i id="t-icon" class="fa-solid fa-moon text-purple-400"></i></button>
        </div>
        
        <div class="flex-1 overflow-y-auto px-6 space-y-8">
            <div>
                <p class="text-[10px] font-black text-slate-500 uppercase tracking-widest mb-4">Workspaces</p>
                <div onclick="openChat('global')" class="p-4 bg-purple-600/10 border-l-4 border-purple-500 rounded-2xl cursor-pointer font-bold flex items-center gap-4 text-purple-400">
                    <i class="fa-solid fa-layer-group"></i> global-terminal
                </div>
            </div>
            
            <div>
                <p class="text-[10px] font-black text-slate-500 uppercase tracking-widest mb-4">Direct Nodes</p>
                <div id="u-list" class="space-y-3"></div>
            </div>
        </div>

        <div class="p-6 mt-auto border-t border-white/5">
            <div class="flex items-center gap-4">
                <div id="my-av" class="w-12 h-12 rounded-2xl bg-purple-600 flex items-center justify-center font-black text-white shadow-lg">?</div>
                <div class="flex-1 truncate"><p id="my-n" class="font-black text-sm truncate">Identity</p><span class="text-[9px] text-green-500 font-bold uppercase tracking-widest">Secure Link</span></div>
                <button onclick="logout()" class="text-slate-500 hover:text-red-500"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col min-w-0 bg-[var(--bg)] relative">
        <header class="h-20 flex items-center px-8 justify-between border-b border-white/5 glass sticky top-0 z-50">
            <div class="flex items-center gap-4">
                <button class="lg:hidden text-2xl" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                <h2 id="chat-h" class="font-black text-sm uppercase tracking-widest">Main Hub</h2>
            </div>
            <div class="flex items-center gap-3">
                <div class="hidden sm:block text-[10px] font-black bg-purple-600/10 px-4 py-2 rounded-full text-purple-400 uppercase">Users Online: <span id="stat-count">0</span></div>
            </div>
        </header>

        <div id="chat-flow">
            <div class="m-auto text-center opacity-10">
                <i class="fa-solid fa-wind text-7xl mb-4"></i>
                <p class="font-black uppercase tracking-widest">Awaiting Transmission</p>
            </div>
        </div>

        <div id="input-area" class="input-area hidden">
            <div id="reply-tag" class="hidden mb-4 p-4 glass rounded-2xl border-l-4 border-purple-500 flex justify-between items-center text-xs animate-bounce-short">
                <span id="reply-txt" class="truncate font-bold opacity-60"></span>
                <button onclick="cancelReply()"><i class="fa-solid fa-circle-xmark"></i></button>
            </div>
            <form id="m-form" class="pill">
                <input type="file" id="f-in" class="hidden" accept="image/*" onchange="upImg(this)">
                <button type="button" onclick="document.getElementById('f-in').click()" class="text-slate-500 hover:text-purple-500 transition"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                <input id="m-in" type="text" placeholder="Type a secure message..." autocomplete="off">
                <button class="bg-purple-600 text-white w-12 h-10 rounded-2xl flex items-center justify-center shadow-lg active:scale-90 transition-all"><i class="fa-solid fa-paper-plane text-xs"></i></button>
            </form>
        </div>
    </main>

    <div id="overlay" class="overlay" onclick="toggleSide()"></div>

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

        let user = localStorage.getItem('ae_u'), active = null, reply = null;
        if(user) login(user);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            user = val; localStorage.setItem('ae_u', val);
            document.getElementById('auth').style.display = 'none';
            document.getElementById('my-n').innerText = user;
            document.getElementById('my-av').innerText = user[0].toUpperCase();
            set(ref(db, 'online/' + user), true);
            loadUsers();
        }

        window.toggleTheme = () => {
            document.body.classList.toggle('light');
            const isL = document.body.classList.contains('light');
            document.getElementById('t-icon').className = isL ? "fa-solid fa-sun" : "fa-solid fa-moon";
        };

        window.toggleSide = () => {
            document.getElementById('side').classList.toggle('active');
            document.getElementById('overlay').classList.toggle('active');
        };

        window.openChat = (t) => {
            active = t;
            document.getElementById('input-area').classList.remove('hidden');
            document.getElementById('chat-h').innerText = t === 'global' ? 'Global Terminal' : `@${t}`;
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
                        d.className = "p-4 glass rounded-2xl cursor-pointer hover:scale-[1.02] flex items-center gap-4";
                        d.innerHTML = `<div class="w-3 h-3 rounded-full bg-purple-500 shadow-[0_0_10px_#a855f7]"></div> <span class="text-xs font-bold uppercase tracking-wider">${u.key}</span>`;
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
                    wrap.className = `msg-unit ${isM ? 'mine' : 'other'}`;
                    wrap.innerHTML = `
                        <div class="msg-menu text-white flex items-center">
                            <button onclick="setRep('${d.sender}','${d.text||"Media"}')" class="text-[9px] font-black px-2 hover:text-purple-400">REPLY</button>
                            <button onclick="react('${id}','❤️')" class="text-xs">❤️</button>
                            ${isM ? `<button onclick="del('${id}')" class="text-[9px] font-black px-2 text-red-400">DEL</button>` : ''}
                        </div>
                        <p class="text-[9px] font-black opacity-30 uppercase mb-2 px-3">${d.sender}</p>
                        <div class="card-ui">
                            ${d.reply ? `<div class="text-[10px] bg-black/10 p-3 rounded-xl mb-3 border-l-2 border-purple-500 italic opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                            ${d.img ? `<img src="${d.img}" class="rounded-2xl max-w-full shadow-lg">` : `<span>${d.text}</span>`}
                            <div id="rx-${id}" class="flex gap-1 mt-3"></div>
                        </div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="glass px-2 py-1 rounded-lg text-[9px] font-bold">${e} ${c}</span>`;
                        });
                    }
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setRep = (u, m) => {
            reply = { to: u, msg: m };
            document.getElementById('reply-tag').classList.remove('hidden');
            document.getElementById('reply-txt').innerText = `Replying to @${u}: ${m}`;
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
            if(confirm("Delete this transmission?")) {
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
