<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro OS | Slack-Skype Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap');
        :root { --neon-p: #a855f7; --neon-c: #22d3ee; --bg: #020617; }
        body, html { height: 100%; margin: 0; padding: 0; overflow: hidden; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; }
        .app-shell { display: flex; height: 100dvh; width: 100%; overflow: hidden; }
        
        /* Sidebar */
        .sidebar { width: 320px; background: rgba(15, 23, 42, 0.95); border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100; transition: 0.3s; }
        @media (max-width: 768px) { .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; } .sidebar.active { left: 0; } }

        /* Chat View */
        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); position: relative; }
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 220px !important; display: flex; flex-direction: column; gap: 10px; }

        /* Skype Style Bubbles */
        .msg-wrap { display: flex; flex-direction: column; max-width: 80%; position: relative; group; }
        .msg-mine { align-self: flex-end; align-items: flex-end; }
        .msg-other { align-self: flex-start; align-items: flex-start; }
        
        .bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; transition: 0.2s; border: 1px solid rgba(255,255,255,0.05); }
        .msg-mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); color: white; border-bottom-right-radius: 4px; }
        .msg-other .bubble { background: rgba(255,255,255,0.05); border-bottom-left-radius: 4px; }
        
        /* Slack Style Actions */
        .actions { position: absolute; top: -20px; right: 0; display: none; background: #1e293b; border-radius: 8px; padding: 4px; border: 1px solid rgba(255,255,255,0.1); gap: 10px; z-index: 10; }
        .msg-wrap:hover .actions { display: flex; }

        /* Dashboard */
        #dash { position: absolute; inset: 0; background: var(--bg); z-index: 50; padding: 40px; display: flex; flex-direction: column; overflow-y: auto; }
        .card { background: rgba(255,255,255,0.02); border: 1px solid rgba(255,255,255,0.05); border-radius: 24px; padding: 20px; backdrop-filter: blur(10px); }

        /* Input Bar */
        .input-bar { display: none; position: fixed; bottom: 25px; left: 340px; right: 25px; background: rgba(15, 23, 42, 0.9); backdrop-filter: blur(20px); border-radius: 24px; border: 2px solid var(--neon-p); padding: 10px 20px; z-index: 1000; box-shadow: 0 10px 40px rgba(0,0,0,0.5); }
        @media (max-width: 768px) { .input-bar { left: 15px; right: 15px; } }

        .status-dot { width: 10px; height: 10px; border-radius: 50%; border: 2px solid #0f172a; }
        .online { background: #22c55e; box-shadow: 0 0 10px #22c55e; }
        .reply-preview { display: none; background: rgba(168, 85, 247, 0.2); padding: 10px; border-radius: 12px; font-size: 12px; margin-bottom: 10px; justify-content: space-between; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[2000] bg-black flex items-center justify-center p-6">
        <div class="bg-slate-900 border border-purple-500/30 p-10 rounded-[2.5rem] w-full max-w-sm text-center">
            <h2 class="text-3xl font-bold text-white mb-6 tracking-tighter">Neon<span class="text-purple-500">Pro</span></h2>
            <input id="u-input" type="text" placeholder="Username" class="w-full p-4 bg-slate-800 rounded-2xl mb-4 text-white text-center outline-none border border-transparent focus:border-purple-500 transition">
            <button id="join-btn" class="w-full bg-purple-600 text-white font-bold py-4 rounded-2xl shadow-xl hover:bg-purple-500 transition">LAUNCH</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-6">
                <div class="flex items-center justify-between mb-6">
                    <span class="text-xl font-bold">WORKSPACE</span>
                    <button onclick="logout()" class="text-slate-500"><i class="fa-solid fa-power-off"></i></button>
                </div>
                <div class="bg-white/5 rounded-xl flex items-center px-4 py-2 border border-white/5">
                    <i class="fa-solid fa-magnifying-glass text-xs text-slate-500 mr-2"></i>
                    <input type="text" id="user-search" placeholder="Search team..." class="bg-transparent text-xs text-white outline-none w-full">
                </div>
            </div>

            <div class="flex-1 overflow-y-auto px-4 space-y-4">
                <div onclick="switchChat('global')" class="p-3 bg-purple-500/10 border border-purple-500/20 text-purple-300 rounded-xl cursor-pointer font-bold text-sm flex items-center gap-3">
                    <i class="fa-solid fa-hashtag"></i> global-hq
                </div>
                <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest ml-2">Direct Messages</p>
                <div id="users-list" class="space-y-1"></div>
            </div>

            <div class="p-6 border-t border-white/5 bg-black/20">
                <div class="flex items-center gap-3">
                    <div id="u-avatar" class="w-10 h-10 rounded-xl bg-purple-600 flex items-center justify-center font-bold">?</div>
                    <div><p id="u-name" class="text-xs font-bold">User</p><p class="text-[10px] text-green-500 font-bold">● Active</p></div>
                </div>
            </div>
        </aside>

        <main class="chat-view">
            <div id="dash">
                <h1 class="text-4xl font-bold mb-2 tracking-tighter">Enterprise <span class="text-purple-500">Dashboard</span></h1>
                <p class="text-slate-500 mb-10">Select a channel to begin secure communication.</p>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <div class="card"><p class="text-slate-500 text-[10px] font-bold uppercase mb-2">Users Online</p><h2 id="stat-online" class="text-3xl font-bold text-cyan-400">0</h2></div>
                    <div class="card"><p class="text-slate-500 text-[10px] font-bold uppercase mb-2">Network Status</p><h2 class="text-3xl font-bold text-green-500">Optimal</h2></div>
                    <div class="card"><p class="text-slate-500 text-[10px] font-bold uppercase mb-2">System Version</p><h2 class="text-3xl font-bold">v4.0.2</h2></div>
                </div>
                <div class="mt-8 card flex-1">
                    <h3 class="text-sm font-bold mb-4">Quick Links</h3>
                    <div class="space-y-3">
                        <div class="p-3 bg-white/5 rounded-xl text-xs hover:bg-white/10 cursor-pointer"><i class="fa-solid fa-gear mr-2"></i> Open Workspace Settings</div>
                        <div class="p-3 bg-white/5 rounded-xl text-xs hover:bg-white/10 cursor-pointer"><i class="fa-solid fa-circle-info mr-2"></i> View About & License</div>
                        <div class="p-3 bg-white/5 rounded-xl text-xs hover:bg-white/10 cursor-pointer"><i class="fa-solid fa-headset mr-2"></i> Contact Enterprise Support</div>
                    </div>
                </div>
            </div>

            <header class="h-20 border-b border-white/5 flex items-center justify-between px-6 bg-slate-900/40 backdrop-blur-xl">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-white"><i class="fa-solid fa-bars"></i></button>
                    <h2 id="chat-title" class="text-lg font-bold">Main Console</h2>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-bar" id="input-container">
                <div id="reply-box" class="reply-preview"><span>Replying...</span><button onclick="cancelReply()"><i class="fa-solid fa-xmark"></i></button></div>
                <form id="msg-form" class="flex items-center gap-4">
                    <input type="file" id="f-input" class="hidden" accept="image/*" onchange="upFile(this)">
                    <button type="button" onclick="document.getElementById('f-input').click()" class="text-slate-400 hover:text-cyan-400"><i class="fa-solid fa-camera text-xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Message..." class="bg-transparent text-white w-full outline-none">
                    <button class="bg-purple-600 px-5 py-2 rounded-xl font-bold shadow-glow">Send</button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove, update } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let myN = "", activeC = null, replyTo = null;

        window.onload = () => { if(localStorage.getItem('chatUser')) init(localStorage.getItem('chatUser')); };
        document.getElementById('join-btn').onclick = () => { 
            const n = document.getElementById('u-input').value.trim(); 
            if(n){ localStorage.setItem('chatUser', n); init(n); }
        };

        function init(n) {
            myN = n;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('u-name').innerText = n;
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
            if(window.innerWidth < 768) toggleSidebar(); 
        };

        function loadMsgs() {
            const p = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
            onValue(ref(db, p), (s) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isM = d.sender === myN;
                    const div = document.createElement('div');
                    div.className = `msg-wrap ${isM ? 'msg-mine' : 'msg-other'}`;
                    
                    let body = d.text || "";
                    if(d.img) body = `<img src="${d.img}" class="rounded-xl max-w-full">`;
                    
                    div.innerHTML = `
                        <div class="actions">
                            <button onclick="setReply('${d.sender}', '${d.text || "Image"}')" class="text-white hover:text-cyan-400 text-xs px-2">Reply</button>
                            <button onclick="delMsg('${id}')" class="text-red-500 hover:text-red-400 text-xs px-2">Delete</button>
                        </div>
                        <span class="text-[9px] font-bold text-slate-500 uppercase mb-1 px-2">${d.sender}</span>
                        ${d.reply ? `<div class="text-[10px] bg-white/10 p-2 rounded-lg mb-1 border-l-2 border-purple-500 italic text-slate-400">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble">${body}</div>
                        <span class="text-[8px] text-slate-600 mt-1 px-2">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
                    `;
                    flow.appendChild(div);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setReply = (to, msg) => { replyTo = {to, msg}; document.getElementById('reply-box').style.display = 'flex'; document.getElementById('reply-box').querySelector('span').innerText = `Replying to @${to}...`; };
        window.cancelReply = () => { replyTo = null; document.getElementById('reply-box').style.display = 'none'; };
        window.delMsg = (id) => { if(confirm("Delete message?")) remove(ref(db, (activeC === 'global' ? 'msgs/global/' : `private/${[myN, activeC].sort().join('_')}/`) + id)); };

        window.upFile = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `media/${Date.now()}`);
            await uploadBytes(r, f);
            const url = await getDownloadURL(r);
            push(ref(db, activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`), { sender: myN, img: url, time: Date.now() });
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                let count = 0; list.innerHTML = "";
                snap.forEach(c => {
                    count++;
                    if(c.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "flex items-center gap-3 p-3 rounded-xl cursor-pointer hover:bg-white/5 transition";
                        d.innerHTML = `<div class="relative"><div class="w-10 h-10 rounded-xl bg-slate-800 flex items-center justify-center font-bold text-xs">${c.key[0].toUpperCase()}</div><div class="status-dot online absolute -bottom-1 -right-1"></div></div><div class="flex-1"><p class="text-xs font-bold">${c.key}</p><p class="text-[9px] text-green-500">Active Now</p></div>`;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
                document.getElementById('stat-online').innerText = count;
            });
        }

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault(); const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                const d = { sender: myN, text: i.value, time: Date.now() };
                if(replyTo) { d.reply = replyTo; cancelReply(); }
                push(ref(db, activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`), d);
                i.value = ""; 
            }
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
        window.logout = () => { localStorage.clear(); location.reload(); };
    </script>
</body>
</html>
