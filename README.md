<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>เครื่องมือคำนวณ</title>

<style>
body{
    margin:0;
    font-family:Segoe UI, sans-serif;
    background:#f6f2e9;
    display:flex;
    justify-content:center;
    align-items:center;
    height:100vh;
}

/* กระดาษ */
.container{
    width:380px;
    height:520px; /* 🔥 ล็อคขนาด */
    background:#fffdf8;
    border-radius:12px;
    box-shadow:0 10px 25px rgba(0,0,0,0.05);
    border:1px solid #e8e2d3;
    padding:20px;
    position:relative;
    display:flex;
    flex-direction:column;
}

/* เส้นข้าง */
.container::before{
    content:"";
    position:absolute;
    left:0;
    top:0;
    bottom:0;
    width:4px;
    background:#e6dcc7;
}

/* พื้นที่เนื้อหา */
.content{
    flex:1;
    overflow:hidden;
}

h2{
    text-align:center;
    margin:0 0 10px;
    font-weight:500;
    color:#5a5448;
}

/* หน้า */
.section{display:none;}
.active{display:block;}

input{
    width:100%;
    padding:10px;
    margin-top:8px;
    border:none;
    border-bottom:1px solid #ddd;
    background:transparent;
}

button{
    width:100%;
    margin-top:10px;
    padding:10px;
    border:none;
    border-radius:8px;
    background:#e9e2d2;
    cursor:pointer;
}

.back{background:transparent; text-align:left;}
.clear{background:#f3ebe0;}

.result{
    display:none;
    margin-top:10px;
    padding:10px;
    background:#faf7f1;
    border-left:3px solid #d8cdb8;
    font-size:14px;
}

.row{display:flex; gap:10px;}
.row input{flex:1;}

</style>
</head>

<body>

<div class="container">

<h2>เครื่องมือคำนวณ</h2>

<div class="content">

<!-- HOME -->
<div id="home" class="section active">
<button onclick="openMode(1)">เปรียบเทียบราคา</button>
<button onclick="openMode(2)">เทียบราคาต่อหน่วย</button>
<button onclick="openMode(3)">คำนวณกำไร</button>
</div>

<!-- MODE 1 -->
<div id="mode1" class="section">
<button class="back" onclick="goHome()">← กลับ</button>

<input id="p1" type="number" placeholder="ราคาสินค้า 1">
<input id="p2" type="number" placeholder="ราคาสินค้า 2">

<button onclick="comparePrice()">คำนวณ</button>
<button class="clear" onclick="clearMode(1)">เคลียร์</button>

<div id="r1" class="result"></div>
</div>

<!-- MODE 2 -->
<div id="mode2" class="section">
<button class="back" onclick="goHome()">← กลับ</button>

<div class="row">
<input id="p3" type="number" placeholder="ราคา 1">
<input id="q1" type="number" placeholder="ปริมาณ 1">
</div>

<div class="row">
<input id="p4" type="number" placeholder="ราคา 2">
<input id="q2" type="number" placeholder="ปริมาณ 2">
</div>

<button onclick="compareUnit()">คำนวณ</button>
<button class="clear" onclick="clearMode(2)">เคลียร์</button>

<div id="r2" class="result"></div>
</div>

<!-- MODE 3 -->
<div id="mode3" class="section">
<button class="back" onclick="goHome()">← กลับ</button>

<input id="cost" type="number" placeholder="ต้นทุน">
<input id="sell" type="number" placeholder="ราคาขาย">

<button onclick="calcProfit()">คำนวณ</button>
<button class="clear" onclick="clearMode(3)">เคลียร์</button>

<div id="r3" class="result"></div>
</div>

</div>
</div>

<script>

// ===== หน้า =====
function openMode(n){
    hideAll();
    document.getElementById("mode"+n).classList.add("active");
    localStorage.setItem("lastMode", n);
}

function goHome(){
    hideAll();
    document.getElementById("home").classList.add("active");
}

function hideAll(){
    document.querySelectorAll(".section").forEach(s=>s.classList.remove("active"));
}

window.onload=()=>{
    let last=localStorage.getItem("lastMode");
    if(last) openMode(last);
};

// ===== MODE 1 =====
function comparePrice(){
    let box=document.getElementById("r1");

    let p1=parseFloat(document.getElementById("p1").value);
    let p2=parseFloat(document.getElementById("p2").value);

    box.style.display="block";

    if(!p1||!p2){
        box.innerText="กรอกข้อมูลให้ครบ";
        return;
    }

    if(p1===p2){
        box.innerHTML="<strong>สินค้าราคาเท่ากัน</strong>";
        return;
    }

    let diff=Math.abs(p1-p2);
    let percent=diff/Math.max(p1,p2)*100;
    let text=p1<p2?"สินค้า 1 ถูกกว่า":"สินค้า 2 ถูกกว่า";

    box.innerHTML=`<strong>${text}</strong><br>
    ต่างกัน ${diff.toFixed(2)} บาท (${percent.toFixed(2)}%)`;
}

// ===== MODE 2 =====
function compareUnit(){
    let box=document.getElementById("r2");

    let p1=parseFloat(document.getElementById("p3").value);
    let q1=parseFloat(document.getElementById("q1").value);
    let p2=parseFloat(document.getElementById("p4").value);
    let q2=parseFloat(document.getElementById("q2").value);

    box.style.display="block";

    if(!p1||!q1||!p2||!q2){
        box.innerText="กรอกข้อมูลให้ครบ";
        return;
    }

    let u1=p1/q1;
    let u2=p2/q2;

    if(u1===u2){
        box.innerHTML="<strong>คุ้มค่าเท่ากัน</strong>";
        return;
    }

    let percent=Math.abs(u1-u2)/Math.max(u1,u2)*100;
    let text=u1<u2?"สินค้า 1 คุ้มกว่า":"สินค้า 2 คุ้มกว่า";

    box.innerHTML=`<strong>${text}</strong><br>
    ต่างกัน ${percent.toFixed(2)}%<br>
    ${u1.toFixed(2)} vs ${u2.toFixed(2)}`;
}

// ===== MODE 3 =====
function calcProfit(){
    let box=document.getElementById("r3");

    let cost=parseFloat(document.getElementById("cost").value);
    let sell=parseFloat(document.getElementById("sell").value);

    box.style.display="block";

    if(!cost||!sell){
        box.innerText="กรอกข้อมูลให้ครบ";
        return;
    }

    let profit=sell-cost;
    let percent=profit/cost*100;

    if(profit<0){
        box.innerHTML=`<strong>ขาดทุน</strong><br>
        ${Math.abs(profit).toFixed(2)} บาท (${Math.abs(percent).toFixed(2)}%)`;
    }else{
        box.innerHTML=`<strong>กำไร</strong><br>
        ${profit.toFixed(2)} บาท (${percent.toFixed(2)}%)`;
    }
}

// ===== CLEAR =====
function clearMode(n){
    if(n===1){
        document.getElementById("p1").value="";
        document.getElementById("p2").value="";
        document.getElementById("r1").style.display="none";
    }
    if(n===2){
        ["p3","q1","p4","q2"].forEach(id=>document.getElementById(id).value="");
        document.getElementById("r2").style.display="none";
    }
    if(n===3){
        document.getElementById("cost").value="";
        document.getElementById("sell").value="";
        document.getElementById("r3").style.display="none";
    }
}

</script>

</body>
</html>
