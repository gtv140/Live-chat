<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro | Ultimate Business & Personal Hub</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; transition: background 0.3s; }
        .dark-mode { background-color: #0f172a; color: #f1f5f9; }
        .dark-mode .glass { background: #1e293b; border-color: #334155; color: white; }
        .dark-mode aside { background: #1e293b; border-color: #334155; }
        .dark-mode .msg-received { background: #334155; color: white; }
        .glass { background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(10px); }
        #chat-flow { height: calc(100vh - 180px); overflow-y: auto; scroll-behavior: smooth; }
        .msg-bubble { max-width: 75%; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .hidden { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col overflow-hidden bg-gray-50">

    <div id="login-screen" class="fixed inset-0 z-[100] bg-gradient-to-br from-blue-600 to-indigo-800 flex items-center justify-center p-6">
        <div class="bg-white p-10 rounded-[2rem] shadow-2xl w-full max-w-md text-center">
            <div class="w-20 h-20 bg-blue-100 text-blue-600 rounded-full flex items-center justify-center mx-auto mb-6 shadow-inner">
                <i class="fa-solid fa-user-tie text-4xl"></i>
            </div>
            <h2 class="text-3xl font-bold text-gray-800 mb-2">ChatPro Access</h2>
            <p class="text-gray-500 mb-8">Enter your professional name to enter</p>
            <input id="username-input" type="text" placeholder="Username..." class="w-full border-2 border-gray-100 rounded-2xl p-4 mb-4 outline-none focus:border-blue-500 text-center font-bold text-lg">
            <button id="join-btn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-2xl shadow-xl transition-all">
                Enter Dashboard <i class="fa-solid fa-arrow-right ml-2"></i>
            </button>
        </div>
    </div>

    <div id="main-app" class="hidden flex h-full">
        <aside class="w-20 md:w-80 bg-white border-r flex flex-col z-20 transition-all">
            <div class="p-6 flex items-center gap-3 border-b">
                <div class="w-10 h-10 bg-blue-600 rounded-xl flex items-center justify-center text-white shadow-lg">
                    <i class="fa-solid fa-bolt"></i>
                </div>
                <span class="font-bold text-xl hidden md:block">ChatPro <span class="text-blue-600 text-sm">PRO</span></span>
            </div>
            <div class="flex-1 overflow-y-auto p-4 space-y-2">
                <div id="global-tab" class="p-3 rounded-xl cursor-pointer bg-blue-600 text-white flex items-center gap-3 shadow-md">
                    <i class="fa-solid fa-globe"></i> <span class="hidden md:block font-semibold">Global Room</span>
                </div>
                <h3 class="text-[10px] font-bold text-gray-400 uppercase tracking-widest mt-6 mb-2 hidden md:block pl-2">Online Partners</h3>
                <div id="users-list" class="space-y-1"></div>
            </div>
            <div class="p-4 border-t space-y-3">
                <button id="dark-toggle" class="w-full p-2 rounded-xl hover:bg-gray-100 flex items-center justify-center gap-2 transition">
                    <i class="fa-solid fa-moon"></i> <span class="hidden md:block text-sm font-medium">Dark Mode</span>
                </button>
                <div class="flex items-center gap-3 bg-blue-50 p-3 rounded-2xl">
                    <div class="w-10 h-10 rounded-full bg-blue-500 flex items-center justify-center text-white font-bold" id="my-avatar">?</div>
                    <div class="hidden md:block">
                        <p class="text-sm font-bold text-gray-800 leading-none" id="my-name">User</p>
                        <p class="text-[10px] text-green-500 mt-1">● ACTIVE</p>
                    </div>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col relative">
            <header class="p-4 bg-white/80 backdrop-blur-md border-b flex items-center justify-between shadow-sm z-10">
                <div class="flex items-center gap-3">
                    <div id="chat-header-icon" class="w-10 h-10 bg-blue-50 text-blue-600 rounded-full flex items-center justify-center">
                        <i class="fa-solid fa-hashtag"></i>
                    </div>
                    <div>
                        <h4 id="chat-title" class="font-bold text-gray-800">Global Network</h4>
                        <div class="flex items-center gap-1"><span class="w-2 h-2 bg-green-500 rounded-full"></span><span class="text-[10px] text-gray-400">Real-time Encrypted</span></div>
                    </div>
                </div>
                <div class="flex gap-4 text-gray-400 px-2">
                    <i class="fa-solid fa-video cursor-pointer hover:text-blue-500"></i>
                    <i class="fa-solid fa-circle-info cursor-pointer hover:text-blue-500"></i>
                </div>
            </header>

            <div id="chat-flow" class="flex-1 p-6 space-y-4"></div>

            <div class="p-4 bg-white border-t">
                <form id="msg-form" class="max-w-5xl mx-auto flex items-center gap-3 bg-gray-50 p-2 rounded-2xl border">
                    <input type="file" id="file-input" class="hidden">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="text-gray-400 hover:text-blue-500 ml-2">
                        <i class="fa-solid fa-paperclip text-xl"></i>
                    </button>
                    <input id="msg-input" type="text" placeholder="Type message or share files..." class="flex-1 bg-transparent p-2 outline-none text-sm">
                    <button id="send-btn" class="bg-blue-600 text-white px-6 py-2.5 rounded-xl flex items-center gap-2 hover:bg-blue-700 shadow-lg shadow-blue-200">
                        <i class="fa-solid fa-paper-plane text-xs"></i>
                    </button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
        import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        // CONFIG (Replace with your keys if needed)
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

        // AUTH & UI LOGIC
        document.getElementById('join-btn').onclick = () => {
            const val = document.getElementById('username-input').value.trim();
            if(val.length > 2) {
                myName = val;
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('main-app').classList.remove('hidden');
                document.getElementById('my-name').innerText = myName;
                document.getElementById('my-avatar').innerText = myName[0].toUpperCase();
                set(ref(db, 'online_users/' + myName), { status: 'online' });
                startApp();
            }
        };

        function startApp() {
            onValue(ref(db, 'online_users'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(child => {
                    if(child.key !== myName) {
                        const div = document.createElement('div');
                        div.className = "p-3 rounded-xl cursor-pointer hover:bg-gray-100 flex items-center gap-3 transition";
                        div.innerHTML = `<div class="w-8 h-8 rounded-lg bg-blue-100 text-blue-600 flex items-center justify-center font-bold text-xs">${child.key[0]}</div><span class="text-sm font-medium hidden md:block">${child.key}</span>`;
                        div.onclick = () => switchChat(child.key);
                        list.appendChild(div);
                    }
                });
            });
            loadMessages("global");
        }

        // CHAT ENGINE
        function switchChat(target) {
            currentRoom = myName < target ? myName + "_" + target : target + "_" + myName;
            document.getElementById('chat-title').innerText = target;
            document.getElementById('chat-header-icon').innerHTML = '<i class="fa-solid fa-user-shield"></i>';
            document.getElementById('global-tab').classList.remove('bg-blue-600');
            loadMessages(currentRoom);
        }

        document.getElementById('global-tab').onclick = () => {
            currentRoom = "global";
            document.getElementById('chat-title').innerText = "Global Network";
            document.getElementById('chat-header-icon').innerHTML = '<i class="fa-solid fa-hashtag"></i>';
            document.getElementById('global-tab').classList.add('bg-blue-600');
            loadMessages("global");
        };

        function loadMessages(roomId) {
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, 'messages/' + roomId), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const div = document.createElement('div');
                div.className = `flex ${isMine ? 'justify-end' : 'justify-start'}`;
                
                let msgContent = data.fileUrl ? 
                    (data.type === 'image' ? `<img src="${data.fileUrl}" class="rounded-lg max-w-full">` : `<a href="${data.fileUrl}" target="_blank" class="flex items-center gap-2 underline text-xs"><i class="fa-solid fa-file-pdf"></i> ${data.fileName}</a>`) : 
                    `<p class="text-sm leading-relaxed">${data.text}</p>`;

                div.innerHTML = `
                    <div class="msg-bubble ${isMine ? 'bg-blue-600 text-white rounded-2xl rounded-tr-none shadow-blue-200' : 'bg-white text-gray-800 rounded-2xl rounded-tl-none'} p-4 shadow-lg border border-gray-100">
                        <p class="text-[9px] font-bold opacity-50 mb-1">${isMine ? 'You' : data.sender}</p>
                        ${msgContent}
                    </div>`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        // SEND LOGIC
        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const input = document.getElementById('msg-input');
            if(input.value.trim()){
                push(ref(db, 'messages/' + currentRoom), { sender: myName, text: input.value, time: serverTimestamp() });
                input.value = "";
            }
        };

        // FILE UPLOAD
        document.getElementById('file-input').onchange = async (e) => {
            const file = e.target.files[0];
            if(!file) return;
            const btn = document.getElementById('send-btn');
            btn.innerHTML = '<i class="fa-solid fa-spinner fa-spin"></i>';
            const sRefObj = sRef(storage, `uploads/${Date.now()}_${file.name}`);
            await uploadBytes(sRefObj, file);
            const url = await getDownloadURL(sRefObj);
            push(ref(db, 'messages/' + currentRoom), {
                sender: myName, fileUrl: url, fileName: file.name,
                type: file.type.includes('image') ? 'image' : 'file', time: serverTimestamp()
            });
            btn.innerHTML = '<i class="fa-solid fa-paper-plane text-xs"></i>';
        };

        document.getElementById('dark-toggle').onclick = () => document.body.classList.toggle('dark-mode');
    </script>
</body>
</html>
