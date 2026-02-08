<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<style>
body{margin:0;font-family:'Segoe UI',Roboto,Arial,sans-serif;background:#111;color:#eee;overflow-x:hidden;}
button{cursor:pointer;border:none;outline:none;border-radius:8px;transition:0.3s;}
button:hover{opacity:0.8;}
input,textarea{border:1px solid #555;border-radius:8px;padding:8px;width:100%;box-sizing:border-box;background:#1a1a1a;color:#eee;}
a{text-decoration:none;color:#0ff;}

/* Navbar */
.navbar{display:flex;justify-content:space-between;align-items:center;padding:10px 15px;background:#1f1f1f;border-bottom:1px solid #333;flex-wrap:wrap;}
.navbar h2{color:#0ff;}
.navbar .tabs{display:flex;gap:5px;flex-wrap:wrap;}
.navbar .tabs button{padding:6px 12px;background:#333;color:#eee;}
.navbar .tabs button.active{background:#0ff;color:#000;}
.navbar .chat-icon{font-size:22px;color:#0ff;cursor:pointer;}
.navbar .notification{font-size:22px;color:#0ff;cursor:pointer;position:relative;}
.navbar .notification span{position:absolute;top:-5px;right:-5px;background:red;color:#fff;border-radius:50%;padding:2px 5px;font-size:12px;}

/* Layout */
.main-container{display:flex;flex-direction:row;height:calc(100vh - 60px);}
.sidebar{width:220px;background:#1f1f1f;border-right:1px solid #333;overflow-y:auto;display:none;}
.sidebar h3{text-align:center;padding:15px;border-bottom:1px solid #333;color:#0ff;}
.user{padding:12px 15px;cursor:pointer;border-bottom:1px solid #333;display:flex;align-items:center;justify-content:space-between;}
.user.online::after{content:"•";color:#0ff;font-weight:bold;margin-left:5px;}
.content-area{flex:1;display:flex;flex-direction:column;overflow-y:auto;padding:10px;}
.tab-panel{display:none;}
.tab-panel.active{display:block;}

/* Posts */
.post-card{background:#1a1a1a;border-radius:12px;margin-bottom:15px;padding:12px;box-shadow:0 0 8px #0ff;position:relative;transition:0.3s;}
.post-card:hover{box-shadow:0 0 12px #0ff;}
.post-card img,.post-card video{max-width:100%;border-radius:8px;margin-top:8px;cursor:pointer;}
.post-card .post-actions{display:flex;gap:10px;margin-top:8px;}
.post-card .post-actions button{background:#333;color:#eee;padding:4px 8px;border-radius:8px;font-size:14px;}
.post-card .post-actions button.delete-btn{background:#ff0055;color:#000;}
.comment-section{margin-top:8px;border-top:1px solid #333;padding-top:6px;}
.comment{padding:4px 8px;background:#222;border-radius:8px;margin-bottom:4px;}
.comment .reply{margin-left:12px;color:#0ff;font-size:12px;cursor:pointer;}
.comment .reply-box{display:none;margin-top:4px;}
.comment-input{display:flex;gap:5px;margin-top:4px;}
.comment-input input{flex:1;border-radius:20px;padding:4px;border:1px solid #0ff;background:#1a1a1a;color:#eee;}
.comment-input button{background:#0ff;color:#000;padding:4px 8px;border-radius:8px;}

/* Chat */
.chat-container{flex:1;display:flex;flex-direction:column;border-top:1px solid #333;display:none;}
.chat-messages{flex:1;overflow-y:auto;padding:10px;display:flex;flex-direction:column;gap:5px;}
.message{max-width:70%;padding:8px 12px;border-radius:18px;word-wrap:break-word;box-shadow:0 0 3px #0ff;position:relative;}
.message.self{align-self:flex-end;background:#0ff;color:#000;}
.message.other{align-self:flex-start;background:#333;color:#eee;}
.timestamp{font-size:10px;color:#aaa;margin-top:4px;text-align:right;}
.typing-indicator{font-size:12px;color:#0ff;margin:2px 0;}
.chat-input{display:flex;padding:10px;border-top:1px solid #333;gap:5px;flex-wrap:wrap;}
.chat-input input{flex:1;min-width:150px;padding:8px;border-radius:20px;border:1px solid #0ff;background:#1a1a1a;color:#eee;}
.chat-input button{background:#0ff;color:#000;padding:6px 12px;font-weight:bold;}

/* About */
.about-section{background:#1f1f1f;color:#aaa;padding:15px;text-align:center;font-size:14px;margin-top:10px;border-top:1px solid #333;}
.about-section a{color:#0ff;text-decoration:underline;margin:0 5px;}

/* Responsive */
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
<div class="notification" onclick="toggleNotifications()">🔔<span id="notif-count">0</span></div>
</div>
</div>

<div class="main-container">
<div class="sidebar" id="users-sidebar">
<h3>Active Users</h3>
<div id="users-list"></div>
<h3>Groups</h3>
<div id="groups-list"></div>
<button onclick="createGroup()">+ Create Group</button>
</div>

<div class="content-area">
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

<div id="posts" class="tab-panel">
<h3>Your Posts</h3>
<div id="my-posts-container"></div>
</div>

<div id="videos" class="tab-panel">
<h3>Videos</h3>
<div id="videos-container"></div>
</div>

<div id="settings" class="tab-panel">
<h3>Settings</h3>
<button onclick="toggleDarkMode()">Toggle Dark Mode</button>
</div>

<div id="privacy" class="tab-panel">
<h3>Privacy & About</h3>
<p>LiveConnect is a modern platform to chat, create posts, media, groups, and connect with friends in realtime. Enjoy a premium neon look, mobile-friendly layout, and all features fully functional.</p>
</div>

<div class="chat-container" id="chat-container">
<div class="chat-messages" id="messages-container"></div>
<div class="typing-indicator" id="typing-indicator"></div>
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
// Firebase
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

let currentUser="",chatId="",typingTimeout;

// ---- Login ----
function login(){
const username=document.getElementById("username").value.trim();
if(!username){alert("Enter username");return;}
currentUser=username;
document.getElementById("login-screen").style.display="none";
document.getElementById("app-screen").style.display="flex";
db.ref("active_users/"+currentUser).set({online:true});
db.ref("active_users/"+currentUser).onDisconnect().remove();
loadActiveUsers();loadGroups();loadFeed();
}

// ---- Tabs ----
function switchTab(tab){
const tabs=["home","posts","videos","settings","privacy"];
tabs.forEach(t=>{
document.getElementById(t).style.display=(t===tab)?"block":"none";
document.querySelectorAll(".tabs button").forEach(b=>{
if(b.textContent.toLowerCase()===tab) b.classList.add("active"); else b.classList.remove("active");
});
});
}

// ---- Chat Toggle ----
function toggleChat(){
const chat=document.getElementById("chat-container");
chat.style.display=(chat.style.display==="flex")?"none":"flex";
document.getElementById("users-sidebar").style.display=(chat.style.display==="flex")?"block":"none";
}

// ---- Dark Mode ----
function toggleDarkMode(){document.body.classList.toggle("dark-mode");}

// ---- Active Users ----
function loadActiveUsers(){
db.ref("active_users").on("value",snap=>{
const usersList=document.getElementById("users-list");
usersList.innerHTML="";
const users=snap.val()||{};
for(let user in users){
if(user!==currentUser){
const div=document.createElement("div");
div.textContent=user; div.className="user online"; div.onclick=()=>openPrivateChat(user);
usersList.appendChild(div);
}
}
});
}

// ---- Groups ----
function loadGroups(){
db.ref("groups").on("value",snap=>{
const groupsList=document.getElementById("groups-list");
groupsList.innerHTML="";
const groups=snap.val()||{};
for(let gid in groups){
const g=groups[gid];
const div=document.createElement("div");
div.textContent=g.name; div.className="user";
div.onclick=()=>openGroupChat(gid);
groupsList.appendChild(div);
}
});
}

function createGroup(){
const gname=prompt("Enter group name:");
if(!gname) return;
const groupRef=db.ref("groups").push();
groupRef.set({name:gname,creator:currentUser,members:{[currentUser]:true}});
}

// ---- Chat ----
function getChatId(user1,user2){return [user1,user2].sort().join("_");}

function openPrivateChat(user){
chatId=getChatId(currentUser,user);
document.getElementById("messages-container").innerHTML="";
loadChat(chatId,true);
}

function openGroupChat(gid){
chatId="group_"+gid;
document.getElementById("messages-container").innerHTML="";
loadChat(chatId,false,true);
}<script>
let isPrivateChat=true,isGroupChat=false;

// ---- Load Chat Messages ----
function loadChat(id,privateChat=true,groupChat=false){
  isPrivateChat=privateChat;
  isGroupChat=groupChat;
  const msgContainer=document.getElementById("messages-container");
  const typingIndicator=document.getElementById("typing-indicator");

  db.ref("chat/"+id).on("child_added",snap=>{
    const msg=snap.val();
    if(isPrivateChat && !([msg.user, msg.to].includes(currentUser))) return;
    const div=document.createElement("div");
    div.className="message "+(msg.user===currentUser?"self":"other");
    div.innerHTML=`<strong>${msg.user}</strong>: ${msg.text || ""}${msg.media?`<br>${msg.media.endsWith('.mp4')?'<video controls src="'+msg.media+'"></video>':'<img src="'+msg.media+'">'}`
    +`<div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>`;
    msgContainer.appendChild(div);
    msgContainer.scrollTop=msgContainer.scrollHeight;
    if(msg.user!==currentUser)document.getElementById("notify-sound").play();
  });

  db.ref("typing/"+id).on("value",snap=>{
    const data=snap.val()||{};
    const typingUsers=Object.keys(data).filter(u=>u!==currentUser);
    typingIndicator.textContent=typingUsers.length>0?typingUsers.join(", ")+" is typing...":"";
  });

  document.getElementById("message-input").oninput=()=>{
    db.ref("typing/"+id+"/"+currentUser).set(true);
    clearTimeout(typingTimeout);
    typingTimeout=setTimeout(()=>{db.ref("typing/"+id+"/"+currentUser).remove();},2000);
  };

  document.getElementById("send-btn").onclick=()=>sendMessage();
  document.getElementById("media-input").onchange=function(e){
    const file=e.target.files[0]; if(!file) return;
    const storageRef=storage.ref((isGroupChat?'group_media':'chat_media')+'/'+Date.now()+'_'+file.name);
    storageRef.put(file).then(snap=>snap.ref.getDownloadURL().then(url=>sendMessage(url)));
  };
}

// ---- Send Message ----
function sendMessage(mediaUrl=null){
  const text=document.getElementById("message-input").value.trim();
  if(!text && !mediaUrl) return;
  let msgData={user:currentUser,text:text,media:mediaUrl||"",time:Date.now()};
  if(isPrivateChat){
    msgData.to=chatId.replace(currentUser+"_","").replace("_"+currentUser,"");
  }
  db.ref("chat/"+chatId).push(msgData);
  document.getElementById("message-input").value="";
  db.ref("typing/"+chatId+"/"+currentUser).remove();
}

// ---- Posts ----
function createPost(){
  const text=document.getElementById("post-input").value.trim();
  const fileInput=document.getElementById("post-media");
  if(!text && !fileInput.files[0]) return alert("Type something or select media!");
  const postRef=db.ref("feed").push();
  if(fileInput.files[0]){
    const file=fileInput.files[0];
    const storageRef=storage.ref('post_media/'+Date.now()+'_'+file.name);
    storageRef.put(file).then(snap=>snap.ref.getDownloadURL().then(url=>{
      postRef.set({user:currentUser,text:text,media:url,likes:0,time:Date.now(),comments:{}});
      fileInput.value="";
    }));
  }else{
    postRef.set({user:currentUser,text:text,likes:0,time:Date.now(),comments:{}});
  }
  document.getElementById("post-input").value="";
}

// ---- Load Feed ----
function loadFeed(){
  db.ref("feed").on("value",snap=>{
    const feedContainer=document.getElementById("feed-container");
    feedContainer.innerHTML="";
    const posts=snap.val()||{};
    for(let pid in posts){
      feedContainer.appendChild(renderPost(pid,posts[pid]));
    }
  });
}

// ---- Render Post ----
function renderPost(pid,post){
  const div=document.createElement("div");
  div.className="post-card"; div.id="post-"+pid;
  div.innerHTML=`<strong>${post.user}</strong>: ${post.text || ""}${post.media?`<br>${post.media.endsWith('.mp4')?'<video controls src="'+post.media+'"></video>':'<img src="'+post.media+'">':''}
  <div class="post-actions">
  <button onclick="likePost('${pid}')">Like (${post.likes||0})</button>
  <button onclick="toggleCommentBox('${pid}')">Comment</button>
  ${post.user===currentUser?'<button class="delete-btn" onclick="deletePost(\''+pid+'\')">Delete</button>':''}
  </div>
  <div class="comment-section" id="comments-${pid}"></div>
  <div class="comment-input" id="comment-box-${pid}" style="display:none;">
  <input type="text" placeholder="Write a comment..." id="comment-input-${pid}">
  <button onclick="addComment('${pid}')">Post</button>
  </div>`;
  loadComments(pid);
  return div;
}

// ---- Like Post ----
function likePost(pid){
  const ref=db.ref("feed/"+pid+"/likes");
  ref.transaction(likes=>(likes||0)+1);
}

// ---- Delete Post ----
function deletePost(pid){
  if(confirm("Are you sure to delete this post?")){
    db.ref("feed/"+pid).remove();
  }
}

// ---- Comments ----
function toggleCommentBox(pid){
  const box=document.getElementById("comment-box-"+pid);
  box.style.display=(box.style.display==="none")?"flex":"none";
}

function addComment(pid,parentId=null){
  const input=document.getElementById("comment-input-"+pid);
  const text=input.value.trim();
  if(!text) return;
  const commentRef=parentId?db.ref(`feed/${pid}/comments/${parentId}/replies`).push():db.ref(`feed/${pid}/comments`).push();
  commentRef.set({user:currentUser,text:text,time:Date.now(),replies:{}});
  input.value="";
}

// ---- Load Comments ----
function loadComments(pid){
  db.ref(`feed/${pid}/comments`).on("value",snap=>{
    const container=document.getElementById("comments-"+pid);
    container.innerHTML="";
    const comments=snap.val()||{};
    for(let cid in comments){
      container.appendChild(renderComment(pid,cid,comments[cid]));
    }
  });
}

function renderComment(pid,cid,comment){
  const div=document.createElement("div"); div.className="comment";
  div.innerHTML=`<strong>${comment.user}</strong>: ${comment.text} 
  ${comment.user===currentUser?'<button class="delete-btn" onclick="deleteComment(\''+pid+'\',\''+cid+'\')">Delete</button>':''}
  <span class="reply" onclick="replyComment('${pid}','${cid}')">Reply</span>`;
  const replyBox=document.createElement("div"); replyBox.className="comment-input reply-box"; replyBox.id="reply-box-"+cid;
  replyBox.style.display="none"; replyBox.innerHTML=`<input type="text" placeholder="Write a reply..." id="reply-input-${cid}"><button onclick="addComment('${pid}','${cid}')">Post</button>`;
  div.appendChild(replyBox);
  return div;
}

function replyComment(pid,cid){
  const box=document.getElementById("reply-box-"+cid);
  box.style.display=(box.style.display==="none")?"flex":"none";
}

function deleteComment(pid,cid){
  db.ref(`feed/${pid}/comments/${cid}`).remove();
}

// ---- Notifications ----
function toggleNotifications(){alert("Notifications placeholder");}
