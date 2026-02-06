# Valentines
Bob The Minion
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Be My Valentine — Bob the Minion</title>
  <style>
    :root{--bg:#ffeef0;--pink:#ff5a8a;--yellow:#ffd349;--denim:#2b60a6}
    html,body{height:100%;margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;}
    body{background:linear-gradient(180deg, #fff 0%, #ffeef0 50%);display:flex;align-items:center;justify-content:center}
    .card{width:920px;max-width:96vw;background:linear-gradient(180deg,#fff,#fff9);box-shadow:0 8px 30px rgba(0,0,0,.12);border-radius:18px;padding:28px;display:flex;gap:24px;align-items:center}
    .left{flex:1;min-width:280px}
    .right{width:420px;display:flex;flex-direction:column;gap:14px;align-items:center}
    h1{margin:0 0 6px 0;color:#222;font-size:28px}
    p{margin:0;color:#4b4b4b}
    .controls{display:flex;gap:10px;margin-top:12px}
    input[type=text]{flex:1;padding:10px 12px;border-radius:10px;border:1px solid #eee;font-size:16px}
    button{background:var(--pink);color:#fff;padding:10px 16px;border-radius:10px;border:none;font-weight:600;cursor:pointer}
    button:active{transform:translateY(1px)}
    .message{margin-top:8px;color:#b03b62;font-weight:700}
    /* Minion area */
    .minion-wrap{display:flex;align-items:center;justify-content:center;gap:12px}
    svg{max-width:320px;width:100%;height:auto}
    .heart{fill:var(--pink);transform-origin:center;animation:beat 1s infinite}
    @keyframes beat{0%,100%{transform:scale(1)}50%{transform:scale(1.15)}}
    /* Floating hearts */
    .hearts{position:absolute;pointer-events:none;}
    .small{width:18px;height:18px;opacity:.9}
    .meta{color:#666;font-size:13px;margin-top:6px}
    .result{font-size:20px;font-weight:700;color:var(--denim);margin-top:10px;text-align:center}
    .footer{font-size:12px;color:#888;margin-top:8px}
  </style>
</head>
<body>
  <div class="card" role="main">
    <div class="left">
      <h1>Will you be my Valentine?</h1>
      <p>Let Bob the Minion help you ask someone special. Type their name and press the heart button.</p>
      <div class="controls">
        <input id="name" type="text" placeholder="Their name (e.g., Alex)" />
        <button id="ask">💖 Ask</button>
      </div>
      <div class="message" id="message">Bob is shy... but ready with a heart.</div>
      <div class="meta">Tip: click multiple times for extra hearts.</div>
    </div>
    <div class="right">
      <div class="minion-wrap">
        <!-- Simple Bob the Minion (SVG fan-art inspired) -->
        <svg id="bob" viewBox="0 0 200 260" xmlns="http://www.w3.org/2000/svg" aria-label="Bob the Minion cartoon">
          <defs>
            <clipPath id="bodyClip"><rect x="0" y="40" width="200" height="200" rx="40" /></clipPath>
          </defs>
          <rect width="200" height="260" rx="24" fill="transparent"/>
          <!-- Body -->
          <g clip-path="url(#bodyClip)">
            <ellipse cx="100" cy="160" rx="78" ry="100" fill="var(--yellow)"/>
            <!-- Overalls -->
            <path d="M28 160 C60 220 140 220 172 160 L172 220 L28 220 Z" fill="var(--denim)"/>
            <rect x="72" y="120" width="56" height="52" rx="8" fill="#1f4a86"/>
            <!-- Strap buttons -->
            <circle cx="58" cy="150" r="6" fill="#373737"/>
            <circle cx="142" cy="150" r="6" fill="#373737"/>
          </g>
          <!-- Eye / Goggles -->
          <g transform="translate(36,60)">
            <circle cx="64" cy="40" r="36" fill="#ddd" stroke="#555" stroke-width="6" />
            <circle cx="64" cy="40" r="22" fill="#fff"/>
            <circle cx="64" cy="40" r="12" fill="#3b2f1b"/>
            <circle cx="72" cy="36" r="4" fill="#fff" opacity="0.8"/>
            <rect x="0" y="34" width="128" height="18" rx="12" fill="#444" opacity="0.12" />
          </g>
          <!-- Smile and arms -->
          <path d="M70 180 C88 198 112 198 130 180" stroke="#502d00" stroke-width="5" fill="none" stroke-linecap="round"/>
          <path d="M20 160 C40 150 32 190 50 190" stroke="#ffd349" stroke-width="8" stroke-linecap="round" fill="none"/>
          <path d="M180 160 C160 150 168 190 150 190" stroke="#ffd349" stroke-width="8" stroke-linecap="round" fill="none"/>
          <!-- Bob holding a heart -->
          <g id="heartGroup" transform="translate(55,110)">
            <path class="heart" d="M48 10 C48 -6 20 -6 20 10 C20 22 48 38 48 38 C48 38 76 22 76 10 C76 -6 48 -6 48 10 Z"/>
          </g>
        </svg>
      </div>
      <div class="result" id="result">Say it with Bob 💛</div>
      <div class="footer">This page uses a stylized Bob-inspired design for fun.</div>
    </div>
  </div>

  <script>
    const askBtn = document.getElementById('ask');
    const nameInput = document.getElementById('name');
    const message = document.getElementById('message');
    const result = document.getElementById('result');
    const heartGroup = document.getElementById('heartGroup');

    function makeFloatingHeart(xOffset){
      const svgNS = 'http://www.w3.org/2000/svg';
      const small = document.createElementNS(svgNS,'path');
      small.setAttribute('d','M6 1 C6 -1 0 -1 0 1 C0 3 6 7 6 7 C6 7 12 3 12 1 C12 -1 6 -1 6 1 Z');
      small.setAttribute('fill','#ff5a8a');
      small.setAttribute('transform','translate('+(60 + (xOffset||0))+','+ (120) +') scale(.9)');
      small.style.opacity = '0.95';
      document.querySelector('#bob').appendChild(small);
      // animate float
      small.animate([
        {transform: small.getAttribute('transform'), opacity: 0.95},
        {transform: 'translate('+(60 + (xOffset||0))+','+ (30) +') scale(.9)', opacity:0}
      ],{duration:1200+Math.random()*800,easing:'cubic-bezier(.2,.8,.2,1)'}).onfinish = ()=> small.remove();
    }

    askBtn.addEventListener('click', ()=>{
      const who = nameInput.value.trim() || 'you';
      // personalize message
      message.textContent = `Bob offers a heart to ${who}!`;
      result.textContent = `${who}, will you be my Valentine? — from Bob 💛`;
      // animate heart pop
      heartGroup.querySelector('.heart').animate([
        {transform: 'translateY(0) scale(1)'},
        {transform: 'translateY(-8px) scale(1.25)'},
        {transform: 'translateY(0) scale(1)'}
      ],{duration:600,easing:'cubic-bezier(.2,.9,.2,1)'});
      // spawn a few floating hearts
      for(let i=0;i<6;i++) setTimeout(()=>makeFloatingHeart((i-3)*10), i*80);
    });

    // allow Enter key
    nameInput.addEventListener('keydown', e=>{ if(e.key==='Enter') askBtn.click(); });
  </script>
</body>
</html>
