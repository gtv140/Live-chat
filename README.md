<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro | Global Business Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.3s; }
        .glass { background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.4); }
        .dark-mode { background-color: #0f172a; color: #f1f5f9; }
        .dark-mode .glass { background: #1e293b; border-color: #334155; }
        #chat-flow { height: calc(100vh - 200px); scroll-behavior: smooth; }
        .active-room { background: #2563eb !important; color: white !important; box-shadow: 0 10px 15px -3px rgba(37, 99, 235, 0.3); }
        .hidden { display: none; }
        /* Professional Scrollbar */
        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
    </style>
</head>
<body class="h-screen flex flex-col overflow-hidden bg-slate-50">

    <div id="login-screen" class="fixed inset-0 z-[100] bg-[#1e40af] flex items-center justify-center p-6">
        <div class="bg-white p-10 rounded-[2.5rem] shadow-2xl w-full max-w-md text-center">
            <div class="w-20 h-20 bg-blue-100 text-blue-600 rounded-2xl flex items-center justify-center mx-auto mb-6 rotate-3">
                <i class="fa-solid fa-briefcase text-3xl"></i>
            </div>
            <h2 class="text-3xl font-extrabold text-slate-900 mb-2">Business Portal</h2>
            <p class="text-slate-500 mb-8 font-medium">Connect with your global team instantly.</p>
            <input id="username-input" type="text" placeholder="Your Full Name" class="w-full bg-slate-50 border-2 border-slate-100 rounded-2xl p-4 mb-4 outline-none focus:border-blue-500 text-center font-bold">
            <button id="join-btn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-2xl shadow-lg transition-all active:scale-95">
                Launch Workspace <i class="fa-solid fa-rocket ml-2"></i>
            </button>
        </div>
    </div>

    <div id="main-app" class="hidden flex h-full">
        <aside class="w-20 md:w-72 glass border-r flex flex-col z-20 transition-all">
            <div class="p-6 border-b flex items-center justify-between">
                <span class="font-bold text-xl tracking-tight hidden md:block italic">ChatPro <span class="text-blue-600">Biz</span></span>
                <button onclick="logout()" class="text-slate-400 hover:text-red-500 transition"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto p-4 space-y-6">
                <div>
                    <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-3 ml-2">Channels</p>
                    <div id="room-global" onclick="switchRoom('global')" class="active-room p-3 rounded-xl cursor-pointer flex items-center gap-3 mb-1">
                        <i class="fa-solid fa-hashtag w-5 text-center"></i> <span class="hidden md:block text-sm font-semibold">general-hq</span>
                    </div>
                </div>

                <div>
                    <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-3 ml-2">Direct Messages</p>
                    <div id="users-list" class="space-y-1"></div>
                </div>
            </div>

            <div class="p-4 border-t bg-slate-50/50">
                <div class="flex items-center gap-3">
                    <div class="w-10 h-10 rounded-xl bg-blue-600 text-white flex items-center justify-center font-bold" id="my-avatar">?</div>
                    <div class="hidden md:block overflow-hidden">
                        <p class="text-xs font-bold truncate" id="my-name">User Name</p>
                        <p class="text-[10px] text-green-500 font-bold">● ONLINE</p>
                    </div>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col bg-white">
            <header class="p-4 glass border-b flex items-center justify-between shadow-sm">
                <div class="flex items-center gap-4">
                    <div id="chat-icon" class="w-10 h-10 bg-blue-50 text-blue-600 rounded-xl flex items-center justify-center">
                        <i class="fa-solid fa-hashtag"></i>
                    </div>
                    <div>
                        <h4 id="chat-title" class="font-bold text-slate-800">general-hq</h4>
                        <p id="typing-status" class="text-[10px] text-blue-500 font-bold h-3 italic"></p>
                    </div>
                </div>
                <div class="hidden lg:flex bg-slate-100 rounded-lg items-center px-3 py-1.5 w-64 border">
                    <i class="fa-solid fa-search text-slate-400 text-xs mr-2"></i>
                    <input type="text" placeholder="Search history..." class="bg-transparent text-xs outline-none w-full">
                </div>
            </header>

            <div id="chat-flow" class="flex-1 p-6 space-y-6"></div>

            <div class="p-4 bg-slate-50/50 border-t">
                <form id="msg-form" class="max-w-5xl mx-auto glass p-2 rounded-2xl flex items-center gap-2 shadow-xl border-2 border-white">
                    <input type="file" id="file-input" class="hidden">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="w-10 h-10 text-slate-400 hover:text-blue-600 transition">
                        <i class="fa-solid fa-plus-circle text-xl"></i>
                    </button>
                    
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Type a professional message..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium text-slate-700">
                    
                    <button type="button" onclick="addEmoji('🔥')" class="text-slate-400 hover:text-orange-500 px-1"><i class="fa-solid fa-fire text-lg"></i></button>
                    
                    <button id="send-btn" class="bg-blue-600 text-white px-5 py-2.5 rounded-xl font-bold flex items-center gap-2 hover:bg-blue-700 transition shadow-lg shadow-blue-100">
                        <span class="hidden md:block">Send</span> <i class="fa-solid fa-paper-plane text-xs"></i>
                    </button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let myName = "";
        let currentRoom = "global";

        // --- SESSION PERSISTENCE (Refresh logic) ---
        window.onload = () => {
            const savedUser = localStorage.getItem('chatProUser');
            if (savedUser) {
                initWorkspace(savedUser);
            }
        };

        document.getElementById('join-btn').onclick = () => {
            const name = document.getElementById('username-input').value.trim();
            if (name.length > 2) {
                localStorage.setItem('chatProUser', name);
                initWorkspace(name);
            }
        };

        function initWorkspace(name) {
            myName = name;
            document.getElementById('login-screen').classList.add('hidden');
            document.getElementById('main-app').classList.remove('hidden');
            document.getElementById('my-name').innerText = myName;
            document.getElementById('my-avatar').innerText = myName[0].toUpperCase();
            
            // Set Online Status
            set(ref(db, 'presence/' + myName), { status: 'online', lastSeen: serverTimestamp() });
            
            startCoreLogic();
        }

        function startCoreLogic() {
            // Typing Indicator
            const input = document.getElementById('msg-input');
            input.oninput = () => {
                set(ref(db, `typing/${currentRoom}/${myName}`), true);
                setTimeout(() => set(ref(db, `typing/${currentRoom}/${myName}`), false), 2000);
            };

            onValue(ref(db, `typing/${currentRoom}`), (snap) => {
                let typers = [];
                snap.forEach(c => { if(c.val() === true && c.key !== myName) typers.push(c.key) });
                document.getElementById('typing-status').innerText = typers.length ? typers.join(', ') + ' is typing...' : '';
            });

            // Users List
            onValue(ref(db, 'presence'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    if(c.key !== myName) {
                        const div = document.createElement('div');
                        div.id = "user-" + c.key;
                        div.className = "p-3 rounded-xl cursor-pointer hover:bg-slate-100 flex items-center gap-3 transition text-slate-600";
                        div.innerHTML = `<div class="w-8 h-8 rounded-lg bg-blue-50 text-blue-600 flex items-center justify-center font-bold text-xs">${c.key[0]}</div><span class="text-xs font-semibold">${c.key}</span>`;
                        div.onclick = () => switchRoom(c.key);
                        list.appendChild(div);
                    }
                });
            });

            loadMessages("global");
        }

        // --- ROOM LOGIC ---
        window.switchRoom = (target) => {
            currentRoom = (target === 'global') ? 'global' : (myName < target ? myName + "_" + target : target + "_" + myName);
            
            // UI Toggle
            document.getElementById('chat-title').innerText = target === 'global' ? 'general-hq' : target;
            document.getElementById('chat-icon').innerHTML = target === 'global' ? '<i class="fa-solid fa-hashtag"></i>' : '<i class="fa-solid fa-user"></i>';
            
            // Reset active classes
            document.querySelectorAll('.active-room').forEach(el => el.classList.remove('active-room'));
            if(target === 'global') document.getElementById('room-global').classList.add('active-room');
            
            loadMessages(currentRoom);
        };

        function loadMessages(room) {
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, 'msgs/' + room), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const div = document.createElement('div');
                div.className = `flex ${isMine ? 'justify-end' : 'justify-start'} animate-pop`;
                
                let content = data.fileUrl ? 
                    (data.type === 'image' ? `<img src="${data.fileUrl}" class="rounded-xl max-w-full shadow-md border-4 border-white cursor-zoom-in" onclick="window.open('${data.fileUrl}')">` : `<a href="${data.fileUrl}" target="_blank" class="bg-blue-50 p-3 rounded-lg flex items-center gap-3 text-xs border border-blue-100"><i class="fa-solid fa-file-invoice text-blue-600 text-xl"></i><div><p class="font-bold text-blue-800">${data.fileName}</p><p class="text-[10px] text-blue-400 font-medium italic underline">Download Attachment</p></div></a>`) : 
                    `<p class="text-sm font-medium leading-relaxed">${data.text}</p>`;

                div.innerHTML = `
                    <div class="max-w-[80%] ${isMine ? 'bg-blue-600 text-white rounded-2xl rounded-tr-none' : 'bg-white text-slate-800 rounded-2xl rounded-tl-none'} p-4 shadow-xl shadow-slate-200/50">
                        <div class="flex justify-between items-center mb-1 gap-8">
                            <span class="text-[10px] font-black uppercase tracking-tighter opacity-50">${isMine ? 'YOU' : data.sender}</span>
                            <span class="text-[8px] opacity-40 font-bold">${new Date(data.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}</span>
                        </div>
                        ${content}
                    </div>`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        // --- ACTIONS ---
        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const inp = document.getElementById('msg-input');
            if(inp.value.trim()){
                push(ref(db, 'msgs/' + currentRoom), { sender: myName, text: inp.value, time: Date.now() });
                inp.value = "";
            }
        };

        document.getElementById('file-input').onchange = async (e) => {
            const file = e.target.files[0];
            if(!file) return;
            const sRefObj = sRef(storage, `biz_files/${Date.now()}_${file.name}`);
            await uploadBytes(sRefObj, file);
            const url = await getDownloadURL(sRefObj);
            push(ref(db, 'msgs/' + currentRoom), {
                sender: myName, fileUrl: url, fileName: file.name,
                type: file.type.includes('image') ? 'image' : 'file', time: Date.now()
            });
        };

        window.logout = () => {
            localStorage.removeItem('chatProUser');
            location.reload();
        };

        window.addEmoji = (emoji) => document.getElementById('msg-input').value += emoji;

    </script>
</body>
</html>
