<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Prime Ultimate Hub | Business Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;800&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .glass { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(12px); }
        .dark-mode { background-color: #0f172a !important; color: white !important; }
        .dark-sidebar { background-color: #1e293b !important; }
        .chat-container::-webkit-scrollbar { width: 4px; }
        .chat-container::-webkit-scrollbar-thumb { background: #6366f1; border-radius: 10px; }
        .typing-animate { animation: pulse 1.5s infinite; }
        @keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.3; } }
    </style>
</head>
<body id="app-body" class="bg-slate-100 h-screen flex overflow-hidden transition-all duration-500">

    <div id="login-overlay" class="fixed inset-0 bg-indigo-950 z-[100] flex items-center justify-center p-4">
        <div class="bg-white rounded-[2.5rem] shadow-2xl p-10 w-full max-w-md text-center">
            <div class="text-6xl mb-4">🚀</div>
            <h1 class="text-3xl font-black text-slate-800 mb-2 tracking-tight">Prime Workspace</h1>
            <p class="text-slate-400 mb-8 font-medium">Join your professional team chat</p>
            <input type="text" id="username-input" placeholder="Enter Your Name..." 
                   class="w-full bg-slate-50 border-2 border-slate-100 p-4 rounded-2xl mb-4 outline-none focus:border-indigo-600 transition-all text-center font-bold">
            <button id="join-btn" class="w-full bg-indigo-600 text-white p-4 rounded-2xl font-black shadow-xl hover:bg-indigo-700 active:scale-95 transition-all">START CHATTING</button>
        </div>
    </div>

    <aside id="sidebar" class="w-72 bg-slate-900 text-white flex flex-col hidden md:flex border-r border-white/5">
        <div class="p-8 flex items-center justify-between">
            <span class="text-2xl font-black tracking-tighter bg-gradient-to-r from-indigo-400 to-cyan-400 bg-clip-text text-transparent">PRIME.</span>
            <button id="theme-toggle" class="p-2 rounded-xl bg-white/5 hover:bg-white/10"><i class="fas fa-moon"></i></button>
        </div>

        <div class="flex-1 overflow-y-auto px-4 space-y-8">
            <div>
                <p class="px-4 text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-4">Channels</p>
                <div id="channel-list" class="space-y-1">
                    <div onclick="switchRoom('general')" id="chan-general" class="flex items-center gap-3 p-3 bg-indigo-600 rounded-xl cursor-pointer font-bold transition"><i class="fas fa-hashtag"></i> general</div>
                    <div onclick="switchRoom('marketing')" id="chan-marketing" class="flex items-center gap-3 p-3 hover:bg-white/5 rounded-xl cursor-pointer text-slate-400 transition"><i class="fas fa-bullhorn"></i> marketing</div>
                </div>
            </div>

            <div>
                <p class="px-4 text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-4">Active Now — <span id="user-count">0</span></p>
                <ul id="online-users" class="space-y-2"></ul>
            </div>
        </div>

        <div class="p-6 bg-black/20 border-t border-white/5">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 bg-indigo-500 rounded-xl flex items-center justify-center font-black" id="my-avatar">?</div>
                <div>
                    <p class="text-sm font-bold" id="my-display-name">Guest</p>
                    <p class="text-[9px] text-green-400 font-bold uppercase tracking-widest">● Online</p>
                </div>
            </div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col relative glass">
        <header id="chat-header" class="h-20 border-b flex items-center justify-between px-8 bg-white/50">
            <div>
                <h2 id="room-title" class="font-black text-xl text-slate-800 tracking-tight uppercase"># general</h2>
                <p id="typing-text" class="text-[10px] font-bold text-indigo-500 uppercase h-4 typing-animate"></p>
            </div>
            <div class="flex gap-5 text-slate-400">
                <i class="fas fa-video cursor-pointer hover:text-indigo-600 transition"></i>
                <i class="fas fa-info-circle cursor-pointer hover:text-indigo-600 transition"></i>
            </div>
        </header>

        <div id="chat-box" class="flex-1 overflow-y-auto p-8 space-y-6 chat-container"></div>

        <footer class="p-6">
            <div id="input-container" class="max-w-4xl mx-auto flex items-center gap-4 bg-white p-3 rounded-[2rem] shadow-2xl shadow-indigo-500/10 border border-black/5">
                <label class="w-10 h-10 flex items-center justify-center cursor-pointer text-slate-400 hover:text-indigo-600">
                    <i class="fas fa-paperclip"></i>
                    <input type="file" id="file-up" class="hidden">
                </label>
                <input type="text" id="msg-input" placeholder="Type a message..." class="flex-1 outline-none font-semibold text-slate-600 px-2 bg-transparent">
                <button id="send-btn" class="w-14 h-14 bg-indigo-600 text-white rounded-full shadow-lg flex items-center justify-center hover:scale-105 active:scale-90 transition-all">
                    <i class="fas fa-paper-plane"></i>
                </button>
            </div>
        </footer>
    </main>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getAuth, signInAnonymously, updateProfile, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
        import { getDatabase, ref, push, onChildAdded, onValue, serverTimestamp, onDisconnect, set, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        // CONFIG (Your Provided Config)
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

        // THEME TOGGLE
        document.getElementById('theme-toggle').onclick = () => {
            document.getElementById('app-body').classList.toggle('dark-mode');
            document.getElementById('sidebar').classList.toggle('dark-sidebar');
        };

        // LOGIN LOGIC
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
                
                // Admin Check
                if(user.displayName.toLowerCase().includes('admin') || user.displayName.toLowerCase().includes('nazim')) isAdmin = true;

                // PRESENCE: Start Tracking
                const presenceRef = ref(db, 'online_users/' + user.uid);
                set(presenceRef, { name: user.displayName, last_active: serverTimestamp() });
                onDisconnect(presenceRef).remove();
                
                loadMessages();
            }
        });

        // LIVE ACTIVE USERS LIST
        onValue(ref(db, 'online_users'), (snapshot) => {
            const userListUI = document.getElementById('online-users');
            userListUI.innerHTML = "";
            let count = 0;
            snapshot.forEach(snap => {
                count++;
                const u = snap.val();
                userListUI.innerHTML += `
                    <li class="flex items-center gap-3 p-2 hover:bg-white/5 rounded-xl transition">
                        <div class="w-8 h-8 bg-indigo-500/20 text-indigo-400 rounded-lg flex items-center justify-center font-bold text-xs uppercase">${u.name[0]}</div>
                        <span class="text-sm font-medium text-slate-300">${u.name}</span>
                        <div class="w-1.5 h-1.5 bg-green-500 rounded-full ml-auto shadow-[0_0_8px_#22c55e]"></div>
                    </li>`;
            });
            document.getElementById('user-count').innerText = count;
        });

        // CHAT LOGIC
        function loadMessages() {
            const chatBox = document.getElementById('chat-box');
            chatBox.innerHTML = "";
            const msgRef = ref(db, 'messages/' + currentRoom);
            
            onChildAdded(msgRef, (snap) => {
                const data = snap.val();
                const isMe = data.name === auth.currentUser.displayName;
                const time = data.time ? new Date(data.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) : "Now";

                const msgHtml = `
                    <div class="flex flex-col ${isMe ? 'items-end' : 'items-start'} group">
                        <div class="flex items-center gap-2 mb-1 px-2">
                            <span class="text-[10px] font-black text-slate-400 uppercase tracking-widest">${data.name}</span>
                            <span class="text-[9px] text-slate-300 font-bold">${time}</span>
                        </div>
                        <div class="relative ${isMe ? 'bg-indigo-600 text-white rounded-2xl rounded-tr-none' : 'bg-white text-slate-700 rounded-2xl rounded-tl-none'} p-4 shadow-xl border border-black/5 max-w-[80%]">
                            <p class="text-sm font-medium leading-relaxed">${data.text}</p>
                            ${isAdmin ? `<button onclick="deleteMessage('${currentRoom}', '${snap.key}')" class="absolute -left-8 top-1/2 -translate-y-1/2 text-slate-300 hover:text-red-500 opacity-0 group-hover:opacity-100 transition"><i class="fas fa-trash-alt"></i></button>` : ''}
                        </div>
                    </div>`;
                chatBox.insertAdjacentHTML('beforeend', msgHtml);
                chatBox.scrollTop = chatBox.scrollHeight;
            });
        }

        // ADMIN DELETE FUNCTION
        window.deleteMessage = (room, key) => {
            if(confirm("Delete this message, sweetie?")) remove(ref(db, `messages/${room}/${key}`));
        };

        // CHANNEL SWITCHING
        window.switchRoom = (name) => {
            currentRoom = name;
            document.getElementById('room-title').innerText = "# " + name;
            document.querySelectorAll('#channel-list div').forEach(el => el.classList.remove('bg-indigo-600', 'font-bold'));
            document.getElementById('chan-' + name).classList.add('bg-indigo-600', 'font-bold');
            loadMessages();
        };

        // SENDING MESSAGES & TYPING
        const msgInput = document.getElementById('msg-input');
        const sendBtn = document.getElementById('send-btn');

        const sendMessage = () => {
            if(msgInput.value.trim()) {
                push(ref(db, 'messages/' + currentRoom), {
                    name: auth.currentUser.displayName,
                    text: msgInput.value,
                    time: serverTimestamp()
                });
                msgInput.value = "";
                remove(ref(db, 'typing/' + currentRoom)); // Stop typing on send
            }
        };

        sendBtn.onclick = sendMessage;
        msgInput.onkeydown = (e) => {
            if(e.key === 'Enter') sendMessage();
            // Typing Indicator
            set(ref(db, 'typing/' + currentRoom), { name: auth.currentUser.displayName });
            clearTimeout(window.typingTimeout);
            window.typingTimeout = setTimeout(() => remove(ref(db, 'typing/' + currentRoom)), 2000);
        };

        // LISTEN FOR TYPING
        onValue(ref(db, 'typing/' + currentRoom), (snap) => {
            const t = snap.val();
            const status = document.getElementById('typing-text');
            if(t && t.name !== auth.currentUser.displayName) {
                status.innerText = t.name + " is typing...";
            } else {
                status.innerText = "";
            }
        });

    </script>
</body>
</html>
