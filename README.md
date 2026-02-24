<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ChatPro | Business & Personal Suite</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #f8fafc; overflow: hidden; height: 100vh; }
        
        /* Layout Structure */
        .app-container { display: flex; height: 100vh; overflow: hidden; }
        .sidebar { width: 300px; background: #ffffff; border-right: 1px solid #e2e8f0; display: flex; flex-direction: column; z-index: 50; transition: 0.3s; }
        .main-chat { flex: 1; display: flex; flex-direction: column; background: #f1f5f9; position: relative; }
        
        /* Scrollable Chat Area */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 10px; scroll-behavior: smooth; }
        #chat-flow::-webkit-scrollbar { width: 5px; }
        #chat-flow::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }

        /* Professional Bubbles */
        .msg { max-width: 80%; padding: 12px 16px; font-size: 14px; position: relative; line-height: 1.5; }
        .msg.mine { align-self: flex-end; background: #4f46e5; color: white; border-radius: 18px 18px 2px 18px; box-shadow: 0 4px 12px rgba(79, 70, 229, 0.15); }
        .msg.theirs { align-self: flex-start; background: white; color: #1e293b; border-radius: 18px 18px 18px 2px; border: 1px solid #e2e8f0; }

        /* Mobile Adjustments */
        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; bottom: 0; width: 85%; }
            .sidebar.active { left: 0; box-shadow: 20px 0 60px rgba(0,0,0,0.1); }
            .main-chat { width: 100vw; }
            .msg { max-width: 90%; }
        }

        .status-dot { width: 10px; height: 10px; border-radius: 50%; border: 2px solid white; position: absolute; bottom: 0; right: 0; }
        .online { background: #22c55e; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[100] bg-indigo-600 flex items-center justify-center p-6">
        <div class="bg-white p-10 rounded-[2.5rem] shadow-2xl w-full max-w-sm text-center">
            <div class="w-16 h-16 bg-indigo-50 text-indigo-600 rounded-2xl flex items-center justify-center mx-auto mb-6"><i class="fa-solid fa-briefcase text-2xl"></i></div>
            <h2 class="text-2xl font-bold text-slate-900 mb-2">Workspace Login</h2>
            <p class="text-sm text-slate-500 mb-8">Personal & Business Communication</p>
            <input id="username-input" type="text" placeholder="Enter your name..." class="w-full p-4 bg-slate-50 border rounded-xl mb-4 text-center font-bold outline-none focus:border-indigo-600">
            <button id="join-btn" class="w-full bg-indigo-600 text-white font-bold py-4 rounded-xl shadow-lg hover:bg-indigo-700 transition-all">Launch App</button>
        </div>
    </div>

    <div class="app-container">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 border-b flex justify-between items-center">
                <span class="font-bold text-xl tracking-tight text-slate-800">ChatPro <span class="text-indigo-600">Hub</span></span>
                <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-xmark"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto p-4 space-y-6">
                <div>
                    <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-3 ml-2">Channels (Business)</p>
                    <div class="space-y-1">
                        <div class="p-3 bg-indigo-50 text-indigo-600 rounded-xl font-semibold flex items-center gap-3 cursor-pointer"><i class="fa-solid fa-hashtag"></i> general-hq</div>
                        <div class="p-3 hover:bg-slate-50 rounded-xl font-medium text-slate-600 flex items-center gap-3 cursor-pointer"><i class="fa-solid fa-bullhorn"></i> announcements</div>
                    </div>
                </div>
                <div>
                    <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-3 ml-2">Direct Messages (Personal)</p>
                    <div id="users-list" class="space-y-1 text-sm text-slate-600"></div>
                </div>
            </div>

            <div class="p-4 border-t bg-slate-50 flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div class="relative">
                        <div id="u-avatar" class="w-10 h-10 rounded-xl bg-indigo-600 text-white flex items-center justify-center font-bold">?</div>
                        <div class="status-dot online"></div>
                    </div>
                    <div>
                        <p id="u-name" class="text-xs font-bold text-slate-800 truncate max-w-[100px]">User</p>
                        <p class="text-[10px] text-green-500 font-medium">Available</p>
                    </div>
                </div>
                <button onclick="logout()" class="text-slate-400 hover:text-red-500"><i class="fa-solid fa-right-from-bracket"></i></button>
            </div>
        </aside>

        <main class="main-chat">
            <header class="h-16 bg-white/80 backdrop-blur-md border-b flex items-center justify-between px-6 z-10">
                <div class="flex items-center gap-3">
                    <button onclick="toggleSidebar()" class="md:hidden p-2 text-slate-500"><i class="fa-solid fa-bars"></i></button>
                    <div>
                        <h2 class="font-bold text-slate-800"># general-hq</h2>
                        <p id="typing-status" class="text-[10px] text-indigo-500 font-medium h-3"></p>
                    </div>
                </div>
                <div class="flex gap-4">
                    <i class="fa-solid fa-phone text-slate-300 cursor-not-allowed"></i>
                    <i class="fa-solid fa-video text-slate-300 cursor-not-allowed"></i>
                </div>
            </header>

            <div id="chat-flow"></div>

            <div class="p-4 bg-white border-t">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-3 bg-slate-100 p-2 rounded-2xl border border-slate-200">
                    <button type="button" class="w-10 h-10 text-slate-400 hover:text-indigo-600"><i class="fa-solid fa-plus-circle text-xl"></i></button>
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Write a message..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium text-slate-700">
                    <button class="bg-indigo-600 text-white w-10 h-10 rounded-xl flex items-center justify-center shadow-lg active:scale-95 transition-all"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
            document.getElementById('u-name').innerText = n;
            document.getElementById('u-avatar').innerText = n[0].toUpperCase();
            set(ref(db, 'online/' + n), true);
            
            onChildAdded(ref(db, 'msgs/global'), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const flow = document.getElementById('chat-flow');
                const div = document.createElement('div');
                div.className = `msg ${isMine ? 'mine' : 'theirs'}`;
                div.innerHTML = `<p class="text-[9px] font-bold uppercase opacity-50 mb-1">${data.sender}</p><div>${data.text}</div>`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });

            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    if(c.key !== myName) {
                        const div = document.createElement('div');
                        div.className = "p-3 rounded-xl hover:bg-slate-50 cursor-pointer flex items-center gap-3 transition";
                        div.innerHTML = `<div class="w-8 h-8 rounded-lg bg-indigo-100 text-indigo-600 flex items-center justify-center font-bold text-xs">${c.key[0]}</div><span>${c.key}</span>`;
                        list.appendChild(div);
                    }
                });
            });
        }

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
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
