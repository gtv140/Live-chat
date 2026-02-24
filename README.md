<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro Ultra | The Complete Business OS</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.3s; }
        .glass { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.3); }
        .dark-mode { background-color: #0f172a; color: #f1f5f9; }
        .dark-mode .glass { background: #1e293b; border-color: #334155; }
        #chat-flow { height: calc(100vh - 220px); scroll-behavior: smooth; }
        .msg-mine { background: #2563eb; color: white; border-radius: 20px 20px 0 20px; }
        .msg-theirs { background: white; color: #1e293b; border-radius: 20px 20px 20px 0; border: 1px solid #e2e8f0; }
        .active-channel { background: #eff6ff; color: #2563eb; border-left: 4px solid #2563eb; }
        .hidden { display: none; }
        .msg-options { display: none; position: absolute; top: -30px; right: 0; background: white; border-radius: 10px; box-shadow: 0 4px 10px rgba(0,0,0,0.1); padding: 5px; gap: 10px; z-index: 50; }
        .relative:hover .msg-options { display: flex; }
    </style>
</head>
<body class="h-screen flex flex-col overflow-hidden bg-slate-50">

    <div id="login-screen" class="fixed inset-0 z-[100] bg-blue-700 flex items-center justify-center p-6">
        <div class="bg-white p-10 rounded-[3rem] shadow-2xl w-full max-w-md text-center">
            <div class="w-20 h-20 bg-blue-100 text-blue-600 rounded-3xl flex items-center justify-center mx-auto mb-6 shadow-inner rotate-3">
                <i class="fa-solid fa-shield-cat text-4xl"></i>
            </div>
            <h2 class="text-3xl font-bold text-slate-900 mb-2">ChatPro <span class="text-blue-600">Ultra</span></h2>
            <p class="text-slate-500 mb-8 font-medium">Enterprise Communication, Simplified.</p>
            <input id="username-input" type="text" placeholder="Professional Alias..." class="w-full bg-slate-50 border-2 border-slate-100 rounded-2xl p-4 mb-4 outline-none focus:border-blue-500 text-center font-bold">
            <button id="join-btn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-2xl shadow-xl transition-all">Launch Enterprise</button>
        </div>
    </div>

    <div id="main-app" class="hidden flex h-full">
        <aside class="w-20 md:w-72 glass border-r flex flex-col z-20">
            <div class="p-6 border-b flex justify-between items-center">
                <span class="font-bold text-xl hidden md:block">Workspace</span>
                <button onclick="logout()" class="text-slate-400 hover:text-red-500"><i class="fa-solid fa-right-from-bracket"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto p-4 space-y-8">
                <div>
                    <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-4 ml-2">Channels</p>
                    <div id="channels-list" class="space-y-1 text-sm font-semibold">
                        <div onclick="switchChannel('general')" id="chan-general" class="p-3 rounded-xl cursor-pointer active-channel flex items-center gap-3 transition">
                            <i class="fa-solid fa-hashtag"></i> <span>general-hq</span>
                        </div>
                        <div onclick="switchChannel('marketing')" id="chan-marketing" class="p-3 rounded-xl cursor-pointer flex items-center gap-3 transition hover:bg-slate-100">
                            <i class="fa-solid fa-bullhorn"></i> <span>marketing</span>
                        </div>
                        <div onclick="switchChannel('development')" id="chan-development" class="p-3 rounded-xl cursor-pointer flex items-center gap-3 transition hover:bg-slate-100">
                            <i class="fa-solid fa-code"></i> <span>dev-team</span>
                        </div>
                    </div>
                </div>

                <div>
                    <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-4 ml-2">Direct Messages</p>
                    <div id="users-list" class="space-y-1"></div>
                </div>
            </div>

            <div class="p-4 border-t bg-slate-50/80 flex items-center gap-3">
                <div class="w-11 h-11 rounded-2xl bg-gradient-to-tr from-blue-600 to-indigo-600 text-white flex items-center justify-center font-bold shadow-lg shadow-blue-200" id="my-avatar">?</div>
                <div class="hidden md:block">
                    <p class="text-xs font-bold" id="my-name">Loading...</p>
                    <p class="text-[10px] text-green-500 font-bold">● ONLINE</p>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col bg-white">
            <header class="p-5 glass border-b flex items-center justify-between shadow-sm sticky top-0 z-10">
                <div class="flex items-center gap-4">
                    <div id="active-icon" class="w-12 h-12 bg-blue-50 text-blue-600 rounded-2xl flex items-center justify-center shadow-inner"><i class="fa-solid fa-hashtag text-xl"></i></div>
                    <div>
                        <h4 id="chat-title" class="font-bold text-slate-800 text-lg">general-hq</h4>
                        <p id="typing-status" class="text-[10px] text-blue-500 font-bold h-3 italic"></p>
                    </div>
                </div>
                <div class="flex gap-4">
                    <i class="fa-solid fa-magnifying-glass text-slate-400 cursor-pointer hover:text-blue-500 transition"></i>
                    <i class="fa-solid fa-ellipsis-vertical text-slate-400 cursor-pointer hover:text-blue-500 transition"></i>
                </div>
            </header>

            <div id="chat-flow" class="flex-1 p-6 space-y-6 overflow-y-auto bg-[url('https://www.transparenttextures.com/patterns/cubes.png')] bg-opacity-10"></div>

            <div class="p-6 bg-gradient-to-t from-slate-50 to-transparent">
                <form id="msg-form" class="max-w-5xl mx-auto glass p-3 rounded-[2.5rem] flex items-center gap-3 shadow-2xl border-2 border-white">
                    <input type="file" id="file-input" class="hidden">
                    <button type="button" onclick="document.getElementById('file-input').click()" class="w-11 h-11 text-slate-400 hover:text-blue-600 transition"><i class="fa-solid fa-plus-circle text-2xl"></i></button>
                    
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Share your message..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium text-slate-700">
                    
                    <button type="button" onclick="alert('Tic-Tac-Toe coming soon!')" class="text-slate-400 hover:text-orange-500"><i class="fa-solid fa-gamepad text-xl"></i></button>
                    
                    <button id="send-btn" class="bg-blue-600 text-white w-12 h-12 rounded-2xl flex items-center justify-center hover:bg-blue-700 shadow-lg shadow-blue-200 transition-all active:scale-90">
                        <i class="fa-solid fa-paper-plane"></i>
                    </button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, serverTimestamp, remove, update, onChildChanged, onChildRemoved } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let currentRoom = "general";

        // PERSISTENCE LOGIC
        window.onload = () => {
            const saved = localStorage.getItem('chatProUser');
            if (saved) initApp(saved);
        };

        document.getElementById('join-btn').onclick = () => {
            const name = document.getElementById('username-input').value.trim();
            if (name.length > 2) {
                localStorage.setItem('chatProUser', name);
                initApp(name);
            }
        };

        function initApp(name) {
            myName = name;
            document.getElementById('login-screen').classList.add('hidden');
            document.getElementById('main-app').classList.remove('hidden');
            document.getElementById('my-name').innerText = myName;
            document.getElementById('my-avatar').innerText = myName[0].toUpperCase();
            set(ref(db, 'online/' + myName), { status: 'online' });
            startCore();
        }

        function startCore() {
            // Typing
            document.getElementById('msg-input').oninput = () => {
                set(ref(db, `typing/${currentRoom}/${myName}`), true);
                setTimeout(() => set(ref(db, `typing/${currentRoom}/${myName}`), false), 2000);
            };

            onValue(ref(db, `typing/${currentRoom}`), (snap) => {
                let typers = [];
                snap.forEach(c => { if(c.val() === true && c.key !== myName) typers.push(c.key) });
                document.getElementById('typing-status').innerText = typers.length ? typers.join(', ') + ' is typing...' : '';
            });

            // Online Users
            onValue(ref(db, 'online'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(c => {
                    if(c.key !== myName) {
                        const div = document.createElement('div');
                        div.className = "p-3 rounded-xl cursor-pointer hover:bg-slate-100 flex items-center gap-3 transition";
                        div.innerHTML = `<div class="w-8 h-8 rounded-lg bg-blue-50 text-blue-600 flex items-center justify-center font-bold text-xs">${c.key[0]}</div><span class="text-xs font-semibold">${c.key}</span>`;
                        div.onclick = () => switchChannel(c.key, true);
                        list.appendChild(div);
                    }
                });
            });

            loadMessages(currentRoom);
        }

        // NAVIGATION
        window.switchChannel = (target, isPrivate = false) => {
            if(!isPrivate) {
                currentRoom = target;
                document.getElementById('chat-title').innerText = target + "-hq";
                document.querySelectorAll('#channels-list div').forEach(el => el.classList.remove('active-channel'));
                document.getElementById('chan-' + target).classList.add('active-channel');
            } else {
                currentRoom = myName < target ? myName + "_" + target : target + "_" + myName;
                document.getElementById('chat-title').innerText = target;
            }
            loadMessages(currentRoom);
        };

        function loadMessages(room) {
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            const msgRef = ref(db, 'msgs/' + room);
            
            onChildAdded(msgRef, (snap) => renderMsg(snap.key, snap.val()));
            onChildRemoved(msgRef, (snap) => document.getElementById('msg-' + snap.key)?.remove());
            onChildChanged(msgRef, (snap) => {
                const el = document.getElementById('msg-' + snap.key);
                if(el && snap.val().text) el.querySelector('.msg-text').innerText = snap.val().text + " (edited)";
            });
        }

        function renderMsg(id, data) {
            const isMine = data.sender === myName;
            const div = document.createElement('div');
            div.id = 'msg-' + id;
            div.className = `flex ${isMine ? 'justify-end' : 'justify-start'} mb-4 relative group`;
            
            let body = data.fileUrl ? 
                (data.type === 'image' ? `<img src="${data.fileUrl}" class="rounded-xl max-w-full">` : `<a href="${data.fileUrl}" target="_blank" class="flex items-center gap-3 p-2 bg-slate-50 rounded-lg text-xs font-bold"><i class="fa-solid fa-file-pdf text-xl"></i> ${data.fileName}</a>`) : 
                `<span class="msg-text">${data.text}</span>`;

            div.innerHTML = `
                <div class="p-4 max-w-[75%] shadow-sm ${isMine ? 'msg-mine' : 'msg-theirs'} relative">
                    <p class="text-[8px] font-black uppercase tracking-widest opacity-40 mb-1">${isMine ? 'YOU' : data.sender}</p>
                    ${body}
                    <p class="text-[7px] text-right mt-1 opacity-40">${new Date(data.time).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</p>
                    
                    ${isMine ? `
                    <div class="msg-options flex gap-2 p-1 border">
                        <i class="fa-solid fa-pen text-blue-500 cursor-pointer" onclick="editMsg('${id}', '${data.text}')"></i>
                        <i class="fa-solid fa-trash text-red-500 cursor-pointer" onclick="deleteMsg('${id}')"></i>
                    </div>` : ''}
                </div>`;
            document.getElementById('chat-flow').appendChild(div);
            document.getElementById('chat-flow').scrollTop = document.getElementById('chat-flow').scrollHeight;
        }

        // ADVANCED ACTIONS
        window.deleteMsg = (id) => { if(confirm("Delete for everyone?")) remove(ref(db, `msgs/${currentRoom}/${id}`)); };
        window.editMsg = (id, oldText) => {
            const newText = prompt("Edit your message:", oldText);
            if(newText) update(ref(db, `msgs/${currentRoom}/${id}`), { text: newText });
        };

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
            const sRefObj = sRef(storage, `biz/${Date.now()}_${file.name}`);
            await uploadBytes(sRefObj, file);
            const url = await getDownloadURL(sRefObj);
            push(ref(db, 'msgs/' + currentRoom), { sender: myName, fileUrl: url, fileName: file.name, type: file.type.includes('image') ? 'image' : 'file', time: Date.now() });
        };

        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };
    </script>
</body>
</html>
