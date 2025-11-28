<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Wallet App</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
*{margin:0;padding:0;box-sizing:border-box;font-family:Arial;}
body{background:#f3f4f7;}
.container{max-width:400px;margin:40px auto;background:#fff;padding:25px;border-radius:15px;
box-shadow:0 4px 20px rgba(0,0,0,0.1);}
.title{font-size:26px;font-weight:700;text-align:center;margin-bottom:20px;}
input{width:100%;padding:12px;margin:8px 0;border:1px solid #ddd;border-radius:10px;
font-size:16px;}
button{width:100%;padding:12px;margin-top:10px;border:none;border-radius:10px;
background:#4A63E7;color:#fff;font-size:18px;font-weight:600;}
.link{text-align:center;margin-top:10px;color:#4A63E7;cursor:pointer;}
.wallet-box{background:#4A63E7;padding:25px;border-radius:15px;color:#fff;text-align:center;margin-bottom:20px;}
.wallet-amount{font-size:40px;font-weight:bold;}
.hidden{display:none;}
</style>
</head>

<body>

<div class="container" id="loginPage">
  <div class="title">Login</div>

  <input type="number" id="loginMobile" placeholder="Mobile">
  <input type="password" id="loginPassword" placeholder="Password">

  <button onclick="login()">Login</button>
</div>

<div class="container hidden" id="walletPage">
  <div class="wallet-box">
    <div style="opacity:.8">Wallet Balance</div>
    <div class="wallet-amount" id="walletAmount">₹0</div>
  </div>

  <input type="number" id="addMoneyInput" placeholder="Enter amount to add">
  <button onclick="addMoney()">Add Money</button>

  <div class="link" onclick="logout()">Logout</div>
</div>

<script>
const API = "https://script.google.com/macros/s/AKfycbxdHa3EtC6HKsGwNsrxU0LOFmPOcFsBvviua9wWeLEImejlU9kvTjdRRjw3-1A3piyN7Q/exec";

let currentMobile = "";

// ---------- LOGIN ----------
async function login(){
  let mobile = document.getElementById("loginMobile").value.trim();
  let password = document.getElementById("loginPassword").value.trim();

  if(mobile=="" || password==""){
    alert("Fill all fields");
    return;
  }

  let res = await fetch(API,{
    method:"POST",
    body:JSON.stringify({
      action:"login",
      mobile:mobile,
      password:password
    })
  });

  let data = await res.json();
  console.log(data);

  if(data.success){
    currentMobile = mobile;
    document.getElementById("walletAmount").innerText = "₹" + (data.wallet ?? 0);
    showWallet();
  }else{
    alert(data.message);
  }
}

// ---------- ADD MONEY ----------
async function addMoney(){
  let amount = document.getElementById("addMoneyInput").value;

  if(amount=="" || amount<=0){
    alert("Enter valid amount");
    return;
  }

  let res = await fetch(API,{
    method:"POST",
    body:JSON.stringify({
      action:"addMoney",
      mobile:currentMobile,
      amount:amount
    })
  });

  let data = await res.json();
  console.log(data);

  if(data.success){
    document.getElementById("walletAmount").innerText = "₹" + data.wallet;
    alert("Money added!");
    document.getElementById("addMoneyInput").value = "";
  }else{
    alert(data.message);
  }
}

// ---------- UI SWITCH ----------
function showWallet(){
  document.getElementById("loginPage").classList.add("hidden");
  document.getElementById("walletPage").classList.remove("hidden");
}

function logout(){
  location.reload();
}
</script>

</body>
</html>
