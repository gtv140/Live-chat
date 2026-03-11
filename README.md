<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Prime Hub | Live 🟢</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #f8fafc; }
        .dark-mode { background: #0f172a; color: white; }
        .chat-container::-webkit-scrollbar { width: 5px; }
        .chat-container::-webkit-scrollbar-thumb { background: #6366f1; border-radius: 10px; }
    </style>
</head>
<body id="app-body" class="h-screen flex overflow-hidden">

    <div id="login-overlay" class="fixed inset-0 bg-slate-900 z-[100] flex items-center justify-center p-4">
        <div class="bg-white rounded-[2.5rem] p-10 w-full max-w-md text-center">
            <h1 class="text-3xl font-black text-slate-800 mb-6 uppercase tracking-tighter">Prime Hub</h1>
            <input type="text" id="username-input" placeholder="Your Name..." class="w-full bg-slate-50 border-2 p-4 rounded-2xl mb-4 text-center font-bold outline-none focus:border-indigo-600">
            <button id="join-btn" class="w-full bg-indigo-600 text-white p-4 rounded-2xl font-black shadow-xl">JOIN WORKSPACE</button>
        </div>
    </div>

    <aside class="w-72 bg-slate-900 text-white flex flex-col hidden md:flex">
        <div class="p-8 border-b border-white/5 text-2xl font-black tracking-tighter text-indigo-400">PRIME.</div>
        <div class="flex-1 overflow-y-auto p-6 space-y-8">
            <div>
                <p class="text-[10px] font-bold text-slate-500 uppercase mb-4">Channels</p>
                <div id="chan-general" class="p-3 bg-indigo-600 rounded-xl font-bold cursor-pointer"># general</div>
            </div>
            <div>
                <p class="text-[10px] font-bold text-slate-500 uppercase mb-4">Active Members (<span id="user-count">0</span>)</p>
                <ul id="online-users" class="space-y-3"></ul>
            </div>
        </div>
        <div class="p-6 bg-black/20 border-t border-white/5 flex items-center gap-3">
            <div id="my-avatar" class="w-10 h-10 bg-indigo-500 rounded-lg flex items-center justify-center font-bold">?</div>
            <div class="text-sm font-bold truncate" id="my-display-name">Guest</div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col">
        <header class="h-20 border-b flex items-center justify-between px-8 bg-white">
            <h2 class="font-black text-xl text-slate-800 tracking-tight uppercase"># general</h2>
            <div id="typing-status" class="text-[10px] font-bold text-indigo-500 uppercase"></div>
        </header>
        <div id="chat-box" class="flex-1 overflow-y-auto p-8 space-y-4 chat-container"></div>
        <footer class="p-6 bg-white border-t">
            <div class="max-w-4xl mx-auto flex gap-4 bg-slate-100 p-2 rounded-[2rem] border">
                <input type="text" id="msg-input" placeholder="Type message..." class="flex-1 bg-transparent outline-none px-4 font-bold text-slate-700">
                <button id="send-btn" class="w-12 h-12 bg-indigo-600 text-white rounded-full shadow-lg flex items-center justify-center"><i class="fas fa-paper-plane"></i></button>
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

        // LOGIN
        document.getElementById('join-btn').onclick = async () => {
            const name = document.getElementById('username-input').value.trim();
            if(!name) return;
            const cred = await signInAnonymously(auth);
            await updateProfile(cred.user, { displayName: name });
            location.reload();
        };

        onAuthStateChanged(auth, (user) => {
            if (user && user.displayName) {
                document.getElementById('login-overlay').classList.add('hidden');
                document.getElementById('my-display-name').innerText = user.displayName;
                document.getElementById('my-avatar').innerText = user.displayName[0].toUpperCase();

                // PRESENCE
                const pRef = ref(db, 'online_users/' + user.uid);
                set(pRef, { name: user.displayName });
                onDisconnect(pRef).remove();
                
                loadMessages();
                listenUsers();
            }
        });

        // ACTIVE USERS LIST
        function listenUsers() {
            onValue(ref(db, 'online_users'), (snap) => {
                const list = document.getElementById('online-users');
                list.innerHTML = "";
                let count = 0;
                snap.forEach(c => {
                    count++;
                    list.innerHTML += `<li class="flex items-center gap-3 p-2 bg-white/5 rounded-lg"><div class="w-8 h-8 bg-indigo-500 flex items-center justify-center rounded text-xs">${c.val().name[0]}</div><span class="text-sm">${c.val().name}</span></li>`;
                });
                document.getElementById('user-count').innerText = count;
            });
        }

        // MESSAGES
        function loadMessages() {
            const chatBox = document.getElementById('chat-box');
            onChildAdded(ref(db, 'messages/general'), (snap) => {
                const d = snap.val();
                const isMe = d.name === auth.currentUser.displayName;
                chatBox.innerHTML += `<div class="flex flex-col ${isMe ? 'items-end' : 'items-start'}"><span class="text-[9px] text-slate-400 mb-1 font-bold uppercase">${d.name}</span><div class="${isMe ? 'bg-indigo-600 text-white rounded-l-xl rounded-tr-xl shadow-indigo-200' : 'bg-white text-slate-700 rounded-r-xl rounded-tl-xl border'} p-3 shadow-md text-sm font-semibold">${d.text}</div></div>`;
                chatBox.scrollTop = chatBox.scrollHeight;
            });
        }

        // SEND
        const sendMsg = () => {
            const input = document.getElementById('msg-input');
            if(input.value.trim()) {
                push(ref(db, 'messages/general'), {
                    name: auth.currentUser.displayName,
                    text: input.value,
                    time: serverTimestamp()
                }).catch(e => alert("Error: " + e.message));
                input.value = "";
            }
        };

        document.getElementById('send-btn').onclick = sendMsg;
        document.getElementById('msg-input').onkeypress = (e) => { if(e.key === 'Enter') sendMsg(); };
    </script>
</body>
</html>
