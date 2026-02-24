<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NEON-X | Ultimate Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;800&display=swap');
        
        :root { --p: #7c3aed; --s: #06b6d4; --bg: #030712; --card: #111827; --text: #f9fafb; }
        .light { --bg: #f3f4f6; --card: #ffffff; --text: #111827; }

        * { font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        body { background: var(--bg); color: var(--text); height: 100dvh; overflow: hidden; }

        /* Premium Animations */
        @keyframes float { 0% { transform: translateY(0px); } 50% { transform: translateY(-10px); } 100% { transform: translateY(0px); } }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.08); }
        
        /* Layout Structure */
        .sidebar { width: 300px; background: var(--card); border-right: 1px solid rgba(255,255,255,0.05); }
        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; z-index: 100; height: 100%; width: 85%; }
            .sidebar.active { left: 0; box-shadow: 20px 0 50px rgba(0,0,0,0.5); }
        }

        /* Message Bubbles 2.0 */
        #chat-flow { flex: 1; overflow-y: auto; padding: 25px; scroll-behavior: smooth; }
        .msg-wrap { max-width: 80%; margin-bottom: 20px; position: relative; }
        .bubble { padding: 15px 20px; border-radius: 24px; font-size: 15px; position: relative; box-shadow: 0 10px 25px -5px rgba(0,0,0,0.1); }
        
        .mine { align-self: flex-end; }
        .mine .bubble { background: linear-gradient(135deg, var(--p), #4f46e5); color: white; border-bottom-right-radius: 4px; }
        
        .other { align-self: flex-start; }
        .other .bubble { background: var(--card); border: 1px solid rgba(255,255,255,0.05); border-bottom-left-radius: 4px; }

        /* Floating Menu */
        .msg-options { display: none; position: absolute; top: -45px; right: 0; background: var(--card); border-radius: 50px; padding: 5px 15px; gap: 15px; border: 1px solid var(--p); z-index: 10; }
        .msg-wrap:hover .msg-options, .msg-wrap:active .msg-options { display: flex; animation: float 2s infinite ease-in-out; }

        /* Input Deck */
        .input-deck { padding: 20px; background: var(--bg); border-top: 1px solid rgba(255,255,255,0.05); }
        .pill-input { background: var(--card); border-radius: 30px; padding: 8px 15px; border: 1px solid rgba(255,255,255,0.1); display: flex; align-items: center; gap: 12px; }
        .pill-input:focus-within { border-color: var(--p); box-shadow: 0 0 20px rgba(124, 58, 237, 0.15); }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: var(--p); border-radius: 10px; }
    </style>
</head>
<body class="flex">

    <div id="auth" class="fixed inset-0 z-[1000] flex items-center justify-center bg-[#030712] p-8">
        <div class="max-w-md w-full text-center">
            <div class="mb-8 inline-block p-6 rounded-[2.5rem] bg-gradient-to-tr from-purple-600 to-cyan-400 shadow-2xl rotate-6 animate-bounce"><i class="fa-solid fa-bolt-lightning text-4xl text-white"></i></div>
            <h1 class="text-4xl font-black mb-2 tracking-tighter">NEON<span class="text-purple-500">-X</span></h1>
            <p class="text-slate-500 mb-10 font-medium">Ultimate Communication Protocol</p>
            <input id="u-in" type="text" placeholder="Access Identity" class="w-full bg-gray-900 border border-gray-800 p-5 rounded-3xl text-center mb-6 outline-none focus:border-purple-500 transition-all font-bold text-white">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-3xl font-black shadow-2xl hover:bg-purple-700 active:scale-95">ESTABLISH LINK</button>
        </div>
    </div>

    <aside id="side" class="sidebar flex flex-col transition-all duration-500">
        <div class="p-8 flex items-center justify-between">
            <span class="text-2xl font-black italic tracking-tighter text-purple-500">X<span class="text-white">.OS</span></span>
            <button onclick="toggleMode()" class="w-10 h-10 rounded-2xl glass flex items-center justify-center"><i id="m-icon" class="fa-solid fa-moon text-purple-400"></i></button>
        </div>
        
        <div class="flex-1 overflow-y-auto px-6 space-y-8">
            <div>
                <p class="text-[10px] font-black text-slate-500 uppercase tracking-widest mb-4">Core Hubs</p>
                <div onclick="openChat('global')" class="p-4 bg-purple-600/10 border-l-4 border-purple-500 rounded-xl cursor-pointer font-bold flex items-center gap-4 hover:bg-purple-600/20">
                    <i class="fa-solid fa-globe"></i> global-stream
                </div>
            </div>
            
            <div>
                <p class="text-[10px] font-black text-slate-500 uppercase tracking-widest mb-4">Live Nodes</p>
                <div id="u-list" class="space-y-3"></div>
            </div>
        </div>

        <div class="p-6 mt-auto border-t border-white/5 bg-black/20">
            <div class="flex items-center gap-4">
                <div id="my-av" class="w-12 h-12 rounded-2xl bg-gradient-to-br from-purple-600 to-blue-500 flex items-center justify-center font-black text-white shadow-lg">?</div>
                <div class="flex-1 truncate"><p id="my-n" class="font-black text-sm truncate">Identity</p><span class="text-[9px] text-green-500 font-bold uppercase">Online Secure</span></div>
                <button onclick="logout()" class="text-red-500/50 hover:text-red-500"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col min-w-0 bg-[var(--bg)] relative">
        <header class="h-20 flex items-center px-8 border-b border-white/5 glass justify-between sticky top-0 z-50">
            <div class="flex items-center gap-4">
                <button class="lg:hidden text-2xl" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                <div><h2 id="chat-h" class="font-black text-lg tracking-tight uppercase italic">Select Terminal</h2><p class="text-[9px] text-slate-500 font-bold uppercase tracking-widest">Active AES-256 Encryption</p></div>
            </div>
            <div class="flex items-center gap-6 text-slate-400">
                <i class="fa-solid fa-phone hover:text-purple-500 cursor-pointer"></i>
                <i class="fa-solid fa-video hover:text-cyan-500 cursor-pointer"></i>
                <div class="hidden sm:block text-[10px] font-black bg-purple-600/10 px-4 py-2 rounded-full text-purple-400">USERS: <span id="stat-count">0</span></div>
            </div>
        </header>

        <div id="chat-flow" class="flex flex-col">
            <div class="m-auto text-center opacity-10">
                <i class="fa-solid fa-shield-halved text-7xl mb-4"></i>
                <p class="font-black uppercase tracking-widest">Ready for Transmission</p>
            </div>
        </div>

        <div id="input-area" class="input-deck hidden">
            <div id="reply-tag" class="hidden mb-4 p-4 glass rounded-2xl border-l-4 border-purple-500 flex justify-between items-center text-xs animate-pulse">
                <span id="reply-txt" class="truncate font-bold text-slate-400"></span>
                <button onclick="cancelReply()"><i class="fa-solid fa-circle-xmark"></i></button>
            </div>
            <form id="m-form" class="pill-input">
                <input type="file" id="f-in" class="hidden" accept="image/*" onchange="upImg(this)">
                <button type="button" onclick="document.getElementById('f-in').click()" class="w-10 h-10 rounded-full flex items-center justify-center text-slate-400 hover:text-purple-500 transition"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                <input id="m-in" type="text" placeholder="Type an encrypted message..." autocomplete="off" class="bg-transparent flex-1 outline-none text-sm font-medium">
                <button class="bg-purple-600 text-white w-12 h-10 rounded-2xl flex items-center justify-center shadow-xl active:scale-90"><i class="fa-solid fa-paper-plane text-xs"></i></button>
            </form>
        </div>
    </main>

    <div id="overlay" class="fixed inset-0 bg-black/60 hidden z-[90] backdrop-blur-sm" onclick="toggleSide()"></div>

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

        let user = localStorage.getItem('nx_user'), active = null, reply = null;
        if(user) login(user);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            user = val; localStorage.setItem('nx_user', val);
            document.getElementById('auth').style.display = 'none';
            document.getElementById('my-n').innerText = user;
            document.getElementById('my-av').innerText = user[0].toUpperCase();
            set(ref(db, 'online/' + user), true);
            loadUsers();
        }

        window.toggleMode = () => {
            document.body.classList.toggle('light');
            const isL = document.body.classList.contains('light');
            document.getElementById('m-icon').className = isL ? "fa-solid fa-sun" : "fa-solid fa-moon";
        };

        window.toggleSide = () => {
            document.getElementById('side').classList.toggle('active');
            document.getElementById('overlay').classList.toggle('hidden');
        };

        window.openChat = (t) => {
            active = t;
            document.getElementById('input-area').classList.remove('hidden');
            document.getElementById('chat-h').innerText = t === 'global' ? 'Global Stream' : `@${t}`;
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
                        d.className = "p-4 glass rounded-2xl cursor-pointer hover:scale-[1.02] flex items-center gap-4 mb-2";
                        d.innerHTML = `<div class="w-2.5 h-2.5 rounded-full bg-cyan-400 shadow-[0_0_10px_#22d3ee]"></div> <span class="text-xs font-black uppercase italic">${u.key}</span>`;
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
                    wrap.className = `msg-wrap ${isM ? 'mine' : 'other'} flex flex-col`;
                    wrap.innerHTML = `
                        <div class="msg-options text-white items-center">
                            <button onclick="setRep('${d.sender}','${d.text||"Media"}')" class="text-[9px] font-black px-2 hover:text-cyan-400">REPLY</button>
                            <button onclick="react('${id}','❤️')" class="text-xs">❤️</button>
                            ${isM ? `<button onclick="del('${id}')" class="text-[9px] font-black px-2 text-red-400">DEL</button>` : ''}
                        </div>
                        <span class="text-[8px] font-black opacity-30 mb-2 uppercase px-3 tracking-tighter">${d.sender}</span>
                        <div class="bubble">
                            ${d.reply ? `<div class="text-[9px] bg-black/20 p-3 rounded-xl mb-3 border-l-2 border-purple-500 italic opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                            ${d.img ? `<img src="${d.img}" class="rounded-2xl max-w-full">` : `<span class="font-medium">${d.text}</span>`}
                        </div>
                        <div id="rx-${id}" class="flex gap-1 mt-2 px-2"></div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="glass px-2 py-1 rounded-lg text-[9px] font-black">${e} ${c}</span>`;
                        });
                    }
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setRep = (u, m) => {
            reply = { to: u, msg: m };
            document.getElementById('reply-tag').classList.remove('hidden');
            document.getElementById('reply-txt').innerText = `REPLYING TO @${u}: ${m}`;
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
            if(confirm("Erase signal permanently?")) {
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
