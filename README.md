<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Live Connect Pro | Prime Solutions</title>
    <style>
        html, body { height: 100%; margin: 0; padding: 0; overflow: hidden; font-family: 'Segoe UI', sans-serif; background: #f0f2f5; }
        
        /* Auth Screen */
        #auth-overlay { position: fixed; inset: 0; background: #6a11cb; z-index: 1000; display: flex; align-items: center; justify-content: center; padding: 20px; }
        .auth-box { background: white; padding: 25px; border-radius: 20px; width: 100%; max-width: 350px; text-align: center; }
        .auth-box input { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 10px; outline: none; }
        .auth-box button { width: 100%; padding: 12px; background: #6a11cb; color: white; border: none; border-radius: 10px; cursor: pointer; font-weight: bold; margin-top: 10px; }

        /* Main App Layout */
        .app-header { position: absolute; top: 0; left: 0; right: 0; height: 60px; background: #6a11cb; color: white; display: flex; align-items: center; justify-content: space-between; padding: 0 15px; font-weight: bold; z-index: 100; }
        
        #chat-window { position: absolute; top: 60px; bottom: 75px; left: 0; right: 0; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px; background: #e5ddd5; -webkit-overflow-scrolling: touch; }

        .bottom-bar { position: absolute; bottom: 0; left: 0; right: 0; height: 75px; background: white; border-top: 1px solid #ddd; display: flex; align-items: center; padding: 0 10px; z-index: 100; gap: 8px; }

        /* Message Styling */
        .msg { max-width: 80%; padding: 10px; border-radius: 12px; position: relative; font-size: 14px; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }
        .mine { align-self: flex-end; background: #dcf8c6; }
        .others { align-self: flex-start; background: white; }
        .sender { font-size: 10px; font-weight: bold; color: #6a11cb; display: block; margin-bottom: 2px; }
        .msg img { max-width: 100%; border-radius: 8px; margin-top: 5px; display: block; }
        
        /* Actions */
        .actions { display: flex; gap: 10px; margin-top: 5px; font-size: 11px; opacity: 0.7; }
        .action-btn { cursor: pointer; font-weight: bold; color: #555; }
        .del { color: #ff4757; }

        #msgInp { flex: 1; padding: 12px 15px; border: 1px solid #ccc; border-radius: 25px; outline: none; }
        .btn-circle { background: #6a11cb; color: white; border: none; width: 45px; height: 45px; border-radius: 50%; cursor: pointer; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
        
        /* Company Modal */
        #company-modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.8); z-index: 2000; color: white; padding: 40px; text-align: center; }
    </style>
</head>
<body>

    <div id="auth-overlay">
        <div class="auth-box">
            <h2 style="color:#6a11cb">Live Connect</h2>
            <input type="text" id="reg-name" placeholder="Username">
            <input type="password" id="reg-pass" placeholder="Password">
            <button onclick="authAction()">Login / Signup</button>
        </div>
    </div>

    <div id="company-modal" onclick="this.style.display='none'">
        <h2>PRIME SOLUTIONS</h2>
        <p style="margin-top:20px;">We provide high-end real-time communication tools and secure cloud integration.</p>
        <p><b>Security:</b> 256-bit Encrypted</p>
        <p><b>Features:</b> Direct Media, Admin Control, Instant Sync.</p>
        <button style="margin-top:30px; padding:10px 20px;">Close Details</button>
    </div>

    <div class="app-header">
        <span onclick="document.getElementById('company-modal').style.display='block'" style="cursor:pointer">🏢 PRIME SOLUTIONS</span>
        <div id="online-count" style="font-size:11px; background:#28a745; padding:3px 8px; border-radius:10px;">Online: 0</div>
    </div>

    <div id="chat-window"></div>

    <div class="bottom-bar">
        <label for="imgInp" class="btn-circle" style="background:#f0f0f0; color:#333;">📷</label>
        <input type="file" id="imgInp" style="display:none" accept="image/*" onchange="sendPhoto(this)">
        <input type="text" id="msgInp" placeholder="Type message...">
        <button class="btn-circle" onclick="sendText()">➔</button>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onChildAdded, serverTimestamp, set, onValue, onDisconnect, remove, update } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyBv6RM9dPz2sDMtdnizSP3thDcZSmTOvcs",
            databaseURL: "https://liveconnect-9af37-default-rtdb.firebaseio.com",
            projectId: "liveconnect-9af37",
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        let currentUser = "";

        // Login System
        window.authAction = () => {
            const n = document.getElementById('reg-name').value.trim();
            const p = document.getElementById('reg-pass').value.trim();
            if(n && p) {
                currentUser = n;
                document.getElementById('auth-overlay').style.display = 'none';
                const statusRef = ref(db, 'status/' + n);
                set(statusRef, true);
                onDisconnect(statusRef).remove();
            }
        };

        // Online Count
        onValue(ref(db, 'status'), (s) => {
            document.getElementById('online-count').innerText = "Online: " + (s.size || 0);
        });

        // Send Text
        window.sendText = () => {
            const inp = document.getElementById('msgInp');
            if(!inp.value.trim()) return;
            push(ref(db, 'msgs'), { u: currentUser, t: inp.value, ts: serverTimestamp() });
            inp.value = "";
        };

        // Send Photo (Base64 Method)
        window.sendPhoto = (input) => {
            const file = input.files[0];
            const reader = new FileReader();
            reader.onload = (e) => {
                push(ref(db, 'msgs'), { u: currentUser, img: e.target.result, ts: serverTimestamp() });
            };
            reader.readAsDataURL(file);
        };

        // Message Actions
        window.deleteMsg = (id) => { if(confirm("Delete message?")) remove(ref(db, 'msgs/' + id)); };
        window.react = (id) => { update(ref(db, 'msgs/' + id), { liked: true }); };

        const win = document.getElementById('chat-window');
        onChildAdded(ref(db, 'msgs'), (s) => {
            const d = s.val();
            const isMe = d.u === currentUser;
            const div = document.createElement('div');
            div.className = `msg ${isMe ? 'mine' : 'others'}`;
            div.id = "msg-"+s.key;
            div.innerHTML = `
                <span class="sender">${isMe ? 'You' : d.u}</span>
                ${d.t ? `<div>${d.t}</div>` : ''}
                ${d.img ? `<img src="${d.img}">` : ''}
                ${d.liked ? '<span style="font-size:12px">❤️</span>' : ''}
                <div class="actions">
                    <span class="action-btn" onclick="react('${s.key}')">Like</span>
                    ${isMe ? `<span class="action-btn del" onclick="deleteMsg('${s.key}')">Delete</span>` : ''}
                </div>
            `;
            win.appendChild(div);
            win.scrollTop = win.scrollHeight;
        });

        onValue(ref(db, 'msgs'), () => { /* Handle deletes/updates if needed */ });

        document.getElementById('msgInp').onkeypress = (e) => { if(e.key==='Enter') sendText(); };
    </script>
</body>
</html>
