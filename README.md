<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Aura Pro | Next-Gen</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;500;700;800&display=swap');
        
        :root { --p: #8b5cf6; --bg: #0b0e14; --card: #161b26; --txt: #f8fafc; }
        .light { --bg: #f1f5f9; --card: #ffffff; --txt: #0f172a; }

        * { font-family: 'Plus Jakarta Sans', sans-serif; transition: all 0.25s ease; -webkit-tap-highlight-color: transparent; }
        body { background: var(--bg); color: var(--txt); height: 100dvh; overflow: hidden; margin: 0; }

        /* Premium Layout */
        .sidebar { width: 300px; background: var(--card); border-right: 1px solid rgba(255,255,255,0.05); }
        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; z-index: 1000; height: 100%; width: 85%; border-right: none; }
            .sidebar.active { left: 0; box-shadow: 25px 0 60px rgba(0,0,0,0.5); }
        }

        /* Message Aesthetic */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 16px; }
        .msg-node { max-width: 82%; position: relative; animation: pop 0.3s cubic-bezier(0.18, 0.89, 0.32, 1.28); }
        @keyframes pop { from { opacity: 0; transform: scale(0.9); } to { opacity: 1; transform: scale(1); } }

        .bubble { padding: 14px 18px; border-radius: 22px; font-size: 14px; line-height: 1.5; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
        .mine { align-self: flex-end; }
        .mine .bubble { background: linear-gradient(135deg, var(--p), #6d28d9); color: white; border-bottom-right-radius: 4px; }
        
        .other { align-self: flex-start; }
        .other .bubble { background: var(--card); border: 1px solid rgba(255,255,255,0.03); border-bottom-left-radius: 4px; }

        /* Quick Menu (Skype Style) */
        .quick-actions { 
            display: none; position: absolute; top: -38px; right: 0; 
            background: var(--card); border-radius: 50px; padding: 5px 12px; 
            gap: 12px; border: 1px solid var(--p); z-index: 50; box-shadow: 0 10px 20px rgba(0,0,0,0.2);
        }
        .msg-node:hover .quick-actions, .msg-node:active .quick-actions { display: flex; }

        /* Modern Input Area */
        .input-deck { padding: 15px 20px 30px 20px; background: var(--bg); }
        .pill-box { background: var(--card); border-radius: 28px; padding: 6px 14px; display: flex; align-items: center; gap: 10px; border: 1px solid rgba(255,255,255,0.05); }
        .pill-box input { background: transparent; border: none; outline: none; color: inherit; flex: 1; padding: 10px 5px; font-size: 15px; }

        .overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.5); backdrop-filter: blur(4px); z-index: 900; }
        .overlay.active { display: block; }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: var(--p); border-radius: 10px; }
    </style>
</head>
<body class="flex">

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#0b0e14] p-10">
        <div class="w-full max-w-sm text-center">
            <div class="w-16 h-16 bg-purple-600 rounded-3xl flex items-center justify-center mx-auto mb-6 shadow-2xl shadow-purple-500/40"><i class="fa-solid fa-wind text-2xl text-white"></i></div>
            <h1 class="text-3xl font-extrabold mb-2 tracking-tight">Aura<span class="text-purple-500">Pro</span></h1>
            <p class="text-slate-500 text-sm mb-10 font-medium tracking-wide italic">"Simply Premium. Fully Modern."</p>
            <input id="u-in" type="text" placeholder="Enter Nickname" class="w-full bg-white/5 border border-white/10 p-5 rounded-3xl text-center mb-5 outline-none focus:border-purple-500 font-bold text-white transition-all">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-3xl font-black shadow-xl active:scale-95 text-white">START CHATTING</button>
        </div>
    </div>

    <aside id="side" class="sidebar flex flex-col">
        <div class="p-8 flex items-center justify-between">
            <span class="text-xl font-extrabold tracking-tighter">AURA.</span>
            <button onclick="toggleTheme()" class="w-10 h-10 rounded-2xl bg-black/5 flex items-center justify-center"><i id="t-icon" class="fa-solid fa-moon text-purple-500"></i></button>
        </div>
        
        <div class="flex-1 overflow-y-auto px-6 space-y-6">
            <div>
                <p class="text-[10px] font-black text-slate-500 uppercase tracking-widest mb-3">Public Hubs</p>
                <div onclick="openChat('global')" class="p-4 bg-purple-600/10 border border-purple-500/20 rounded-2xl cursor-pointer font-bold flex items-center gap-3 text-purple-400">
                    <i class="fa-solid fa-hashtag"></i> global-chat
                </div>
            </div>
            
            <div>
                <p class="text-[10px] font-black text-slate-500 uppercase tracking-widest mb-3">Direct Messages</p>
                <div id="u-list" class="space-y-2"></div>
            </div>
        </div>

        <div class="p-6 mt-auto border-t border-white/5">
            <div class="flex items-center gap-3">
                <div id="my-av" class="w-10 h-10 rounded-2xl bg-purple-600 flex items-center justify-center font-bold text-white">?</div>
                <div class="flex-1 overflow-hidden"><p id="my-n" class="font-bold text-sm truncate">User</p><span class="text-[8px] text-green-500 font-black uppercase">Protected</span></div>
                <button onclick="logout()" class="text-red-500/60 hover:text-red-500"><i class="fa-solid fa-sign-out-alt"></i></button>
            </div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col min-w-0 bg-[var(--bg)]">
        <header class="h-20 flex items-center px-6 border-b border-white/5 justify-between backdrop-blur-md">
            <div class="flex items-center gap-4">
                <button class="lg:hidden text-2xl" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                <h2 id="chat-h" class="font-extrabold text-sm uppercase tracking-tight">Main Dashboard</h2>
            </div>
            <div class="flex items-center gap-4 text-slate-400 text-xs font-bold">
                Online: <span id="stat-count" class="text-purple-500">0</span>
            </div>
        </header>

        <div id="chat-flow">
            <div class="m-auto text-center opacity-10">
                <i class="fa-solid fa-comment-dots text-6xl mb-4"></i>
                <p class="text-xs font-black uppercase tracking-widest">End-to-End Secure</p>
            </div>
        </div>

        <div id="input-area" class="input-deck hidden">
            <div id="reply-tag" class="hidden mb-3 p-3 bg-purple-600/5 rounded-2xl border-l-4 border-purple-500 flex justify-between items-center text-xs animate-slide">
                <span id="reply-txt" class="truncate italic opacity-60"></span>
                <button onclick="cancelReply()"><i class="fa-solid fa-times"></i></button>
            </div>
            <form id="m-form" class="pill-box">
                <input type="file" id="f-in" class="hidden" accept="image/*" onchange="upImg(this)">
                <button type="button" onclick="document.getElementById('f-in').click()" class="text-slate-400 hover:text-purple-500"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                <input id="m-in" type="text" placeholder="Send a message..." autocomplete="off">
                <button class="bg-purple-600 text-white w-10 h-10 rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-paper-plane text-xs"></i></button>
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

        let user = localStorage.getItem('aura_u'), active = null, reply = null;
        if(user) login(user);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            user = val; localStorage.setItem('aura_u', val);
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
            document.getElementById('chat-h').innerText = t === 'global' ? 'Global Channel' : `@${t}`;
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
                        d.className = "p-4 bg-[var(--card)] rounded-2xl cursor-pointer hover:scale-[1.02] flex items-center gap-3 border border-black/5";
                        d.innerHTML = `<div class="w-2.5 h-2.5 rounded-full bg-purple-500 shadow-lg shadow-purple-500/40"></div> <span class="text-xs font-bold uppercase tracking-tight">${u.key}</span>`;
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
                    wrap.className = `msg-node ${isM ? 'mine' : 'other'}`;
                    wrap.innerHTML = `
                        <div class="quick-actions text-white flex items-center">
                            <button onclick="setRep('${d.sender}','${d.text||"Media"}')" class="text-[9px] font-black px-2 hover:text-purple-400">REPLY</button>
                            <button onclick="react('${id}','❤️')" class="text-xs">❤️</button>
                            ${isM ? `<button onclick="del('${id}')" class="text-[9px] font-black px-2 text-red-400">DEL</button>` : ''}
                        </div>
                        <p class="text-[8px] font-black opacity-30 uppercase tracking-tighter mb-1 px-2">${d.sender}</p>
                        <div class="bubble">
                            ${d.reply ? `<div class="text-[9px] bg-black/10 p-2.5 rounded-xl mb-3 border-l-2 border-purple-500 italic opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                            ${d.img ? `<img src="${d.img}" class="rounded-xl max-w-full shadow-lg">` : `<span>${d.text}</span>`}
                            <div id="rx-${id}" class="flex gap-1 mt-2"></div>
                        </div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="bg-black/10 px-2 py-0.5 rounded-lg text-[8px] font-black">${e} ${c}</span>`;
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
            if(confirm("Erase this message?")) {
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
