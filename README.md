<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro | Enterprise Communication Suite</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.4s; }
        .glass-panel { background: rgba(255, 255, 255, 0.7); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.4); }
        .dark-mode { background-color: #0f172a; color: #f1f5f9; }
        .dark-mode .glass-panel { background: rgba(30, 41, 59, 0.8); border-color: #334155; }
        #chat-flow { height: calc(100vh - 200px); scroll-behavior: smooth; }
        .message-anim { animation: slideUp 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .typing-dot { animation: blink 1s infinite; }
        @keyframes blink { 0%, 100% { opacity: 0.2; } 50% { opacity: 1; } }
    </style>
</head>
<body class="h-screen flex flex-col overflow-hidden bg-slate-50 transition-colors">

    <div id="login-screen" class="fixed inset-0 z-[100] bg-[#1e40af] flex items-center justify-center p-6">
        <div class="bg-white/95 backdrop-blur-xl p-10 rounded-[2.5rem] shadow-2xl w-full max-w-md text-center">
            <div class="w-24 h-24 bg-blue-600 text-white rounded-3xl flex items-center justify-center mx-auto mb-8 rotate-12 shadow-xl">
                <i class="fa-solid fa-comments-dollar text-4xl -rotate-12"></i>
            </div>
            <h2 class="text-3xl font-extrabold text-slate-900 mb-2 italic">ChatPro <span class="text-blue-600">Biz</span></h2>
            <p class="text-slate-500 mb-8 font-medium">The most secure way to collaborate.</p>
            <input id="username-input" type="text" placeholder="Enter Business Name" class="w-full bg-slate-100 border-none rounded-2xl p-4 mb-4 outline-none focus:ring-4 focus:ring-blue-100 text-center font-bold text-lg transition-all">
            <button id="join-btn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-5 rounded-2xl shadow-lg shadow-blue-200 transition-all hover:-translate-y-1 active:scale-95">
                Launch Workspace <i class="fa-solid fa-arrow-right-long ml-2"></i>
            </button>
        </div>
    </div>

    <div id="main-app" class="hidden flex h-full">
        <aside class="w-20 md:w-80 glass-panel border-r flex flex-col z-20">
            <div class="p-6 border-b flex items-center justify-between">
                <span class="font-bold text-2xl tracking-tighter hidden md:block">Workspace</span>
                <button id="theme-toggle" class="p-2 rounded-xl hover:bg-slate-200 transition"><i class="fa-solid fa-moon"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto p-4 space-y-4">
                <div id="global-tab" class="p-4 rounded-2xl cursor-pointer bg-blue-600 text-white flex items-center gap-4 shadow-lg shadow-blue-100 transition-all active:scale-95">
                    <i class="fa-solid fa-layer-group text-lg"></i>
                    <div class="hidden md:block">
                        <p class="font-bold text-sm">General Room</p>
                        <p class="text-[10px] opacity-80">Public Broadcast</p>
                    </div>
                </div>
                
                <div class="pt-4">
                    <p class="text-[10px] font-bold text-slate-400 uppercase tracking-[0.2em] mb-4 ml-2">Team Members</p>
                    <div id="users-list" class="space-y-1"></div>
                </div>
            </div>

            <div class="p-6 border-t bg-slate-50/50">
                <div class="flex items-center gap-4">
                    <div class="w-12 h-12 rounded-2xl bg-gradient-to-tr from-blue-600 to-indigo-600 flex items-center justify-center text-white font-bold shadow-md" id="my-avatar">?</div>
                    <div class="hidden md:block">
                        <p class="text-sm font-bold" id="my-name">User</p>
                        <div class="flex items-center gap-1"><span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span><span class="text-[10px] font-bold text-green-600 uppercase">Pro Member</span></div>
                    </div>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col relative bg-white">
            <header class="p-5 border-b glass-panel flex items-center justify-between sticky top-0 z-10">
                <div class="flex items-center gap-4">
                    <div id="active-icon" class="w-12 h-12 bg-slate-100 rounded-2xl flex items-center justify-center text-blue-600">
                        <i class="fa-solid fa-hashtag text-xl"></i>
                    </div>
                    <div>
                        <h4 id="chat-title" class="font-bold text-lg text-slate-800">General Room</h4>
                        <p id="typing-status" class="text-[10px] text-blue-500 font-bold h-3"></p>
                    </div>
                </div>
                <div class="flex gap-2">
                    <button class="w-10 h-10 rounded-xl hover:bg-slate-100 flex items-center justify-center text-slate-500"><i class="fa-solid fa-phone"></i></button>
                    <button class="w-10 h-10 rounded-xl hover:bg-slate-100 flex items-center justify-center text-slate-500"><i class="fa-solid fa-video"></i></button>
                </div>
            </header>

            <div id="chat-flow" class="flex-1 p-6 space-y-6"></div>

            <div class="p-6 bg-gradient-to-t from-slate-50 to-transparent">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-3 glass-panel p-3 rounded-[2rem] shadow-xl border-2 border-white">
                    <input type="file" id="file-input" class="hidden">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="w-10 h-10 text-slate-400 hover:text-blue-600 transition"><i class="fa-solid fa-paperclip text-xl"></i></button>
                    
                    <input id="msg-input" type="text" placeholder="Share your thoughts..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium">
                    
                    <button type="button" id="voice-btn" class="w-10 h-10 text-slate-400 hover:text-red-500 transition"><i class="fa-solid fa-microphone text-xl"></i></button>
                    
                    <button id="send-btn" class="bg-blue-600 text-white w-12 h-12 rounded-2xl flex items-center justify-center hover:bg-blue-700 shadow-lg shadow-blue-200 transition-all active:scale-90">
                        <i class="fa-solid fa-paper-plane"></i>
                    </button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, serverTimestamp, update } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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

        // APP START & USER HANDLER
        document.getElementById('join-btn').onclick = () => {
            const user = document.getElementById('username-input').value.trim();
            if(user.length > 2) {
                myName = user;
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('main-app').classList.remove('hidden');
                document.getElementById('my-name').innerText = myName;
                document.getElementById('my-avatar').innerText = myName[0].toUpperCase();
                set(ref(db, 'online/' + myName), { status: 'online' });
                runApp();
            }
        };

        function runApp() {
            // Typing Indicator Logic
            const msgInput = document.getElementById('msg-input');
            msgInput.addEventListener('input', () => {
                set(ref(db, `typing/${currentRoom}/${myName}`), { typing: true });
                setTimeout(() => set(ref(db, `typing/${currentRoom}/${myName}`), { typing: false }), 2000);
            });

            onValue(ref(db, `typing/${currentRoom}`), (snap) => {
                let typers = [];
                snap.forEach(c => { if(c.val().typing && c.key !== myName) typers.push(c.key) });
                document.getElementById('typing-status').innerText = typers.length ? typers.join(', ') + ' is typing...' : '';
            });

            // Users List
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    if(c.key !== myName) {
                        const div = document.createElement('div');
                        div.className = "p-3 rounded-xl cursor-pointer hover:bg-blue-50 flex items-center gap-3 transition";
                        div.innerHTML = `<div class="w-8 h-8 rounded-lg bg-slate-200 flex items-center justify-center font-bold text-xs">${c.key[0]}</div><span class="text-sm font-medium hidden md:block">${c.key}</span>`;
                        div.onclick = () => switchRoom(c.key);
                        list.appendChild(div);
                    }
                });
            });
            loadMessages("global");
        }

        // ROOM SWITCHING
        function switchRoom(target) {
            currentRoom = myName < target ? myName + "_" + target : target + "_" + myName;
            document.getElementById('chat-title').innerText = target;
            document.getElementById('active-icon').innerHTML = '<i class="fa-solid fa-user-tie"></i>';
            loadMessages(currentRoom);
        }

        document.getElementById('global-tab').onclick = () => {
            currentRoom = "global";
            document.getElementById('chat-title').innerText = "General Room";
            document.getElementById('active-icon').innerHTML = '<i class="fa-solid fa-layer-group"></i>';
            loadMessages("global");
        };

        // MESSAGE RENDERING
        function loadMessages(room) {
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, 'msgs/' + room), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const div = document.createElement('div');
                div.className = `flex ${isMine ? 'justify-end' : 'justify-start'} message-anim`;
                
                let content = data.fileUrl ? 
                    (data.type === 'image' ? `<img src="${data.fileUrl}" class="rounded-2xl max-w-full shadow-sm">` : `<a href="${data.fileUrl}" target="_blank" class="flex items-center gap-2 underline text-xs"><i class="fa-solid fa-file-pdf"></i> ${data.fileName}</a>`) : 
                    `<p class="text-sm font-medium">${data.text}</p>`;

                div.innerHTML = `
                    <div class="${isMine ? 'bg-blue-600 text-white rounded-3xl rounded-tr-none shadow-blue-100' : 'bg-white text-slate-800 rounded-3xl rounded-tl-none border shadow-sm'} p-4 max-w-xs shadow-lg relative">
                        <p class="text-[8px] font-black uppercase tracking-widest opacity-40 mb-1">${isMine ? 'Me' : data.sender}</p>
                        ${content}
                    </div>`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        // SENDING HANDLERS
        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const input = document.getElementById('msg-input');
            if(input.value.trim()){
                push(ref(db, 'msgs/' + currentRoom), { sender: myName, text: input.value, time: serverTimestamp() });
                input.value = "";
            }
        };

        // IMAGE UPLOAD
        document.getElementById('file-input').onchange = async (e) => {
            const file = e.target.files[0];
            if(!file) return;
            const sRefObj = sRef(storage, `biz_files/${Date.now()}_${file.name}`);
            await uploadBytes(sRefObj, file);
            const url = await getDownloadURL(sRefObj);
            push(ref(db, 'msgs/' + currentRoom), {
                sender: myName, fileUrl: url, fileName: file.name,
                type: file.type.includes('image') ? 'image' : 'file', time: serverTimestamp()
            });
        };

        // THEME TOGGLE
        document.getElementById('theme-toggle').onclick = () => document.body.classList.toggle('dark-mode');
    </script>
</body>
</html>
