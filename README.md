<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect Social Chat Pro</title>
<style>
body{font-family:Arial,sans-serif;margin:0;background:#f5f7fb;}
#login-screen,#app-screen{max-width:900px;margin:auto;padding:20px;}
#login-screen{text-align:center;margin-top:50px;}
#app-screen{display:none;}
#users-list,#feed-container,#chat-container{background:#fff;border-radius:10px;box-shadow:0 0 10px rgba(0,0,0,0.1);}
#users-list{float:left;width:25%;height:500px;overflow-y:auto;padding:10px;}
.user{padding:10px;border-bottom:1px solid #eee;cursor:pointer;}
.user.online::after{content:" •";color:green;font-weight:bold;margin-left:3px;}
#chat-container{float:left;width:35%;height:500px;overflow-y:auto;padding:10px;margin-left:10px;}
.message{margin:5px 0;padding:5px 10px;border-radius:10px;max-width:70%;word-wrap:break-word;}
.message.self{background:#d9f0d9;margin-left:auto;text-align:right;}
.message.other{background:#f0d9d9;margin-right:auto;text-align:left;}
.message .timestamp{font-size:10px;color:#555;display:block;text-align:right;margin-top:2px;}
#feed-container{float:left;width:35%;height:500px;overflow-y:auto;padding:10px;margin-left:10px;}
.post{background:#fdfdfd;margin:10px 0;padding:10px;border-radius:10px;box-shadow:0 1px 3px rgba(0,0,0,0.1);}
.post img, .post video{max-width:100%;border-radius:10px;margin-top:5px;}
.clearfix::after{content:"";clear:both;display:table;}
#message-input,#post-input{width:70%;padding:8px;}
button{padding:8px 12px;margin-left:5px;}
.typing-indicator{font-size:12px;color:#555;margin-top:5px;}
</style>

<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-storage-compat.js"></script>
</head>
<body>

<div id="login-screen">
<h2>Enter Your Username</h2>
<input type="text" id="username" placeholder="Your name">
<button onclick="login()">Login</button>
</div>

<div id="app-screen" class="clearfix">
<div id="users-list"><h3>Users</h3></div>

<div id="chat-container">
<h3>Chat</h3>
<div id="messages-container"></div>
<div class="typing-indicator" id="typing-indicator"></div>
<input type="text" id="message-input" placeholder="Type a message...">
<button id="send-btn">Send</button>
<input type="file" id="media-input" style="display:none">
<button id="media-btn" onclick="document.getElementById('media-input').click()">Send Media</button>
<audio id="notify-sound" src="https://www.myinstants.com/media/sounds/facebook_messenger.mp3" preload="auto"></audio>
</div>

<div id="feed-container">
<h3>Feed</h3>
<input type="text" id="post-input" placeholder="What's on your mind?">
<input type="file" id="post-media" style="display:none">
<button onclick="createPost()">Post</button>
<button onclick="document.getElementById('post-media').click()">Add Media</button>
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
document.getElementById("app-screen").style.display="block";

// Active users
db.ref("active_users/"+currentUser).set({online:true});
db.ref("active_users/"+currentUser).onDisconnect().remove();

// Load users list
db.ref("active_users").on("value",snap=>{
  const usersList=document.getElementById("users-list");
  usersList.innerHTML="<h3>Users</h3>";
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

// Chat functions
function selectUser(user){
activeChatUser=user;
document.getElementById("messages-container").innerHTML="";
document.getElementById("typing-indicator").textContent="";

const chatRef=db.ref("private_chats/"+[currentUser,activeChatUser].sort().join("_"));

// Listen for messages
chatRef.on("child_added",snap=>{
  const data=snap.val();
  const msgDiv=document.createElement("div");
  if(data.type==="media"){
    const mediaEl = data.mediaType.startsWith("image") ? document.createElement("img") : document.createElement("video");
    mediaEl.src=data.url;
    if(data.mediaType.startsWith("video")) mediaEl.controls=true;
    msgDiv.appendChild(mediaEl);
  } else {
    msgDiv.textContent=data.user+": "+data.message;
  }
  msgDiv.className="message "+(data.user===currentUser?"self":"other");
  const ts=document.createElement("span");
  ts.className="timestamp";
  ts.textContent=new Date(data.timestamp).toLocaleTimeString();
  msgDiv.appendChild(ts);
  document.getElementById("messages-container").appendChild(msgDiv);
  document.getElementById("messages-container").scrollTop=document.getElementById("messages-container").scrollHeight;
  if(data.user!==currentUser) document.getElementById("notify-sound").play();
});

// Typing indicator
db.ref("typing/"+currentUser+"/"+activeChatUser).on("value",snap=>{
  document.getElementById("typing-indicator").textContent = snap.val()?activeChatUser+" is typing...":"";
});

// Send message
document.getElementById("send-btn").onclick=()=>{
  const msg=document.getElementById("message-input").value.trim();
  if(!msg) return;
  chatRef.push({user:currentUser,message:msg,timestamp:Date.now(),type:"text"});
  document.getElementById("message-input").value="";
};

// Send media
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

// Post functions
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

// Load feed realtime
db.ref("feed").on("child_added",snap=>{
  const data=snap.val();
  const feedContainer=document.getElementById("feed-container");
  const postDiv=document.createElement("div");
  postDiv.className="post";
  postDiv.innerHTML=`<b>${data.user}</b><br>${data.text||""}`;
  if(data.url){
    if(data.mediaType.startsWith("image")){
      const img=document.createElement("img"); img.src=data.url; postDiv.appendChild(img);
    } else {
      const vid=document.createElement("video"); vid.src=data.url; vid.controls=true; postDiv.appendChild(vid);
    }
  }
  feedContainer.appendChild(postDiv);
  feedContainer.scrollTop=feedContainer.scrollHeight;
});

// Remove user on disconnect
window.addEventListener("beforeunload",()=>{
  if(currentUser) db.ref("active_users/"+currentUser).remove();
});
</script>
</body>
</html>
