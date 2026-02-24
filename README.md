<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ChatPro | Ultimate Business & Personal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        
        :root { --neon: #7928ca; --pink: #ff0080; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #0f172a; height: 100dvh; margin: 0; overflow: hidden; }
        
        /* Fixed Mobile Scroll Fix */
        .app-container { height: 100dvh; display: flex; flex-direction: column; background: #f8fafc; overflow: hidden; }
        
        #chat-flow { 
            flex: 1; 
            overflow-y: auto; 
            padding: 15px; 
            display: flex; 
            flex-direction: column; 
            gap: 12px;
            -webkit-overflow-scrolling: touch;
        }

        /* Modern Bubbles */
        .msg { max-width: 85%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative; }
        .msg.mine { align-self: flex-end; background: linear-gradient(135deg, var(--pink), var(--neon)); color: white; border-bottom-right-radius: 4px; box-shadow: 0 4px 12px rgba(121, 40, 202, 0.2); }
        .msg.theirs { align-self: flex-start; background: white; color: #1e293b; border-bottom-left-radius: 4px; border: 1px solid #e2e8f0; }

        .sidebar { transition: 0.3s; z-index: 100; position: fixed; left: -100%; top: 0; bottom: 0; width: 280px; background: white; box-shadow: 10px 0 30px rgba(0,0,0,0.1); }
        .sidebar.active { left: 0; }
        @media (min-width: 1024px) { .sidebar { position: static; left: 0; display: flex !important; } }

        /* Animation for Voice Recording */
        .rec-active { animation: pulse 1.5s infinite; color: #ff0080; }
        @keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.4; } 100% { opacity: 1; } }
    </style>
</head>
<body>

    <div id="login-screen" class="fixed inset-0 z-[200] bg-slate-900 flex items-center justify-center p-6">
        <div class="bg-white p-8 rounded-[2rem] shadow-2xl w-full max-w-sm text-center">
            <h2 class="text-2xl font-bold mb-6">ChatPro <span class="text-purple-600">v4.0</span></h2>
            <input id="username-input" type="text" placeholder="Enter Name..." class="w-full p-4 bg-slate-100 rounded-xl mb-4 text-center font-bold outline-none border-2 border-transparent focus:border-purple-500">
            <button id="join-btn" class="w-full bg-purple-600 text-white font-bold py-4 rounded-xl shadow-lg">Launch App</button>
        </div>
    </div>

    <div class="app-container flex-row">
        <aside id="sidebar" class="sidebar flex flex-col">
            <div class="p-6 border-b flex justify-between items-center">
                <span class="font-bold text-xl text-purple-600">Workspace</span>
                <button onclick="toggleSidebar()" class="lg:hidden text-slate-400"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div id="users-list" class="flex-1 overflow-y-auto p-4 space-y-2"></div>
            <div class="p-4 border-t bg-slate-50 flex items-center justify-between">
                <div class="flex items-center gap-2">
                    <div id="u-avatar" class="w-8 h-8 rounded-lg bg-purple-600 text-white flex items-center justify-center text-xs font-bold">?</div>
                    <span id="u-name" class="text-xs font-bold truncate max-w-[100px]">User</span>
                </div>
                <button onclick="logout()" class="text-slate-400 hover:text-red-500"><i class="fa-solid fa-power-off"></i></button>
            </div>
        </aside>

        <main class="flex-1 flex flex-col relative bg-[#f1f5f9]">
            <header class="h-16 bg-white border-b flex items-center justify-between px-6 shrink-0">
                <div class="flex items-center gap-3">
                    <button onclick="toggleSidebar()" class="lg:hidden text-slate-500 text-xl"><i class="fa-solid fa-bars"></i></button>
                    <h2 class="font-bold"># general-chat</h2>
                </div>
                <button onclick="sendLoc()" class="text-purple-600"><i class="fa-solid fa-location-dot"></i></button>
            </header>

            <div id="chat-flow"></div>

            <div class="p-3 bg-white border-t shrink-0">
                <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-2 bg-slate-100 p-1.5 rounded-2xl border">
                    <button type="button" id="voice-btn" class="w-10 h-10 text-slate-400"><i class="fa-solid fa-microphone"></i></button>
                    <input id="msg-input" type="text" autocomplete="off" placeholder="Write message..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium">
                    <button class="bg-purple-600 text-white w-10 h-10 rounded-xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-paper-plane text-xs"></i></button>
                </form>
            </div>
        </main>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let myName = "", mediaRecorder, audioChunks = [];

        window.onload = () => { const s = localStorage.getItem('chatProUser'); if(s) init(s); };
        document.getElementById('join-btn').onclick = () => { 
            const n = document.getElementById('username-input').value.trim(); 
            if(n){ localStorage.setItem('chatProUser', n); init(n); }
        };

        function init(n) {
            myName = n;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('u-name').innerText = n;
            document.getElementById('u-avatar').innerText = n[0];
            set(ref(db, 'online/' + n), true);
            
            onChildAdded(ref(db, 'msgs/global'), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const flow = document.getElementById('chat-flow');
                const div = document.createElement('div');
                div.className = `msg ${isMine ? 'mine' : 'theirs'}`;
                
                let content = data.text || "";
                if(data.voiceUrl) content = `<audio controls style="width:180px;height:30px;"><source src="${data.voiceUrl}"></audio>`;
                if(data.locUrl) content = `<a href="${data.locUrl}" target="_blank" class="text-xs underline font-bold">📍 View My Location</a>`;

                div.innerHTML = `<p class="text-[9px] font-bold uppercase opacity-50 mb-1">${data.sender}</p><div>${content}</div>`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        // Voice Messages Fix for Mobile
        const vBtn = document.getElementById('voice-btn');
        vBtn.onclick = async () => {
            if(!mediaRecorder || mediaRecorder.state === "inactive") {
                const s = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(s);
                mediaRecorder.start(); audioChunks = [];
                vBtn.classList.add('rec-active');
                mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                mediaRecorder.onstop = async () => {
                    const blob = new Blob(audioChunks, { type: 'audio/webm' });
                    const r = sRef(storage, `v/${Date.now()}.webm`);
                    await uploadBytes(r, blob);
                    const url = await getDownloadURL(r);
                    push(ref(db, 'msgs/global'), { sender: myName, voiceUrl: url, time: Date.now() });
                    vBtn.classList.remove('rec-active');
                };
            } else { mediaRecorder.stop(); }
        };

        window.toggleSidebar = () => document.getElementById('sidebar').classList.toggle('active');
        window.logout = () => { localStorage.removeItem('chatProUser'); location.reload(); };
        window.sendLoc = () => {
            navigator.geolocation.getCurrentPosition(p => {
                const url = `https://www.google.com/maps?q=${p.coords.latitude},${p.coords.longitude}`;
                push(ref(db, 'msgs/global'), { sender: myName, locUrl: url, time: Date.now() });
            });
        };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ push(ref(db, 'msgs/global'), { sender: myName, text: i.value, time: Date.now() }); i.value = ""; }
        };
    </script>
</body>
</html>
