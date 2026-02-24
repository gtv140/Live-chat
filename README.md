<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Universal | Final</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        :root { --p: #bc13fe; --s: #00d2ff; --bg: #020617; }
        
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Plus Jakarta Sans', sans-serif; }
        body { background: var(--bg); color: #fff; height: 100dvh; overflow: hidden; margin: 0; }

        .glass { background: rgba(15, 23, 42, 0.7); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        
        /* Layout Grid */
        .app-container { display: flex; height: 100dvh; width: 100vw; }

        /* Responsive Sidebar */
        .sidebar { 
            width: 320px; flex-shrink: 0; background: #070b14; border-right: 1px solid rgba(188, 19, 254, 0.1);
            display: flex; flex-direction: column; transition: 0.3s ease;
        }

        /* Mobile Adjustments */
        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; z-index: 2000; }
            .sidebar.active { left: 0; box-shadow: 20px 0 50px rgba(0,0,0,0.8); }
            .main-content { width: 100vw !important; }
        }

        .main-content { flex-grow: 1; display: flex; flex-direction: column; position: relative; }

        /* Stat Cards */
        .stat-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; padding: 10px; }
        .stat-card { background: rgba(255,255,255,0.03); padding: 10px; border-radius: 15px; text-align: center; border: 1px solid rgba(255,255,255,0.05); }

        /* Chat Stream */
        #chat-flow { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 15px; }
        .bubble { padding: 12px 16px; border-radius: 20px; max-width: 85%; font-size: 14px; position: relative; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
        .mine { align-self: flex-end; background: linear-gradient(135deg, var(--p), #7e22ce); border-bottom-right-radius: 4px; }
        .other { align-self: flex-start; background: #1e293b; border-bottom-left-radius: 4px; }

        /* Better Input Bar */
        .input-footer { padding: 10px 15px 30px 15px; background: var(--bg); border-top: 1px solid rgba(255,255,255,0.05); }
        .input-pill { background: rgba(255,255,255,0.05); border: 1px solid rgba(188,19,254,0.3); border-radius: 30px; display: flex; align-items: center; padding: 5px 15px; }
        .input-pill input { background: transparent; border: none; outline: none; color: #fff; flex: 1; padding: 10px 5px; font-size: 15px; }

        .overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.6); z-index: 1500; }
        .overlay.active { display: block; }
        
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: var(--p); border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#020617] p-4">
        <div class="glass p-10 rounded-[2.5rem] w-full max-w-sm text-center">
            <h1 class="text-3xl font-black mb-8 italic">NEON <span class="text-purple-500">PRO</span></h1>
            <input id="u-in" type="text" placeholder="Your Name" class="w-full bg-white/5 border border-white/10 p-4 rounded-xl text-white text-center mb-5 outline-none focus:border-purple-500 font-bold">
            <button onclick="login()" class="w-full bg-purple-600 py-4 rounded-xl font-black shadow-lg active:scale-95 transition">GET STARTED</button>
        </div>
    </div>

    <div id="overlay" class="overlay" onclick="toggleSide()"></div>

    <div class="app-container">
        <aside id="side" class="sidebar">
            <div class="p-6 flex justify-between items-center border-b border-white/5">
                <span class="text-xl font-black tracking-tighter italic">NEON.OS</span>
                <button onclick="logout()" class="text-red-400 text-sm"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto p-4">
                <div onclick="openChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-xl cursor-pointer font-bold mb-6 flex items-center gap-3">
                    <i class="fa-solid fa-earth-americas text-purple-400"></i> Global HQ
                </div>
                
                <p class="text-[10px] text-slate-500 font-black uppercase mb-3 ml-2">Active Users</p>
                <div id="u-list" class="space-y-2"></div>
            </div>

            <div class="p-4 border-t border-white/5">
                <div class="stat-grid">
                    <div class="stat-card"><p class="text-[8px] text-slate-400">Online</p><p id="stat-count" class="text-sm font-bold text-cyan-400">0</p></div>
                    <div class="stat-card"><p class="text-[8px] text-slate-400">Secure</p><p class="text-xs font-bold text-green-400">YES</p></div>
                    <div class="stat-card"><p class="text-[8px] text-slate-400">Server</p><p class="text-[8px] font-bold">ACTIVE</p></div>
                </div>
            </div>
        </aside>

        <main class="main-content">
            <header class="h-16 flex items-center px-4 border-b border-white/5 justify-between glass">
                <div class="flex items-center gap-3">
                    <button class="lg:hidden text-purple-400 text-xl p-2" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 id="chat-h" class="text-sm font-bold tracking-tight">Select Chat</h2>
                        <div class="flex items-center gap-1"><span class="w-1 h-1 bg-green-500 rounded-full"></span><span class="text-[8px] text-slate-500 font-bold uppercase">Encrypted Signal</span></div>
                    </div>
                </div>
                <div class="flex gap-4 text-slate-400">
                    <i class="fa-solid fa-video text-sm"></i>
                    <i class="fa-solid fa-phone text-sm"></i>
                </div>
            </header>

            <div id="chat-flow">
                <div class="m-auto text-center opacity-20">
                    <i class="fa-solid fa-comment-dots text-5xl mb-3"></i>
                    <p class="text-xs font-bold uppercase">Ready to transmit</p>
                </div>
            </div>

            <div id="input-area" class="input-footer hidden">
                <div id="reply-box" class="hidden mb-2 p-2 bg-purple-600/10 rounded-lg border-l-2 border-purple-500 text-[10px] flex justify-between">
                    <span id="reply-txt" class="truncate opacity-70"></span>
                    <button onclick="cancelReply()"><i class="fa-solid fa-xmark"></i></button>
                </div>
                <form id="m-form" class="input-pill">
                    <input type="file" id="f-in" class="hidden" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('f-in').click()" class="text-slate-400"><i class="fa-solid fa-circle-plus text-xl"></i></button>
                    <input id="m-in" type="text" placeholder="Type message..." autocomplete="off">
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

        let user = localStorage.getItem('neonUser'), active = null, reply = null;
        if(user) login(user);

        function login(n) {
            const val = n || document.getElementById('u-in').value.trim();
            if(!val) return;
            user = val; localStorage.setItem('neonUser', val);
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
                        d.className = "p-3 glass rounded-xl cursor-pointer hover:bg-purple-600/10 flex items-center gap-3 mb-1";
                        d.innerHTML = `<div class="w-2 h-2 rounded-full bg-cyan-400"></div> <span class="text-sm font-bold">${u.key}</span>`;
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
                    wrap.className = isM ? 'mine bubble' : 'other bubble';
                    wrap.innerHTML = `
                        <p class="text-[8px] font-bold opacity-50 mb-1">${d.sender}</p>
                        ${d.reply ? `<div class="text-[9px] bg-black/20 p-2 rounded mb-1 border-l-2 border-cyan-400 italic">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        ${d.img ? `<img src="${d.img}" class="rounded-lg max-w-full">` : `<span>${d.text}</span>`}
                        <div class="flex gap-2 mt-2">
                            <button onclick="setRep('${d.sender}','${d.text||"Img"}')" class="text-[8px] font-bold uppercase">Reply</button>
                            ${isM ? `<button onclick="del('${id}')" class="text-[8px] font-bold uppercase text-red-400">Del</button>` : ''}
                        </div>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setRep = (u, m) => {
            reply = { to: u, msg: m };
            document.getElementById('reply-box').classList.remove('hidden');
            document.getElementById('reply-txt').innerText = `Reply to @${u}: ${m}`;
            document.getElementById('m-in').focus();
        };

        window.cancelReply = () => { reply = null; document.getElementById('reply-box').classList.add('hidden'); };

        window.upImg = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `media/${Date.now()}`);
            const snap = await uploadBytes(r, f);
            const url = await getDownloadURL(snap.ref);
            const p = active === 'global' ? 'msgs/global' : `private/${[user, active].sort().join('_')}`;
            push(ref(db, p), { sender: user, img: url });
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
