<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Empire-X | Admin Sovereign</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root { --primary: #8b5cf6; --secondary: #ec4899; --dark: #0f172a; --glass: rgba(30, 41, 59, 0.7); }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { 
            margin: 0; background: var(--dark); color: #f1f5f9; 
            font-family: 'Inter', sans-serif; height: 100dvh; display: flex; 
        }

        /* --- Elite Sidebar --- */
        .sidebar { 
            width: 80px; background: #020617; border-right: 1px solid rgba(255,255,255,0.05);
            display: flex; flex-direction: column; align-items: center; py: 30px; gap: 30px; 
        }
        .nav-link { 
            width: 50px; height: 50px; display: flex; align-items: center; justify-content: center;
            border-radius: 15px; color: #64748b; cursor: pointer; transition: 0.3s;
        }
        .nav-link:hover, .nav-link.active { background: var(--primary); color: white; box-shadow: 0 0 20px rgba(139, 92, 246, 0.4); }

        /* --- Dashboard Wrapper --- */
        .wrapper { flex: 1; display: flex; flex-direction: column; overflow: hidden; position: relative; }
        
        header { 
            padding: 20px 30px; display: flex; justify-content: space-between; align-items: center;
            background: rgba(15, 23, 42, 0.9); backdrop-filter: blur(10px); z-index: 10;
        }
        .user-tag { background: linear-gradient(to right, var(--primary), var(--secondary)); padding: 5px 15px; border-radius: 20px; font-weight: 800; font-size: 0.8rem; }

        /* --- Chat & Modules --- */
        .main-stage { flex: 1; display: flex; overflow: hidden; }
        .chat-view { flex: 1; display: flex; flex-direction: column; position: relative; }
        .feed { flex: 1; overflow-y: auto; padding: 25px; display: flex; flex-direction: column; gap: 15px; padding-bottom: 120px; }
        
        /* Message Bubbles */
        .bubble { max-width: 70%; padding: 14px 18px; border-radius: 22px; font-size: 14px; position: relative; animation: slideUp 0.3s ease; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } }
        .bubble.me { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 4px; }
        .bubble.other { align-self: flex-start; background: var(--glass); border: 1px solid rgba(255,255,255,0.1); border-bottom-left-radius: 4px; }

        /* Admin Action UI */
        #adminPanel { 
            display: none; position: absolute; top: 80px; right: 20px; 
            background: #ef4444; color: white; padding: 10px 20px; 
            border-radius: 12px; font-weight: 800; cursor: pointer; z-index: 100;
            box-shadow: 0 10px 20px rgba(239, 68, 68, 0.3);
        }

        /* --- Input Dock --- */
        .input-area { 
            position: absolute; bottom: 25px; left: 25px; right: 25px;
            background: rgba(30, 41, 59, 0.8); backdrop-filter: blur(20px);
            padding: 10px; border-radius: 50px; display: flex; gap: 10px;
            border: 1px solid rgba(255,255,255,0.1);
        }
        .input-area input { flex: 1; background: none; border: none; color: white; padding: 12px 20px; outline: none; font-size: 15px; }
        .action-btn { background: var(--primary); border: none; width: 45px; height: 45px; border-radius: 50%; color: white; cursor: pointer; }

        /* User List Sidebar */
        .user-list { width: 280px; background: rgba(0,0,0,0.2); padding: 25px; overflow-y: auto; border-left: 1px solid rgba(255,255,255,0.05); }
        @media (max-width: 900px) { .user-list { display: none; } }
    </style>
</head>
<body>

<nav class="sidebar">
    <div class="nav-link active"><i class="fa-solid fa-house"></i></div>
    <div class="nav-link"><i class="fa-solid fa-user-group"></i></div>
    <div class="nav-link" onclick="toggleView('settings')"><i class="fa-solid fa-sliders"></i></div>
    <div class="nav-link" onclick="toggleView('contact')"><i class="fa-solid fa-headset"></i></div>
</nav>

<div class="wrapper">
    <header>
        <div style="display: flex; flex-direction: column;">
            <span id="welcomeUser" style="font-weight: 900; font-size: 1.2rem; letter-spacing: -0.5px;">Empire_Dashboard</span>
            <small style="opacity: 0.5;">Secure Node: Active</small>
        </div>
        <div id="adminTag" class="user-tag" style="display:none">👑 SUPREME ADMIN</div>
    </header>

    <div id="adminPanel" onclick="clearSystemChat()">WIPE ALL DATA</div>

    <div class="main-stage">
        <div class="chat-view">
            <div class="feed" id="chatFeed"></div>
            
            <div class="input-area">
                <label for="pUpload" style="padding:10px; cursor:pointer; opacity:0.6"><i class="fa-solid fa-paperclip"></i></label>
                <input type="file" id="pUpload" hidden onchange="handleFile(this)">
                <input type="text" id="mInput" placeholder="Broadcast message..." onkeydown="if(event.key==='Enter') dispatch()">
                <button class="action-btn" onclick="dispatch()"><i class="fa-solid fa-location-arrow"></i></button>
            </div>
        </div>

        <aside class="user-list">
            <h3 style="margin-bottom:20px; font-size:1rem; opacity:0.6">Online Assets</h3>
            <div id="activeNodes"></div>
        </aside>
    </div>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getAuth, signInAnonymously } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
    import { getDatabase, ref, set, push, onValue, serverTimestamp, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

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
    const auth = getAuth(app);
    const db = getDatabase(app);

    let uName = prompt("Enter Empire Codename:") || "Agent_" + Math.floor(Math.random()*1000);
    let uUID;

    // Start Session
    signInAnonymously(auth).then(res => {
        uUID = res.user.uid;
        document.getElementById("welcomeUser").innerText = "Welcome, " + uName;
        
        // Admin Privileges Check (Aapka Naam Nazim ho toh)
        if(uName.toLowerCase() === 'nazim') {
            document.getElementById("adminTag").style.display = "block";
            document.getElementById("adminPanel").style.display = "block";
        }

        set(ref(db, 'online/' + uUID), { name: uName, last: serverTimestamp() });
        syncData();
    });

    window.dispatch = (img = null) => {
        const i = document.getElementById("mInput");
        if(!i.value.trim() && !img) return;
        push(ref(db, 'empire_v90'), {
            uid: uUID, sender: uName, text: i.value, img: img, ts: serverTimestamp()
        });
        i.value = "";
    };

    window.handleFile = (el) => {
        const reader = new FileReader();
        reader.onload = (e) => dispatch(e.target.result);
        reader.readAsDataURL(el.files[0]);
    };

    window.clearSystemChat = () => {
        if(confirm("Destroy all records?")) remove(ref(db, 'empire_v90'));
    };

    function syncData() {
        onValue(ref(db, 'empire_v90'), snap => {
            const f = document.getElementById("chatFeed");
            f.innerHTML = "";
            snap.forEach(s => {
                const d = s.val();
                f.innerHTML += `
                    <div class="bubble ${d.uid === uUID ? 'me' : 'other'}">
                        <small style="opacity:0.5; display:block; margin-bottom:5px;">${d.sender}</small>
                        ${d.text ? `<div>${d.text}</div>` : ''}
                        ${d.img ? `<img src="${d.img}" style="max-width:100%; border-radius:10px; margin-top:10px;">` : ''}
                    </div>
                `;
            });
            f.scrollTop = f.scrollHeight;
        });

        onValue(ref(db, 'online'), snap => {
            const list = document.getElementById("activeNodes");
            list.innerHTML = "";
            snap.forEach(s => {
                list.innerHTML += `<div style="padding:10px; background:rgba(255,255,255,0.05); border-radius:10px; margin-bottom:10px; font-size:0.9rem;">
                    <i class="fa-solid fa-circle" style="color:#10b981; font-size:8px; margin-right:10px;"></i> ${s.val().name}
                </div>`;
            });
        });
    }
</script>
</body>
</html>
