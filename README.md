<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatPro | Multi-Device Suite</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        
        :root { --primary: #4f46e5; --bg-light: #f8fafc; --bg-dark: #0f172a; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; transition: background 0.3s, color 0.3s; }
        
        .dark { background-color: var(--bg-dark); color: #f1f5f9; }
        .dark .glass-header { background: rgba(30, 41, 59, 0.8); border-color: #334155; }
        .dark .sidebar { background: #1e293b; border-color: #334155; }
        .dark .msg-theirs { background: #334155; color: white; border: none; }
        .dark .input-area { background: #1e293b; border-color: #334155; color: white; }

        /* Responsive Sidebar */
        @media (max-width: 768px) {
            .sidebar { position: fixed; left: -100%; top: 0; height: 100%; z-index: 50; transition: 0.3s; width: 80%; }
            .sidebar.active { left: 0; }
            .msg-bubble { max-width: 90% !important; }
        }

        .msg-mine { background: linear-gradient(135deg, #6366f1, #4f46e5); color: white; border-radius: 1.2rem 1.2rem 0.2rem 1.2rem; }
        .msg-theirs { background: white; color: #1e293b; border-radius: 1.2rem 1.2rem 1.2rem 0.2rem; border: 1px solid #e2e8f0; }
    </style>
</head>
<body class="h-screen flex overflow-hidden bg-slate-50" id="body-tag">

    <div id="sidebar-overlay" onclick="toggleSidebar()" class="fixed inset-0 bg-black/50 z-40 hidden"></div>

    <aside id="sidebar" class="sidebar w-72 bg-white border-r border-slate-200 flex flex-col shadow-xl md:shadow-none">
        <div class="p-6 border-b flex justify-between items-center">
            <div class="flex items-center gap-2 font-bold text-xl text-indigo-600">
                <i class="fa-solid fa-bolt"></i> <span>ChatPro</span>
            </div>
            <button onclick="toggleSidebar()" class="md:hidden text-slate-400"><i class="fa-solid fa-xmark text-xl"></i></button>
        </div>
        <div class="flex-1 overflow-y-auto p-4 space-y-4" id="users-list">
            </div>
        <div class="p-4 border-t flex items-center justify-between bg-slate-50/50">
            <div class="flex items-center gap-3">
                <div id="my-avatar" class="w-10 h-10 rounded-xl bg-indigo-600 text-white flex items-center justify-center font-bold">?</div>
                <p id="my-name" class="text-xs font-bold truncate max-w-[100px]">User</p>
            </div>
            <button onclick="toggleDarkMode()" class="p-2 rounded-lg hover:bg-slate-200 transition text-slate-500"><i class="fa-solid fa-moon"></i></button>
        </div>
    </aside>

    <main class="flex-1 flex flex-col min-w-0 relative">
        <header class="h-16 glass-header bg-white/80 backdrop-blur-md border-b flex items-center justify-between px-4 md:px-8">
            <div class="flex items-center gap-3">
                <button onclick="toggleSidebar()" class="md:hidden p-2 text-slate-500"><i class="fa-solid fa-bars-staggered text-lg"></i></button>
                <h2 id="chat-title" class="font-bold text-slate-800"># general-hq</h2>
            </div>
            <button onclick="logout()" class="text-slate-400 hover:text-red-500"><i class="fa-solid fa-power-off"></i></button>
        </header>

        <div id="chat-flow" class="flex-1 overflow-y-auto p-4 md:p-8 space-y-6 bg-slate-50/30"></div>

        <div class="p-4 md:p-6">
            <form id="msg-form" class="max-w-4xl mx-auto flex items-center gap-2 bg-white p-2 rounded-2xl shadow-lg border border-slate-100 input-area">
                <input type="file" id="file-input" class="hidden">
                <button type="button" onclick="document.getElementById('file-input').click()" class="w-10 h-10 text-slate-400 hover:text-indigo-600"><i class="fa-solid fa-paperclip"></i></button>
                <input id="msg-input" type="text" placeholder="Type a message..." class="flex-1 bg-transparent p-2 outline-none text-sm">
                <button class="bg-indigo-600 text-white w-10 h-10 rounded-xl shadow-lg shadow-indigo-200 active:scale-90 transition-all">
                    <i class="fa-solid fa-paper-plane text-xs"></i>
                </button>
            </form>
        </div>
    </main>

    <script type="module">
        // [Firebase Initialization remains same as previous version]
        
        // Toggle Functions for Mobile
        window.toggleSidebar = () => {
            const sidebar = document.getElementById('sidebar');
            const overlay = document.getElementById('sidebar-overlay');
            sidebar.classList.toggle('active');
            overlay.classList.toggle('hidden');
        };

        window.toggleDarkMode = () => {
            document.getElementById('body-tag').classList.toggle('dark');
            const icon = document.querySelector('.fa-moon');
            if(icon) {
                icon.classList.toggle('fa-sun');
                icon.classList.toggle('fa-moon');
            }
        };

        // UI rendering logic update for Mobile
        // (Make sure to call window.toggleSidebar() when a user is clicked on mobile)
    </script>
</body>
</html>
