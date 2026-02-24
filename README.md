<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>ChatPro | Fixed Enterprise</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        :root { --brand: #6366f1; }
        
        body, html { 
            height: 100%; margin: 0; padding: 0; overflow: hidden;
            background: #0f172a; font-family: 'Inter', sans-serif; color: white;
        }

        .app-shell { display: flex; height: 100dvh; width: 100%; position: relative; }

        /* Sidebar */
        .sidebar { 
            width: 280px; background: #1e1b4b; border-right: 1px solid rgba(255,255,255,0.05);
            display: flex; flex-direction: column; transition: 0.3s; z-index: 100;
        }

        /* Chat Body */
        .chat-view { flex: 1; display: flex; flex-direction: column; position: relative; height: 100%; }
        #chat-flow { 
            flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 120px; /* Space for input */
            display: flex; flex-direction: column; gap: 10px;
        }

        /* Fixed Message Input (ASLI FIX) */
        .input-bar-fixed {
            position: fixed;
            bottom: 20px; /* Thoda upar rakha hai taake browser buttons ke piche na jaye */
            left: 20px;
            right: 20px;
            z-index: 50;
        }
        
        @media (min-width: 769px) {
            .input-bar-fixed { left: 300px; bottom: 30px; }
        }

        .input-box {
            background: #1e293b; border: 1.5px solid rgba(99, 102, 241, 0.5);
            border-radius: 20px; display: flex; align-items: center; padding: 10px 15px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
        }

        input#msg-input { background: transparent; border: none; color: white; flex: 1; outline: none; font-size: 16px; padding: 10px; }

        .send-btn { 
            background: var(--brand); width: 45px; height: 45px; border-radius: 15px;
            display: flex; align-items: center; justify-content: center; transition: 0.2s;
        }

        /* Message Style */
        .msg-row { display: flex; gap: 12px; margin-bottom: 5px; }
        .avatar { width: 35px; height: 35px; border-radius: 10px; background: #4f46e5; display: flex; align-items: center; justify-content: center; font-weight: bold; font-size: 12px; }
        .text-box { flex: 1; }
        .sender { font-weight: 700; font-size: 13px; color: #818cf8; margin-right: 5px; }
        .time { font-size: 10px; color: #64748b; }
        .content { font-size: 14px; color: #cbd5e1; margin-top: 2px; }

        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; }
            .sidebar.active { left: 0; }
        }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[200] bg-[#0f172a] flex items-center justify-center p-6">
        <div class="bg-[#1e1b4b] p-8 rounded-3xl w-full max-w-sm border border-indigo-500/30 text-center">
            <h2 class="text-3xl font-bold mb-6 text-white">ChatPro <span class="text-indigo-500">v15</span></h2>
            <input id="username-input" type="text" placeholder="Apna Naam Likhein" class="w-full p-4 bg-slate-900 border border-slate-700 rounded-xl mb-4 text-white text-center font-bold">
            <button id="join-btn" class="w-full bg-indigo-600 text-white font-bold py-4 rounded-xl">Connect Now</button>
        </div>
    </div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 border-b border-white/5 flex justify-between items-center text-white">
                <span class="font-bold">Chat Workspace</span>
                <button onclick="toggleSidebar()" class="md:hidden"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div class="flex-1 overflow-y-auto p-4">
                <div onclick="switchChat('global')" id="global-btn" class="p-3 bg-indigo-600/20 text-indigo-400 rounded-xl cursor-pointer font-bold mb-4"># general-hq</div>
                <p class="text-[10px] text-slate-500 font-bold mb-2 uppercase">Users Online</p>
                <div id="users-list"></div>
            </div>
        </aside>

        <main class="chat-view">
            <header class="h-16 bg-[#0f172a] border-b border-white/5 flex items-center justify-between px-6 shrink-0">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="md:hidden text-white"><i class="fa-solid fa-bars"></i></button>
                    <h2 id="chat-title" class="text-white font-bold"># general-hq</h2>
                </div>
                <button onclick="sendLoc()" class="text-indigo-400"><i class="fa-solid fa-location-dot"></i></button>
            </header>

            <div id="chat-flow"></div>

            <div class="input-bar-fixed">
                <form id="msg-form" class="input-box">
                    <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="text-slate-400 px-2"><i class="fa-solid fa-plus-circle text-xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Type message..." autocomplete="off">
                    <button class="send-btn text-white"><i class="fa-solid fa-paper-plane"></i></button>
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
        }

        function loadMessages() {
            const path = activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`;
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onValue(ref(db, path), (snap) => {
                flow.innerHTML = "";
                snap.forEach(msg => {
                    const d = msg.val();
                    const row = document.createElement('div');
                    row.className = "msg-row";
                    let content = d.text || "";
                    if(d.fileUrl) content = `<img src="${d.fileUrl}" class="rounded-lg max-w-full md:max-w-xs mt-1">`;
                    if(d.locUrl) content = `<a href="${d.locUrl}" target="_blank" class="text-indigo-400 underline text-xs">📍 Location Shared</a>`;
                    
                    row.innerHTML = `
                        <div class="avatar">${d.sender[0].toUpperCase()}</div>
                        <div class="text-box">
                            <div><span class="sender">${d.sender}</span><span class="time">${new Date(d.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}</span></div>
                            <div class="content">${content}</div>
                        </div>
                    `;
                    flow.appendChild(row);
                });
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
                        d.className = "p-3 text-slate-300 flex items-center gap-3 cursor-pointer hover:bg-white/5 rounded-xl";
                        d.innerHTML = `<div class="w-2 h-2 bg-green-500 rounded-full"></div> ${c.key}`;
                        d.onclick = () => switchChat(c.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        window.switchChat = (user) => {
            activeChat = user;
            document.getElementById('chat-title').innerText = user === "global" ? "# general-hq" : `@ ${user}`;
            loadMessages();
            if(window.innerWidth < 768) toggleSidebar();
        };

        window.uploadFile = async (i) => {
            const file = i.files[0]; if(!file) return;
            const r = sRef(storage, `files/${Date.now()}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            push(ref(db, activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, fileUrl: url, time: Date.now() });
        };

        window.sendLoc = () => {
            navigator.geolocation.getCurrentPosition(p => {
                const url = `https://www.google.com/maps?q=${p.coords.latitude},${p.coords.longitude}`;
                push(ref(db, activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, locUrl: url, time: Date.now() });
            });
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                push(ref(db, activeChat === "global" ? 'msgs/global' : `private/${[myName, activeChat].sort().join('_')}`), { sender: myName, text: i.value, time: Date.now() });
                i.value = ""; 
            }
        };
    </script>
</body>
</html>
