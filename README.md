<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Elite | All-in-One Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        
        :root { --p: #a855f7; --bg: #020617; }
        * { scroll-behavior: smooth; -webkit-tap-highlight-color: transparent; box-sizing: border-box; }
        
        body, html { height: 100%; margin: 0; background: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; overflow: hidden; }

        /* Modern Scrollbar */
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: rgba(168, 85, 247, 0.4); border-radius: 10px; }

        .glass { background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.05); }
        
        /* Layout Grid */
        .app-grid { display: grid; grid-template-columns: 320px 1fr; height: 100dvh; width: 100%; }
        @media (max-width: 1024px) { .app-grid { grid-template-columns: 1fr; } }

        /* Sidebar Adaptive */
        .sidebar { background: #070b14; border-right: 1px solid rgba(255,255,255,0.03); display: flex; flex-direction: column; transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); z-index: 100; }
        @media (max-width: 1024px) { 
            .sidebar { position: fixed; inset: 0; width: 100%; z-index: 2000; } 
            .sidebar.mobile-hidden { transform: translateX(-100%); }
        }

        /* Messaging UI */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; padding-bottom: 120px; }
        .msg-wrap { display: flex; flex-direction: column; max-width: 80%; position: relative; transition: 0.2s; }
        .msg-mine { align-self: flex-end; align-items: flex-end; }
        .msg-other { align-self: flex-start; align-items: flex-start; }
        
        .bubble { padding: 12px 18px; border-radius: 22px; font-size: 14px; line-height: 1.5; border: 1px solid rgba(255,255,255,0.03); position: relative; }
        .msg-mine .bubble { background: linear-gradient(135deg, #7e22ce, #a855f7); border-bottom-right-radius: 4px; box-shadow: 0 4px 15px rgba(168, 85, 247, 0.2); }
        .msg-other .bubble { background: #1e293b; border-bottom-left-radius: 4px; }

        /* Reaction Logic */
        .msg-actions { position: absolute; top: -35px; background: #1e293b; border-radius: 12px; padding: 5px 12px; display: none; gap: 10px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); z-index: 50; border: 1px solid rgba(255,255,255,0.1); }
        .msg-wrap:hover .msg-actions { display: flex; }
        .reaction-badge { background: rgba(255,255,255,0.08); border-radius: 10px; padding: 2px 8px; font-size: 11px; margin-top: 5px; cursor: pointer; border: 1px solid rgba(255,255,255,0.05); }

        /* Floating Input */
        .input-box { position: absolute; bottom: 25px; left: 25px; right: 25px; border-radius: 28px; z-index: 100; box-shadow: 0 10px 40px rgba(0,0,0,0.4); }
        @media (max-width: 1024px) { .input-box { bottom: 15px; left: 10px; right: 10px; } }
        
        #login-screen { background: radial-gradient(circle at center, #1e1b4b, #020617); }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[5000] flex items-center justify-center p-6">
        <div class="glass p-12 rounded-[3rem] w-full max-w-sm text-center">
            <div class="w-20 h-20 bg-purple-600 rounded-3xl mx-auto mb-6 flex items-center justify-center text-white text-4xl shadow-2xl shadow-purple-600/40"><i class="fa-solid fa-bolt"></i></div>
            <h1 class="text-4xl font-black mb-10 tracking-tighter">NEON PRO</h1>
            <input id="u-input" type="text" placeholder="Your Nickname" class="w-full bg-white/5 border border-white/10 p-5 rounded-2xl text-white text-center outline-none focus:border-purple-500 mb-4 font-bold transition-all">
            <button id="join-btn" class="w-full bg-purple-600 text-white font-black py-5 rounded-2xl shadow-xl active:scale-95 transition-all">CONNECT</button>
        </div>
    </div>

    <div class="app-grid">
        <aside id="sidebar" class="sidebar">
            <div class="p-8 flex justify-between items-center">
                <span class="text-2xl font-black tracking-tighter text-purple-400">NEON.OS</span>
                <button onclick="window.logout()" class="text-slate-500 hover:text-red-500 transition-colors"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="px-6 mb-8">
                <div class="glass rounded-2xl flex items-center px-4 py-3">
                    <i class="fa-solid fa-magnifying-glass text-slate-500 mr-3 text-sm"></i>
                    <input type="text" id="user-search" oninput="window.filterUsers()" placeholder="Search workspace..." class="bg-transparent text-sm text-white outline-none w-full">
                </div>
            </div>

            <div id="users-list" class="flex-1 overflow-y-auto px-4 space-y-2">
                <div onclick="window.switchChat('global')" class="p-4 bg-purple-600/10 border border-purple-600/20 rounded-2xl cursor-pointer font-bold flex items-center gap-3 hover:bg-purple-600/20 transition group">
                    <div class="w-10 h-10 bg-purple-600/20 rounded-xl flex items-center justify-center text-purple-400"><i class="fa-solid fa-globe"></i></div>
                    <span class="text-slate-200">Global HQ</span>
                </div>
                <p class="text-[10px] text-slate-600 font-black uppercase tracking-widest ml-2 mt-6 mb-4">Channels & DMs</p>
                </div>

            <div class="p-6 bg-black/20 border-t border-white/5 flex items-center gap-4">
                <div id="u-avatar" class="w-12 h-12 rounded-2xl bg-purple-600 flex items-center justify-center font-bold text-white shadow-lg text-xl">?</div>
                <div class="overflow-hidden">
                    <p id="u-name" class="text-sm font-bold text-white truncate">Username</p>
                    <p class="text-[9px] text-green-500 font-bold uppercase tracking-widest">● Active</p>
                </div>
            </div>
        </aside>

        <main class="flex flex-col relative h-full">
            <header class="h-20 border-b border-white/5 flex items-center justify-between px-8 glass z-50">
                <div class="flex items-center gap-4">
                    <button onclick="window.goBack()" class="lg:hidden w-10 h-10 glass rounded-xl text-white flex items-center justify-center transition-transform active:scale-90"><i class="fa-solid fa-chevron-left"></i></button>
                    <div>
                        <h2 id="chat-title" class="text-lg font-extrabold tracking-tight">Main Console</h2>
                        <div id="chat-sub" class="text-[9px] text-slate-500 font-bold uppercase tracking-widest">Awaiting Sync</div>
                    </div>
                </div>
                <div class="flex items-center gap-6 text-slate-400">
                    <i class="fa-solid fa-video hover:text-white cursor-pointer transition"></i>
                    <i class="fa-solid fa-phone hover:text-white cursor-pointer transition"></i>
                    <i class="fa-solid fa-ellipsis-v hover:text-white cursor-pointer transition"></i>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="input-box glass p-2 flex flex-col" id="input-container" style="display: none;">
                <div id="reply-box" class="hidden p-3 text-xs bg-purple-500/10 rounded-xl mb-2 border-l-4 border-purple-500 flex justify-between items-center">
                    <span id="reply-msg" class="text-purple-200 opacity-80 truncate"></span>
                    <button onclick="window.cancelReply()" class="text-slate-500"><i class="fa-solid fa-xmark"></i></button>
                </div>
                <form id="msg-form" class="flex items-center gap-3">
                    <input type="file" id="f-input" class="hidden" accept="image/*" onchange="window.upFile(this)">
                    <button type="button" onclick="document.getElementById('f-input').click()" class="text-slate-500 hover:text-purple-400 p-2"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Type a message..." autocomplete="off" class="bg-transparent text-white w-full outline-none p-2 text-sm font-medium">
                    <button class="bg-purple-600 hover:bg-purple-500 w-12 h-12 rounded-2xl shadow-xl shadow-purple-600/20 transition-all active:scale-90 flex items-center justify-center"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        // 🔥 FIREBASE CONFIG (Final Sync)
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

        // Init App
        window.onload = () => { const s = localStorage.getItem('neonUser'); if(s) init(s); };
        document.getElementById('join-btn').onclick = () => {
            const v = document.getElementById('u-input').value.trim();
            if(v) { localStorage.setItem('neonUser', v); init(v); }
        };

        function init(n) {
            myN = n;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('u-name').innerText = n;
            document.getElementById('u-avatar').innerText = n[0].toUpperCase();
            set(ref(db, 'online/' + n), true);
            loadUsers();
        }

        window.goBack = () => document.getElementById('sidebar').classList.remove('mobile-hidden');

        window.switchChat = (u) => {
            activeC = u;
            document.getElementById('input-container').style.display = 'block';
            document.getElementById('chat-title').innerText = u === 'global' ? '# Global Channel' : `@ ${u}`;
            document.getElementById('chat-sub').innerText = u === 'global' ? 'Team Communication Hub' : 'Encrypted Private Session';
            document.getElementById('sidebar').classList.add('mobile-hidden');
            loadMsgs();
        };

        // 📸 PHOTO SHARING LOGIC
        window.upFile = async (i) => {
            const f = i.files[0]; if(!f) return;
            try {
                const r = sRef(st, `uploads/${Date.now()}_${f.name}`);
                const snap = await uploadBytes(r, f);
                const url = await getDownloadURL(snap.ref);
                const path = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
                push(ref(db, path), { sender: myN, img: url, time: Date.now(), type: 'image' });
            } catch(e) { alert("Photo failed! Please check Firebase Storage Rules (set allow to 'if true')."); }
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
                            rxHtml += `<div class="reaction-badge" onclick="window.react('${id}','${emoji}')">${emoji} ${count}</div>`;
                        });
                    }

                    wrap.innerHTML = `
                        <div class="msg-actions">
                            <button onclick="window.setReply('${d.sender}','${d.text||"Image"}')" class="text-xs hover:text-purple-400">Reply</button>
                            <button onclick="window.react('${id}','👍')">👍</button>
                            <button onclick="window.react('${id}','❤️')">❤️</button>
                            <button onclick="window.react('${id}','😂')">😂</button>
                            <button onclick="window.delMsg('${id}')" class="text-red-500 text-xs ml-2">Delete</button>
                        </div>
                        <span class="text-[9px] text-slate-600 font-bold px-3 mb-1 uppercase tracking-tight">${d.sender}</span>
                        ${d.reply ? `<div class="text-[10px] bg-white/5 p-2 rounded-xl mb-1 border-l-2 border-purple-500 opacity-60 italic">@${d.reply.to}: ${d.reply.msg}</div>` : ''}
                        <div class="bubble">${d.img ? `<img src="${d.img}" class="rounded-2xl max-w-full shadow-lg">` : d.text}</div>
                        <div class="flex gap-1 flex-wrap">${rxHtml}</div>
                    `;
                    flow.appendChild(wrap);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.setReply = (u, m) => {
            replyTo = { to: u, msg: m };
            document.getElementById('reply-box').classList.remove('hidden');
            document.getElementById('reply-msg').innerText = `Replying to @${u}: ${m}`;
            document.getElementById('msg-input').focus();
        };

        window.cancelReply = () => { replyTo = null; document.getElementById('reply-box').classList.add('hidden'); };

        window.react = (msgId, emoji) => {
            const path = (activeC === 'global' ? 'msgs/global/' : `private/${[myN, activeC].sort().join('_')}/`) + msgId + '/reactions/' + emoji;
            const rRef = ref(db, path);
            onValue(rRef, (s) => {
                set(rRef, (s.val() || 0) + 1);
            }, { onlyOnce: true });
        };

        window.delMsg = (id) => {
            if(confirm("Erase from history?")) {
                const p = (activeC === 'global' ? 'msgs/global/' : `private/${[myN, activeC].sort().join('_')}/`) + id;
                remove(ref(db, p));
            }
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('users-list');
                const globalBtn = list.querySelector('div');
                list.innerHTML = ""; list.appendChild(globalBtn);
                s.forEach(c => {
                    if(c.key !== myN) {
                        const d = document.createElement('div');
                        d.className = "flex items-center gap-4 p-4 glass rounded-[1.5rem] cursor-pointer hover:bg-purple-600/10 transition mb-2";
                        d.innerHTML = `<div class="w-10 h-10 rounded-xl bg-slate-800 flex items-center justify-center font-black text-xs border border-white/5">${c.key[0]}</div>
                                       <div class="flex-1 overflow-hidden"><p class="text-sm font-bold text-white truncate">${c.key}</p><p class="text-[9px] text-green-500 font-bold uppercase tracking-widest">Online</p></div>`;
                        d.onclick = () => window.switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        window.filterUsers = () => {
            const q = document.getElementById('user-search').value.toLowerCase();
            document.querySelectorAll('#users-list div').forEach(u => {
                if(u.innerText) u.style.display = u.innerText.toLowerCase().includes(q) ? 'flex' : 'none';
            });
        };

        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim() && activeC) {
                const p = activeC === 'global' ? 'msgs/global' : `private/${[myN, activeC].sort().join('_')}`;
                const m = { sender: myN, text: i.value.trim(), time: Date.now() };
                if(replyTo) { m.reply = replyTo; window.cancelReply(); }
                push(ref(db, p), m);
                i.value = "";
            }
        };

        window.logout = () => { if(confirm("Disconnect Session?")) { remove(ref(db, 'online/' + myN)); localStorage.clear(); location.reload(); } };
    </script>
</body>
</html>
