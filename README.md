<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Modular Rings — Interactive Visualizer</title>
  <style>
    :root{
      --bg:#0f1724;
      --card:#0b1220;
      --accent:#7dd3fc;
      --muted:#94a3b8;
      --good:#34d399;
      --danger:#fb7185;
      --glass: rgba(255,255,255,0.03);
      --panel-w: 340px;
      --ui-pad: 14px;
      --radius: 8px;
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      color: #e6eef8;
    }
    html,body{
      height:100%;
      margin:0;
      background: linear-gradient(180deg,var(--bg),#061226 120%);
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
      overflow:hidden;
    }
    .app {
      display:flex;
      height:100vh;
      gap:18px;
      padding:18px;
      box-sizing:border-box;
    }
    .panel {
      width:var(--panel-w);
      min-width:240px;
      background:linear-gradient(180deg,var(--card), rgba(255,255,255,0.02));
      border-radius:12px;
      padding:var(--ui-pad);
      box-shadow: 0 6px 22px rgba(2,6,23,0.6);
      display:flex;
      flex-direction:column;
      gap:12px;
    }
    .panel h2 {
      margin:0;
      font-size:18px;
      color:var(--accent);
    }
    label { font-size:13px; color:var(--muted); }
    input[type="number"], input[type="range"], select {
      width:100%;
      padding:8px 10px;
      background:var(--glass);
      border:1px solid rgba(255,255,255,0.04);
      color:inherit;
      border-radius:8px;
      box-sizing:border-box;
      margin-top:6px;
    }
    .row { display:flex; gap:8px; align-items:center; }
    .btn {
      background:linear-gradient(180deg,var(--accent), #3fc5ff);
      border:none;
      padding:8px 12px;
      border-radius:10px;
      color:#06202a;
      font-weight:700;
      cursor:pointer;
      box-shadow: 0 4px 10px rgba(125,211,252,0.08);
    }
    .btn.secondary {
      background:transparent;
      border:1px solid rgba(255,255,255,0.06);
      color:var(--muted);
      font-weight:600;
    }
    .controls { display:flex; flex-direction:column; gap:10px; }
    .footer {
      margin-top:auto;
      font-size:12px;
      color:var(--muted);
    }

    /* Canvas / viewport */
    .viewport {
      flex:1;
      border-radius:12px;
      position:relative;
      overflow:hidden;
      display:flex;
      align-items:stretch;
      justify-content:center;
      background:
        radial-gradient(circle at 10% 10%, rgba(125,211,252,0.03), transparent 6%),
        linear-gradient(180deg, rgba(255,255,255,0.01), transparent 40%);
      padding:14px;
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.02);
    }
    canvas {
      background:transparent;
      width:100%;
      height:100%;
      border-radius:8px;
      display:block;
    }
    .legend {
      position:absolute;
      left:28px;
      top:18px;
      background:rgba(2,6,23,0.6);
      padding:8px 12px;
      border-radius:10px;
      font-size:13px;
      color:var(--muted);
      border:1px solid rgba(255,255,255,0.03);
    }
    .legend .item {display:flex;gap:8px;align-items:center;margin-bottom:6px}
    .swatch { width:12px;height:12px;border-radius:3px;flex:0 0 12px; }
    .sw-accent{background:var(--accent);}
    .sw-gcd1{background:var(--good);}
    .sw-pt{background:rgba(255,255,255,0.08);}

    .tooltip {
      pointer-events:none;
      position:absolute;
      background:rgba(2,6,23,0.9);
      color:var(--accent);
      padding:8px 10px;
      font-size:13px;
      border-radius:8px;
      border:1px solid rgba(125,211,252,0.08);
      transform:translate(-50%,-120%);
      white-space:nowrap;
      display:none;
      z-index:50;
    }

    /* responsive */
    @media (max-width:900px){
      .app{flex-direction:column;padding:12px}
      .panel{width:100%;min-width:unset;order:2}
      .viewport{order:1;height:60vh}
    }
    footer.note{
      position:absolute;
      right:18px;
      bottom:16px;
      color:rgba(255,255,255,0.06);
      font-size:12px;
    }
    .small { font-size:12px; color:var(--muted); }
    .control-row { display:flex; gap:8px; align-items:center; }
  </style>
</head>
<body>
  <div class="app" role="application" aria-label="Modular Rings Visualizer">
    <div class="panel" aria-hidden="false">
      <h2>Modular Rings — Visualizer</h2>
      <div class="controls" role="region" aria-label="controls">
        <div>
          <label for="baseMod">Base modulus M</label>
          <input id="baseMod" type="number" min="1" step="1" value="30" />
        </div>

        <div>
          <label for="rings">Number of rings</label>
          <input id="rings" type="number" min="1" max="10" step="1" value="5" />
        </div>

        <div>
          <label for="pointsPerRing">Residues per ring (show every k-th residue)</label>
          <input id="pointsPerRing" type="number" min="1" step="1" value="1" />
        </div>

        <div>
          <label for="pointSize">Point size (px)</label>
          <input id="pointSize" type="range" min="1" max="8" step="0.5" value="3" />
        </div>

        <div class="control-row">
          <label><input id="showGcd1" type="checkbox" checked /> show only gcd(r, m)=1</label>
        </div>

        <div class="control-row">
          <button id="drawBtn" class="btn">Draw</button>
          <button id="animateBtn" class="btn secondary">Play</button>
        </div>

        <div class="control-row">
          <button id="exportPng" class="btn secondary">Export PNG</button>
          <button id="copyHtml" class="btn">Copy HTML</button>
        </div>

        <div class="control-row small">
          <label for="colorMode">Color mode</label>
          <select id="colorMode" style="width:160px;">
            <option value="gradient">Gradient by ring</option>
            <option value="solid">Solid points</option>
            <option value="monochrome">Monochrome</option>
          </select>
        </div>

        <div class="footer small">
          Tip: Hover points for details. Click a point to pin info. Press <strong>space</strong> to play/pause animation.
          <div style="margin-top:8px;color:var(--muted)">Built for modular residue/gcd visualization.</div>
        </div>
      </div>
    </div>

    <div class="viewport" role="region" aria-label="visualization">
      <div class="legend" aria-hidden="true">
        <div class="item"><div class="swatch sw-accent"></div> modulus ring</div>
        <div class="item"><div class="swatch sw-gcd1"></div> gcd = 1 residues</div>
        <div class="item"><div class="swatch sw-pt"></div> other residues</div>
      </div>

      <canvas id="vizCanvas" width="1200" height="800"></canvas>
      <div id="tooltip" class="tooltip" role="status" aria-live="polite"></div>
    </div>
  </div>

  <footer class="note">Modular Rings • interactive demo</footer>

  <script>
    // Modular Rings Visualizer
    // Single-file interactive HTML. No external dependencies.
    // Author: ChatGPT (generated). Feel free to modify.

    // --- Utilities ---------------------------------------------------------
    const $ = (id) => document.getElementById(id);
    const gcd = (a,b) => { a = Math.abs(a); b = Math.abs(b); while(b){ [a,b] = [b, a % b]; } return a; };

    // --- Canvas setup -----------------------------------------------------
    const canvas = $('vizCanvas');
    const ctx = canvas.getContext('2d', { alpha: true });
    function resizeCanvasToDisplaySize() {
      const dpr = Math.min(window.devicePixelRatio || 1, 2);
      const rect = canvas.getBoundingClientRect();
      canvas.width = Math.round(rect.width * dpr);
      canvas.height = Math.round(rect.height * dpr);
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
    }
    window.addEventListener('resize', () => { resizeCanvasToDisplaySize(); draw(); });
    resizeCanvasToDisplaySize();

    // --- State -------------------------------------------------------------
    const state = {
      baseMod: 30,
      rings: 5,
      pointsPerRingSkip: 1,
      pointSize: 3,
      showGcd1: true,
      colorMode: 'gradient',
      anim: { playing:false, t:0, speed:0.8 },
      pinned: null,
      cached: [] // precomputed points
    };

    // DOM refs
    const baseModInput = $('baseMod');
    const ringsInput = $('rings');
    const pointsPerRingInput = $('pointsPerRing');
    const pointSizeInput = $('pointSize');
    const showGcd1Input = $('showGcd1');
    const drawBtn = $('drawBtn');
    const animateBtn = $('animateBtn');
    const exportPng = $('exportPng');
    const tooltip = $('tooltip');
    const colorModeSelect = $('colorMode');
    const copyHtml = $('copyHtml');

    // initialize UI values
    baseModInput.value = state.baseMod;
    ringsInput.value = state.rings;
    pointsPerRingInput.value = state.pointsPerRingSkip;
    pointSizeInput.value = state.pointSize;
    showGcd1Input.checked = state.showGcd1;
    colorModeSelect.value = state.colorMode;

    function computePoints() {
      const M = parseInt(baseModInput.value) || 30;
      const rings = parseInt(ringsInput.value) || 5;
      const skip = Math.max(1, parseInt(pointsPerRingInput.value) || 1);
      const points = [];
      for (let r = 0; r < rings; r++) {
        const mod = M * Math.pow(2, r); // use doubling sequence M, 2M, 4M, 8M...
        const N = Math.max(1, mod);
        for (let a = 0; a < N; a += skip) {
          const angle = (a / N) * Math.PI * 2 - Math.PI/2; // start at top
          const isGcd1 = gcd(a, mod) === 1;
          points.push({ ring: r, mod, residue: a, N, angle, isGcd1 });
        }
      }
      state.cached = points;
      return points;
    }

    // Color helpers
    function ringColor(r, total) {
      // gradient from cyan -> purple -> orange
      const t = total <= 1 ? 0 : r/(total-1);
      const c1 = [125,211,252]; // cyan
      const c2 = [167,139,250]; // purple
      const c3 = [255,179,71];  // orange
      // blend c1->c2->c3
      if (t < 0.5) {
        const u = t / 0.5;
        return lerpColor(c1, c2, u);
      } else {
        const u = (t-0.5) / 0.5;
        return lerpColor(c2, c3, u);
      }
    }
    function lerpColor(a,b,t){ return `rgb(${Math.round(a[0]+(b[0]-a[0])*t)},${Math.round(a[1]+(b[1]-a[1])*t)},${Math.round(a[2]+(b[2]-a[2])*t)})`; }

    // --- Draw logic -------------------------------------------------------
    function clear() {
      ctx.clearRect(0,0,canvas.width,canvas.height);
    }

    function draw() {
      resizeCanvasToDisplaySize();
      clear();
      const pts = state.cached.length ? state.cached : computePoints();
      const rings = Math.max(1, parseInt(ringsInput.value) || 5);
      const padding = 28;
      const vw = canvas.width / (window.devicePixelRatio || 1);
      const vh = canvas.height / (window.devicePixelRatio || 1);
      const cx = vw/2;
      const cy = vh/2;
      // radius range
      const maxRadius = Math.min(vw, vh) / 2 - 64;
      const ringSpacing = maxRadius / Math.max(1, rings);
      // draw rings
      for (let r = rings-1; r >= 0; r--) {
        const radius = ringSpacing * (r + 0.7); // slight offset
        ctx.beginPath();
        ctx.lineWidth = 1.2;
        ctx.strokeStyle = 'rgba(255,255,255,0.03)';
        ctx.arc(cx, cy, radius, 0, Math.PI*2);
        ctx.stroke();

        // ring label
        const modForLabel = (parseInt(baseModInput.value) || 30) * Math.pow(2, r);
        ctx.fillStyle = 'rgba(255,255,255,0.04)';
        ctx.font = '12px Inter, Arial';
        ctx.fillText(`mod ${modForLabel}`, cx + radius - 52, cy - 8);
      }

      // animate radial offset (for visual effect)
      const animOffset = state.anim.playing ? Math.sin(state.anim.t * state.anim.speed) * 0.3 : 0;

      // draw points
      for (const p of pts) {
        if (showGcd1Input.checked && !p.isGcd1) continue;
        const r = p.ring;
        const radius = ringSpacing * (r + 0.7) * (1 + animOffset * (r / Math.max(1, rings-1)));
        const x = cx + Math.cos(p.angle) * radius;
        const y = cy + Math.sin(p.angle) * radius;

        // style
        const size = parseFloat(pointSizeInput.value) || 3;
        if (p.isGcd1) {
          ctx.fillStyle = (state.colorMode === 'monochrome') ? 'rgb(52,211,153)' : (state.colorMode === 'solid' ? 'rgb(125,211,252)' : ringColor(r, rings));
        } else {
          ctx.fillStyle = 'rgba(255,255,255,0.08)';
        }
        // subtle radial glow for gcd1
        if (p.isGcd1) {
          ctx.beginPath();
          ctx.globalAlpha = 0.08;
          ctx.fillStyle = ctx.fillStyle;
          ctx.arc(x, y, size * 2.2, 0, Math.PI*2);
          ctx.fill();
          ctx.globalAlpha = 1;
        }
        // draw dot
        ctx.beginPath();
        ctx.arc(x, y, size, 0, Math.PI*2);
        ctx.fill();

        // attach cached canvas coordinates for hit-testing
        p._x = x;
        p._y = y;
        p._radius = size;
      }

      // pinned info (if any)
      if (state.pinned) {
        const p = state.pinned;
        // draw a highlighted halo
        ctx.beginPath();
        ctx.lineWidth = 2;
        ctx.strokeStyle = 'rgba(125,211,252,0.9)';
        ctx.arc(p._x, p._y, p._radius + 6, 0, Math.PI*2);
        ctx.stroke();
      }
    }

    // initial compute & draw
    computePoints();
    draw();

    // --- Interaction / hit-testing ---------------------------------------
    function findPointAt(pos) {
      const pts = state.cached;
      // search from topmost (largest ring)
      for (let i = pts.length - 1; i >= 0; i--) {
        const p = pts[i];
        if (showGcd1Input.checked && !p.isGcd1) continue;
        if (!p._x) continue;
        const dx = pos.x - p._x;
        const dy = pos.y - p._y;
        const r = p._radius + 4;
        if (dx*dx + dy*dy <= r*r) return p;
      }
      return null;
    }

    function pageToCanvas(e) {
      const rect = canvas.getBoundingClientRect();
      const x = (e.clientX - rect.left);
      const y = (e.clientY - rect.top);
      return { x, y };
    }

    canvas.addEventListener('mousemove', (ev) => {
      const pos = pageToCanvas(ev);
      const p = findPointAt(pos);
      if (p) {
        showTooltip(ev.clientX, ev.clientY, `mod ${p.mod}, r=${p.residue} (N=${p.N}) — gcd=${gcd(p.residue,p.mod)}`);
      } else {
        hideTooltip();
      }
    });

    canvas.addEventListener('mouseleave', (ev) => { hideTooltip(); });

    canvas.addEventListener('click', (ev) => {
      const pos = pageToCanvas(ev);
      const p = findPointAt(pos);
      if (p) {
        state.pinned = p;
        showTooltip(ev.clientX, ev.clientY, `Pinned → mod ${p.mod}, r=${p.residue} — gcd=${gcd(p.residue,p.mod)}. Click pin again to unpin.`);
      } else {
        state.pinned = null;
        hideTooltip();
      }
      draw();
    });

    function showTooltip(clientX, clientY, text) {
      tooltip.style.display = 'block';
      tooltip.textContent = text;
      // position relative to viewport bounds
      const rect = canvas.getBoundingClientRect();
      tooltip.style.left = (clientX - rect.left) + 'px';
      tooltip.style.top = (clientY - rect.top) + 'px';
    }
    function hideTooltip() {
      if (state.pinned) return; // keep pinned
      tooltip.style.display = 'none';
    }

    // --- Animation loop --------------------------------------------------
    let lastTime = 0;
    function loop(ts) {
      const dt = (ts - lastTime) / 1000;
      lastTime = ts;
      if (state.anim.playing) {
        state.anim.t += dt;
        draw();
      }
      requestAnimationFrame(loop);
    }
    requestAnimationFrame(loop);

    // --- UI wiring -------------------------------------------------------
    drawBtn.addEventListener('click', () => {
      computePoints();
      draw();
    });

    animateBtn.addEventListener('click', () => {
      toggleAnimation();
    });

    function toggleAnimation() {
      state.anim.playing = !state.anim.playing;
      animateBtn.textContent = state.anim.playing ? 'Pause' : 'Play';
      if (!state.anim.playing) draw();
    }
    // space toggles animation
    window.addEventListener('keydown', (e) => {
      if (e.code === 'Space') {
        e.preventDefault();
        toggleAnimation();
      }
    });

    // live updates for inputs (minor performance: only recompute on Draw click)
    baseModInput.addEventListener('input', () => { /* leave for Draw */ });
    ringsInput.addEventListener('input', () => { /* leave for Draw */ });
    pointsPerRingInput.addEventListener('input', () => { /* leave for Draw */ });
    pointSizeInput.addEventListener('input', () => { state.pointSize = parseFloat(pointSizeInput.value); draw(); });
    showGcd1Input.addEventListener('change', () => { draw(); });
    colorModeSelect.addEventListener('change', () => { state.colorMode = colorModeSelect.value; draw(); });

    // Export PNG
    exportPng.addEventListener('click', () => {
      // produce an image from the canvas
      const link = document.createElement('a');
      link.download = `modular_rings_M${baseModInput.value}_rings${ringsInput.value}.png`;
      // to avoid cross-origin problems, the canvas is same-origin (no external images)
      link.href = canvas.toDataURL('image/png');
      link.click();
    });

    // Copy HTML to clipboard (helpful convenience)
    copyHtml.addEventListener('click', async () => {
      try {
        // Reconstruct the original HTML (this file) by grabbing outerHTML
        // We'll copy a minimal instruction with a message pointing to the canvas data
        const htmlText = `<!-- Save this full HTML into a file named modular_rings.html and open in a browser -->\n` +
          document.documentElement.outerHTML;
        await navigator.clipboard.writeText(htmlText);
        copyHtml.textContent = 'Copied!';
        setTimeout(()=> copyHtml.textContent = 'Copy HTML', 1500);
      } catch (err) {
        copyHtml.textContent = 'Denied';
        setTimeout(()=> copyHtml.textContent = 'Copy HTML', 1500);
      }
    });

    // Recompute when the numeric inputs lose focus (for convenience)
    [baseModInput, ringsInput, pointsPerRingInput].forEach(el => {
      el.addEventListener('change', () => { computePoints(); draw(); });
      el.addEventListener('keyup', (e) => { if (e.key === 'Enter') { computePoints(); draw(); } });
    });

    // Keep canvas crisp on hot-reload: recompute on visibility change
    document.addEventListener('visibilitychange', () => { if (!document.hidden) draw(); });

    // Initial instruction overlay
    setTimeout(()=> {
      // gentle first-frame draw
      draw();
    }, 50);
  </script>
</body>
</html>
