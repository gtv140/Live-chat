<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple Chat Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        :root { --bg: #ffffff; --card: #f3f4f6; --text: #111827; --primary: #7c3aed; }
        .dark-mode { --bg: #0f172a; --card: #1e293b; --text: #f8fafc; }

        body { background-color: var(--bg); color: var(--text); transition: 0.3s; font-family: sans-serif; }
        
        /* Simple Card Style */
        .chat-card {
            background-color: var(--card);
            border-radius: 15px;
            padding: 12px 16px;
            margin-bottom: 10px;
            max-width: 80%;
            position: relative;
            box-shadow: 0 2px 5px rgba(0,0,0,0.05);
        }
        .mine { align-self: flex-end; background-color: var(--primary); color: white; }
        .other { align-self: flex-start; }

        #chat-flow { height: calc(100dvh - 140px); overflow-y: auto; display: flex; flex-direction: column; padding: 20px; }
        
        .input-area { position: fixed; bottom: 0; width: 100%; padding: 15px; background: var(--bg); border-top: 1px solid rgba(0,0,0,0.1); }
    </style>
</head>
<body>

    <header class="p-4 border-b flex justify-between items-center bg-white dark:bg-slate-900 sticky top-0 z-50">
        <h1 class="font-bold text-xl">Neon<span class="text-purple-600">Chat</span></h1>
        <button onclick="toggleDark()" class="p-2 bg-gray-200 dark:bg-gray-700 rounded-full w-10 h-10">
            <i id="theme-icon" class="fa-solid fa-moon"></i>
        </button>
    </header>

    <div id="chat-flow">
        </div>

    <div class="input-area">
        <form id="chat-form" class="max-w-4xl mx-auto flex gap-2">
            <input id="msg-input" type="text" placeholder="Type a message..." class="flex-1 p-3 rounded-full border dark:bg-gray-800 outline-none focus:border-purple-500">
            <button class="bg-purple-600 text-white px-6 rounded-full hover:bg-purple-700 transition">
                <i class="fa-solid fa-paper-plane"></i>
            </button>
        </form>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        // Firebase Configuration
        const firebaseConfig = {
            apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
            databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
            projectId: "live-chat-b810c",
            appId: "1:555058795334:web:f668887409800c32970b47"
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const user = "User_" + Math.floor(Math.random() * 1000); // Simple random username

        // Theme Toggle
        window.toggleDark = () => {
            document.body.classList.toggle('dark-mode');
            const isDark = document.body.classList.contains('dark-mode');
            document.getElementById('theme-icon').className = isDark ? "fa-solid fa-sun" : "fa-solid fa-moon";
        };

        // Send Message
        document.getElementById('chat-form').onsubmit = (e) => {
            e.preventDefault();
            const input = document.getElementById('msg-input');
            if (input.value.trim()) {
                push(ref(db, 'messages'), {
                    sender: user,
                    text: input.value,
                    time: Date.now()
                });
                input.value = '';
            }
        };

        // Load Messages
        onValue(ref(db, 'messages'), (snapshot) => {
            const flow = document.getElementById('chat-flow');
            flow.innerHTML = '';
            snapshot.forEach((child) => {
                const data = child.val();
                const div = document.createElement('div');
                div.className = `chat-card ${data.sender === user ? 'mine' : 'other'}`;
                div.innerHTML = `<small style="display:block; font-size:10px; opacity:0.7">${data.sender}</small>${data.text}`;
                flow.appendChild(div);
            });
            flow.scrollTop = flow.scrollHeight;
        });
    </script>
</body>
</html>
