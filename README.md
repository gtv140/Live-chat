<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Enterprise | Final Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        :root { --neon-purple: #a855f7; --neon-cyan: #22d3ee; }
        body, html { height: 100%; margin: 0; padding: 0; overflow: hidden; background: #020617; font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; }
        .app-shell { display: flex; height: 100dvh; width: 100%; position: relative; }
        
        /* Sidebar */
        .sidebar { width: 280px; background: rgba(15, 23, 42, 0.98); border-right: 1px solid rgba(255,255,255,0.05); display: flex; flex-direction: column; z-index: 100; transition: 0.3s ease; }
        @media (max-width: 768px) { .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; } .sidebar.active { left: 0; } }

        /* Chat Body */
        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); position: relative; }
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 240px !important; display: flex; flex-direction: column; gap: 15px; scroll-behavior: smooth; }

        /* Neon Message Cards */
        .msg-card { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); padding: 14px; border-radius: 20px; max-width: 85%; align-self: flex-start; position: relative; transition: 0.3s; }
        .msg-card.mine { align-self: flex-end; border-color: var(--neon-purple); background: rgba(168, 85, 247, 0.12); }
        
        /* Reactions */
        .reaction-bar { display: flex; gap: 6px; margin-top: 10px; flex-wrap: wrap; }
        .react-btn { background: rgba(255,255,255,0.06); padding: 4px 10px; border-radius: 10px; font-size: 12px; cursor: pointer; border: 1px solid transparent; transition: 0.2s; display: flex; align-items: center; gap: 5px; }
        .react-btn:hover { border-color: var(--neon-cyan); background: rgba(34, 211, 238, 0.1); }

        /* THE FLOATING INPUT - Always Visible */
        .input-container { 
            position: fixed !important; bottom: 30px !important; left: 15px !important; right: 15px !important; 
            z-index: 999 !important; background: rgba(15, 23, 42, 0.95) !important; 
            backdrop-filter: blur(20px); border-radius: 26px; padding: 12px 20px; 
            border: 2px solid var(--neon-purple); box-shadow: 0 0 40px rgba(168, 85, 247, 0.4); 
        }
        @media (min-width: 769px) { .input-container { left: 300px; right: 30px; } }

        input#msg-input { background: transparent; border: none; color: white; flex: 1; outline: none; font-size: 16px; }
        .status-online { width: 10px; height: 10px; background: #22c55e; border-radius: 50%; box-shadow: 0 0 10px #22c55e; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[1000] bg-[#020617] flex items-center justify-center p-6">
        <div class="bg-slate-900/80 border border-purple-500/30 p-10 rounded-[2.5rem] w-full max-w-sm backdrop-blur-3xl text-center">
            <div class="w-16 h-16 bg-purple-600 rounded-2xl flex items-center justify-center mx-auto mb-6 shadow-xl shadow-purple-500/20">
                <i class="fa-solid fa-bolt text-white text-2xl"></i>
            </div>
            <h2 class="text-3xl font-bold mb-2 text-white">Neon<span class="text-purple-500">Pro</span></h2>
            <p class="text-slate-400 text-xs mb-8">Login to Workspace</p>
            <input id="username-input" type="text" placeholder="Your Name" class="w-full p-4 bg-slate-800 rounded-2xl mb-4 text-white text-center font-bold focus:border-cyan-400 outline-none border border-transparent transition">
            <input id="admin-key" type="password" placeholder="Admin Password (Optional)" class="w-full p-4 bg-slate-800/40 rounded-2xl mb-6 text-white text-center text-sm outline-none border border-transparent">
            <button id="join-btn" class="w-full bg-purple-600 text-white font-bold py-4 rounded-2xl shadow-lg active:scale-95 transition">Access System</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-8 border-b border-white/5 font-bold text-xl text-white tracking-widest">NEON HQ</div>
            <div class="flex-1 overflow-y-auto p-4 space-y-6">
                <div class="p-3 bg-purple-500/10 border border-purple-500/20 text-purple-300 rounded-xl font-bold text-sm cursor-pointer"># general-workspace</div>
                <p class="text-[10px] text-slate-500 font-bold uppercase ml-2">Online Now</p>
                <div id="users-list" class="space-y-2"></div>
            </div>
            <div id="admin-panel" class="hidden p-6 bg-red-900/10 border-t border-red-500/20">
                <button onclick="clearChat()" class="w-full py-3 bg-red-600 text-white rounded-xl text-xs font-bold shadow-lg">WIPE ALL CHAT</button>
            </div>
        </aside>

        <main class="chat-view">
            <header class="h-20 border-b border-white/5 flex items-center justify-between px-6 bg-slate-900/20">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-white"><i class="fa-solid fa-bars-staggered"></i></button>
                    <div>
                        <h2 class="text-xl font-bold text-white tracking-tight"># general-workspace</h2>
                        <div id="typing-box" class="text-[10px] text-cyan-400 font-bold h-3"></div>
                    </div>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-container">
                <form id="msg-form" class="flex items-center gap-4">
                    <input type="file" id="file-upload" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-upload').click()" class="text-slate-400 hover:text-cyan-400 transition">
                        <i class="fa-solid fa-camera text-2xl"></i>
                    </button>
                    
                    <input id="msg-input" type="text" placeholder="Type a message..." class="bg-transparent text-white" autocomplete="off" oninput="handleTyping()">
                    
                    <button class="w-11 h-11 bg-purple-600 rounded-xl flex items-center justify-center text-white shadow-xl active:scale-90 transition">
                        <i class="fa-solid fa-paper-plane text-xs"></i>
                    </button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, update, increment, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let myName = "", isAdmin = false, typingTimer;
        const ADMIN_PASS = "SECRET_BOSS"; // Admin Password

        document.getElementById('join-btn').onclick = () => {
            const n = document.getElementById('username-input').value.trim();
            const p = document.getElementById('admin-key').value.trim();
            if(n){
                myName = n;
                if(p === ADMIN_PASS) { 
                    isAdmin = true; 
                    document.getElementById('admin-panel').classList.remove('hidden'); 
                }
                document.getElementById('login-screen').style.display = 'none';
                initSession();
            }
        };

        function initSession() {
            set(ref(db, 'online/' + myName), true);
            loadMessages();
            loadUsers();
            listenTyping();
        }

        function loadMessages() {
            onValue(ref(db, 'msgs/global'), (snap) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                snap.forEach(msg => {
                    const d = msg.val();
                    const mid = msg.key;
                    const isMine = d.sender === myName;
                    const div = document.createElement('div');
                    div.className = `msg-card ${isMine ? 'mine' : ''}`;
                    
                    let content = d.text || "";
                    if(d.img) content = `<img src="${d.img}" class="rounded-xl max-w-full">`;
                    
                    let reacts = "";
                    if(d.reactions) Object.keys(d.reactions).forEach(e => {
                        reacts += `<div class="react-btn" onclick="react('${mid}', '${e}')">${e} <b>${d.reactions[e]}</b></div>`;
                    });

                    div.innerHTML = `
                        <div class="flex justify-between items-center gap-4 mb-1">
                            <span class="text-[10px] font-bold ${d.admin ? 'text-yellow-400' : 'text-purple-400'}">${d.sender} ${d.admin ? '[ADMIN]' : ''}</span>
                            <span class="text-[9px] text-slate-500">${new Date(d.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
                        </div>
                        <div class="text-sm text-slate-200">${content}</div>
                        <div class="reaction-bar">${reacts} <div class="react-btn opacity-40" onclick="react('${mid}', '👍')">+ 👍</div><div class="react-btn opacity-40" onclick="react('${mid}', '🔥')">+ 🔥</div></div>
                        ${isAdmin ? `<button onclick="deleteMsg('${mid}')" class="text-[9px] text-red-500 mt-2 font-bold uppercase underline">Delete Message</button>` : ''}
                    `;
                    flow.appendChild(div);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.react = (id, e) => update(ref(db, `msgs/global/${id}/reactions`), { [e]: increment(1) });
        window.deleteMsg = (id) => remove(ref(db, `msgs/global/${id}`));
        window.clearChat = () => { if(confirm("DELETE EVERYTHING?")) remove(ref(db, 'msgs/global')); };

        function loadUsers() {
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    const d = document.createElement('div');
                    d.className = "flex items-center justify-between p-3 bg-white/5 rounded-xl";
                    d.innerHTML = `<span class="text-xs font-bold text-white">${c.key}</span><div class="status-online"></div>`;
                    list.appendChild(d);
                });
            });
        }

        window.uploadFile = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(storage, `files/${Date.now()}`);
            await uploadBytes(r, f);
            const url = await getDownloadURL(r);
            push(ref(db, 'msgs/global'), { sender: myName, img: url, time: Date.now(), admin: isAdmin });
        };

        window.handleTyping = () => {
            set(ref(db, `typing/${myName}`), true);
            clearTimeout(typingTimer);
            typingTimer = setTimeout(() => remove(ref(db, `typing/${myName}`)), 2000);
        };

        function listenTyping() {
            onValue(ref(db, 'typing'), (snap) => {
                const users = snap.val() ? Object.keys(snap.val()).filter(u => u !== myName) : [];
                document.getElementById('typing-box').innerText = users.length > 0 ? users[0] + " is typing..." : "";
            });
        }

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                push(ref(db, 'msgs/global'), { sender: myName, text: i.value, time: Date.now(), admin: isAdmin });
                i.value = ""; 
            }
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
    </script>
</body>
</html>
