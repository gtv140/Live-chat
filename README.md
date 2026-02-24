<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro | Elegant Business Studio</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #f8fafc; }
        
        /* Modern Scrollbar */
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: #e2e8f0; border-radius: 20px; }

        .chat-container { height: calc(100vh - 160px); }
        
        /* Premium Bubbles */
        .msg-bubble-mine {
            background: linear-gradient(135deg, #4f46e5 0%, #3b82f6 100%);
            color: white;
            border-radius: 1.25rem 1.25rem 0.2rem 1.25rem;
            box-shadow: 0 4px 15px rgba(59, 130, 246, 0.2);
        }
        .msg-bubble-theirs {
            background: white;
            color: #1e293b;
            border-radius: 1.25rem 1.25rem 1.25rem 0.2rem;
            border: 1px solid #f1f5f9;
            box-shadow: 0 2px 5px rgba(0,0,0,0.02);
        }
        
        .sidebar-item { transition: all 0.2s ease; border-radius: 12px; }
        .sidebar-item:hover { background: #f1f5f9; transform: translateX(5px); }
        .sidebar-item.active { background: #eef2ff; color: #4f46e5; }

        .input-glass {
            background: rgba(255, 255, 255, 0.8);
            backdrop-filter: blur(10px);
            border: 1px solid #e2e8f0;
        }
    </style>
</head>
<body class="h-screen flex overflow-hidden">

    <div id="login-screen" class="fixed inset-0 z-[100] bg-white flex items-center justify-center p-6">
        <div class="w-full max-w-sm text-center">
            <div class="mb-8 inline-flex items-center justify-center w-16 h-16 rounded-2xl bg-indigo-600 text-white shadow-xl shadow-indigo-200">
                <i class="fa-solid fa-bolt-lightning text-2xl"></i>
            </div>
            <h1 class="text-3xl font-bold text-slate-900 mb-2 tracking-tight">Welcome Back</h1>
            <p class="text-slate-500 mb-10 text-sm">Enter your alias to join the workspace.</p>
            <input id="username-input" type="text" placeholder="e.g. alex_pro" class="w-full bg-slate-50 border border-slate-200 rounded-xl p-4 mb-4 outline-none focus:ring-2 focus:ring-indigo-500/20 focus:border-indigo-500 transition-all text-center font-medium">
            <button id="join-btn" class="w-full bg-slate-900 hover:bg-black text-white font-semibold py-4 rounded-xl transition-all active:scale-95 shadow-lg">Enter Workspace</button>
        </div>
    </div>

    <div id="main-app" class="hidden flex w-full h-full">
        <aside class="w-72 bg-white border-r border-slate-100 flex flex-col">
            <div class="p-8">
                <div class="flex items-center gap-3 mb-10">
                    <div class="w-8 h-8 bg-indigo-600 rounded-lg flex items-center justify-center text-white text-xs"><i class="fa-solid fa-leaf"></i></div>
                    <span class="font-bold text-lg tracking-tight text-slate-800">ChatPro</span>
                </div>

                <nav class="space-y-6">
                    <div>
                        <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-4 ml-2">Main Channel</p>
                        <div onclick="switchRoom('global')" id="room-global" class="sidebar-item active p-3 cursor-pointer flex items-center gap-3">
                            <i class="fa-solid fa-hashtag opacity-50"></i> <span class="text-sm font-semibold">general-chat</span>
                        </div>
                    </div>

                    <div>
                        <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-4 ml-2">Team Members</p>
                        <div id="users-list" class="space-y-1"></div>
                    </div>
                </nav>
            </div>

            <div class="mt-auto p-6 border-t border-slate-50">
                <div class="flex items-center justify-between">
                    <div class="flex items-center gap-3">
                        <div id="my-avatar" class="w-10 h-10 rounded-full bg-indigo-100 text-indigo-600 flex items-center justify-center font-bold text-sm">?</div>
                        <div class="max-w-[100px]">
                            <p id="my-name" class="text-xs font-bold text-slate-800 truncate">Username</p>
                            <p class="text-[10px] text-green-500 font-medium">Online</p>
                        </div>
                    </div>
                    <button onclick="logout()" class="text-slate-300 hover:text-red-500 transition"><i class="fa-solid fa-arrow-right-from-bracket"></i></button>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col bg-slate-50/50">
            <header class="h-20 bg-white/80 backdrop-blur-md border-b border-slate-100 flex items-center justify-between px-8">
                <div>
                    <h2 id="chat-title" class="font-bold text-slate-800 tracking-tight"># general-chat</h2>
                    <p id="typing-status" class="text-[10px] text-indigo-500 font-medium h-3"></p>
                </div>
                <div class="flex gap-4 items-center">
                    <div class="flex -space-x-2">
                        <div class="w-7 h-7 rounded-full border-2 border-white bg-slate-200"></div>
                        <div class="w-7 h-7 rounded-full border-2 border-white bg-slate-300 flex items-center justify-center text-[8px] font-bold">+5</div>
                    </div>
                </div>
            </header>

            <div id="chat-flow" class="flex-1 overflow-y-auto p-8 space-y-6 chat-container"></div>

            <div class="p-6">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-3 input-glass p-2 rounded-2xl shadow-sm">
                    <input type="file" id="file-input" class="hidden">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="w-10 h-10 rounded-xl hover:bg-slate-100 text-slate-400 transition flex items-center justify-center">
                        <i class="fa-solid fa-paperclip"></i>
                    </button>
                    
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Message #general-chat..." class="flex-1 bg-transparent p-2 outline-none text-sm text-slate-700 font-medium">
                    
                    <button id="send-btn" class="bg-indigo-600 hover:bg-indigo-700 text-white w-10 h-10 rounded-xl flex items-center justify-center shadow-lg shadow-indigo-100 transition-all active:scale-90">
                        <i class="fa-solid fa-paper-plane text-xs"></i>
                    </button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, serverTimestamp, remove, update } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        // (Use your same Firebase Config here)
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
        let myName = "";
        let currentRoom = "global";

        // APP START & PERSISTENCE
        window.onload = () => {
            const saved = localStorage.getItem('chatProUser');
            if (saved) initApp(saved);
        };

        document.getElementById('join-btn').onclick = () => {
            const name = document.getElementById('username-input').value.trim();
            if (name.length > 2) {
                localStorage.setItem('chatProUser', name);
                initApp(name);
            }
        };

        function initApp(name) {
            myName = name;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('main-app').classList.remove('hidden');
            document.getElementById('my-name').innerText = myName;
            document.getElementById('my-avatar').innerText = myName[0].toUpperCase();
            set(ref(db, 'online/' + myName), { status: 'online' });
            startCore();
        }

        function startCore() {
            // Typing Logic
            document.getElementById('msg-input').oninput = () => {
                set(ref(db, `typing/${currentRoom}/${myName}`), true);
                setTimeout(() => set(ref(db, `typing/${currentRoom}/${myName}`), false), 2000);
            };
            onValue(ref(db, `typing/${currentRoom}`), (snap) => {
                let typers = [];
                snap.forEach(c => { if(c.val() === true && c.key !== myName) typers.push(c.key) });
                document.getElementById('typing-status').innerText = typers.length ? typers.join(', ') + ' is typing...' : '';
            });

            // Online Users
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    if(c.key !== myName) {
                        const div = document.createElement('div');
                        div.className = "sidebar-item p-3 cursor-pointer flex items-center gap-3";
                        div.innerHTML = `<div class="w-6 h-6 rounded-full bg-slate-100 flex items-center justify-center text-[10px] font-bold">${c.key[0]}</div><span class="text-sm font-medium">${c.key}</span>`;
                        div.onclick = () => switchRoom(c.key, true);
                        list.appendChild(div);
                    }
                });
            });
            loadMessages("global");
        }

        window.switchRoom = (target, isPrivate = false) => {
            currentRoom = !isPrivate ? 'global' : (myName < target ? myName + "_" + target : target + "_" + myName);
            document.getElementById('chat-title').innerText = !isPrivate ? "# general-chat" : "# " + target;
            loadMessages(currentRoom);
        };

        function loadMessages(room) {
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, 'msgs/' + room), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const div = document.createElement('div');
                div.className = `flex ${isMine ? 'justify-end' : 'justify-start'}`;
                
                let body = data.fileUrl ? 
                    (data.type === 'image' ? `<img src="${data.fileUrl}" class="rounded-xl max-w-sm border shadow-sm">` : `<a href="${data.fileUrl}" target="_blank" class="flex items-center gap-2 text-xs underline font-bold"><i class="fa-solid fa-file"></i> ${data.fileName}</a>`) : 
                    `<p class="text-sm font-medium leading-relaxed">${data.text}</p>`;

                div.innerHTML = `
                    <div class="max-w-[70%] ${isMine ? 'msg-bubble-mine' : 'msg-bubble-theirs'} p-4">
                        <p class="text-[9px] font-bold uppercase tracking-widest opacity-50 mb-1">${isMine ? 'You' : data.sender}</p>
                        ${body}
                        <p class="text-[8px] mt-1 opacity-40 text-right">${new Date(data.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</p>
                    </div>`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const inp = document.getElementById('msg-input');
            if(inp.value.trim()){
                push(ref(db, 'msgs/' + currentRoom), { sender: myName, text: inp.value, time: Date.now() });
                inp.value = "";
            }
        };

        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };
    </script>
</body>
</html>
