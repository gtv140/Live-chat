<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Beyond Elite</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@200;400;600;800&display=swap');
        
        :root { --p: #a855f7; --bg: #030712; }
        
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; outline: none; }
        
        body { 
            background: var(--bg); 
            color: #f8fafc; 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            overflow: hidden; 
            height: 100dvh;
            /* Animated Background */
            background: linear-gradient(-45deg, #030712, #0f172a, #1e1b4b, #030712);
            background-size: 400% 400%;
            animation: gradient 15s ease infinite;
        }

        @keyframes gradient { 0% {background-position: 0% 50%;} 50% {background-position: 100% 50%;} 100% {background-position: 0% 50%;} }

        .glass { background: rgba(15, 23, 42, 0.6); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.05); }
        .neon-border { border: 1px solid rgba(168, 85, 247, 0.2); box-shadow: 0 0 20px rgba(168, 85, 247, 0.05); }

        .app-grid { display: grid; grid-template-columns: 320px 1fr; height: 100dvh; }
        @media (max-width: 1024px) { .app-grid { grid-template-columns: 1fr; } }

        /* Sidebar Modernization */
        .sidebar { transition: 0.5s cubic-bezier(0.4, 0, 0.2, 1); z-index: 2000; }
        @media (max-width: 1024px) { 
            .sidebar { position: fixed; inset: 0; transform: translateX(-100%); width: 100%; background: rgba(3, 7, 18, 0.95); } 
            .sidebar.active { transform: translateX(0); }
        }

        /* Message Bubbles - Extra Modern */
        #chat-flow { flex: 1; overflow-y: auto; padding: 25px; display: flex; flex-direction: column; gap: 20px; scroll-behavior: smooth; }
        .msg-wrap { position: relative; max-width: 75%; transition: 0.3s; }
        .mine { align-self: flex-end; }
        .other { align-self: flex-start; }

        .bubble { 
            padding: 15px 22px; 
            border-radius: 28px; 
            font-size: 14.5px; 
            line-height: 1.6;
            position: relative;
            box-shadow: 0 10px 25px -5px rgba(0,0,0,0.1);
        }
        .mine .bubble { 
            background: linear-gradient(135deg, #7e22ce, #a855f7); 
            border-bottom-right-radius: 4px;
            color: white;
            text-shadow: 0 1px 2px rgba(0,0,0,0.2);
        }
        .other .bubble { 
            background: rgba(30, 41, 59, 0.7); 
            border: 1px solid rgba(255,255,255,0.08);
            border-bottom-left-radius: 4px;
        }

        /* Floating Input System */
        .input-wrapper { 
            padding: 15px 25px 35px 25px; 
            background: linear-gradient(to top, rgba(3,7,18,1), rgba(3,7,18,0));
            z-index: 1000;
        }
        .modern-bar { 
            background: rgba(255,255,255,0.05); 
            border: 1px solid rgba(255,255,255,0.1);
            border-radius: 30px; 
            padding: 8px 10px 8px 20px;
            transition: 0.3s;
        }
        .modern-bar:focus-within { border-color: #a855f7; background: rgba(255,255,255,0.08); box-shadow: 0 0 20px rgba(168, 85, 247, 0.1); }

        /* Fancy Animations */
        .fade-in { animation: fadeIn 0.4s ease-out forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: rgba(168, 85, 247, 0.4); border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center p-6">
        <div class="glass p-12 rounded-[4rem] w-full max-w-sm text-center neon-border">
            <div class="w-20 h-20 bg-purple-600 rounded-3xl mx-auto mb-8 flex items-center justify-center rotate-12 shadow-2xl">
                <i class="fa-solid fa-bolt-lightning text-4xl text-white"></i>
            </div>
            <h1 class="text-4xl font-black mb-10 tracking-tighter">NEON<span class="text-purple-500">PRO</span></h1>
            <input id="u-in" type="text" placeholder="Enter Nickname" class="w-full bg-white/5 border border-white/10 p-5 rounded-2xl text-white text-center outline-none mb-4 font-bold focus:border-purple-500 transition">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-2xl font-black shadow-xl hover:bg-purple-500 active:scale-95 transition-all">INITIALIZE</button>
        </div>
    </div>

    <div class="app-grid">
        <aside id="side" class="sidebar flex flex-col p-8 glass">
            <div class="flex justify-between items-center mb-12">
                <span class="text-2xl font-black tracking-tighter">WORKSPACE</span>
                <button onclick="logout()" class="w-12 h-12 rounded-2xl bg-red-500/10 text-red-500 border border-red-500/10 flex items-center justify-center active:scale-90 transition"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto space-y-6">
                <div>
                    <p class="text-[10px] text-slate-500 font-black uppercase tracking-widest mb-4 ml-2">Main Hubs</p>
                    <div onclick="openChat('global')" class="p-5 bg-purple-600/10 border border-purple-600/20 rounded-[2rem] cursor-pointer font-bold flex items-center justify-between group">
                        <span class="flex items-center gap-3"><i class="fa-solid fa-hashtag text-purple-400"></i> global-hq</span>
                        <i class="fa-solid fa-chevron-right text-[10px] opacity-0 group-hover:opacity-100 transition"></i>
                    </div>
                </div>
                
                <div class="pt-4">
                    <p class="text-[10px] text-slate-500 font-black uppercase tracking-widest mb-4 ml-2">Direct Links</p>
                    <div id="u-list" class="space-y-3"></div>
                </div>
            </div>

            <div class="mt-auto pt-8 flex items-center gap-4 border-t border-white/5">
                <div id="my-av" class="w-14 h-14 rounded-3xl bg-gradient-to-tr from-purple-700 to-purple-400 flex items-center justify-center font-black text-2xl shadow-xl">?</div>
                <div><p id="my-n" class="font-black text-sm">Offline</p><p class="text-[10px] text-green-500 font-bold uppercase tracking-tighter">● Online Now</p></div>
            </div>
        </aside>

        <main class="flex flex-col h-full relative">
            <header class="h-24 flex items-center justify-between px-8 z-50">
                <div class="flex items-center gap-5">
                    <button class="lg:hidden w-12 h-12 glass rounded-2xl text-purple-400 flex items-center justify-center" onclick="toggleSide()"><i class="fa-solid fa-chevron-left"></i></button>
                    <div>
                        <h2 id="chat-h" class="text-xl font-extrabold tracking-tight">Select a Signal</h2>
                        <div class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-purple-500 rounded-full animate-pulse"></span><span class="text-[10px] text-slate-400 font-bold uppercase tracking-widest">Secure Channel</span></div>
                    </div>
                </div>
                <div class="flex gap-4">
                    <button class="w-10 h-10 glass rounded-full flex items-center justify-center text-slate-400"><i class="fa-solid fa-video text-xs"></i></button>
                    <button class="w-10 h-10 glass rounded-full flex items-center justify-center text-slate-400"><i class="fa-solid fa-phone text-xs"></i></button>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div id="input-area" class="input-wrapper hidden">
                <div id="reply-box" class="hidden mb-3 p-4 glass rounded-3xl border-l-4 border-purple-500 flex justify-between items-center animate-slide-up">
                    <div class="text-xs">
                        <p class="font-black text-purple-400">Replying to message</p>
                        <p id="reply-txt" class="text-slate-400 truncate w-60"></p>
                    </div>
                    <button onclick="cancelReply()"><i class="fa-solid fa-circle-xmark text-slate-500"></i></button>
                </div>
                
                <form id="m-form" class="modern-bar flex items-center gap-4">
                    <input type="file" id="f-in" class="hidden" accept="image/*" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('f-in').click()" class="w-10 h-10 rounded-full flex items-center justify-center text-slate-400 hover:text-purple-400 transition hover:bg-white/5"><i class="fa-solid fa-plus"></i></button>
                    <input id="m-in" type="text" placeholder="Start typing..." class="bg-transparent flex-1 outline-none text-sm font-medium" autocomplete="off">
                    <button class="bg-purple-600 w-12 h-12 rounded-2xl flex items-center justify-center shadow-lg shadow-purple-600/40 active:scale-90 transition-all"><i class="fa-solid fa-paper-plane text-[10px]"></i></button>
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

        let myN = localStorage.getItem('neonUser'), active = null, reply = null;

        if(myN) login(myN);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            myN = val; localStorage.setItem('neonUser', val);
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
            document.getElementById('chat-h').innerText = t === 'global' ? 'Global Headquarters' : `@${t.toLowerCase()}`;
            if(window.innerWidth < 1024) toggleSide();
            loadMsgs();
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('u-list'); list.innerHTML = "";
                s.forEach(u => {
                    if(u.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "p-4 glass rounded-[1.5rem] cursor-pointer hover:bg-white/5 transition flex items-center gap-3 border border-white/5";
                        d.innerHTML = `<div class="w-3 h-3 rounded-full bg-green-500 shadow-[0_0_10px_#22c55e]"></div> <span class="font-bold text-sm tracking-tight">${u.key}</span>`;
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
                    wrap.className = `msg-wrap fade-in ${isM ? 'mine' : 'other'}`;
                    
                    wrap.innerHTML = `
                        <div class="flex flex-col ${isM ? 'items-end' : 'items-start'}">
                            <span class="text-[9px] font-black text-slate-500 mb-2 uppercase tracking-widest ml-4 mr-4">${d.sender} • ${new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}</span>
                            ${d.reply ? `<div class="text-[10px] glass p-3 rounded-2xl mb-1 border-l-4 border-purple-500 italic opacity-60 max-w-[200px] truncate">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                            <div class="bubble group relative cursor-pointer" onclick="showMenu('${id}','${d.sender}','${d.text}')">
                                ${d.img ? `<img src="${d.img}" class="rounded-2xl max-w-full hover:scale-[1.02] transition duration-300">` : `<span>${d.text}</span>`}
                                <div id="act-${id}" class="absolute -top-10 ${isM ? 'right-0' : 'left-0'} glass p-2 rounded-xl hidden gap-3 border border-purple-500/30 shadow-2xl">
                                    <button onclick="doReply('${d.sender}','${d.text||"Img"}')" class="text-[10px] font-bold">Reply</button>
                                    <button onclick="react('${id}','🔥')" class="text-xs">🔥</button>
                                    ${isM ? `<button onclick="del('${id}')" class="text-[10px] text-red-500">Del</button>` : ''}
                                </div>
                            </div>
                            <div id="rx-${id}" class="flex gap-1 mt-2"></div>
                        </div>
                    `;
                    flow.appendChild(wrap);
                    if(d.reactions) {
                        const rxD = document.getElementById(`rx-${id}`);
                        Object.entries(d.reactions).forEach(([e, c]) => {
                            rxD.innerHTML += `<span class="glass px-2 py-1 rounded-lg text-[10px] border border-white/10">${e} ${c}</span>`;
                        });
                    }
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.showMenu = (id) => {
            const m = document.getElementById(`act-${id}`);
            m.classList.toggle('flex'); m.classList.toggle('hidden');
        };

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
            document.getElementById('reply-box').classList.remove('hidden');
            document.getElementById('reply-txt').innerText = m;
        };

        window.cancelReply = () => { reply = null; document.getElementById('reply-box').classList.add('hidden'); };

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

        window.logout = () => { localStorage.clear(); location.reload(); };
        window.login = login;
    </script>
</body>
</html>
