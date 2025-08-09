<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Do You Love Me? — Tiny Game</title>
<style>
  :root{
    --bg-1: linear-gradient(135deg,#ffe6f0 0%, #ffd9b3 100%);
    --accent:#ff4b6b;
    --muted:#6b6f76;
    --card-bg: rgba(255,255,255,0.92);
  }
  *{box-sizing:border-box}
  html,body{height:100%;margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,Arial;}
  body{
    display:flex;align-items:center;justify-content:center;
    padding:18px;background:var(--bg-1);transition:background 600ms ease;
  }
  .container{
    width:100%;max-width:460px;background:var(--card-bg);
    border-radius:16px;box-shadow:0 12px 30px rgba(15,15,20,0.12);
    padding:22px;text-align:center;position:relative;overflow:hidden;
  }
  h1{font-size:1.45rem;margin:0;color:var(--accent)}
  p.lead{margin:8px 0 18px;color:var(--muted);font-size:0.95rem}
  .mascot{width:120px;height:120px;margin:10px auto;transition:transform 300ms ease}
  .bubble{display:inline-block;background:white;padding:10px 14px;border-radius:12px;box-shadow:0 10px 28px rgba(0,0,0,0.06);font-size:0.98rem;color:#333;margin-bottom:12px}
  .card{padding:8px 6px 18px}
  .question{font-weight:700;font-size:1.05rem;margin:12px 0}
  .btn-row{display:flex;gap:12px;justify-content:center;align-items:center;position:relative;padding:12px}
  button.btn{
    min-width:128px;padding:12px 14px;border-radius:12px;border:0;font-weight:700;
    cursor:pointer;box-shadow:0 10px 22px rgba(12,12,20,0.06);transition:transform 200ms ease,opacity 200ms;
    font-size:1rem;
  }
  button.yes{background:linear-gradient(90deg,#ff7a97,#ffb36b);color:white}
  button.no{background:#eef2f7;color:#2b3a67;position:absolute;left:60%}
  .progress{height:8px;background:#fdecec;border-radius:999px;margin-top:12px;overflow:hidden}
  .progress > span{display:block;height:100%;width:0%;background:linear-gradient(90deg,#ff8aa0,#ffb36b);transition:width 420ms ease}
  .confetti{pointer-events:none;position:fixed;left:0;top:0;width:100%;height:100%;overflow:hidden;z-index:9999}
  .piece{position:absolute;border-radius:3px;will-change:transform,opacity}
  .mascot.happy{transform:translateY(-6px) scale(1.02)}
  .mascot.sad{transform:translateY(6px) scale(.98) rotate(-3deg);opacity:.95}
  @media (max-width:420px){ .btn-row{gap:8px} button.btn{min-width:110px;font-size:0.96rem} .no{left:55%} }
</style>
</head>
<body>
  <div class="container" id="app">
    <header>
      <h1>Do you love me?</h1>
      <p class="lead">Tap an answer — tiny warning: the game will try hard to get a “Yes”. 😉</p>
    </header>

    <!-- Mascot: simple SVG emoji-like -->
    <div style="display:flex;flex-direction:column;align-items:center">
      <div id="mascot" class="mascot" aria-hidden="true" style="font-size:90px">😊</div>
      <div class="bubble" id="bubble">Be honest — it's okay to say Yes. 💖</div>
    </div>

    <div class="card" aria-live="polite">
      <div class="question" id="question">Do you love me?</div>

      <div class="btn-row" id="btnRow">
        <button class="btn yes" id="yesBtn">Yes ❤️</button>
        <button class="btn no" id="noBtn" aria-label="No">No 😏</button>
      </div>

      <div class="progress" aria-hidden="true"><span id="progressBar"></span></div>
    </div>

    <p style="font-size:0.85rem;color:var(--muted);margin-top:12px">Tip: open on phone for best “No runs away” action.</p>
  </div>

  <div class="confetti" id="confettiArea" aria-hidden="true"></div>

<script>
/* Simple, smooth progressive web game logic */

const prompts = [
  { q: "Do you love me?", yes: "Yesss 😘", no: "Wait, really?" },
  { q: "Would you hold my hand forever?", yes: "Heart stolen ❤️", no: "Hand-holding is peak" },
  { q: "If I made you breakfast, would you smile?", yes: "Breakfast champion 🥞", no: "Even sad eggs can't resist" },
  { q: "Would you travel with me?", yes: "Passport ready ✈️", no: "I'll pack for both" },
  { q: "Do you prefer cozy nights in with me?", yes: "Couch & chill 🥰", no: "I make great popcorn" },
  { q: "Final honest time — do you love me?", yes: "That's the one 💖", no: "Okay, I'm persistent 😉" }
];

let idx = 0;
let noCount = 0;

const yesBtn = document.getElementById('yesBtn');
const noBtn = document.getElementById('noBtn');
const questionEl = document.getElementById('question');
const bubble = document.getElementById('bubble');
const mascot = document.getElementById('mascot');
const progress = document.getElementById('progressBar');
const confettiArea = document.getElementById('confettiArea');

function updateUI(){
  questionEl.textContent = prompts[idx].q;
  bubble.textContent = (idx===0) ? "Be honest — it's okay to say Yes. 💖" : "Think carefully... 😏";
  progress.style.width = `${Math.round((idx / (prompts.length -1)) * 100)}%`;
  // neutral mascot
  mascot.textContent = "😊";
  mascot.classList.remove('happy','sad');
  document.body.style.background = 'linear-gradient(135deg,#ffe6f0 0%, #ffd9b3 100%)';
}
updateUI();

function celebrate(){
  // happy finish screen and confetti
  mascot.textContent = "😍";
  mascot.classList.add('happy');
  bubble.textContent = prompts[idx].yes || "Yes!";
  spawnConfetti();
  setTimeout(()=> {
    // replace container nicely
    const app = document.getElementById('app');
    app.innerHTML = `
      <div style="padding:28px;text-align:center">
        <h1 style="color:var(--accent);margin:0 0 8px">You said Yes! 💖</h1>
        <p style="color:#444;margin:0 0 20px">Winner — unlimited hugs & front-row attention.</p>
        <div style="display:flex;gap:10px;justify-content:center">
          <button style="padding:10px 16px;border-radius:10px;border:0;background:var(--accent);color:#fff;font-weight:700" onclick="location.reload()">Play again</button>
        </div>
      </div>
    `;
  },900);
}

function spawnConfetti(){
  confettiArea.innerHTML = '';
  const colors = ['#ff4b6b','#ffd166','#9ad3bc','#bde0fe','#ffb3c1','#ffd5b4'];
  for(let i=0;i<60;i++){
    const d = document.createElement('div');
    d.className = 'piece';
    const w = 6 + Math.random()*12;
    d.style.width = w + 'px';
    d.style.height = (8 + Math.random()*14) + 'px';
    d.style.background = colors[Math.floor(Math.random()*colors.length)];
    d.style.left = (Math.random()*100) + '%';
    d.style.top = (-10 - Math.random()*20) + 'px';
    d.style.opacity = 0.95;
    d.style.transform = `rotate(${Math.random()*360}deg)`;
    d.style.transition = `transform ${1200 + Math.random()*900}ms cubic-bezier(.2,.9,.2,1), top ${1200 + Math.random()*900}ms linear, opacity 1200ms linear`;
    confettiArea.appendChild(d);
    setTimeout(()=> {
      d.style.top = (30 + Math.random()*60) + '%';
      d.style.transform = `translateY(${200 + Math.random()*400}px) rotate(${Math.random()*720}deg)`;
    }, 20 + Math.random()*240);
    setTimeout(()=> d.remove(), 2800 + Math.random()*1200);
  }
}

function handleYes(){
  // progress to happy end
  bubble.textContent = prompts[idx].yes || "Yes!";
  mascot.textContent = "😍";
  celebrate();
}

function handleNoAttempt(manual=false){
  noCount++;
  idx = Math.min(idx + 1, prompts.length -1);
  questionEl.textContent = prompts[idx].q;
  bubble.textContent = (noCount < 3) ? "Are you sure? Think again..." : "Okay, I'll be persistent 😉";
  mascot.textContent = "😢";
  mascot.classList.add('sad');
  document.body.style.background = 'linear-gradient(135deg,#e6edf8 0%, #d6d6d6 100%)';
  progress.style.width = `${Math.round((idx / (prompts.length -1)) * 100)}%`;
  // make the No button dodge after a couple tries
  if(noCount >= 1) dodgeNoButton();
}

/* No button dodge/teleport */
function dodgeNoButton(){
  const vw = Math.max(document.documentElement.clientWidth || 0, window.innerWidth || 0);
  const vh = Math.max(document.documentElement.clientHeight || 0, window.innerHeight || 0);
  const pad = 18;
  const w = noBtn.offsetWidth;
  const h = noBtn.offsetHeight;
  let nx = Math.random() * (vw - w - pad*2) + pad;
  let ny = Math.random() * (vh - h - pad*2) + pad + 40;
  // avoid landing on top of yes
  const yesRect = yesBtn.getBoundingClientRect();
  if(Math.abs(nx - yesRect.left) < 90) nx += (nx < yesRect.left) ? -90 : 90;
  noBtn.style.transition = 'left 380ms ease, top 380ms ease, transform 380ms ease';
  noBtn.style.position = 'fixed';
  noBtn.style.left = nx + 'px';
  noBtn.style.top = ny + 'px';
}

/* proximity dodge for mouse */
document.addEventListener('mousemove',(ev)=>{
  if(window.innerWidth < 520) return;
  const rect = noBtn.getBoundingClientRect();
  const dx = ev.clientX - (rect.left + rect.width/2);
  const dy = ev.clientY - (rect.top + rect.height/2);
  const dist = Math.hypot(dx,dy);
  if(dist < 110){
    dodgeNoButton();
    noCount++;
    bubble.textContent = ["Too close!","Not today 😉","Try the other button..."][noCount % 3];
    mascot.textContent = "😟";
  }
});

/* touch start: tap near no triggers dodge */
document.addEventListener('touchstart',(ev)=>{
  const t = ev.touches[0];
  const rect = noBtn.getBoundingClientRect();
  const dx = t.clientX - (rect.left + rect.width/2);
  const dy = t.clientY - (rect.top + rect.height/2);
  const dist = Math.hypot(dx,dy);
  if(dist < 100){
    dodgeNoButton();
    noCount++;
    bubble.textContent = ["Hehe!","You almost had it","Not so fast 😏"][noCount % 3];
    mascot.textContent = "😢";
  }
});

/* click handlers */
yesBtn.addEventListener('click', handleYes);
noBtn.addEventListener('click', ()=> handleNoAttempt(true));

/* keyboard shortcuts: y/n */
document.addEventListener('keydown',(e)=>{
  if(e.key.toLowerCase() === 'y') handleYes();
  if(e.key.toLowerCase() === 'n') handleNoAttempt(true);
});

</script>
</body>
</html>
# love-game
A fun game made by me with love
