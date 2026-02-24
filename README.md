<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro | Enterprise Realtime App</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        :root { --primary: #2563eb; --bg: #f8fafc; }
        body { font-family: 'Inter', sans-serif; transition: 0.3s; }
        .dark-mode { background-color: #0f172a; color: #f1f5f9; }
        .dark-mode .glass { background: #1e293b; border-color: #334155; color: white; }
        .dark-mode .msg-received { background: #334155; color: white; }
        .glass { background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(10px); }
        .msg-bubble { max-width: 70%; animation: pop 0.2s ease-out; }
        @keyframes pop { from { transform: scale(0.9); opacity: 0; } to { transform: scale(1); opacity: 1; } }
        #chat-flow::-webkit-scrollbar { width: 4px; }
        #chat-flow::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
        .hidden { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col overflow-hidden">

    <div id="login-screen" class="fixed inset-0 z-[100] bg-blue-600 flex items-center justify-center p-6">
        <div class="bg-white p-10 rounded-[2.5rem] shadow-2xl w-full max-w-md text-center transform transition-all">
            <div class="inline-block p-4 bg-blue-50 rounded-3xl mb-6">
                <i class="fa-solid fa-shield-halved text-4xl text-blue-600"></i>
            </div>
            <h2 class="text-3xl font-extrabold text-gray-900 mb-2">Secure Access</h2>
            <p class="text-gray-500 mb-8">Enter your business or personal name</p>
            <input id="username-input" type="text" placeholder="Your Name..." class="w-full bg-gray-50 border-2 border-gray-100 rounded-2xl p-4 mb-4 outline-none focus:border-blue-500 text-center font-semibold text-lg">
            <button id="join-btn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-2xl shadow-lg transition-transform active:scale-95">
                Launch Dashboard <i class="fa-solid fa-chevron-right ml-2"></i>
            </button>
        </div>
    </div>

    <div id="main-app" class="hidden flex h-full">
        
        <aside class="w-20 md:w-80 glass border-r flex flex-col transition-all">
            <div class="p-6 flex items-center gap-3 border-b">
                <div class="w-10 h-10 bg-blue-600 rounded-xl flex items-center justify-center text-white shadow-lg">
                    <i class="fa-solid fa-paper-plane"></i>
                </div>
                <span class="font-bold text-xl tracking-tight hidden md:block">ChatPro <span class="text-blue-600">Biz</span></span>
            </div>

            <div class="flex-1 overflow-y-auto p-4 space-y-2">
                <div id="global-tab" class="p-3 rounded-xl cursor-pointer bg-blue-600 text-white flex items-center gap-3">
                    <i class="fa-solid fa-globe"></i> <span class="hidden md:block font-medium">Global Network</span>
                </div>
                <p class="text-[10px] font-bold text-gray-400 uppercase tracking-widest mt-6 mb-2 hidden md:block">Active Accounts</p>
                <div id="users-list" class="space-y-1"></div>
            </div>

            <div class="p-4 border-t space-y-4">
                <button id="dark-toggle" class="w-full p-2 rounded-xl hover:bg-gray-100 flex items-center justify-center gap-2">
                    <i class="fa-solid fa-moon"></i> <span class="hidden md:block text-sm">Dark Appearance</span>
                </button>
                <div class="flex items-center gap-3 p-2">
                    <div class="w-10 h-10 rounded-full bg-blue-500 flex items-center justify-center text-white font-bold" id="my-avatar">?</div>
                    <div class="hidden md:block">
                        <p class="text-xs font-bold leading-none mb-1" id="my-name">User</p>
                        <p class="text-[10px] text-green-500 font-bold">● ONLINE</p>
                    </div>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col bg-gray-50 dark-mode:bg-slate-900">
            <header class="p-4 glass border-b flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div id="chat-icon" class="w-10 h-10 bg-gray-100 rounded-full flex items-center justify-center text-gray-600">
                        <i class="fa-solid fa-hashtag"></i>
                    </div>
                    <div>
                        <h4 id="chat-title" class="font-bold text-sm md:text-base">Global Network</h4>
                        <p class="text-[10px] text-gray-500">Live communication enabled</p>
                    </div>
                </div>
                <div class="flex gap-3">
                    <button class="w-9 h-9 rounded-lg hover:bg-gray-100 flex items-center justify-center"><i class="fa-solid fa-search text-xs"></i></button>
                    <button class="w-9 h-9 rounded-lg hover:bg-gray-100 flex items-center justify-center text-blue-600"><i class="fa-solid fa-video text-xs"></i></button>
                </div>
            </header>

            <div id="chat-flow" class="flex-1 p-6 overflow-y-auto space-y-4"></div>

            <div class="p-4 glass">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-3 bg-white p-2 rounded-2xl border shadow-sm">
                    <div class="flex gap-2 ml-2">
                        <button type="button" onclick="addEmoji('😊')" class="text-gray-400 hover:text-yellow-500"><i class="fa-regular fa-face-smile text-lg"></i></button>
                        <button type="button" class="text-gray-400 hover:text-blue-500"><i class="fa-solid fa-paperclip text-lg"></i></button>
                    </div>
                    <input id="msg-input" type="text" placeholder="Type a professional message..." class="flex-1 bg-transparent p-2 outline-none text-sm text-gray-700">
                    <button class="bg-blue-600 text-white px-5 py-2.5 rounded-xl flex items-center gap-2 hover:bg-blue-700 transition shadow-lg shadow-blue-200">
                        <span class="hidden md:block text-sm font-bold">Send</span>
                        <i class="fa-solid fa-paper-plane text-xs"></i>
                    </button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
        let myName = "";
        let currentRoom = "global";

        // --- DARK MODE ---
        document.getElementById('dark-toggle').onclick = () => {
            document.body.classList.toggle('dark-mode');
        };

        // --- LOGIN ---
        document.getElementById('join-btn').onclick = () => {
            const input = document.getElementById('username-input').value.trim();
            if(input.length > 2) {
                myName = input;
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('main-app').classList.remove('hidden');
                document.getElementById('my-name').innerText = myName;
                document.getElementById('my-avatar').innerText = myName[0].toUpperCase();
                
                set(ref(db, 'online_users/' + myName), { status: 'online', joinTime: serverTimestamp() });
                startApp();
            }
        };

        function startApp() {
            onValue(ref(db, 'online_users'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(child => {
                    if(child.key !== myName) {
                        const item = document.createElement('div');
                        item.className = "p-3 rounded-xl cursor-pointer hover:bg-gray-100 flex items-center gap-3 transition";
                        item.innerHTML = `<div class="w-8 h-8 rounded-lg bg-indigo-100 text-indigo-600 flex items-center justify-center font-bold text-xs">${child.key[0]}</div><span class="text-sm font-medium hidden md:block">${child.key}</span>`;
                        item.onclick = () => switchChat(child.key);
                        list.appendChild(item);
                    }
                });
            });
            loadMessages("global");
        }

        function switchChat(target) {
            currentRoom = myName < target ? myName + "_" + target : target + "_" + myName;
            document.getElementById('chat-title').innerText = target;
            document.getElementById('chat-icon').innerHTML = '<i class="fa-solid fa-user-tie"></i>';
            loadMessages(currentRoom);
        }

        document.getElementById('global-tab').onclick = () => {
            currentRoom = "global";
            document.getElementById('chat-title').innerText = "Global Network";
            document.getElementById('chat-icon').innerHTML = '<i class="fa-solid fa-hashtag"></i>';
            loadMessages("global");
        };

        function loadMessages(roomId) {
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, 'messages/' + roomId), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const time = data.time ? new Date(data.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) : '...';
                
                const div = document.createElement('div');
                div.className = `flex ${isMine ? 'justify-end' : 'justify-start'}`;
                div.innerHTML = `
                    <div class="msg-bubble ${isMine ? 'bg-blue-600 text-white rounded-2xl rounded-tr-none' : 'bg-white text-gray-800 rounded-2xl rounded-tl-none'} p-4 shadow-sm">
                        <p class="text-[10px] font-bold opacity-50 mb-1">${isMine ? 'Me' : data.sender}</p>
                        <p class="text-sm leading-relaxed">${data.text}</p>
                        <p class="text-[8px] text-right mt-1 opacity-40">${time}</p>
                    </div>
                `;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const input = document.getElementById('msg-input');
            if(input.value.trim()){
                push(ref(db, 'messages/' + currentRoom), {
                    sender: myName,
                    text: input.value,
                    time: serverTimestamp()
                });
                input.value = "";
            }
        };

        window.addEmoji = (emoji) => {
            document.getElementById('msg-input').value += emoji;
        };
    </script>
</body>
</html>
