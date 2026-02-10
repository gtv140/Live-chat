<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Connect 🚀</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
/* ================= GLOBAL STYLES ================= */
:root {
  --bg: #f3f4f6;
  --card: #fff;
  --pri: #22c55e;
  --txt: #111;
  --muted: #6b7280;
}
body.dark {
  --bg: #0f172a;
  --card: #1e293b;
  --pri: #10b981;
  --txt: #e5e7eb;
  --muted: #9ca3af;
}
body {
  margin: 0;
  font-family: system-ui, sans-serif;
  background: var(--bg);
  color: var(--txt);
  font-size: 16px;
}
header {
  height: 56px;
  background: var(--pri);
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 16px;
  font-size: 20px;
  font-weight: 700;
  position: sticky;
  top: 0;
  z-index: 1000;
}
.container {
  padding: 12px;
  max-width: 700px;
  margin: auto;
  padding-bottom: 90px; /* for nav */
}
.page { display: none; }
.page.active { display: block; }
.card {
  background: var(--card);
  border-radius: 12px;
  padding: 14px;
  margin-bottom: 12px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.08);
}
button {
  background: var(--pri);
  border: none;
  color: #fff;
  padding: 12px;
  margin-top: 6px;
  width: 100%;
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
}
input, textarea {
  width: 100%;
  padding: 12px;
  border-radius: 8px;
  border: 1px solid #ccc;
  margin-bottom: 8px;
  font-size: 15px;
}
.user {
  padding: 10px 14px;
  background: #e5e7eb;
  border-radius: 20px;
  margin: 4px;
  display: inline-flex;
  align-items: center;
  gap: 6px;
  cursor: pointer;
}
.user::before { content: "🟢"; }
.list { display: flex; overflow-x: auto; padding: 4px 0; }
.msg {
  padding: 10px;
  margin: 6px 0;
  border-radius: 12px;
  max-width: 80%;
}
.me { background: #dcfce7; margin-left: auto; }
.other { background: var(--card); }
.status-post {
  border-bottom: 1px solid #ddd;
  padding: 10px 0;
}
.status-post p { margin: 6px 0; }
.actions { display: flex; gap: 14px; margin-top: 6px; font-size: 18px; cursor: pointer; }
.bottom-nav {
  position: fixed;
  bottom: 0;
  width: 100%;
  background: var(--pri);
  display: flex;
  justify-content: space-around;
  align-items: center;
  height: 72px;
  color: #fff;
}
.bottom-nav i { font-size: 28px; cursor: pointer; }
.active-nav { color: #fff; opacity: 1; }
.socials { display: flex; justify-content: center; gap: 24px; margin-top: 10px; }
.socials i { font-size: 26px; color: var(--pri); cursor: pointer; }
@media (max-width: 480px) {
  body { font-size: 18px; }
  .bottom-nav i { font-size: 30px; }
}
</style>
</head>
<body>

<header>
  <span>Live Connect 🚀</span>
  <i class="fa fa-moon" onclick="document.body.classList.toggle('dark')"></i>
</header>

<div class="container">

<!-- LOGIN -->
<div id="login" class="page active card">
  <h3>Enter Username</h3>
  <input id="uname" placeholder="Username">
  <button onclick="login()">Continue</button>
</div>

<!-- HOME -->
<div id="home" class="page">
  <div class="card">
    <h3>🟢 Online Users</h3>
    <div id="onlineList" class="list"></div>
  </div>
</div>

<!-- CHAT -->
<div id="chat" class="page card">
  <h3>💬 Chat</h3>
  <div id="chatUsers" class="list"></div>
  <div id="chatBox" style="height:260px; overflow:auto;"></div>
  <input id="chatMsg" placeholder="Type a message...">
  <button onclick="sendChat()">Send</button>
</div>

<!-- STATUS -->
<div id="status" class="page">
  <div class="card">
    <h3>📣 Post Status</h3>
    <textarea id="statusText" placeholder="What's on your mind?"></textarea>
    <button onclick="postStatus()">Post</button>
  </div>
  <div id="statusFeed"></div>
</div>

<!-- PROFILE -->
<div id="profile" class="page card">
  <h3>👤 Profile</h3>
  <input type="file" id="profileImg">
  <button onclick="uploadProfile()">Upload Avatar</button>
  <textarea id="myStatus" placeholder="Your status..."></textarea>
  <button onclick="updateMyStatus()">Update</button>
</div>

<!-- ABOUT -->
<div id="about" class="page card">
  <h3>ℹ️ About</h3>
  <p>Live Connect — modern chat & social app.</p>
  <div class="socials">
    <i class="fab fa-facebook" onclick="window.open('https://www.facebook.com/profile.php?id=100084218946114','_blank')"></i>
    <i class="fab fa-instagram" onclick="window.open('https://www.instagram.com/mr_nazim073','_blank')"></i>
    <i class="fab fa-youtube" onclick="window.open('https://youtube.com/@crazykhantv','_blank')"></i>
  </div>
</div>

<!-- CONTACT -->
<div id="contact" class="page card">
  <h3>✉️ Contact Us</h3>
  <p>Email: <b>webhub262@gmail.com</b></p>
</div>

</div>

<div class="bottom-nav">
  <i class="fa fa-house active-nav" onclick="switchPage('home',this)"></i>
  <i class="fa fa-comment" onclick="switchPage('chat',this)"></i>
  <i class="fa fa-bell" onclick="switchPage('status',this)"></i>
  <i class="fa fa-user" onclick="switchPage('profile',this)"></i>
  <i class="fa fa-circle-info" onclick="switchPage('about',this)"></i>
  <i class="fa fa-envelope" onclick="switchPage('contact',this)"></i>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import { getDatabase, ref, set, push, onValue, update, remove } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";
import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/12.9.0/firebase-storage.js";

// ============ FIREBASE CONFIG ============
const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.appspot.com",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
const st = getStorage(app);

let me = "", chatWith = "";

// ============ NAVIGATION ============
window.switchPage = (page, el) => {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(page).classList.add('active');
  document.querySelectorAll('.bottom-nav i').forEach(i=>i.classList.remove('active-nav'));
  el.classList.add('active-nav');
};

// ============ LOGIN ============
window.login = ()=> {
  const u = uname.value.trim();
  if(!u) return;
  me = u;
  set(ref(db,"users/"+me),{online:true,lastSeen:Date.now()});
  switchPage('home', document.querySelector('.bottom-nav i'));
};

// ============ ONLINE USERS ============
onValue(ref(db,"users"),snap=>{
  onlineList.innerHTML="";
  chatUsers.innerHTML="";
  snap.forEach(u=>{
    if(!u.val().online) return; // hide offline
    const d = document.createElement("div");
    d.className="user";
    d.textContent = u.key + (u.val().status? " — "+u.val().status:"");
    d.onclick = ()=>openChat(u.key);
    onlineList.appendChild(d.cloneNode(true));
    if(u.key!==me) chatUsers.appendChild(d);
  });
});

// ============ CHAT ============
function openChat(u){
  chatWith = u;
  onValue(ref(db,"chats/"+[me,u].sort().join("_")),snap=>{
    chatBox.innerHTML="";
    snap.forEach(m=>{
      const div = document.createElement("div");
      div.className = "msg "+(m.val().from===me? "me":"other");
      div.textContent = m.val().text;
      chatBox.appendChild(div);
    });
  });
}
window.sendChat = ()=>{
  if(!chatWith||!chatMsg.value) return;
  push(ref(db,"chats/"+[me,chatWith].sort().join("_")),{from:me,text:chatMsg.value});
  chatMsg.value="";
};

// ============ STATUS POSTS ============
window.postStatus = ()=>{
  if(!statusText.value) return;
  push(ref(db,"status"),{by:me,text:statusText.value,likes:0});
  statusText.value="";
};
onValue(ref(db,"status"),snap=>{
  statusFeed.innerHTML="";
  snap.forEach(s=>{
    const c = document.createElement("div");
    c.className="status-post card";
    c.innerHTML = `
      <b>${s.val().by}</b>
      <p>${s.val().text}</p>
      <div class="actions">
        <i class="fa fa-heart" onclick="likeStatus('${s.key}',${s.val().likes||0})"></i>
        <span>${s.val().likes||0}</span>
      </div>
    `;
    statusFeed.appendChild(c);
  });
});
window.likeStatus=(id,count)=>update(ref(db,"status/"+id),{likes:count+1});

// ============ PROFILE ============
window.uploadProfile=async()=>{
  const f = profileImg.files[0]; if(!f) return;
  const r = sRef(st,"avatars/"+me+"_"+Date.now()+f.name);
  await uploadBytes(r,f);
  const url = await getDownloadURL(r);
  set(ref(db,"users/"+me+"/avatar"),url);
};
window.updateMyStatus=()=>{update(ref(db,"users/"+me),{status:myStatus.value});};

// ============ AUTO OFFLINE ============
window.addEventListener("beforeunload",()=>{
  if(me) update(ref(db,"users/"+me),{online:false,lastSeen:Date.now()});
});
</script>
</body>
</html>
