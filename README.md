<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Quantum Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        :root { --neon: #bc13fe; --cyan: #00d2ff; --bg: #020617; }
        body, html { height: 100%; margin: 0; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #fff; overflow: hidden; }
        
        .glass { background: rgba(15, 23, 42, 0.7); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .neon-card { border: 1px solid rgba(188, 19, 254, 0.3); box-shadow: 0 0 15px rgba(188, 19, 254, 0.1); }
        
        .app-grid { display: grid; grid-template-columns: 280px 1fr; height: 100dvh; }
        @media (max-width: 1024px) { .app-grid { grid-template-columns: 1fr; } }

        /* Dashboard Stats */
        .stat-box { padding: 1.25rem; border-radius: 1.5rem; background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); }
        .stat-val { font-size: 1.5rem; font-weight: 800; color: var(--cyan); text-shadow: 0 0 10px rgba(0,210,255,0.4); }

        /* Sidebar & Navigation */
        .sidebar { background: #070b14; border-right: 1px solid rgba(188, 19, 254, 0.2); transition: 0.4s; z-index: 2000; }
        @media (max-width: 1024px) { .sidebar { position: fixed; inset: 0; transform: translateX(-100%); width: 100%; } .sidebar.active { transform: translateX(0); } }

        /* Chat System */
        #chat-flow { flex: 1; overflow-y: auto; padding: 1.5rem; display: flex; flex-direction: column; gap: 1rem; scroll-behavior: smooth; }
        .bubble { padding: 12px 18px; border-radius: 20px; font-size: 14px; position: relative; }
        .mine .bubble { background: linear-gradient(135deg, var(--neon), #7e22ce); border-bottom-right-radius: 2px; }
        .other .bubble { background: #1e293b; border-bottom-left-radius: 2px; }

        /* Input Area */
        .footer-area { background: linear-gradient(to top, #020617, transparent); padding: 1rem 1.5rem 2.5rem 1.5rem; }
        .input-pill { background: rgba(255,255,255,0.05); border: 1px solid rgba(188, 19, 254, 0.3); border-radius: 30px; padding: 6px 10px; }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: var(--neon); border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#020617] p-6">
        <div class="glass p-12 rounded-[3.5rem] w-full max-w-sm text-center border-purple-500/30 border">
            <h1 class="text-4xl font-black mb-10 tracking-tighter italic text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-cyan-400">NEON PRO</h1>
            <input id="u-in" type="text" placeholder="Access Identity" class="w-full bg-white/5 border border-white/10 p-5 rounded-2xl text-white text-center mb-6 focus:border-purple-500 transition font-bold outline-none">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-2xl font-black shadow-xl active:scale-95 transition-all">CONNECT</button>
        </div>
    </div>

    <div class="app-grid">
        <aside id="side" class="sidebar flex flex-col p-6">
            <div class="flex justify-between items-center mb-10">
                <span class="text-xl font-black italic tracking-tighter">QUANTUM<span class="text-cyan-400">.OS</span></span>
                <button onclick="logout()" class="text-red-500 bg-red-500/10 p-2 rounded-lg"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto space-y-4">
                <p class="text-[10px] text-slate-500 font-black uppercase tracking-[0.2em]">Live Channels</p>
                <div onclick="openChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-2xl cursor-pointer font-bold flex items-center gap-3">
                    <i class="fa-solid fa-hashtag text-purple-400"></i> global-hq
                </div>
                
                <p class="text-[10px] text-slate-500 font-black uppercase tracking-[0.2em] mt-8">Secure Nodes</p>
                <div id="u-list" class="space-y-2"></div>
            </div>

            <div class="mt-8 pt-6 border-t border-white/5 flex items-center gap-4">
                <div id="my-av" class="w-12 h-12 rounded-2xl bg-gradient-to-tr from-purple-600 to-cyan-500 flex items-center justify-center font-black text-xl">?</div>
                <div><p id="my-n" class="font-bold text-sm">Offline</p><p class="text-[9px] text-green-500 font-bold uppercase tracking-widest animate-pulse">● System Ready</p></div>
            </div>
        </aside>

        <main class="flex flex-col h-full overflow-hidden">
            <header class="p-6 glass border-b border-white/5">
                <div class="flex items-center gap-4 mb-6">
                    <button class="lg:hidden w-10 h-10 glass rounded-xl text-purple-400 flex items-center justify-center" onclick="toggleSide()"><i class="fa-solid fa-bars"></i></button>
                    <h2 id="chat-h" class="text-lg font-extrabold tracking-tight">Command Center</h2>
                </div>

                <div class="grid grid-cols-3 gap-4">
                    <div class="stat-box">
                        <p class="text-[9px] text-slate-400 font-bold uppercase mb-1">Active Nodes</p>
                        <p id="stat-count" class="stat-val">01</p>
                    </div>
                    <div class="stat-box">
                        <p class="text-[9px] text-slate-400 font-bold uppercase mb-1">Secured Data</p>
                        <p class="stat-val">AES-256</p>
                    </div>
                    <div class="stat-box">
                        <p class="text-[9px] text-slate-400 font-bold uppercase mb-1">Uptime</p>
                        <p class="stat-val text-green-400">99.9%</p>
                    </div>
                </div>
            </header>

            <div id="chat-flow">
                <div class="h-full flex flex-col items-center justify-center text-center opacity-30">
                    <i class="fa-solid fa-shield-halved text-6xl mb-4"></i>
                    <p class="font-black italic text-xl">SELECT A CHANNEL TO INITIALIZE SYNC</p>
                </div>
            </div>

            <div id="input-area" class="footer-area hidden">
                <div id="reply-box" class="hidden mb-3 p-3 bg-purple-600/10 rounded-xl border-l-4 border-purple-500 flex justify-between items-center text-[10px]">
                    <span id="reply-txt" class="italic opacity-70"></span>
                    <button onclick="cancelReply()"><i class="fa-solid fa-x"></i></button>
                </div>
                <form id="m-form" class="input-pill flex items-center gap-3">
                    <input type="file" id="f-in" class="hidden" accept="image/*" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('f-in').click()" class="text-slate-500 hover:text-cyan-400 transition ml-2"><i class="fa-solid fa-plus-circle text-2xl"></i></button>
                    <input id="m-in" type="text" placeholder="Broadcast signal..." class="bg-transparent flex-1 outline-none text-sm p-3 font-medium text-white" autocomplete="off">
                    <button class="bg-gradient-to-r from-purple-600 to-blue-500 w-12 h-10 rounded-full flex items-center justify-center shadow-lg active:scale-90 transition"><i class="fa-solid fa-paper-plane text-[10px]"></i></button>
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

        let user = localStorage.getItem('neonSyncUser'), active = null, reply = null;

        if(user) login(user);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            user = val; localStorage.setItem('neonSyncUser', val);
            document.getElementById('auth').style.display = 'none';
            document.getElementById('my-n').innerText = user;
            document.getElementById('my-av').innerText = user[0].toUpperCase();
            set(ref(db, 'online/' + user), true);
            loadUsers();
        }

        window.toggleSide = () => document.getElementById('side').classList.toggle('active');

        window.openChat = (t) => {
            active = t;
            document.getElementById('input-area').classList.remove('hidden');
            document.getElementById('chat-h').innerText = t === 'global' ? 'Global Command HQ' : `@${t.toUpperCase()}`;
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
                        d.className = "p-4 glass rounded-2xl cursor-pointer hover:bg-purple-600/10 transition flex items-center gap-3 mb-2 border border-white/5";
                        d.innerHTML = `<div class="w-2 h-2 rounded-full bg-cyan-400 shadow-[0_0_10px_#00d2ff]"></div> <span class="font-bold text-sm tracking-tighter uppercase italic">${u.key}</span>`;
                        d.onclick = () => openChat(u.key);
                        list.appendChild(d);
                    }
                });
                document.getElementById('stat-count').innerText = count.toString().padStart(2, '0');
            });
        }

        function loadMsgs() {
            const p = active === 'global' ? 'msgs/global' : `private/${[user, active].sort().join('_')}`;
            onValue(ref(db, p), (s) => {
                const flow = document.getElementById('chat-flow'); flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isM = d.sender === user;
                    const wrap = document.createElement('div');
                    wrap.className = `flex flex-col mb-4 ${isM ? 'items-end' : 'items-start'}`;
                    
                    wrap.innerHTML = `
                        <span class="text-[8px] font-black text-slate-500 mb-1 uppercase tracking-widest px-2">${d.sender} • Secured</span>
                        ${d.reply ? `<div class="text-[9px] glass p-2 rounded-xl mb-1 border-l-2 border-cyan-400 opacity-60 italic">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble cursor-pointer hover:scale-[1.02] transition" onclick="toggleMenu('${id}')">
                            ${d.img ? `<img src="${d.img}" class="rounded-xl max-w-full">` : d.text}
                            <div id="act-${id}" class="hidden absolute -top-10 ${isM ? 'right-0' : 'left-0'} glass p-2 rounded-xl gap-3 border-purple-500/50 shadow-2xl z-50">
                                <button onclick="setReply('${d.sender}','${d.text||"Media"}')" class="text-[9px] font-black">REPLY</button>
                                <button onclick="react('${id}','🔥')" class="text-xs">🔥</button>
                                ${isM ? `<button onclick="del('${id}')" class="text-[9px] text-red-500 font-black">DEL</button>` : ''}
                            </div>
                        </div>
                        <div id="rx-${id}" class="flex gap-1 mt-1"></div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="glass px-2 py-0.5 rounded-lg text-[8px] font-bold border border-purple-500/30">${e} ${c}</span>`;
                        });
                    }
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.toggleMenu = (id) => {
            const el = document.getElementById(`act-${id}`);
            el.classList.toggle('hidden'); el.classList.toggle('flex');
        }

        window.upImg = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `media/${Date.now()}`);
            const snap = await uploadBytes(r, f);
            const url = await getDownloadURL(snap.ref);
            const p = active === 'global' ? 'msgs/global' : `private/${[user, active].sort().join('_')}`;
            push(ref(db, p), { sender: user, img: url });
        };

        window.setReply = (u, m) => {
            reply = { to: u, msg: m };
            document.getElementById('reply-box').classList.remove('hidden');
            document.getElementById('reply-txt').innerText = `Syncing with @${u}: ${m}`;
            document.getElementById('m-in').focus();
        };

        window.cancelReply = () => { reply = null; document.getElementById('reply-box').classList.add('hidden'); };

        window.react = (mid, e) => {
            const p = (active === 'global' ? 'msgs/global/' : `private/${[user, active].sort().join('_')}/`) + mid + '/reactions/' + e;
            onValue(ref(db, p), (s) => set(ref(db, p), (s.val() || 0) + 1), { onlyOnce: true });
        };

        window.del = (id) => {
            const p = (active === 'global' ? 'msgs/global/' : `private/${[user, active].sort().join('_')}/`) + id;
            remove(ref(db, p));
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

        window.logout = () => { if(confirm("Disconnect Signal?")) { remove(ref(db, 'online/' + user)); localStorage.clear(); location.reload(); }};
        window.login = login;
    </script>
</body>
</html>
