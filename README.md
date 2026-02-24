<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro Ultra | Enterprise Suite</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        
        :root { --p: #4f46e5; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.3s; background: #f8fafc; }
        .dark { background: #0f172a; color: #f1f5f9; }
        
        .chat-area { height: calc(100vh - 160px); scroll-behavior: smooth; }
        .msg-mine { background: linear-gradient(135deg, #6366f1, #4f46e5); color: white; border-radius: 1.2rem 1.2rem 0.2rem 1.2rem; box-shadow: 0 4px 12px rgba(79, 70, 229, 0.2); }
        .msg-theirs { background: white; color: #1e293b; border-radius: 1.2rem 1.2rem 1.2rem 0.2rem; border: 1px solid #e2e8f0; }
        .dark .msg-theirs { background: #1e293b; color: white; border-color: #334155; }
        
        .sidebar { transition: 0.3s; z-index: 50; }
        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; }
            .sidebar.active { left: 0; }
        }
        
        .recording-pulse { animation: pulse 1s infinite; color: #ef4444; }
        @keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.3; } 100% { opacity: 1; } }
        .reaction-badge { position: absolute; bottom: -12px; right: 10px; background: white; border-radius: 10px; padding: 2px 6px; font-size: 10px; border: 1px solid #eee; color: #000; box-shadow: 0 2px 4px rgba(0,0,0,0.1); display: flex; gap: 2px; }
    </style>
</head>
<body class="h-screen flex overflow-hidden" id="app-body">

    <div id="login-screen" class="fixed inset-0 z-[100] bg-indigo-600 flex items-center justify-center p-6">
        <div class="bg-white p-10 rounded-[2.5rem] shadow-2xl w-full max-w-sm text-center">
            <div class="w-16 h-16 bg-indigo-100 text-indigo-600 rounded-2xl flex items-center justify-center mx-auto mb-6"><i class="fa-solid fa-bolt text-2xl"></i></div>
            <h2 class="text-2xl font-extrabold text-slate-900 mb-8">ChatPro Ultra</h2>
            <input id="username-input" type="text" placeholder="Your Name" class="w-full bg-slate-50 border p-4 rounded-xl mb-4 text-center font-bold outline-none focus:border-indigo-500">
            <button id="join-btn" class="w-full bg-indigo-600 text-white font-bold py-4 rounded-xl shadow-lg hover:bg-indigo-700 transition">Get Started</button>
        </div>
    </div>

    <div id="overlay" onclick="toggleSidebar()" class="fixed inset-0 bg-black/40 z-40 hidden"></div>

    <aside id="sidebar" class="sidebar w-72 bg-white border-r flex flex-col dark:bg-slate-900 dark:border-slate-800">
        <div class="p-6 border-b flex justify-between items-center dark:border-slate-800">
            <span class="font-bold text-xl text-indigo-600">Workspace</span>
            <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div id="users-list" class="flex-1 overflow-y-auto p-4 space-y-2"></div>
        <div class="p-4 border-t dark:border-slate-800 flex items-center justify-between">
            <div class="flex items-center gap-2">
                <div id="u-avatar" class="w-8 h-8 rounded-lg bg-indigo-600 text-white flex items-center justify-center text-xs font-bold">?</div>
                <span id="u-name" class="text-xs font-bold truncate max-w-[100px]">User</span>
            </div>
            <div class="flex gap-2">
                <button onclick="toggleDark()" class="text-slate-400 hover:text-indigo-500"><i class="fa-solid fa-circle-half-stroke"></i></button>
                <button onclick="logout()" class="text-slate-400 hover:text-red-500"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col min-w-0">
        <header class="h-16 bg-white/80 backdrop-blur-md border-b flex items-center justify-between px-4 md:px-8 dark:bg-slate-900/80 dark:border-slate-800">
            <div class="flex items-center gap-3">
                <button onclick="toggleSidebar()" class="md:hidden p-2 text-slate-500"><i class="fa-solid fa-bars-staggered"></i></button>
                <h2 id="chat-title" class="font-bold tracking-tight"># general-hq</h2>
            </div>
            <button onclick="sendLocation()" class="text-slate-400 hover:text-indigo-500 px-2" title="Share Location"><i class="fa-solid fa-location-arrow"></i></button>
        </header>

        <div id="chat-flow" class="flex-1 overflow-y-auto p-4 md:p-8 space-y-6 chat-area"></div>

        <div class="p-4 md:p-6">
            <div id="rec-status" class="text-[10px] text-center mb-2 font-bold text-red-500 hidden animate-pulse">Recording Audio...</div>
            <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-2 bg-white dark:bg-slate-800 p-2 rounded-2xl shadow-xl border border-slate-100 dark:border-slate-700">
                <input type="file" id="file-input" class="hidden">
                <button type="button" onclick="document.getElementById('file-input').click()" class="w-10 h-10 text-slate-400 hover:text-indigo-600"><i class="fa-solid fa-paperclip"></i></button>
                <button type="button" id="voice-btn" class="w-10 h-10 text-slate-400 hover:text-red-500"><i class="fa-solid fa-microphone"></i></button>
                <input id="msg-input" type="text" placeholder="Type a message..." class="flex-1 bg-transparent p-2 outline-none text-sm dark:text-white">
                <button class="bg-indigo-600 text-white w-10 h-10 rounded-xl shadow-lg active:scale-90 transition-all"><i class="fa-solid fa-paper-plane text-xs"></i></button>
            </form>
        </div>
    </main>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, remove, update, onChildChanged, onChildRemoved } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let myName = "", currentRoom = "global", mediaRecorder, audioChunks = [];

        window.onload = () => { const saved = localStorage.getItem('chatProUser'); if(saved) init(saved); };
        document.getElementById('join-btn').onclick = () => { const n = document.getElementById('username-input').value.trim(); if(n){ localStorage.setItem('chatProUser', n); init(n); }};

        function init(n) {
            myName = n;
            document.getElementById('login-screen').classList.add('hidden');
            document.getElementById('u-name').innerText = n;
            document.getElementById('u-avatar').innerText = n[0].toUpperCase();
            set(ref(db, 'online/' + n), true);
            loadMsgs();
            loadUsers();
        }

        // --- CORE FEATURES ---
        function loadMsgs() {
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, 'msgs/' + currentRoom), (snap) => render(snap.key, snap.val()));
            onChildRemoved(ref(db, 'msgs/' + currentRoom), (snap) => document.getElementById(snap.key)?.remove());
            onChildChanged(ref(db, 'msgs/' + currentRoom), (snap) => {
                const el = document.getElementById(snap.key);
                if(el && snap.val().reactions) {
                    const rb = el.querySelector('.reaction-badge');
                    rb.innerHTML = Object.values(snap.val().reactions).join('');
                    rb.classList.remove('hidden');
                }
            });
        }

        function render(id, data) {
            const isMine = data.sender === myName;
            const div = document.createElement('div');
            div.id = id;
            div.className = `flex ${isMine ? 'justify-end' : 'justify-start'} mb-6 group relative`;
            
            let content = data.text || "";
            if(data.voiceUrl) content = `<audio controls class="max-w-[200px] h-8"><source src="${data.voiceUrl}"></audio>`;
            if(data.locUrl) content = `<a href="${data.locUrl}" target="_blank" class="text-xs font-bold underline text-blue-500"><i class="fa-solid fa-map-location-dot"></i> View My Location</a>`;
            if(data.fileUrl && data.type === 'image') content = `<img src="${data.fileUrl}" class="rounded-lg max-w-xs border shadow-sm">`;

            div.innerHTML = `
                <div class="p-4 max-w-[85%] relative ${isMine ? 'msg-mine' : 'msg-theirs'}">
                    <p class="text-[8px] font-bold uppercase opacity-50 mb-1">${data.sender}</p>
                    <div class="msg-content text-sm">${content}</div>
                    <div class="reaction-badge ${data.reactions ? '' : 'hidden'}">${data.reactions ? Object.values(data.reactions).join('') : ''}</div>
                    <div class="absolute -top-6 ${isMine?'right-0':'left-0'} hidden group-hover:flex bg-white shadow-lg rounded-full px-2 py-1 gap-2 text-xs border border-slate-100 dark:bg-slate-800">
                        <span class="cursor-pointer" onclick="react('${id}', '👍')">👍</span>
                        <span class="cursor-pointer" onclick="react('${id}', '❤️')">❤️</span>
                        ${isMine ? `<i class="fa-solid fa-trash text-red-400 ml-2 pt-1" onclick="del('${id}')"></i>` : ''}
                    </div>
                </div>`;
            document.getElementById('chat-flow').appendChild(div);
            document.getElementById('chat-flow').scrollTop = document.getElementById('chat-flow').scrollHeight;
        }

        // VOICE
        document.getElementById('voice-btn').onclick = async () => {
            if(!mediaRecorder || mediaRecorder.state === "inactive") {
                const s = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(s);
                mediaRecorder.start(); audioChunks = [];
                document.getElementById('rec-status').classList.remove('hidden');
                mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                mediaRecorder.onstop = async () => {
                    const blob = new Blob(audioChunks, { type: 'audio/webm' });
                    const r = sRef(storage, `v/${Date.now()}.webm`);
                    await uploadBytes(r, blob);
                    const url = await getDownloadURL(r);
                    push(ref(db, 'msgs/' + currentRoom), { sender: myName, voiceUrl: url, time: Date.now() });
                    document.getElementById('rec-status').classList.add('hidden');
                };
            } else { mediaRecorder.stop(); }
        };

        // HELPERS
        window.sendLocation = () => {
            navigator.geolocation.getCurrentPosition(p => {
                const url = `https://www.google.com/maps?q=${p.coords.latitude},${p.coords.longitude}`;
                push(ref(db, 'msgs/' + currentRoom), { sender: myName, locUrl: url, time: Date.now() });
            });
        };
        window.del = (id) => { if(confirm("Delete message?")) remove(ref(db, `msgs/${currentRoom}/${id}`)); };
        window.react = (id, e) => update(ref(db, `msgs/${currentRoom}/${id}/reactions`), { [myName]: e });
        window.toggleDark = () => document.getElementById('app-body').classList.toggle('dark');
        window.toggleSidebar = () => { document.getElementById('sidebar').classList.toggle('active'); document.getElementById('overlay').classList.toggle('hidden'); };
        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ push(ref(db, 'msgs/' + currentRoom), { sender: myName, text: i.value, time: Date.now() }); i.value = ""; }
        };

        function loadUsers() {
            onValue(ref(db, 'online'), s => {
                const l = document.getElementById('users-list'); l.innerHTML = "";
                s.forEach(c => {
                    const d = document.createElement('div');
                    d.className = "flex items-center gap-3 p-3 rounded-xl hover:bg-slate-50 dark:hover:bg-slate-800 cursor-pointer transition";
                    d.innerHTML = `<div class="w-8 h-8 rounded-full bg-indigo-100 text-indigo-600 flex items-center justify-center font-bold text-xs">${c.key[0]}</div><span class="text-sm font-medium">${c.key}</span>`;
                    l.appendChild(d);
                });
            });
        }
    </script>
</body>
</html>
