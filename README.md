<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro | Advanced Voice & Location Suite</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; transition: 0.3s; }
        .dark { background-color: #0f172a; color: #f1f5f9; }
        .msg-mine { background: #4f46e5; color: white; border-radius: 1.2rem 1.2rem 0.2rem 1.2rem; }
        .msg-theirs { background: white; color: #1e293b; border-radius: 1.2rem 1.2rem 1.2rem 0.2rem; border: 1px solid #e2e8f0; }
        #chat-flow { height: calc(100vh - 180px); scroll-behavior: smooth; }
        .recording-pulse { animation: pulse 1s infinite; color: red; }
        @keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.3; } 100% { opacity: 1; } }
    </style>
</head>
<body class="h-screen flex overflow-hidden bg-slate-50" id="main-body">

    <div id="login-screen" class="fixed inset-0 z-[100] bg-indigo-600 flex items-center justify-center p-6">
        <div class="bg-white p-10 rounded-[2.5rem] shadow-2xl w-full max-w-sm text-center">
            <h2 class="text-3xl font-bold mb-6">ChatPro <span class="text-indigo-600">v3.0</span></h2>
            <input id="username-input" type="text" placeholder="Apna Naam Likhein..." class="w-full border p-4 rounded-xl mb-4 text-center font-bold outline-none">
            <button id="join-btn" class="w-full bg-indigo-600 text-white font-bold py-4 rounded-xl">Enter Workspace</button>
        </div>
    </div>

    <aside class="w-72 bg-white border-r hidden md:flex flex-col">
        <div class="p-6 border-b font-bold text-xl text-indigo-600">ChatPro</div>
        <div id="users-list" class="flex-1 overflow-y-auto p-4 space-y-2"></div>
    </aside>

    <main class="flex-1 flex flex-col relative">
        <header class="h-16 bg-white border-b flex items-center justify-between px-6 shadow-sm">
            <h2 id="chat-title" class="font-bold"># global-hq</h2>
            <div class="flex gap-4 items-center">
                <button onclick="sendLocation()" title="Send Location" class="text-slate-400 hover:text-blue-500"><i class="fa-solid fa-location-dot text-lg"></i></button>
                <button onclick="toggleDark()" class="text-slate-400"><i class="fa-solid fa-moon"></i></button>
            </div>
        </header>

        <div id="chat-flow" class="flex-1 p-6 space-y-6 overflow-y-auto"></div>

        <div class="p-4 bg-white border-t">
            <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-3 bg-slate-50 p-2 rounded-2xl border">
                <button type="button" id="voice-btn" class="w-10 h-10 flex items-center justify-center text-slate-400 hover:text-red-500">
                    <i class="fa-solid fa-microphone text-xl"></i>
                </button>
                <input id="msg-input" type="text" placeholder="Kuch likhein..." class="flex-1 bg-transparent p-2 outline-none text-sm font-medium">
                <button id="send-btn" class="bg-indigo-600 text-white w-10 h-10 rounded-xl flex items-center justify-center shadow-lg active:scale-90 transition-all">
                    <i class="fa-solid fa-paper-plane text-xs"></i>
                </button>
            </form>
            <p id="rec-status" class="text-[10px] text-center mt-2 font-bold hidden"></p>
        </div>
    </main>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        let mediaRecorder;
        let audioChunks = [];

        // STARTUP
        window.onload = () => {
            const saved = localStorage.getItem('chatProUser');
            if(saved) login(saved);
        };

        document.getElementById('join-btn').onclick = () => {
            const val = document.getElementById('username-input').value.trim();
            if(val) { localStorage.setItem('chatProUser', val); login(val); }
        };

        function login(name) {
            myName = name;
            document.getElementById('login-screen').classList.add('hidden');
            loadMsgs();
        }

        // --- VOICE RECORDING LOGIC ---
        const vBtn = document.getElementById('voice-btn');
        vBtn.onclick = async () => {
            if (!mediaRecorder || mediaRecorder.state === "inactive") {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                mediaRecorder.start();
                audioChunks = [];
                vBtn.innerHTML = '<i class="fa-solid fa-stop recording-pulse"></i>';
                document.getElementById('rec-status').innerText = "Recording...";
                document.getElementById('rec-status').classList.remove('hidden');

                mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                mediaRecorder.onstop = async () => {
                    const audioBlob = new Blob(audioChunks, { type: 'audio/webm' });
                    const fileRef = sRef(storage, `voice/${Date.now()}.webm`);
                    await uploadBytes(fileRef, audioBlob);
                    const url = await getDownloadURL(fileRef);
                    push(ref(db, 'msgs/global'), { sender: myName, voiceUrl: url, time: Date.now() });
                    vBtn.innerHTML = '<i class="fa-solid fa-microphone text-xl"></i>';
                    document.getElementById('rec-status').classList.add('hidden');
                };
            } else {
                mediaRecorder.stop();
            }
        };

        // --- LOCATION LOGIC ---
        window.sendLocation = () => {
            navigator.geolocation.getCurrentPosition(pos => {
                const mapLink = `https://www.google.com/maps?q=${pos.coords.latitude},${pos.coords.longitude}`;
                push(ref(db, 'msgs/global'), { sender: myName, text: "📍 Meri Live Location", locUrl: mapLink, time: Date.now() });
            });
        };

        function loadMsgs() {
            onChildAdded(ref(db, 'msgs/global'), (snap) => {
                const data = snap.val();
                const isMine = data.sender === myName;
                const flow = document.getElementById('chat-flow');
                const div = document.createElement('div');
                div.className = `flex ${isMine ? 'justify-end' : 'justify-start'}`;

                let content = data.text || "";
                if(data.voiceUrl) content = `<audio controls class="w-48 h-8"><source src="${data.voiceUrl}" type="audio/webm"></audio>`;
                if(data.locUrl) content = `<a href="${data.locUrl}" target="_blank" class="bg-blue-50 p-2 rounded-lg text-xs font-bold block text-blue-600 underline">Open in Google Maps 🌍</a>`;

                div.innerHTML = `
                    <div class="max-w-[80%] ${isMine ? 'msg-mine' : 'msg-theirs'} p-4 shadow-sm">
                        <p class="text-[8px] font-bold opacity-50 mb-1">${data.sender}</p>
                        ${content}
                        <p class="text-[7px] text-right mt-1 opacity-40">Sent</p>
                    </div>`;
                flow.appendChild(div);
                flow.scrollTop = flow.scrollHeight;
            });
        }

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const inp = document.getElementById('msg-input');
            if(inp.value.trim()) {
                push(ref(db, 'msgs/global'), { sender: myName, text: inp.value, time: Date.now() });
                inp.value = "";
            }
        };

        window.toggleDark = () => document.getElementById('main-body').classList.toggle('dark');
    </script>
</body>
</html>
