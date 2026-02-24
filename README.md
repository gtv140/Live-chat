<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>NeonPro | Professional Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        :root { --p: #a855f7; --bg: #020617; }
        body { background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; overflow: hidden; height: 100dvh; }
        
        /* Layout Structure */
        .main-wrapper { display: grid; grid-template-columns: 300px 1fr; height: 100vh; }
        @media (max-width: 1024px) { .main-wrapper { grid-template-columns: 1fr; } }

        .sidebar { background: #070b14; border-right: 1px solid rgba(255,255,255,0.05); transition: 0.3s ease; }
        @media (max-width: 1024px) { .sidebar.hide { display: none; } .sidebar.show { position: fixed; inset: 0; z-index: 100; width: 100%; } }

        /* Modern Glass Cards */
        .glass { background: rgba(15, 23, 42, 0.7); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.05); }
        
        /* Messages */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 12px; padding-bottom: 120px; }
        .msg-container { position: relative; max-width: 80%; display: flex; flex-direction: column; group }
        .mine { align-self: flex-end; align-items: flex-end; }
        .other { align-self: flex-start; align-items: flex-start; }

        .bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; transition: 0.2s; }
        .mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); border-bottom-right-radius: 4px; }
        .other .bubble { background: #1e293b; border-bottom-left-radius: 4px; }

        /* Floating Actions Menu (Skype Style) */
        .actions-menu { position: absolute; top: -35px; background: #1e293b; border-radius: 10px; padding: 4px 8px; display: none; gap: 10px; border: 1px solid rgba(255,255,255,0.1); box-shadow: 0 5px 15px rgba(0,0,0,0.4); z-index: 10; }
        .msg-container:hover .actions-menu { display: flex; }
        
        .reaction-bar { display: flex; gap: 4px; margin-top: 4px; }
        .rx-chip { background: rgba(255,255,255,0.05); padding: 2px 6px; border-radius: 8px; font-size: 10px; cursor: pointer; border: 1px solid transparent; }
        .rx-chip:hover { border-color: #a855f7; }

        /* Input Bar */
        .input-area { position: absolute; bottom: 20px; left: 20px; right: 20px; border-radius: 24px; padding: 8px 16px; }
        
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 10px; }
    </style>
</head>
<body>

    <div id="auth" class="fixed inset-0 z-[1000] flex items-center justify-center bg-[#020617] p-6">
        <div class="glass p-10 rounded-[2.5rem] w-full max-w-sm text-center">
            <h1 class="text-3xl font-extrabold mb-8 tracking-tighter uppercase text-purple-500">Neon Workspace</h1>
            <input id="u-name-input" type="text" placeholder="Enter Nickname" class="w-full bg-white/5 border border-white/10 p-4 rounded-2xl text-white text-center outline-none focus:border-purple-500 mb-4 font-bold">
            <button onclick="startApp()" class="w-full bg-purple-600 py-4 rounded-2xl font-black hover:bg-purple-500 transition-all">JOIN SPACE</button>
        </div>
    </div>

    <div class="main-wrapper">
        <aside id="side-menu" class="sidebar flex flex-col p-6">
            <div class="flex justify-between items-center mb-10">
                <span class="text-xl font-extrabold text-purple-400">NEON.OS</span>
                <button class="lg:hidden" onclick="toggleSidebar()"><i class="fa-solid fa-xmark"></i></button>
            </div>

            <div class="flex-1 overflow-y-auto space-y-6">
                <div>
                    <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest mb-4">Channels</p>
                    <div onclick="openChat('global')" class="flex items-center gap-3 p-3 rounded-xl bg-purple-600/10 border border-purple-600/20 cursor-pointer">
                        <i class="fa-solid fa-hashtag text-purple-400"></i> <span class="font-semibold">global-hq</span>
                    </div>
                </div>
                
                <div>
                    <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest mb-4">Direct Messages</p>
                    <div id="user-list" class="space-y-2"></div>
                </div>
            </div>

            <div class="pt-6 border-t border-white/5 flex items-center gap-3">
                <div id="my-avatar" class="w-10 h-10 rounded-xl bg-purple-600 flex items-center justify-center font-bold">?</div>
                <div class="truncate"><p id="my-name-display" class="text-sm font-bold">User</p></div>
            </div>
        </aside>

        <main class="relative flex flex-col bg-[#020617]">
            <header class="h-16 border-b border-white/5 flex items-center justify-between px-6 glass">
                <div class="flex items-center gap-4">
                    <button class="lg:hidden" onclick="toggleSidebar()"><i class="fa-solid fa-bars"></i></button>
                    <h2 id="current-chat-name" class="font-bold">Select Chat</h2>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div id="input-wrap" class="input-area glass hidden">
                <div id="reply-preview" class="hidden mb-2 p-2 bg-white/5 rounded-lg flex justify-between items-center">
                    <span id="reply-text" class="text-[11px] text-slate-400 italic"></span>
                    <button onclick="cancelReply()"><i class="fa-solid fa-circle-xmark"></i></button>
                </div>
                <form id="chat-form" class="flex items-center gap-3">
                    <input type="file" id="img-input" class="hidden" accept="image/*" onchange="uploadImage(this)">
                    <button type="button" onclick="document.getElementById('img-input').click()" class="text-slate-400 hover:text-purple-500"><i class="fa-solid fa-circle-plus text-xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Send a message..." class="bg-transparent flex-1 outline-none text-sm p-2" autocomplete="off">
                    <button class="bg-purple-600 w-10 h-10 rounded-xl flex items-center justify-center"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove, update } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        
        let userName = "", currentChat = null, replyData = null;

        window.startApp = () => {
            const input = document.getElementById('u-name-input').value.trim();
            if(!input) return;
            userName = input;
            document.getElementById('auth').style.display = 'none';
            document.getElementById('my-name-display').innerText = userName;
            document.getElementById('my-avatar').innerText = userName[0].toUpperCase();
            set(ref(db, 'online/' + userName), true);
            listenUsers();
        };

        window.toggleSidebar = () => {
            const side = document.getElementById('side-menu');
            side.classList.toggle('show');
            side.classList.toggle('hide');
        };

        window.openChat = (target) => {
            currentChat = target;
            document.getElementById('input-wrap').classList.remove('hidden');
            document.getElementById('current-chat-name').innerText = target === 'global' ? '# global-hq' : `@ ${target}`;
            if(window.innerWidth < 1024) toggleSidebar();
            listenMessages();
        };

        function listenUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('user-list');
                list.innerHTML = "";
                s.forEach(c => {
                    if(c.key !== userName) {
                        const div = document.createElement('div');
                        div.className = "flex items-center gap-3 p-3 rounded-xl hover:bg-white/5 cursor-pointer transition";
                        div.innerHTML = `<div class="w-2 h-2 rounded-full bg-green-500"></div> <span class="text-sm">${c.key}</span>`;
                        div.onclick = () => openChat(c.key);
                        list.appendChild(div);
                    }
                });
            });
        }

        function listenMessages() {
            const path = currentChat === 'global' ? 'msgs/global' : `private/${[userName, currentChat].sort().join('_')}`;
            onValue(ref(db, path), (s) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isMine = d.sender === userName;
                    const wrap = document.createElement('div');
                    wrap.className = `msg-container ${isMine ? 'mine' : 'other'}`;
                    
                    let rxHtml = "";
                    if(d.reactions) Object.entries(d.reactions).forEach(([emoji, count]) => {
                        rxHtml += `<div class="rx-chip" onclick="react('${id}','${emoji}')">${emoji} ${count}</div>`;
                    });

                    wrap.innerHTML = `
                        <div class="actions-menu">
                            <button onclick="setReply('${d.sender}','${d.text||"Media"}')" class="text-[10px] hover:text-purple-400">Reply</button>
                            <button onclick="react('${id}','👍')" class="text-xs">👍</button>
                            <button onclick="react('${id}','❤️')" class="text-xs">❤️</button>
                            ${isMine ? `<button onclick="deleteMsg('${id}')" class="text-[10px] text-red-500 ml-2">Delete</button>` : ''}
                        </div>
                        <span class="text-[9px] text-slate-500 font-bold mb-1 px-2 uppercase">${d.sender}</span>
                        ${d.reply ? `<div class="text-[10px] bg-white/5 p-2 rounded-lg mb-1 border-l-2 border-purple-500 opacity-60">Replying to ${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble">${d.img ? `<img src="${d.img}" class="rounded-lg max-w-full shadow-lg">` : d.text}</div>
                        <div class="reaction-bar">${rxHtml}</div>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setReply = (u, m) => {
            replyData = { to: u, msg: m };
            document.getElementById('reply-preview').classList.remove('hidden');
            document.getElementById('reply-text').innerText = `Replying to @${u}: ${m}`;
            document.getElementById('msg-input').focus();
        };

        window.cancelReply = () => { replyData = null; document.getElementById('reply-preview').classList.add('hidden'); };

        window.react = (msgId, emoji) => {
            const path = (currentChat === 'global' ? 'msgs/global/' : `private/${[userName, currentChat].sort().join('_')}/`) + msgId + '/reactions/' + emoji;
            const rRef = ref(db, path);
            onValue(rRef, (s) => { set(rRef, (s.val() || 0) + 1); }, { onlyOnce: true });
        };

        window.deleteMsg = (id) => {
            if(!confirm("Delete this message?")) return;
            const path = (currentChat === 'global' ? 'msgs/global/' : `private/${[userName, currentChat].sort().join('_')}/`) + id;
            remove(ref(db, path));
        };

        window.uploadImage = async (input) => {
            const file = input.files[0]; if(!file) return;
            try {
                const storageRef = sRef(st, `work_files/${Date.now()}_${file.name}`);
                const snap = await uploadBytes(storageRef, file);
                const url = await getDownloadURL(snap.ref);
                const path = currentChat === 'global' ? 'msgs/global' : `private/${[userName, currentChat].sort().join('_')}`;
                push(ref(db, path), { sender: userName, img: url, time: Date.now() });
            } catch(e) { alert("Check Firebase Storage Rules!"); }
        };

        document.getElementById('chat-form').onsubmit = (e) => {
            e.preventDefault();
            const input = document.getElementById('msg-input');
            if(!input.value.trim() || !currentChat) return;
            
            const path = currentChat === 'global' ? 'msgs/global' : `private/${[userName, currentChat].sort().join('_')}`;
            const msgObj = { sender: userName, text: input.value.trim(), time: Date.now() };
            if(replyData) { msgObj.reply = replyData; cancelReply(); }
            
            push(ref(db, path), msgObj);
            input.value = "";
        };

        window.onbeforeunload = () => { if(userName) remove(ref(db, 'online/' + userName)); };
    </script>
</body>
</html>
