<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Prime Hub | Live Connect 🟢</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        .glass-sidebar { background: #0f172a; }
        .chat-area { background: #f1f5f9; }
        .chat-scroll::-webkit-scrollbar { width: 5px; }
        .chat-scroll::-webkit-scrollbar-thumb { background: #6366f1; border-radius: 10px; }
    </style>
</head>
<body class="h-screen flex overflow-hidden">

    <div id="login-overlay" class="fixed inset-0 bg-slate-900 z-[100] flex items-center justify-center p-4">
        <div class="bg-white rounded-[2rem] p-10 w-full max-w-md text-center shadow-2xl">
            <h1 class="text-3xl font-black mb-6">Prime Hub</h1>
            <input type="text" id="username-input" placeholder="Enter Name..." class="w-full border-2 p-4 rounded-xl mb-4 text-center font-bold">
            <button id="join-btn" class="w-full bg-indigo-600 text-white p-4 rounded-xl font-bold">JOIN LIVE</button>
        </div>
    </div>

    <aside class="w-72 glass-sidebar text-white flex flex-col hidden md:flex">
        <div class="p-8 text-2xl font-black text-indigo-400 border-b border-white/5">PRIME.</div>
        <div class="flex-1 p-6 space-y-6">
            <div>
                <p class="text-[10px] font-bold text-slate-500 uppercase mb-4 tracking-widest">Active Members — <span id="user-count">0</span></p>
                <ul id="online-users" class="space-y-2"></ul>
            </div>
        </div>
        <div class="p-6 bg-black/20 border-t border-white/5 flex items-center gap-3">
            <div id="my-avatar" class="w-10 h-10 bg-indigo-600 rounded-lg flex items-center justify-center font-bold">?</div>
            <div id="my-name" class="text-sm font-bold truncate">Guest</div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col chat-area">
        <header class="h-20 border-b bg-white flex items-center px-8 justify-between">
            <h2 class="font-black text-slate-800"># GENERAL</h2>
            <div id="typing-status" class="text-[10px] font-bold text-indigo-500 uppercase"></div>
        </header>
        <div id="chat-box" class="flex-1 overflow-y-auto p-8 space-y-4 chat-scroll"></div>
        <footer class="p-6 bg-white border-t">
            <div class="max-w-4xl mx-auto flex gap-4 bg-slate-100 p-2 rounded-full border shadow-inner">
                <input type="text" id="msg-input" placeholder="Type a message..." class="flex-1 bg-transparent outline-none px-4 font-bold">
                <button id="send-btn" class="w-12 h-12 bg-indigo-600 text-white rounded-full flex items-center justify-center shadow-lg"><i class="fas fa-paper-plane"></i></button>
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

        // LOGIN SYSTEM
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
                document.getElementById('my-name').innerText = user.displayName;
                document.getElementById('my-avatar').innerText = user.displayName[0].toUpperCase();

                // 🟢 PRESENCE SYSTEM (ACTIVE USERS)
                const pRef = ref(db, 'online_users/' + user.uid);
                set(pRef, { name: user.displayName });
                onDisconnect(pRef).remove();
                
                loadMessages();
                listenOnlineUsers();
            }
        });

        // LIVE USERS LIST
        function listenOnlineUsers() {
            onValue(ref(db, 'online_users'), (snap) => {
                const list = document.getElementById('online-users');
                list.innerHTML = "";
                let count = 0;
                snap.forEach(c => {
                    count++;
                    list.innerHTML += `<li class="flex items-center gap-3 p-2 bg-white/5 rounded-lg"><div class="w-8 h-8 bg-indigo-500 flex items-center justify-center rounded text-[10px] font-bold">${c.val().name[0]}</div><span class="text-xs font-bold">${c.val().name}</span></li>`;
                });
                document.getElementById('user-count').innerText = count;
            });
        }

        // CHAT LOGIC
        function loadMessages() {
            const chatBox = document.getElementById('chat-box');
            onChildAdded(ref(db, 'messages/general'), (snap) => {
                const d = snap.val();
                const isMe = d.name === auth.currentUser.displayName;
                chatBox.innerHTML += `<div class="flex flex-col ${isMe ? 'items-end' : 'items-start'} animate-in fade-in"><span class="text-[9px] text-slate-400 mb-1 font-bold">${d.name}</span><div class="${isMe ? 'bg-indigo-600 text-white rounded-l-xl rounded-tr-xl' : 'bg-white text-slate-700 rounded-r-xl rounded-tl-xl border'} p-3 shadow-sm text-sm font-bold max-w-[80%]">${d.text}</div></div>`;
                chatBox.scrollTop = chatBox.scrollHeight;
            });
        }

        // SEND MESSAGE
        const send = () => {
            const input = document.getElementById('msg-input');
            if(input.value.trim()) {
                push(ref(db, 'messages/general'), {
                    name: auth.currentUser.displayName,
                    text: input.value,
                    time: serverTimestamp()
                }).catch(err => alert("Rules Error: " + err.message));
                input.value = "";
            }
        };

        document.getElementById('send-btn').onclick = send;
        document.getElementById('msg-input').onkeydown = (e) => { if(e.key === 'Enter') send(); };
    </script>
</body>
</html>
