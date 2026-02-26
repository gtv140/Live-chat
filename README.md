<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>NeonPro | Private & Global</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        
        :root { --p: #a855f7; --bg: #0f172a; --card: #1e293b; --text: #f8fafc; }
        .light { --bg: #f8fafc; --card: #ffffff; --text: #0f172a; }

        * { font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        body { background: var(--bg); color: var(--text); height: 100dvh; overflow: hidden; margin: 0; }

        /* Sidebar Styling */
        .sidebar { width: 280px; background: var(--card); border-right: 1px solid rgba(255,255,255,0.05); }
        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; z-index: 1000; height: 100%; width: 80%; }
            .sidebar.active { left: 0; box-shadow: 20px 0 50px rgba(0,0,0,0.5); }
        }

        /* Message Cards */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 12px; }
        .msg-card { max-width: 80%; padding: 12px 16px; border-radius: 20px; position: relative; font-size: 14px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        .mine { align-self: flex-end; background: var(--p); color: white; border-bottom-right-radius: 4px; }
        .other { align-self: flex-start; background: var(--card); border: 1px solid rgba(255,255,255,0.05); border-bottom-left-radius: 4px; }

        /* Input Deck */
        .input-deck { padding: 15px 20px; background: var(--bg); border-top: 1px solid rgba(255,255,255,0.05); }
        .pill-input { background: var(--card); border-radius: 30px; padding: 10px 20px; display: flex; align-items: center; gap: 10px; border: 1px solid rgba(255,255,255,0.1); }
        
        .overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.5); backdrop-filter: blur(4px); z-index: 900; }
        .overlay.active { display: block; }
    </style>
</head>
<body class="flex">

    <div id="login-screen" class="fixed inset-0 z-[5000] flex items-center justify-center bg-[#0f172a] p-6">
        <div class="w-full max-w-sm text-center">
            <div class="w-20 h-20 bg-purple-600 rounded-3xl flex items-center justify-center mx-auto mb-6 shadow-2xl rotate-12"><i class="fa-solid fa-user-shield text-3xl text-white"></i></div>
            <h1 class="text-3xl font-black mb-2">Neon<span class="text-purple-500">Secure</span></h1>
            <p class="text-slate-400 text-sm mb-10">Enter your name to join the hub</p>
            <input id="user-name-in" type="text" placeholder="Your Name" class="w-full bg-white/5 border border-white/10 p-4 rounded-2xl text-center mb-4 outline-none focus:border-purple-500 text-white font-bold">
            <button onclick="handleLogin()" class="w-full bg-purple-600 py-4 rounded-2xl font-black text-white shadow-xl hover:scale-[1.02] active:scale-95 transition-all">LAUNCH DASHBOARD</button>
        </div>
    </div>

    <aside id="side" class="sidebar flex flex-col">
        <div class="p-6 border-b border-white/5 flex justify-between items-center">
            <span class="text-xl font-black text-purple-500 italic">N<span class="text-white">.PRO</span></span>
            <button onclick="toggleTheme()" class="text-slate-400"><i id="t-icon" class="fa-solid fa-moon"></i></button>
        </div>
        
        <div class="flex-1 overflow-y-auto p-4 space-y-6">
            <div>
                <p class="text-[10px] font-black text-slate-500 uppercase tracking-widest mb-3">Public</p>
                <div onclick="switchChat('global')" class="p-3 bg-purple-600/10 border-l-4 border-purple-500 rounded-xl cursor-pointer font-bold text-sm hover:bg-purple-600/20">
                    <i class="fa-solid fa-hashtag mr-2"></i> Global Lounge
                </div>
            </div>
            
            <div>
                <p class="text-[10px] font-black text-slate-500 uppercase tracking-widest mb-3">Live Users (Private)</p>
                <div id="user-list" class="space-y-2"></div>
            </div>
        </div>

        <div class="p-6 bg-black/20 border-t border-white/5">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 rounded-full bg-purple-600 flex items-center justify-center font-bold text-white shadow-lg" id="avatar">?</div>
                <div class="flex-1 overflow-hidden"><p id="display-name" class="font-bold text-xs truncate">Username</p><span class="text-[8px] text-green-500 font-bold uppercase">Active</span></div>
                <button onclick="handleLogout()" class="text-red-400 hover:text-red-500 transition-colors"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col min-w-0 bg-[var(--bg)]">
        <header class="h-16 flex items-center px-6 border-b border-white/5 justify-between backdrop-blur-md">
            <div class="flex items-center gap-4">
                <button class="lg:hidden" onclick="toggleSide()"><i class="fa-solid fa-bars-staggered"></i></button>
                <h2 id="current-chat-title" class="font-extrabold text-sm uppercase tracking-widest text-purple-400">Global Lounge</h2>
            </div>
            <div id="welcome-msg" class="hidden sm:block text-[10px] font-bold text-slate-400">WELCOME BACK, <span id="user-title" class="text-white">USER</span>!</div>
        </header>

        <div id="chat-flow"></div>

        <div class="input-deck">
            <form id="chat-form" class="pill-input">
                <input id="msg-input" type="text" placeholder="Type a message..." autocomplete="off" class="bg-transparent flex-1 outline-none text-sm">
                <button class="text-purple-500 hover:scale-110 active:scale-90"><i class="fa-solid fa-paper-plane text-xl"></i></button>
            </form>
        </div>
    </main>

    <div id="overlay" class="overlay" onclick="toggleSide()"></div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
            databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
            projectId: "live-chat-b810c",
            appId: "1:555058795334:web:f668887409800c32970b47"
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);

        let currentUser = localStorage.getItem('neon_user'), activeChat = 'global';

        if(currentUser) initApp();

        window.handleLogin = () => {
            const name = document.getElementById('user-name-in').value.trim();
            if(!name) return alert("Please enter a name!");
            currentUser = name;
            localStorage.setItem('neon_user', name);
            initApp();
        };

        function initApp() {
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('display-name').innerText = currentUser;
            document.getElementById('user-title').innerText = currentUser.toUpperCase();
            document.getElementById('avatar').innerText = currentUser[0].toUpperCase();
            set(ref(db, 'online/' + currentUser), true);
            loadUserList();
            switchChat('global');
        }

        window.handleLogout = () => {
            remove(ref(db, 'online/' + currentUser));
            localStorage.removeItem('neon_user');
            location.reload();
        };

        window.toggleTheme = () => {
            document.body.classList.toggle('light');
            const isLight = document.body.classList.contains('light');
            document.getElementById('t-icon').className = isLight ? "fa-solid fa-sun" : "fa-solid fa-moon";
        };

        window.toggleSide = () => {
            document.getElementById('side').classList.toggle('active');
            document.getElementById('overlay').classList.toggle('active');
        };

        window.switchChat = (chatId) => {
            activeChat = chatId;
            document.getElementById('current-chat-title').innerText = chatId === 'global' ? 'Global Lounge' : '@' + chatId;
            if(window.innerWidth < 1024) toggleSide();
            loadMessages();
        };

        function loadUserList() {
            onValue(ref(db, 'online'), (snapshot) => {
                const list = document.getElementById('user-list');
                list.innerHTML = "";
                snapshot.forEach(userSnap => {
                    const name = userSnap.key;
                    if(name !== currentUser) {
                        const div = document.createElement('div');
                        div.className = "p-3 bg-white/5 rounded-xl cursor-pointer hover:bg-white/10 flex items-center gap-3 text-xs font-semibold";
                        div.innerHTML = `<div class="w-2 h-2 rounded-full bg-green-500 shadow-lg shadow-green-500/50"></div> ${name}`;
                        div.onclick = () => switchChat(name);
                        list.appendChild(div);
                    }
                });
            });
        }

        function loadMessages() {
            const path = activeChat === 'global' ? 'global' : 'private/' + [currentUser, activeChat].sort().join('_');
            onValue(ref(db, 'messages/' + path), (snapshot) => {
                const flow = document.getElementById('chat-flow');
                flow.innerHTML = "";
                snapshot.forEach(msgSnap => {
                    const data = msgSnap.val();
                    const isMine = data.sender === currentUser;
                    const div = document.createElement('div');
                    div.className = `msg-card ${isMine ? 'mine' : 'other'}`;
                    div.innerHTML = `<p style="font-size:9px; opacity:0.6; margin-bottom:4px; font-weight:800;">${data.sender.toUpperCase()}</p>${data.text}`;
                    flow.appendChild(div);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        document.getElementById('chat-form').onsubmit = (e) => {
            e.preventDefault();
            const input = document.getElementById('msg-input');
            if(!input.value.trim() || !currentUser) return;
            const path = activeChat === 'global' ? 'global' : 'private/' + [currentUser, activeChat].sort().join('_');
            push(ref(db, 'messages/' + path), {
                sender: currentUser,
                text: input.value,
                time: Date.now()
            });
            input.value = "";
        };

        // Tab focus activity
        window.onbeforeunload = () => { if(currentUser) remove(ref(db, 'online/' + currentUser)); };
    </script>
</body>
</html>
