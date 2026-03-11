<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Prime Ultimate Hub | Professional Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        .glass { background: rgba(255, 255, 255, 0.7); backdrop-filter: blur(15px); }
        .dark-mode { background: #0f172a !important; color: white; }
        .admin-msg { border-left: 4px solid #f59e0b; }
        .chat-scroll::-webkit-scrollbar { width: 5px; }
        .chat-scroll::-webkit-scrollbar-thumb { background: #6366f1; border-radius: 10px; }
        @keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.5; } }
        .typing { animation: pulse 1s infinite; }
    </style>
</head>
<body id="app-body" class="bg-slate-50 h-screen flex overflow-hidden transition-all duration-500">

    <div id="login-overlay" class="fixed inset-0 bg-indigo-900 z-[100] flex items-center justify-center p-4">
        <div class="bg-white rounded-[2.5rem] shadow-2xl p-10 w-full max-w-md text-center">
            <div class="text-6xl mb-4">🚀</div>
            <h1 class="text-3xl font-black text-slate-800 mb-2">Prime Workspace</h1>
            <p class="text-slate-500 mb-8 font-medium">Enter your professional name to start</p>
            <input type="text" id="username-input" placeholder="Your Name..." 
                   class="w-full bg-slate-50 border-2 border-slate-100 p-4 rounded-2xl mb-4 outline-none focus:border-indigo-600 transition-all text-center font-bold">
            <button id="join-btn" class="w-full bg-indigo-600 text-white p-4 rounded-2xl font-black shadow-xl hover:bg-indigo-700 active:scale-95 transition-all">LAUNCH HUB</button>
        </div>
    </div>

    <aside id="sidebar" class="w-72 bg-slate-900 text-white flex flex-col hidden md:flex border-r border-white/5">
        <div class="p-8 flex items-center justify-between">
            <span class="text-2xl font-black tracking-tighter bg-gradient-to-r from-indigo-400 to-cyan-400 bg-clip-text text-transparent">PRIME.</span>
            <button id="theme-btn" class="p-2 rounded-xl bg-white/5 hover:bg-white/10"><i class="fas fa-moon"></i></button>
        </div>

        <div class="flex-1 overflow-y-auto px-4 space-y-8">
            <div>
                <p class="px-4 text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-4">Channels</p>
                <div id="channels" class="space-y-1">
                    <div onclick="switchRoom('general')" class="flex items-center gap-3 p-3 bg-indigo-600 rounded-xl cursor-pointer font-bold"><i class="fas fa-hashtag"></i> general</div>
                    <div onclick="switchRoom('dev')" class="flex items-center gap-3 p-3 hover:bg-white/5 rounded-xl cursor-pointer text-slate-400 transition"><i class="fas fa-code"></i> development</div>
                </div>
            </div>

            <div>
                <p class="px-4 text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-4">Online - <span id="user-count">0</span></p>
                <ul id="online-users" class="space-y-2"></ul>
            </div>
        </div>
        
        <div class="p-6 bg-black/20 border-t border-white/5">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 bg-indigo-500 rounded-xl flex items-center justify-center font-black" id="user-avatar">?</div>
                <div class="overflow-hidden">
                    <p class="text-sm font-bold truncate" id="my-name">Guest</p>
                    <p class="text-[10px] text-green-400 font-bold uppercase tracking-widest">Active Now</p>
                </div>
            </div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col relative">
        <header class="h-20 border-b flex items-center justify-between px-8 bg-white transition-colors">
            <div>
                <h2 id="room-title" class="font-black text-xl text-slate-800 tracking-tight"># general</h2>
                <p id="typing-status" class="text-[10px] font-bold text-indigo-500 uppercase h-4 typing"></p>
            </div>
            <div class="flex gap-4 text-slate-400">
                <i class="fas fa-search cursor-pointer hover:text-indigo-600"></i>
                <i class="fas fa-bell cursor-pointer hover:text-indigo-600"></i>
                <i onclick="location.reload()" class="fas fa-power-off cursor-pointer hover:text-red-500"></i>
            </div>
        </header>

        <div id="chat-box" class="flex-1 overflow-y-auto p-8 space-y-6 chat-scroll bg-slate-50/50"></div>

        <footer class="p-6">
            <div class="max-w-4xl mx-auto flex items-center gap-4 bg-white p-2 rounded-[2rem] shadow-2xl border border-black/5">
                <button class="w-12 h-12 rounded-full text-slate-400 hover:bg-slate-50"><i class="fas fa-plus"></i></button>
                <input type="text" id="msg-input" placeholder="Type a message..." class="flex-1 outline-none font-medium text-slate-700 px-2">
                <button id="mic-btn" class="w-12 h-12 text-slate-400 hover:text-indigo-600"><i class="fas fa-microphone"></i></button>
                <button id="send-btn" class="w-14 h-14 bg-indigo-600 text-white rounded-full shadow-lg shadow-indigo-200 flex items-center justify-center hover:scale-105 active:scale-90 transition-all">
                    <i class="fas fa-paper-plane"></i>
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

        // Admin Secret Key Logic
        const checkAdmin = (name) => {
            if(name.toLowerCase().includes('admin') || name.toLowerCase().includes('nazim')) isAdmin = true;
        };

        // UI Interactions
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
                document.getElementById('my-name').innerText = user.displayName;
                document.getElementById('user-avatar').innerText = user.displayName[0].toUpperCase();
                checkAdmin(user.displayName);
                
                // Presence
                const presenceRef = ref(db, 'online_users/' + user.uid);
                set(presenceRef, { name: user.displayName, avatar: user.displayName[0] });
                onDisconnect(presenceRef).remove();
                
                loadMessages();
            }
        });

        // Online List
        onValue(ref(db, 'online_users'), (snap) => {
            const list = document.getElementById('online-users');
            list.innerHTML = "";
            let count = 0;
            snap.forEach(c => {
                count++;
                list.innerHTML += `<li class="flex items-center gap-3 p-2 hover:bg-white/5 rounded-lg transition">
                    <div class="w-8 h-8 bg-indigo-500/20 text-indigo-400 rounded-lg flex items-center justify-center font-bold text-xs">${c.val().name[0]}</div>
                    <span class="text-sm font-medium text-slate-300">${c.val().name}</span>
                    <div class="w-1.5 h-1.5 bg-green-500 rounded-full ml-auto"></div>
                </li>`;
            });
            document.getElementById('user-count').innerText = count;
        });

        // Chat System
        function loadMessages() {
            const chatBox = document.getElementById('chat-box');
            chatBox.innerHTML = "";
            const msgRef = ref(db, 'messages/' + currentRoom);
            onChildAdded(msgRef, (snap) => {
                const data = snap.val();
                const isMe = data.name === auth.currentUser.displayName;
                const time = data.time ? new Date(data.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) : "Now";
                
                chatBox.innerHTML += `
                    <div class="group flex flex-col ${isMe ? 'items-end' : 'items-start'} animate-in fade-in duration-300">
                        <div class="flex items-center gap-2 mb-1 px-2">
                            <span class="text-[10px] font-black text-slate-400 uppercase tracking-widest">${data.name}</span>
                            <span class="text-[9px] text-slate-300 font-bold">${time}</span>
                        </div>
                        <div class="relative ${isMe ? 'bg-indigo-600 text-white rounded-2xl rounded-tr-none' : 'bg-white text-slate-700 rounded-2xl rounded-tl-none'} p-4 shadow-xl shadow-indigo-500/5 max-w-[85%] border border-black/5">
                            <p class="text-sm font-medium leading-relaxed">${data.text}</p>
                            ${isAdmin ? `<button onclick="deleteMsg('${currentRoom}', '${snap.key}')" class="absolute -left-8 top-2 text-slate-300 hover:text-red-500 opacity-0 group-hover:opacity-100 transition"><i class="fas fa-trash text-xs"></i></button>` : ''}
                        </div>
                    </div>`;
                chatBox.scrollTop = chatBox.scrollHeight;
            });
        }

        window.deleteMsg = (room, key) => {
            if(confirm("Admin: Delete message?")) remove(ref(db, `messages/${room}/${key}`));
        };

        window.switchRoom = (name) => {
            currentRoom = name;
            document.getElementById('room-title').innerText = "# " + name;
            loadMessages();
        };

        document.getElementById('send-btn').onclick = sendMessage;
        document.getElementById('msg-input').onkeypress = (e) => { 
            if(e.key === 'Enter') sendMessage();
            // Typing Indicator Logic
            set(ref(db, 'typing/' + currentRoom), { name: auth.currentUser.displayName });
            setTimeout(() => remove(ref(db, 'typing/' + currentRoom)), 2000);
        };

        onValue(ref(db, 'typing/' + currentRoom), (snap) => {
            const t = snap.val();
            document.getElementById('typing-status').innerText = (t && t.name !== auth.currentUser.displayName) ? `${t.name} is typing...` : '';
        });

        function sendMessage() {
            const input = document.getElementById('msg-input');
            if(input.value.trim()) {
                push(ref(db, 'messages/' + currentRoom), {
                    name: auth.currentUser.displayName,
                    text: input.value,
                    time: serverTimestamp()
                });
                input.value = "";
            }
        }
    </script>
</body>
</html>
