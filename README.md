<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gemini Live Chat</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background-color: #f0f2f5; }
        #chat-container { height: 80vh; overflow-y: auto; scroll-behavior: smooth; }
        .message { margin-bottom: 12px; padding: 8px 15px; border-radius: 18px; max-width: 75%; width: fit-content; }
        .received { background: white; align-self: flex-start; border-bottom-left-radius: 2px; }
        .sent { background: #0084ff; color: white; align-self: flex-end; border-bottom-right-radius: 2px; }
    </style>
</head>
<body class="flex flex-col h-screen">

    <div class="bg-blue-600 p-4 text-white text-center font-bold shadow-md">
        🚀 Global Chat Room
    </div>

    <div id="chat-container" class="flex-1 p-4 flex flex-col overflow-y-auto">
        </div>

    <form id="chat-form" class="p-4 bg-white border-t flex gap-2">
        <input id="message-input" type="text" placeholder="Type a message..." 
               class="flex-1 border rounded-full px-4 py-2 outline-none focus:border-blue-500">
        <button type="submit" class="bg-blue-600 text-white px-6 py-2 rounded-full hover:bg-blue-700 transition">
            Send
        </button>
    </form>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, set, onChildAdded } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        // Aapki Firebase Config
        const firebaseConfig = {
            apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
            authDomain: "live-chat-b810c.firebaseapp.com",
            databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
            projectId: "live-chat-b810c",
            storageBucket: "live-chat-b810c.firebasestorage.app",
            messagingSenderId: "555058795334",
            appId: "1:555058795334:web:f668887409800c32970b47"
        };

        // Initialize Firebase
        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const chatContainer = document.getElementById('chat-container');
        const chatForm = document.getElementById('chat-form');
        const messageInput = document.getElementById('message-input');

        // Username prompt
        const username = prompt("Apna naam likhein:") || "Guest_" + Math.floor(Math.random() * 1000);

        // 1. Message Send Karne ka Logic
        chatForm.addEventListener('submit', (e) => {
            e.preventDefault();
            const text = messageInput.value.trim();
            if (text !== "") {
                const messagesRef = ref(db, 'messages');
                const newMessageRef = push(messagesRef);
                set(newMessageRef, {
                    user: username,
                    message: text,
                    timestamp: Date.now()
                });
                messageInput.value = "";
            }
        });

        // 2. Real-time Messages Receive Karne ka Logic
        const messagesRef = ref(db, 'messages');
        onChildAdded(messagesRef, (data) => {
            const val = data.val();
            appendMessage(val.user, val.message);
        });

        function appendMessage(user, msg) {
            const div = document.createElement('div');
            const isMine = user === username;
            div.className = `message \${isMine ? 'sent' : 'received shadow-sm'}`;
            div.innerHTML = `
                <span class="block text-[10px] font-bold \${isMine ? 'text-blue-100' : 'text-gray-500'}">\${user}</span>
                <span>\${msg}</span>
            `;
            chatContainer.appendChild(div);
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }
    </script>
</body>
</html>
