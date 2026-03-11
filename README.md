<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Prime Ultra Hub</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        .chat-container::-webkit-scrollbar { width: 4px; }
        .chat-container::-webkit-scrollbar-thumb { background: #6366f1; border-radius: 10px; }
        .glass-card { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(12px); }
        .dark-mode { background-color: #020617 !important; color: #f8fafc; }
        .typing-dot { animation: blink 1s infinite; }
        @keyframes blink { 0%, 100% { opacity: 0.2; } 50% { opacity: 1; } }
    </style>
</head>
<body id="body" class="bg-slate-100 h-screen flex transition-all duration-500 overflow-hidden">

    <div id="login-overlay" class="fixed inset-0 bg-gradient-to-br from-indigo-900 to-slate-900 z-[100] flex items-center justify-center">
        <div class="bg-white/10 backdrop-blur-xl p-10 rounded-[2rem] border border-white/20 shadow-2xl w-full max-w-md text-center">
            <div class="text-5xl mb-4">🚀</div>
            <h1 class="text-3xl font-black text-white mb-2 tracking-tight">Prime Workspace</h1>
            <p class="text-indigo-200 mb-8">Ready to start your business day?</p>
            <input type="text" id="username-input" placeholder="Your Name" class="w-full bg-white/10 border border-white/20 p-4 rounded-2xl mb-4 outline-none text-white placeholder-indigo-300 text-center font-bold focus:ring-2 ring-indigo-500">
            <button id="join-btn" class="w-full bg-indigo-500 hover:bg-indigo-400 text-white p-4 rounded-2xl font-black shadow-xl transition-all active:scale-95">START CHATTING</button>
        </div>
    </div>

    <aside id="sidebar" class="w-80 bg-slate-900 text-white flex flex-col hidden md:flex border-r border-white/5">
        <div class="p-8 flex items-center justify-between">
            <h2 class="text-2xl font-black tracking-tighter bg-gradient-to-r from-indigo-400 to-purple-400 bg-clip-text text-transparent">PRIME.AI</h2>
            <button id="dark-toggle" class="w-10 h-10 rounded-xl bg-white/5 hover:bg-white/10 transition"><i class="fas fa-moon"></i></button>
        </div>

        <div class="flex-1 px-4 space-y-8 overflow-y-auto">
            <div class="space-y-1">
                <p class="px-4 text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-2">Channels</p>
                <div onclick="switchChannel('general')" class="flex items-center gap-3 p-4 bg-indigo-500/10 border border-indigo-500/20 rounded-2xl text-indigo-400 cursor-pointer"><i class="fas fa-hashtag"></i> <b>general</b></div>
                <div onclick="switchChannel('dev')" class="flex items-center gap-3 p-4 hover:bg-white/5 rounded-2xl cursor-pointer text-slate-400 transition"><i class="fas fa-code"></i> <b>development</b></div>
            </div>

            <div class="space-y-1">
                <p class="px-4 text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-2">Who's Online</p>
                <ul id="users-list" class="space-y-1"></ul>
            </div>
        </div>
    </aside>

    <main class="flex-1 flex flex-col glass-card">
        <header class="h-24 border-b border-black/5 flex items-center justify-between px-10">
            <div>
                <h2 id="channel-name" class="text-2xl font-black text-slate-800"># general</h2>
                <div id="typing-area" class="text-[10px] font-bold text-indigo-500 uppercase tracking-tighter h-4"></div>
            </div>
            <div id="my-profile" class="flex items-center gap-3 bg-slate-100 p-2 rounded-2xl border border-black/5">
                <div class="w-8 h-8 bg-indigo-500 rounded-lg flex items-center justify-center text-white text-xs font-bold">P</div>
                <span class="text-sm font-black text-slate-700 uppercase" id="display-name">Guest</span>
            </div>
        </header>

        <div id="chat-messages" class="flex-1 overflow-y-auto p-10 space-y-8 chat-container"></div>

        <footer class="p-8">
            <div id="footer-box" class="max-w-4xl mx-auto flex items-center gap-4 bg-white shadow-2xl shadow-indigo-200/20 p-3 rounded-[2rem] border border-black/5">
                <label class="w-12 h-12 flex items-center justify-center cursor-pointer hover:bg-slate-50 rounded-full text-slate-400 transition">
                    <i class="fas fa-paperclip"></i>
                    <input type="file" id="file-up" class="hidden">
                </label>
                <input type="text" id="msg-input" placeholder="Drop a message..." class="flex-1 outline-none font-bold text-slate-600 px-2">
                
                <button id="voice-btn" class="w-12 h-12 flex items-center justify-center text-slate-400 hover:text-red-500 transition"><i class="fas fa-microphone"></i></button>
                
                <button id="send-btn" class="bg-indigo-600 text-white w-14 h-14 rounded-full shadow-xl shadow-indigo-300 hover:scale-105 active:scale-90 transition-all flex items-center justify-center">
                    <i class="fas fa-paper-plane"></i>
                </button>
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

        // UI Logic & Dark Mode
        document.getElementById('dark-toggle').onclick = () => {
            document.getElementById('body').classList.toggle('dark-mode');
            document.getElementById('footer-box').classList.toggle('bg-slate-800');
        };

        // Complete Logic for Login, Presence, and Voice/Typing indicators goes here...
        // ... (Adding specialized listeners for typing and presence)
    </script>
</body>
</html>
