<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
/* ----- Clean Dark Theme ----- */
body{
  margin:0;
  font-family:'Segoe UI',Roboto,Arial,sans-serif;
  background:#121212;
  color:#eee;
  overflow-x:hidden;
}
button{cursor:pointer;border:none;outline:none;border-radius:8px;}
input,textarea{border:1px solid #555;border-radius:8px;padding:8px;width:100%;box-sizing:border-box;background:#1c1c1c;color:#eee;}

/* ----- Navbar ----- */
.navbar{
  display:flex;justify-content:space-between;align-items:center;
  padding:10px 15px;background:#1f1f1f;border-bottom:1px solid #333;flex-wrap:wrap;
}
.navbar h2{color:#0ff;}
.navbar .tabs{display:flex;gap:5px;flex-wrap:wrap;}
.navbar .tabs button{padding:6px 12px;background:#333;color:#eee;}
.navbar .tabs button.active{background:#0ff;color:#000;}
.navbar .chat-icon{font-size:22px;color:#0ff;cursor:pointer;}

/* ----- Main Container ----- */
.main-container{display:flex;flex-direction:row;height:calc(100vh - 60px);}
.sidebar{width:220px;background:#1f1f1f;border-right:1px solid #333;overflow-y:auto;display:none;}
.sidebar h3{text-align:center;padding:15px;border-bottom:1px solid #333;color:#0ff;}
.user{padding:12px 15px;cursor:pointer;border-bottom:1px solid #333;display:flex;align-items:center;justify-content:space-between;}
.user.online::after{content:"•";color:#0ff;font-weight:bold;margin-left:5px;}

/* ----- Content Area ----- */
.content-area{flex:1;display:flex;flex-direction:column;overflow-y:auto;padding:10px;}
.tab-panel{display:none;}
.tab-panel.active{display:block;}

/* ----- Posts ----- */
.post-card{background:#1c1c1c;border-radius:12px;margin-bottom:15px;padding:12px;box-shadow:0 0 5px #0ff;}
.post-card img, .post-card video{max-width:100%;border-radius:8px;margin-top:8px;}
.post-input{display:flex;gap:5px;margin-bottom:10px;flex-wrap:wrap;}
.post-input input{flex:1;min-width:150px;border-radius:20px;padding:8px;border:1px solid #0ff;background:#1c1c1c;color:#eee;}

/* ----- Chat ----- */
.chat-container{flex:1;display:flex;flex-direction:column;border-top:1px solid #333;display:none;}
.chat-messages{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:5px;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;box-shadow:0 0 3px #0ff;}
.message.self{align-self:flex-end;background:#0ff;color:#000;}
.message.other{align-self:flex-start;background:#333;color:#eee;}
.timestamp{font-size:10px;color:#aaa;margin-top:4px;text-align:right;}
.chat-input{display:flex;padding:10px;border-top:1px solid #333;gap:5px;flex-wrap:wrap;}
.chat-input input{flex:1;min-width:150px;padding:8px;border-radius:20px;border:1px solid #0ff;background:#1c1c1c;color:#eee;}
.chat-input button{background:#0ff;color:#000;padding:6px 12px;font-weight:bold;}

/* ----- About/Footer ----- */
.about-section{background:#1f1f1f;color:#aaa;padding:15px;text-align:center;font-size:14px;margin-top:10px;border-top:1px solid #333;}
.about-section a{color:#0ff;text-decoration:underline;margin:0 5px;}

/* ----- Responsive ----- */
@media(max-width:900px){
  .main-container{flex-direction:column;height:auto;}
  .sidebar{width:100%;height:auto;}
  .post-input,.chat-input{flex-direction:column;}
}
</style>

<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-storage-compat.js"></script>
</head>
<body>

<!-- Login -->
<div id="login-screen" style="text-align:center;margin-top:50px;">
<h2>Welcome to LiveConnect</h2>
<input type="text" id="username" placeholder="Enter Your Name"><br><br>
<button onclick="login()">Enter</button>
</div>

<!-- App -->
<div id="app-screen" style="display:none;flex-direction:column;">
<div class="navbar">
<h2>LiveConnect</h2>
<div class="tabs">
<button class="active" onclick="switchTab('home')">Home</button>
<button onclick="switchTab('posts')">Posts</button>
<button onclick="switchTab('videos')">Videos</button>
<button onclick="switchTab('settings')">Settings</button>
<button onclick="switchTab('privacy')">Privacy</button>
<button class="chat-icon" onclick="toggleChat()">💬</button>
</div>
</div>

<div class="main-container">
<div class="sidebar" id="users-sidebar">
<h3>Active Users</h3>
<div id="users-list"></div>
</div>

<div class="content-area">
<!-- Home -->
<div id="home" class="tab-panel active">
<h3>Feed</h3>
<div class="post-input">
<input type="text" id="post-input" placeholder="What's on your mind?">
<button onclick="createPost()">Post</button>
<input type="file" id="post-media" style="display:none">
<button onclick="document.getElementById('post-media').click()">Media</button>
</div>
<div id="feed-container"></div>
</div>

<!-- Posts -->
<div id="posts" class="tab-panel">
<h3>Your Posts</h3>
<div id="my-posts-container"></div>
</div>

<!-- Videos -->
<div id="videos" class="tab-panel">
<h3>Videos</h3>
<div id="videos-container"></div>
</div>

<!-- Settings -->
<div id="settings" class="tab-panel">
<h3>Settings</h3>
<button onclick="toggleDarkMode()">Toggle Dark Mode</button>
</div>

<!-- Privacy -->
<div id="privacy" class="tab-panel">
<h3>Privacy & About</h3>
<p>LiveConnect is a modern platform to chat, share posts and media, and connect with friends.</p>
</div>

<!-- Chat -->
<div class="chat-container" id="chat-container">
<div class="chat-messages" id="messages-container"></div>
<div class="chat-input">
<input type="text" id="message-input" placeholder="Type a message...">
<button id="send-btn">Send</button>
<input type="file" id="media-input" style="display:none">
<button id="media-btn" onclick="document.getElementById('media-input').click()">Media</button>
<audio id="notify-sound" src="https://www.myinstants.com/media/sounds/facebook_messenger.mp3" preload="auto"></audio>
</div>
</div>

</div>
<div class="about-section">
LiveConnect - Connect & Share | <a href="#">About</a> | <a href="#">Terms</a> | <a href="#">Privacy</a>
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
if(!username){alert("Enter username");return;}
currentUser=username;
document.getElementById("login-screen").style.display="none";
document.getElementById("app-screen").style.display="flex";
db.ref("active_users/"+currentUser).set({online:true});
db.ref("active_users/"+currentUser).onDisconnect().remove();
db.ref("active_users").on("value",snap=>{
  const usersList=document.getElementById("users-list"); usersList.innerHTML="";
  const users=snap.val()||{};
  for(let user in users){
    if(user!==currentUser){
      const div=document.createElement("div");
      div.textContent=user; div.className="user online"; usersList.appendChild(div);
      div.onclick=()=>selectUser(user);
    }
  }
});

// Feed
db.ref("feed").on("child_added",snap=>loadFeedPost(snap));
}

// Tabs
function switchTab(tab){
const tabs=["home","posts","videos","settings","privacy"];
tabs.forEach(t=>{
  document.getElementById(t).style.display=(t===tab)?"block":"none";
  document.querySelectorAll(".tabs button").forEach(b=>{
    if(b.textContent.toLowerCase()===t) b.classList.add("active"); else b.classList.remove("active");
  });
});
}

// Chat toggle
function toggleChat(){
const chat=document.getElementById("chat-container");
chat.style.display=(chat.style.display==="flex")?"none":"flex";
document.getElementById("users-sidebar").style.display=(chat.style.display==="flex")?"block":"none";
}

// Chat
function selectUser(user){
activeChatUser=user;
document.getElementById("messages-container").innerHTML="";
const chatRef=db.ref("private_chats/"+[currentUser,activeChatUser].sort().join("_"));
chatRef.on("child_added",snap=>{
  const data=snap.val(); const msgDiv=document.createElement("div");
  if(data.type==="media"){
    const mediaEl=data.mediaType.startsWith("image")?document.createElement("img"):document.createElement("video");
    mediaEl.src=data.url;if(data.mediaType.startsWith("video")) mediaEl.controls=true; mediaEl.style.maxWidth="70%"; msgDiv.appendChild(mediaEl);
  } else { msgDiv.textContent=data.user+": "+data.message; }
  msgDiv.className="message "+(data.user===currentUser?"self":"other");
  const ts=document.createElement("div"); ts.className="timestamp"; ts.textContent=new Date(data.timestamp).toLocaleTimeString();
  msgDiv.appendChild(ts); document.getElementById("messages-container").appendChild(msgDiv);
  document.getElementById("messages-container").scrollTop=document.getElementById("messages-container").scrollHeight;
  if(data.user!==currentUser) document.getElementById("notify-sound").play();
});

// Send
document.getElementById("send-btn").onclick=()=>{
  const msg=document.getElementById("message-input").value.trim(); if(!msg) return;
  chatRef.push({user:currentUser,message:msg,timestamp:Date.now(),type:"text"});
  document.getElementById("message-input").value="";
};

// Media
document.getElementById("media-input").onchange=function(e){
  const file=e.target.files[0]; if(!file) return;
  const storageRef=storage.ref("media/"+Date.now()+"_"+file.name);
  storageRef.put(file).then(snap=>snap.ref.getDownloadURL().then(url=>chatRef.push({user:currentUser,mediaType:file.type,url:url,timestamp:Date.now(),type:"media"})));
};
}

// Posts
function createPost(){
const text=document.getElementById("post-input").value.trim();
const file=document.getElementById("post-media").files[0];
const postRef=db.ref("feed").push();
if(file){
  const storageRef=storage.ref("feed_media/"+Date.now()+"_"+file.name);
  storageRef.put(file).then(snap=>snap.ref.getDownloadURL().then(url=>{
    postRef.set({user:currentUser,text:text,url:url,mediaType:file.type,timestamp:Date.now(),likes:0,comments:[]});
    document.getElementById("post-input").value="";
    document.getElementById("post-media").value="";
  }));
} else {
  postRef.set({user:currentUser,text:text,timestamp:Date.now(),likes:0,comments:[]});
  document.getElementById("post-input").value="";
}
}

// Load Feed
function loadFeedPost(snap){
const data=snap.val(); const feedContainer=document.getElementById("feed-container");
const postDiv=document.createElement("div"); postDiv.className="post-card";
postDiv.innerHTML=`<h4>${data.user}</h4><p>${data.text||""}</p>
<button onclick="likePost('${snap.key}')">❤️ ${data.likes||0}</button>
<button onclick="commentPost('${snap.key}')">💬 ${data.comments?Object.keys(data.comments).length:0}</button>`;
if(data.url){
  if(data.mediaType.startsWith("image")){
    const img=document.createElement("img"); img.src=data.url; postDiv.appendChild(img);
  } else { const vid=document.createElement("video"); vid.src=data.url; vid.controls=true; postDiv.appendChild(vid); }
}
feedContainer.prepend(postDiv);
}

// Likes & Comments
function likePost(key){ db.ref("feed/"+key+"/likes").transaction(current=> (current||0)+1); }
function commentPost(key){
const c=prompt("Enter comment:"); if(c) db.ref("feed/"+key+"/comments").push({user:currentUser,text:c,timestamp:Date.now()});
}

// Dark Mode
function toggleDarkMode(){document.body.classList.toggle("dark-mode");}

// Disconnect
window.addEventListener("beforeunload",()=>{ if(currentUser) db.ref("active_users/"+currentUser).remove(); });
</script>

</body>
</html>
