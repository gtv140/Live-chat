<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro | Ultra Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        :root { --neon-purple: #a855f7; --neon-cyan: #22d3ee; --neon-green: #4ade80; }
        
        body, html { height: 100%; margin: 0; padding: 0; overflow: hidden; background: #020617; font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; }
        
        /* Layout Structure */
        .app-shell { display: flex; height: 100dvh; width: 100%; position: relative; }
        
        /* Modern Sidebar */
        .sidebar { width: 300px; background: rgba(15, 23, 42, 0.98); border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100; transition: 0.3s; }

        /* Chat Window */
        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); position: relative; }
        
        /* Message Flow Fix */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 240px !important; display: flex; flex-direction: column; gap: 16px; scroll-behavior: smooth; }

        /* Neon Bubbles */
        .msg-card { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); padding: 14px; border-radius: 22px; max-width: 80%; align-self: flex-start; transition: 0.3s; position: relative; box-shadow: 0 10px 20px rgba(0,0,0,0.2); }
        .msg-card.mine { align-self: flex-end; border-color: var(--neon-purple); background: rgba(168, 85, 247, 0.15); box-shadow: 0 0 20px rgba(168, 85, 247, 0.2); }

        /* Advanced Reactions */
        .reaction-bar { display: flex; gap: 8px; margin-top: 10px; }
        .react-p { background: rgba(255,255,255,0.08); padding: 4px 10px; border-radius: 12px; font-size: 13px; cursor: pointer; transition: 0.2s; border: 1px solid transparent; }
        .react-p:hover { border-color: var(--neon-cyan); background: rgba(34, 211, 238, 0.1); transform: translateY(-3px); }

        /* Floating Fixed Input Bar - NEVER HIDDEN */
        .input-bar { position: fixed !important; bottom: 30px !important; left: 15px !important; right: 15px !important; z-index: 999 !important; background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(25px); border-radius: 30px; padding: 12px 24px; border: 2.5px solid var(--neon-purple); box-shadow: 0 0 40px rgba(168, 85, 247, 0.5); }
        @media (min-width: 769px) { .input-bar { left: 330px; right: 40px; } }

        input#msg-input { background: transparent; border: none; color: white; flex: 1; outline: none; font-size: 16px; font-weight: 500; }
        
        .user-status { width: 10px; height: 10px; border-radius: 50%; background: var(--neon-green); box-shadow: 0 0 10px var(--neon-green); }

        /* Mobile View Fix */
        @media (max-width: 768px) { .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; } .sidebar.active { left: 0; } }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[1001] bg-[#020617] flex items-center justify-center p-6">
        <div class="bg-slate-900/60 border border-purple-500/30 p-12 rounded-[3rem] w-full max-w-sm backdrop-blur-3xl text-center">
            <h1 class="text-4xl font-bold mb-8 text-white">Neon<span class="text-purple-500">Pro</span></h1>
            <input id="username-input" type="text" placeholder="Workspace ID" class="w-full p-4 bg-slate-800 rounded-2xl mb-4 text-white text-center font-bold border border-transparent focus:border-cyan-400 outline-none transition">
            <input id="admin-key" type="password" placeholder="System Key (Optional)" class="w-full p-4 bg-slate-800/40 rounded-2xl mb-6 text-white text-center text-sm outline-none">
            <button id="join-btn" class="w-full bg-gradient-to-r from-purple-600 to-indigo-600 text-white font-bold py-5 rounded-2xl shadow-2xl active:scale-95 transition">Enter Core System</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-8 border-b border-white/5 font-bold text-2xl bg-gradient-to-r from-purple-400 to-cyan-400 bg-clip-text text-transparent">NEON OS</div>
            <div class="flex-1 overflow-y-auto p-4 space-y-8">
                <div>
                    <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest mb-4">Channels</p>
                    <div class="p-4 bg-purple-600/10 border border-purple-500/20 text-purple-300 rounded-2xl cursor-pointer font-bold"># general-chat</div>
                </div>
                <div>
                    <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest mb-4">Active Staff</p>
                    <div id="users-list" class="space-y-3"></div>
                </div>
            </div>
            <div id="admin-p" class="hidden p-6 bg-red-950/20 border-t border-red-500/20">
                <button onclick="wipeChat()" class="w-full py-3 bg-red-600/20 text-red-400 rounded-xl text-xs font-bold hover:bg-red-600/40 transition">WIPE DATABASE</button>
            </div>
        </aside>

        <main class="chat-view">
            <header class="h-24 border-b border-white/5 flex items-center justify-between px-8 backdrop-blur-xl">
                <div class="flex items-center gap-5">
                    <button onclick="toggleSidebar()" class="md:hidden text-white"><i class="fa-solid fa-bars-staggered text-2xl"></i></button>
                    <div>
                        <h2 class="text-2xl font-bold text-white tracking-tight"># general-chat</h2>
                        <div id="typing-notif" class="text-[10px] text-cyan-400 font-bold h-4"></div>
                    </div>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-bar">
                <form id="msg-form" class="flex items-center gap-5">
                    <input type="file" id="media-upload" class="hidden" onchange="handleFile(this)">
                    <button type="button" onclick="document.getElementById('media-upload').click()" class="text-slate-400 hover:text-cyan-400 transition">
                        <i class="fa-solid fa-circle-plus text-3xl"></i>
                    </button>
                    
                    <input id="msg-input" type="text" placeholder="Share your thoughts..." class="bg-transparent text-white" autocomplete="off" oninput="reportTyping()">
                    
                    <button class="w-12 h-12 bg-purple-600 rounded-2xl flex items-center justify-center text-white shadow-lg shadow-purple-500/30 active:scale-90 transition">
                        <i class="fa-solid fa-paper-plane text-xs"></i>
                    </button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove, update, increment } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        const storage = getStorage(app);
        let uName = "", isMaster = false, tTimer;
        const MASTER_PASS = "SECRET_BOSS"; // Admin Key

        document.getElementById('join-btn').onclick = () => {
            const name = document.getElementById('username-input').value.trim();
            const pass = document.getElementById('admin-key').value.trim();
            if(name){
                uName = name;
                if(pass === MASTER_PASS) { isMaster = true; document.getElementById('admin-p').classList.remove('hidden'); }
                document.getElementById('login-screen').style.display = 'none';
                initSession();
            }
        };

        function initSession() {
            set(ref(db, 'active/' + uName), { status: 'online', mode: isMaster ? 'admin' : 'user' });
            loadMainChat();
            loadActiveUsers();
            listenToTyping();
        }

        function loadMainChat() {
            onValue(ref(db, 'global_feed'), (snap) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                snap.forEach(m => {
                    const d = m.val();
                    const mid = m.key;
                    const card = document.createElement('div');
                    card.className = `msg-card ${d.sender === uName ? 'mine' : ''}`;
                    
                    let body = d.text || "";
                    if(d.file) body = `<img src="${d.file}" class="rounded-xl max-w-full hover:brightness-110 transition cursor-zoom-in">`;
                    
                    let reacts = "";
                    if(d.emoji) Object.keys(d.emoji).forEach(e => {
                        reacts += `<div class="react-p" onclick="addReact('${mid}', '${e}')">${e} <b>${d.emoji[e]}</b></div>`;
                    });

                    card.innerHTML = `
                        <p class="text-[10px] font-bold ${d.admin ? 'text-yellow-400' : 'text-cyan-400'} mb-2 tracking-widest">${d.sender.toUpperCase()}</p>
                        <div class="text-sm font-medium leading-relaxed">${body}</div>
                        <div class="reaction-bar">${reacts} <div class="react-p opacity-30" onclick="addReact('${mid}', '👍')">+ 👍</div><div class="react-p opacity-30" onclick="addReact('${mid}', '❤️')">+ ❤️</div></div>
                        ${isMaster ? `<button onclick="wipeMsg('${mid}')" class="absolute -top-2 -right-2 bg-red-600 w-5 h-5 rounded-full text-[10px]"><i class="fa-solid fa-xmark"></i></button>` : ''}
                    `;
                    flow.appendChild(card);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.addReact = (id, e) => update(ref(db, `global_feed/${id}/emoji`), { [e]: increment(1) });
        window.wipeMsg = (id) => remove(ref(db, `global_feed/${id}`));
        window.wipeChat = () => { if(confirm("NUKE CHAT HISTORY?")) remove(ref(db, 'global_feed')); };

        function loadActiveUsers() {
            onValue(ref(db, 'active'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(u => {
                    const row = document.createElement('div');
                    row.className = "flex items-center justify-between p-3 bg-white/5 rounded-xl";
                    row.innerHTML = `<div class="flex items-center gap-3"><div class="w-8 h-8 bg-slate-800 rounded-lg flex items-center justify-center text-xs font-bold">${u.key[0]}</div><span class="text-xs font-bold">${u.key}</span></div><div class="user-status"></div>`;
                    list.appendChild(row);
                });
            });
        }

        window.handleFile = async (input) => {
            const f = input.files[0]; if(!f) return;
            const s = sRef(storage, `uploads/${Date.now()}`);
            await uploadBytes(s, f);
            const url = await getDownloadURL(s);
            push(ref(db, 'global_feed'), { sender: uName, file: url, time: Date.now(), admin: isMaster });
        };

        window.reportTyping = () => {
            set(ref(db, `typing_status/${uName}`), true);
            clearTimeout(tTimer);
            tTimer = setTimeout(() => remove(ref(db, `typing_status/${uName}`)), 2000);
        };

        function listenToTyping() {
            onValue(ref(db, 'typing_status'), (snap) => {
                const active = snap.val() ? Object.keys(snap.val()).filter(u => u !== uName) : [];
                document.getElementById('typing-notif').innerText = active.length > 0 ? active[0] + " typing..." : "";
            });
        }

        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){
                push(ref(db, 'global_feed'), { sender: uName, text: i.value, time: Date.now(), admin: isMaster });
                i.value = "";
            }
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
    </script>
</body>
</html>
