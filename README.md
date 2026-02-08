<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect Multi-User Chat</title>

<style>
body { font-family: Arial, sans-serif; background: #f5f7fb; padding: 20px; }
.chat-container { max-width: 500px; margin: auto; background: #fff; padding: 20px; border-radius: 10px; }
.stats { display: flex; justify-content: space-between; margin-bottom: 15px; }
.stat-box { background: #e7f0ff; padding: 10px; border-radius: 6px; width: 48%; text-align: center; }
.chat-box { height: 300px; overflow-y: scroll; border: 1px solid #ccc; padding: 10px; margin-bottom: 10px; border-radius: 6px; background:#fafafa; }
input { width: 70%; padding: 8px; }
button { padding: 8px 12px; margin-left: 5px; }
h1 { margin-bottom: 5px; text-align:center; }
p { margin-top: 0; color: #555; text-align:center; }
.message { margin-bottom:5px; padding:5px; border-radius:4px; background:#d9f0d9; }
.user-message { background:#f0d9d9; text-align:right; }
</style>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

</head>
<body>

<div class="chat-container">
  <h1>LiveConnect</h1>
  <p>Manage real-time multi-user chats easily</p>

  <!-- Stats -->
  <div class="stats">
    <div class="stat-box">Active Chats: <span id="active-chats">0</span></div>
    <div class="stat-box">Waiting Users: <span id="waiting-users">0</span></div>
  </div>

  <!-- Chat Box -->
  <div class="chat-box" id="chat-box"></div>

  <!-- Message Input -->
  <input type="text" id="message" placeholder="Type your message...">
  <button onclick="sendMessage()">Send</button>
  <button onclick="startChat()">Start Chat</button>
  <button onclick="endChat()">End Chat</button>
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

// Initialize Firebase
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

// User identifier
let currentUser = "User" + Math.floor(Math.random()*1000);
let activeChatRef = null;

// Send Message
function sendMessage() {
  const msgInput = document.getElementById("message");
  const msg = msgInput.value.trim();
  if(!msg || !activeChatRef) return;

  db.ref(activeChatRef + "/messages").push({
    user: currentUser,
    message: msg,
    timestamp: Date.now()
  });
  msgInput.value = "";
}

// Start Chat
function startChat() {
  activeChatRef = "chat_" + Date.now();
  db.ref("active_chats/" + activeChatRef).set({
    user: currentUser,
    start: Date.now()
  });
  updateStats();
}

// End Chat
function endChat() {
  if(!activeChatRef) return;
  db.ref("active_chats/" + activeChatRef).remove();
  activeChatRef = null;
  updateStats();
}

// Listen for all messages in all chats
db.ref("active_chats").on("child_added", function(snapshot){
  const chatId = snapshot.key;
  db.ref("active_chats/" + chatId + "/messages").on("child_added", function(msgSnap){
    const data = msgSnap.val();
    const chatBox = document.getElementById("chat-box");
    const msgDiv = document.createElement("div");
    msgDiv.textContent = data.user + ": " + data.message;
    msgDiv.className = data.user === currentUser ? "message user-message" : "message";
    chatBox.appendChild(msgDiv);
    chatBox.scrollTop = chatBox.scrollHeight;
  });
  updateStats();
});

db.ref("active_chats").on("child_removed", updateStats);

// Update Stats
function updateStats() {
  db.ref("active_chats").once("value", function(snapshot){
    const val = snapshot.val() || {};
    document.getElementById("active-chats").textContent = Object.keys(val).length;
    document.getElementById("waiting-users").textContent = Object.keys(val).length; // same for simplicity
  });
}
</script>

</body>
</html>
