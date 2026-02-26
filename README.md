<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect | Professional Network</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap');
        
        :root { --p: #2563eb; --bg: #f9fafb; --card: #ffffff; --text: #1f2937; }
        .dark { --p: #3b82f6; --bg: #111827; --card: #1f2937; --text: #f9fafb; }

        * { font-family: 'Inter', sans-serif; transition: all 0.2s ease; }
        body { background-color: var(--bg); color: var(--text); height: 100dvh; margin: 0; overflow: hidden; }

        .glass-nav { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(10px); border-bottom: 1px solid rgba(0,0,0,0.1); }
        .dark .glass-nav { background: rgba(31, 41, 55, 0.8); border-bottom: 1px solid rgba(255,255,255,0.1); }

        .sidebar { width: 320px; background: var(--card); border-right: 1px solid rgba(0,0,0,0.05); }
        @media (max-width: 1024px) {
            .sidebar { position: fixed; left: -100%; z-index: 50; height: 100%; width: 85%; }
            .sidebar.active { left: 0; box-shadow: 10px 0 30px rgba(0,0,0,0.1); }
        }

        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; }
        .bubble { max-width: 75%; padding: 12px 16px; border-radius: 18px; position: relative; font-size: 14.5px; box-shadow: 0 2px 8px rgba(0,0,0,0.04); }
        .mine { align-self: flex-end; background: var(--p); color: white; border-bottom-right-radius: 2px; }
        .other { align-self: flex-start; background: var(--card); border: 1px solid rgba(0,0,0,0.05); border-bottom-left-radius: 2px; }

        .input-box { background: var(--card); border: 1px solid rgba(0,0,0,0.1); border-radius: 50px; padding: 10px 20px; }
        .dark .input-box { border-color: rgba(255,255,255,0.1); }
    </style>
</head>
<body class="flex flex-col">

    <div id="auth-screen" class="fixed inset-0 z-[100] flex items-center justify-center bg-blue-600 p-6">
        <div class="bg-white rounded-[2rem] p-10 max-w-sm w-full shadow-2xl text-center">
            <div class="w-20 h-20 bg-blue-100 text-blue-600 rounded-3xl flex items-center justify-center mx-auto mb-6">
                <i class="fa-solid fa-link text-4xl"></i>
            </div>
            <h1 class="text-3xl font-bold text-gray-800 mb-2">Live Connect</h1>
            <p class="text-gray-500 mb-8 text-sm">Welcome! Login to start connecting.</p>
            <input id="u-name" type="text" placeholder="Your Full Name" class="w-full bg-gray-50 border border-gray-200 p-4 rounded-2xl mb-4 outline-none focus:border-blue-500 font-semibold text-gray-800 text-center">
            <button onclick="doLogin()" class="w-full bg-blue-600 text-white py-4 rounded-2xl font-bold shadow-lg hover:bg-blue-700 active:scale-95 transition-transform">JOIN NETWORK</button>
        </div>
    </div>

    <nav class="glass-nav h-16 flex items-center px-6 justify-between shrink-0 z-40">
        <div class="flex items-center gap-4">
            <button onclick="toggleSide()" class="lg:hidden text-xl"><i class="fa-solid fa-bars"></i></button>
            <div class="flex items-center gap-2">
                <div class="w-8 h-8 bg-blue-600 rounded-lg flex items-center justify-center text-white"><i class="fa-solid fa-link text-xs"></i></div>
                <span class="font-bold text-lg tracking-tight">Live Connect</span>
            </div>
        </div>
        <div class="flex items-center gap-4">
            <button onclick="toggleMode()" class="w-10 h-10 rounded-full border flex items-center justify-center"><i id="m-icon" class="fa-solid fa-moon"></i></button>
            <div class="hidden sm:flex items-center gap-2 bg-blue-50 dark:bg-blue-900/20 px-4 py-2 rounded-full">
                <span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                <span id="welcome-txt" class="text-xs font-bold text-blue-600 dark:text-blue-400">Hi, Member</span>
            </div>
        </div>
    </nav>

    <div class="flex flex-1 overflow-hidden">
        <aside id="sidebar" class="sidebar flex flex-col shrink-0">
            <div class="p-6">
                <h3 class="text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-4">Chat Channels</h3>
                <button onclick="setChat('global')" class="w-full flex items-center gap-3 p-4 bg-blue-600 text-white rounded-2xl shadow-lg mb-8 font-semibold">
                    <i class="fa-solid fa-globe"></i> Global Feed
                </button>

                <h3 class="text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-4">Online Users</h3>
                <div id="u-list" class="space-y-3"></div>
            </div>

            <div class="mt-auto p-6 border-t bg-gray-50/50 dark:bg-black/10">
                <div class="flex items-center gap-3">
                    <div id="my-av" class="w-12 h-12 rounded-2xl bg-blue-600 flex items-center justify-center text-white font-bold text-lg">?</div>
                    <div class="flex-1 overflow-hidden">
                        <p id="my-name" class="font-bold text-sm truncate">User Name</p>
                        <p class="text-[10px] text-green-500 font-bold uppercase">Connected</p>
                    </div>
                    <button onclick="doLogout()" class="text-red-500 bg-red-50 p-3 rounded-xl hover:bg-red-100"><i class="fa-solid fa-power-off"></i></button>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col min-w-0 bg-white/50 dark:bg-transparent">
            <header class="h-14 px-6 border-b flex items-center bg-white/30 dark:bg-black/10 backdrop-blur-sm">
                <h2 id="chat-title" class="font-bold text-sm uppercase tracking-wide">Global Feed</h2>
            </header>

            <div id="chat-flow"></div>

            <div class="p-4 bg-white/50 dark:bg-transparent backdrop-blur-md">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-3 input-box">
                    <input type="file" id="img-in" class="hidden" accept="image/*" onchange="upImg(this)">
                    <button type="button" onclick="document.getElementById('img-in').click()" class="text-gray-400 hover:text-blue-600"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="m-text" type="text" placeholder="Write something..." class="flex-1 bg-transparent outline-none text-sm font-medium py-2" autocomplete="off">
                    <button class="bg-blue-600 text-white w-10 h-10 rounded-full flex items-center justify-center shadow-lg active:scale-90"><i class="fa-solid fa-paper-plane text-sm"></i></button>
                </form>
            </div>
        </main>
    </div>

    <div id="overlay" class="fixed inset-0 bg-black/50 z-40 hidden" onclick="toggleSide()"></div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        const cfg = {
            apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
            databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
            projectId: "live-chat-b810c",
            storageBucket: "live-chat-b810c.firebasestorage.app",
            appId: "1:555058795334:web:f668887409800c32970b47"
        };

        const app = initializeApp(cfg);
        const db = getDatabase(app);
        const st = getStorage(app);

        let user = localStorage.getItem('lc_user'), active = 'global';

        if(user) login();

        window.doLogin = () => {
            const val = document.getElementById('u-name').value.trim();
            if(!val) return alert("Please enter your name!");
            user = val; localStorage.setItem('lc_user', val);
            login();
        };

        function login() {
            document.getElementById('auth-screen').style.display = 'none';
            document.getElementById('my-name').innerText = user;
            document.getElementById('welcome-txt').innerText = `Hi, ${user.split(' ')[0]}`;
            document.getElementById('my-av').innerText = user[0].toUpperCase();
            set(ref(db, 'online/' + user), true);
            loadUsers();
            setChat('global');
        }

        window.doLogout = () => {
            remove(ref(db, 'online/' + user));
            localStorage.clear();
            location.reload();
        };

        window.toggleMode = () => {
            document.documentElement.classList.toggle('dark');
            const isD = document.documentElement.classList.contains('dark');
            document.getElementById('m-icon').className = isD ? "fa-solid fa-sun" : "fa-solid fa-moon";
        };

        window.toggleSide = () => {
            document.getElementById('sidebar').classList.toggle('active');
            document.getElementById('overlay').classList.toggle('hidden');
        };

        window.setChat = (c) => {
            active = c;
            document.getElementById('chat-title').innerText = c === 'global' ? 'Global Feed' : '@' + c;
            if(window.innerWidth < 1024) toggleSide();
            loadMsgs();
        };

        function loadUsers() {
            onValue(ref(db, 'online'), (s) => {
                const list = document.getElementById('u-list'); list.innerHTML = "";
                s.forEach(u => {
                    if(u.key !== user) {
                        const d = document.createElement('div');
                        d.className = "flex items-center gap-3 p-3 bg-gray-50 dark:bg-white/5 rounded-xl cursor-pointer hover:bg-blue-50";
                        d.innerHTML = `<div class="w-8 h-8 rounded-full bg-blue-100 text-blue-600 flex items-center justify-center font-bold text-xs">${u.key[0]}</div> <span class="text-sm font-semibold">${u.key}</span>`;
                        d.onclick = () => setChat(u.key);
                        list.appendChild(d);
                    }
                });
            });
        }

        function loadMsgs() {
            const p = active === 'global' ? 'global' : 'private/' + [user, active].sort().join('_');
            onValue(ref(db, 'chats/' + p), (s) => {
                const flow = document.getElementById('chat-flow'); flow.innerHTML = "";
                s.forEach(m => {
                    const d = m.val(), isM = d.sender === user;
                    const div = document.createElement('div');
                    div.className = `bubble ${isM ? 'mine' : 'other'}`;
                    div.innerHTML = `
                        <p class="text-[9px] font-bold uppercase opacity-50 mb-1">${d.sender}</p>
                        ${d.img ? `<img src="${d.img}" class="rounded-lg max-w-full">` : `<span>${d.text}</span>`}
                    `;
                    flow.appendChild(div);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const i = document.getElementById('m-text');
            if(!i.value.trim()) return;
            const p = active === 'global' ? 'global' : 'private/' + [user, active].sort().join('_');
            push(ref(db, 'chats/' + p), { sender: user, text: i.value, time: Date.now() });
            i.value = "";
        };

        window.upImg = async (i) => {
            const f = i.files[0]; if(!f) return;
            const r = sRef(st, `files/${Date.now()}`);
            const snap = await uploadBytes(r, f);
            const url = await getDownloadURL(snap.ref);
            const p = active === 'global' ? 'global' : 'private/' + [user, active].sort().join('_');
            push(ref(db, 'chats/' + p), { sender: user, img: url, time: Date.now() });
        };
    </script>
</body>
</html>
