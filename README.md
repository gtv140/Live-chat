<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Live Connect – Real-Time Chat</title>

<!-- Icons -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css"/>

<style>
:root{
  --bg:#f6f7fb;
  --card:#ffffff;
  --text:#111827;
  --muted:#6b7280;
  --primary:#2563eb;
}
body.dark{
  --bg:#0f172a;
  --card:#111827;
  --text:#e5e7eb;
  --muted:#9ca3af;
  --primary:#3b82f6;
}

*{box-sizing:border-box}
body{
  margin:0;
  font-family:system-ui,-apple-system,Segoe UI,Roboto;
  background:var(--bg);
  color:var(--text);
}

/* Layout */
.app{
  max-width:420px;
  margin:auto;
  min-height:100vh;
  display:flex;
  flex-direction:column;
}

/* Header */
header{
  padding:14px 16px;
  display:flex;
  justify-content:space-between;
  align-items:center;
  background:var(--card);
  box-shadow:0 2px 10px rgba(0,0,0,.06);
}
header h1{
  font-size:18px;
  margin:0;
}
header button{
  background:none;
  border:none;
  font-size:18px;
  color:var(--text);
}

/* Pages */
.page{
  display:none;
  padding:16px;
  flex:1;
}
.page.active{display:block}

/* Home */
.hero{
  background:linear-gradient(135deg,var(--primary),#60a5fa);
  color:#fff;
  padding:24px;
  border-radius:18px;
}
.hero h2{margin:0 0 8px}
.hero p{margin:0;opacity:.9}

/* Chat */
.chat-box{
  background:var(--card);
  border-radius:16px;
  padding:12px;
  height:50vh;
  overflow-y:auto;
  margin-bottom:10px;
}
.msg{
  background:#e5e7eb;
  color:#000;
  padding:8px 12px;
  border-radius:14px;
  margin-bottom:8px;
  font-size:14px;
}
body.dark .msg{
  background:#1f2937;
  color:#e5e7eb;
}
.input-row{
  display:flex;
  gap:8px;
}
.input-row input{
  flex:1;
  padding:12px;
  border-radius:12px;
  border:1px solid #ccc;
}
.input-row button{
  padding:12px 16px;
  border:none;
  border-radius:12px;
  background:var(--primary);
  color:#fff;
}

/* Online users */
.user{
  display:flex;
  align-items:center;
  gap:8px;
  margin-bottom:10px;
}
.dot{
  width:10px;height:10px;
  background:#22c55e;
  border-radius:50%;
}

/* Bottom Nav */
nav{
  display:flex;
  justify-content:space-around;
  background:var(--card);
  padding:10px 0;
  box-shadow:0 -2px 10px rgba(0,0,0,.06);
}
nav button{
  background:none;
  border:none;
  font-size:20px;
  color:var(--muted);
}
nav button.active{color:var(--primary)}
</style>
</head>

<body>
<div class="app">

<header>
  <h1>Live Connect</h1>
  <button onclick="toggleDark()"><i class="fa-solid fa-moon"></i></button>
</header>

<!-- HOME -->
<div id="home" class="page active">
  <div class="hero">
    <h2>Real-Time Live Chat</h2>
    <p>Fast • Secure • Mobile Friendly</p>
  </div>
  <p style="margin-top:16px;color:var(--muted)">
    Connect instantly, see who’s online, and chat smoothly on mobile without any mess.
  </p>
</div>

<!-- CHAT -->
<div id="chat" class="page">
  <div class="chat-box" id="chatBox">
    <div class="msg"><b>Admin:</b> Welcome to Live Connect 👋</div>
  </div>
  <div class="input-row">
    <input id="msgInput" placeholder="Type message…" />
    <button onclick="sendMsg()"><i class="fa-solid fa-paper-plane"></i></button>
  </div>
</div>

<!-- USERS -->
<div id="users" class="page">
  <h3>Online Users</h3>
  <div class="user"><div class="dot"></div> Khan</div>
  <div class="user"><div class="dot"></div> Guest123</div>
</div>

<!-- ABOUT -->
<div id="about" class="page">
  <h3>About</h3>
  <p>
    Live Connect is a modern real-time chat platform designed specially for mobile users.
  </p>
</div>

<!-- CONTACT -->
<div id="contact" class="page">
  <h3>Contact & Support</h3>
  <p><i class="fa-solid fa-envelope"></i> webhub262@gmail.com</p>
  <p>
    <a href="https://www.facebook.com/profile.php?id=100084218946114">Facebook</a> |
    <a href="https://www.instagram.com/mr_nazim073">Instagram</a> |
    <a href="https://youtube.com/@crazykhantv">YouTube</a>
  </p>
</div>

<nav>
  <button onclick="openPage('home',this)" class="active"><i class="fa-solid fa-house"></i></button>
  <button onclick="openPage('chat',this)"><i class="fa-solid fa-comments"></i></button>
  <button onclick="openPage('users',this)"><i class="fa-solid fa-user-group"></i></button>
  <button onclick="openPage('about',this)"><i class="fa-solid fa-circle-info"></i></button>
  <button onclick="openPage('contact',this)"><i class="fa-solid fa-envelope"></i></button>
</nav>

</div>

<script>
function openPage(id,btn){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
}

function toggleDark(){
  document.body.classList.toggle('dark');
}

function sendMsg(){
  const input=document.getElementById('msgInput');
  if(!input.value) return;
  const box=document.getElementById('chatBox');
  box.innerHTML+=`<div class="msg"><b>You:</b> ${input.value}</div>`;
  input.value='';
  box.scrollTop=box.scrollHeight;
}
</script>
</body>
</html>
