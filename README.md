<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>NeonPro | Modern & Clean</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;700;800&display=swap');
        
        :root { --accent: #bc13fe; --secondary: #00d2ff; --bg-main: #05070a; }
        
        * { box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: var(--bg-main); color: #e2e8f0; height: 100dvh; overflow: hidden; margin: 0; }

        /* Clean Modern Sidebar */
        .sidebar { 
            width: 280px; flex-shrink: 0; background: #0a0c10; border-right: 1px solid rgba(255,255,255,0.03);
            display: flex; flex-direction: column; transition: 0.3s ease;
        }

        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; z-index: 2000; }
            .sidebar.active { left: 0; box-shadow: 10px 0 30px rgba(0,0,0,0.5); }
        }

        .main-view { flex-grow: 1; display: flex; flex-direction: column; background: #05070a; }

        /* Modern Bubbles */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 14px; }
        .msg-unit { max-width: 80%; position: relative; }
        .bubble { padding: 12px 16px; border-radius: 16px; font-size: 14px; line-height: 1.6; transition: 0.2s; position: relative; }
        
        .mine { align-self: flex-end; }
        .mine .bubble { background: var(--accent); color: white; border-bottom-right-radius: 4px; }
        
        .other { align-self: flex-start; }
        .other .bubble { background: #1a1d23; border-bottom-left-radius: 4px; border: 1px solid rgba(255,255,255,0.05); }

        /* Feature Menu (Contextual) */
        .action-menu { 
            display: none; position: absolute; top: -35px; right: 0; background: #1e2229; 
            border-radius: 10px; border: 1px solid var(--accent); padding: 4px 8px; gap: 12px; z-index: 50;
        }
        .msg-unit:hover .action-menu { display: flex; }

        /* Slack-like Inputs */
        .input-container { padding: 15px 20px 30px 20px; border-top: 1px solid rgba(255,255,255,0.03); }
        .modern-pill { background: #11141a; border-radius: 14px; border: 1px solid rgba(255,255,255,0.08); padding: 6px 12px; display: flex; align-items: center; gap: 12px; }
        .modern-pill:focus-within { border-color: var(--accent); }
        .modern-pill input { background: transparent; border: none; outline: none; color: white; flex: 1; padding: 10px 0; font-size: 15px; }

        /* Overlay */
        .overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.6); z-index: 1500; backdrop-filter: blur(4px); }
        .overlay.active { display: block; }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: #1e2229; border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#05070a] p-6">
        <div class="w-full max-w-sm text-center">
            <div class="mb-6"><i class="fa-solid fa-circle-nodes text-4xl text-purple-500"></i></div>
            <h1 class="text-2xl font-bold mb-8 tracking-tight">Sign in to <span class="text-purple-500">NeonPro</span></h1>
            <input id="u-in" type="text" placeholder="Your Display Name" class="w-full bg-[#11141a] border border-white/5 p-4 rounded-xl text-white text-center mb-4 outline-none focus:border-purple-500 transition-all font-medium">
            <button onclick="login()" class="w-full bg-purple-600 py-4 rounded-xl font-bold shadow-lg active:scale-95 transition-all">Launch Dashboard</button>
        </div>
    </div>

    <div id="overlay" class="overlay" onclick="toggleSide()"></div>

    <div class="flex h-screen w-screen overflow-hidden">
        <aside id="side" class="sidebar">
            <div class="p-6 flex items-center gap-3 border-b border-white/5">
                <div class="w-8 h-8 bg-purple-600 rounded-lg flex items-center justify-center"><i class="fa-solid fa-n text-sm"></i></div>
                <span class="font-bold tracking-tight">Main Hub</span>
                <button onclick="logout()" class="ml-auto text-slate-500 text-xs hover:text-red-500"><i class="fa-solid fa-right-from-bracket"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto p-4 space-y-6">
                <div>
                    <p class="text-[11px] text-slate-500 font-bold uppercase tracking-wider mb-3">Channels</p>
                    <div onclick="openChat('global')" class="p-3 bg-white/5 border border-white/5 rounded-xl cursor-pointer font-semibold flex items-center gap-3 hover:bg-white/10 transition">
                        <i class="fa-solid fa-hashtag text-purple-400"></i> global-chat
                    </div>
                </div>
                
                <div>
                    <p class="text-[11px] text-slate-500 font-bold uppercase tracking-wider mb-3">Direct Messages</p>
                    <div id="u-list" class="space-y-1"></div>
                </div>
            </div>

            <div class="p-4 border-t border-white/5 flex items-center gap-3">
                <div class="w-10 h-10 rounded-full bg-gradient-to-br from-purple-500 to-blue-500 flex items-center justify-center font-bold text-xs" id="my-av">?</div>
                <div><p id="my-n" class="text-xs font-bold truncate w-24">Guest</p><p class="text-[8px] text-green-500 uppercase font-black tracking-widest">Active Now</p></div>
            </div>
        </aside>

        <main class="main-view">
            <header class="h-16 flex items-center px-6 border-b border-white/5 bg-[#0a0c10]/50 backdrop-blur-md justify-between">
                <div class="flex items-center gap-3">
                    <button class="lg:hidden text-slate-400" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                    <h2 id="chat-h" class="text-sm font-bold">Select a conversation</h2>
                </div>
                <div class="flex gap-4 text-slate-500 text-xs">
                    <div class="hidden sm:block">Nodes: <span id="stat-count" class="text-purple-400">0</span></div>
                    <i class="fa-solid fa-shield-halved text-green-500"></i>
                </div>
            </header>

            <div id="chat-flow">
                <div class="m-auto text-center opacity-20">
                    <i class="fa-solid fa-comments text-5xl mb-4"></i>
                    <p class="text-xs font-bold uppercase tracking-widest">End-to-End Encrypted</p>
                </div>
            </div>

            <div id="input-area" class="input-container hidden">
                <div id="reply-tag" class="hidden mb-2 p-3 bg-purple-500/10 rounded-xl border-l-2 border-purple-500 text-[10px] flex justify-between items-center">
                    <span id="reply-txt" class="truncate italic text-slate-400"></span>
                    <button onclick="cancelReply()"><i class="fa-solid fa-xmark"></i></button>
                </div>
                <form id="m-form" class="modern-pill">
                    <input type="file" id="f-in" class="hidden" accept="image/*" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('f-in').click()" class="text-slate-500 hover:text-purple-400"><i class="fa-solid fa-plus text-lg"></i></button>
                    <input id="m-in" type="text" placeholder="Type a message..." autocomplete="off">
                    <button class="text-purple-500 px-2"><i class="fa-solid fa-paper-plane"></i></button>
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

        let user = localStorage.getItem('neonSyncU'), active = null, reply = null;
        if(user) login(user);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            user = val; localStorage.setItem('neonSyncU', val);
            document.getElementById('auth').style.display = 'none';
            document.getElementById('my-n').innerText = user;
            document.getElementById('my-av').innerText = user[0].toUpperCase();
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
            document.getElementById('chat-h').innerText = t === 'global' ? '# global-chat' : `@ ${t}`;
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
                        d.className = "p-3 rounded-lg cursor-pointer hover:bg-white/5 transition flex items-center gap-3";
                        d.innerHTML = `<div class="w-2 h-2 rounded-full bg-purple-500 shadow-[0_0_8px_#bc13fe]"></div> <span class="text-xs font-semibold">${u.key}</span>`;
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
                        <div class="bubble">
                            <div class="action-menu">
                                <button onclick="setRep('${d.sender}','${d.text||"Media"}')" class="text-[9px] font-bold text-white hover:text-purple-400">REPLY</button>
                                <button onclick="react('${id}','🔥')" class="text-[10px]">🔥</button>
                                ${isM ? `<button onclick="del('${id}')" class="text-[9px] text-red-400 font-bold">DEL</button>` : ''}
                            </div>
                            ${d.reply ? `<div class="text-[9px] bg-black/20 p-2 rounded-lg mb-2 border-l-2 border-purple-500 italic opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                            ${d.img ? `<img src="${d.img}" class="rounded-lg max-w-full shadow-lg mb-1">` : `<span>${d.text}</span>`}
                            <p class="text-[8px] opacity-30 mt-1 font-bold">${d.sender}</p>
                        </div>
                        <div id="rx-${id}" class="flex gap-1 mt-1"></div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="bg-white/5 px-2 py-0.5 rounded-full text-[8px] border border-white/5">${e} ${c}</span>`;
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
            push(ref(db, p), { sender: user, img: url });
        };

        window.react = (mid, e) => {
            const p = (active === 'global' ? 'msgs/global/' : `private/${[user, active].sort().join('_')}/`) + mid + '/reactions/' + e;
            onValue(ref(db, p), (s) => set(ref(db, p), (s.val() || 0) + 1), { onlyOnce: true });
        };

        window.del = (id) => {
            if(confirm("Delete message?")) {
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
    </script>
</body>
</html>
