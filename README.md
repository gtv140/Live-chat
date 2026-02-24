<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>NeonPro | Sweet & Smooth</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;500;800&display=swap');
        
        :root { --neon: #bc13fe; --cyan: #00d2ff; --dark: #0a0a0c; }
        
        * { box-sizing: border-box; font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: var(--dark); color: #fff; height: 100dvh; overflow: hidden; margin: 0; }

        /* Light & Fast Glass Effect */
        .glass-ui { background: rgba(20, 20, 25, 0.8); border: 1px solid rgba(255,255,255,0.05); }
        .neon-text { color: var(--neon); text-shadow: 0 0 8px var(--neon); }

        .app-layout { display: flex; height: 100dvh; width: 100vw; }
        
        /* Responsive Sidebar */
        .sidebar { 
            width: 300px; flex-shrink: 0; background: #0e0e12; border-right: 1px solid rgba(188, 19, 254, 0.1);
            display: flex; flex-direction: column; transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 80%; z-index: 2000; }
            .sidebar.active { left: 0; box-shadow: 20px 0 40px rgba(0,0,0,0.8); }
        }

        .main-chat { flex-grow: 1; display: flex; flex-direction: column; background: radial-gradient(circle at center, #111, #0a0a0c); }

        /* Smooth Bubbles */
        #chat-flow { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px; }
        .msg-box { max-width: 85%; animation: slide 0.2s ease-out; }
        @keyframes slide { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }

        .bubble { padding: 12px 16px; border-radius: 18px; font-size: 14px; position: relative; border: 1px solid rgba(255,255,255,0.03); }
        .mine { align-self: flex-end; }
        .mine .bubble { background: linear-gradient(135deg, var(--neon), #8e0ce8); border-bottom-right-radius: 2px; box-shadow: 0 4px 12px rgba(188, 19, 254, 0.2); }
        
        .other { align-self: flex-start; }
        .other .bubble { background: #1a1a20; border-bottom-left-radius: 2px; }

        /* Feature Menu */
        .msg-menu { display: none; background: #222; border-radius: 12px; padding: 5px; gap: 10px; position: absolute; top: -40px; z-index: 100; border: 1px solid var(--neon); }
        .msg-box:hover .msg-menu, .msg-box:active .msg-menu { display: flex; }

        /* Input System */
        .bottom-bar { padding: 10px 15px 25px 15px; background: #0e0e12; }
        .input-pill { background: #1a1a20; border-radius: 25px; padding: 5px 10px; border: 1px solid rgba(188,19,254,0.2); }
        .input-pill input { background: transparent; border: none; outline: none; color: #fff; flex: 1; padding: 8px; font-size: 15px; }

        .overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.5); z-index: 1500; backdrop-filter: blur(2px); }
        .overlay.active { display: block; }

        ::-webkit-scrollbar { width: 3px; }
        ::-webkit-scrollbar-thumb { background: var(--neon); }
    </style>
</head>
<body>

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#0a0a0c] p-6">
        <div class="glass-ui p-10 rounded-[2.5rem] w-full max-w-sm text-center">
            <h1 class="text-3xl font-extrabold mb-8 italic neon-text">NEON<span class="text-white">PRO</span></h1>
            <input id="u-in" type="text" placeholder="Enter Nickname" class="w-full bg-white/5 border border-white/10 p-4 rounded-2xl text-white text-center mb-5 outline-none focus:border-purple-500 font-bold">
            <button onclick="login()" class="w-full bg-purple-600 py-4 rounded-2xl font-black shadow-lg active:scale-95 transition">JOIN HUB</button>
        </div>
    </div>

    <div id="overlay" class="overlay" onclick="toggleSide()"></div>

    <div class="app-layout">
        <aside id="side" class="sidebar">
            <div class="p-6 flex justify-between items-center border-b border-white/5">
                <span class="text-xl font-black neon-text italic">QUANTUM</span>
                <button onclick="logout()" class="text-red-500"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto p-4 space-y-4">
                <div onclick="openChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-xl cursor-pointer font-bold flex items-center gap-3">
                    <i class="fa-solid fa-globe text-cyan-400"></i> Global Chat
                </div>
                <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest ml-2">Active Signals</p>
                <div id="u-list" class="space-y-2"></div>
            </div>

            <div class="p-4 bg-[#0a0a0c] border-t border-white/5 flex justify-around text-[10px] font-bold">
                <div class="text-center"><p class="text-slate-500">NODES</p><p id="stat-count" class="text-cyan-400">0</p></div>
                <div class="text-center"><p class="text-slate-500">SECURE</p><p class="text-green-500">100%</p></div>
            </div>
        </aside>

        <main class="main-chat">
            <header class="h-16 flex items-center px-4 border-b border-white/5 glass-ui justify-between">
                <div class="flex items-center gap-3">
                    <button class="lg:hidden text-purple-500 text-xl" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 id="chat-h" class="text-sm font-bold uppercase tracking-tight">Select Frequency</h2>
                        <span class="text-[8px] text-green-500 font-black">● LIVE SYNC</span>
                    </div>
                </div>
            </header>

            <div id="chat-flow">
                <div class="m-auto text-center opacity-30">
                    <i class="fa-solid fa-bolt-lightning text-4xl mb-3 text-purple-500"></i>
                    <p class="text-[10px] font-black uppercase">Initialize Connection</p>
                </div>
            </div>

            <div id="input-area" class="bottom-bar hidden">
                <div id="reply-tag" class="hidden mb-2 p-2 bg-purple-600/10 rounded-lg border-l-2 border-purple-500 text-[10px] flex justify-between">
                    <span id="reply-txt" class="truncate opacity-70"></span>
                    <button onclick="cancelReply()"><i class="fa-solid fa-xmark"></i></button>
                </div>
                <form id="m-form" class="input-pill flex items-center gap-2">
                    <input type="file" id="f-in" class="hidden" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('f-in').click()" class="text-slate-400 px-2"><i class="fa-solid fa-circle-plus text-xl"></i></button>
                    <input id="m-in" type="text" placeholder="Type signal..." autocomplete="off">
                    <button class="bg-purple-600 w-10 h-10 rounded-full flex items-center justify-center shadow-lg"><i class="fa-solid fa-paper-plane text-[10px]"></i></button>
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

        let user = localStorage.getItem('neonName'), active = null, reply = null;
        if(user) login(user);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            user = val; localStorage.setItem('neonName', val);
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
            document.getElementById('chat-h').innerText = t === 'global' ? 'Global HQ' : `@${t}`;
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
                        d.className = "p-3 glass-ui rounded-xl cursor-pointer hover:border-purple-500/50 flex items-center gap-3";
                        d.innerHTML = `<div class="w-2 h-2 rounded-full bg-cyan-400"></div> <span class="text-sm font-bold uppercase">${u.key}</span>`;
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
                    wrap.className = `msg-box ${isM ? 'mine' : 'other'}`;
                    wrap.innerHTML = `
                        <p class="text-[8px] font-bold opacity-40 mb-1 ml-2 uppercase">${d.sender}</p>
                        <div class="bubble">
                            <div class="msg-menu">
                                <button onclick="setRep('${d.sender}','${d.text||"Image"}')" class="text-[9px] font-bold px-1">REPLY</button>
                                <button onclick="react('${id}','🔥')" class="text-xs">🔥</button>
                                ${isM ? `<button onclick="del('${id}')" class="text-[9px] text-red-500 font-bold px-1">DEL</button>` : ''}
                            </div>
                            ${d.reply ? `<div class="text-[9px] bg-black/30 p-2 rounded mb-1 border-l-2 border-cyan-400 italic">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                            ${d.img ? `<img src="${d.img}" class="rounded-lg max-w-full">` : `<span>${d.text}</span>`}
                        </div>
                        <div id="rx-${id}" class="flex gap-1 mt-1"></div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="bg-black/40 px-2 py-0.5 rounded-lg text-[8px] border border-white/5">${e} ${c}</span>`;
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

        window.logout = () => { remove(ref(db, 'online/' + user)); localStorage.clear(); location.reload(); };
        window.login = login;
    </script>
</body>
</html>
