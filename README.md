<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ChatPro | Fully Responsive Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #0f172a; height: 100dvh; margin: 0; overflow: hidden; }
        
        /* Layout Structure */
        .app-shell { display: flex; height: 100dvh; width: 100%; background: #f8fafc; }
        
        /* Responsive Sidebar */
        .sidebar { 
            width: 280px; 
            background: white; 
            border-right: 1px solid #e2e8f0; 
            display: flex; 
            flex-direction: column; 
            transition: transform 0.3s ease;
            z-index: 100;
        }

        /* Chat Flow Scroll */
        #chat-flow { 
            flex: 1; 
            overflow-y: auto; 
            padding: 20px; 
            display: flex; 
            flex-direction: column; 
            gap: 12px;
            background: #f1f5f9;
        }

        /* Responsive Logic */
        @media (max-width: 768px) {
            .sidebar { position: fixed; left: 0; top: 0; bottom: 0; transform: translateX(-100%); }
            .sidebar.active { transform: translateX(0); box-shadow: 20px 0 50px rgba(0,0,0,0.1); }
            .main-content { width: 100%; }
        }

        .msg-bubble { max-width: 85%; padding: 12px 16px; border-radius: 18px; font-size: 14px; position: relative; line-height: 1.5; }
        .msg-mine { align-self: flex-end; background: linear-gradient(135deg, #6366f1, #4f46e5); color: white; border-bottom-right-radius: 4px; }
        .msg-theirs { align-self: flex-start; background: white; color: #1e293b; border-bottom-left-radius: 4px; border: 1px solid #e2e8f0; }

        /* Overlay for Mobile */
        #overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.4); z-index: 90; }
        #overlay.active { display: block; }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[200] bg-indigo-600 flex items-center justify-center p-6">
        <div class="bg-white p-8 rounded-[2rem] shadow-2xl w-full max-w-sm text-center">
            <h2 class="text-2xl font-bold mb-6 text-slate-800">ChatPro <span class="text-indigo-600">v5.0</span></h2>
            <input id="username-input" type="text" placeholder="Apna Naam Likhein..." class="w-full p-4 bg-slate-50 border rounded-xl mb-4 text-center font-bold outline-none focus:border-indigo-600">
            <button id="join-btn" class="w-full bg-indigo-600 text-white font-bold py-4 rounded-xl shadow-lg">Start Chatting</button>
        </div>
    </div>

    <div id="overlay" onclick="toggleSidebar()"></div>

    <div class="app-shell">
        <aside id="sidebar" class="sidebar">
            <div class="p-6 border-b flex justify-between items-center">
                <span class="font-bold text-xl text-indigo-600">Workspace</span>
                <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div id="users-list" class="flex-1 overflow-y-auto p-4 space-y-2">
                </div>
            <div class="p-4 border-t flex items-center justify-between bg-slate-50">
                <div class="flex items-center gap-2">
                    <div id="u-avatar" class="w-8 h-8 rounded-lg bg-indigo-600 text-white flex items-center justify-center text-xs font-bold">?</div>
                    <span id="u-name" class="text-xs font-bold truncate max-w-[100px]">User</span>
                </div>
                <button onclick="logout()" class="text-slate-400 hover:text-red-500"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </aside>

        <main class="flex-1 flex flex-col min-w-0">
            <header class="h-16 bg-white border-b flex items-center justify-between px-4 md:px-8 shrink-0">
                <div class="flex items-center gap-3">
                    <button onclick="toggleSidebar()" class="md:hidden p-2 text-slate-500 text-xl"><i class="fa-solid fa-bars"></i></button>
                    <h2 class="font-bold text-slate-800"># global-hq</h2>
                </div>
                <button onclick="sendLoc()" class="text-indigo-600 p-2"><i class="fa-solid fa-location-dot text-xl"></i></button>
            </header>

            <div id="chat-flow"></div>

            <div class="p-3 bg-white border-t shrink-0">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-2 bg-slate-100 p-1.5 rounded-2xl border">
                    <button type="button" id="voice-btn" class="w-10 h-10 text-slate-400 hover:text-indigo-600"><i class="fa-solid fa-microphone text-xl"></i></button>
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Write message..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium">
                    <button class="bg-indigo-600 text-white w-10 h-10 rounded-xl flex items-center justify-center shadow-lg active:scale-95 transition-all"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        // Firebase Config (Aapka original config)
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
                div.className = `msg-bubble ${isMine ? 'msg-mine' : 'msg-theirs'}`;
                
                let content = data.text || "";
                if(data.locUrl) content = `<a href="${data.locUrl}" target="_blank" class="text-xs font-bold underline">📍 Meri Location Dekho</a>`;

                div.innerHTML = `<p class="text-[9px] font-bold uppercase opacity-50 mb-1">${data.sender}</p><div>${content}</div>`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });

            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    const d = document.createElement('div');
                    d.className = "p-3 rounded-xl hover:bg-slate-50 flex items-center gap-3 transition cursor-pointer";
                    d.innerHTML = `<div class="w-8 h-8 rounded-lg bg-indigo-100 text-indigo-600 flex items-center justify-center font-bold text-xs">${c.key[0]}</div><span class="text-sm font-medium">${c.key}</span>`;
                    list.appendChild(d);
                });
            });
        }

        window.toggleSidebar = () => {
            document.getElementById('sidebar').classList.toggle('active');
            document.getElementById('overlay').classList.toggle('active');
        };

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
