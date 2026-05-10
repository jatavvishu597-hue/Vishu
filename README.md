<!DOCTYPE html><html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>XN PAY - Full App</title><link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css"/>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore-compat.js"></script>

<style>
    body{margin:0;font-family:Arial;background:#f8fafc;}
    .container{max-width:450px;margin:auto;background:#fff;min-height:100vh;}
    .hidden{display:none;}

    .center{padding:30px;text-align:center;}
    .logo{width:80px;height:80px;background:#10b981;color:#fff;border-radius:20px;margin:auto;display:flex;align-items:center;justify-content:center;font-size:30px;font-weight:bold;}
    .title{color:#10b981;font-size:22px;margin:10px 0;}

    input{width:100%;padding:12px;margin:8px 0;border:1px solid #ddd;border-radius:10px;}
    button{width:100%;padding:12px;border:none;border-radius:10px;font-size:16px;cursor:pointer;margin-top:8px;}
    .btn{background:#10b981;color:#fff;}
    .btn-dark{background:#334155;color:#fff;}

    .header{background:#10b981;color:#fff;padding:15px;display:flex;justify-content:space-between;align-items:center;}
    .card{margin:15px;padding:15px;border:1px solid #eee;border-radius:12px;}
</style>

</head>
<body><div class="container"><!-- LOGIN -->
<div id="authPage" class="center">
    <div class="logo">XN</div>
    <div class="title">XN PAY</div>

    <input id="email" type="email" placeholder="Email" />
    <input id="password" type="password" placeholder="Password" />

    <button class="btn" onclick="login()">Login</button>
    <button class="btn-dark" onclick="signup()">Sign Up</button>
</div>

<!-- DASHBOARD -->
<div id="appPage" class="hidden">

    <div class="header">
        <b>XN PAY</b>
        <i class="fa fa-sign-out" onclick="logout()" style="cursor:pointer"></i>
    </div>

    <div class="card">
        <h3 id="userEmail">User</h3>
        <p>Wallet Balance: ₹ <span id="balance">0</span></p>
    </div>

    <div class="card">
        <button class="btn">Buy Coin</button>
        <button class="btn-dark">Sell Coin</button>
    </div>

</div>

</div><script>

// Firebase Config (replace)
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
};

firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db = firebase.firestore();

// Auth state
auth.onAuthStateChanged(async (user) => {
    if(user){
        document.getElementById('authPage').classList.add('hidden');
        document.getElementById('appPage').classList.remove('hidden');

        document.getElementById('userEmail').innerText = user.email;

        // create user doc if not exists
        const ref = db.collection('users').doc(user.uid);
        const doc = await ref.get();

        if(!doc.exists){
            await ref.set({
                email: user.email,
                balance: 1000
            });
        }

        loadUserData(user.uid);
    } else {
        document.getElementById('authPage').classList.remove('hidden');
        document.getElementById('appPage').classList.add('hidden');
    }
});

async function loadUserData(uid){
    const doc = await db.collection('users').doc(uid).get();
    document.getElementById('balance').innerText = doc.data().balance;
}

function login(){
    const email = document.getElementById('email').value;
    const pass = document.getElementById('password').value;

    auth.signInWithEmailAndPassword(email, pass)
    .catch(e => alert(e.message));
}

function signup(){
    const email = document.getElementById('email').value;
    const pass = document.getElementById('password').value;

    auth.createUserWithEmailAndPassword(email, pass)
    .catch(e => alert(e.message));
}

function logout(){
    auth.signOut();
}

</script></body>
</html>
