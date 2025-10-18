<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>SunsetStealer Login</title>
<style>
  body {
    margin: 0;
    padding: 0;
    overflow: hidden;
    font-family: "Poppins", sans-serif;
    background: black;
    color: white;
    height: 100vh;
  }
  canvas {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 0;
  }
  .container {
    position: relative;
    z-index: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }
  h1 {
    font-style: italic;
    font-size: 3em;
    background: linear-gradient(90deg, #00ff00, #00ffff, #00ff00);
    background-size: 300% 300%;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    animation: colorShift 4s infinite linear;
    margin-bottom: 1em;
  }
  @keyframes colorShift {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
  }
  .form-box {
    background: rgba(0, 0, 0, 0.7);
    border: 1px solid #00ff00;
    border-radius: 10px;
    padding: 2em;
    width: 320px;
    text-align: center;
    box-shadow: 0 0 20px #00ff00;
  }
  input, textarea, select {
    width: 100%;
    padding: 10px;
    margin: 10px 0;
    background: black;
    border: 1px solid #00ff00;
    color: #00ff00;
    border-radius: 5px;
  }
  button {
    width: 100%;
    padding: 10px;
    background: #00ff00;
    border: none;
    color: black;
    font-weight: bold;
    cursor: pointer;
    border-radius: 5px;
    margin-top: 10px;
  }
  button:hover { background: #00cc00; }
  a {
    display: block;
    color: #00ffff;
    margin-top: 10px;
    text-decoration: none;
  }
  .hidden { display: none; }
  .dashboard {
    text-align: center;
    margin-top: 3em;
  }
  .ad, .user {
    background: rgba(0, 255, 0, 0.1);
    border: 1px solid #00ff00;
    margin: 1em auto;
    padding: 1em;
    width: 260px;
    border-radius: 10px;
  }
  .admin-table {
    width: 90%;
    margin: 1em auto;
    border-collapse: collapse;
  }
  .admin-table th, .admin-table td {
    border: 1px solid #00ff00;
    padding: 5px;
    text-align: center;
  }
</style>
</head>
<body>

<canvas id="matrix"></canvas>
<div class="container">
  <h1>SunsetStealer</h1>

  <!-- LOGIN -->
  <div id="login" class="form-box">
    <h2>Login</h2>
    <input type="text" id="loginEmail" placeholder="Username or Email" />
    <input type="password" id="loginPassword" placeholder="Password" />
    <button onclick="login()">Login</button>
    <a href="#" onclick="showRegister()">Create account</a>
    <a href="#" onclick="showRecover()">Forgot password?</a>
  </div>

  <!-- REGISTER -->
  <div id="register" class="form-box hidden">
    <h2>Register</h2>
    <input type="text" id="regEmail" placeholder="Username or Email" />
    <input type="password" id="regPassword" placeholder="Password" />
    <button onclick="register()">Create Account</button>
    <a href="#" onclick="showLogin()">Back to login</a>
  </div>

  <!-- RECOVER -->
  <div id="recover" class="form-box hidden">
    <h2>Recover Password</h2>
    <input type="text" id="recEmail" placeholder="Username or Email" />
    <input type="text" id="recCode" placeholder="Recovery Code" />
    <input type="password" id="newPass" placeholder="New Password" />
    <button onclick="recover()">Reset Password</button>
    <a href="#" onclick="showLogin()">Back to login</a>
  </div>

  <!-- USER DASHBOARD -->
  <div id="dashboard" class="form-box hidden">
    <h2>User Dashboard</h2>
    <p>Balance: <span id="balance">0</span> KSH</p>
    <div id="messageBox" style="color:#00ffff;"></div>
    <div class="ad">
      <p><strong>Ad #1:</strong> CyberWorld Headphones</p>
      <button onclick="watchAd()">Watch Ad</button>
    </div>
    <div class="ad">
      <p><strong>Ad #2:</strong> Quantum Phone</p>
      <button onclick="watchAd()">Watch Ad</button>
    </div>
    <button onclick="logout()">Logout</button>
  </div>

  <!-- ADMIN DASHBOARD -->
  <div id="adminDashboard" class="form-box hidden" style="width:90%; max-width:900px;">
    <h2>Admin Dashboard</h2>
    <table class="admin-table" id="userTable">
      <tr>
        <th>User</th><th>Balance</th><th>Status</th><th>Actions</th>
      </tr>
    </table>
    <h3>Send Message</h3>
    <select id="msgUser"></select>
    <textarea id="msgText" placeholder="Enter message"></textarea>
    <button onclick="sendMessage()">Send</button>
    <button onclick="logout()">Logout</button>
  </div>
</div>

<script>
// ---------- MATRIX BACKGROUND ----------
const canvas = document.getElementById("matrix");
const ctx = canvas.getContext("2d");
canvas.height = window.innerHeight;
canvas.width = window.innerWidth;
const letters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789@#$%^&*()";
const fontSize = 16;
const columns = canvas.width / fontSize;
const drops = Array(Math.floor(columns)).fill(1);
function drawMatrix() {
  ctx.fillStyle = "rgba(0, 0, 0, 0.05)";
  ctx.fillRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "#0F0";
  ctx.font = fontSize + "px monospace";
  for (let i = 0; i < drops.length; i++) {
    const text = letters.charAt(Math.floor(Math.random() * letters.length));
    ctx.fillText(text, i * fontSize, drops[i] * fontSize);
    if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) drops[i] = 0;
    drops[i]++;
  }
}
setInterval(drawMatrix, 33);

// ---------- FORM SWITCH ----------
function showLogin() {
  ["login","register","recover","dashboard","adminDashboard"].forEach(id=>document.getElementById(id).classList.add("hidden"));
  document.getElementById("login").classList.remove("hidden");
}
function showRegister() {
  document.getElementById("login").classList.add("hidden");
  document.getElementById("register").classList.remove("hidden");
}
function showRecover() {
  document.getElementById("login").classList.add("hidden");
  document.getElementById("recover").classList.remove("hidden");
}

// ---------- REGISTER / LOGIN / RECOVER ----------
function register() {
  const email = regEmail.value.trim();
  const pass = regPassword.value.trim();
  if (!email || !pass) return alert("Please fill all fields");
  if (email === "sunsetstealer") return alert("Reserved for admin");
  const code = Math.random().toString(36).substring(2, 8).toUpperCase();
  const user = { email, pass, code, balance: 0, active: false, message: "" };
  localStorage.setItem(email, JSON.stringify(user));
  alert("Account created! Your recovery code: " + code);
  showLogin();
}

// Admin auto registration (if missing)
if (!localStorage.getItem("sunsetstealer")) {
  const admin = { email: "sunsetstealer", pass: "lonasoka2025.", role: "admin" };
  localStorage.setItem("sunsetstealer", JSON.stringify(admin));
}

function login() {
  const email = loginEmail.value.trim();
  const pass = loginPassword.value.trim();

  // ADMIN CHECK
  if (email === "sunsetstealer" && pass === "lonasoka2025.") {
    sessionStorage.setItem("currentUser", "sunsetstealer");
    showAdminDashboard();
    return;
  }

  const userData = localStorage.getItem(email);
  if (!userData) return alert("No such account (register first)");
  const user = JSON.parse(userData);
  if (user.pass !== pass) return alert("Wrong password");
  user.active = true;
  localStorage.setItem(email, JSON.stringify(user));
  sessionStorage.setItem("currentUser", email);
  showDashboard();
}

function recover() {
  const email = recEmail.value.trim();
  const code = recCode.value.trim();
  const newPass = newPass.value.trim();
  const userData = localStorage.getItem(email);
  if (!userData) return alert("User not found");
  const user = JSON.parse(userData);
  if (user.code !== code) return alert("Invalid recovery code");
  user.pass = newPass;
  localStorage.setItem(email, JSON.stringify(user));
  alert("Password reset successfully!");
  showLogin();
}

// ---------- USER DASHBOARD ----------
function showDashboard() {
  ["login","register","recover","adminDashboard"].forEach(id=>document.getElementById(id).classList.add("hidden"));
  dashboard.classList.remove("hidden");
  const email = sessionStorage.getItem("currentUser");
  const user = JSON.parse(localStorage.getItem(email));
  document.getElementById("balance").innerText = user.balance;
  if (user.message) {
    messageBox.innerHTML = `<strong>Message:</strong> ${user.message}`;
    user.message = "";
    localStorage.setItem(email, JSON.stringify(user));
  }
}

function watchAd() {
  const email = sessionStorage.getItem("currentUser");
  const user = JSON.parse(localStorage.getItem(email));
  user.balance += 5;
  localStorage.setItem(email, JSON.stringify(user));
  document.getElementById("balance").innerText = user.balance;
  alert("You watched an ad and earned 5 KSH (demo)");
}

function logout() {
  const email = sessionStorage.getItem("currentUser");
  if (email && email !== "sunsetstealer") {
    const user = JSON.parse(localStorage.getItem(email));
    user.active = false;
    localStorage.setItem(email, JSON.stringify(user));
  }
  sessionStorage.removeItem("currentUser");
  showLogin();
}

// ---------- ADMIN DASHBOARD ----------
function showAdminDashboard() {
  ["login","register","recover","dashboard"].forEach(id=>document.getElementById(id).classList.add("hidden"));
  adminDashboard.classList.remove("hidden");
  refreshUsers();
}

function refreshUsers() {
  const table = document.getElementById("userTable");
  const select = document.getElementById("msgUser");
  table.innerHTML = `<tr><th>User</th><th>Balance</th><th>Status</th><th>Actions</th></tr>`;
  select.innerHTML = "";
  Object.keys(localStorage).forEach(key => {
    if (key === "sunsetstealer") return;
    const user = JSON.parse(localStorage.getItem(key));
    if (!user.email) return;
    const tr = document.createElement("tr");
    tr.innerHTML = `
      <td>${user.email}</td>
      <td>${user.balance}</td>
      <td style="color:${user.active?'#00ff00':'#ff0000'};">${user.active?'Active':'Offline'}</td>
      <td>
        <button onclick="changeBalance('${user.email}',5)">+5</button>
        <button onclick="changeBalance('${user.email}',-5)">-5</button>
      </td>
    `;
    table.appendChild(tr);
    const opt = document.createElement("option");
    opt.value = user.email;
    opt.textContent = user.email;
    select.appendChild(opt);
  });
}

function changeBalance(email, amount) {
  const user = JSON.parse(localStorage.getItem(email));
  user.balance += amount;
  if (user.balance < 0) user.balance = 0;
  localStorage.setItem(email, JSON.stringify(user));
  refreshUsers();
}

function sendMessage() {
  const email = document.getElementById("msgUser").value;
  const msg = document.getElementById("msgText").value.trim();
  if (!email || !msg) return alert("Select a user and write a message");
  const user = JSON.parse(localStorage.getItem(email));
  user.message = msg;
  localStorage.setItem(email, JSON.stringify(user));
  alert("Message sent!");
  document.getElementById("msgText").value = "";
}
</script>
</body>
</html>
