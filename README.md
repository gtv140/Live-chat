<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Prime Hub | Live Connect 🟢</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; transition: all 0.4s ease; }
        .glass-sidebar { background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(10px); }
        .chat-area { background: #f8fafc; }
        .dark-mode .chat-area { background: #0f172a; color: white; }
        .dark-mode header, .dark-mode footer { background: #1e293b !important; border-color: #334155 !important; }
        .dark-mode #msg-input { color: white; }
        .typing-dots { animation: pulse 1.5s infinite; }
        @keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.3; } }
        .chat-scroll::-webkit-scrollbar { width: 5px; }
        .chat-scroll::-webkit-scrollbar-thumb { background: #6366f1; border-radius: 10px; }
    </style>
</head>
<body id="app-body" class="h-screen flex overflow-hidden">

    <div id="login-overlay" class="fixed inset-0 bg-slate-900 z-[100] flex items-center justify-center p-4">
        <div class="bg-white rounded-[3rem] shadow-2xl p-10 w-full max-w-md text-center transform scale-100 transition-transform">
            <div class="text-6xl mb-6">🚀</div>
            <h1 class="text-4xl font-black text-slate-800 mb-2 tracking-tighter">Prime Hub</h1>
            <p class="text-slate-500 mb-8 font-semibold uppercase text-xs tracking-widest">Enterprise Workspace</p>
            <input type="text" id="username-input" placeholder="Your Full Name..." 
                   class="w-full bg-slate-50 border-2 border-slate-100 p-5 rounded-3xl mb-4 outline-none focus:border-indigo-600 transition-all text-center font-bold text-lg">
            <button id="join-btn" class="w-full bg-indigo-600 text-white p-5 rounded-3xl font-black shadow-2xl shadow-indigo-200 hover:bg-indigo-700 active:scale-95 transition-all uppercase tracking-widest">Connect Now</button>
        </div>
    </div>

    <aside id="sidebar" class="w-80 glass-sidebar text-white flex flex-col hidden md:flex">
        <div class="p-8 flex items-center justify-between border-b border-white/5">
            <span class="text-3xl font-black tracking-tighter bg-gradient-to-r from-indigo-400 to-cyan-400 bg-clip-text text-transparent">PRIME.</span>
            <button id="theme-btn" class="w-10 h-10 rounded-2xl bg-white/5 hover:bg-white/10 flex items-center justify-center transition"><i class="fas fa-moon"></i></button>
        </div>

        <div class="flex-1 overflow-y-auto p-6 space-y-10 chat-scroll">
            <div>
                <p class="px-4 text-[10px] font-black text-indigo-400 uppercase tracking-[0.2em] mb-4">Channels</p>
                <div id="channel-list" class="space-y-2">
                    <div onclick="switchRoom('general')" id="chan-general" class="flex items-center gap-3 p-4 bg-indigo-600 rounded-2xl cursor-pointer font-bold shadow-lg shadow-indigo-900/50"><i class="fas fa-hashtag"></i> general</div>
                    <div onclick="switchRoom('announcements')" id="chan-announcements" class="flex items-center gap-3 p-4 hover:bg-white/5 rounded-2xl cursor-pointer text-slate-400 transition font-bold"><i class="fas fa-bullhorn"></i> announcements</div>
                </div>
            </div>

            <div>
                <p class="px-4 text-[10px] font-black text-indigo-400 uppercase tracking-[0.2em] mb-4">Active Members — <span id="user-count" class="text-white">0</span></p>
                <ul id="online-users" class="space-y-3"></ul>
            </div>
        </div>

        <div class="p-8 bg-black/20 border-t border-white/5">
            <div class="flex items-center gap-4">
                <div class="w-12 h-12 bg-gradient-to-br from-indigo-500 to-purple-600 rounded-2xl flex items-center justify-center font-black text-xl shadow-lg" id="my-avatar">?</div>
                <div class="overflow-hidden">
                    <p class="text-base font-black truncate" id="my-display-name">Guest User</p>
                    <p class="text-[10px] text-cyan-400 font-black uppercase tracking-widest flex items-center gap-1"><span class="w-1.5 h-1.5 bg-cyan-400 rounded-full animate-pulse"></span> Connected</p>
                </div>
            </div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col chat-area relative">
        <header class="h-24 border-b border-slate-200 flex items-center justify-between px-10 bg-white/80 backdrop-blur-md">
            <div>
                <h2 id="room-title" class="font-black text-2xl text-slate-800 tracking-tight uppercase"># GENERAL</h2>
                <div id="typing-status" class="text-[10px] font-black text-indigo-500 uppercase h-4 typing-dots"></div>
            </div>
            <div class="flex gap-6 items-center">
                <i class="fas fa-video text-slate-400 cursor-pointer hover:text-indigo-600 text-lg transition"></i>
                <i onclick="location.reload()" class="fas fa-power-off text-slate-300 cursor-pointer hover:text-red-500 text-lg transition"></i>
            </div>
        </header>

        <div id="chat-box" class="flex-1 overflow-y-auto p-10 space-y-8 chat-scroll"></div>

        <footer class="p-8 bg-white/50 border-t border-slate-200">
            <div id="input-bar" class="max-w-5xl mx-auto flex items-center gap-4 bg-white p-3 rounded-[2.5rem] shadow-2xl shadow-indigo-500/10 border border-slate-100">
                <button class="w-12 h-12 rounded-full text-slate-400 hover:bg-slate-50 transition"><i class="fas fa-plus-circle text-xl"></i></button>
                <input type="text" id="msg-input" placeholder="Compose a brilliant message..." class="flex-1 outline-none font-bold text-slate-600 px-4 bg-transparent text-lg">
                <button id="send-btn" class="w-16 h-16 bg-indigo-600 text-white rounded-full shadow-2xl shadow-indigo-400 flex items-center justify-center hover:scale-105 hover:bg-indigo-700 active:scale-90 transition-all">
                    <i class="fas fa-paper-plane text-xl"></i>
                </button>
            </div>
        </footer>
    </main>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getAuth, signInAnonymously, updateProfile, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
        import { getDatabase, ref, push, onChildAdded, onValue, serverTimestamp, onDisconnect, set, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
        const auth = getAuth(app);
        const db = getDatabase(app);

        let currentRoom = 'general';
        let isAdmin = false;
        let unread = 0;
        let isFocused = true;

        // --- LIVE TITLE SYSTEM ---
        const updateTitle = () => {
            const status = navigator.onLine ? "🟢" : "🔴";
            const notify = unread > 0 ? `(${unread}) ` : "";
            document.title = `${notify}Prime Hub | #${currentRoom} ${status}`;
        };

        window.onfocus = () => { isFocused = true; unread = 0; updateTitle(); };
        window.onblur = () => { isFocused = false; };

        // --- THEME SYSTEM ---
        document.getElementById('theme-btn').onclick = () => {
            document.getElementById('app-body').classList.toggle('dark-mode');
        };

        // --- AUTH & LOGIN ---
        document.getElementById('join-btn').onclick = async () => {
            const name = document.getElementById('username-input').value.trim();
            if(!name) return;
            const cred = await signInAnonymously(auth);
            await updateProfile(cred.user, { displayName: name });
            location.reload();
        };

        onAuthStateChanged(auth, (user) => {
            if (user && user.displayName) {
                document.getElementById('login-overlay').classList.add('hidden');
                document.getElementById('my-display-name').innerText = user.displayName;
                document.getElementById('my-avatar').innerText = user.displayName[0].toUpperCase();
                
                if(user.displayName.toLowerCase().includes('admin') || user.displayName.toLowerCase().includes('nazim')) isAdmin = true;

                // Presence Management
                const presenceRef = ref(db, 'online_users/' + user.uid);
                set(presenceRef, { name: user.displayName, joined: serverTimestamp() });
                onDisconnect(presenceRef).remove();
                
                loadMessages();
            }
        });

        // --- REAL-TIME ACTIVE USERS ---
        onValue(ref(db, 'online_users'), (snapshot) => {
            const list = document.getElementById('online-users');
            list.innerHTML = "";
            let count = 0;
            snapshot.forEach(snap => {
                count++;
                const u = snap.val();
                list.innerHTML += `
                    <li class="flex items-center gap-4 p-3 hover:bg-white/5 rounded-2xl transition group">
                        <div class="w-10 h-10 bg-indigo-500/10 text-indigo-400 rounded-xl flex items-center justify-center font-black text-sm border border-indigo-500/20">${u.name[0]}</div>
                        <span class="text-sm font-bold text-slate-300 group-hover:text-white transition">${u.name}</span>
                        <div class="w-2 h-2 bg-green-500 rounded-full ml-auto shadow-[0_0_10px_#22c55e]"></div>
                    </li>`;
            });
            document.getElementById('user-count').innerText = count;
        });

        // --- CHAT & MESSAGES ---
        function loadMessages() {
            const chatBox = document.getElementById('chat-box');
            chatBox.innerHTML = "";
            const msgRef = ref(db, 'messages/' + currentRoom);
            
            onChildAdded(msgRef, (snap) => {
                const data = snap.val();
                const isMe = data.name === auth.currentUser.displayName;
                const time = data.time ? new Date(data.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) : "Now";

                if(!isFocused && !isMe) { unread++; updateTitle(); }

                const msgHtml = `
                    <div class="flex flex-col ${isMe ? 'items-end' : 'items-start'} group">
                        <div class="flex items-center gap-3 mb-2 px-2">
                            <span class="text-[10px] font-black text-slate-400 uppercase tracking-widest">${data.name}</span>
                            <span class="text-[10px] text-slate-300 font-bold bg-slate-100 dark:bg-slate-800 px-2 py-0.5 rounded-md">${time}</span>
                        </div>
                        <div class="relative ${isMe ? 'bg-indigo-600 text-white rounded-3xl rounded-tr-none shadow-indigo-200' : 'bg-white text-slate-700 rounded-3xl rounded-tl-none border-slate-100'} p-5 shadow-2xl max-w-[85%] border transition-all hover:scale-[1.01]">
                            <p class="text-[15px] font-semibold leading-relaxed">${data.text}</p>
                            ${isAdmin ? `<button onclick="adminDelete('${currentRoom}', '${snap.key}')" class="absolute -left-10 top-1/2 -translate-y-1/2 text-slate-300 hover:text-red-500 opacity-0 group-hover:opacity-100 transition"><i class="fas fa-trash-alt"></i></button>` : ''}
                        </div>
                    </div>`;
                chatBox.insertAdjacentHTML('beforeend', msgHtml);
                chatBox.scrollTop = chatBox.scrollHeight;
            });
        }

        // --- ADMIN POWERS ---
        window.adminDelete = (room, key) => {
            if(confirm("Admin: Permanently delete this message, sweetie?")) remove(ref(db, `messages/${room}/${key}`));
        };

        // --- ROOM SWITCHING ---
        window.switchRoom = (name) => {
            currentRoom = name;
            document.getElementById('room-title').innerText = "# " + name;
            document.querySelectorAll('#channel-list div').forEach(el => el.classList.remove('bg-indigo-600', 'shadow-lg', 'font-black'));
            document.getElementById('chan-' + name).classList.add('bg-indigo-600', 'font-black');
            updateTitle();
            loadMessages();
        };

        // --- INPUT & TYPING INDICATOR ---
        const input = document.getElementById('msg-input');
        const send = document.getElementById('send-btn');

        const doSend = () => {
            if(input.value.trim()) {
                push(ref(db, 'messages/' + currentRoom), {
                    name: auth.currentUser.displayName,
                    text: input.value,
                    time: serverTimestamp()
                });
                input.value = "";
                remove(ref(db, 'typing/' + currentRoom));
            }
        };

        send.onclick = doSend;
        input.onkeydown = (e) => {
            if(e.key === 'Enter') doSend();
            set(ref(db, 'typing/' + currentRoom), { name: auth.currentUser.displayName });
            clearTimeout(window.tOut);
            window.tOut = setTimeout(() => remove(ref(db, 'typing/' + currentRoom)), 2000);
        };

        onValue(ref(db, 'typing/' + currentRoom), (snap) => {
            const t = snap.val();
            const el = document.getElementById('typing-status');
            el.innerText = (t && t.name !== auth.currentUser.displayName) ? `${t.name} is typing...` : '';
        });

        updateTitle();
    </script>
</body>
</html>
