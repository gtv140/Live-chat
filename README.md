<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ChatPro | Balanced Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #f1f5f9; }
        
        /* Layout Fixes */
        .chat-container { height: calc(100vh - 140px); overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 12px; }
        .input-box { position: fixed; bottom: 0; left: 0; right: 0; background: white; padding: 15px; border-top: 1px solid #e2e8f0; z-index: 50; }
        
        /* Chat Bubbles - Pehle wala look wapis */
        .msg { max-width: 80%; padding: 12px 16px; border-radius: 20px; position: relative; font-size: 14px; line-height: 1.5; }
        .msg.mine { align-self: flex-end; background: #4f46e5; color: white; border-bottom-right-radius: 4px; box-shadow: 0 4px 10px rgba(79, 70, 229, 0.2); }
        .msg.theirs { align-self: flex-start; background: white; color: #1e293b; border-bottom-left-radius: 4px; border: 1px solid #e2e8f0; }

        /* Sidebar for Desktop */
        @media (min-width: 768px) {
            .main-content { margin-left: 280px; }
            .sidebar { width: 280px; position: fixed; left: 0; top: 0; bottom: 0; background: white; border-right: 1px solid #e2e8f0; display: flex !important; }
        }
        @media (max-width: 767px) {
            .sidebar { display: none; position: fixed; left: 0; top: 0; bottom: 0; width: 80%; background: white; z-index: 100; box-shadow: 20px 0 50px rgba(0,0,0,0.2); }
            .sidebar.show { display: flex !important; }
            .main-content { margin-left: 0; }
        }
    </style>
</head>
<body class="bg-slate-50">

    <div id="login-screen" class="fixed inset-0 z-[200] bg-indigo-600 flex items-center justify-center p-6">
        <div class="bg-white p-8 rounded-3xl shadow-2xl w-full max-w-sm text-center">
            <h2 class="text-2xl font-bold text-slate-900 mb-6">Welcome to ChatPro</h2>
            <input id="username-input" type="text" placeholder="Apna naam likhein..." class="w-full p-4 bg-slate-100 rounded-xl mb-4 border-none outline-none font-bold text-center">
            <button id="join-btn" class="w-full bg-indigo-600 text-white p-4 rounded-xl font-bold shadow-lg active:scale-95 transition">Start Chatting</button>
        </div>
    </div>

    <aside id="sidebar" class="sidebar flex-col">
        <div class="p-6 border-b flex justify-between items-center bg-white sticky top-0">
            <h1 class="font-bold text-xl text-indigo-600">Messages</h1>
            <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div id="users-list" class="flex-1 overflow-y-auto p-4 space-y-2"></div>
    </aside>

    <div class="main-content">
        <header class="h-16 bg-white/90 backdrop-blur-md border-b sticky top-0 flex items-center justify-between px-6 z-40">
            <div class="flex items-center gap-3">
                <button onclick="toggleSidebar()" class="md:hidden text-slate-500 text-xl"><i class="fa-solid fa-bars"></i></button>
                <h2 id="chat-title" class="font-bold text-slate-800"># global-hq</h2>
            </div>
            <button onclick="logout()" class="text-slate-400 hover:text-red-500"><i class="fa-solid fa-power-off"></i></button>
        </header>

        <div id="chat-flow" class="chat-container">
            </div>

        <div class="input-box md:pl-[300px]">
            <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-2 bg-slate-100 p-2 rounded-2xl border">
                <button type="button" id="voice-btn" class="w-10 h-10 text-slate-400 hover:text-indigo-600"><i class="fa-solid fa-microphone"></i></button>
                <input id="msg-input" type="text" placeholder="Type a message..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium">
                <button class="bg-indigo-600 text-white w-10 h-10 rounded-xl shadow-lg flex items-center justify-center"><i class="fa-solid fa-paper-plane text-xs"></i></button>
            </form>
        </div>
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
        let myName = "";

        window.onload = () => { const s = localStorage.getItem('chatProUser'); if(s) init(s); };
        document.getElementById('join-btn').onclick = () => { 
            const n = document.getElementById('username-input').value.trim(); 
            if(n){ localStorage.setItem('chatProUser', n); init(n); }
        };

        function init(n) {
            myName = n;
            document.getElementById('login-screen').style.display = 'none';
            set(ref(db, 'online/' + n), true);
            
            onChildAdded(ref(db, 'msgs/global'), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const flow = document.getElementById('chat-flow');
                const div = document.createElement('div');
                div.className = `msg ${isMine ? 'mine' : 'theirs'}`;
                div.innerHTML = `<p class="text-[9px] font-bold uppercase opacity-50 mb-1">${data.sender}</p><div>${data.text}</div>`;
                flow.appendChild(div);
                flow.scrollTo(0, flow.scrollHeight);
            });
        }

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('show');
        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                push(ref(db, 'msgs/global'), { sender: myName, text: i.value, time: Date.now() }); 
                i.value = ""; 
            }
        };
    </script>
</body>
</html>
