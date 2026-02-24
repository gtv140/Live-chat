<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Live Connect Pro</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
  --bg: #f4f7f6;
  --white: #ffffff;
  --primary: #6b5bff;
  --text: #333333;
  --gray: #888888;
  --shadow: 0 4px 12px rgba(0,0,0,0.05);
}

* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
body { 
  margin: 0; padding: 0; background: var(--bg); color: var(--text); 
  font-family: 'Inter', -apple-system, sans-serif; height: 100vh; overflow: hidden;
}

/* Header */
header {
  padding: 15px 20px; background: var(--white); display: flex;
  justify-content: space-between; align-items: center; border-bottom: 1px solid #eee;
}
header h1 { margin: 0; font-size: 18px; font-weight: 700; color: var(--primary); }

/* Main Container */
.app { height: 100vh; display: flex; flex-direction: column; width: 100%; max-width: 480px; margin: 0 auto; background: var(--bg); position: relative; }

/* Page Content */
.page { display: none; flex: 1; padding: 20px; overflow-y: auto; padding-bottom: 90px; }
.page.active { display: block; animation: fadeIn 0.3s ease; }
@keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

/* Login UI */
.login-box { text-align: center; margin-top: 40%; background: var(--white); padding: 30px; border-radius: 20px; box-shadow: var(--shadow); }
.login-box input { width: 100%; padding: 15px; border-radius: 12px; border: 1px solid #ddd; margin-bottom: 15px; font-size: 16px; outline: none; }
.btn-login { width: 100%; padding: 15px; border-radius: 12px; border: none; background: var(--primary); color: white; font-weight: 700; cursor: pointer; }

/* Dashboard Grid Boxes */
.grid-container { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 10px; }
.dash-box {
  background: var(--white); padding: 20px; border-radius: 20px;
  box-shadow: var(--shadow); text-align: center; cursor: pointer;
  transition: transform 0.2s;
}
.dash-box:active { transform: scale(0.95); }
.dash-box i { font-size: 28px; color: var(--primary); margin-bottom: 12px; display: block; }
.dash-box span { font-size: 14px; font-weight: 600; color: var(--text); }
.dash-box small { display: block; font-size: 11px; color: var(--gray); margin-top: 4px; }

/* Chat Box UI */
#chatBox { background: #fff; height: 65vh; border-radius: 20px; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 10px; margin-bottom: 10px; box-shadow: var(--shadow); }
.msg { max-width: 80%; padding: 10px 15px; border-radius: 15px; font-size: 14px; line-height: 1.4; }
.msg.me { align-self: flex-end; background: var(--primary); color: white; border-bottom-right-radius: 4px; }
.msg.other { align-self: flex-start; background: #eee; color: #333; border-bottom-left-radius: 4px; }

/* Input Bar */
.input-area { display: flex; gap: 10px; background: var(--white); padding: 10px; border-radius: 50px; box-shadow: var(--shadow); position: absolute; bottom: 85px; width: calc(100% - 40px); left: 20px; }
.input-area input { flex: 1; border: none; outline: none; padding-left: 10px; font-size: 14px; }
.btn-send { background: var(--primary); color: white; width: 40px; height: 40px; border-radius: 50%; border: none; cursor: pointer; }

/* Bottom Navbar */
nav {
  position: absolute; bottom: 0; width: 100%; height: 70px;
  background: var(--white); display: flex; justify-content: space-around; align-items: center;
  border-top: 1px solid #eee; z-index: 100;
}
nav button { background: none; border: none; color: var(--gray); font-size: 20px; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 4px; }
nav button.active { color: var(--primary); }
nav button span { font-size: 10px; font-weight: 600; }

/* Admin Panel Popup */
#adminModal { display: none; position: fixed; bottom: 100px; left: 20px; right: 20px; background: white; padding: 20px; border-radius: 20px; box-shadow: 0 0 20px rgba(0,0,0,0.2); z-index: 1000; border: 1px solid #eee; }
.admin-btn { padding: 10px; border-radius: 10px; border: 1px solid #eee; font-size: 12px; cursor: pointer; background: #f9f9f9; }
</style>
</head>
<body>

<div class="app">
  <header>
    <h1>LIVE CONNECT</h1>
    <i class="fa-solid fa-power-off" onclick="logout()" style="color:red; cursor:pointer;"></i>
  </header>

  <div id="loginPage" class="page active">
    <div class="login-box">
      <i class="fa-solid fa-circle-user" style="font-size: 50px; color: var(--primary); margin-bottom: 15px;"></i>
      <h3>Sign In</h3>
      <input type="text" id="uNameInp" placeholder="Enter your name">
      <button class="btn-login" onclick="doLogin()">CONTINUE</button>
    </div>
  </div>

  <div id="home" class="page">
    <div style="margin-bottom: 20px;">
      <h2 id="greet" style="margin:0;">Hi, Agent</h2>
      <p style="color:var(--gray); font-size: 13px;">Everything is under control.</p>
    </div>

    <div class="grid-container">
      <div class="dash-box" onclick="openPage('chat', document.getElementById('chatBtn'))">
        <i class="fa-solid fa-comments"></i>
        <span>Global Chat</span>
        <small>Real-time group</small>
      </div>
      <div class="dash-box" onclick="openPage('users', document.getElementById('userBtn'))">
        <i class="fa-solid fa-users"></i>
        <span>Active Users</span>
        <small id="userCount">0 online</small>
      </div>
      <div class="dash-box" id="adminBox" style="display:none;" onclick="toggleAdmin()">
        <i class="fa-solid fa-wand-magic-sparkles" style="color:gold"></i>
        <span>Admin Tools</span>
        <small>Secret Access</small>
      </div>
      <div class="dash-box">
        <i class="fa-solid fa-shield-check" style="color:limegreen"></i>
        <span>Security</span>
        <small>Encrypted</small>
      </div>
    </div>
  </div>

  <div id="chat" class="page">
    <div id="chatBox"></div>
    <div class="input-area">
      <input type="text" id="mInp" placeholder="Write something...">
      <button class="btn-send" onclick="pushMsg()"><i class="fa-solid fa-paper-plane"></i></button>
    </div>
  </div>

  <div id="users" class="page">
    <h3 style="margin-top:0;">Nearby Signals</h3>
    <div id="uList"></div>
  </div>

  <div id="adminModal">
    <h4 style="margin:0 0 10px 0;">Hacker Controls</h4>
    <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px;">
      <button class="admin-btn" onclick="sendTrap('freeze')" style="color:red;">FREEZE USERS</button>
      <button class="admin-btn" onclick="sendTrap('restore')" style="color:green;">RESTORE ALL</button>
    </div>
  </div>

  <nav id="navbar" style="display:none;">
    <button onclick="openPage('home', this)" class="active"><i class="fa-solid fa-house-user"></i><span>Home</span></button>
    <button id="chatBtn" onclick="openPage('chat', this)"><i class="fa-solid fa-message-dots"></i><span>Chat</span></button>
    <button id="userBtn" onclick="openPage('users', this)"><i class="fa-solid fa-ghost"></i><span>Users</span></button>
  </nav>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, remove, onDisconnect } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";

const fbConfig = { apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE", databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com" };
const app = initializeApp(fbConfig);
const db = getDatabase(app);
let user = null;

window.doLogin = () => {
  const val = document.getElementById("uNameInp").value.trim();
  if(!val) return;
  user = val;
  document.getElementById("greet").innerText = "Hi, " + user;
  if(user === "Nazim") document.getElementById("adminBox").style.display = "block";

  const uRef = ref(db, "users/" + user);
  set(uRef, { online: true });
  onDisconnect(uRef).update({ online: false });

  document.getElementById("loginPage").classList.remove("active");
  document.getElementById("home").classList.add("active");
  document.getElementById("navbar").style.display = "flex";
};

window.openPage = (id, btn) => {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
  document.getElementById("adminModal").style.display = "none";
};

onValue(ref(db, "users"), snap => {
  let c = 0; const list = document.getElementById("uList"); list.innerHTML = "";
  snap.forEach(u => {
    if(u.val().online) {
      c++;
      if(u.key !== user) {
        let d = document.createElement("div"); d.style = "background:white; padding:15px; border-radius:15px; margin-bottom:10px; display:flex; align-items:center; gap:10px; box-shadow:var(--shadow);";
        d.innerHTML = `<i class="fa-solid fa-circle" style="color:limegreen; font-size:10px;"></i> <b>${u.key}</b>`;
        list.appendChild(d);
      }
    }
  });
  document.getElementById("userCount").innerText = c + " online";
});

window.pushMsg = () => {
  const input = document.getElementById("mInp"); if(!input.value) return;
  push(ref(db, "chat_v2"), { from: user, text: input.value });
  input.value = "";
};

onValue(ref(db, "chat_v2"), snap => {
  const box = document.getElementById("chatBox"); box.innerHTML = "";
  snap.forEach(m => {
    let div = document.createElement("div"); div.className = "msg " + (m.val().from === user ? "me" : "other");
    div.innerHTML = `<small style="display:block; font-weight:bold; font-size:10px; margin-bottom:2px;">${m.val().from}</small>${m.val().text}`;
    box.appendChild(div);
  });
  box.scrollTop = box.scrollHeight;
});

window.toggleAdmin = () => {
  const m = document.getElementById("adminModal");
  m.style.display = m.style.display === "none" ? "block" : "none";
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
