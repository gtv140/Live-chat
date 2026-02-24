<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ChatPro | Ultra Smooth Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        
        body { font-family: 'Plus Jakarta Sans', sans-serif; overflow: hidden; height: 100vh; background-color: #f8fafc; }
        
        /* Fixed Heights */
        .app-wrapper { height: 100vh; display: flex; overflow: hidden; }
        .chat-main { display: flex; flex-direction: column; flex: 1; height: 100vh; position: relative; }
        
        /* The Magic Scroll Area */
        #chat-flow { 
            flex: 1; 
            overflow-y: auto; 
            overflow-x: hidden; 
            padding: 20px;
            scroll-behavior: smooth;
            -webkit-overflow-scrolling: touch;
        }

        /* Custom Scrollbar */
        #chat-flow::-webkit-scrollbar { width: 5px; }
        #chat-flow::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }

        /* Bubble Styles */
        .bubble { max-width: 85%; position: relative; margin-bottom: 4px; }
        .mine { background: #4f46e5; color: white; border-radius: 18px 18px 2px 18px; align-self: flex-end; }
        .theirs { background: white; color: #1e293b; border-radius: 18px 18px 18px 2px; border: 1px solid #e2e8f0; align-self: flex-start; }

        .sidebar { width: 300px; background: white; border-right: 1px solid #e2e8f0; display: flex; flex-direction: column; transition: 0.3s; }

        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; z-index: 100; width: 80%; box-shadow: 20px 0 50px rgba(0,0,0,0.1); }
            .sidebar.open { left: 0; }
            .bubble { max-width: 92%; }
        }

        .dark { background-color: #0f172a !important; color: white; }
        .dark .sidebar { background: #1e293b; border-color: #334155; }
        .dark .theirs { background: #334155; color: white; border-color: #475569; }
    </style>
</head>
<body id="app-body">

    <div id="login-screen" class="fixed inset-0 z-[200] bg-white flex items-center justify-center p-6">
        <div class="w-full max-w-xs text-center">
            <div class="w-16 h-16 bg-indigo-600 rounded-2xl flex items-center justify-center text-white mx-auto mb-6 shadow-xl shadow-indigo-200">
                <i class="fa-solid fa-comments text-2xl"></i>
            </div>
            <h2 class="text-2xl font-bold mb-8">ChatPro</h2>
            <input id="username-input" type="text" placeholder="Username" class="w-full p-4 bg-slate-100 rounded-xl mb-4 outline-none focus:ring-2 focus:ring-indigo-600 border-none text-center font-bold">
            <button id="join-btn" class="w-full bg-indigo-600 text-white p-4 rounded-xl font-bold shadow-lg">Start Chatting</button>
        </div>
    </div>

    <div class="app-wrapper">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 border-b flex justify-between items-center">
                <h1 class="font-bold text-xl text-indigo-600 tracking-tight">Messages</h1>
                <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div id="users-list" class="flex-1 overflow-y-auto p-4 space-y-2"></div>
            <div class="p-4 border-t flex items-center justify-between">
                <div class="flex items-center gap-2">
                    <div id="my-avatar" class="w-8 h-8 rounded-full bg-indigo-600 text-white flex items-center justify-center font-bold text-xs uppercase">?</div>
                    <span id="my-name" class="text-xs font-bold truncate max-w-[100px]">User</span>
                </div>
                <div class="flex gap-3">
                    <i class="fa-solid fa-moon text-slate-400 cursor-pointer" onclick="toggleDark()"></i>
                    <i class="fa-solid fa-power-off text-slate-400 cursor-pointer" onclick="logout()"></i>
                </div>
            </div>
        </aside>

        <main class="chat-main">
            <header class="h-16 bg-white/80 backdrop-blur-md border-b flex items-center justify-between px-4 md:px-8 z-10">
                <div class="flex items-center gap-3">
                    <button onclick="toggleSidebar()" class="md:hidden p-2 text-slate-500"><i class="fa-solid fa-bars-staggered"></i></button>
                    <h2 id="chat-title" class="font-bold text-slate-800"># global-hq</h2>
                </div>
                <button onclick="sendLoc()" class="text-slate-400 hover:text-indigo-600 p-2"><i class="fa-solid fa-location-dot"></i></button>
            </header>

            <div id="chat-flow" class="flex flex-col">
                </div>

            <div class="p-4 bg-white border-t">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-2 bg-slate-100 p-1.5 rounded-2xl">
                    <button type="button" id="voice-btn" class="w-10 h-10 text-slate-400"><i class="fa-solid fa-microphone"></i></button>
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Type a message..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium">
                    <button class="bg-indigo-600 text-white w-10 h-10 rounded-xl flex items-center justify-center shadow-md active:scale-95 transition-all"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        // Firebase Config (Keep yours)
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
        let myName = "", mediaRecorder, audioChunks = [];

        window.onload = () => { const s = localStorage.getItem('chatProUser'); if(s) init(s); };
        document.getElementById('join-btn').onclick = () => { const n = document.getElementById('username-input').value.trim(); if(n){ localStorage.setItem('chatProUser', n); init(n); }};

        function init(n) {
            myName = n;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('my-name').innerText = n;
            document.getElementById('my-avatar').innerText = n[0];
            set(ref(db, 'online/' + n), true);
            
            onChildAdded(ref(db, 'msgs/global'), (snap) => {
                render(snap.val());
                const flow = document.getElementById('chat-flow');
                flow.scrollTop = flow.scrollHeight;
            });
        }

        function render(data) {
            const isMine = data.sender === myName;
            const flow = document.getElementById('chat-flow');
            const div = document.createElement('div');
            div.className = `bubble p-4 shadow-sm ${isMine ? 'mine' : 'theirs'}`;
            
            let content = data.text || "";
            if(data.voiceUrl) content = `<audio controls class="h-8"><source src="${data.voiceUrl}"></audio>`;
            if(data.locUrl) content = `<a href="${data.locUrl}" target="_blank" class="text-xs font-bold underline">📍 Live Location</a>`;

            div.innerHTML = `<p class="text-[9px] font-bold uppercase opacity-50 mb-1">${data.sender}</p><div>${content}</div>`;
            flow.appendChild(div);
            flow.scrollTop = flow.scrollHeight;
        }

        // Functions
        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('open');
        window.toggleDark = () => document.getElementById('app-body').classList.toggle('dark');
        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };
        window.sendLoc = () => {
            navigator.geolocation.getCurrentPosition(p => {
                const url = `https://www.google.com/maps?q=${p.coords.latitude},${p.coords.longitude}`;
                push(ref(db, 'msgs/global'), { sender: myName, locUrl: url, time: Date.now() });
            });
        };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ push(ref(db, 'msgs/global'), { sender: myName, text: i.value, time: Date.now() }); i.value = ""; }
        };
    </script>
</body>
</html>
