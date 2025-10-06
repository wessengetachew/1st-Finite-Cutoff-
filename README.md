<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Visualizing the Riemann Zeta Function - Wessen Getachew</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        
        body {
            font-family: 'Georgia', serif;
            line-height: 1.8;
            color: #333;
            background: #f5f5f5;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
            border-radius: 8px;
            overflow: hidden;
        }

        .header {
            background: #2c3e50;
            color: white;
            padding: 40px;
            text-align: center;
        }

        .header h1 {
            font-size: 2em;
            margin-bottom: 15px;
            font-weight: 400;
        }

        .disclaimer {
            background: #fff3cd;
            border-left: 4px solid #ffc107;
            padding: 20px;
            margin: 20px 40px;
            font-size: 0.95em;
        }

        .disclaimer strong {
            color: #856404;
        }

        .nav {
            background: #34495e;
            padding: 12px 0;
            position: sticky;
            top: 0;
            z-index: 100;
        }

        .nav ul {
            list-style: none;
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            gap: 8px;
        }

        .nav a {
            color: white;
            text-decoration: none;
            padding: 8px 16px;
            border-radius: 4px;
            transition: background 0.3s;
        }

        .nav a:hover { background: #2c3e50; }

        .content { padding: 40px; }

        .section {
            margin-bottom: 40px;
        }

        .section h2 {
            color: #2c3e50;
            font-size: 1.8em;
            margin-bottom: 20px;
            border-left: 4px solid #3498db;
            padding-left: 15px;
        }

        .section h3 {
            color: #34495e;
            font-size: 1.3em;
            margin: 25px 0 15px 0;
        }

        .warning-box {
            background: #f8d7da;
            border-left: 4px solid #dc3545;
            padding: 20px;
            margin: 20px 0;
            border-radius: 4px;
        }

        .info-box {
            background: #d1ecf1;
            border-left: 4px solid #17a2b8;
            padding: 20px;
            margin: 20px 0;
            border-radius: 4px;
        }

        .success-box {
            background: #d4edda;
            border-left: 4px solid #28a745;
            padding: 20px;
            margin: 20px 0;
            border-radius: 4px;
        }

        canvas {
            border: 2px solid #3498db;
            border-radius: 5px;
            background: #000;
            display: block;
            margin: 20px auto;
        }

        .controls {
            background: #ecf0f1;
            padding: 20px;
            border-radius: 5px;
            margin: 20px 0;
        }

        .control-group { 
            margin: 15px 0;
        }

        .control-group label {
            display: block;
            margin-bottom: 5px;
            font-weight: 500;
        }

        input[type="range"] {
            width: 100%;
            height: 6px;
            background: #bdc3c7;
            border-radius: 3px;
        }

        button {
            background: #3498db;
            color: white;
            border: none;
            padding: 10px 24px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 1em;
            margin: 8px 4px;
        }

        button:hover { background: #2980b9; }

        table {
            width: 100%;
            border-collapse: collapse;
            margin: 20px 0;
        }

        th {
            background: #34495e;
            color: white;
            padding: 12px;
            text-align: left;
        }

        td { 
            padding: 10px; 
            border-bottom: 1px solid #ecf0f1;
        }

        .code-block {
            background: #2c3e50;
            color: #ecf0f1;
            padding: 15px;
            border-radius: 5px;
            font-family: 'Courier New', monospace;
            margin: 15px 0;
            overflow-x: auto;
            font-size: 0.9em;
        }

        .footer {
            background: #2c3e50;
            color: white;
            padding: 30px;
            text-align: center;
        }

        ul {
            margin: 15px 0 15px 30px;
        }

        li {
            margin: 8px 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Visualizing the Riemann Zeta Function:<br>A Modular-Cayley Coordinate Exploration</h1>
            <div style="margin-top: 10px;">Wessen Getachew • October 2025</div>
            <div style="margin-top: 5px; font-size: 0.9em; opacity: 0.9;">Exploratory Visualization Study</div>
        </div>

        <div class="disclaimer">
            <strong>Important Disclaimer:</strong> This is a visualization and computational study only. It makes <strong>no claims</strong> about proving the Riemann Hypothesis or discovering new mathematical structure. The patterns observed are artifacts of the chosen construction and have pedagogical value but do not constitute mathematical insight into the distribution of zeta zeros.
        </div>

        <nav class="nav">
            <ul>
                <li><a href="#scope">Scope</a></li>
                <li><a href="#framework">Framework</a></li>
                <li><a href="#viz">Visualization</a></li>
                <li><a href="#observations">Observations</a></li>
                <li><a href="#limitations">Limitations</a></li>
                <li><a href="#code">Code</a></li>
            </ul>
        </nav>

        <div class="content">
            <section id="scope" class="section">
                <h2>Scope and Purpose</h2>
                
                <div class="success-box">
                    <h3>What This Work IS:</h3>
                    <ul>
                        <li>A visualization study combining known mathematical concepts</li>
                        <li>An exploration of the Cayley transform applied to the critical line</li>
                        <li>A pedagogical tool for understanding modular arithmetic patterns</li>
                        <li>A computational implementation exercise</li>
                    </ul>
                </div>

                <div class="warning-box">
                    <h3>What This Work IS NOT:</h3>
                    <ul>
                        <li>A proof attempt of the Riemann Hypothesis</li>
                        <li>A claim of discovering new mathematical structure</li>
                        <li>A proposal for a viable RH proof strategy</li>
                        <li>Evidence that modular arithmetic determines zeta zero locations</li>
                    </ul>
                </div>

                <p>The Cayley transform Γ(s) = (s - 1/2)/(s + 1/2) is simply a <strong>coordinate change</strong>. Saying "RH is true iff all zeros satisfy |Γ(ρ)| = 1" is a tautology—it's true by construction but doesn't reduce the problem's difficulty.</p>
            </section>

            <section id="framework" class="section">
                <h2>Mathematical Framework</h2>
                
                <h3>The Cayley Transform</h3>
                <p>For s ∈ ℂ, we define Γ(s) = (s - 1/2)/(s + 1/2). This Möbius transformation maps:</p>
                <ul>
                    <li>The critical line Re(s) = 1/2 → unit circle |Γ| = 1</li>
                    <li>Right half-plane Re(s) > 1/2 → disk interior |Γ| < 1</li>
                    <li>Left half-plane Re(s) < 1/2 → exterior |Γ| > 1</li>
                </ul>

                <div class="info-box">
                    <strong>Connection to Smith Chart:</strong> RF engineers use an identical transform for impedance matching. This is a mathematical coincidence—both contexts use the same Möbius transformation, but there's no known physical connection between impedance and zeta zeros.
                </div>

                <h3>Modular Arithmetic Overlay</h3>
                <p>For visualization purposes, we overlay modular structure:</p>
                <div class="code-block">
For modulus M and residue r:
    θ = 2πr/M           (angular position)
    t = c·θ              (scaling by arbitrary constant c)
    s = 1/2 + it        (on critical line)
    Γ = it/(1 + it)     (Cayley coordinate)
</div>

                <p>We filter for coprime residues where gcd(r,M) = 1 and compute density ρ(M) = φ(M)/M.</p>

                <div class="warning-box">
                    <strong>Critical Disclaimer:</strong> This overlay is chosen for <em>visualization</em>. There is <strong>no established mathematical connection</strong> between residue classes mod M and zeta zero locations. Patterns we observe come from our construction choices, not from intrinsic properties of ζ(s).
                </div>
            </section>

            <section id="viz" class="section">
                <h2>Interactive Visualization</h2>
                
                <div class="controls">
                    <div class="control-group">
                        <label>Maximum Modulus M: <span id="m-val">150</span></label>
                        <input type="range" id="m-slider" min="20" max="300" value="150">
                        <small>Note: Different values create different visual patterns</small>
                    </div>
                    <div class="control-group">
                        <label>Scaling Factor: <span id="scale-val">10</span></label>
                        <input type="range" id="scale-slider" min="5" max="20" value="10">
                        <small>Note: Arbitrary choice affecting visual appearance</small>
                    </div>
                    <div class="control-group">
                        <label><input type="checkbox" id="coprime" checked> Coprime only (gcd=1)</label>
                    </div>
                    <div class="control-group">
                        <label><input type="checkbox" id="zeros" checked> Show known zeros (for reference)</label>
                    </div>
                    <button onclick="draw()">Regenerate</button>
                    <button onclick="exportImg()">Export PNG</button>
                </div>

                <canvas id="canvas" width="800" height="800"></canvas>
                
                <div id="stats" style="background:#ecf0f1; padding:15px; border-radius:5px; margin-top:15px;">
                    <h3>Visualization Statistics</h3>
                    <p id="stats-text">Click Regenerate to compute...</p>
                </div>
            </section>

            <section id="observations" class="section">
                <h2>Observations and Interpretation</h2>
                
                <h3>What We See</h3>
                <p>The visualization shows:</p>
                <ul>
                    <li>Concentration of points near |Γ| = 1 (the unit circle)</li>
                    <li>Density variation: ρ(M) ranges from ~0.2 to ~1.0</li>
                    <li>Rotational symmetry (expected from angular mapping)</li>
                    <li>Prime moduli create denser rings than composite moduli</li>
                </ul>

                <h3>What These Patterns Mean</h3>
                <div class="success-box">
                    <strong>Legitimate observations:</strong>
                    <ul>
                        <li>The Cayley transform successfully maps the critical line to a circle</li>
                        <li>Coprime residues mod M create predictable density patterns</li>
                        <li>Prime moduli have higher ρ(M) than highly composite moduli</li>
                        <li>The visualization has aesthetic and pedagogical value</li>
                    </ul>
                </div>

                <div class="warning-box">
                    <strong>What we CANNOT claim:</strong>
                    <ul>
                        <li>These patterns "explain" why zeros lie on the critical line</li>
                        <li>Modular arithmetic "constrains" zero locations</li>
                        <li>This reveals "emergent structure" relevant to RH</li>
                        <li>This suggests a proof strategy</li>
                    </ul>
                    <p style="margin-top:10px;">The concentration near |Γ| = 1 is largely an artifact of our scaling choices and the Cayley transform's properties.</p>
                </div>

                <h3>Known Zeros</h3>
                <p>The red points show known zeta zeros from established tables. They lie exactly on |Γ| = 1 because they lie on Re(s) = 1/2. This confirms our implementation is correct but provides zero new information about the zeros themselves.</p>
            </section>

            <section id="limitations" class="section">
                <h2>Limitations and Why This Doesn't Advance RH</h2>
                
                <h3>The Coordinate Change Problem</h3>
                <p>Many RH attempts follow this pattern:</p>
                <ol>
                    <li>Find a clever coordinate change or reformulation</li>
                    <li>Observe interesting patterns</li>
                    <li>Hope this makes the problem easier</li>
                    <li>Discover the difficulty is preserved</li>
                </ol>
                <p>This work falls squarely in that category.</p>

                <h3>Why RH Is Actually Hard</h3>
                <p>The Riemann Hypothesis is difficult because:</p>
                <ul>
                    <li>The <strong>analytic continuation</strong> of ζ(s) is subtle</li>
                    <li>The <strong>functional equation</strong> creates complex constraints</li>
                    <li>The <strong>distribution of primes</strong> connects to zeros in non-obvious ways</li>
                    <li>Existing tools from complex analysis aren't sufficient</li>
                </ul>
                <p>Simply rewriting RH in different coordinates doesn't address any of these fundamental difficulties.</p>

                <h3>Pedagogical Value</h3>
                <p>Despite not advancing RH, this work may be useful for:</p>
                <ul>
                    <li>Teaching how conformal maps work</li>
                    <li>Illustrating Euler's totient function visually</li>
                    <li>Providing programming practice with complex mathematics</li>
                    <li>Showing connections between different mathematical areas</li>
                </ul>
            </section>

            <section id="code" class="section">
                <h2>Implementation Code</h2>
                
                <div class="code-block">
def euler_totient(n):
    result = n
    p = 2
    while p * p <= n:
        if n % p == 0:
            while n % p == 0:
                n //= p
            result -= result // p
        p += 1
    if n > 1:
        result -= result // n
    return result

def cayley_transform(sigma, t):
    """Γ(s) = (s - 1/2)/(s + 1/2)"""
    num_real = sigma - 0.5
    num_imag = t
    den_real = sigma + 0.5
    den_imag = t
    mag_sq = den_real**2 + den_imag**2
    return (
        (num_real * den_real + num_imag * den_imag) / mag_sq,
        (num_imag * den_real - num_real * den_imag) / mag_sq
    )

# Generate points
for M in range(1, M_max + 1):
    phi_M = euler_totient(M)
    density = phi_M / M
    for r in range(M):
        if gcd(r, M) != 1: continue
        theta = 2 * pi * r / M
        t = theta * scaling_factor  # Arbitrary choice
        gamma = cayley_transform(0.5, t)
        # Plot (gamma, density, M)
                </div>
            </section>

            <section class="section">
                <h2>Conclusions</h2>
                
                <p>We have presented a visualization technique combining the Cayley transform with modular arithmetic patterns. The resulting visualizations are aesthetically interesting and may have pedagogical value, but they <strong>do not constitute progress</strong> on the Riemann Hypothesis.</p>

                <p>This work is best understood as:</p>
                <ul>
                    <li>An exercise in mathematical visualization</li>
                    <li>A programming project connecting multiple concepts</li>
                    <li>A reminder that pattern recognition ≠ mathematical insight</li>
                </ul>

                <div class="info-box">
                    <strong>For anyone interested in actually working on RH:</strong>
                    <ul>
                        <li>Study existing literature deeply (Edwards, Titchmarsh, Ivić)</li>
                        <li>Get formal training in complex analysis and analytic number theory</li>
                        <li>Work with experienced mentors</li>
                        <li>Build rigorous theoretical foundations</li>
                    </ul>
                </div>
            </section>
        </div>

        <div class="footer">
            <h3>Wessen Getachew • 2025</h3>
            <p>Exploratory Visualization Study</p>
            <p style="margin-top:10px; font-size:0.9em;">This work makes no claims about advancing the Riemann Hypothesis</p>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        const w = canvas.width;
        const h = canvas.height;
        const cx = w/2, cy = h/2;
        const scale = 160;

        const zeros = [14.134725,21.022040,25.010858,30.424876,32.935062,
                       37.586178,40.918719,43.327073,48.005151,49.773832];

        function gcd(a,b) { return b===0 ? a : gcd(b, a%b); }

        function phi(n) {
            let res = n, temp = n;
            for(let p=2; p*p<=temp; p++) {
                if(temp%p===0) {
                    while(temp%p===0) temp/=p;
                    res -= res/p;
                }
            }
            if(temp>1) res -= res/temp;
            return Math.floor(res);
        }

        function cayley(sigma, t) {
            const nr = sigma - 0.5, ni = t;
            const dr = sigma + 0.5, di = t;
            const mag = dr*dr + di*di;
            return {
                x: (nr*dr + ni*di)/mag,
                y: (ni*dr - nr*di)/mag
            };
        }

        function draw() {
            ctx.fillStyle = '#000';
            ctx.fillRect(0,0,w,h);

            const M = parseInt(document.getElementById('m-slider').value);
            const scaleFactor = parseInt(document.getElementById('scale-slider').value);
            const coprimeOnly = document.getElementById('coprime').checked;
            const showZeros = document.getElementById('zeros').checked;

            document.getElementById('m-val').textContent = M;
            document.getElementById('scale-val').textContent = scaleFactor;

            let points = 0, coprimeCount = 0;

            for(let m=1; m<=M; m++) {
                const phiM = phi(m);
                const density = phiM/m;
                
                for(let r=0; r<m; r++) {
                    if(coprimeOnly && gcd(r,m)!==1) continue;
                    
                    points++;
                    if(gcd(r,m)===1) coprimeCount++;
                    
                    const theta = 2*Math.PI*r/m;
                    const t = theta * scaleFactor;
                    const g = cayley(0.5, t);
                    
                    const px = cx + g.x * scale;
                    const py = cy - g.y * scale;
                    
                    const hue = 60 + density*180;
                    ctx.fillStyle = `hsla(${hue},70%,55%,0.3)`;
                    ctx.fillRect(px-1, py-1, 2, 2);
                }
            }

            // Unit circle
            ctx.strokeStyle = '#3498db';
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.arc(cx, cy, scale, 0, 2*Math.PI);
            ctx.stroke();

            // Zeros
            if(showZeros) {
                ctx.fillStyle = '#e74c3c';
                ctx.strokeStyle = '#fff';
                ctx.lineWidth = 1;
                zeros.forEach(t => {
                    const g = cayley(0.5, t);
                    const px = cx + g.x*scale;
                    const py = cy - g.y*scale;
                    ctx.beginPath();
                    ctx.arc(px, py, 4, 0, 2*Math.PI);
                    ctx.fill();
                    ctx.stroke();
                    
                    const g2 = cayley(0.5, -t);
                    const px2 = cx + g2.x*scale;
                    const py2 = cy - g2.y*scale;
                    ctx.beginPath();
                    ctx.arc(px2, py2, 4, 0, 2*Math.PI);
                    ctx.fill();
                    ctx.stroke();
                });
            }

            document.getElementById('stats-text').innerHTML = `
                <strong>Modulus range:</strong> 1 to ${M}<br>
                <strong>Scaling factor:</strong> ${scaleFactor} (arbitrary choice)<br>
                <strong>Total points:</strong> ${points.toLocaleString()}<br>
                <strong>Coprime points:</strong> ${coprimeCount.toLocaleString()} (${(100*coprimeCount/points).toFixed(1)}%)<br>
                <strong>Known zeros:</strong> ${showZeros ? '20 shown (±10 zeros)' : 'Hidden'}<br>
                <em>Note: Patterns depend on parameter choices</em>
            `;
        }

        function exportImg() {
            const link = document.createElement('a');
            link.download = 'cayley-modular-visualization.png';
            link.href = canvas.toDataURL();
            link.click();
        }

        document.getElementById('m-slider').addEventListener('input', () => {
            document.getElementById('m-val').textContent = document.getElementById('m-slider').value;
        });

        document.getElementById('scale-slider').addEventListener('input', () => {
            document.getElementById('scale-val').textContent = document.getElementById('scale-slider').value;
        });

        draw();
    </script>
</body>
</html>
