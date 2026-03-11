<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Prime Solutions | Business Hub</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
</head>
<body class="bg-gray-100 h-screen flex overflow-hidden">

    <div id="login-overlay" class="fixed inset-0 bg-indigo-950 z-50 flex items-center justify-center">
        <div class="bg-white p-8 rounded-2xl shadow-2xl w-80 text-center">
            <h1 class="text-2xl font-bold text-gray-800 mb-2">Prime Hub</h1>
            <input type="text" id="username-input" placeholder="Apna Naam Likhein..." 
                   class="w-full border-2 border-gray-200 p-3 rounded-lg mb-4 text-center outline-none focus:border-indigo-600">
            <button id="join-btn" class="w-full bg-indigo-600 text-white p-3 rounded-lg font-bold hover:bg-indigo-700 transition">Enter Workspace</button>
        </div>
    </div>

    <aside class="w-64 bg-indigo-900 text-white flex flex-col hidden md:flex">
        <div class="p-4 text-xl font-bold border-b border-indigo-800 flex justify-between items-center">
            <span>Workspace</span>
            <i class="fas fa-circle-dot text-green-400 text-xs"></i>
        </div>
        
        <div class="flex-1 overflow-y-auto p-4 space-y-6">
            <div>
                <h3 class="text-indigo-300 text-xs font-bold uppercase mb-3">Channels</h3>
                <ul id="channel-list" class="space-y-1">
                    <li onclick="switchChannel('general')" class="bg-indigo-700 p-2 rounded cursor-pointer font-medium"># general</li>
                    <li onclick="switchChannel('marketing')" class="p-2 hover:bg-indigo-800 rounded cursor-pointer text-indigo-100"># marketing</li>
                </ul>
            </div>

            <div>
                <h3 class="text-indigo-300 text-xs font-bold uppercase mb-3">Active Users</h3>
                <ul id="online-users-list" class="space-y-2 text-sm">
                    </ul>
            </div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col bg-white">
        <header class="h-16 border-b flex items-center justify-between px-6 shadow-sm">
            <h2 id="current-channel-name" class="font-bold text-gray-800 text-lg"># general</h2>
            <div id="user-display" class="text-sm font-semibold text-indigo-600 bg-indigo-50 px-3 py-1 rounded-full"></div>
        </header>

        <div id="chat-messages" class="flex-1 overflow-y-auto p-6 space-y-4 bg-gray-50"></div>

        <footer class="p-4 border-t bg-white">
            <div class="flex items-center gap-3 bg-gray-100 rounded-xl px-4 py-2 border focus-within:border-indigo-500">
                <label class="cursor-pointer text-gray-500 hover:text-indigo-600">
                    <i class="fas fa-paperclip"></i>
                    <input type="file" id="file-input" class="hidden">
                </label>
                <input type="text" id="msg-input" placeholder="Message..." class="bg-transparent flex-1 outline-none py-2">
                <button id="send-btn" class="text-indigo-600 text-xl hover:scale-110 transition"><i class="fas fa-paper-plane"></i></button>
            </div>
        </footer>
    </main>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getAuth, signInAnonymously, updateProfile, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
        import { getDatabase, ref, push, onChildAdded, onValue, serverTimestamp, onDisconnect, set } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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

        let currentChannel = 'general';
        let currentUserData = null;

        // Login System
        document.getElementById('join-btn').onclick = async () => {
            const name = document.getElementById('username-input').value.trim();
            if(!name) return;
            const cred = await signInAnonymously(auth);
            await updateProfile(cred.user, { displayName: name });
            location.reload(); 
        };

        onAuthStateChanged(auth, (user) => {
            if (user && user.displayName) {
                currentUserData = user;
                document.getElementById('login-overlay').classList.add('hidden');
                document.getElementById('user-display').innerText = user.displayName;
                
                // Presence
                const statusRef = ref(db, 'online_users/' + user.uid);
                set(statusRef, { name: user.displayName });
                onDisconnect(statusRef).remove();
                
                loadMessages();
            }
        });

        // Online List
        onValue(ref(db, 'online_users'), (snap) => {
            const list = document.getElementById('online-users-list');
            list.innerHTML = "";
            snap.forEach(child => {
                list.innerHTML += `<li class="flex items-center gap-2"><span class="w-2 h-2 bg-green-500 rounded-full"></span>${child.val().name}</li>`;
            });
        });

        // Messaging
        function loadMessages() {
            const chatBox = document.getElementById('chat-messages');
            chatBox.innerHTML = "";
            onChildAdded(ref(db, 'messages/' + currentChannel), (snap) => {
                const data = snap.val();
                const isMe = data.name === auth.currentUser.displayName;
                chatBox.innerHTML += `
                    <div class="flex flex-col ${isMe ? 'items-end' : 'items-start'}">
                        <span class="text-[10px] text-gray-400 mb-1 px-2">${data.name}</span>
                        <div class="${isMe ? 'bg-indigo-600 text-white rounded-l-lg rounded-tr-lg' : 'bg-white text-gray-800 rounded-r-lg rounded-tl-lg'} p-3 shadow-sm border border-gray-100 max-w-[70%]">
                            ${data.file ? `<a href="${data.file}" target="_blank" class="underline text-xs block mb-1"><i class="fas fa-file"></i> View Attachment</a>` : ''}
                            ${data.text}
                        </div>
                    </div>`;
                chatBox.scrollTop = chatBox.scrollHeight;
            });
        }

        window.switchChannel = (name) => {
            currentChannel = name;
            document.getElementById('current-channel-name').innerText = "# " + name;
            loadMessages();
        };

        document.getElementById('send-btn').onclick = sendMessage;
        document.getElementById('msg-input').onkeypress = (e) => { if(e.key === 'Enter') sendMessage(); };

        function sendMessage() {
            const input = document.getElementById('msg-input');
            if(input.value.trim()) {
                push(ref(db, 'messages/' + currentChannel), {
                    name: auth.currentUser.displayName,
                    text: input.value,
                    timestamp: serverTimestamp()
                });
                input.value = "";
            }
        }
    </script>
</body>
</html>
