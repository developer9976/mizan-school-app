<!DOCTYPE html>
<html>
<head>
  <title>Mizan School App</title>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-auth-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <h1>Mizan School Parent Link</h1>

  <div id="auth-section">
    <h3>Login / Signup</h3>
    <input type="email" id="email" placeholder="Email" required />
    <input type="password" id="password" placeholder="Password" required />
    <button onclick="login()">Login</button>
    <button onclick="signup()">Sign Up</button>
    <p id="auth-msg"></p>
  </div>

  <div id="form-section" style="display:none;">
    <h3>Submit Student Info</h3>
    <input type="text" id="studentName" placeholder="Student Name" />
    <input type="text" id="schoolName" placeholder="School Name (e.g., T/Haymanot)" />
    <input type="text" id="parentPhone" placeholder="Parent Phone Number" />
    <button onclick="submitData()">Submit</button>
    <p id="success-msg"></p>
  </div>

  <script src="script.js"></script>
</body>
</html>
body {
  font-family: Arial;
  padding: 20px;
  background-color: #f4f4f4;
}
input, button {
  padding: 10px;
  margin: 10px 0;
  width: 300px;
  display: block;
}
button {
  background: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}
button:hover {
  background: #0056b3;
}
// 🔐 Replace this with YOUR real Firebase config
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  databaseURL: "https://YOUR_PROJECT_ID.firebaseio.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "SENDER_ID",
  appId: "APP_ID"
};

// ✅ Initialize Firebase
firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db = firebase.database();

// 🔑 Login
function login() {
  const email = document.getElementById('email').value;
  const pass = document.getElementById('password').value;
  auth.signInWithEmailAndPassword(email, pass)
    .then(() => {
      showForm();
    })
    .catch(error => {
      document.getElementById('auth-msg').innerText = "Login error: " + error.message;
    });
}

// 🆕 Signup
function signup() {
  const email = document.getElementById('email').value;
  const pass = document.getElementById('password').value;
  auth.createUserWithEmailAndPassword(email, pass)
    .then(() => {
      document.getElementById('auth-msg').innerText = "Signup successful. Please login.";
    })
    .catch(error => {
      document.getElementById('auth-msg').innerText = "Signup error: " + error.message;
    });
}

// 💾 Save Data
function submitData() {
  const name = document.getElementById('studentName').value;
  const school = document.getElementById('schoolName').value;
  const phone = document.getElementById('parentPhone').value;

  db.ref("schools/" + school + "/students").push({
    name: name,
    parentPhone: phone
  }).then(() => {
    document.getElementById('success-msg').innerText = "Saved successfully!";
  }).catch(error => {
    document.getElementById('success-msg').innerText = "Error: " + error.message;
  });
}

// 🔓 Show form if logged in
auth.onAuthStateChanged(user => {
  if (user) {
    showForm();
  }
});

function showForm() {
  document.getElementById('auth-section').style.display = "none";
  document.getElementById('form-section').style.display = "block";
}
