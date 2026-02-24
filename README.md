<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro - Private & Global</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .hidden { display: none; }
        .active-tab { border-bottom: 3px solid white; }
        #chat-window { height: calc(100vh - 250px); overflow-y: auto; }
    </style>
</head>
<body class="bg-gray-100 h-screen flex flex-col">

    <div id="auth-section" class="flex-1 flex items-center justify-center p-4">
        <div class="bg-white p-8 rounded-2xl shadow-2xl w-full max-w-md">
            <h2 class="text-2xl font-bold text-center text-blue-600 mb-6">Welcome to ChatPro</h2>
            <input id="auth-email" type="email" placeholder="Email" class="w-full border p-3 rounded-lg mb-4 outline-none">
            <input id="auth-pass" type="password" placeholder="Password" class="w-full border p-3 rounded-lg mb-6 outline-none">
            <div class="flex gap-2">
                <button id="btn-login" class="flex-1 bg-blue-600 text-white py-3 rounded-lg font-bold">Login</button>
                <button id="btn-signup" class="flex-1 border-2 border-blue-600 text-blue-600 py-3 rounded-lg font-bold">Signup</button>
            </div>
        </div>
    </div>

    <div id="app-section" class="hidden flex-1 flex flex-col">
        <nav class="bg-blue-600 text-white p-4 shadow-md">
            <div class="max-w-6xl mx-auto flex justify-between items-center">
                <h1 class="font-bold text-xl">ChatPro Dashboard</h1>
                <div class="flex gap-4 items-center">
                    <span id="user-display" class="text-sm font-medium"></span>
                    <button id="btn-logout" class="bg-red-500 px-3 py-1 rounded text-sm">Logout</button>
                </div>
            </div>
        </nav>

        <div class="flex flex-1 overflow-hidden">
            <div class="w-1/4 bg-white border-r overflow-y-auto hidden md:block">
                <div class="p-4 border-b font-bold bg-gray-50">Online Users</div>
                <div id="global-room-btn" class="p-4 cursor-pointer hover:bg-blue-50 bg-blue-100 font-bold border-b">🌎 Global Chat</div>
                <div id="users-list" class="divide-y">
                    </div>
            </div>

            <div class="flex-1 flex flex-col bg-gray-50">
                <div id="chat-header" class="p-4 bg-white border-b font-bold text-blue-700">🌎 Global Chat</div>
                <div id="chat-window" class="p-4 space-y-4"></div>
                
                <form id="chat-form" class="p-4 bg-white border-t flex gap-2">
                    <input id="msg-input" type="text" placeholder="Type your message..." class="flex-1 border p-2 rounded-full px-4 outline-none">
                    <button class="bg-blue-600 text-white p-2 rounded-full w-10 h-10">➤</button>
                </form>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
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
        const auth = getAuth(app);
        const db = getDatabase(app);

        let currentUser = null;
        let currentChatID = "global"; // Default room

        // --- AUTH LOGIC ---
        document.getElementById('btn-signup').onclick = () => {
            const email = document.getElementById('auth-email').value;
            const pass = document.getElementById('auth-pass').value;
            createUserWithEmailAndPassword(auth, email, pass).then(res => {
                set(ref(db, 'users/' + res.user.uid), { email: email, status: 'online' });
            }).catch(err => alert(err.message));
        };

        document.getElementById('btn-login').onclick = () => {
            const email = document.getElementById('auth-email').value;
            const pass = document.getElementById('auth-pass').value;
            signInWithEmailAndPassword(auth, email, pass).catch(err => alert(err.message));
        };

        document.getElementById('btn-logout').onclick = () => signOut(auth);

        onAuthStateChanged(auth, (user) => {
            if (user) {
                currentUser = user;
                document.getElementById('auth-section').classList.add('hidden');
                document.getElementById('app-section').classList.remove('hidden');
                document.getElementById('user-display').innerText = user.email;
                loadUsers();
                loadMessages("global");
            } else {
                document.getElementById('auth-section').classList.remove('hidden');
                document.getElementById('app-section').classList.add('hidden');
            }
        });

        // --- USER LIST ---
        function loadUsers() {
            onValue(ref(db, 'users'), (snap) => {
                const list = document.getElementById('users-list');
                list.innerHTML = "";
                snap.forEach(child => {
                    if (child.key !== currentUser.uid) {
                        const div = document.createElement('div');
                        div.className = "p-4 cursor-pointer hover:bg-gray-100 border-b transition";
                        div.innerText = child.val().email.split('@')[0];
                        div.onclick = () => startPrivateChat(child.key, child.val().email);
                        list.appendChild(div);
                    }
                });
            });
        }

        // --- CHAT LOGIC ---
        function startPrivateChat(targetUID, targetEmail) {
            currentChatID = currentUser.uid < targetUID ? currentUser.uid + "_" + targetUID : targetUID + "_" + currentUser.uid;
            document.getElementById('chat-header').innerText = "Chat with " + targetEmail.split('@')[0];
            loadMessages(currentChatID);
        }

        document.getElementById('global-room-btn').onclick = () => {
            currentChatID = "global";
            document.getElementById('chat-header').innerText = "🌎 Global Chat";
            loadMessages("global");
        };

        function loadMessages(id) {
            const win = document.getElementById('chat-window');
            win.innerHTML = "";
            const chatRef = ref(db, 'chats/' + id);
            onChildAdded(chatRef, (snap) => {
                const data = snap.val();
                const div = document.createElement('div');
                const isMine = data.sender === currentUser.email;
                div.className = `flex flex-col \${isMine ? 'items-end' : 'items-start'}`;
                div.innerHTML = \`
                    <div class="\${isMine ? 'bg-blue-600 text-white' : 'bg-white'} px-4 py-2 rounded-xl shadow-sm max-w-xs">
                        <p class="text-[10px] opacity-70">\${data.sender.split('@')[0]}</p>
                        <p>\${data.text}</p>
                    </div>\`;
                win.appendChild(div);
                win.scrollTop = win.scrollHeight;
            });
        }

        document.getElementById('chat-form').onsubmit = (e) => {
            e.preventDefault();
            const input = document.getElementById('msg-input');
            if (input.value.trim()) {
                push(ref(db, 'chats/' + currentChatID), {
                    sender: currentUser.email,
                    text: input.value,
                    time: Date.now()
                });
                input.value = "";
            }
        };
    </script>
</body>
</html>
