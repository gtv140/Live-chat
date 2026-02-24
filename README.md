<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Quantum Elite</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        
        :root { 
            --neon-purple: #bc13fe;
            --neon-blue: #00d2ff;
            --bg-dark: #030712;
        }

        body, html { 
            height: 100%; margin: 0; background: var(--bg-dark); 
            font-family: 'Plus Jakarta Sans', sans-serif; color: #fff; overflow: hidden;
        }

        /* Ultimate Neon Background */
        .bg-glow {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: radial-gradient(circle at 10% 20%, rgba(188, 19, 254, 0.15) 0%, transparent 40%),
                        radial-gradient(circle at 90% 80%, rgba(0, 210, 255, 0.15) 0%, transparent 40%);
            z-index: -1;
        }

        /* Glassmorphism Classes */
        .glass { background: rgba(15, 23, 42, 0.6); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.1); }
        .glass-dark { background: rgba(7, 11, 20, 0.8); backdrop-filter: blur(30px); }

        /* Dashboard Grid */
        .app-grid { display: grid; grid-template-columns: 280px 1fr; height: 100dvh; }
        @media (max-width: 1024px) { .app-grid { grid-template-columns: 1fr; } }

        /* Modern Sidebar */
        .sidebar { transition: 0.5s cubic-bezier(0.4, 0, 0.2, 1); z-index: 2000; padding: 2rem; border-right: 1px solid rgba(188, 19, 254, 0.2); }
        @media (max-width: 1024px) { 
            .sidebar { position: fixed; inset: 0; transform: translateX(-100%); width: 100%; background: var(--bg-dark); } 
            .sidebar.active { transform: translateX(0); }
        }

        /* Chat Flow Bubbles */
        #chat-flow { flex: 1; overflow-y: auto; padding: 2rem; display: flex; flex-direction: column; gap: 1.5rem; scroll-behavior: smooth; }
        
        .msg-wrap { max-width: 80%; animation: slideUp 0.3s ease-out forwards; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .bubble { 
            padding: 1rem 1.5rem; border-radius: 1.5rem; font-size: 0.95rem; line-height: 1.5;
            position: relative; box-shadow: 0 10px 20px rgba(0,0,0,0.2);
        }
        
        .mine { align-self: flex-end; }
        .mine .bubble { 
            background: linear-gradient(135deg, var(--neon-purple), #7e22ce); 
            border-bottom-right-radius: 0; color: white; border: none;
            box-shadow: 0 0 20px rgba(188, 19, 254, 0.4);
        }

        .other { align-self: flex-start; }
        .other .bubble { background: rgba(30, 41, 59, 0.8); border-bottom-left-radius: 0; border: 1px solid rgba(255,255,255,0.05); }

        /* Input Area Modernization */
        .input-footer { padding: 1.5rem 2rem 3rem 2rem; background: linear-gradient(to top, var(--bg-dark), transparent); }
        .input-pill { 
            background: rgba(255,255,255,0.05); border: 1px solid rgba(188, 19, 254, 0.3);
            border-radius: 2rem; padding: 0.5rem 0.5rem 0.5rem 1.5rem; transition: 0.3s;
        }
        .input-pill:focus-within { border-color: var(--neon-blue); box-shadow: 0 0 20px rgba(0, 210, 255, 0.2); }

        /* Status Glow */
        .online-dot { width: 10px; height: 10px; background: #22c55e; border-radius: 50%; box-shadow: 0 0 10px #22c55e; }

        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-thumb { background: var(--neon-purple); border-radius: 10px; }
    </style>
</head>
<body>
    <div class="bg-glow"></div>

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center bg-black/80 backdrop-blur-md p-6">
        <div class="glass p-12 rounded-[3.5rem] w-full max-w-sm text-center border-purple-500/30">
            <div class="mb-6 flex justify-center">
                <div class="w-16 h-16 bg-gradient-to-tr from-purple-600 to-blue-400 rounded-2xl rotate-12 flex items-center justify-center shadow-2xl">
                    <i class="fa-solid fa-bolt text-3xl"></i>
                </div>
            </div>
            <h1 class="text-4xl font-extrabold mb-8 tracking-tighter uppercase italic">Neon<span class="text-blue-400">Pro</span></h1>
            <input id="u-in" type="text" placeholder="Enter Nickname" class="w-full bg-white/5 border border-white/10 p-5 rounded-2xl text-white text-center mb-6 focus:border-purple-500 transition outline-none font-bold">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-2xl font-black shadow-[0_0_30px_rgba(188,19,254,0.4)] hover:scale-105 active:scale-95 transition-all">LAUNCH SESSION</button>
        </div>
    </div>

    <div class="app-grid">
        <aside id="side" class="sidebar flex flex-col glass-dark">
            <div class="flex justify-between items-center mb-12">
                <span class="text-2xl font-black italic tracking-tighter text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-blue-400">NEON.OS</span>
                <button onclick="logout()" class="w-10 h-10 rounded-xl bg-red-500/10 text-red-500 border border-red-500/20 hover:bg-red-500/20 transition"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <nav class="flex-1 overflow-y-auto space-y-6">
                <div>
                    <p class="text-[10px] text-slate-500 font-black uppercase tracking-[0.2em] mb-4">Channels</p>
                    <div onclick="openChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/30 rounded-2xl cursor-pointer font-bold flex items-center gap-3 hover:bg-purple-600/20 transition-all group">
                        <i class="fa-solid fa-hashtag text-purple-400 group-hover:rotate-12 transition"></i> global-main
                    </div>
                </div>
                
                <div class="pt-4">
                    <p class="text-[10px] text-slate-500 font-black uppercase tracking-[0.2em] mb-4">Direct Signals</p>
                    <div id="u-list" class="space-y-3"></div>
                </div>
            </nav>

            <div class="mt-8 pt-6 border-t border-white/10 flex items-center gap-4">
                <div id="my-av" class="w-14 h-14 rounded-2xl bg-gradient-to-tr from-purple-600 to-blue-500 flex items-center justify-center font-black text-2xl shadow-xl">?</div>
                <div>
                    <p id="my-n" class="font-bold text-sm tracking-tight">Offline</p>
                    <div class="flex items-center gap-2"><div class="online-dot"></div><span class="text-[10px] text-green-500 font-bold uppercase">Ready</span></div>
                </div>
            </div>
        </aside>

        <main class="flex flex-col relative h-full">
            <header class="h-24 flex items-center justify-between px-8 glass border-b border-white/5 z-50">
                <div class="flex items-center gap-5">
                    <button class="lg:hidden w-12 h-12 glass rounded-2xl text-purple-400 flex items-center justify-center" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 id="chat-h" class="text-xl font-extrabold tracking-tight">Select a Frequency</h2>
                        <p class="text-[10px] text-slate-500 font-black uppercase tracking-widest">End-to-End Encrypted Sync</p>
                    </div>
                </div>
                <div class="flex gap-4">
                    <div class="w-10 h-10 glass rounded-xl flex items-center justify-center text-slate-400 hover:text-white transition cursor-pointer"><i class="fa-solid fa-magnifying-glass text-sm"></i></div>
                    <div class="w-10 h-10 glass rounded-xl flex items-center justify-center text-slate-400 hover:text-white transition cursor-pointer"><i class="fa-solid fa-gear text-sm"></i></div>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div id="input-area" class="input-footer hidden">
                <div id="reply-tag" class="hidden mb-4 p-4 glass rounded-2xl border-l-4 border-blue-400 flex justify-between items-center animate-bounce-in">
                    <div class="text-xs">
                        <span class="font-black text-blue-400 uppercase tracking-tighter">Replying To</span>
                        <p id="reply-msg" class="text-slate-400 italic truncate max-w-xs"></p>
                    </div>
                    <button onclick="cancelReply()"><i class="fa-solid fa-xmark text-slate-500"></i></button>
                </div>
                
                <form id="m-form" class="input-pill flex items-center gap-4">
                    <input type="file" id="f-in" class="hidden" accept="image/*" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('f-in').click()" class="text-slate-400 hover:text-blue-400 transition transform hover:rotate-90 duration-300"><i class="fa-solid fa-plus text-xl"></i></button>
                    <input id="m-in" type="text" placeholder="Type your signal..." class="bg-transparent flex-1 outline-none text-sm font-medium py-3" autocomplete="off">
                    <button class="bg-gradient-to-r from-purple-600 to-blue-500 w-14 h-12 rounded-[1.5rem] flex items-center justify-center shadow-xl hover:shadow-purple-500/20 active:scale-90 transition-all"><i class="fa-solid fa-paper-plane text-xs"></i></button>
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

        let myN = localStorage.getItem('neonSyncUser'), active = null, reply = null;

        if(myN) login(myN);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            myN = val; localStorage.setItem('neonSyncUser', val);
            document.getElementById('auth').style.display = 'none';
            document.getElementById('my-n').innerText = myN;
            document.getElementById('my-av').innerText = myN[0].toUpperCase();
            set(ref(db, 'online/' + myN), true);
            loadUsers();
        }

        window.toggleSide = () => document.getElementById('side').classList.toggle('active');

        window.openChat = (t) => {
            active = t;
            document.getElementById('input-area').classList.remove('hidden');
            document.getElementById('chat-h').innerText = t === 'global' ? 'Global Command' : `@${t.toLowerCase()}`;
            if(window.innerWidth < 1024) toggleSide();
            loadMsgs();
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('u-list'); list.innerHTML = "";
                s.forEach(u => {
                    if(u.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "p-4 glass rounded-2xl cursor-pointer hover:bg-blue-500/10 transition-all flex items-center gap-3 border border-white/5";
                        d.innerHTML = `<div class="online-dot"></div> <span class="font-bold text-sm tracking-tight italic uppercase">${u.key}</span>`;
                        d.onclick = () => openChat(u.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        function loadMsgs() {
            const p = active === 'global' ? 'msgs/global' : `private/${[myN, active].sort().join('_')}`;
            onValue(ref(db, p), (s) => {
                const flow = document.getElementById('chat-flow'); flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isM = d.sender === myN;
                    const wrap = document.createElement('div');
                    wrap.className = `msg-wrap ${isM ? 'mine' : 'other'}`;
                    
                    wrap.innerHTML = `
                        <div class="flex flex-col ${isM ? 'items-end' : 'items-start'}">
                            <span class="text-[9px] font-black text-slate-500 mb-2 uppercase tracking-widest px-4">${d.sender} • Signal Active</span>
                            ${d.reply ? `<div class="text-[9px] glass p-3 rounded-2xl mb-1 border-l-4 border-blue-400 italic opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                            <div class="bubble group cursor-pointer relative" onclick="toggleMenu('${id}')">
                                ${d.img ? `<img src="${d.img}" class="rounded-xl max-w-full shadow-2xl">` : `<span>${d.text}</span>`}
                                <div id="act-${id}" class="hidden absolute -top-12 ${isM ? 'right-0' : 'left-0'} glass p-2 rounded-xl gap-3 border-purple-500/50 shadow-2xl z-[100]">
                                    <button onclick="doReply('${d.sender}','${d.text||"Media"}')" class="text-[10px] font-black">REPLY</button>
                                    <button onclick="react('${id}','🔥')" class="text-xs">🔥</button>
                                    ${isM ? `<button onclick="del('${id}')" class="text-[10px] text-red-500 font-black">DELETE</button>` : ''}
                                </div>
                            </div>
                            <div id="rx-${id}" class="flex gap-1 mt-2"></div>
                        </div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="glass px-2 py-1 rounded-lg text-[10px] border border-blue-500/30 font-bold">${e} ${c}</span>`;
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
            const p = active === 'global' ? 'msgs/global' : `private/${[myN, active].sort().join('_')}`;
            push(ref(db, p), { sender: myN, img: url });
        };

        window.doReply = (u, m) => {
            reply = { to: u, msg: m };
            document.getElementById('reply-tag').classList.remove('hidden');
            document.getElementById('reply-msg').innerText = m;
        };

        window.cancelReply = () => { reply = null; document.getElementById('reply-tag').classList.add('hidden'); };

        window.react = (mid, e) => {
            const p = (active === 'global' ? 'msgs/global/' : `private/${[myN, active].sort().join('_')}/`) + mid + '/reactions/' + e;
            onValue(ref(db, p), (s) => set(ref(db, p), (s.val() || 0) + 1), { onlyOnce: true });
        };

        window.del = (id) => {
            const p = (active === 'global' ? 'msgs/global/' : `private/${[myN, active].sort().join('_')}/`) + id;
            remove(ref(db, p));
        };

        document.getElementById('m-form').onsubmit = (e) => {
            e.preventDefault();
            const i = document.getElementById('m-in');
            if(!i.value.trim() || !active) return;
            const p = active === 'global' ? 'msgs/global' : `private/${[myN, active].sort().join('_')}`;
            const m = { sender: myN, text: i.value.trim() };
            if(reply) { m.reply = reply; cancelReply(); }
            push(ref(db, p), m); i.value = "";
        };

        window.logout = () => { if(confirm("Disconnect Signal?")) { remove(ref(db, 'online/' + myN)); localStorage.clear(); location.reload(); }};
        window.login = login;
    </script>
</body>
</html>
