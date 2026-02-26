<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Live Connect Modern 🚀</title>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

<style>
:root{ 
--bg:#030508; --card:#0d1117; --primary:#00d2ff; --accent:#ff0055;
--text:#e6edf3; --glass:rgba(13,17,23,.9);
--grad:linear-gradient(135deg,#00d2ff,#3a7bd5);
}

*{box-sizing:border-box;-webkit-tap-highlight-color:transparent;outline:none;}

body{
margin:0;
font-family:'Segoe UI',Roboto,sans-serif;
background:var(--bg);
color:var(--text);
min-height:100dvh;   /* FIX */
display:flex;
flex-direction:column;
}

/* HEADER FIX */
header{
padding:20px 15px 10px;
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
overflow-y:auto;
position:relative;
padding-bottom:160px; /* bottom space */
}

/* PAGE */
.page{
display:none;
height:100%;
flex-direction:column;
padding:15px;
animation:slideIn .3s ease-out;
}
.page.active{display:flex;}

@keyframes slideIn{
from{opacity:0;transform:translateX(20px);}
to{opacity:1;transform:translateX(0);}
}

/* SEARCH */
.search-bar{
background:#161b22;
border:1px solid #30363d;
border-radius:15px;
padding:12px 15px;
display:flex;
align-items:center;
gap:10px;
margin-bottom:15px;
}
.search-bar input{
flex:1;background:none;border:none;color:white;font-size:14px;
}

/* SCROLL BOX */
.scroll-container{
flex:1;
overflow-y:auto;
background:rgba(255,255,255,.03);
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
transition:.2s;
}
.card:active{transform:scale(.97);background:#1c2128;}
.pfp{
width:40px;height:40px;border-radius:50%;
background:var(--grad);
display:flex;align-items:center;justify-content:center;
font-weight:bold;color:white;
}

/* CHAT */
#chatBox{
display:flex;
flex-direction:column;
gap:12px;
padding-bottom:20px;
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
border-bottom-right-radius:2px;
box-shadow:0 4px 15px rgba(0,210,255,.2);
}
.m-bubble.other{
align-self:flex-start;
background:#21262d;
border-bottom-left-radius:2px;
}

/* BOTTOM FIX */
.bottom-shelf{
position:fixed;
bottom:0;
left:0;
width:100%;
padding:10px 0 35px;
background:var(--bg);
border-top:1px solid #1a1d21;
z-index:999;
}

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
flex:1;background:none;border:none;color:white;
}

/* NAV */
nav{
width:92%;
max-width:500px;
margin:0 auto;
height:75px;
background:var(--glass);
backdrop-filter:blur(20px);
border-radius:35px;
display:flex;
justify-content:space-around;
align-items:center;
border:1px solid rgba(255,255,255,.1);
box-shadow:0 -10px 25px rgba(0,0,0,.5);
}
nav button{
background:none;border:none;color:#484f58;
font-size:22px;transition:.3s;padding:10px;
}
nav button.active{
color:var(--primary);
transform:translateY(-5px);
}

/* OVERLAYS */
#loginOverlay{
position:fixed;inset:0;background:#000;
z-index:10000;display:flex;align-items:center;justify-content:center;
}
#banOverlay{
display:none;position:fixed;inset:0;background:#000;
z-index:11000;flex-direction:column;
align-items:center;justify-content:center;text-align:center;padding:30px;
}
</style>
</head>
