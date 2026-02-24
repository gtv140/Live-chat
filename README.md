<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ChatPro Stable v12</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        
        body, html { 
            height: 100%; margin: 0; padding: 0; overflow: hidden;
            background: #0f172a; font-family: 'Plus Jakarta Sans', sans-serif;
        }

        /* App Layout */
        .app-wrapper { display: flex; height: 100vh; width: 100%; position: relative; }

        /* Sidebar Fix */
        .sidebar { 
            width: 280px; background: #1e293b; border-right: 1px solid rgba(255,255,255,0.1);
            display: flex; flex-direction: column; transition: 0.3s; z-index: 100;
        }

        /* Chat Body Fix (Scrollable Area) */
        .chat-section { 
            flex: 1; display: flex; flex-direction: column; position: relative; 
            background: #0f172a; height: 100%;
        }

        #chat-flow { 
            flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 100px; /* Space for input */
            display: flex; flex-direction: column; gap: 15px;
        }

        /* Message Bubbles */
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 18px; color: white; position: relative; }
        .mine { align-self: flex-end; background: #6366f1; border-bottom-right-radius: 2px; }
        .theirs { align-self: flex-start; background: #334155; border-bottom-left-radius: 2px; }

        /* INPUT AREA FIXED (Ye gayab nahi hoga) */
        .bottom-bar {
            position: absolute; bottom: 0; left: 0; right: 0;
            background: #1e293b; padding: 15px; border-top: 1px solid rgba(255,255,255,0.1);
            z-index: 10;
        }

        .input-pill {
            display: flex; align-items: center; gap: 10px; background: #0f172a;
            padding: 5px 15px; border-radius: 50px; border: 1px solid #4f46e5;
        }

        input { background: transparent; border: none; color: white; padding: 10px; flex: 1; outline: none; font-size: 16px; }

        .send-btn { 
            background: #6366f1; color: white; width: 45px; height: 45px; 
            border-radius: 50%; display: flex; align-items: center; justify-content: center;
            box-shadow: 0 4px 10px rgba(99, 102, 241, 0.4);
        }

        /* Mobile Adjustments */
        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; }
            .sidebar.active { left: 0; }
        }

        /* Status & Icons */
        .online-dot { width: 10px; height: 10px; background: #22c55e; border-radius: 50%; display: inline-block; margin-right: 5px; }
        .indicator { color: #818cf8; font-size: 12px; height: 20px; padding: 0 20px; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[200] bg-[#0f172a] flex items-center justify-center p-6">
        <div class="bg-[#1e293b] p-8 rounded-3xl shadow-2xl w-full max-w-sm text-center border border-indigo-500/30">
            <h2 class="text-3xl font-bold text-white mb-6">ChatPro <span class="text-indigo-500">v12</span></h2>
            <input id="username-input" type="text" placeholder="Apna Naam Likhein" class="w-full p-4 bg-slate-900 rounded-xl mb-4 text-white text-center font-bold border border-slate-700">
            <button id="join-btn" class="w-full bg-indigo-600 text-white font-bold py-4 rounded-xl shadow-lg">Login Karein</button>
        </div>
    </div>

    <div class="app-wrapper">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 border-b border-white/10 flex justify-between items-center text-white">
                <span class="font-bold text-xl">Workspace</span>
                <button onclick="toggleSidebar()" class="md:hidden"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div class="flex-1 overflow-y-auto p-4">
                <div onclick="switchChat('global')" id="global-tab" class="p-3 bg-indigo-600/20 text-indigo-400 rounded-xl cursor-pointer font-bold mb-4"># Global Group</div>
                <p class="text-[10px] text-slate-500 uppercase font-bold mb-2">Direct Messages</p>
                <div id="users-list" class="space-y-1"></div>
            </div>
        </aside>

        <main class="chat-section">
            <header class="h-16 bg-[#1e293b] border-b border-white/10 flex items-center justify-between px-6 shrink-0">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-white"><i class="fa-solid fa-bars"></i></button>
                    <h2 id="chat-title" class="text-white font-bold text-sm"># Global Group</h2>
                </div>
                <button onclick="sendLoc()" class="text-indigo-400"><i class="fa-solid fa-location-dot"></i></button>
            </header>

            <div id="chat-flow"></div>
            <div id="typing-indicator" class="indicator"></div>

            <div class="bottom-bar">
                <form id="msg-form" class="max-w-4xl mx-auto input-pill">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="text-slate-400"><i class="fa-solid fa-paperclip text-lg"></i></button>
                    <input id="msg-input" type="text" placeholder="Message likhein..." autocomplete="off" oninput="handleTyping()">
                    <button class="send-btn"><i class="fa-solid fa-paper-plane"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let myName = "", activeChat = "global", typingTimer;

        window.onload = () => { if(localStorage.getItem('chatProUser')) init(localStorage.getItem('chatProUser')); };
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
            listenTyping();
        }

        function loadMessages() {
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, path), (snap) => {
                const d = snap.val();
                const isMine = d.sender === myName;
                const div = document.createElement('div');
                div.className = `msg ${isMine ? 'mine' : 'theirs'}`;
                let content = d.text || "";
                if(d.fileUrl) content = `<img src="${d.fileUrl}" class="rounded-lg max-w-full">`;
                div.innerHTML = `<p class="text-[9px] font-bold opacity-50 mb-1">${d.sender}</p>${content}`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        function loadUsers() {
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    if(c.key !== myName) {
                        const d = document.createElement('div');
                        d.className = "p-3 text-white flex items-center gap-3 cursor-pointer hover:bg-white/5 rounded-xl";
                        d.innerHTML = `<div class="online-dot"></div> ${c.key}`;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        window.switchChat = (user) => {
            activeChat = user;
            document.getElementById('chat-title').innerText = user === "global" ? "# Global Group" : `@ ${user}`;
            loadMessages();
            if(window.innerWidth < 768) toggleSidebar();
        };

        window.handleTyping = () => {
            set(ref(db, `typing/${activeChat}/${myName}`), true);
            clearTimeout(typingTimer);
            typingTimer = setTimeout(() => remove(ref(db, `typing/${activeChat}/${myName}`)), 2000);
        };

        function listenTyping() {
            onValue(ref(db, `typing/${myName}`), (snap) => {
                const indicator = document.getElementById('typing-indicator');
                const typers = snap.val() ? Object.keys(snap.val()) : [];
                indicator.innerText = typers.length > 0 ? `${typers[0]} is typing...` : "";
            });
        }

        window.uploadFile = async (i) => {
            const file = i.files[0]; if(!file) return;
            const r = sRef(storage, `files/${Date.now()}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            push(ref(db, path), { sender: myName, fileUrl: url, time: Date.now() });
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
                push(ref(db, path), { sender: myName, text: i.value, time: Date.now() });
                i.value = ""; 
            }
        };
    </script>
</body>
</html>
