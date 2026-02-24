<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Ultimate</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        :root { --p: #a855f7; --bg: #020617; }
        body, html { height: 100%; margin: 0; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; overflow: hidden; }
        
        .glass { background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .app-grid { display: grid; grid-template-columns: 320px 1fr; height: 100dvh; }
        @media (max-width: 1024px) { .app-grid { grid-template-columns: 1fr; } }

        /* Sidebar */
        .sidebar { background: #070b14; border-right: 1px solid rgba(255,255,255,0.05); transition: 0.4s ease; z-index: 2000; }
        @media (max-width: 1024px) { .sidebar { position: fixed; inset: 0; transform: translateX(-100%); width: 100%; } .sidebar.active { transform: translateX(0); } }

        /* Chat Layout - Input fix */
        .chat-main { display: flex; flex-direction: column; height: 100dvh; position: relative; }
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; scroll-behavior: smooth; padding-bottom: 100px; }
        
        /* Fixed Footer */
        .footer-fixed { position: absolute; bottom: 0; width: 100%; background: #070b14; border-top: 1px solid rgba(255,255,255,0.1); padding: 12px 15px 25px 15px; z-index: 1000; }
        .input-bar { background: rgba(255,255,255,0.05); border-radius: 25px; border: 1px solid rgba(255,255,255,0.15); padding: 4px 12px; }

        /* Bubbles */
        .msg-wrap { position: relative; max-width: 85%; display: flex; flex-direction: column; }
        .mine { align-self: flex-end; align-items: flex-end; }
        .other { align-self: flex-start; align-items: flex-start; }
        .bubble { padding: 12px 18px; border-radius: 22px; font-size: 14px; border: 1px solid rgba(255,255,255,0.05); }
        .mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); border-bottom-right-radius: 4px; box-shadow: 0 4px 15px rgba(168, 85, 247, 0.2); }
        .other .bubble { background: #1e293b; border-bottom-left-radius: 4px; }

        /* Actions Menu */
        .actions { position: absolute; top: -35px; background: #1e293b; border-radius: 10px; padding: 5px 12px; display: none; gap: 10px; border: 1px solid rgba(255,255,255,0.1); z-index: 50; }
        .msg-wrap:hover .actions { display: flex; }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: #a855f7; border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth-scr" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#020617] p-6 text-center">
        <div class="glass p-12 rounded-[3.5rem] w-full max-w-sm">
            <h1 class="text-3xl font-black mb-8 text-purple-500 tracking-tighter italic">NEON SYNC</h1>
            <input id="u-in" type="text" placeholder="Your Name" class="w-full bg-white/5 border border-white/10 p-5 rounded-2xl text-white outline-none mb-4 focus:border-purple-500 font-bold">
            <button onclick="login()" class="w-full bg-purple-600 py-5 rounded-2xl font-black shadow-xl active:scale-95 transition-all">ENTER SPACE</button>
        </div>
    </div>

    <div class="app-grid">
        <aside id="side" class="sidebar flex flex-col p-6">
            <div class="flex justify-between items-center mb-8">
                <span class="text-2xl font-black text-purple-400">NEON.PRO</span>
                <button onclick="logout()" class="w-10 h-10 rounded-xl bg-red-500/10 text-red-500 border border-red-500/20"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto space-y-3">
                <div onclick="openChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-2xl cursor-pointer font-bold flex items-center gap-3">
                    <i class="fa-solid fa-hashtag text-purple-400"></i> global-hq
                </div>
                <div id="u-list" class="space-y-2 pt-4"></div>
            </div>

            <div class="pt-6 border-t border-white/5 flex items-center gap-4">
                <div id="my-av" class="w-12 h-12 rounded-2xl bg-purple-600 flex items-center justify-center font-bold text-xl shadow-lg shadow-purple-600/20">?</div>
                <div><p id="my-n" class="font-bold text-sm">User</p><p class="text-[9px] text-green-500 font-bold">● ONLINE</p></div>
            </div>
        </aside>

        <main class="chat-main">
            <header class="h-20 flex items-center justify-between px-6 glass border-b border-white/5 z-50">
                <div class="flex items-center gap-4">
                    <button class="lg:hidden w-10 h-10 bg-white/5 rounded-xl text-purple-400 flex items-center justify-center" onclick="toggleSide()"><i class="fa-solid fa-chevron-left"></i></button>
                    <h2 id="chat-h" class="text-lg font-black tracking-tight">Select Chat</h2>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div id="input-area" class="footer-fixed hidden">
                <div id="reply-box" class="hidden mb-2 p-2 bg-purple-600/10 rounded-xl flex justify-between items-center text-[10px] text-purple-300">
                    <span id="reply-txt"></span>
                    <button onclick="cancelReply()"><i class="fa-solid fa-xmark"></i></button>
                </div>
                <form id="m-form" class="input-bar flex items-center gap-3">
                    <input type="file" id="f-img" class="hidden" accept="image/*" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('f-img').click()" class="text-slate-400 hover:text-purple-400"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="m-in" type="text" placeholder="Write a message..." class="bg-transparent flex-1 outline-none text-sm p-3 font-medium text-white" autocomplete="off">
                    <button class="bg-purple-600 w-12 h-12 rounded-2xl flex items-center justify-center shadow-lg active:scale-90 transition-transform"><i class="fa-solid fa-paper-plane text-[10px]"></i></button>
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
            document.getElementById('auth-scr').style.display = 'none';
            document.getElementById('my-n').innerText = myN;
            document.getElementById('my-av').innerText = myN[0].toUpperCase();
            set(ref(db, 'online/' + myN), true);
            loadUsers();
        }

        window.toggleSide = () => document.getElementById('side').classList.toggle('active');

        window.openChat = (t) => {
            active = t;
            document.getElementById('input-area').classList.remove('hidden');
            document.getElementById('chat-h').innerText = t === 'global' ? '# global-hq' : `@ ${t}`;
            if(window.innerWidth < 1024) toggleSide();
            loadMsgs();
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('u-list'); list.innerHTML = "";
                s.forEach(u => {
                    if(u.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "p-4 glass rounded-2xl cursor-pointer hover:bg-purple-600/10 transition flex items-center gap-3 mb-2";
                        d.innerHTML = `<div class="w-2 h-2 rounded-full bg-green-500 shadow-[0_0_5px_#22c55e]"></div> <span class="font-bold text-sm uppercase">${u.key}</span>`;
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
                        rx += `<span onclick="react('${id}','${e}')" class="bg-white/5 px-2 py-1 rounded-lg text-[10px] cursor-pointer border border-white/10">${e} ${c}</span>`;
                    });

                    wrap.innerHTML = `
                        <div class="actions">
                            <button onclick="doReply('${d.sender}','${d.text||"Img"}')" class="text-[10px] font-bold hover:text-purple-400">REPLY</button>
                            <button onclick="react('${id}','👍')" class="text-xs">👍</button>
                            ${isM ? `<button onclick="del('${id}')" class="text-[10px] text-red-500 font-bold ml-2">DELETE</button>` : ''}
                        </div>
                        <span class="text-[9px] text-slate-500 font-bold mb-1 px-3 uppercase tracking-tighter">${d.sender}</span>
                        ${d.reply ? `<div class="text-[9px] bg-white/5 p-2 rounded-xl mb-1 border-l-2 border-purple-500 italic opacity-60">Replying to ${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble">${d.img ? `<img src="${d.img}" class="rounded-xl max-w-full shadow-lg">` : d.text}</div>
                        <div class="flex gap-1 mt-2">${rx}</div>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.upImg = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `media/${Date.now()}`);
            const snap = await uploadBytes(r, f);
            const url = await getDownloadURL(snap.ref);
            const p = active === 'global' ? 'msgs/global' : `private/${[myN, active].sort().join('_')}`;
            push(ref(db, p), { sender: myN, img: url, time: Date.now() });
        };

        window.doReply = (u, m) => {
            reply = { to: u, msg: m };
            document.getElementById('reply-box').classList.remove('hidden');
            document.getElementById('reply-txt').innerText = `Reply to ${u}`;
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
