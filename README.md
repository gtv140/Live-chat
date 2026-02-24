<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Quantum | Final Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        
        :root { --p: #bc13fe; --s: #00d2ff; --bg: #020617; }
        
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body { background: var(--bg); color: #fff; height: 100dvh; overflow: hidden; margin: 0; }

        /* Glassmorphism & Neon */
        .glass { background: rgba(15, 23, 42, 0.7); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .neon-border { border: 1px solid rgba(188, 19, 254, 0.3); }
        .neon-text { text-shadow: 0 0 10px rgba(188, 19, 254, 0.6); }

        /* Layout Architecture */
        .app-shell { display: flex; height: 100dvh; width: 100vw; }
        
        /* Sidebar Logic */
        .sidebar { 
            width: 320px; flex-shrink: 0; background: #070b14; border-right: 1px solid rgba(255,255,255,0.05);
            display: flex; flex-direction: column; transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
        }

        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; z-index: 2000; }
            .sidebar.active { left: 0; box-shadow: 25px 0 60px rgba(0,0,0,0.9); }
            .main-content { width: 100vw !important; }
        }

        .main-content { flex-grow: 1; display: flex; flex-direction: column; position: relative; background: radial-gradient(circle at top right, rgba(188, 19, 254, 0.05), transparent); }

        /* Dashboard Stats Boxes */
        .stat-card { background: rgba(255,255,255,0.03); padding: 12px; border-radius: 18px; border: 1px solid rgba(255,255,255,0.05); text-align: center; }
        .stat-val { font-size: 1.2rem; font-weight: 800; color: var(--s); }

        /* Message Bubbles - Ultra Modern */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 16px; scroll-behavior: smooth; }
        .msg-container { max-width: 85%; position: relative; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .bubble { padding: 14px 18px; border-radius: 22px; font-size: 14px; line-height: 1.5; position: relative; }
        .mine { align-self: flex-end; }
        .mine .bubble { background: linear-gradient(135deg, var(--p), #7e22ce); border-bottom-right-radius: 4px; box-shadow: 0 8px 20px rgba(188, 19, 254, 0.2); }
        
        .other { align-self: flex-start; }
        .other .bubble { background: #1e293b; border-bottom-left-radius: 4px; border: 1px solid rgba(255,255,255,0.05); }

        /* Advanced Input Bar */
        .input-area { padding: 15px 20px 35px 20px; background: linear-gradient(to top, var(--bg), transparent); }
        .pill-box { background: rgba(255,255,255,0.05); border: 1px solid rgba(188, 19, 254, 0.3); border-radius: 35px; padding: 6px 10px; display: flex; align-items: center; gap: 10px; }
        .pill-box input { background: transparent; border: none; outline: none; color: #fff; flex: 1; padding: 10px; font-size: 15px; }

        .overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.7); backdrop-filter: blur(4px); z-index: 1500; }
        .overlay.active { display: block; }
        
        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-thumb { background: var(--p); border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#020617] p-6">
        <div class="glass p-12 rounded-[3.5rem] w-full max-w-sm text-center neon-border">
            <div class="mb-6 inline-flex p-4 bg-purple-600/20 rounded-3xl"><i class="fa-solid fa-bolt-lightning text-3xl text-purple-400"></i></div>
            <h1 class="text-3xl font-black mb-10 tracking-tighter italic uppercase">Neon<span class="text-cyan-400">Pro</span></h1>
            <input id="u-in" type="text" placeholder="Identity Name" class="w-full bg-white/5 border border-white/10 p-5 rounded-2xl text-white text-center mb-6 outline-none focus:border-purple-500 font-bold transition-all">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-2xl font-black shadow-[0_0_30px_rgba(188,19,254,0.3)] hover:scale-105 active:scale-95 transition-all">LAUNCH WORKSPACE</button>
        </div>
    </div>

    <div id="overlay" class="overlay" onclick="toggleSide()"></div>

    <div class="app-shell">
        <aside id="side" class="sidebar">
            <div class="p-8 flex justify-between items-center border-b border-white/5">
                <span class="text-2xl font-black italic tracking-tighter neon-text">NEON<span class="text-cyan-400">.OS</span></span>
                <button onclick="logout()" class="w-10 h-10 rounded-xl bg-red-500/10 text-red-500 flex items-center justify-center border border-red-500/10"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto p-6 space-y-6">
                <div>
                    <p class="text-[10px] text-slate-500 font-black uppercase tracking-widest mb-4">Core Channels</p>
                    <div onclick="openChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-2xl cursor-pointer font-bold flex items-center gap-3 hover:bg-purple-600/20 transition-all">
                        <i class="fa-solid fa-hashtag text-purple-400"></i> global-lounge
                    </div>
                </div>
                
                <div>
                    <p class="text-[10px] text-slate-500 font-black uppercase tracking-widest mb-4">Active Nodes</p>
                    <div id="u-list" class="space-y-3"></div>
                </div>
            </div>

            <div class="p-6 border-t border-white/5 grid grid-cols-2 gap-3">
                <div class="stat-card">
                    <p class="text-[8px] text-slate-400 uppercase font-black">Users</p>
                    <p id="stat-count" class="stat-val">0</p>
                </div>
                <div class="stat-card">
                    <p class="text-[8px] text-slate-400 uppercase font-black">Security</p>
                    <p class="stat-val text-green-400 text-xs">AES-256</p>
                </div>
            </div>
        </aside>

        <main class="main-content">
            <header class="h-20 flex items-center px-6 border-b border-white/5 justify-between glass z-50">
                <div class="flex items-center gap-4">
                    <button class="lg:hidden w-12 h-12 glass rounded-2xl text-purple-400 flex items-center justify-center border border-purple-500/20" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 id="chat-h" class="text-lg font-black tracking-tight uppercase italic">Dashboard Hub</h2>
                        <div class="flex items-center gap-2"><div class="w-1.5 h-1.5 bg-green-500 rounded-full animate-pulse shadow-[0_0_8px_#22c55e]"></div><span class="text-[9px] text-slate-500 font-black uppercase tracking-widest">Signal Verified</span></div>
                    </div>
                </div>
            </header>

            <div id="chat-flow">
                <div class="m-auto text-center opacity-20 max-w-xs">
                    <i class="fa-solid fa-shield-halved text-7xl mb-6 text-purple-500"></i>
                    <p class="text-sm font-black uppercase tracking-widest">Select a node to initialize encrypted transmission</p>
                </div>
            </div>

            <div id="input-area" class="input-area hidden">
                <div id="reply-tag" class="hidden mb-3 p-4 glass rounded-2xl border-l-4 border-cyan-400 flex justify-between items-center animate-bounce-in">
                    <div class="text-[10px]">
                        <span class="font-black text-cyan-400">REPLYING TO MESSAGE</span>
                        <p id="reply-msg" class="text-slate-400 italic truncate max-w-xs"></p>
                    </div>
                    <button onclick="cancelReply()"><i class="fa-solid fa-circle-xmark text-slate-500"></i></button>
                </div>
                
                <form id="m-form" class="pill-box">
                    <input type="file" id="f-in" class="hidden" accept="image/*" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('f-in').click()" class="text-slate-400 hover:text-cyan-400 transition ml-2"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="m-in" type="text" placeholder="Broadcast signal..." autocomplete="off">
                    <button class="bg-gradient-to-r from-purple-600 to-blue-500 w-14 h-11 rounded-[1.5rem] flex items-center justify-center shadow-xl active:scale-90 transition-all"><i class="fa-solid fa-paper-plane text-[10px]"></i></button>
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
            set(ref(db, 'online/' + user), true);
            loadUsers();
        }

        window.toggleSide = () => {
            document.getElementById('side').classList.toggle('active');
            document.getElementById('overlay').classList.toggle('active');
        };

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
                    wrap.className = `msg-container ${isM ? 'mine' : 'other'}`;
                    wrap.innerHTML = `
                        <p class="text-[9px] font-black text-slate-500 mb-2 uppercase tracking-widest px-2">${d.sender} • Sync Active</p>
                        ${d.reply ? `<div class="text-[10px] glass p-3 rounded-xl mb-1 border-l-2 border-cyan-400 italic opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble group cursor-pointer" onclick="toggleMsgMenu('${id}')">
                            ${d.img ? `<img src="${d.img}" class="rounded-xl max-w-full shadow-2xl">` : `<span>${d.text}</span>`}
                            <div id="menu-${id}" class="hidden absolute -top-10 ${isM ? 'right-0' : 'left-0'} glass p-2 rounded-xl gap-3 border-purple-500/50 shadow-2xl z-50 animate-bounce-in">
                                <button onclick="setRep('${d.sender}','${d.text||"Media"}')" class="text-[9px] font-black hover:text-cyan-400">REPLY</button>
                                <button onclick="react('${id}','🔥')" class="text-xs">🔥</button>
                                ${isM ? `<button onclick="del('${id}')" class="text-[9px] text-red-500 font-black">DELETE</button>` : ''}
                            </div>
                        </div>
                        <div id="rx-${id}" class="flex gap-1 mt-2"></div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="glass px-2 py-0.5 rounded-lg text-[9px] font-bold border border-purple-500/20">${e} ${c}</span>`;
                        });
                    }
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.toggleMsgMenu = (id) => {
            const el = document.getElementById(`menu-${id}`);
            el.classList.toggle('hidden'); el.classList.toggle('flex');
        }

        window.setRep = (u, m) => {
            reply = { to: u, msg: m };
            document.getElementById('reply-tag').classList.remove('hidden');
            document.getElementById('reply-msg').innerText = m;
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
            if(confirm("Erase signal?")) {
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

        window.logout = () => { if(confirm("Terminate session?")) { remove(ref(db, 'online/' + user)); localStorage.clear(); location.reload(); }};
        window.login = login;
    </script>
</body>
</html>
