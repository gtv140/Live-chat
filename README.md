<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Elite | Ultimate Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        :root { --p: #a855f7; --bg: #020617; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body, html { height: 100%; margin: 0; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; overflow: hidden; }
        
        .glass { background: rgba(15, 23, 42, 0.85); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        
        /* Layout Grid */
        .app-grid { display: grid; grid-template-columns: 300px 1fr; height: 100dvh; }
        @media (max-width: 1024px) { .app-grid { grid-template-columns: 1fr; } }

        .sidebar { background: #070b14; border-right: 1px solid rgba(255,255,255,0.03); transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); z-index: 2000; }
        @media (max-width: 1024px) { 
            .sidebar { position: fixed; inset: 0; transform: translateX(-100%); width: 100%; }
            .sidebar.active { transform: translateX(0); }
        }

        /* Chat Flow */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; padding-bottom: 120px; }
        .msg-wrap { position: relative; max-width: 85%; display: flex; flex-direction: column; }
        .mine { align-self: flex-end; align-items: flex-end; }
        .other { align-self: flex-start; align-items: flex-start; }

        .bubble { padding: 14px 20px; border-radius: 24px; font-size: 14px; position: relative; transition: 0.2s; box-shadow: 0 4px 15px rgba(0,0,0,0.2); }
        .mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); border-bottom-right-radius: 4px; }
        .other .bubble { background: #1e293b; border-bottom-left-radius: 4px; border: 1px solid rgba(255,255,255,0.05); }

        /* Floating Actions Menu */
        .actions-menu { position: absolute; top: -40px; background: #1e293b; border-radius: 12px; padding: 6px 14px; display: none; gap: 12px; z-index: 50; border: 1px solid rgba(255,255,255,0.15); box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        .msg-wrap:hover .actions-menu { display: flex; }
        @media (max-width: 1024px) { .msg-wrap:active .actions-menu { display: flex; } }

        /* Fixed Input Logic */
        .input-container { position: fixed; bottom: 0; right: 0; left: 300px; background: rgba(7, 11, 20, 0.95); border-top: 1px solid rgba(255,255,255,0.05); padding: 15px 25px 25px 25px; z-index: 1000; }
        @media (max-width: 1024px) { .input-container { left: 0; padding: 10px; } }

        .input-bar { background: rgba(255,255,255,0.05); border-radius: 20px; border: 1px solid rgba(255,255,255,0.1); padding: 5px 15px; }
        
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth-scr" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#020617] p-6">
        <div class="glass p-12 rounded-[3.5rem] w-full max-w-sm text-center">
            <h1 class="text-4xl font-black mb-10 tracking-tighter text-purple-500">NEON ELITE</h1>
            <input id="u-in" type="text" placeholder="Your Nickname" class="w-full bg-white/5 border border-white/10 p-5 rounded-2xl text-white text-center outline-none focus:border-purple-500 mb-4 font-bold">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-2xl font-black shadow-xl active:scale-95 transition-all">ENTER SPACE</button>
        </div>
    </div>

    <div class="app-grid">
        <aside id="side" class="sidebar flex flex-col p-6">
            <div class="flex justify-between items-center mb-10">
                <span class="text-2xl font-black text-purple-400 tracking-tighter">WORKSPACE</span>
                <button onclick="logout()" class="w-10 h-10 rounded-xl bg-red-500/10 text-red-500 flex items-center justify-center"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto space-y-2">
                <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest mb-4">Live Channels</p>
                <div onclick="openChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-2xl cursor-pointer font-bold flex items-center gap-3">
                    <i class="fa-solid fa-hashtag text-purple-400"></i> global-hq
                </div>
                <div id="u-list" class="pt-4 space-y-2"></div>
            </div>
            
            <div class="pt-6 border-t border-white/5 flex items-center gap-4">
                <div id="my-avatar" class="w-12 h-12 rounded-2xl bg-purple-600 flex items-center justify-center font-bold text-xl">?</div>
                <div>
                    <p id="my-name" class="font-bold text-sm">User</p>
                    <p class="text-[9px] text-green-500 font-bold">● ONLINE</p>
                </div>
            </div>
        </aside>

        <main class="relative flex flex-col h-full overflow-hidden">
            <header class="h-20 flex items-center justify-between px-8 glass border-b border-white/5 z-50">
                <div class="flex items-center gap-4">
                    <button class="lg:hidden text-2xl" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                    <h2 id="chat-title" class="text-lg font-black tracking-tight">Select Chat</h2>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div id="input-area" class="input-container hidden">
                <div id="reply-box" class="hidden mb-2 p-3 bg-purple-600/10 rounded-xl border-l-4 border-purple-500 flex justify-between items-center">
                    <span id="reply-txt" class="text-[10px] text-purple-200 truncate"></span>
                    <button onclick="cancelReply()"><i class="fa-solid fa-xmark"></i></button>
                </div>
                <form id="m-form" class="input-bar flex items-center gap-3">
                    <input type="file" id="img-in" class="hidden" accept="image/*" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('img-in').click()" class="text-slate-400 hover:text-purple-400"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="m-in" type="text" placeholder="Message..." class="bg-transparent flex-1 outline-none text-sm p-3 font-medium" autocomplete="off">
                    <button class="bg-purple-600 w-10 h-10 rounded-xl flex items-center justify-center shadow-lg active:scale-90 transition-all"><i class="fa-solid fa-paper-plane text-[10px]"></i></button>
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
            myN = val;
            localStorage.setItem('neonUser', val);
            document.getElementById('auth-scr').style.display = 'none';
            document.getElementById('my-name').innerText = myN;
            document.getElementById('my-avatar').innerText = myN[0].toUpperCase();
            set(ref(db, 'online/' + myN), true);
            loadUsers();
        }

        window.toggleSide = () => document.getElementById('side').classList.toggle('active');

        window.openChat = (t) => {
            active = t;
            document.getElementById('input-area').classList.remove('hidden');
            document.getElementById('chat-title').innerText = t === 'global' ? '# global-hq' : `@ ${t}`;
            if(window.innerWidth < 1024) toggleSide();
            loadMsgs();
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('u-list'); list.innerHTML = "";
                s.forEach(u => {
                    if(u.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "p-4 glass rounded-2xl cursor-pointer hover:bg-purple-600/10 transition flex items-center gap-3";
                        d.innerHTML = `<div class="w-2 h-2 rounded-full bg-green-500"></div> <span class="text-sm font-bold">${u.key}</span>`;
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
                    
                    let rx = "";
                    if(d.reactions) Object.entries(d.reactions).forEach(([e, c]) => {
                        rx += `<span onclick="react('${id}','${e}')" class="bg-white/5 px-2 py-1 rounded-lg text-[10px] cursor-pointer border border-white/5">${e} ${c}</span>`;
                    });

                    wrap.innerHTML = `
                        <div class="actions-menu">
                            <button onclick="doReply('${d.sender}','${d.text||"Media"}')" class="text-[10px] font-bold">Reply</button>
                            <button onclick="react('${id}','👍')" class="text-xs">👍</button>
                            <button onclick="react('${id}','🔥')" class="text-xs">🔥</button>
                            ${isM ? `<button onclick="del('${id}')" class="text-[10px] text-red-500 font-bold ml-2">Delete</button>` : ''}
                        </div>
                        <span class="text-[9px] text-slate-500 font-bold mb-1 px-3 uppercase tracking-tighter">${d.sender}</span>
                        ${d.reply ? `<div class="text-[9px] bg-white/5 p-2 rounded-xl mb-1 border-l-2 border-purple-500 opacity-60">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble">${d.img ? `<img src="${d.img}" class="rounded-xl max-w-full shadow-lg">` : d.text}</div>
                        <div class="flex gap-1 mt-1">${rx}</div>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.upImg = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `media/${Date.now()}_${f.name}`);
            const snap = await uploadBytes(r, f);
            const url = await getDownloadURL(snap.ref);
            const p = active === 'global' ? 'msgs/global' : `private/${[myN, active].sort().join('_')}`;
            push(ref(db, p), { sender: myN, img: url, time: Date.now() });
        };

        window.doReply = (u, m) => {
            reply = { to: u, msg: m };
            document.getElementById('reply-box').classList.remove('hidden');
            document.getElementById('reply-txt').innerText = `Replying to @${u}: ${m}`;
            document.getElementById('m-in').focus();
        };

        window.cancelReply = () => { reply = null; document.getElementById('reply-box').classList.add('hidden'); };

        window.react = (mid, e) => {
            const p = (active === 'global' ? 'msgs/global/' : `private/${[myN, active].sort().join('_')}/`) + mid + '/reactions/' + e;
            onValue(ref(db, p), (s) => set(ref(db, p), (s.val() || 0) + 1), { onlyOnce: true });
        };

        window.del = (id) => {
            if(confirm("Delete message?")) {
                const p = (active === 'global' ? 'msgs/global/' : `private/${[myN, active].sort().join('_')}/`) + id;
                remove(ref(db, p));
            }
        };

        document.getElementById('m-form').onsubmit = (e) => {
            e.preventDefault();
            const i = document.getElementById('m-in');
            if(!i.value.trim() || !active) return;
            const p = active === 'global' ? 'msgs/global' : `private/${[myN, active].sort().join('_')}`;
            const m = { sender: myN, text: i.value.trim(), time: Date.now() };
            if(reply) { m.reply = reply; cancelReply(); }
            push(ref(db, p), m); i.value = "";
        };

        window.logout = () => { if(confirm("Logout?")) { remove(ref(db, 'online/' + myN)); localStorage.clear(); location.reload(); }};
        window.login = login;
    </script>
</body>
</html>
