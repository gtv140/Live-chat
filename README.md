<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro OS | Futuristic Workspace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap');
        
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0;
            overflow: hidden;
        }

        /* Glassmorphism Container */
        .glass-panel {
            background: rgba(255, 255, 255, 0.9);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.3);
            border-radius: 2rem;
            width: 95vw;
            height: 90vh;
            display: flex;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
            overflow: hidden;
        }

        /* Sidebar Styling */
        .sidebar {
            width: 280px;
            background: #f8fafc;
            border-right: 1px solid #e2e8f0;
            display: flex;
            flex-direction: column;
        }

        /* Neon Bubbles */
        .msg-mine {
            background: linear-gradient(135deg, #ff0080, #7928ca);
            color: white;
            border-radius: 20px 20px 4px 20px;
            box-shadow: 0 10px 15px -3px rgba(121, 40, 202, 0.3);
        }

        .msg-theirs {
            background: white;
            color: #1e293b;
            border-radius: 20px 20px 20px 4px;
            border: 1px solid #f1f5f9;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05);
        }

        /* Custom Scrollbar */
        .chat-flow::-webkit-scrollbar { width: 6px; }
        .chat-flow::-webkit-scrollbar-thumb { background: #e2e8f0; border-radius: 10px; }

        .channel-btn.active {
            background: white;
            box-shadow: 0 4px 12px rgba(0,0,0,0.05);
            border-left: 4px solid #7928ca;
        }

        @media (max-width: 768px) {
            .sidebar { display: none; }
            .glass-panel { width: 100vw; height: 100vh; border-radius: 0; }
        }
    </style>
</head>
<body>

    <div id="login-overlay" class="fixed inset-0 z-[100] bg-indigo-900/90 backdrop-blur-md flex items-center justify-center">
        <div class="bg-white p-10 rounded-[2.5rem] shadow-2xl text-center max-w-sm w-full">
            <div class="w-20 h-20 bg-gradient-to-tr from-purple-600 to-pink-500 rounded-3xl flex items-center justify-center text-white mx-auto mb-6 shadow-lg rotate-12">
                <i class="fa-solid fa-rocket text-3xl"></i>
            </div>
            <h1 class="text-3xl font-bold text-slate-900 mb-2">ChatPro <span class="text-purple-600">OS</span></h1>
            <p class="text-slate-500 mb-8 font-medium">Next-gen Business Communication</p>
            <input id="username-input" type="text" placeholder="Your Name" class="w-full p-4 bg-slate-100 rounded-2xl mb-4 border-none outline-none text-center font-bold focus:ring-2 focus:ring-purple-500">
            <button id="join-btn" class="w-full bg-slate-900 text-white font-bold py-4 rounded-2xl hover:scale-105 transition">Launch Workspace</button>
        </div>
    </div>

    <div class="glass-panel">
        <aside class="sidebar">
            <div class="p-8">
                <div class="flex items-center gap-3 mb-10">
                    <div class="w-10 h-10 bg-purple-600 rounded-xl flex items-center justify-center text-white shadow-lg">
                        <i class="fa-solid fa-layer-group"></i>
                    </div>
                    <span class="font-bold text-xl tracking-tighter">ChatPro <span class="text-purple-600">OS</span></span>
                </div>

                <div class="relative mb-8">
                    <i class="fa-solid fa-magnifying-glass absolute left-4 top-3.5 text-slate-400"></i>
                    <input type="text" placeholder="Search Channels" class="w-full pl-12 pr-4 py-3 bg-slate-100 rounded-xl text-sm outline-none">
                </div>

                <nav class="space-y-2">
                    <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest ml-2 mb-4">Channels</p>
                    <div class="channel-btn active p-4 rounded-2xl flex items-center gap-4 cursor-pointer">
                        <i class="fa-solid fa-hashtag text-purple-600"></i>
                        <span class="text-sm font-bold">general-chat</span>
                    </div>
                    <div class="channel-btn p-4 rounded-2xl flex items-center gap-4 cursor-pointer hover:bg-slate-50">
                        <i class="fa-solid fa-briefcase text-slate-400"></i>
                        <span class="text-sm font-bold text-slate-600">marketing-team</span>
                    </div>
                </nav>
            </div>

            <div class="mt-auto p-6 border-t flex items-center gap-4">
                <div class="w-12 h-12 rounded-2xl bg-gradient-to-tr from-purple-500 to-indigo-600 text-white flex items-center justify-center font-bold shadow-lg" id="u-avatar">?</div>
                <div>
                    <p id="u-name" class="font-bold text-sm">Username</p>
                    <p class="text-[10px] text-green-500 font-bold">● ONLINE</p>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col bg-white">
            <header class="h-24 px-8 border-b flex items-center justify-between">
                <div>
                    <h2 class="text-xl font-bold text-slate-800"># general-chat</h2>
                    <p class="text-xs text-slate-400">Official workspace for company updates</p>
                </div>
                <div class="flex gap-4">
                    <button class="w-10 h-10 rounded-full border flex items-center justify-center text-slate-400"><i class="fa-solid fa-phone-flip"></i></button>
                    <button class="w-10 h-10 rounded-full bg-purple-600 text-white flex items-center justify-center shadow-lg"><i class="fa-solid fa-video"></i></button>
                </div>
            </header>

            <div id="chat-flow" class="chat-flow flex-1 overflow-y-auto p-8 space-y-6 bg-slate-50/50">
                </div>

            <div class="p-6">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-4 bg-white p-3 rounded-[2rem] shadow-2xl border border-slate-100">
                    <button type="button" class="w-12 h-12 text-slate-300 hover:text-purple-600"><i class="fa-solid fa-circle-plus text-2xl"></i></button>
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Write something inspiring..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium">
                    <button class="bg-purple-600 text-white px-8 py-3 rounded-2xl font-bold shadow-lg shadow-purple-200 hover:scale-105 transition">Send</button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
        let myName = "";

        window.onload = () => { const s = localStorage.getItem('chatProUser'); if(s) init(s); };
        document.getElementById('join-btn').onclick = () => { 
            const n = document.getElementById('username-input').value.trim(); 
            if(n){ localStorage.setItem('chatProUser', n); init(n); }
        };

        function init(n) {
            myName = n;
            document.getElementById('login-overlay').style.display = 'none';
            document.getElementById('u-name').innerText = n;
            document.getElementById('u-avatar').innerText = n[0].toUpperCase();
            
            onChildAdded(ref(db, 'msgs/global'), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const flow = document.getElementById('chat-flow');
                const div = document.createElement('div');
                div.className = `flex ${isMine ? 'justify-end' : 'justify-start'}`;
                
                div.innerHTML = `
                    <div class="msg ${isMine ? 'msg-mine' : 'msg-theirs'} p-4">
                        <p class="text-[9px] font-bold uppercase ${isMine?'text-purple-200':'text-slate-400'} mb-1">${data.sender}</p>
                        <p class="text-sm font-medium leading-relaxed">${data.text}</p>
                    </div>`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                push(ref(db, 'msgs/global'), { sender: myName, text: i.value, time: Date.now() }); 
                i.value = ""; 
            }
        };
    </script>
</body>
</html>
