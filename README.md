<!doctype html>
<html lang="fr">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Mini Casino Gratuit — Machine à Sous</title>
<style>
  body{font-family:system-ui,Segoe UI,Roboto,Arial;display:flex;flex-direction:column;align-items:center;padding:20px;background:#0e0f13;color:#f2f4f8}
  .card{background:#121217;padding:18px;border-radius:12px;box-shadow:0 8px 30px rgba(0,0,0,.6);max-width:420px;width:100%}
  h1{margin:0 0 12px;font-size:20px;text-align:center}
  .reels{display:flex;justify-content:space-between;gap:8px;margin:14px 0}
  .reel{flex:1;height:110px;border-radius:8px;background:linear-gradient(180deg,#fff,#eee);display:flex;align-items:center;justify-content:center;font-size:28px;font-weight:700;color:#222}
  .controls{display:flex;gap:8px;flex-wrap:wrap;justify-content:center}
  button{padding:10px 14px;border-radius:8px;border:0;background:#ffb86b;color:#111;font-weight:700;cursor:pointer}
  .muted{background:#2b2c33;color:#9aa0b0}
  .info{display:flex;justify-content:space-between;margin-top:12px;color:#9aa0b0;font-size:14px}
  .log{margin-top:12px;background:#0b0c0f;padding:10px;border-radius:8px;max-height:160px;overflow:auto;color:#cbd5e1;font-size:13px}
</style>
</head>
<body>
  <div class="card" role="application" aria-label="Machine à sous gratuite">
    <h1>Mini Machine à Sous — Gratuit</h1>
    <div style="text-align:center;margin-bottom:8px">Crédits: <strong id="credits">0</strong></div>

    <div class="reels" aria-hidden="false">
      <div class="reel" id="r1">🍒</div>
      <div class="reel" id="r2">🍋</div>
      <div class="reel" id="r3">🔔</div>
    </div>

    <div class="controls">
      <button id="spin">Lancer (1 crédit)</button>
      <button id="add" class="muted">+100 crédits gratuits</button>
      <button id="reset" class="muted">Réinitialiser</button>
    </div>

    <div class="info">
      <div>Dernier gain: <span id="last">—</span></div>
      <div>Jackpot: <strong id="jackpot">500</strong></div>
    </div>

    <div class="log" id="log" aria-live="polite"></div>
  </div>

<script>
(() => {
  const symbols = ['🍒','🍋','🍊','🔔','⭐','7️⃣'];
  const paytable = {
    '7️⃣,7️⃣,7️⃣': 500,
    '⭐,⭐,⭐': 150,
    '🔔,🔔,🔔': 100,
    '🍊,🍊,🍊': 50,
    '🍋,🍋,🍋': 25,
    '🍒,🍒,🍒': 10
  };

  const creditsEl = document.getElementById('credits');
  const r1 = document.getElementById('r1'), r2 = document.getElementById('r2'), r3 = document.getElementById('r3');
  const spinBtn = document.getElementById('spin'), addBtn = document.getElementById('add'), resetBtn = document.getElementById('reset');
  const logEl = document.getElementById('log'), lastEl = document.getElementById('last'), jackpotEl = document.getElementById('jackpot');

  // localStorage keys
  const KEY = 'mini-casino-credits-v1';
  function getCredits(){ return parseInt(localStorage.getItem(KEY) || '0',10); }
  function setCredits(v){ localStorage.setItem(KEY, String(v)); creditsEl.textContent = v; }

  // init
  setCredits(getCredits());

  function randInt(n){ return Math.floor(Math.random()*n); }

  function spinOnce(){
    // coût 1 crédit
    let credits = getCredits();
    if(credits < 1){ log('Crédits insuffisants — ajoute des crédits gratuits.'); return; }
    setCredits(credits - 1);
    spinBtn.disabled = true;

    // animation simple
    const rounds = 16;
    let i=0;
    const iv = setInterval(()=>{
      r1.textContent = symbols[randInt(symbols.length)];
      r2.textContent = symbols[randInt(symbols.length)];
      r3.textContent = symbols[randInt(symbols.length)];
      i++;
      if(i>=rounds){ clearInterval(iv); finishSpin(); }
    }, 60);

    function finishSpin(){
      // résultat final (réel RNG)
      const s1 = symbols[randInt(symbols.length)];
      const s2 = symbols[randInt(symbols.length)];
      const s3 = symbols[randInt(symbols.length)];
      r1.textContent = s1; r2.textContent = s2; r3.textContent = s3;

      const key = `${s1},${s2},${s3}`;
      const win = paytable[key] || 0;
      if(win>0){
        setCredits(getCredits() + win);
        lastEl.textContent = `+${win}`;
        log(`Gagné ${win} crédits ! (${key})`);
      } else {
        lastEl.textContent = '0';
        log(`Perdu — (${key})`);
      }
      spinBtn.disabled = false;
    }
  }

  function log(text){
    const now = new Date().toLocaleTimeString();
    logEl.innerHTML = `<div>[${now}] ${text}</div>` + logEl.innerHTML;
  }

  spinBtn.addEventListener('click', spinOnce);
  addBtn.addEventListener('click', ()=>{
    setCredits(getCredits()+100);
    log('Ajout de 100 crédits gratuits');
  });
  resetBtn.addEventListener('click', ()=>{
    localStorage.removeItem(KEY);
    setCredits(0);
    log('Réinitialisation des crédits locaux');
    lastEl.textContent = '—';
  });

  // accessible shortcut: touche R ajoute 100 crédits
  document.addEventListener('keyup', e => { if(e.key.toLowerCase()==='r'){ setCredits(getCredits()+100); log('Raccourci: +100 crédits'); } });

})();
</script>
</body>
</html>
