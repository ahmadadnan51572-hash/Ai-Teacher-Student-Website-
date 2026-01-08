<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>School / Coaching ERP – Phase 1</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<!-- Firebase (GitHub Pages compatible) -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

<style>
body{font-family:Arial;background:#f2f4f7;margin:0}
header{background:#0a3d62;color:#fff;text-align:center;padding:15px}
nav{background:#34495e;text-align:center;padding:8px}
nav button{margin:4px;padding:8px 12px;border:0;border-radius:4px;background:#2c3e50;color:#fff}
section{display:none;padding:15px}
.card{background:#fff;padding:15px;border-radius:8px;margin-bottom:15px}
input,select,button{width:100%;padding:8px;margin:6px 0}
button{background:#1e3799;color:#fff;border:0;border-radius:4px}
.small{font-size:12px;color:#555}
</style>
</head>

<body>

<header>
<h2>Institute Management System</h2>
<p>Phase-1 • Login • Roles • Firebase</p>
</header>

<nav>
<button onclick="show('login')">Login</button>
<button onclick="show('signup')">Signup</button>
<button onclick="logout()">Logout</button>
</nav>

<!-- LOGIN -->
<section id="login" class="card">
<h3>Login</h3>
<select id="loginRole">
<option value="admin">Admin</option>
<option value="teacher">Teacher</option>
<option value="student">Student</option>
</select>
<input id="loginId" placeholder="User ID">
<input id="loginPass" type="password" placeholder="Password">
<button onclick="login()">Login</button>
<p id="loginMsg" class="small"></p>
</section>

<!-- SIGNUP -->
<section id="signup" class="card">
<h3>Signup</h3>
<select id="signupRole">
<option value="teacher">Teacher</option>
<option value="student">Student</option>
</select>
<input id="signupId" placeholder="Choose User ID">
<input id="signupName" placeholder="Full Name">
<input id="signupPass" type="password" placeholder="Password">
<button onclick="signup()">Create Account</button>
<p class="small">Admin account manually banega (Firebase me)</p>
</section>

<!-- PASSWORD CHANGE -->
<section id="changePass" class="card">
<h3>Change Password</h3>
<input id="newPass" type="password" placeholder="New Password">
<button onclick="changePassword()">Update Password</button>
</section>

<!-- ADMIN DASHBOARD -->
<section id="adminPanel" class="card">
<h3>Admin Dashboard</h3>
<p>Welcome, Admin</p>
<p class="small">Phase-2 me: teachers, classes, register</p>
<button onclick="show('changePass')">Change Password</button>
</section>

<!-- TEACHER DASHBOARD -->
<section id="teacherPanel" class="card">
<h3>Teacher Dashboard</h3>
<p id="teacherInfo"></p>
<p class="small">Phase-2 me: profile, students, attendance, notes</p>
<button onclick="show('changePass')">Change Password</button>
</section>

<!-- STUDENT DASHBOARD -->
<section id="studentPanel" class="card">
<h3>Student Dashboard</h3>
<p id="studentInfo"></p>
<p class="small">Phase-2 me: attendance, notes, AI doubt chat</p>
<button onclick="show('changePass')">Change Password</button>
</section>

<script>
/* 🔥 FIREBASE CONFIG – YAHAN APNA CONFIG PASTE KARO */
firebase.initializeApp({
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  databaseURL: "https://YOUR_PROJECT-default-rtdb.firebaseio.com",
  projectId: "YOUR_PROJECT",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "XXXX",
  appId: "XXXX"
});
const db = firebase.database();

/* GLOBAL STATE */
let currentUser = null;
let currentRole = null;

/* NAVIGATION */
function show(id){
 document.querySelectorAll("section").forEach(s=>s.style.display="none");
 document.getElementById(id).style.display="block";
}
show("login");

/* SIGNUP */
function signup(){
 let role = signupRole.value;
 let id = signupId.value;
 let name = signupName.value;
 let pass = signupPass.value;

 if(!id || !pass){
  alert("Fill all fields");
  return;
 }

 db.ref("users/"+id).set({
  role: role,
  name: name,
  password: pass
 });

 alert("Account Created. Now Login.");
 show("login");
}

/* LOGIN */
function login(){
 let role = loginRole.value;
 let id = loginId.value;
 let pass = loginPass.value;

 db.ref("users/"+id).once("value",snap=>{
  if(!snap.exists()){
   loginMsg.innerText="User not found";
   return;
  }
  let u = snap.val();
  if(u.password !== pass || u.role !== role){
   loginMsg.innerText="Invalid credentials";
   return;
  }

  currentUser = id;
  currentRole = role;

  if(role==="admin"){
   show("adminPanel");
  }
  if(role==="teacher"){
   teacherInfo.innerHTML = `<b>Name:</b> ${u.name}<br><b>ID:</b> ${id}`;
   show("teacherPanel");
  }
  if(role==="student"){
   studentInfo.innerHTML = `<b>Name:</b> ${u.name}<br><b>ID:</b> ${id}`;
   show("studentPanel");
  }
 });
}

/* PASSWORD CHANGE */
function changePassword(){
 if(!currentUser){
  alert("Login first");
  return;
 }
 db.ref("users/"+currentUser+"/password").set(newPass.value);
 alert("Password Updated");
 show(currentRole+"Panel");
}

/* LOGOUT */
function logout(){
 currentUser=null;
 currentRole=null;
 show("login");
}
</script>

</body>
</html>
