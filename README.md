<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect - Modern Social Chat</title>
<style>
/* ---- Global ---- */
body{margin:0;font-family:'Segoe UI',Roboto,Arial,sans-serif;background:#f0f2f5;color:#111;}
button{cursor:pointer;border:none;outline:none;}
h2,h3,h4{margin:0;padding:0;}
input{border:1px solid #ccc;border-radius:5px;padding:8px;width:100%;box-sizing:border-box;}

/* ---- Login ---- */
#login-screen{text-align:center;margin-top:80px;}
#login-screen input{width:250px;margin-bottom:10px;}
#login-screen button{background:#1877f2;color:#fff;padding:10px 20px;border-radius:8px;font-weight:bold;}

/* ---- App Layout ---- */
#app-screen{display:none;height:100vh;display:flex;flex-direction:row;}
.sidebar{width:250px;background:#fff;border-right:1px solid #ddd;display:flex;flex-direction:column;height:100vh;overflow-y:auto;}
.sidebar h3{text-align:center;padding:15px;background:#f7f7f7;border-bottom:1px solid #ddd;}
.user{padding:12px 15px;cursor:pointer;border-bottom:1px solid #eee;display:flex;align-items:center;justify-content:space-between;}
.user.online::after{content:"•";color:green;font-weight:bold;margin-left:5px;}
.chat-area{flex:2;display:flex;flex-direction:column;height:100vh;background:#e5ddd5;}
.chat-header{padding:10px 15px;background:#f7f7f7;border-bottom:1px solid #ddd;font-weight:bold;display:flex;justify-content:space-between;align-items:center;}
#messages-container{flex:1;overflow-y:auto;padding:15px;display:flex;flex-direction:column;gap:8px;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;position:relative;word-wrap:break-word;display:inline-block;animation:fadeIn 0.3s;}
.message.self{align-self:flex-end;background:#dcf8c6;}
.message.other{align-self:flex-start;background:#fff;}
.timestamp{font-size:10px;color:#555;margin-top:4px;text-align:right;}
.typing-indicator{font-size:12px;color:#555;padding-left:10px;}
.chat-input{display:flex;padding:10px;border-top:1px solid #ddd;background:#f7f7f7;}
.chat-input input{flex:1;padding:10px;border-radius:20px;border:1px solid #ccc;margin-right:5px;}
.chat-input button{background:#1877f2;color:#fff;padding:8px 15px;border-radius:20px;font-weight:bold;}

/* ---- Feed ---- */
.feed-area{flex:2;background:#f0f2f5;height:100vh;overflow-y:auto;padding:10px;}
.post-card{background:#fff;border-radius:12px;box-shadow:0 1px 4px rgba(0,0,0,0.1);margin-bottom:15px;padding:12px;transition:transform 0.2s;animation:fadeIn 0.3s;}
.post-card:hover{transform:scale(1.01);}
.post-card img, .post-card video{max-width:100%;border-radius:12px;margin-top:8px;}
.post-card h4{margin-bottom:5px;font-weight:bold;}
.post-card p{margin-top:0;}
.post-input{display:flex;margin-bottom:15px;gap:5px;}
.post-input input{flex:1;border-radius:20px;padding:10px;border:1px solid #ccc;}
.post-input button{background:#1877f2;color:#fff;padding:10px 15px;border-radius:20px;font-weight:bold;}

/* ---- Animations ---- */
@keyframes fadeIn{from{opacity:0;transform:translateY(5px);}to{opacity:1;transform:translateY(0);}}

/* ---- Responsive ---- */
@media(max-width:900px){#app-screen{flex-direction:column;height:auto;} .sidebar, .chat-area, .feed-area{width:100%;height:auto;}}
</style>

<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-storage-compat.js"></script>
</head>
<body>

<div id="login-screen">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter Your Name">
<br>
<button onclick="login()">Login</button>
</div>

<div id="app-screen">
<div class="sidebar">
<h3>Active Users</h3>
<div id="users-list"></div>
</div>

<div class="chat-area">
<div class="chat-header" id="chat-header">Select a user to start chat</div>
<div id="messages-container"></div>
<div class="typing-indicator" id="typing-indicator"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button id="send-btn">Send</button>
<input type="file" id="media-input" style="display:none">
<button id="media-btn" onclick="document.getElementById('media-input').click()">Media</button>
<audio id="notify-sound" src="https://www.myinstants.com/media/sounds/facebook_messenger.mp3" preload="auto"></audio>
</div>
</div>

<div class="feed-area">
<h3>LiveConnect Feed</h3>
<div class="post-input">
<input type="text" id="post-input" placeholder="What's on your mind?">
<button onclick="createPost()">Post</button>
<input type="file" id="post-media" style="display:none">
<button onclick="document.getElementById('post-media').click()">Media</button>
</div>
<div id="feed-container"></div>
</div>
</div>

<script>
// Firebase Config
const firebaseConfig = {
  apiKey: "AIzaSyCSD1O9tV7xDZu_kljq-0NMhA2DqtW5quE",
  authDomain: "live-chat-b810c.firebaseapp.com",
  databaseURL: "https://live-chat-b810c-default-rtdb.firebaseio.com",
  projectId: "live-chat-b810c",
  storageBucket: "live-chat-b810c.appspot.com",
  messagingSenderId: "555058795334",
  appId: "1:555058795334:web:f668887409800c32970b47"
};
firebase.initializeApp(firebaseConfig);
const db=firebase.database();
const storage=firebase.storage();

let currentUser="";
let activeChatUser="";

// Login
function login(){
const username=document.getElementById("username").value.trim();
if(!username) return;
currentUser=username;
document.getElementById("login-screen").style.display="none";
document.getElementById("app-screen").style.display="flex";

db.ref("active_users/"+currentUser).set({online:true});
db.ref("active_users/"+currentUser).onDisconnect().remove();

db.ref("active_users").on("value",snap=>{
  const usersList=document.getElementById("users-list");
  usersList.innerHTML="";
  const users=snap.val()||{};
  for(let user in users){
    if(user!==currentUser){
      const div=document.createElement("div");
      div.textContent=user;
      div.className="user online";
      div.onclick=()=>selectUser(user);
      usersList.appendChild(div);
    }
  }
});
}

// Chat
function selectUser(user){
activeChatUser=user;
document.getElementById("messages-container").innerHTML="";
document.getElementById("chat-header").textContent="Chat with "+user;
document.getElementById("typing-indicator").textContent="";

const chatRef=db.ref("private_chats/"+[currentUser,activeChatUser].sort().join("_"));

chatRef.on("child_added",snap=>{
  const data=snap.val();
  const msgDiv=document.createElement("div");
  if(data.type==="media"){
    const mediaEl = data.mediaType.startsWith("image") ? document.createElement("img") : document.createElement("video");
    mediaEl.src=data.url;
    if(data.mediaType.startsWith("video")) mediaEl.controls=true;
    mediaEl.style.maxWidth="70%";
    msgDiv.appendChild(mediaEl);
  } else {
    msgDiv.textContent=data.user+": "+data.message;
  }
  msgDiv.className="message "+(data.user===currentUser?"self":"other");
  const ts=document.createElement("div");
  ts.className="timestamp";
  ts.textContent=new Date(data.timestamp).toLocaleTimeString();
  msgDiv.appendChild(ts);
  document.getElementById("messages-container").appendChild(msgDiv);
  document.getElementById("messages-container").scrollTop=document.getElementById("messages-container").scrollHeight;
  if(data.user!==currentUser) document.getElementById("notify-sound").play();
});

db.ref("typing/"+currentUser+"/"+activeChatUser).on("value",snap=>{
  document.getElementById("typing-indicator").textContent = snap.val()?activeChatUser+" is typing...":"";
});

document.getElementById("send-btn").onclick=()=>{
  const msg=document.getElementById("message-input").value.trim();
  if(!msg) return;
  chatRef.push({user:currentUser,message:msg,timestamp:Date.now(),type:"text"});
  document.getElementById("message-input").value="";
};

document.getElementById("media-input").onchange=function(e){
  const file=e.target.files[0];
  if(!file) return;
  const storageRef=storage.ref("media/"+Date.now()+"_"+file.name);
  storageRef.put(file).then(snap=>{
    snap.ref.getDownloadURL().then(url=>{
      chatRef.push({user:currentUser,mediaType:file.type,url:url,timestamp:Date.now(),type:"media"});
    });
  });
};
}

// Posts
function createPost(){
  const text=document.getElementById("post-input").value.trim();
  const file=document.getElementById("post-media").files[0];
  const postRef=db.ref("feed").push();
  if(file){
    const storageRef=storage.ref("feed_media/"+Date.now()+"_"+file.name);
    storageRef.put(file).then(snap=>{
      snap.ref.getDownloadURL().then(url=>{
        postRef.set({user:currentUser,text:text,url:url,mediaType:file.type,timestamp:Date.now()});
        document.getElementById("post-input").value="";
        document.getElementById("post-media").value="";
      });
    });
  } else {
    postRef.set({user:currentUser,text:text,timestamp:Date.now()});
    document.getElementById("post-input").value="";
  }
}

// Feed load
db.ref("feed").on("child_added",snap=>{
  const data=snap.val();
  const feedContainer=document.getElementById("feed-container");
  const postDiv=document.createElement("div");
  postDiv.className="post-card";
  postDiv.innerHTML=`<h4>${data.user}</h4><p>${data.text||""}</p>`;
  if(data.url){
    if(data.mediaType.startsWith("image")){
      const img=document.createElement("img"); img.src=data.url; postDiv.appendChild(img);
    } else {
      const vid=document.createElement("video"); vid.src=data.url; vid.controls=true; postDiv.appendChild(vid);
    }
  }
  feedContainer.prepend(postDiv);
});

// Disconnect
window.addEventListener("beforeunload",()=>{
  if(currentUser) db.ref("active_users/"+currentUser).remove();
});
</script>
</body>
</html>
