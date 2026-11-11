# Colorful-Calculator-
🌈 Colorful Calculator for Kids – Learn Math with Fun Sounds! 🎵

Make learning math fun and exciting with our Colorful Calculator for Kids! 🧮✨

Designed especially for young learners, this interactive calculator features bright, cheerful colors, playful buttons, and delightful sound effects. Every time kids tap a button, they hear a fun and pleasant sound, making each interaction more enjoyable!

With its colorful design and easy-to-use interface, kids can explore numbers, practice basic math, and develop their counting skills while having fun.

🌟 Tap, Calculate, Listen, and Learn! 🎉
<!DOCTYPE html>
<html lang="uz">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Rangli kalkulyator</title>
<link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600;700&display=swap" rel="stylesheet">
<style>
:root{
  box-sizing:border-box;
  padding-top:env(safe-area-inset-top,0px);
  padding-bottom:env(safe-area-inset-bottom,0px);
  --bg1:#fff1f7; --bg2:#e8f4ff; --panel:#ffffff; --screen:#1b1236; --screen-text:#ffffff;
  --ink:#1b1236; --muted:#7a6f99; --glow:#ff5fa2;
}
@media (prefers-color-scheme: dark){
  :root:not([data-theme="light"]){
    --bg1:#1a1030; --bg2:#0e1c33; --panel:#261a45; --screen:#0f0a22; --screen-text:#ffffff; --ink:#fff; --muted:#b3a8d6;
  }
}
:root[data-theme="dark"]{
  --bg1:#1a1030; --bg2:#0e1c33; --panel:#261a45; --screen:#0f0a22; --screen-text:#ffffff; --ink:#fff; --muted:#b3a8d6;
}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
*{box-sizing:border-box}
html,body{height:100%;margin:0}
body{
  font-family:"Fredoka","Trebuchet MS",system-ui,sans-serif;
  background:linear-gradient(135deg,var(--bg1),var(--bg2));
  color:var(--ink);
  display:flex;align-items:center;justify-content:center;
  padding:16px;
}
.calc{
  width:100%;max-width:380px;
  background:var(--panel);
  border-radius:36px;
  padding:18px;
  box-shadow:0 0 0 4px var(--glow), 0 24px 60px rgba(120,60,200,.25);
  transition:box-shadow .25s;
}
.top{display:flex;justify-content:space-between;align-items:center;padding:2px 8px 12px}
.top h1{font-size:1.1rem;margin:0;font-weight:700}
.theme{
  border:0;background:var(--screen);color:var(--screen-text);
  border-radius:999px;padding:6px 14px;font:inherit;font-size:.85rem;cursor:pointer;
}
.screen{
  background:var(--screen);color:var(--screen-text);
  border-radius:24px;padding:14px 20px;margin-bottom:16px;
  text-align:right;min-height:112px;
  display:flex;flex-direction:column;justify-content:flex-end;overflow:hidden;
}
.expr{font-size:1rem;color:var(--muted);min-height:1.4em;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.out{
  font-size:3rem;font-weight:700;line-height:1.1;white-space:nowrap;
  color:var(--glow);transition:color .2s;
}
.out.small{font-size:2.1rem}
.out.err{font-size:1.4rem}
.keys{display:grid;grid-template-columns:repeat(4,1fr);gap:12px}
.k{
  --d:color-mix(in srgb,var(--c) 60%,#000);
  border:0;cursor:pointer;
  background:var(--c);color:#1b1236;
  font:inherit;font-size:1.6rem;font-weight:700;
  height:64px;border-radius:22px;
  box-shadow:0 6px 0 var(--d);
  transition:transform .08s, box-shadow .08s;
  -webkit-tap-highlight-color:transparent;
}
.k:active,.k.hit{transform:translateY(5px);box-shadow:0 1px 0 var(--d)}
.k:focus-visible{outline:3px solid var(--ink);outline-offset:3px}
.k.wide{grid-column:span 2}
@media (prefers-reduced-motion: reduce){.k,.out,.calc{transition:none}}
</style>
</head>
<body>
<main class="calc" id="calc">
  <div class="top">
    <h1>Rangli kalkulyator</h1>
    <div style="display:flex;gap:6px"><button class="theme" id="snd" type="button" aria-pressed="true">🔊 Ovoz</button><button class="theme" id="theme" type="button">Qorong‘i</button></div>
  </div>
  <div class="screen" aria-live="polite">
    <div class="expr" id="expr"></div>
    <div class="out" id="out">0</div>
  </div>
  <div class="keys" id="keys"></div>
</main>
<script>
var layout=[
 ["AC",330],["⌫",285],["%",45],["÷",15],
 ["7",190],["8",160],["9",130],["×",265],
 ["4",95],["5",75],["6",50],["−",200],
 ["1",25],["2",5],["3",345],["+",170],
 ["0",310,1],[".",240],["=",100]
];
var keys=document.getElementById("keys"),out=document.getElementById("out"),exprEl=document.getElementById("expr"),calc=document.getElementById("calc");
var cur="0",prev=null,op=null,fresh=false,err=false,btns={};
layout.forEach(function(b){
  var el=document.createElement("button");
  el.type="button";el.className="k"+(b[2]?" wide":"");el.textContent=b[0];
  el.style.setProperty("--c","hsl("+b[1]+" 92% 64%)");
  el.dataset.hue=b[1];
  el.addEventListener("click",function(){press(b[0],b[1])});
  keys.appendChild(el);btns[b[0]]=el;
});
function fmt(s){
  if(err)return s;
  var n=parseFloat(s);
  if(!isFinite(n))return s;
  if(s.slice(-1)==="."||/\.\d*0$/.test(s)&&s.indexOf("e")<0)return s;
  var r=parseFloat(n.toPrecision(12));
  return String(r);
}
function show(){
  var t=fmt(cur);
  out.textContent=t;
  out.className="out"+(err?" err":t.length>11?" small":"");
  exprEl.textContent=op&&prev!==null?fmt(prev)+" "+op:"";
}
function calcOp(a,b,o){
  a=parseFloat(a);b=parseFloat(b);
  if(o==="+")return a+b;
  if(o==="−")return a-b;
  if(o==="×")return a*b;
  if(o==="÷")return b===0?null:a/b;
  return b;
}
function setErr(){cur="Nolga bo‘lib bo‘lmaydi";err=true;prev=null;op=null;fresh=true}
var AC=null,soundOn=true;
var SCALE=[523.25,587.33,659.25,783.99,880,1046.5,1174.66,1318.51,1567.98,1760];
function tone(f,t,dur,type,vol,f2){
  var o=AC.createOscillator(),g=AC.createGain();
  o.type=type;o.frequency.setValueAtTime(f,t);
  if(f2)o.frequency.exponentialRampToValueAtTime(f2,t+dur);
  g.gain.setValueAtTime(0.0001,t);
  g.gain.exponentialRampToValueAtTime(vol,t+0.008);
  g.gain.exponentialRampToValueAtTime(0.0001,t+dur);
  o.connect(g);g.connect(AC.destination);
  o.start(t);o.stop(t+dur+0.02);
}
function clickNoise(t){
  var n=Math.floor(AC.sampleRate*0.012),buf=AC.createBuffer(1,n,AC.sampleRate),d=buf.getChannelData(0);
  for(var i=0;i<n;i++)d[i]=(Math.random()*2-1)*(1-i/n);
  var s=AC.createBufferSource(),g=AC.createGain(),h=AC.createBiquadFilter();
  h.type="highpass";h.frequency.value=1800;g.gain.value=0.07;
  s.buffer=buf;s.connect(h);h.connect(g);g.connect(AC.destination);s.start(t);
}
function sfx(k){
  if(!soundOn)return;
  try{
    AC=AC||new (window.AudioContext||window.webkitAudioContext)();
    if(AC.state==="suspended")AC.resume();
    var t=AC.currentTime;
    clickNoise(t);
    if("0123456789".indexOf(k)>=0){tone(SCALE[+k],t,0.22,"sine",0.16);tone(SCALE[+k]*2,t,0.1,"sine",0.03)}
    else if(k==="="){[523.25,659.25,783.99,1046.5].forEach(function(f,i){tone(f,t+i*0.06,0.35,"triangle",0.13)})}
    else if(k==="AC"){tone(900,t,0.28,"sine",0.14,300)}
    else if(k==="⌫"){tone(330,t,0.12,"triangle",0.14)}
    else if(k==="."){tone(700,t,0.08,"sine",0.12)}
    else{tone(392,t,0.2,"triangle",0.15);tone(587.33,t+0.04,0.18,"sine",0.07)}
  }catch(e){}
}
function press(k,hue){
  sfx(k);
  if(hue!==undefined){
    var c="hsl("+hue+" 95% 62%)";
    out.style.color=c;calc.style.setProperty("--glow",c);
  }
  if(err){cur="0";err=false;}
  if(/^\d$/.test(k)){
    if(fresh||cur==="0"){cur=k}else if(cur.replace(/[-.]/g,"").length<12){cur+=k}
    fresh=false;
  }else if(k==="."){
    if(fresh){cur="0.";fresh=false}else if(cur.indexOf(".")<0){cur+="."}
  }else if(k==="AC"){
    cur="0";prev=null;op=null;fresh=false;
  }else if(k==="⌫"){
    if(!fresh){cur=cur.length>1&&!(cur.length===2&&cur[0]==="-")?cur.slice(0,-1):"0"}
  }else if(k==="%"){
    cur=String(parseFloat(cur)/100);
  }else if(k==="="){
    if(op&&prev!==null){
      var r=calcOp(prev,cur,op);
      if(r===null){setErr()}else{cur=String(parseFloat(r.toPrecision(12)));prev=null;op=null;fresh=true}
    }
  }else{
    if(op&&prev!==null&&!fresh){
      var r2=calcOp(prev,cur,op);
      if(r2===null){setErr();show();return}
      cur=String(parseFloat(r2.toPrecision(12)));
    }
    prev=cur;op=k;fresh=true;
  }
  show();
}
document.addEventListener("keydown",function(e){
  var m={"*":"×","/":"÷","-":"−","Enter":"=","=":"=","Backspace":"⌫","Escape":"AC","Delete":"AC",",":"."};
  var k=m[e.key]||e.key;
  if(btns[k]&&!e.ctrlKey&&!e.metaKey){
    e.preventDefault();
    var b=btns[k];b.classList.add("hit");
    setTimeout(function(){b.classList.remove("hit")},120);
    press(k,b.dataset.hue);
  }
});
var themeBtn=document.getElementById("theme"),root=document.documentElement;
themeBtn.addEventListener("click",function(){
  var dark=root.dataset.theme?root.dataset.theme==="dark":matchMedia("(prefers-color-scheme: dark)").matches;
  root.dataset.theme=dark?"light":"dark";
  themeBtn.textContent=dark?"Qorong‘i":"Yorug‘";
});
