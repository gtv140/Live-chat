<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>NeonPro Ultra | Final Fixed</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        :root { --neon-purple: #a855f7; --neon-cyan: #22d3ee; }
        body, html { height: 100%; margin: 0; padding: 0; overflow: hidden; background: #020617; font-family: 'Plus Jakarta Sans', sans-serif; color: #f8fafc; }
        
        .chat-view { flex: 1; display: flex; flex-direction: column; background: radial-gradient(circle at top right, #1e1b4b, #020617); position: relative; height: 100vh; }
        
        /* ASLI FIX: Chat flow mein itni jagah ke message box kabhi rasta na rokay */
        #chat-flow { flex: 1; overflow-y: auto; padding: 20px; padding-bottom: 220px !important; display: flex; flex-direction: column; gap: 15px; }

        /* INPUT BAR - ALWAYS ON TOP */
        .input-area { 
            position: fixed !important; bottom: 30px !important; left: 15px !important; right: 15px !important; 
            z-index: 999 !important; background: rgba(15, 23, 42, 0.95) !important; 
            backdrop-filter: blur(20px); border-radius: 24px; padding: 12px 20px; 
            border: 2px solid var(--neon-purple); box-shadow: 0 0 40px rgba(168, 85, 247, 0.5); 
        }

        .msg-card { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); padding: 12px; border-radius: 18px; max-width: 85%; align-self: flex-start; }
        .msg-card.mine { align-self: flex-end; border-color: var(--neon-purple); background: rgba(168, 85, 247, 0.15); }

        .reaction-chip { background: rgba(255,255,255,0.05); padding: 4px 10px; border-radius: 12px; font-size: 12px; cursor: pointer; border: 1px solid rgba(255,255,255,0.1); display: inline-flex; align-items: center; gap: 5px; margin-top: 5px; }
    </style>
</head>
<body>

    <div class="chat-view">
        <header class="h-20 border-b border-white/5 flex items-center px-6">
            <h2 class="text-xl font-bold text-white tracking-tighter"># Global-HQ <span class="text-xs text-cyan-400 ml-2">● Online</span></h2>
        </header>

        <div id="chat-flow"></div>

        <div class="input-area">
            <form id="msg-form" class="flex items-center gap-4">
                <input type="file" id="file-input" class="hidden" onchange="uploadFile(this)">
                <button type="button" onclick="document.getElementById('file-input').click()" class="text-purple-400 hover:text-cyan-400 transition">
                    <i class="fa-solid fa-camera-retro text-2xl"></i>
                </button>
                
                <input id="msg-input" type="text" placeholder="Type message here..." class="bg-transparent text-white flex-1 outline-none font-medium" autocomplete="off">
                
                <button class="w-12 h-12 bg-purple-600 rounded-2xl flex items-center justify-center text-white shadow-lg active:scale-90 transition">
                    <i class="fa-solid fa-paper-plane text-sm"></i>
                </button>
            </form>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onValue, update, increment } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";
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
        const myName = localStorage.getItem('chatProUser') || "User_" + Math.floor(Math.random()*100);

        function loadMessages() {
            const flow = document.getElementById('chat-flow');
            onValue(ref(db, 'msgs/global'), (snap) => {
                flow.innerHTML = "";
                snap.forEach(msg => {
                    const d = msg.val();
                    const msgId = msg.key;
                    const div = document.createElement('div');
                    div.className = `msg-card ${d.sender === myName ? 'mine' : ''}`;
                    
                    let content = d.text || "";
                    if(d.fileUrl) content = `<img src="${d.fileUrl}" class="rounded-xl max-w-full mb-2">`;
                    
                    let reacts = "";
                    if(d.reactions) Object.keys(d.reactions).forEach(e => {
                        reacts += `<div class="reaction-chip" onclick="react('${msgId}', '${e}')">${e} <b>${d.reactions[e]}</b></div>`;
                    });

                    div.innerHTML = `
                        <p class="text-[10px] font-bold text-purple-400 mb-1">${d.sender}</p>
                        <div class="text-sm">${content}</div>
                        <div class="flex gap-2">${reacts} <div class="reaction-chip opacity-40" onclick="react('${msgId}', '👍')">+ 👍</div></div>
                    `;
                    flow.appendChild(div);
                });
                flow.scrollTop = flow.scrollHeight;
            });
        }

        window.react = (id, e) => update(ref(db, `msgs/global/${id}/reactions`), { [e]: increment(1) });

        window.uploadFile = async (i) => {
            const file = i.files[0]; if(!file) return;
            const r = sRef(storage, `files/${Date.now()}`);
            await uploadBytes(r, file);
            const url = await getDownloadURL(r);
            push(ref(db, 'msgs/global'), { sender: myName, fileUrl: url, time: Date.now() });
        };

        document.getElementById('msg-form').onsubmit = e => {
            e.preventDefault();
            const i = document.getElementById('msg-input');
            if(i.value.trim()){ 
                push(ref(db, 'msgs/global'), { sender: myName, text: i.value, time: Date.now() });
                i.value = ""; 
            }
        };

        loadMessages();
    </script>
</body>
</html>
