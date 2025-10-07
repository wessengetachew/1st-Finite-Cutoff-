<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Getachew Modular Prime Sieve Explorer</title>

<!-- MathJax for LaTeX rendering (CDN). Replace if you need an offline renderer -->
<script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js" async></script>

<style>
  :root{
    --bg:#0b1220;
    --panel:#071226;
    --muted:#94a3b8;
    --accent:#66c2ff;
    --good:#36d399;
    --glass: rgba(255,255,255,0.03);
    --card-radius:12px;
    --ui-pad:12px;
    font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    color: #e6eef8;
    -webkit-font-smoothing:antialiased;
    -moz-osx-font-smoothing:grayscale;
  }
  html,body { height:100%; margin:0; background: linear-gradient(180deg,#071322,#04101a 140%); }
  .app {
    display:grid;
    grid-template-columns: 360px 1fr 320px;
    gap:18px;
    padding:18px;
    height:100vh;
    box-sizing:border-box;
  }

  /* Left controls */
  .panel {
    background: linear-gradient(180deg,var(--panel), rgba(255,255,255,0.01));
    border-radius:var(--card-radius);
    padding:var(--ui-pad);
    box-shadow: 0 8px 30px rgba(2,6,23,0.6);
    display:flex;
    flex-direction:column;
    gap:12px;
    min-height:0;
  }
  .panel h1 { margin:0; font-size:18px; color:var(--accent); }
  .panel h2 { margin:0; font-size:13px; color:var(--muted); }
  label { display:block; font-size:13px; color:var(--muted); margin-bottom:6px; }
  input[type="number"], input[type="range"], select {
    width:100%;
    padding:8px 10px;
    background:var(--glass);
    border:1px solid rgba(255,255,255,0.04);
    color:inherit;
    border-radius:8px;
    font-size:13px;
    box-sizing:border-box;
  }
  .row { display:flex; gap:8px; align-items:center; }
  .btn {
    background:linear-gradient(180deg,var(--accent), #4fb6f7);
    border:none;
    padding:8px 12px;
    border-radius:8px;
    color:#042025;
    font-weight:700;
    cursor:pointer;
  }
  .btn.secondary {
    background:transparent;
    border:1px solid rgba(255,255,255,0.06);
    color:var(--muted);
    font-weight:600;
  }
  .small { font-size:12px; color:var(--muted); }
  .controls { display:flex; flex-direction:column; gap:10px; }

  /* Center canvas */
  .viewport {
    background: radial-gradient(circle at 20% 20%, rgba(102,194,255,0.02), transparent 10%), linear-gradient(180deg, rgba(255,255,255,0.01), transparent);
    border-radius:var(--card-radius);
    box-shadow: inset 0 1px 0 rgba(255,255,255,0.02);
    padding:14px;
    position:relative;
    overflow:hidden;
    min-height:0;
  }
  canvas { width:100%; height:100%; display:block; border-radius:8px; background:linear-gradient(180deg,#02060b,#001018 120%); }

  /* Right panel (stats & LaTeX) */
  .side {
    background: linear-gradient(180deg,var(--panel), rgba(255,255,255,0.01));
    border-radius:var(--card-radius);
    padding:var(--ui-pad);
    box-shadow: 0 8px 20px rgba(2,6,23,0.55);
    display:flex;
    flex-direction:column;
    gap:10px;
    min-height:0;
  }

  .legend { font-size:13px; color:var(--muted); display:flex; gap:8px; flex-direction:column; }
  .legend .item { display:flex; gap:8px; align-items:center; }
  .swatch { width:14px; height:14px; border-radius:3px; flex:0 0 14px; border:1px solid rgba(255,255,255,0.04); }
  .sw-ring{ background:transparent; border:1px dashed rgba(255,255,255,0.06); }
  .sw-gcd1{ background:var(--good); }
  .sw-pt{ background:rgba(255,255,255,0.07); }
  .sw-line{ background:linear-gradient(90deg,var(--accent), #a78bfa); height:3px; width:24px; border-radius:2px; }

  /* Tooltip */
  .tooltip {
    position:absolute;
    pointer-events:none;
    background:rgba(2,6,23,0.9);
    padding:8px 10px;
    border-radius:8px;
    border:1px solid rgba(125,211,252,0.08);
    color:var(--accent);
    font-size:13px;
    transform:translate(-50%,-120%);
    display:none;
    z-index:50;
  }
  footer.note { position:absolute; right:18px; bottom:16px; color:rgba(255,255,255,0.05); font-size:12px; }

  /* Responsive */
  @media (max-width:1100px){
    .app { grid-template-columns: 1fr; grid-template-rows: auto 1fr auto; padding:12px; gap:12px; }
    .panel, .side { order:2; }
    .viewport { order:1; height:60vh; }
  }
</style>
</head>
<body>
  <div class="app" role="application" aria-label="Getachew Modular Prime Sieve Explorer">
    <!-- Left controls -->
    <aside class="panel" id="leftPanel" aria-label="controls">
      <h1>Getachew Modular Prime Sieve Explorer</h1>
      <div class="small">Interactive exploration of M = 30·2ⁿ modular sieves, twin transitions, Goldbach pairs, and residue lifts.</div>

      <div class="controls" role="region" aria-label="controls region">
        <div>
          <label for="nInput">n (modulus exponent):</label>
          <input id="nInput" type="number" min="-6" max="16" step="1" value="0" />
        </div>

        <div>
          <label for="ringsInput">Number of rings displayed:</label>
          <input id="ringsInput" type="number" min="1" max="10" step="1" value="5" />
        </div>

        <div>
          <label for="densityInput">Residue density (show every k-th residue):</label>
          <input id="densityInput" type="number" min="1" max="50" step="1" value="1" />
        </div>

        <div>
          <label for="pointSize">Point size (px):</label>
          <input id="pointSize" type="range" min="1" max="8" step="0.5" value="3" />
        </div>

        <div>
          <label for="viewMode">View mode:</label>
          <select id="viewMode">
            <option value="residue">Residue Rings</option>
            <option value="twin">Twin Transitions</option>
            <option value="goldbach">Goldbach Pairs</option>
            <option value="riemann">Riemann Map</option>
            <option value="lifts">Modular Lifts (M → 2M)</option>
          </select>
        </div>

        <div class="row">
          <button id="drawBtn" class="btn">Redraw</button>
          <button id="animateBtn" class="btn secondary">Play</button>
          <button id="exportBtn" class="btn secondary">Export PNG</button>
        </div>

        <div class="row">
          <button id="copyHtml" class="btn secondary">Copy HTML</button>
          <button id="resetView" class="btn secondary">Reset</button>
        </div>

        <div class="small">
          Keyboard: Space toggles animation. Enter re-draws when focused on inputs.
        </div>
      </div>
    </aside>

    <!-- Center visualization -->
    <main class="viewport" id="viewport" aria-label="visualization">
      <canvas id="mainCanvas" width="1200" height="800" role="img" aria-label="modular visualization canvas"></canvas>
      <div id="tooltip" class="tooltip" role="status" aria-live="polite"></div>
      <footer class="note">Getachew Modular Sieve Explorer</footer>
    </main>

    <!-- Right side (stats, LaTeX, legend) -->
    <aside class="side" id="rightPanel" aria-label="information">
      <h2>Current parameters</h2>
      <div class="small" id="params">
        <!-- dynamic content inserted here -->
      </div>

      <h2>Math (dynamic)</h2>
      <div id="mathArea" class="small">
        <!-- MathJax will render these formulas dynamically -->
        <div id="formula1"></div>
        <div id="formula2"></div>
        <div id="formula3"></div>
        <div id="formula4"></div>
      </div>

      <h2>Statistics</h2>
      <div class="legend" id="statsArea">
        <div class="item"><div class="swatch sw-ring"></div> Ring modulus labels</div>
        <div class="item"><div class="swatch sw-gcd1"></div> Residues with gcd = 1</div>
        <div class="item"><div class="swatch sw-pt"></div> Other residues</div>
        <div class="item"><div class="sw-line"></div> Twin residue transition (r, r+2)</div>
      </div>

      <h2>Computed metrics</h2>
      <div id="metrics" class="small">
        <!-- dynamic metrics appear here -->
      </div>
    </aside>
  </div>

<script>
/* Main JavaScript for Getachew Modular Prime Sieve Explorer
   - Computes M = 30 * 2^n
   - Displays residues on concentric rings
   - Detects twin residue transitions (r, r+2 mod M) in Phi(M)
   - Goldbach mode uses sieve-of-eratosthenes primes up to primeBound
   - Riemann Map maps residues to unit circle radial angles
*/

/* Utilities */
const $ = (id) => document.getElementById(id);
const gcd = (a,b) => { a = Math.abs(a); b = Math.abs(b); while(b){ [a,b] = [b, a % b]; } return Math.abs(a); };
const isInteger = (v) => Number.isFinite(v) && Math.floor(v) === v;
const clamp = (v,a,b) => Math.max(a, Math.min(b,v));

/* DOM refs */
const nInput = $('nInput');
const ringsInput = $('ringsInput');
const densityInput = $('densityInput');
const pointSizeInput = $('pointSize');
const viewModeSelect = $('viewMode');
const drawBtn = $('drawBtn');
const animateBtn = $('animateBtn');
const exportBtn = $('exportBtn');
const copyHtml = $('copyHtml');
const resetView = $('resetView');
const params = $('params');
const formula1 = $('formula1');
const formula2 = $('formula2');
const formula3 = $('formula3');
const formula4 = $('formula4');
const metrics = $('metrics');
const tooltip = $('tooltip');

const canvas = $('mainCanvas');
const ctx = canvas.getContext('2d', { alpha: true });

/* State */
const state = {
  anim: { playing:false, t:0, speed:0.8 },
  cachedPoints: [],
  primeBound: 200000, // default prime generation bound; adjustable in code
  primes: [],
  primeSet: new Set(),
  lastParamsHash: '',
};

/* Resize canvas to fit container properly */
function resizeCanvas() {
  const rect = canvas.getBoundingClientRect();
  const dpr = Math.min(window.devicePixelRatio || 1.5, 2);
  canvas.width = Math.round(rect.width * dpr);
  canvas.height = Math.round(rect.height * dpr);
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
}
window.addEventListener('resize', () => { resizeCanvas(); draw(); });
resizeCanvas();

/* Compute M and related values */
function computeM(n) {
  // M = 30 * 2^n
  return 30 * Math.pow(2, n);
}

function eulerPhi(m) {
  // compute phi(m) by prime factors
  let x = m;
  let result = m;
  for (let p = 2; p * p <= x; p++) {
    if (x % p === 0) {
      while (x % p === 0) x /= p;
      result = Math.floor(result * (1 - 1/p));
    }
  }
  if (x > 1) result = Math.floor(result * (1 - 1/x));
  return result;
}

/* Prime sieve (Eratosthenes) for Goldbach and twin checks */
function generatePrimes(limit) {
  limit = Math.max(10, Math.floor(limit));
  const bs = new Uint8Array(limit + 1);
  const primes = [];
  for (let i = 2; i <= limit; i++) {
    if (!bs[i]) {
      primes.push(i);
      if (i * i <= limit) {
        for (let j = i * i; j <= limit; j += i) bs[j] = 1;
      }
    }
  }
  state.primes = primes;
  state.primeSet = new Set(primes);
  return primes;
}

/* Cached primes generation on startup (non-blocking-ish) */
(function initPrimes() {
  // limit to primeBound, but if device slow, reduce
  const limit = clamp(state.primeBound, 20000, 200000);
  // generate in a setTimeout to allow UI paint
  setTimeout(()=> {
    generatePrimes(limit);
    draw(); // redraw once primes are ready
  }, 20);
})();

/* Compute residues to show */
function buildResiduePoints(n, rings, density) {
  const M0 = computeM(n);
  const points = [];
  for (let r = 0; r < rings; r++) {
    const mod = Math.max(1, Math.floor(M0 * Math.pow(2, r)));
    if (mod <= 0) continue;
    for (let a = 0; a < mod; a += Math.max(1, density)) {
      const isGcd1 = gcd(a, mod) === 1;
      points.push({ ring: r, mod, residue: a, isGcd1 });
    }
  }
  return points;
}

/* Twin residue transitions for a given modulus M:
   r is valid if gcd(r,M)=1 and gcd(r+2,M)=1 (both in Phi(M))
*/
function computeTwinResidueTransitions(M) {
  const transitions = [];
  for (let r = 0; r < M; r++) {
    if (gcd(r, M) === 1 && gcd((r + 2) % M, M) === 1) transitions.push(r);
  }
  return transitions;
}

/* Goldbach: return pairs for even numbers up to an upperN (uses primes) */
function computeGoldbachPairs(upperN) {
  // ensure primes are generated up to upperN
  if (!state.primes.length || state.primes[state.primes.length - 1] < upperN) {
    generatePrimes(Math.max(upperN, state.primeBound));
  }
  const primeSet = state.primeSet;
  const pairs = {};
  for (let even = 4; even <= upperN; even += 2) {
    let found = null;
    for (const p of state.primes) {
      if (p > even) break;
      const q = even - p;
      if (q < 2) break;
      if (primeSet.has(q)) { found = [p, q]; break; }
    }
    pairs[even] = found;
  }
  return pairs;
}

/* Riemann map: map residue r in mod M to unit circle point (cos θ, sin θ) with θ = 2πr/M */
function riemannMapPoint(r, M) {
  const theta = 2 * Math.PI * (r % M) / M;
  return { x: Math.cos(theta), y: Math.sin(theta), theta };
}

/* Draw functions */
function clearCanvas() {
  const w = canvas.width / (window.devicePixelRatio || 1);
  const h = canvas.height / (window.devicePixelRatio || 1);
  ctx.clearRect(0, 0, w, h);
  // background subtle
  ctx.fillStyle = 'rgba(0,8,16,0.35)';
  ctx.fillRect(0,0,w,h);
}

function draw() {
  resizeCanvas();
  clearCanvas();

  const n = parseInt(nInput.value, 10) || 0;
  const rings = clamp(parseInt(ringsInput.value, 10) || 5, 1, 10);
  const density = clamp(parseInt(densityInput.value, 10) || 1, 1, 100);
  const viewMode = viewModeSelect.value;
  const pointSize = parseFloat(pointSizeInput.value) || 3;

  // dynamic computed values
  const M0 = computeM(n);
  const phiM0 = eulerPhi(Math.max(1, Math.floor(M0)));
  const T_of_M = 3 * Math.pow(2, n); // user's theorem: T(M) = 3·2^n
  const predicted_Xn = 240 * Math.pow(Math.abs(n), 2.2) * Math.pow(2, Math.max(0, n)); // from user's formula (approx)

  // update params panel
  params.innerHTML = `
    <div><strong>M = 30 · 2^n</strong> → n = ${n}, M₀ = ${M0}</div>
    <div>Rings = ${rings}, density = every ${density}-th residue</div>
  `;

  // math formulas (LaTeX)
  formula1.innerHTML = `\\[M = 30 \\cdot 2^{${n}} = ${M0}\\]`;
  formula2.innerHTML = `\\[\\Phi(M) = \\{r:\\, 0 \\le r < M,\\ \\gcd(r,M)=1\\}\\]`;
  formula3.innerHTML = `\\[T(M) \\;\\approx\\; 3\\cdot 2^{${n}} = ${T_of_M}\\]`;
  formula4.innerHTML = `\\[X_n \\approx 240\\cdot n^{2.2}\\cdot 2^{n} \\approx ${Math.round(predicted_Xn)}\\]`;
  if (window.MathJax && window.MathJax.typesetClear) {
    MathJax.typesetClear([formula1, formula2, formula3, formula4]);
    MathJax.typesetPromise([formula1, formula2, formula3, formula4]).catch(()=>{});
  }

  // compute points once per draw
  const points = buildResiduePoints(n, rings, density);

  // layout
  const rect = canvas.getBoundingClientRect();
  const w = rect.width;
  const h = rect.height;
  const cx = w / 2;
  const cy = h / 2;
  const maxRadius = Math.min(w, h) / 2 - 64;
  const ringSpacing = maxRadius / Math.max(1, rings);

  // draw ring outlines and labels
  ctx.save();
  ctx.lineWidth = 1.2;
  ctx.font = '12px Inter, Arial';
  ctx.fillStyle = 'rgba(230,238,248,0.9)';
  for (let r = 0; r < rings; r++) {
    const radius = ringSpacing * (r + 0.8);
    ctx.beginPath();
    ctx.strokeStyle = 'rgba(255,255,255,0.06)';
    ctx.arc(cx, cy, radius, 0, Math.PI*2);
    ctx.stroke();

    const mod = Math.max(1, Math.floor(M0 * Math.pow(2, r)));
    ctx.fillStyle = 'rgba(230,238,248,0.7)';
    ctx.fillText(`mod ${mod}`, cx + radius - 64, cy - 8);
  }

  // draw mode-specific overlays
  if (viewMode === 'residue' || viewMode === 'twin' || viewMode === 'lifts' || viewMode === 'riemann') {
    // draw residues for each ring
    for (const p of points) {
      const r = p.ring;
      const mod = p.mod;
      const radius = ringSpacing * (r + 0.8);
      // angle: place 0 at top (-pi/2)
      const angle = (p.residue / mod) * Math.PI * 2 - Math.PI/2;
      const animOffset = state.anim.playing ? Math.sin(state.anim.t * state.anim.speed + r) * 0.08 * (r / rings) : 0;
      const radAdj = radius * (1 + animOffset);

      const x = cx + Math.cos(angle) * radAdj;
      const y = cy + Math.sin(angle) * radAdj;

      // store for hit testing
      p._x = x; p._y = y; p._radius = pointSize;

      // style
      if (p.isGcd1) {
        // gcd1 visible color
        const hue = 190 + Math.floor(60 * (r / Math.max(1,rings-1 || 1)));
        ctx.fillStyle = `hsl(${hue}deg 90% 60%)`;
      } else {
        ctx.fillStyle = 'rgba(255,255,255,0.07)';
      }

      // larger highlight for gcd1
      if (p.isGcd1) {
        ctx.beginPath();
        ctx.globalAlpha = 0.08;
        ctx.fillStyle = ctx.fillStyle;
        ctx.arc(x, y, pointSize * 2.1, 0, Math.PI*2);
        ctx.fill();
        ctx.globalAlpha = 1;
      }

      // draw point
      ctx.beginPath();
      ctx.arc(x, y, pointSize, 0, Math.PI*2);
      ctx.fill();
    }

    // twin transitions overlay (lines connecting r -> r+2 mod M0) shown on base ring
    if (viewMode === 'twin') {
      const Mbase = Math.max(1, Math.floor(M0));
      const twinResidues = computeTwinResidueTransitions(Mbase);
      ctx.beginPath();
      for (const r of twinResidues) {
        const ringIdx = 0; // draw on base ring
        const radius = ringSpacing * (ringIdx + 0.8);
        const a1 = (r / Mbase) * Math.PI*2 - Math.PI/2;
        const a2 = ((r + 2) % Mbase / Mbase) * Math.PI*2 - Math.PI/2;
        const x1 = cx + Math.cos(a1) * radius;
        const y1 = cy + Math.sin(a1) * radius;
        const x2 = cx + Math.cos(a2) * radius;
        const y2 = cy + Math.sin(a2) * radius;
        // line
        ctx.strokeStyle = 'rgba(102,194,255,0.9)';
        ctx.lineWidth = 1.2;
        ctx.beginPath();
        ctx.moveTo(x1, y1);
        ctx.lineTo(x2, y2);
        ctx.stroke();
        // small cap dots
        ctx.beginPath();
        ctx.fillStyle = 'rgba(102,194,255,0.95)';
        ctx.arc(x1, y1, Math.max(1.8, pointSize), 0, Math.PI*2);
        ctx.arc(x2, y2, Math.max(1.8, pointSize), 0, Math.PI*2);
        ctx.fill();
      }
    }

    // modular lifts mode: show mapping from residues in ring r to ring r+1 (M -> 2M)
    if (viewMode === 'lifts') {
      // draw thin arcs showing lifts between ring r and r+1 for first rings only
      for (let r = 0; r < Math.min(rings-1, 3); r++) {
        const Mcur = Math.max(1, Math.floor(M0 * Math.pow(2, r)));
        const Mnext = Math.max(1, Math.floor(M0 * Math.pow(2, r+1)));
        for (let a = 0; a < Mcur; a += Math.max(1, density)) {
          if (gcd(a, Mcur) !== 1) continue;
          // lift candidates: a and a + k*Mcur for k in {0,1} mapping to mod Mnext
          const aNext1 = a; // same residue value
          const aNext2 = (a + Mcur) % Mnext;
          // coordinates on current ring
          const radiusCur = ringSpacing * (r + 0.8);
          const angleCur = (a / Mcur) * Math.PI*2 - Math.PI/2;
          const x1 = cx + Math.cos(angleCur) * radiusCur;
          const y1 = cy + Math.sin(angleCur) * radiusCur;
          // coordinates on next ring for both lifts
          const radiusNext = ringSpacing * (r + 1 + 0.8);
          const angleNext1 = (aNext1 / Mnext) * Math.PI*2 - Math.PI/2;
          const angleNext2 = (aNext2 / Mnext) * Math.PI*2 - Math.PI/2;
          const x2 = cx + Math.cos(angleNext1) * radiusNext;
          const y2 = cy + Math.sin(angleNext1) * radiusNext;
          const x3 = cx + Math.cos(angleNext2) * radiusNext;
          const y3 = cy + Math.sin(angleNext2) * radiusNext;
          // draw small connecting lines
          ctx.strokeStyle = 'rgba(167,139,250,0.6)';
          ctx.lineWidth = 0.8;
          ctx.beginPath();
          ctx.moveTo(x1, y1);
          ctx.lineTo(x2, y2);
          ctx.stroke();
          ctx.beginPath();
          ctx.moveTo(x1, y1);
          ctx.lineTo(x3, y3);
          ctx.stroke();
        }
      }
    }

    // Riemann map: if selected, draw unit circle at side and map residues to circle points
    if (viewMode === 'riemann') {
      const radiusUIC = Math.min(w, h) * 0.16;
      const cxU = w - radiusUIC - 24;
      const cyU = radiusUIC + 28;
      // circle base
      ctx.beginPath();
      ctx.strokeStyle = 'rgba(255,255,255,0.08)';
      ctx.lineWidth = 1.2;
      ctx.arc(cxU, cyU, radiusUIC, 0, Math.PI*2);
      ctx.stroke();
      // draw residues from base modulus on the unit circle
      const Mbase = Math.max(1, Math.floor(M0));
      for (let r = 0; r < Mbase; r += Math.max(1, density)) {
        const pt = riemannMapPoint(r, Mbase);
        const x = cxU + pt.x * radiusUIC;
        const y = cyU + pt.y * radiusUIC;
        // color by gcd1
        ctx.beginPath();
        ctx.fillStyle = gcd(r, Mbase) === 1 ? 'rgba(54,211,153,0.95)' : 'rgba(255,255,255,0.06)';
        ctx.arc(x, y, Math.max(1.6, pointSize * 0.9), 0, Math.PI*2);
        ctx.fill();
      }
      // annotation
      ctx.fillStyle = 'rgba(230,238,248,0.8)';
      ctx.font = '12px Inter, Arial';
      ctx.fillText('Riemann map (residues on unit circle)', cxU - radiusUIC + 6, cyU + radiusUIC + 16);
    }
  }

  // Goldbach view: show pair mapping in a small lattice area
  if (viewMode === 'goldbach') {
    const upper = Math.min(2000, Math.max(200, Math.floor(M0))); // compute modest range to keep UI responsive
    const pairs = computeGoldbachPairs(upper);
    // layout a small grid at left
    const gridLeft = 28;
    const gridTop = 28;
    let y = gridTop;
    ctx.fillStyle = 'rgba(230,238,248,0.8)';
    ctx.font = '12px Inter, Arial';
    ctx.fillText(`Goldbach pairs (even ≤ ${upper}) — first 40 shown`, gridLeft, y);
    y += 18;
    let count = 0;
    for (let even = 4; even <= upper; even += 2) {
      const pair = pairs[even];
      const txt = pair ? `${even} = ${pair[0]} + ${pair[1]}` : `${even} = (no pair found)`;
      ctx.fillStyle = pair ? 'rgba(102,194,255,0.95)' : 'rgba(255,110,110,0.9)';
      ctx.fillText(txt, gridLeft, y);
      y += 14;
      if (++count >= 40) break;
    }
  }

  // update metrics panel
  const twinResiduesCount = computeTwinResidueTransitions(Math.max(1, Math.floor(M0))).length;
  metrics.innerHTML = `
    <div>φ(M₀) = ${phiM0}</div>
    <div>Predicted T(M) = ${T_of_M} (user theorem)</div>
    <div>Detected twin residue transitions (r, r+2 mod M₀): ${twinResiduesCount}</div>
    <div>Predicted completion threshold Xₙ ≈ ${Math.round(predicted_Xn)}</div>
    <div>Primes cached: ${state.primes.length}</div>
  `;

  ctx.restore();
}

/* Hit testing and tooltip */
function pageToCanvasCoords(e) {
  const rect = canvas.getBoundingClientRect();
  return { x: e.clientX - rect.left, y: e.clientY - rect.top };
}

canvas.addEventListener('mousemove', (ev) => {
  const pos = pageToCanvasCoords(ev);
  const found = hitTest(pos);
  if (found) {
    tooltip.style.display = 'block';
    tooltip.style.left = (ev.clientX - canvas.getBoundingClientRect().left) + 'px';
    tooltip.style.top = (ev.clientY - canvas.getBoundingClientRect().top) + 'px';
    tooltip.textContent = `ring ${found.ring}, mod ${found.mod}, r=${found.residue}, gcd=${gcd(found.residue, found.mod)}`;
  } else {
    tooltip.style.display = 'none';
  }
});
canvas.addEventListener('mouseleave', () => { tooltip.style.display = 'none'; });

function hitTest(pos) {
  // loop through last built points
  // Reconstruct current points quickly (same as draw)
  const n = parseInt(nInput.value, 10) || 0;
  const rings = clamp(parseInt(ringsInput.value, 10) || 5, 1, 10);
  const density = clamp(parseInt(densityInput.value, 10) || 1, 1, 100);
  const pointSize = parseFloat(pointSizeInput.value) || 3;

  const M0 = computeM(n);
  const rect = canvas.getBoundingClientRect();
  const w = rect.width;
  const h = rect.height;
  const cx = w / 2;
  const cy = h / 2;
  const maxRadius = Math.min(w, h) / 2 - 64;
  const ringSpacing = maxRadius / Math.max(1, rings);

  // search through rings from outermost inward
  for (let r = rings - 1; r >= 0; r--) {
    const mod = Math.max(1, Math.floor(M0 * Math.pow(2, r)));
    const radius = ringSpacing * (r + 0.8);
    for (let a = 0; a < mod; a += Math.max(1, density)) {
      const angle = (a / mod) * Math.PI*2 - Math.PI/2;
      const x = cx + Math.cos(angle) * radius;
      const y = cy + Math.sin(angle) * radius;
      const dx = pos.x - x;
      const dy = pos.y - y;
      const rdist = Math.sqrt(dx*dx + dy*dy);
      if (rdist <= Math.max(4, pointSize + 2)) {
        return { ring: r, mod, residue: a };
      }
    }
  }
  return null;
}

/* Controls wiring */
drawBtn.addEventListener('click', () => { draw(); });

animateBtn.addEventListener('click', () => {
  state.anim.playing = !state.anim.playing;
  animateBtn.textContent = state.anim.playing ? 'Pause' : 'Play';
});

resetView.addEventListener('click', () => {
  nInput.value = '0';
  ringsInput.value = '5';
  densityInput.value = '1';
  pointSizeInput.value = '3';
  viewModeSelect.value = 'residue';
  draw();
});

exportBtn.addEventListener('click', () => {
  const link = document.createElement('a');
  link.download = `getachew_modular_M${computeM(parseInt(nInput.value,10))}_view.png`;
  link.href = canvas.toDataURL('image/png');
  link.click();
});

copyHtml.addEventListener('click', async () => {
  try {
    const htmlText = '<!-- Save this HTML as a single file to run the Getachew Modular Sieve Explorer -->\n' + document.documentElement.outerHTML;
    await navigator.clipboard.writeText(htmlText);
    copyHtml.textContent = 'Copied';
    setTimeout(()=> copyHtml.textContent = 'Copy HTML', 1500);
  } catch (e) {
    copyHtml.textContent = 'Copy failed';
    setTimeout(()=> copyHtml.textContent = 'Copy HTML', 1500);
  }
});

/* Keyboard: space toggles animation, Enter draws */
window.addEventListener('keydown', (e) => {
  if (e.code === 'Space') {
    e.preventDefault();
    state.anim.playing = !state.anim.playing;
    animateBtn.textContent = state.anim.playing ? 'Pause' : 'Play';
  } else if (e.key === 'Enter') {
    draw();
  }
});

/* Animation loop */
let lastTime = performance.now();
function rafLoop(ts) {
  const dt = (ts - lastTime) / 1000;
  lastTime = ts;
  if (state.anim.playing) {
    state.anim.t += dt;
    draw();
  }
  requestAnimationFrame(rafLoop);
}
requestAnimationFrame(rafLoop);

/* Initial draw */
draw();

</script>
</body>
</html>
