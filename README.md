<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LiveConnect Chat</title>

<style>
body { font-family: Arial, sans-serif; background: #f5f7fb; padding: 20px; }
.chat-container { max-width: 400px; margin: auto; background: #fff; padding: 20px; border-radius: 10px; }
.chat-box { height: 300px; overflow-y: scroll; border: 1px solid #ccc; padding: 10px; margin-bottom: 10px; border-radius: 6px; background:#fafafa; }
input { width: 70%; padding: 8px; }
button { padding: 8px 12px; }
h1 { margin-bottom: 5px; }
p { margin-top: 0; color: #555; }
</style>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

</head>
<body>

<div class="chat-container">
  <h1>LiveConnect</h1>
  <p>Manage real-time customer conversations easily</p>

  <div class="chat-box" id="chat-box"></div>

  <input type="text" id="message" placeholder="Type your message...">
  <button onclick="sendMessage()">Send</button>
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

// Send Message
function sendMessage() {
  const msgInput = document.getElementById("message");
  const msg = msgInput.value;
  if(msg.trim() === "") return;

  db.ref("messages").push().set({
    message: msg,
    timestamp: Date.now()
  });

  msgInput.value = "";
}

// Listen for new messages
const chatBox = document.getElementById("chat-box");
db.ref("messages").on("child_added", function(snapshot){
  const data = snapshot.val();
  const msgDiv = document.createElement("div");
  msgDiv.textContent = data.message;
  chatBox.appendChild(msgDiv);
  chatBox.scrollTop = chatBox.scrollHeight;
});
</script>

</body>
</html>
