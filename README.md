<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro | Ultra Master</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        :root { --neon-p: #a855f7; --neon-c: #22d3ee; }
        body, html { height: 100%; margin: 0; padding: 0; overflow: hidden; background: #020617; font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; }
        .app-shell { display: flex; height: 100dvh; width: 100%; }
        
        /* Sidebar Design */
        .sidebar { width: 300px; background: rgba(15, 23, 42, 0.98); border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100; transition: 0.3s; }
        @media (max-width: 768px) { .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; } .sidebar.active { left: 0; } }

        /* Chat Window */
        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); position: relative; }
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 120px; display: flex; flex-direction: column; gap: 15px; scroll-behavior: smooth; }

        /* Input Dock Fixed to Bottom */
        .input-dock { position: absolute; bottom: 20px; left: 15px; right: 15px; background: rgba(15, 23, 42, 0.9); backdrop-filter: blur(20px); border-radius: 30px; padding: 10px 20px; border: 2px solid var(--neon-p); box-shadow: 0 0 30px rgba(168, 85, 247, 0.3); z-index: 50; }
        input#msg-input { background: transparent; border: none; color: white; flex: 1; outline: none; font-size: 16px; padding: 10px; }

        /* Message Bubble */
        .msg-card { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); padding: 14px; border-radius: 20px; max-width: 85%; align-self: flex-start; position: relative; }
        .msg-card.mine { align-self: flex-end; border-color: var(--neon-p); background: rgba(168, 85, 247, 0.1); }
        
        .status-on { width: 10px; height: 10px; background: #22c55e; border-radius: 50%; box-shadow: 0 0 10px #22c55e; }
        #back-btn { display: none; } #back-btn.show { display: flex; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[200] bg-[#020617] flex items-center justify-center p-6">
        <div class="bg-slate-900 border border-purple-500/30 p-10 rounded-[3rem] w-full max-w-sm text-center">
            <h1 class="text-3xl font-bold mb-6 text-white">NeonChat Pro</h1>
            <input id="u-input" type="text" placeholder="Username" class="w-full p-4 bg-slate-800 rounded-2xl mb-4 text-white text-center outline-none">
            <input id="a-key" type="password" placeholder="Admin Key (Optional)" class="w-full p-4 bg-slate-800 rounded-2xl mb-6 text-white text-center outline-none">
            <button id="join-btn" class="w-full bg-purple-600 text-white font-bold py-4 rounded-2xl shadow-xl">Join Workspace</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-8 border-b border-white/5 font-bold text-xl text-white">NEON HQ</div>
            <div class="flex-1 overflow-y-auto p-4 space-y-4">
                <div onclick="switchChat('global')" class="p-4 bg-purple-600/20 text-purple-300 rounded-xl font-bold text-sm cursor-pointer border border-purple-500/20">
                    <i class="fa-solid fa-globe mr-2"></i> # Global Chat
                </div>
                <p class="text-[10px] text-slate-500 font-bold uppercase ml-2 mt-4">Direct Messages</p>
                <div id="users-list" class="space-y-2"></div>
            </div>
            <div id="admin-panel" class="hidden p-4 bg-red-950/20 border-t border-red-500/20">
                <button onclick="wipe()" class="w-full py-2 bg-red-600 text-white rounded-lg text-xs font-bold">WIPE CHAT</button>
            </div>
            <div class="p-5 border-t border-white/5 flex items-center justify-between">
                <span id="my-name" class="text-xs font-bold text-slate-400"></span>
                <button onclick="logout()" class="text-red-400"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </aside>

        <main class="chat-view">
            <header class="h-20 border-b border-white/5 flex items-center px-6 bg-slate-900/40">
                <button id="back-btn" onclick="switchChat('global')" class="bg-white/10 w-10 h-10 rounded-full items-center justify-center text-white mr-3">
                    <i class="fa-solid fa-arrow-left"></i>
                </button>
                <button onclick="toggleSide()" class="md:hidden text-white mr-3"><i class="fa-solid fa-bars-staggered"></i></button>
                <h2 id="chat-title" class="text-lg font-bold text-white"># Global Chat</h2>
            </header>

            <div id="chat-flow"></div>

            <div class="input-dock">
                <form id="msg-form" class="flex items-center gap-4">
                    <input type="file" id="f-up" class="hidden" onchange="upF(this)">
                    <button type="button" onclick="document.getElementById('f-up').click()" class="text-slate-400"><i class="fa-solid fa-camera text-2xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Type a message..." autocomplete="off">
                    <button class="w-10 h-10 bg-purple-600 rounded-full flex items-center justify-center text-white"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove, update, increment, onDisconnect } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        const fbCfg = {
            apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
            authDomain: "live-chat-b810c.firebaseapp.com",
            databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
            projectId: "live-chat-b810c",
            storageBucket: "live-chat-b810c.firebasestorage.app",
            messagingSenderId: "555058795334",
            appId: "1:555058795334:web:f668887409800c32970b47"
        };

        const app = initializeApp(fbCfg);
        const db = getDatabase(app);
        const st = getStorage(app);
        
        let uName = localStorage.getItem('nU') || "", isAdm = localStorage.getItem('nA') === 'true', activeC = localStorage.getItem('lC') || "global";

        if(uName) { document.getElementById('login-screen').style.display = 'none'; start(); }

        document.getElementById('join-btn').onclick = () => {
            const n = document.getElementById('u-input').value.trim();
            const p = document.getElementById('a-key').value.trim();
            if(n){
                uName = n; localStorage.setItem('nU', n);
                if(p === "SECRET_BOSS") { isAdm = true; localStorage.setItem('nA', 'true'); }
                document.getElementById('login-screen').style.display = 'none';
                start();
            }
        };

        function start() {
            document.getElementById('my-name').innerText = uName;
            if(isAdm) document.getElementById('admin-panel').classList.remove('hidden');
            const oRef = ref(db, 'online/' + uName);
            set(oRef, true); onDisconnect(oRef).remove();
            switchChat(activeC);
            loadUsers();
        }

        window.switchChat = (t) => {
            activeC = t; localStorage.setItem('lC', t);
            document.getElementById('chat-title').innerText = t === 'global' ? '# Global Chat' : '@ ' + t;
            document.getElementById('back-btn').className = t === 'global' ? '' : 'show';
            loadMsgs();
        };

        function loadMsgs() {
            const p = activeC === 'global' ? 'msgs/global' : `private/${[uName, activeC].sort().join('_')}`;
            onValue(ref(db, p), (s) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isM = d.sender === uName;
                    const div = document.createElement('div');
                    div.className = `msg-card ${isM ? 'mine' : ''}`;
                    div.innerHTML = `
                        <p class="text-[10px] font-bold ${d.adm ? 'text-yellow-400' : 'text-cyan-400'}">${d.sender}</p>
                        <div class="text-sm my-1">${d.text || (d.img ? `<img src="${d.img}" class="rounded-xl">` : '')}</div>
                        ${isAdm ? `<button onclick="del('${id}')" class="text-[9px] text-red-500 font-bold underline">Delete</button>` : ''}
                    `;
                    flow.appendChild(div);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.del = (id) => remove(ref(db, `${activeC==='global'?'msgs/global':`private/${[uName,activeC].sort().join('_')}`}/${id}`));
        window.wipe = () => { if(confirm("Clear this chat?")) remove(ref(db, activeC==='global'?'msgs/global':`private/${[uName,activeC].sort().join('_')}`)); };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                s.forEach(u => {
                    if(u.key !== uName) {
                        const d = document.createElement('div');
                        d.className = "flex items-center justify-between p-3 bg-white/5 rounded-xl cursor-pointer";
                        d.innerHTML = `<span class="text-xs font-bold text-white">${u.key}</span><div class="status-on"></div>`;
                        d.onclick = () => switchChat(u.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault(); const i = document.getElementById('msg-input');
            const p = activeC === 'global' ? 'msgs/global' : `private/${[uName, activeC].sort().join('_')}`;
            if(i.value.trim()){ push(ref(db, p), { sender: uName, text: i.value, time: Date.now(), adm: isAdm }); i.value = ""; }
        };

        window.upF = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `media/${Date.now()}`);
            const p = activeC === 'global' ? 'msgs/global' : `private/${[uName, activeC].sort().join('_')}`;
            await uploadBytes(r, f); const url = await getDownloadURL(r);
            push(ref(db, p), { sender: uName, img: url, time: Date.now(), adm: isAdm });
        };

        window.logout = () => { localStorage.clear(); location.reload(); };
        window.toggleSide = () => document.getElementById('sidebar').classList.toggle('active');
    </script>
</body>
</html>
