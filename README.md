<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Live Connect</title>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
:root{--bg:#f0f2f5;--card:#fff;--me:#dcf8c6;--other:#fff;--pri:#25D366}
body.dark{--bg:#0b141a;--card:#111b21;--me:#005c4b;--other:#1f2c34}
body{margin:0;font-family:system-ui;background:var(--bg)}

header{height:56px;background:var(--pri);color:#fff;display:flex;align-items:center;justify-content:center;font-weight:700}

.hero{height:180px;background-size:cover;color:#fff;display:flex;align-items:center;justify-content:center;font-size:20px;text-shadow:0 2px 6px #000}

.login{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center}
.login div{background:var(--card);padding:20px;border-radius:12px;width:90%}
.login input{width:100%;padding:12px;margin:10px 0}

.app{display:none;flex-direction:column;height:calc(100vh - 236px)}

.users{display:flex;gap:8px;padding:8px;overflow-x:auto;background:var(--card)}
.user{padding:6px 12px;border-radius:20px;background:#00000015;font-size:14px}
.user::before{content:"● ";color:#25D366}
.group{background:#00000030}

.chat{flex:1;display:flex;flex-direction:column;min-height:0}
.chat-head{padding:6px;background:var(--card);display:flex;justify-content:space-between;font-size:13px}

.messages{flex:1;overflow-y:auto;padding:8px}
.msg{max-width:75%;margin-bottom:6px;padding:8px;border-radius:12px;font-size:13px}
.me{background:var(--me);margin-left:auto}
.other{background:var(--other)}
.msg img{max-width:180px;border-radius:8px}

.input{display:flex;gap:6px;padding:6px;background:var(--card)}
.input input{flex:1;padding:10px;border-radius:20px;border:1px solid #ccc}
.input button{width:38px;height:38px;border-radius:50%;border:none;background:var(--pri);color:#fff}
</style>
</head>

<body>

<header>Live Connect</header>
<div class="hero" id="hero">Welcome to Live Connect</div>

<div class="login" id="login">
  <div>
    <h3>Enter Full Name</h3>
    <input id="name">
    <button onclick="start()">Start Chat</button>
  </div>
</div>

<div class="app" id="app">
  <div class="users" id="users"></div>

  <div class="chat">
    <div class="chat-head">
      <span id="chatWith">Select user</span>
      <span>
        <button onclick="clearChat()">Clear</button>
        <button onclick="delGroup()">Delete Group</button>
        <i class="fa fa-moon" onclick="document.body.classList.toggle('dark')"></i>
      </span>
    </div>

    <div class="messages" id="msgs"></div>

    <div class="input">
      <input id="msg">
      <input type="file" id="img" accept="image/*" hidden>
      <button onclick="send()">➤</button>
      <button onclick="img.click()">🖼️</button>
    </div>
  </div>
</div>

<script type="module">
import{initializeApp}from"https://www.gstatic.com/firebasejs/12.9.0/firebase-app.js";
import{getDatabase,ref,set,push,onValue,remove,onDisconnect}from"https://www.gstatic.com/firebasejs/12.9.0/firebase-database.js";
import{getStorage,ref as sRef,uploadBytes,getDownloadURL}from"https://www.gstatic.com/firebasejs/12.9.0/firebase-storage.js";

const firebaseConfig={
apiKey:"AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
databaseURL:"https://live-chat-b810c-default-rtdb.firebaseio.com",
projectId:"live-chat-b810c",
storageBucket:"live-chat-b810c.appspot.com"
};

const app=initializeApp(firebaseConfig),
db=getDatabase(app),
st=getStorage(app);

let me="",cur="",isGroup=false,creator={};

window.start=()=>{
me=name.value.trim();
if(!me)return;
login.style.display="none";
app.style.display="flex";
set(ref(db,"users/"+me),{online:true});
onDisconnect(ref(db,"users/"+me)).set({online:false});
load();
};

function load(){
onValue(ref(db,"users"),s=>{
users.innerHTML="";
s.forEach(u=>{
if(u.val().online&&u.key!==me){
let d=document.createElement("div");
d.className="user";
d.textContent=u.key;
d.onclick=()=>open(u.key,false);
users.appendChild(d);
}});
});
onValue(ref(db,"groups"),s=>{
s.forEach(g=>{
creator[g.key]=g.val().by;
let d=document.createElement("div");
d.className="user group";
d.textContent=g.key;
d.onclick=()=>open(g.key,true);
users.appendChild(d);
});
});
}

window.open=(u,g)=>{
cur=u;isGroup=g;
chatWith.textContent=u;
let p=g?"groupChats/"+u:"chats/"+[me,u].sort().join("_");
onValue(ref(db,p),s=>{
msgs.innerHTML="";
s.forEach(m=>{
let d=document.createElement("div");
d.className="msg "+(m.val().from===me?"me":"other");
d.innerHTML=m.val().img?`<img src="${m.val().img}">`:m.val().text;
if(m.val().from===me){
let b=document.createElement("button");
b.textContent="🗑️";
b.onclick=()=>remove(ref(db,p+"/"+m.key));
d.appendChild(b);
}
msgs.appendChild(d);
});
msgs.scrollTop=msgs.scrollHeight;
});
};

window.send=()=>{
if(!cur)return;
push(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")),{from:me,text:msg.value});
msg.value="";
};

img.onchange=async()=>{
let f=img.files[0];
let r=sRef(st,"imgs/"+Date.now()+f.name);
await uploadBytes(r,f);
let u=await getDownloadURL(r);
push(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")),{from:me,img:u});
};

window.clearChat=()=>remove(ref(db,isGroup?"groupChats/"+cur:"chats/"+[me,cur].sort().join("_")));
window.delGroup=()=>{if(isGroup&&creator[cur]===me)remove(ref(db,"groups/"+cur))};

const imgs=[
"https://images.unsplash.com/photo-1525182008055-f88b95ff7980",
"https://images.unsplash.com/photo-1515378791036-0648a3ef77b2",
"https://images.unsplash.com/photo-1492724441997-5dc865305da7"
];
let i=0;
setInterval(()=>hero.style.backgroundImage=`url(${imgs[i++%imgs.length]})`,3000);
</script>

</body>
</html>
