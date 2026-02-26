<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Live Connect Modern 🚀</title>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root { 
    --bg:#030508;
    --card:#0d1117;
    --primary:#00d2ff;
    --accent:#ff0055;
    --text:#e6edf3;
    --grad:linear-gradient(135deg,#00d2ff,#3a7bd5);
}

*{box-sizing:border-box;-webkit-tap-highlight-color:transparent;}
html,body{
    margin:0;
    padding:0;
    height:100%;
    background:var(--bg);
    color:var(--text);
    font-family:'Segoe UI',Roboto,sans-serif;
}

/* MAIN LAYOUT FIX */
body{
    display:flex;
    flex-direction:column;
    min-height:100dvh;
}

/* HEADER FIX */
header{
    padding:18px 15px;
    display:flex;
    justify-content:space-between;
    align-items:center;
    border-bottom:1px solid #1a1d21;
    background:var(--bg);
    position:sticky;
    top:0;
    z-index:1000;
}

header h1{
    margin:0;
    font-size:18px;
    font-weight:800;
    background:var(--grad);
    -webkit-background-clip:text;
    -webkit-text-fill-color:transparent;
}

/* VIEWPORT */
.viewport{
    flex:1;
    overflow-y:auto;
    padding:15px;
    padding-bottom:140px;
}

/* PAGE SYSTEM */
.page{display:none;animation:fade .25s ease;}
.page.active{display:block;}
@keyframes fade{from{opacity:0;transform:translateY(10px)}to{opacity:1;transform:translateY(0)}}

/* CARDS */
.card{
    background:var(--card);
    padding:14px;
    border-radius:18px;
    margin-bottom:10px;
    border:1px solid #30363d;
}

/* SCROLL BOX */
.scroll-container{
    max-height:55vh;
    overflow-y:auto;
    border-radius:18px;
}

/* CHAT */
#chatBox{
    display:flex;
    flex-direction:column;
    gap:10px;
}

.m-bubble{
    padding:12px 16px;
    border-radius:18px;
    max-width:80%;
    font-size:14px;
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

/* BOTTOM FIXED NAV */
.bottom-shelf{
    position:fixed;
    bottom:0;
    left:0;
    width:100%;
    padding:10px 15px 25px;
    background:rgba(3,5,8,0.95);
    backdrop-filter:blur(20px);
    border-top:1px solid #1a1d21;
}

/* MESSAGE INPUT */
.msg-input-wrap{
    display:none;
    background:#161b22;
    padding:12px 15px;
    border-radius:30px;
    align-items:center;
    gap:10px;
    margin-bottom:12px;
}

.msg-input-wrap input{
    flex:1;
    background:none;
    border:none;
    color:white;
    font-size:14px;
}

/* NAV BAR */
nav{
    display:flex;
    justify-content:space-around;
}

nav button{
    background:none;
    border:none;
    color:#555;
    font-size:22px;
}

nav button.active{
    color:var(--primary);
    transform:translateY(-3px);
}

/* RESPONSIVE IMPROVE */
@media(min-width:768px){
    .viewport{
        max-width:600px;
        margin:auto;
    }
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
<i class="fa-solid fa-power-off" style="color:var(--accent)"></i>
</div>
</header>

<div class="viewport">

<div id="home" class="page active">
<div class="card">
<h2>Hello 👋</h2>
<p style="font-size:12px;opacity:.6;">System Ready</p>
</div>
</div>

<div id="chat" class="page">
<div id="chatBox"></div>
</div>

</div>

<div class="bottom-shelf">

<div class="msg-input-wrap" id="chatInputArea">
<input placeholder="Type message...">
<i class="fa-solid fa-circle-arrow-up" style="color:var(--primary);font-size:24px;"></i>
</div>

<nav>
<button class="active"><i class="fa-solid fa-house"></i></button>
<button><i class="fa-solid fa-comments"></i></button>
<button><i class="fa-solid fa-users"></i></button>
</nav>

</div>

</body>
</html>
