<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro | Advanced Realtime Hub</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #f8fafc; }
        .glass { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.3); }
        .sidebar-item.active { background: #3b82f6; color: white; box-shadow: 0 4px 12px rgba(59, 130, 246, 0.3); }
        #chat-flow { height: calc(100vh - 200px); scrollbar-width: thin; }
        .hidden { display: none; }
    </style>
</head>
<body class="h-screen overflow-hidden flex flex-col">

    <div id="login-screen" class="fixed inset-0 z-[100] bg-blue-600 flex items-center justify-center p-4">
        <div class="bg-white p-8 rounded-3xl shadow-2xl w-full max-w-md text-center">
            <div class="w-20 h-20 bg-blue-100 text-blue-600 rounded-full flex items-center justify-center mx-auto mb-6 text-3xl">
                <i class="fa-solid fa-rocket"></i>
            </div>
            <h2 class="text-3xl font-bold text-gray-800 mb-2">Welcome!</h2>
            <p class="text-gray-500 mb-8">Please enter a username to start chatting</p>
            <input id="username-input" type="text" placeholder="Username..." class="w-full border-2 border-gray-100 rounded-2xl p-4 mb-4 outline-none focus:border-blue-500 transition-all text-center text-lg font-semibold">
            <button id="join-btn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-2xl shadow-lg transition-all transform hover:scale-[1.02]">
                Join Community <i class="fa-solid fa-arrow-right ml-2"></i>
            </button>
        </div>
    </div>

    <div id="main-app" class="hidden flex flex-1 flex-col md:flex-row h-full">
        
        <aside class="w-full md:w-80 bg-white border-r flex flex-col">
            <div class="p-6 border-b flex justify-between items-center bg-blue-50">
                <h1 class="text-xl font-bold text-blue-600"><i class="fa-solid fa-bolt mr-2"></i>ChatPro</h1>
                <button onclick="location.reload()" class="text-gray-400 hover:text-red-500"><i class="fa-solid fa-power-off"></i></button>
            </div>
            
            <div class="p-4 flex-1 overflow-y-auto">
                <div id="global-tab" class="sidebar-item active p-4 rounded-2xl mb-4 cursor-pointer flex items-center gap-3 transition-all">
                    <i class="fa-solid fa-earth-americas"></i>
                    <span class="font-bold">Global Chat</span>
                </div>
                
                <h3 class="text-xs font-bold text-gray-400 uppercase tracking-widest mb-4 mt-6 ml-2">Private Messages</h3>
                <div id="users-list" class="space-y-2">
                    </div>
            </div>

            <div class="p-4 bg-gray-50 border-t flex items-center gap-3">
                <div class="w-10 h-10 bg-blue-500 rounded-full flex items-center justify-center text-white font-bold" id="my-avatar">U</div>
                <div>
                    <p class="text-sm font-bold text-gray-800" id="my-name">Username</p>
                    <p class="text-[10px] text-green-500 font-bold"><i class="fa-solid fa-circle text-[8px]"></i> Online</p>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col relative bg-slate-50">
            <header class="p-4 bg-white shadow-sm flex items-center justify-between border-b z-10">
                <div class="flex items-center gap-3">
                    <div id="active-chat-icon" class="w-10 h-10 bg-blue-100 text-blue-600 rounded-full flex items-center justify-center">
                        <i class="fa-solid fa-hashtag"></i>
                    </div>
                    <div>
                        <h2 id="active-chat-name" class="font-bold text-gray-800">Global Chat</h2>
                        <p class="text-xs text-gray-400">Public channel for everyone</p>
                    </div>
                </div>
                <div class="flex gap-4 text-gray-400">
                    <i class="fa-solid fa-phone cursor-pointer hover:text-blue-500"></i>
                    <i class="fa-solid fa-video cursor-pointer hover:text-blue-500"></i>
                    <i class="fa-solid fa-ellipsis-vertical cursor-pointer hover:text-blue-500"></i>
                </div>
            </header>

            <div id="chat-flow" class="flex-1 p-6 space-y-4 overflow-y-auto">
                </div>

            <div class="p-4 bg-white border-t">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-3 bg-gray-100 p-2 rounded-2xl border border-gray-200">
                    <button type="button" class="text-gray-400 hover:text-blue-500 ml-2"><i class="fa-solid fa-circle-plus text-xl"></i></button>
                    <input id="msg-input" type="text" placeholder="Type a message..." class="flex-1 bg-transparent p-2 outline-none text-gray-700">
                    <button class="bg-blue-600 text-white w-10 h-10 rounded-xl flex items-center justify-center hover:bg-blue-700 shadow-md transition-all">
                        <i class="fa-solid fa-paper-plane"></i>
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
        let myUsername = "";
        let currentRoom = "global";

        // --- LOGIN LOGIC ---
        document.getElementById('join-btn').onclick = () => {
            const userField = document.getElementById('username-input').value.trim();
            if(userField.length > 2) {
                myUsername = userField;
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('main-app').classList.remove('hidden');
                document.getElementById('my-name').innerText = myUsername;
                document.getElementById('my-avatar').innerText = myUsername[0].toUpperCase();
                
                // Add me to online users
                set(ref(db, 'online_users/' + myUsername), { name: myUsername, lastSeen: serverTimestamp() });
                initApp();
            } else {
                alert("Username must be at least 3 characters");
            }
        };

        function initApp() {
            // Load Users
            onValue(ref(db, 'online_users'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(child => {
                    if(child.key !== myUsername) {
                        const userDiv = document.createElement('div');
                        userDiv.className = "sidebar-item p-4 rounded-2xl cursor-pointer flex items-center gap-3 text-gray-600 hover:bg-gray-100 transition-all";
                        userDiv.innerHTML = `<div class="w-8 h-8 bg-gray-200 rounded-full flex items-center justify-center text-xs font-bold">${child.key[0].toUpperCase()}</div><span>${child.key}</span>`;
                        userDiv.onclick = () => switchRoom(child.key);
                        list.appendChild(userDiv);
                    }
                });
            });

            loadMessages("global");
        }

        // --- CHAT LOGIC ---
        function switchRoom(targetUser) {
            currentRoom = myUsername < targetUser ? myUsername + "_" + targetUser : targetUser + "_" + myUsername;
            document.getElementById('active-chat-name').innerText = targetUser;
            document.getElementById('active-chat-icon').innerHTML = '<i class="fa-solid fa-user"></i>';
            document.getElementById('global-tab').classList.remove('active');
            loadMessages(currentRoom);
        }

        document.getElementById('global-tab').onclick = () => {
            currentRoom = "global";
            document.getElementById('active-chat-name').innerText = "Global Chat";
            document.getElementById('active-chat-icon').innerHTML = '<i class="fa-solid fa-hashtag"></i>';
            document.getElementById('global-tab').classList.add('active');
            loadMessages("global");
        };

        function loadMessages(roomId) {
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = "";
            onChildAdded(ref(db, 'messages/' + roomId), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myUsername;
                const msgDiv = document.createElement('div');
                msgDiv.className = `flex ${isMine ? 'justify-end' : 'justify-start'}`;
                msgDiv.innerHTML = `
                    <div class="max-w-[80%] ${isMine ? 'bg-blue-600 text-white rounded-t-2xl rounded-bl-2xl shadow-blue-200' : 'bg-white text-gray-800 rounded-t-2xl rounded-br-2xl shadow-sm'} p-4 shadow-lg">
                        <p class="text-[10px] font-bold opacity-60 mb-1">${isMine ? 'You' : data.sender}</p>
                        <p class="text-sm">${data.text}</p>
                    </div>
                `;
                flow.appendChild(msgDiv);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        document.getElementById('msg-form').onsubmit = (e) => {
            e.preventDefault();
            const inp = document.getElementById('msg-input');
            if(inp.value.trim()) {
                push(ref(db, 'messages/' + currentRoom), {
                    sender: myUsername,
                    text: inp.value,
                    time: serverTimestamp()
                });
                inp.value = "";
            }
        };
    </script>
</body>
</html>
