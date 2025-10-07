
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>منصة مكافحة الابتزاز الإلكتروني</title>
<meta name="description" content="منصة مكافحة الابتزاز الإلكتروني – قدّم بلاغك بأمان وسرية كاملة.">
<meta name="keywords" content="مكافحة الابتزاز, الابتزاز الإلكتروني, بلاغات, أمن, حماية, WhatsApp">
<meta name="author" content="منظمة اللواء القاعدة الجوية 590">
<style>
:root {
  --gold: #ffd700;
  --cyan: #19e6d8;
  --pink: #ff4eb3;
  --bg-color: rgba(10,10,10,0.85);
  --text-color: #f0f0f0;
  --glow: rgba(255,215,0,0.5);
}

* {box-sizing:border-box;}
body {
  margin:0;
  font-family:"Segoe UI",Tahoma,Arial,sans-serif;
  background:#000;
  color:var(--text-color);
}

canvas.bgCanvas {
  position:fixed; top:0; left:0;
  width:100%; height:100%;
  z-index:0;
}

.container {
  position:relative; z-index:1;
  max-width:600px;
  margin:50px auto;
  padding:30px 25px;
  border-radius:15px;
  background:var(--bg-color);
  box-shadow:0 0 20px var(--gold), 0 0 40px var(--cyan);
  text-align:center;
  transition: all 0.3s ease;
}

h1 {
  font-size:2rem;
  color:var(--gold);
  text-shadow:0 0 15px var(--glow),0 0 30px var(--cyan);
  margin-bottom:20px;
}

p#welcomeText, p#welcomeText2 {
  margin-bottom:25px;
  font-size:1.1rem;
  line-height:1.6;
  color:#bfeff0;
  min-height:100px;
}

button {
  padding:15px 35px;
  font-size:1.2rem;
  border:none;
  border-radius:12px;
  background:linear-gradient(45deg,var(--gold),var(--cyan),var(--pink));
  color:#000;
  font-weight:bold;
  cursor:pointer;
  text-shadow:0 0 5px var(--glow);
  transition: all 0.3s ease;
  margin-bottom:15px;
}

button:hover {
  transform:scale(1.05);
  box-shadow:0 0 20px var(--gold),0 0 30px var(--pink),0 0 40px var(--cyan);
}

#reportPage {display:none; margin-top:20px;}

input, textarea {
  width:100%;
  padding:12px;
  margin-bottom:15px;
  border-radius:10px;
  border:2px solid rgba(255,255,255,0.2);
  background:transparent;
  color:var(--text-color);
  font-size:1rem;
  transition: all 0.3s ease;
}

input:focus, textarea:focus {border-color:var(--gold); box-shadow:0 0 10px var(--glow);}
input::placeholder, textarea::placeholder {color:#bfeff0;}

#status {margin-top:15px; font-weight:bold; font-size:1rem;}

.cell {
  background: var(--bg-color);
  border:2px solid var(--gold);
  border-radius:12px;
  margin-bottom:15px;
  padding:15px;
  box-shadow:0 0 10px rgba(0,0,0,0.5);
  opacity:0; transform:scale(0.8); transition: all 0.4s ease;
}
.cell.show {opacity:1; transform:scale(1);}
.cell span.label {font-weight:bold; display:block; color:var(--cyan);}
.cell span.value {display:block; color:#bfeff0; margin-top:5px;}

/* تجاوب للشاشات الصغيرة */
@media (max-width:768px){
  .container {margin:30px 15px; padding:20px;}
  h1 {font-size:1.8rem;}
  button {padding:12px 25px; font-size:1rem;}
  p#welcomeText, p#welcomeText2 {font-size:1rem;}
}
@media (max-width:480px){
  h1 {font-size:1.5rem;}
  button {padding:10px 20px; font-size:0.9rem;}
  input, textarea {font-size:0.95rem;}
}
</style>
</head>
<body>

<canvas class="bgCanvas" id="bgCanvas"></canvas>

<div class="container" id="homePage">
  <h1>منظمة اللواء القاعدة الجوية 590</h1>
  <p id="welcomeText"></p>
  <button id="openForm">تقديم بلاغ</button>
</div>

<div class="container" id="reportPage">
  <h1>تقديم البلاغ</h1>
  <p id="welcomeText2"></p>
  <div id="boxesContainer"></div>
  <form id="reportForm" action="https://formspree.io/f/xgvnzjnw" method="POST">
    <input type="text" name="name" id="nameInput" placeholder="اسمك الثلاثي" required>
    <input type="text" name="phone" id="phoneInput" placeholder="رقم الواتس للتواصل" required>
    <input type="hidden" name="date" id="dateInput">
    <button type="submit">إرسال البلاغ</button>
  </form>
  <div id="status"></div>
</div>

<script>
// خلفية متحركة
const canvasBG = document.getElementById('bgCanvas');
const ctxBG = canvasBG.getContext('2d');
let w = canvasBG.width = window.innerWidth;
let h = canvasBG.height = window.innerHeight;
window.addEventListener('resize',()=>{w=canvasBG.width=window.innerWidth; h=canvasBG.height=window.innerHeight;});
const stars=[]; const orbs=[];
for(let i=0;i<150;i++) stars.push({x:Math.random()*w, y:Math.random()*h, r:Math.random()*2+1, dx:(Math.random()-0.5)*0.4, dy:(Math.random()-0.5)*0.4});
for(let i=0;i<30;i++) orbs.push({x:Math.random()*w, y:Math.random()*h, r:Math.random()*15+7, dx:(Math.random()-0.5)*0.3, dy:(Math.random()-0.5)*0.3, a:Math.random()});
function animateBG(){
  ctxBG.fillStyle='rgba(0,0,0,0.25)'; ctxBG.fillRect(0,0,w,h);
  stars.forEach(s=>{ctxBG.beginPath();ctxBG.arc(s.x,s.y,s.r,0,Math.PI*2);ctxBG.fillStyle='white';ctxBG.fill(); s.x+=s.dx; s.y+=s.dy; if(s.x<0)s.x=w;if(s.x>w)s.x=0;if(s.y<0)s.y=h;if(s.y>h)s.y=0;});
  orbs.forEach(o=>{const grd=ctxBG.createRadialGradient(o.x,o.y,0,o.x,o.y,o.r*3); grd.addColorStop(0,`rgba(255,215,0,${0.15*o.a})`); grd.addColorStop(1,'rgba(0,0,0,0)'); ctxBG.fillStyle=grd; ctxBG.beginPath(); ctxBG.arc(o.x,o.y,o.r*3,0,Math.PI*2); ctxBG.fill(); o.x+=o.dx;o.y+=o.dy;if(o.x<0)o.x=w;if(o.x>w)o.x=0;if(o.y<0)o.y=h;if(o.y>h)o.y=0;});
  requestAnimationFrame(animateBG);
}
animateBG();

// نص ترحيبي
const welcomeText = `مرحبًا بك في منظمة اللواء القاعدة الجوية 590 – مكافحة الابتزاز الإلكتروني.\nأنت الآن في بيئة آمنة وسرّية بالكامل. قدّم بلاغك الآن بكل راحة واطمئنان.`;
let i=0;
const welcomeTextEl = document.getElementById('welcomeText');
function typeWriter(){
  if(i<welcomeText.length){
    welcomeTextEl.textContent += welcomeText.charAt(i);
    i++;
    setTimeout(typeWriter,15);
  }
}
typeWriter();

// فتح النموذج
document.getElementById('openForm').addEventListener('click', ()=>{
  document.getElementById('homePage').style.display='none';
  document.getElementById('reportPage').style.display='block';
  window.scrollTo({ top: 0, behavior:'smooth' });

  const inputs = [document.getElementById('nameInput'), document.getElementById('phoneInput')];
  inputs.forEach((input,idx)=>{ setTimeout(()=>{input.classList.add('show');}, idx*200); });

  const text2 = `مرحبًا! أدخل اسمك الثلاثي ورقم الواتس فقط، معلوماتك سرية بالكامل.`;
  const welcomeText2El = document.getElementById('welcomeText2');
  let j=0;
  function typeWriter2(){
    if(j<text2.length){
      welcomeText2El.textContent += text2.charAt(j);
      j++;
      setTimeout(typeWriter2,15);
    }
  }
  typeWriter2();
});

// إرسال البيانات عبر Formspree + عرض جدول
const form = document.getElementById('reportForm');
const container = document.getElementById('boxesContainer');
const statusEl = document.getElementById('status');

form.addEventListener('submit', async (e)=>{
  e.preventDefault();
  const name = document.getElementById('nameInput').value.trim();
  const phone = document.getElementById('phoneInput').value.trim();
  if(!name || !phone){ alert("الرجاء كتابة الاسم الثلاثي ورقم الواتس."); return; }
  document.getElementById('dateInput').value = new Date().toLocaleString();

  const formData = new FormData(form);
  try{
    const response = await fetch(form.action,{
      method: form.method,
      body: formData,
      headers: {'Accept':'application/json'}
    });
    if(response.ok){
      const newCell = document.createElement('div');
      newCell.classList.add('cell');
      newCell.innerHTML = `<span class="label">الاسم الثلاثي</span><span class="value">${name}</span>
                           <span class="label">رقم التواصل (واتس)</span><span class="value">${phone}</span>
                           <span class="label">تاريخ الإرسال</span><span class="value">${new Date().toLocaleString()}</span>`;
      container.appendChild(newCell);
      setTimeout(()=>{newCell.classList.add('show');},50);
      statusEl.style.color='#bff8f2';
      statusEl.textContent='تم الإرسال ✅ سيتم التواصل معك عبر رقمك الذي أرسلته لنا عبر الواتس.';
      form.reset();
    } else {
      const data = await response.json();
      statusEl.style.color='#ff4e4e';
      statusEl.textContent = data.error || 'حدث خطأ، حاول مرة أخرى.';
    }
  } catch(err){
    statusEl.style.color='#ff4e4e';
    statusEl.textContent='حدث خطأ في الاتصال، تحقق من الانترنت.';
  }
});
</script>
</body>
</html>
