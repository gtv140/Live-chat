<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Elite | 2026</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        :root { --p: #a855f7; --bg: #020617; }
        * { scroll-behavior: smooth; -webkit-tap-highlight-color: transparent; }
        body, html { height: 100%; margin: 0; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; overflow: hidden; }

        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: rgba(168, 85, 247, 0.3); border-radius: 10px; }

        .glass { background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.05); }
        .app-grid { display: grid; grid-template-columns: 300px 1fr; height: 100dvh; }
        @media (max-width: 1024px) { .app-grid { grid-template-columns: 1fr; } }

        /* Sidebar Adaptive */
        .sidebar { background: #070b14; border-right: 1px solid rgba(255,255,255,0.03); display: flex; flex-direction: column; transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        @media (max-width: 1024px) { 
            .sidebar { position: fixed; inset: 0; z-index: 2000; width: 100%; } 
            .sidebar.hidden-mobile { transform: translateX(-100%); }
        }

        /* Message UI */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; padding-bottom: 120px; }
        .msg-wrap { display: flex; flex-direction: column; max-width: 80%; position: relative; }
        .msg-mine { align-self: flex-end; align-items: flex-end; }
        .msg-other { align-self: flex-start; align-items: flex-start; }
        .bubble { padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; transition: 0.2s; border: 1px solid rgba(255,255,255,0.03); }
        .msg-mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); border-bottom-right-radius: 4px; }
        .msg-other .bubble { background: #1e293b; border-bottom-left-radius: 4px; }

        /* Reactions Style */
        .reaction-chip { background: rgba(255,255,255,0.1); border-radius: 20px; padding: 2px 8px; font-size: 12px; margin-top: 4px; cursor: pointer; display: flex; gap: 4px; align-items: center; border: 1px solid rgba(255,255,255,0.05); }
        .reaction-menu { position: absolute; top: -35px; background: #1e293b; border-radius: 12px; padding: 5px; display: none; gap: 8px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); z-index: 100; border: 1px solid rgba(255,255,255,0.1); }
        .msg-wrap:hover .reaction-menu { display: flex; }

        /* Floating Input */
        .input-box { position: absolute; bottom: 20px; left: 20px; right: 20px; border-radius: 24px; z-index: 100; }
        @media (max-width: 1024px) { .input-box { bottom: 15px; left: 10px; right: 10px; } }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[3000] glass flex items-center justify-center p-6">
        <div class="w-full max-w-sm text-center">
            <h1 class="text-5xl font-black mb-10 tracking-tighter text-purple-500">NeonPro</h1>
            <input id="u-input" type="text" placeholder="Username" class="w-full bg-white/5 border border-white/10 p-5 rounded-3xl text-white text-center outline-none focus:border-purple-500 mb-4 font-bold">
            <button id="join-btn" class="w-full bg-purple-600 p-5 rounded-3xl font-black shadow-2xl shadow-purple-600/30">LOG IN</button>
        </div>
    </div>

    <div class="app-grid">
        <aside id="sidebar" class="sidebar">
            <div class="p-8 flex justify-between items-center">
                <span class="text-2xl font-black tracking-tighter">WORKSPACE</span>
                <button onclick="window.logout()" class="text-slate-500"><i class="fa-solid fa-power-off"></i></button>
            </div>
            <div class="px-6 mb-4">
                <div class="glass rounded-2xl flex items-center px-4 py-3">
                    <i class="fa-solid fa-search text-slate-500 mr-3"></i>
                    <input type="text" id="user-search" oninput="window.filterUsers()" placeholder="Search team..." class="bg-transparent text-sm text-white outline-none w-full">
                </div>
            </div>
            <div id="users-list" class="flex-1 overflow-y-auto px-4 space-y-1">
                <div onclick="window.switchChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-2xl cursor-pointer font-bold mb-4 flex items-center gap-3">
                    <i class="fa-solid fa-hashtag text-purple-400"></i> Global Chat
                </div>
            </div>
        </aside>

        <main class="flex flex-col relative h-full">
            <header class="h-20 border-b border-white/5 flex items-center justify-between px-6 glass z-50">
                <div class="flex items-center gap-4">
                    <button onclick="window.goBack()" class="lg:hidden w-10 h-10 flex items-center justify-center glass rounded-xl text-white"><i class="fa-solid fa-arrow-left"></i></button>
                    <div>
                        <h2 id="chat-title" class="font-bold text-lg">Neon Console</h2>
                        <p id="chat-sub" class="text-[10px] text-green-500 font-bold uppercase tracking-widest">Select a user</p>
                    </div>
                </div>
                <div class="flex gap-4 text-slate-400">
                    <i class="fa-solid fa-video cursor-pointer hover:text-white"></i>
                    <i class="fa-solid fa-phone cursor-pointer hover:text-white"></i>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-box glass p-2 flex flex-col" id="input-container" style="display: none;">
                <div id="reply-box" class="hidden p-2 text-xs text-purple-300 border-l-2 border-purple-500 mb-2 flex justify-between">
                    <span id="reply-msg" class="truncate"></span>
                    <button onclick="window.cancelReply()"><i class="fa-solid fa-xmark"></i></button>
                </div>
                <form id="msg-form" class="flex items-center gap-3">
                    <button type="button" onclick="document.getElementById('f-input').click()" class="text-slate-400 pl-2"><i class="fa-solid fa-paperclip text-xl"></i></button>
                    <input type="file" id="f-input" class="hidden" onchange="window.upFile(this)">
                    <input id="msg-input" type="text" placeholder="Message..." autocomplete="off" class="bg-transparent text-white w-full outline-none p-2">
                    <button class="bg-purple-600 w-12 h-12 rounded-2xl shadow-lg"><i class="fa-solid fa-paper-plane"></i></button>
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
        let myN = "", activeC = null, replyTo = null;

        window.onload = () => { const s = localStorage.getItem('neonUser'); if(s) init(s); };
        document.getElementById('join-btn').onclick = () => {
            const v = document.getElementById('u-input').value.trim();
            if(v) { localStorage.setItem('neonUser', v); init(v); }
        };

        function init(n) {
            myN = n;
            document.getElementById('login-screen').style.display = 'none';
            set(ref(db, 'online/' + n), true);
            loadUsers();
        }

        window.goBack = () => document.getElementById('sidebar').classList.remove('hidden-mobile');

        window.switchChat = (u) => {
            activeC = u;
            document.getElementById('input-container').style.display = 'block';
            document.getElementById('chat-title').innerText = u === 'global' ? '# Global Chat' : `@ ${u}`;
            document.getElementById('sidebar').classList.add('hidden-mobile');
            loadMsgs();
        };

        function loadMsgs() {
            const path = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
            onValue(ref(db, path), (s) => {
                const flow = document.getElementById('chat-flow'); flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), id = m.key, isM = d.sender === myN;
                    const wrap = document.createElement('div');
                    wrap.className = `msg-wrap ${isM ? 'msg-mine' : 'msg-other'}`;
                    
                    let rxHtml = "";
                    if(d.reactions) {
                        Object.entries(d.reactions).forEach(([emoji, count]) => {
                            rxHtml += `<div class="reaction-chip" onclick="window.react('${id}','${emoji}')">${emoji} ${count}</div>`;
                        });
                    }

                    wrap.innerHTML = `
                        <div class="reaction-menu">
                            <button onclick="window.react('${id}','👍')">👍</button>
                            <button onclick="window.react('${id}','❤️')">❤️</button>
                            <button onclick="window.react('${id}','😂')">😂</button>
                            <button onclick="window.react('${id}','🔥')">🔥</button>
                            <button onclick="window.delMsg('${id}')" class="ml-2 text-red-500"><i class="fa-solid fa-trash"></i></button>
                        </div>
                        <span class="text-[9px] text-slate-500 font-bold mb-1 uppercase">${d.sender}</span>
                        <div class="bubble">${d.img ? `<img src="${d.img}" class="rounded-xl">` : d.text}</div>
                        <div class="flex gap-1">${rxHtml}</div>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.react = (msgId, emoji) => {
            const path = (activeC === 'global' ? 'msgs/global/' : `private/${[myN, activeC].sort().join('_')}/`) + msgId + '/reactions/' + emoji;
            const rRef = ref(db, path);
            onValue(rRef, (s) => {
                const count = s.exists() ? s.val() + 1 : 1;
                set(rRef, count);
            }, { onlyOnce: true });
        };

        window.delMsg = (id) => {
            if(confirm("Delete?")) {
                const p = (activeC === 'global' ? 'msgs/global/' : `private/${[myN, activeC].sort().join('_')}/`) + id;
                remove(ref(db, p));
            }
        };

        window.upFile = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `media/${Date.now()}`);
            await uploadBytes(r, f);
            const url = await getDownloadURL(r);
            const p = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
            push(ref(db, p), { sender: myN, img: url, time: Date.now() });
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('users-list');
                const existing = list.querySelectorAll('.user-item');
                existing.forEach(e => e.remove());
                s.forEach(c => {
                    if(c.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "user-item flex items-center gap-3 p-4 glass rounded-2xl cursor-pointer hover:bg-purple-600/10 transition mb-2";
                        d.innerHTML = `<div class="w-10 h-10 rounded-xl bg-purple-600 flex items-center justify-center font-bold">${c.key[0]}</div>
                                       <div><p class="text-sm font-bold">${c.key}</p><p class="text-[10px] text-green-500">Online</p></div>`;
                        d.onclick = () => window.switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim() && activeC) {
                const p = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
                push(ref(db, p), { sender: myN, text: i.value.trim(), time: Date.now() });
                i.value = "";
            }
        };

        window.logout = () => { remove(ref(db, 'online/' + myN)); localStorage.clear(); location.reload(); };
    </script>
</body>
</html>
