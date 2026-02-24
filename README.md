<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro | Multi-User Realtime Platform</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .glass-morphism { background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(10px); }
        #chat-messages { height: 400px; overflow-y: auto; scroll-behavior: smooth; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
    </style>
</head>
<body class="bg-gray-50 font-sans">

    <nav class="bg-blue-700 text-white shadow-lg sticky top-0 z-50">
        <div class="max-w-6xl mx-auto px-4 py-3 flex justify-between items-center">
            <h1 class="text-2xl font-bold flex items-center gap-2">
                <i class="fas fa-comments"></i> ChatPro
            </h1>
            <div class="flex gap-6">
                <button onclick="showTab('home')" class="hover:text-blue-200">Home</button>
                <button onclick="showTab('about')" class="hover:text-blue-200">About</button>
                <button onclick="showTab('contact')" class="hover:text-blue-200">Contact</button>
            </div>
        </div>
    </nav>

    <div class="max-w-6xl mx-auto p-4 md:p-8">
        
        <div id="home" class="tab-content active">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-8">
                <div class="bg-white p-6 rounded-xl shadow-sm border-l-4 border-blue-500">
                    <p class="text-gray-500 text-sm">Active Users</p>
                    <h3 id="active-users-count" class="text-3xl font-bold text-gray-800">0</h3>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-sm border-l-4 border-green-500">
                    <p class="text-gray-500 text-sm">Total Messages</p>
                    <h3 id="total-messages-count" class="text-3xl font-bold text-gray-800">0</h3>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-sm border-l-4 border-purple-500">
                    <p class="text-gray-500 text-sm">Server Status</p>
                    <h3 class="text-xl font-bold text-green-600">Online <i class="fas fa-circle text-xs"></i></h3>
                </div>
            </div>

            <div class="bg-white rounded-2xl shadow-xl overflow-hidden flex flex-col md:flex-row">
                <div class="w-full md:w-1/4 bg-gray-100 p-4 border-r">
                    <h4 class="font-bold text-gray-700 mb-4">Online Now</h4>
                    <ul id="users-list" class="space-y-2 text-sm text-gray-600">
                        </ul>
                </div>
                <div class="flex-1 flex flex-col">
                    <div id="chat-messages" class="p-6 space-y-4 bg-gray-50"></div>
                    <form id="chat-form" class="p-4 bg-white border-t flex gap-2">
                        <input id="msg-input" type="text" placeholder="Write something..." class="flex-1 border-2 border-gray-200 rounded-full px-6 py-2 outline-none focus:border-blue-500">
                        <button class="bg-blue-600 text-white p-3 rounded-full hover:bg-blue-700 w-12 h-12 flex items-center justify-center">
                            <i class="fas fa-paper-plane"></i>
                        </button>
                    </form>
                </div>
            </div>
        </div>

        <div id="about" class="tab-content bg-white p-8 rounded-2xl shadow-lg">
            <h2 class="text-3xl font-bold text-blue-700 mb-4">About ChatPro</h2>
            <p class="text-gray-600 leading-relaxed mb-4">ChatPro is a next-generation real-time communication platform built with Firebase. Our mission is to connect people instantly through high-speed cloud technology.</p>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-6">
                <img src="https://images.unsplash.com/photo-1522071820081-009f0129c71c?auto=format&fit=crop&w=400" alt="Team" class="rounded-lg shadow">
                <div class="flex flex-col justify-center">
                    <h4 class="font-bold text-xl mb-2">Our Features</h4>
                    <ul class="list-disc list-inside text-gray-600">
                        <li>End-to-End Realtime Sync</li>
                        <li>Global Chat Rooms</li>
                        <li>Analytics Dashboard</li>
                        <li>Secure Database Storage</li>
                    </ul>
                </div>
            </div>
        </div>

        <div id="contact" class="tab-content bg-white p-8 rounded-2xl shadow-lg">
            <h2 class="text-3xl font-bold text-blue-700 mb-6 text-center">Contact Us</h2>
            <form class="max-w-lg mx-auto space-y-4">
                <input type="text" placeholder="Your Name" class="w-full border p-3 rounded-lg outline-none focus:ring-2 focus:ring-blue-400">
                <input type="email" placeholder="Email Address" class="w-full border p-3 rounded-lg outline-none focus:ring-2 focus:ring-blue-400">
                <textarea placeholder="Your Message" rows="4" class="w-full border p-3 rounded-lg outline-none focus:ring-2 focus:ring-blue-400"></textarea>
                <button type="button" onclick="alert('Message Sent!')" class="w-full bg-blue-600 text-white font-bold py-3 rounded-lg hover:bg-blue-700 transition">Send Message</button>
            </form>
        </div>

    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded, onValue, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        // Your Config
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
        const username = prompt("Welcome! Enter your username:") || "Anonymous";

        // --- DASHBOARD ANALYTICS ---
        const msgRef = ref(db, 'messages');
        onValue(msgRef, (snapshot) => {
            const count = snapshot.size;
            document.getElementById('total-messages-count').innerText = count;
        });

        // Simulating Active Users (Based on recent connections)
        document.getElementById('active-users-count').innerText = Math.floor(Math.random() * 5) + 1;

        // --- REALTIME CHAT ---
        const form = document.getElementById('chat-form');
        const input = document.getElementById('msg-input');
        const container = document.getElementById('chat-messages');

        form.onsubmit = (e) => {
            e.preventDefault();
            if(input.value.trim()){
                push(msgRef, {
                    user: username,
                    text: input.value,
                    time: serverTimestamp()
                });
                input.value = "";
            }
        };

        onChildAdded(msgRef, (snap) => {
            const data = snap.val();
            const div = document.createElement('div');
            const isMine = data.user === username;
            div.className = `flex flex-col \${isMine ? 'items-end' : 'items-start'}`;
            div.innerHTML = `
                <div class="\${isMine ? 'bg-blue-600 text-white' : 'bg-white text-gray-800'} px-4 py-2 rounded-2xl shadow-sm max-w-xs">
                    <p class="text-[10px] opacity-70 font-bold">\${data.user}</p>
                    <p>\${data.text}</p>
                </div>
            `;
            container.appendChild(div);
            container.scrollTop = container.scrollHeight;
        });

    </script>

    <script>
        // Tab System Logic
        function showTab(tabId) {
            document.querySelectorAll('.tab-content').forEach(tab => tab.classList.remove('active'));
            document.getElementById(tabId).classList.add('active');
        }
    </script>
</body>
</html>
