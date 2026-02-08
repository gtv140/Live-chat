<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
<style>
/* ----- General ----- */
body{margin:0;font-family:'Segoe UI',Roboto,sans-serif;background:#e5ddd5;color:#000;}
.dark-mode body{background:#121b22;color:#fff;}
button{cursor:pointer;border:none;outline:none;border-radius:6px;transition:0.3s;}
button:hover{opacity:0.8;}
input{border:1px solid #ccc;border-radius:20px;padding:8px;width:100%;box-sizing:border-box;}
img,video{max-width:200px;border-radius:10px;}

/* ----- Layout ----- */
.container{display:flex;height:100vh;}
.sidebar{width:320px;background:#f0f0f0;display:flex;flex-direction:column;transition:0.3s;}
.dark-mode .sidebar{background:#1f1f1f;}
.sidebar h2{text-align:center;padding:15px;margin:0;border-bottom:1px solid #ccc;}
.user-list{flex:1;overflow-y:auto;}
.user-item{padding:12px 15px;cursor:pointer;display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid #ccc;}
.dark-mode .user-item{border-bottom:1px solid #333;color:#eee;}
.user-item:hover{background:#ddd;}
.dark-mode .user-item:hover{background:#2a2a2a;}
.active-dot{width:10px;height:10px;border-radius:50%;background:green;margin-left:10px;}

/* ----- Chat Panel ----- */
.chat-panel{flex:1;display:flex;flex-direction:column;background:#fff;}
.dark-mode .chat-panel{background:#121b22;}
.chat-header{padding:15px;border-bottom:1px solid #ccc;display:flex;justify-content:space-between;align-items:center;}
.dark-mode .chat-header{border-bottom:1px solid #333;}
.chat-messages{flex:1;padding:15px;overflow-y:auto;display:flex;flex-direction:column;gap:8px;}
.message{max-width:70%;padding:10px 14px;border-radius:20px;position:relative;word-wrap:break-word;display:inline-block;}
.message.self{align-self:flex-end;background:#dcf8c6;position:relative;}
.dark-mode .message.self{background:#054640;color:#fff;}
.message.other{align-self:flex-start;background:#fff;border:1px solid #ccc;position:relative;}
.dark-mode .message.other{background:#1f2c33;color:#fff;border:none;}
.timestamp{font-size:10px;color:#999;margin-top:4px;text-align:right;}
.read-receipt{font-size:12px;position:absolute;bottom:2px;right:6px;color:#999;}
.delete-btn{font-size:12px;color:red;margin-left:5px;cursor:pointer;}
.chat-input{display:flex;padding:10px;gap:8px;border-top:1px solid #ccc;align-items:center;}
.dark-mode .chat-input{border-top:1px solid #333;}
.chat-input input{flex:1;border-radius:20px;padding:10px;border:1px solid #ccc;}
.dark-mode .chat-input input{border:1px solid #555;background:#1f2c33;color:#fff;}
.chat-input button{padding:8px 15px;background:#0b93f6;color:#fff;font-weight:bold;border-radius:20px;}
.typing-indicator{font-size:12px;color:#555;margin-left:15px;}
.emoji-picker{display:flex;flex-wrap:wrap;gap:5px;padding:5px;background:#eee;border-radius:10px;max-height:100px;overflow-y:auto;display:none;}
.dark-mode .emoji-picker{background:#2a2a2a;}
.emoji{cursor:pointer;font-size:20px;}
.mode-toggle{cursor:pointer;padding:5px 10px;background:#0b93f6;color:#fff;border-radius:6px;}
</style>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-storage-compat.js"></script>
</head>
<body>
<div class="container">
  <!-- Sidebar -->
  <div class="sidebar">
    <h2>LiveConnect</h2>
    <div style="padding:10px;text-align:center;">
      <input type="text" id="search-user" placeholder="Search user...">
    </div>
    <div class="user-list" id="users-list"></div>
    <div style="padding:10px;text-align:center;">
      <button class="mode-toggle" onclick="toggleMode()">Dark/Light Mode</button>
    </div>
  </div>

  <!-- Chat Panel -->
  <div class="chat-panel">
    <div class="chat-header" id="chat-header">Select a user to chat</div>
    <div class="chat-messages" id="chat-messages"></div>
    <div class="typing-indicator" id="typing-indicator"></div>
    <div class="emoji-picker" id="emoji-picker"></div>
    <div class="chat-input">
      <button onclick="toggleEmojiPicker()">😀</button>
      <input type="text" id="message-input" placeholder="Type a message...">
      <button id="send-btn">Send</button>
      <input type="file" id="media-input" style="display:none">
      <button onclick="document.getElementById('media-input').click()">📎</button>
      <audio id="notify-sound" src="https://www.myinstants.com/media/sounds/facebook_messenger.mp3" preload="auto"></audio>
    </div>
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

let currentUser="",chatId="",typingTimeout;

// ----- Prompt Username -----
function loginPrompt(){
  let username=prompt("Enter your username:");
  if(!username) username="User"+Math.floor(Math.random()*1000);
  currentUser=username;
  db.ref("active_users/"+currentUser).set({online:true,lastSeen:Date.now()});
  db.ref("active_users/"+currentUser).onDisconnect().remove();
  loadUsers();
}
loginPrompt();

// ----- Dark/Light Mode -----
function toggleMode(){document.body.classList.toggle("dark-mode");}

// ----- Emoji Picker -----
const emojis=["😀","😂","😎","😍","😢","👍","🙏","🎉","💯","🔥"];
const picker=document.getElementById("emoji-picker");
emojis.forEach(e=>{
  const span=document.createElement("span");
  span.className="emoji"; span.textContent=e;
  span.onclick=()=>{document.getElementById("message-input").value+=e;};
  picker.appendChild(span);
});
function toggleEmojiPicker(){picker.style.display=(picker.style.display==="flex")?"none":"flex";picker.style.flexWrap="wrap";}

// ----- Load Active Users -----
function loadUsers(){
  db.ref("active_users").on("value",snap=>{
    const usersList=document.getElementById("users-list");
    usersList.innerHTML="";
    const users=snap.val()||{};
    for(let u in users){
      if(u!==currentUser){
        const div=document.createElement("div");
        div.className="user-item";
        const lastSeen=new Date(users[u].lastSeen).toLocaleTimeString();
        div.innerHTML=`${u} <span class="active-dot"></span> <span style="font-size:10px;color:#555;">Last: ${lastSeen}</span>`;
        div.onclick=()=>openChat(u);
        usersList.appendChild(div);
      }
    }
  });
}

// ----- Open Chat -----
function openChat(user){
  chatId=[currentUser,user].sort().join("_");
  document.getElementById("chat-header").textContent="Chat with "+user;
  document.getElementById("chat-messages").innerHTML="";
  db.ref("chat/"+chatId).on("child_added",snap=>{
    const msg=snap.val();
    const div=document.createElement("div");
    div.className="message "+(msg.user===currentUser?"self":"other");
    div.innerHTML=`<strong>${msg.user}</strong>: ${msg.text || ""}${msg.media?`<br>${msg.media.endsWith('.mp4')?'<video controls src="'+msg.media+'"></video>':'<img src="'+msg.media+'">':''}
    <div class="timestamp">${new Date(msg.time).toLocaleTimeString()}</div>
    <div class="read-receipt">${msg.read?"✓✓":"✓"}</div>
    ${msg.user===currentUser?'<span class="delete-btn" onclick="deleteMessage(\''+snap.key+'\')">🗑️</span>':''}`;
    document.getElementById("chat-messages").appendChild(div);
    document.getElementById("chat-messages").scrollTop=document.getElementById("chat-messages").scrollHeight;
    if(msg.user!==currentUser)document.getElementById("notify-sound").play();
  });

  // Typing indicator
  const input=document.getElementById("message-input");
  input.oninput=()=>{
    db.ref("typing/"+chatId+"/"+currentUser).set(true);
    clearTimeout(typingTimeout);
    typingTimeout=setTimeout(()=>{db.ref("typing/"+chatId+"/"+currentUser).remove();},2000);
  };

  db.ref("typing/"+chatId).on("value",snap=>{
    const data=snap.val()||{};
    const usersTyping=Object.keys(data).filter(u=>u!==currentUser);
    document.getElementById("typing-indicator").textContent=usersTyping.length>0?usersTyping.join(", ")+" is typing...":"";
  });
}

// ----- Send Message -----
document.getElementById("send-btn").onclick=()=>sendMessage();
document.getElementById("media-input").onchange=function(e){
  const file=e.target.files[0]; if(!file) return;
  const storageRef=storage.ref('chat_media/'+Date.now()+'_'+file.name);
  storageRef.put(file).then(snap=>snap.ref.getDownloadURL().then(url=>sendMessage(url)));
};
function sendMessage(mediaUrl=null){
  const text=document.getElementById("message-input").value.trim();
  if(!text && !mediaUrl) return;
  db.ref("chat/"+chatId).push({user:currentUser,to:chatId.replace(currentUser+"_","").replace("_"+currentUser,""),text:text,media:mediaUrl||"",time:Date.now(),read:false});
  document.getElementById("message-input").value="";
  db.ref("typing/"+chatId+"/"+currentUser).remove();
}

// ----- Delete Message -----
function deleteMessage(key){
  if(confirm("Delete this message?")) db.ref("chat/"+chatId+"/"+key).remove();
}
</script>
</body>
</html>
