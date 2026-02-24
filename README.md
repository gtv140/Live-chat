<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ChatPro Pro | Private & Business Suite</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #f1f5f9; height: 100dvh; overflow: hidden; }
        .app-container { display: flex; height: 100dvh; width: 100%; }
        
        /* Sidebar & Navigation */
        .sidebar { width: 300px; background: white; border-right: 1px solid #e2e8f0; display: flex; flex-direction: column; transition: 0.3s ease; z-index: 100; }
        .chat-area { flex: 1; display: flex; flex-direction: column; background: #f8fafc; position: relative; }
        
        /* Messages */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 10px; }
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 18px; font-size: 14px; position: relative; }
        .msg.mine { align-self: flex-end; background: #4f46e5; color: white; border-bottom-right-radius: 2px; }
        .msg.theirs { align-self: flex-start; background: white; color: #1e293b; border-bottom-left-radius: 2px; border: 1px solid #e2e8f0; }

        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; }
            .sidebar.active { left: 0; box-shadow: 10px 0 50px rgba(0,0,0,0.1); }
        }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[200] bg-indigo-600 flex items-center justify-center p-6 text-white text-center">
        <div class="bg-white p-10 rounded-[2.5rem] shadow-2xl w-full max-w-sm">
            <h2 class="text-3xl font-extrabold text-slate-900 mb-6">ChatPro <span class="text-indigo-600">v6.0</span></h2>
            <input id="username-input" type="text" placeholder="Apna Name Likhein" class="w-full p-4 bg-slate-100 rounded-2xl mb-4 text-center font-bold text-slate-900 outline-none">
            <button id="join-btn" class="w-full bg-indigo-600 p-4 rounded-2xl font-bold shadow-lg hover:bg-indigo-700">Launch App</button>
        </div>
    </div>

    <div class="app-container">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 border-b flex justify-between items-center">
                <h1 class="font-extrabold text-xl text-indigo-600">Workspace</h1>
                <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div class="p-4 flex-1 overflow-y-auto">
                <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-4">Users (Private Chat)</p>
                <div id="users-list" class="space-y-2"></div>
            </div>
        </aside>

        <main class="chat-area">
            <header class="h-16 bg-white border-b flex items-center justify-between px-6">
                <div class="flex items-center gap-3">
                    <button onclick="toggleSidebar()" class="md:hidden text-slate-500"><i class="fa-solid fa-bars"></i></button>
                    <h2 id="current-chat-title" class="font-bold text-slate-800"># global-hq</h2>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="p-4 bg-white border-t">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-3 bg-slate-100 p-2 rounded-2xl border">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="w-10 h-10 text-slate-400"><i class="fa-solid fa-paperclip"></i></button>
                    <input id="msg-input" type="text" placeholder="Type a message..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium">
                    <button class="bg-indigo-600 text-white w-10 h-10 rounded-xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let myName = "", activeChat = "global";

        window.onload = () => { const s = localStorage.getItem('chatProUser'); if(s) init(s); };
        document.getElementById('join-btn').onclick = () => { 
            const n = document.getElementById('username-input').value.trim(); 
            if(n){ localStorage.setItem('chatProUser', n); init(n); }
        };

        function init(n) {
            myName = n;
            document.getElementById('login-screen').style.display = 'none';
            set(ref(db, 'online/' + n), true);
            loadMessages();
            loadUsers();
        }

        function loadMessages() {
            const path = activeChat === "global" ? 'msgs/global' : `private/${getChatId(myName, activeChat)}`;
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, path), (snap) => {
                const data = snap.val();
                const div = document.createElement('div');
                div.className = `msg ${data.sender === myName ? 'mine' : 'theirs'}`;
                let content = data.text || "";
                if(data.fileUrl) content = `<img src="${data.fileUrl}" class="rounded-lg max-w-[200px]">`;
                div.innerHTML = `<p class="text-[9px] font-bold opacity-50 mb-1">${data.sender}</p><div>${content}</div>`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        function loadUsers() {
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = `<div onclick="switchChat('global')" class="p-3 bg-indigo-50 rounded-xl cursor-pointer font-bold text-indigo-600"># Global Group</div>`;
                snap.forEach(c => {
                    if(c.key !== myName) {
                        const d = document.createElement('div');
                        d.className = "p-3 hover:bg-slate-50 rounded-xl cursor-pointer flex items-center gap-3 font-medium transition";
                        d.innerHTML = `<div class="w-8 h-8 rounded-lg bg-slate-200 flex items-center justify-center text-xs">${c.key[0]}</div> ${c.key}`;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        window.switchChat = (user) => {
            activeChat = user;
            document.getElementById('current-chat-title').innerText = user === "global" ? "# global-hq" : `@ ${user}`;
            loadMessages();
            if(window.innerWidth < 768) toggleSidebar();
        };

        window.uploadFile = async (input) => {
            const file = input.files[0];
            if(!file) return;
            const r = sRef(storage, `files/${Date.now()}_${file.name}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            const path = activeChat === "global" ? 'msgs/global' : `private/${getChatId(myName, activeChat)}`;
            push(ref(db, path), { sender: myName, fileUrl: url, time: Date.now() });
        };

        function getChatId(u1, u2) { return [u1, u2].sort().join('_'); }
        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                const path = activeChat === "global" ? 'msgs/global' : `private/${getChatId(myName, activeChat)}`;
                push(ref(db, path), { sender: myName, text: i.value, time: Date.now() });
                i.value = ""; 
            }
        };
    </script>
</body>
</html>
