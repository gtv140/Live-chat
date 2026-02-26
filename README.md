<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Live Connect Modern 🚀</title>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

<style>
:root { 
    --bg:#030508; 
    --card:#0d1117; 
    --primary:#00d2ff; 
    --accent:#ff0055; 
    --text:#e6edf3; 
    --glass:rgba(13,17,23,.9);
    --grad:linear-gradient(135deg,#00d2ff,#3a7bd5);
}

*{box-sizing:border-box;-webkit-tap-highlight-color:transparent;outline:none;}

body{
    margin:0;
    font-family:'Segoe UI',Roboto,sans-serif;
    background:var(--bg);
    color:var(--text);
    min-height:100dvh;   /* FIXED */
    display:flex;
    flex-direction:column;
}

/* HEADER FIX */
header{
    padding:20px 15px 10px;  /* FIXED */
    flex-shrink:0;
    background:var(--bg);
    display:flex;
    justify-content:space-between;
    align-items:center;
    border-bottom:1px solid #1a1d21;
    position:sticky;
    top:0;
    z-index:1000;
}

header h1{
    margin:0;
    font-size:20px;
    font-weight:800;
    background:var(--grad);
    -webkit-background-clip:text;
    -webkit-text-fill-color:transparent;
}

/* VIEWPORT FIX */
.viewport{
    flex:1;
    overflow-y:auto;   /* FIXED */
    position:relative;
    padding:15px;
    padding-bottom:150px; /* space for bottom nav */
}

/* PAGE SYSTEM */
.page{display:none;animation:fade .25s ease;}
.page.active{display:block;}
@keyframes fade{from{opacity:0;transform:translateY(10px)}to{opacity:1;transform:translateY(0)}}

/* SCROLL BOX */
.scroll-container{
    overflow-y:auto;
    border-radius:20px;
    border:1px solid #21262d;
    padding:10px;
    margin-bottom:15px;
}

/* CARDS */
.card{
    display:flex;
    align-items:center;
    gap:12px;
    background:var(--card);
    padding:14px;
    border-radius:18px;
    margin-bottom:8px;
    border:1px solid #30363d;
}

.card:active{
    transform:scale(.97);
    background:#1c2128;
}

/* CHAT */
#chatBox{
    display:flex;
    flex-direction:column;
    gap:12px;
}

.m-bubble{
    padding:12px 16px;
    border-radius:20px;
    max-width:80%;
    font-size:14px;
    line-height:1.5;
    word-wrap:break-word;
}

.m-bubble.me{
    align-self:flex-end;
    background:var(--grad);
    border-bottom-right-radius:4px;
}

.m-bubble.other{
    align-self:flex-start;
    background:#21262d;
    border-bottom-left-radius:4px;
}

/* BOTTOM NAV FIX */
.bottom-shelf{
    position:fixed;   /* FIXED */
    bottom:0;
    left:0;
    width:100%;
    padding:10px 15px 30px;
    background:var(--bg);
    border-top:1px solid #1a1d21;
}

/* MESSAGE INPUT */
.msg-input-wrap{
    width:92%;
    max-width:500px;
    margin:0 auto 12px;
    background:#161b22;
    padding:12px 18px;
    border-radius:30px;
    display:none;
    align-items:center;
    gap:12px;
    border:1px solid #30363d;
}

.msg-input-wrap input{
    flex:1;
    background:none;
    border:none;
    color:white;
}

/* NAV */
nav{
    width:92%;
    max-width:500px;
    margin:auto;
    height:70px;
    background:var(--glass);
    backdrop-filter:blur(20px);
    border-radius:35px;
    display:flex;
    justify-content:space-around;
    align-items:center;
    border:1px solid rgba(255,255,255,.1);
}

nav button{
    background:none;
    border:none;
    color:#484f58;
    font-size:22px;
    padding:10px;
}

nav button.active{
    color:var(--primary);
    transform:translateY(-4px);
}

/* RESPONSIVE CENTER */
@media(min-width:768px){
    .viewport{max-width:600px;margin:auto;}
    .bottom-shelf{
        max-width:600px;
        left:50%;
        transform:translateX(-50%);
        border-radius:25px 25px 0 0;
    }
}
</style>
</head>

<body>

<header>
<h1>Live Connect</h1>
<div>
<i class="fa-solid fa-power-off" style="color:var(--accent);font-size:20px;"></i>
</div>
</header>

<div class="viewport">
<div class="page active">
<div class="card">
<h2>Hello 👋</h2>
<p style="font-size:12px;opacity:.6;">System Active</p>
</div>
</div>
</div>

<div class="bottom-shelf">
<div class="msg-input-wrap">
<input placeholder="Type message...">
<i class="fa-solid fa-circle-arrow-up" style="color:var(--primary);font-size:26px;"></i>
</div>
<nav>
<button class="active"><i class="fa-solid fa-house"></i></button>
<button><i class="fa-solid fa-comments"></i></button>
<button><i class="fa-solid fa-users"></i></button>
</nav>
</div>

</body>
</html>
