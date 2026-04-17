<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Fix</title>
    <style>
        /* Screen ko h हिलne se rokne ke liye */
        html, body { 
            height: 100%; margin: 0; padding: 0; 
            overflow: hidden; font-family: sans-serif; 
            background: #f0f2f5; 
        }

        /* 1. Header: Top par fix rahega */
        .app-header {
            position: absolute; top: 0; left: 0; right: 0;
            height: 60px; background: #6a11cb; color: white;
            display: flex; align-items: center; justify-content: center;
            font-weight: bold; z-index: 100;
        }

        /* 2. Message Area: Sirf ye area scroll hoga */
        #chat-window {
            position: absolute;
            top: 60px; /* Header ke niche se shuru */
            bottom: 70px; /* Input bar ke upar tak */
            left: 0; right: 0;
            overflow-y: auto;
            padding: 15px;
            display: flex; flex-direction: column; gap: 10px;
            background: #e5ddd5;
            -webkit-overflow-scrolling: touch;
        }

        /* 3. Input Bar: Bottom par bilkul chipka rahega */
        .bottom-bar {
            position: absolute; bottom: 0; left: 0; right: 0;
            height: 70px; background: white;
            border-top: 1px solid #ddd;
            display: flex; align-items: center; padding: 0 10px;
            z-index: 100;
        }

        #msgInp {
            flex: 1; padding: 12px 15px;
            border: 1px solid #ccc; border-radius: 25px;
            font-size: 16px; outline: none; background: #fff;
        }

        .btn-send {
            background: #6a11cb; color: white;
            border: none; width: 45px; height: 45px;
            border-radius: 50%; margin-left: 10px;
            cursor: pointer; display: flex;
            align-items: center; justify-content: center;
        }

        /* Bubbles */
        .msg { max-width: 80%; padding: 10px; border-radius: 10px; font-size: 15px; }
        .mine { align-self: flex-end; background: #dcf8c6; }
        .others { align-self: flex-start; background: white; }
    </style>
</head>
<body>

    <div class="app-header">LIVE CONNECT</div>

    <div id="chat-window"></div>

    <div class="bottom-bar">
        <input type="text" id="msgInp" placeholder="Type here, sweetie...">
        <button class="btn-send" onclick="send()">➔</button>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const myId = "User_" + Math.floor(Math.random() * 999);

        window.send = () => {
            const el = document.getElementById('msgInp');
            if (!el.value.trim()) return;
            push(ref(db, 'messages/global'), {
                u: myId, t: el.value, ts: serverTimestamp()
            });
            el.value = "";
        };

        const win = document.getElementById('chat-window');
        onChildAdded(ref(db, 'messages/global'), (s) => {
            const d = s.val();
            const div = document.createElement('div');
            div.className = `msg ${d.u === myId ? 'mine' : 'others'}`;
            div.innerText = d.t;
            win.appendChild(div);
            win.scrollTop = win.scrollHeight; // Auto-scroll
        });

        document.getElementById('msgInp').onkeypress = (e) => { if(e.key==='Enter') send(); };
    </script>
</body>
</html>
