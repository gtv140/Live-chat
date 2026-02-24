<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Ultimate Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        :root { --p: #a855f7; --bg: #020617; }
        body { background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; overflow: hidden; height: 100dvh; }
        
        /* Layout Structure */
        .app-container { display: grid; grid-template-columns: 280px 1fr; height: 100vh; }
        @media (max-width: 1024px) { 
            .app-container { grid-template-columns: 1fr; }
            .sidebar { position: fixed; inset: 0; z-index: 1000; width: 100%; transform: translateX(-100%); transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
            .sidebar.active { transform: translateX(0); }
        }

        .sidebar { background: #070b14; border-right: 1px solid rgba(255,255,255,0.05); }
        .glass { background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.05); }
        
        /* Message Area */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 16px; padding-bottom: 140px; }
        .msg-box { position: relative; max-width: 85%; display: flex; flex-direction: column; }
        .mine { align-self: flex-end; align-items: flex-end; }
        .other { align-self: flex-start; align-items: flex-start; }

        .bubble { padding: 12px 18px; border-radius: 22px; font-size: 14px; line-height: 1.5; position: relative; border: 1px solid rgba(255,255,255,0.03); }
        .mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); border-bottom-right-radius: 4px; box-shadow: 0 4px 15px rgba(168, 85, 247, 0.2); }
        .other .bubble { background: #1e293b; border-bottom-left-radius: 4px; }

        /* Actions (Reply/Delete/React) */
        .actions { position: absolute; top: -38px; background: #1e293b; border-radius: 12px; padding: 6px 12px; display: none; gap: 12px; border: 1px solid rgba(255,255,255,0.1); box-shadow: 0 8px 20px rgba(0,0,0,0.5); z-index: 50; }
        .msg-box:hover .actions { display: flex; }
        @media (max-width: 1024px) { .msg-box:active .actions { display: flex; } } /* Mobile Tap support */

        /* Reaction Chips */
        .rx-container { display: flex; flex-wrap: wrap; gap: 4px; margin-top: 6px; }
        .rx-chip { background: rgba(255,255,255,0.05); padding: 2px 8px; border-radius: 10px; font-size: 11px; cursor: pointer; border: 1px solid rgba(255,255,255,0.1); transition: 0.2s; }
        .rx-chip:hover { background: rgba(168, 85, 247, 0.2); border-color: #a855f7; }

        /* Inputs */
        .input-bar { position: absolute; bottom: 30px; left: 20px; right: 20px; border-radius: 30px; z-index: 100; }
        @media (max-width: 1024px) { .input-bar { bottom: 15px; left: 10px; right: 10px; } }
        
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth-screen" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#020617] p-6">
        <div class="glass p-10 rounded-[3rem] w-full max-w-sm text-center">
            <div class="w-16 h-16 bg-purple-600 rounded-2xl mx-auto mb-6 flex items-center justify-center text-3xl shadow-lg shadow-purple-600/30"><i class="fa-solid fa-bolt"></i></div>
            <h1 class="text-3xl font-black mb-8 tracking-tighter uppercase">Neon Elite</h1>
            <input id="u-name-in" type="text" placeholder="Your Name" class="w-full bg-white/5 border border-white/10 p-5 rounded-2xl text-white text-center outline-none focus:border-purple-500 mb-4 font-bold">
            <button onclick="enterApp()" class="w-full bg-purple-600 py-5 rounded-2xl font-black hover:bg-purple-500 transition-all active:scale-95 shadow-xl">JOIN WORKSPACE</button>
        </div>
    </div>

    <div class="app-container">
        <aside id="sidebar-menu" class="sidebar flex flex-col p-6">
            <div class="flex justify-between items-center mb-10">
                <span class="text-2xl font-black tracking-tighter text-purple-400">NEON.PRO</span>
                <button class="lg:hidden text-slate-500" onclick="toggleMenu()"><i class="fa-solid fa-xmark text-xl"></i></button>
            </div>

            <div class="flex-1 overflow-y-auto space-y-6">
                <div>
                    <p class="text-[10px] text-slate-500 font-bold uppercase tracking-[0.2em] mb-4">Channels</p>
                    <div onclick="switchChat('global')" class="flex items-center gap-3 p-4 rounded-2xl bg-purple-600/10 border border-purple-600/20 cursor-pointer hover:bg-purple-600/20 transition">
                        <i class="fa-solid fa-hashtag text-purple-400"></i> <span class="font-bold text-sm">global-hq</span>
                    </div>
                </div>
                
                <div>
                    <p class="text-[10px] text-slate-500 font-bold uppercase tracking-[0.2em] mb-4">Users Online</p>
                    <div id="active-users" class="space-y-2"></div>
                </div>
            </div>

            <div class="pt-6 border-t border-white/5 flex items-center gap-4">
                <div id="user-avatar" class="w-12 h-12 rounded-2xl bg-purple-600 flex items-center justify-center font-bold text-xl shadow-lg">?</div>
                <div class="truncate"><p id="user-name-display" class="font-bold text-sm">Guest</p><p class="text-[9px] text-green-500 font-bold uppercase">Active Now</p></div>
            </div>
        </aside>

        <main class="relative flex flex-col h-full bg-[#020617]">
            <header class="h-20 border-b border-white/5 flex items-center justify-between px-8 glass z-50">
                <div class="flex items-center gap-4">
                    <button class="lg:hidden w-10 h-10 glass rounded-xl" onclick="toggleMenu()"><i class="fa-solid fa-bars"></i></button>
                    <div>
                        <h2 id="header-title" class="text-lg font-black tracking-tight">Select a Chat</h2>
                        <p id="header-status" class="text-[10px] text-slate-500 font-bold uppercase">Awaiting Connection</p>
                    </div>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div id="input-section" class="input-bar glass p-2 flex flex-col hidden">
                <div id="reply-ctx" class="hidden mb-2 p-3 bg-purple-500/10 rounded-2xl border-l-4 border-purple-500 flex justify-between items-center transition-all">
                    <span id="reply-msg-text" class="text-[11px] text-purple-200 italic truncate mr-4"></span>
                    <button onclick="closeReply()"><i class="fa-solid fa-circle-xmark text-slate-500"></i></button>
                </div>
                
                <form id="chat-form" class="flex items-center gap-3">
                    <input type="file" id="file-in" class="hidden" accept="image/*" onchange="handleImage(this)">
                    <button type="button" onclick="document.getElementById('file-in').click()" class="text-slate-400 hover:text-purple-400 pl-2"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="chat-in" type="text" placeholder="Type a message..." class="bg-transparent flex-1 outline-none text-sm p-2 font-medium" autocomplete="off">
                    <button class="bg-purple-600 w-12 h-12 rounded-2xl flex items-center justify-center shadow-lg active:scale-90 transition-transform"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove, update } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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

        let myName = "", activeChat = null, currentReply = null;

        window.enterApp = () => {
            const input = document.getElementById('u-name-in').value.trim();
            if(!input) return;
            myName = input;
            document.getElementById('auth-screen').style.display = 'none';
            document.getElementById('user-name-display').innerText = myName;
            document.getElementById('user-avatar').innerText = myName[0].toUpperCase();
            set(ref(db, 'online/' + myName), true);
            listenForUsers();
        };

        window.toggleMenu = () => {
            const sidebar = document.getElementById('sidebar-menu');
            sidebar.classList.toggle('active');
        };

        window.switchChat = (target) => {
            activeChat = target;
            document.getElementById('input-section').classList.remove('hidden');
            document.getElementById('header-title').innerText = target === 'global' ? '# global-hq' : `@ ${target}`;
            document.getElementById('header-status').innerText = target === 'global' ? 'Main Team Channel' : 'Direct Encrypted Chat';
            if(window.innerWidth < 1024) toggleMenu();
            listenForMessages();
        };

        function listenForUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('active-users');
                list.innerHTML = "";
                s.forEach(u => {
                    if(u.key !== myName) {
                        const d = document.createElement('div');
                        d.className = "flex items-center gap-3 p-4 glass rounded-2xl cursor-pointer hover:bg-purple-600/10 transition mb-2";
                        d.innerHTML = `<div class="w-2 h-2 rounded-full bg-green-500 shadow-glow"></div> <span class="text-sm font-bold">${u.key}</span>`;
                        d.onclick = () => switchChat(u.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        function listenForMessages() {
            const path = activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            onValue(ref(db, path), (s) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                s.forEach(m => {
                    const data = m.val(), id = m.key, isMe = data.sender === myName;
                    const wrap = document.createElement('div');
                    wrap.className = `msg-box ${isMe ? 'mine' : 'other'}`;
                    
                    let rxHtml = "";
                    if(data.reactions) Object.entries(data.reactions).forEach(([emoji, count]) => {
                        rxHtml += `<div class="rx-chip" onclick="handleReact('${id}','${emoji}')">${emoji} ${count}</div>`;
                    });

                    wrap.innerHTML = `
                        <div class="actions">
                            <button onclick="triggerReply('${data.sender}','${data.text||"Photo"}')" class="text-[10px] font-bold">Reply</button>
                            <button onclick="handleReact('${id}','👍')" class="text-xs">👍</button>
                            <button onclick="handleReact('${id}','❤️')" class="text-xs">❤️</button>
                            ${isMe ? `<button onclick="handleDelete('${id}')" class="text-[10px] text-red-500 ml-2 font-bold">Delete</button>` : ''}
                        </div>
                        <span class="text-[9px] text-slate-500 font-bold mb-1 px-3 uppercase tracking-tighter">${data.sender}</span>
                        ${data.reply ? `<div class="text-[10px] bg-white/5 p-2 rounded-xl mb-1 border-l-2 border-purple-500 opacity-60 italic">Replying to ${data.reply.to}: ${data.reply.msg}</div>` : ''}
                        <div class="bubble">${data.img ? `<img src="${data.img}" class="rounded-xl max-w-full shadow-lg border border-white/5">` : data.text}</div>
                        <div class="rx-container">${rxHtml}</div>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.triggerReply = (u, m) => {
            currentReply = { to: u, msg: m };
            document.getElementById('reply-ctx').classList.remove('hidden');
            document.getElementById('reply-msg-text').innerText = `Replying to @${u}: ${m}`;
            document.getElementById('chat-in').focus();
        };

        window.closeReply = () => { currentReply = null; document.getElementById('reply-ctx').classList.add('hidden'); };

        window.handleReact = (msgId, emoji) => {
            const path = (activeChat === 'global' ? 'msgs/global/' : `private/${[myName, activeChat].sort().join('_')}/`) + msgId + '/reactions/' + emoji;
            const rRef = ref(db, path);
            onValue(rRef, (s) => { set(rRef, (s.val() || 0) + 1); }, { onlyOnce: true });
        };

        window.handleDelete = (id) => {
            if(!confirm("Erase this message?")) return;
            const path = (activeChat === 'global' ? 'msgs/global/' : `private/${[myName, activeChat].sort().join('_')}/`) + id;
            remove(ref(db, path));
        };

        window.handleImage = async (input) => {
            const file = input.files[0]; if(!file) return;
            try {
                const sRefObj = sRef(st, `media/${Date.now()}_${file.name}`);
                const snap = await uploadBytes(sRefObj, file);
                const url = await getDownloadURL(snap.ref);
                const path = activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
                push(ref(db, path), { sender: myName, img: url, time: Date.now() });
            } catch(e) { alert("Error: Make sure Firebase Storage Rules are set to 'allow read, write: if true;'"); }
        };

        document.getElementById('chat-form').onsubmit = (e) => {
            e.preventDefault();
            const input = document.getElementById('chat-in');
            if(!input.value.trim() || !activeChat) return;
            
            const path = activeChat === 'global' ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            const msgBody = { sender: myName, text: input.value.trim(), time: Date.now() };
            if(currentReply) { msgBody.reply = currentReply; closeReply(); }
            
            push(ref(db, path), msgBody);
            input.value = "";
        };

        window.onbeforeunload = () => { if(myName) remove(ref(db, 'online/' + myName)); };
    </script>
</body>
</html>
