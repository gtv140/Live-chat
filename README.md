<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Fix</title>
    <style>
        /* Screen ko freeze karne ke liye */
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body, html { height: 100%; overflow: hidden; font-family: sans-serif; background: #f0f2f5; }

        /* Main App Container */
        .app-container {
            display: flex;
            flex-direction: column;
            height: 100vh; /* Puri mobile screen */
        }

        /* 1. Fix Header */
        .header {
            height: 60px;
            background: #6a11cb;
            color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            flex-shrink: 0;
            z-index: 10;
        }

        /* 2. Chat Box (Sirf yeh area scroll hoga) */
        #chat-screen {
            flex: 1;
            overflow-y: auto;
            padding: 15px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            background: #e5ddd5; /* Classic chat background */
            -webkit-overflow-scrolling: touch;
        }

        .msg {
            max-width: 80%;
            padding: 10px;
            border-radius: 10px;
            font-size: 15px;
            line-height: 1.4;
            box-shadow: 0 1px 2px rgba(0,0,0,0.1);
        }
        .mine { align-self: flex-end; background: #dcf8c6; }
        .others { align-self: flex-start; background: white; }

        /* 3. Input Area (Nichy Fix rahega) */
        .input-bar {
            height: 70px;
            background: white;
            border-top: 1px solid #ccc;
            display: flex;
            align-items: center;
            padding: 0 10px;
            flex-shrink: 0;
            z-index: 10;
        }

        #messageInput {
            flex: 1;
            padding: 12px 15px;
            border: 1px solid #ddd;
            border-radius: 25px;
            outline: none;
            font-size: 16px;
        }

        .send-btn {
            background: #6a11cb;
            color: white;
            border: none;
            width: 45px;
            height: 45px;
            border-radius: 50%;
            margin-left: 10px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
        }
    </style>
</head>
<body>

    <div class="app-container">
        <div class="header">LIVE CONNECT</div>

        <div id="chat-screen">
            </div>

        <div class="input-bar">
            <input type="text" id="messageInput" placeholder="Type message, sweetie...">
            <button class="send-btn" onclick="sendMsg()">➔</button>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        // Aapka Firebase Config
        const firebaseConfig = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const userId = "User_" + Math.floor(Math.random() * 1000);

        // Message bhejne ka function
        window.sendMsg = () => {
            const input = document.getElementById('messageInput');
            if (!input.value.trim()) return;
            
            push(ref(db, 'messages/global'), {
                user: userId,
                text: input.value,
                timestamp: serverTimestamp()
            });
            input.value = "";
        };

        // Messages display karne aur auto-scroll ke liye
        const chatScreen = document.getElementById('chat-screen');
        onChildAdded(ref(db, 'messages/global'), (data) => {
            const msg = data.val();
            const isMe = msg.user === userId;
            const div = document.createElement('div');
            div.className = `msg ${isMe ? 'mine' : 'others'}`;
            div.innerText = msg.text;
            chatScreen.appendChild(div);
            
            // Auto scroll to bottom
            chatScreen.scrollTop = chatScreen.scrollHeight;
        });

        // Enter key support
        document.getElementById('messageInput').onkeypress = (e) => {
            if (e.key === 'Enter') sendMsg();
        };
    </script>
</body>
</html>
