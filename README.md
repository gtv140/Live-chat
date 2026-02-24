<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Ultra | Final Master</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap');
        :root { --neon-p: #a855f7; --bg: #020617; }
        body, html { height: 100%; margin: 0; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; overflow: hidden; }
        .app-shell { display: flex; height: 100dvh; width: 100%; overflow: hidden; }
        
        /* Sidebar Responsive */
        .sidebar { width: 300px; background: rgba(15, 23, 42, 0.98); border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100; transition: 0.3s; }
        @media (max-width: 768px) { .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; } .sidebar.active { left: 0; box-shadow: 20px 0 50px black; } }

        /* Chat Layout */
        .chat-view { flex: 1; display: flex; flex-direction: column; position: relative; background: radial-gradient(circle at top right, #1e1b4b, #020617); }
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 140px; display: flex; flex-direction: column; gap: 12px; }
        
        /* Bubbles */
        .msg-wrap { display: flex; flex-direction: column; max-width: 80%; position: relative; margin-bottom: 5px; group; }
        .msg-mine { align-self: flex-end; align-items: flex-end; }
        .msg-other { align-self: flex-start; align-items: flex-start; }
        .bubble { padding: 10px 15px; border-radius: 18px; font-size: 14px; position: relative; border: 1px solid rgba(255,255,255,0.05); }
        .msg-mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); border-bottom-right-radius: 4px; box-shadow: 0 4px 15px rgba(168, 85, 247, 0.2); }
        .msg-other .bubble { background: rgba(255,255,255,0.1); border-bottom-left-radius: 4px; }

        /* Action Buttons */
        .actions { position: absolute; top: -25px; background: #1e293b; border-radius: 8px; padding: 2px 8px; display: none; gap: 10px; font-size: 10px; border: 1px solid rgba(255,255,255,0.1); z-index: 50; }
        .msg-wrap:hover .actions { display: flex; }

        /* Input Bar */
        .input-bar { display: none; position: fixed; bottom: 20px; left: 320px; right: 20px; background: rgba(15, 23, 42, 0.9); backdrop-filter: blur(20px); border-radius: 20px; border: 1px solid var(--neon-p); padding: 10px 20px; z-index: 1000; }
        @media (max-width: 768px) { .input-bar { left: 15px; right: 15px; } }
        
        .reply-preview { display: none; background: rgba(168, 85, 247, 0.2); padding: 8px 12px; border-radius: 12px; font-size: 11px; margin-bottom: 8px; justify-content: space-between; align-items: center; border-left: 3px solid #a855f7; }
        
        #dash { position: absolute; inset: 0; background: var(--bg); z-index: 50; padding: 40px; display: flex; flex-direction: column; justify-content: center; align-items: center; text-align: center; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[3000] bg-black flex items-center justify-center p-6">
        <div class="bg-slate-900 border border-purple-500/30 p-10 rounded-[2.5rem] w-full max-w-sm text-center">
            <div class="w-16 h-16 bg-purple-600 rounded-2xl mx-auto mb-6 flex items-center justify-center text-white text-3xl"><i class="fa-solid fa-bolt"></i></div>
            <h2 class="text-3xl font-bold mb-8 tracking-tighter">Neon<span class="text-purple-500">Pro</span></h2>
            <input id="u-input" type="text" placeholder="Your Username" class="w-full p-4 bg-slate-800 rounded-xl mb-4 text-white text-center outline-none border border-transparent focus:border-purple-500 transition-all">
            <button id="join-btn" class="w-full bg-purple-600 py-4 rounded-xl font-bold shadow-lg hover:bg-purple-500 transition-all">START SESSION</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 flex justify-between items-center">
                <span class="font-extrabold text-xl tracking-tighter">WORKSPACE</span>
                <button onclick="window.logoutApp()" class="text-slate-500 hover:text-red-500 transition-colors"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="px-4 mb-6">
                <div class="bg-white/5 rounded-xl flex items-center px-4 py-2 border border-white/5">
                    <i class="fa-solid fa-magnifying-glass text-xs text-slate-500 mr-2"></i>
                    <input type="text" id="user-search" oninput="window.filterUsers()" placeholder="Search team..." class="bg-transparent text-xs text-white outline-none w-full">
                </div>
            </div>

            <div class="flex-1 overflow-y-auto px-4">
                <div onclick="window.switchChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-2xl cursor-pointer font-bold mb-6 text-purple-300 hover:bg-purple-600/20 transition">
                    <i class="fa-solid fa-hashtag mr-2"></i> global-hq
                </div>
                <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest mb-4 px-2">Personnel</p>
                <div id="users-list" class="space-y-1"></div>
            </div>

            <div class="p-6 border-t border-white/5 flex items-center gap-3 bg-black/20">
                <div id="u-avatar" class="w-10 h-10 bg-gradient-to-tr from-purple-600 to-blue-500 rounded-xl flex items-center justify-center font-bold text-white shadow-lg">?</div>
                <div><p id="u-name" class="text-xs font-bold"></p><p class="text-[10px] text-green-500 font-bold">● Active Now</p></div>
            </div>
        </aside>

        <main class="chat-view">
            <div id="dash">
                <h1 class="text-5xl font-extrabold tracking-tighter mb-4">Welcome, <span id="dash-user" class="text-purple-500">User</span></h1>
                <p class="text-slate-500 mb-10 max-w-sm">NeonPro is ready for secure communication. Select a contact from the sidebar to begin.</p>
                <div class="grid grid-cols-2 gap-6 w-full max-w-md">
                    <div class="p-6 bg-white/5 rounded-[2rem] border border-white/5"><h3 id="stat-online" class="text-3xl font-bold">0</h3><p class="text-[10px] text-slate-500 uppercase font-bold mt-2">Team Online</p></div>
                    <div class="p-6 bg-white/5 rounded-[2rem] border border-white/5"><h3 class="text-3xl font-bold text-cyan-400">Stable</h3><p class="text-[10px] text-slate-500 uppercase font-bold mt-2">Network</p></div>
                </div>
            </div>

            <header class="h-20 border-b border-white/5 flex items-center justify-between px-6 bg-slate-900/40 backdrop-blur-xl">
                <div class="flex items-center gap-4">
                    <button onclick="document.getElementById('sidebar').classList.toggle('active')" class="md:hidden text-white"><i class="fa-solid fa-bars-staggered"></i></button>
                    <h2 id="chat-title" class="font-bold text-lg">Main Console</h2>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-bar" id="input-container">
                <div id="reply-box" class="reply-preview">
                    <span id="reply-text" class="truncate">Replying...</span>
                    <button onclick="window.cancelReply()"><i class="fa-solid fa-xmark"></i></button>
                </div>
                <form id="msg-form" class="flex items-center gap-4">
                    <input type="file" id="f-input" class="hidden" accept="image/*" onchange="window.upFile(this)">
                    <button type="button" onclick="document.getElementById('f-input').click()" class="text-slate-400 hover:text-cyan-400 transition-colors"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Type a message..." class="bg-transparent text-white w-full outline-none font-medium text-sm">
                    <button class="bg-purple-600 px-6 py-2 rounded-xl font-bold shadow-lg text-sm transition-all active:scale-95">SEND</button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        const firebaseConfig = {
            apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
            authDomain: "live-chat-b810c.firebaseapp.com",
            databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
            projectId: "live-chat-b810c",
            storageBucket: "live-chat-b810c.firebasestorage.app",
            messagingSenderId: "555058795334",
            appId: "1:555058795334:web:f668887409800c32970b47"
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const st = getStorage(app);
        
        let myN = ""; 
        let activeC = null;
        let replyTo = null;

        // Start Logic
        window.onload = () => {
            const saved = localStorage.getItem('neonUser');
            if(saved) startApp(saved);
        };

        document.getElementById('join-btn').onclick = () => {
            const val = document.getElementById('u-input').value.trim();
            if(val) {
                localStorage.setItem('neonUser', val);
                startApp(val);
            }
        };

        function startApp(n) {
            myN = n;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('u-name').innerText = n;
            document.getElementById('dash-user').innerText = n;
            document.getElementById('u-avatar').innerText = n[0].toUpperCase();
            set(ref(db, 'online/' + n), true);
            loadUsers();
        }

        window.switchChat = (u) => {
            activeC = u;
            document.getElementById('dash').style.display = 'none';
            document.getElementById('input-container').style.display = 'block';
            document.getElementById('chat-title').innerText = u === 'global' ? '# global-hq' : `@ ${u}`;
            loadMsgs();
            if(window.innerWidth < 768) document.getElementById('sidebar').classList.remove('active');
        };

        function loadMsgs() {
            const path = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
            onValue(ref(db, path), (s) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isM = d.sender === myN;
                    const wrap = document.createElement('div');
                    wrap.className = `msg-wrap ${isM ? 'msg-mine' : 'msg-other'}`;
                    
                    let body = d.text || "";
                    if(d.img) body = `<img src="${d.img}" class="rounded-xl max-w-full shadow-lg">`;

                    wrap.innerHTML = `
                        <div class="actions">
                            <button onclick="window.setReply('${d.sender}', '${d.text || "Image"}')" class="text-cyan-400">Reply</button>
                            <button onclick="window.delMsg('${id}')" class="text-red-400">Delete</button>
                        </div>
                        <span class="text-[9px] text-slate-500 px-2 font-bold uppercase">${d.sender}</span>
                        ${d.reply ? `<div class="text-[10px] bg-white/5 p-2 rounded-lg mb-1 border-l-2 border-purple-500 italic text-slate-400">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble">${body}</div>
                        <span class="text-[8px] text-slate-600 px-2 mt-1">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setReply = (to, msg) => {
            replyTo = {to, msg};
            document.getElementById('reply-box').style.display = 'flex';
            document.getElementById('reply-text').innerText = `Replying to @${to}: ${msg}`;
            document.getElementById('msg-input').focus();
        };

        window.cancelReply = () => {
            replyTo = null;
            document.getElementById('reply-box').style.display = 'none';
        };

        window.delMsg = (id) => {
            if(confirm("Delete message for everyone?")) {
                const path = (activeC === 'global' ? 'msgs/global/' : `private/${[myN, activeC].sort().join('_')}/`) + id;
                remove(ref(db, path));
            }
        };

        window.upFile = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `neon_files/${Date.now()}_${f.name}`);
            await uploadBytes(r, f);
            const url = await getDownloadURL(r);
            const path = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
            push(ref(db, path), { sender: myN, img: url, time: Date.now() });
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                let count = 0; list.innerHTML = "";
                snap.forEach(c => {
                    count++;
                    if(c.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "user-item p-3 bg-white/5 rounded-2xl cursor-pointer hover:bg-white/10 flex items-center gap-3 transition-all";
                        d.innerHTML = `<div class="w-8 h-8 rounded-lg bg-slate-800 flex items-center justify-center font-bold text-xs">${c.key[0].toUpperCase()}</div> 
                                       <div class="flex-1"><p class="text-xs font-bold">${c.key}</p><p class="text-[9px] text-green-500">Online</p></div>`;
                        d.onclick = () => window.switchChat(c.key);
                        list.appendChild(d);
                    }
                });
                document.getElementById('stat-online').innerText = count;
            });
        }

        window.filterUsers = () => {
            const query = document.getElementById('user-search').value.toLowerCase();
            document.querySelectorAll('.user-item').forEach(item => {
                item.style.display = item.innerText.toLowerCase().includes(query) ? 'flex' : 'none';
            });
        };

        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim() && activeC) {
                const path = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
                const data = { sender: myN, text: i.value.trim(), time: Date.now() };
                if(replyTo) { data.reply = replyTo; window.cancelReply(); }
                push(ref(db, path), data);
                i.value = "";
            }
        };

        window.logoutApp = () => {
            if(confirm("Terminate session?")) {
                remove(ref(db, 'online/' + myN));
                localStorage.clear();
                location.reload();
            }
        };
    </script>
</body>
</html>
