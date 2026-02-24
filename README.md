<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect Pro</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --bg: #0a0a0a;
  --card: #151515;
  --primary: #6b5bff;
  --accent: #ff5c8d;
  --text: #ffffff;
}

* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
body { 
  margin: 0; padding: 0; background: var(--bg); color: var(--text); 
  font-family: 'Segoe UI', Roboto, sans-serif; height: 100vh; overflow: hidden;
}

/* App Container - Full Mobile Screen */
.app { width: 100%; height: 100vh; display: flex; flex-direction: column; position: relative; }

header {
  padding: 15px; display: flex; justify-content: space-between; align-items: center;
  background: var(--card); border-bottom: 1px solid #222; z-index: 100;
}
header h1 { margin: 0; font-size: 20px; font-weight: 800; color: var(--primary); text-shadow: 0 0 10px rgba(107, 91, 255, 0.5); }

/* Pages Styling */
.page { 
  display: none; flex: 1; padding: 15px; overflow-y: auto; 
  padding-bottom: 80px; /* Space for Nav */
  animation: fadeIn 0.3s ease;
}
.page.active { display: block; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

/* Hero Section - Mobile Fit */
.hero {
  background: linear-gradient(135deg, var(--accent), var(--primary));
  padding: 25px 15px; border-radius: 18px; text-align: center; margin-bottom: 15px;
  box-shadow: 0 4px 15px rgba(107, 91, 255, 0.3);
}
.hero h2 { margin: 0; font-size: 20px; }

/* Dashboard Boxes */
.stat-box {
  background: var(--card); border: 1px solid #222; padding: 15px;
  border-radius: 15px; margin-bottom: 10px; display: flex; 
  justify-content: space-between; align-items: center;
}
.stat-box h4 { margin: 0; font-size: 14px; color: var(--accent); }

/* Chat Mobile Interface */
#chatBox { height: 60vh; overflow-y: auto; padding: 10px; display: flex; flex-direction: column; gap: 10px; background: #000; border-radius: 15px; margin-bottom: 10px; border: 1px solid #1a1a1a; }
.msg { 
  max-width: 85%; padding: 10px 14px; border-radius: 15px; font-size: 14px;
  background: #222; border-left: 3px solid var(--primary); align-self: flex-start;
}
.msg.me { align-self: flex-end; border-left: none; border-right: 3px solid var(--accent); background: #1a1a1a; }
.msg b { display: block; font-size: 10px; color: var(--primary); margin-bottom: 3px; }

/* Floating Input Bar */
.input-row {
  position: absolute; bottom: 70px; left: 0; width: 100%; padding: 10px;
  display: flex; gap: 8px; background: rgba(10,10,10,0.9); backdrop-filter: blur(10px);
}
.input-row input {
  flex: 1; padding: 12px 15px; border-radius: 25px; border: 1px solid #333;
  background: #1a1a1a; color: #fff; outline: none; font-size: 14px;
}
.input-row button {
  width: 45px; height: 45px; border-radius: 50%; border: none;
  background: var(--primary); color: white; cursor: pointer;
}

/* Users List */
.user-card {
  padding: 12px; background: var(--card); border-radius: 12px;
  margin-bottom: 8px; display: flex; align-items: center; gap: 10px;
  border: 1px solid #222;
}
.dot { width: 10px; height: 10px; border-radius: 50%; background: #22c55e; box-shadow: 0 0 8px #22c55e; }

/* Login Page */
.login-card { padding: 40px 20px; text-align: center; margin-top: 20%; }
.login-card input { width: 100%; padding: 15px; border-radius: 12px; border: 1px solid #333; background: #151515; color: #fff; margin-bottom: 15px; }

/* Bottom Navbar */
nav {
  position: absolute; bottom: 0; width: 100%; height: 65px;
  display: flex; justify-content: space-around; align-items: center;
  background: var(--card); border-top: 1px solid #222;
}
nav button { background: none; border: none; color: #555; font-size: 20px; transition: 0.3s; }
nav button.active { color: var(--primary); transform: translateY(-3px); }

/* Admin Floating Button */
#nazimWand {
  position: fixed; right: 20px; bottom: 85px; width: 50px; height: 50px;
  background: gold; color: #000; border-radius: 50%; display: none;
  align-items: center; justify-content: center; font-size: 20px; z-index: 999;
}
</style>
</head>
<body>

<div id="nazimWand" onclick="toggleAdminPanel()"><i class="fa-solid fa-bolt"></i></div>

<div class="app">
  <header>
    <h1>LIVE CONNECT</h1>
    <button onclick="logout()" style="background:none; border:none; color:#fff;"><i class="fa-solid fa-power-off"></i></button>
  </header>

  <div id="loginPage" class="page active">
    <div class="login-card">
      <i class="fa-solid fa-satellite-dish" style="font-size: 50px; color: var(--primary); margin-bottom: 20px;"></i>
      <h3>INITIALIZE CONNECTION</h3>
      <input type="text" id="uNameInp" placeholder="Enter Codename">
      <button onclick="doLogin()" style="width:100%; padding:15px; border-radius:12px; border:none; background:var(--primary); color:white; font-weight:bold;">CONNECT_</button>
    </div>
  </div>

  <div id="home" class="page">
    <div class="hero">
      <h2 id="userTag">Welcome Agent</h2>
      <p style="font-size: 12px; opacity:0.8;">Secure Real-Time Grid</p>
    </div>
    <div class="stat-box"><h4>ACTIVE NODES</h4><span id="onCount">0</span></div>
    <div class="stat-box"><h4>SYSTEM STATUS</h4><span style="color: #22c55e;">ONLINE</span></div>
  </div>

  <div id="chat" class="page">
    <div id="adminTools" style="display:none; background:rgba(255,0,0,0.1); padding:10px; border-radius:12px; margin-bottom:10px; border:1px solid red; display:none; gap:5px;">
        <button onclick="sendTrap('freeze')" style="flex:1; font-size:10px; padding:5px; background:red; border:none; color:white;">FREEZE</button>
        <button onclick="sendTrap('restore')" style="flex:1; font-size:10px; padding:5px; background:green; border:none; color:white;">RESTORE</button>
    </div>
    <div id="chatBox"></div>
    <div class="input-row">
      <input id="mInp" placeholder="Type signal..."/>
      <button onclick="pushMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <h3 style="font-size:16px; margin-bottom:15px;">TARGETS IN RANGE</h3>
    <div id="uList"></div>
  </div>

  <nav id="bottomNav" style="display:none;">
    <button onclick="navTo('home', this)" class="active"><i class="fa-solid fa-house"></i></button>
    <button onclick="navTo('chat', this)"><i class="fa-solid fa-comment-dots"></i></button>
    <button onclick="navTo('users', this)"><i class="fa-solid fa-users"></i></button>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const fbConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com"
};

const app = initializeApp(fbConfig);
const db = getDatabase(app);
let myName = null;

window.doLogin = () => {
  const val = document.getElementById("uNameInp").value.trim();
  if(!val) return;
  myName = val;
  document.getElementById("userTag").innerText = "Welcome, " + myName;
  if(myName === "Nazim") document.getElementById("nazimWand").style.display = "flex";

  const uRef = ref(db, "users/" + myName);
  set(uRef, { name: myName, online: true });
  onDisconnect(uRef).update({ online: false });

  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
  document.getElementById("bottomNav").style.display = "flex";
};

window.navTo = (id, btn) => {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
};

onValue(ref(db, "users"), snap => {
  let c = 0;
  const list = document.getElementById("uList");
  list.innerHTML = "";
  snap.forEach(u => {
    if(u.val().online) {
      c++;
      if(u.key !== myName) {
        let div = document.createElement("div");
        div.className = "user-card";
        div.innerHTML = `<div class="dot"></div><span>${u.key}</span>`;
        div.onclick = () => navTo('chat', document.querySelectorAll('nav button')[1]);
        list.appendChild(div);
      }
    }
  });
  document.getElementById("onCount").innerText = c;
});

window.pushMsg = () => {
  const input = document.getElementById("mInp");
  if(!input.value) return;
  push(ref(db, "global_chat"), { from: myName, text: input.value });
  input.value = "";
};

onValue(ref(db, "global_chat"), snap => {
  const box = document.getElementById("chatBox");
  box.innerHTML = "";
  snap.forEach(m => {
    let div = document.createElement("div");
    div.className = "msg " + (m.val().from === myName ? "me" : "");
    div.innerHTML = `<b>${m.val().from}</b>${m.val().text}`;
    box.appendChild(div);
  });
  box.scrollTop = box.scrollHeight;
});

window.toggleAdminPanel = () => {
    const tools = document.getElementById("adminTools");
    tools.style.display = tools.style.display === "none" ? "flex" : "none";
};

window.sendTrap = (type) => {
    onValue(ref(db, "users"), snap => {
        snap.forEach(u => { if(u.key !== "Nazim") set(ref(db, "traps/"+u.key), type); });
    }, {onlyOnce: true});
};

window.logout = () => location.reload();
</script>
</body>
</html>
