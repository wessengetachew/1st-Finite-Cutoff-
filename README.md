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
                        <label>Maximum Modulus M: <span id="m-val">500</span></label>
                        <input type="range" id="m-slider" min="20" max="2000" value="500">
                        <small>Note: Values >1000 may slow rendering. Reveals finer structure.</small>
                    </div>
                    <div class="control-group">
                        <label>Scaling Factor: <span id="scale-val">10</span></label>
                        <input type="range" id="scale-slider" min="1" max="50" value="10" step="0.5">
                        <small>Note: Higher values map to larger imaginary parts</small>
                    </div>
                    <div class="control-group">
                        <label>Point Size: <span id="size-val">2</span>px</label>
                        <input type="range" id="size-slider" min="1" max="5" value="2">
                        <small>Adjust for high-density visualizations</small>
                    </div>
                    <div class="control-group">
                        <label>Opacity: <span id="opacity-val">0.3</span></label>
                        <input type="range" id="opacity-slider" min="0.1" max="1" value="0.3" step="0.1">
                        <small>Lower opacity better for overlapping points</small>
                    </div>
                    <div class="control-group">
                        <label><input type="checkbox" id="coprime" checked> Coprime only (gcd=1)</label>
                    </div>
                    <div class="control-group">
                        <label><input type="checkbox" id="zeros" checked> Show known zeros</label>
                    </div>
                    <div class="control-group">
                        <label><input type="checkbox" id="density-color" checked> Color by density ρ(M)</label>
                    </div>
                    <div class="control-group">
                        <label><input type="checkbox" id="radial-lines"> Show radial structure</label>
                    </div>
                    <button onclick="draw()">Regenerate</button>
                    <button onclick="exportImg()">Export PNG</button>
                    <button onclick="analyze()">Deep Analysis</button>
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
                <h2>Limitations and Missing Elements</h2>
                
                <h3>What's Missing: The Critical Gaps</h3>
                <div class="warning-box">
                    <strong>1. No Connection to Actual Zeta Function</strong>
                    <p>This visualization doesn't compute ζ(s) at all! It only:</p>
                    <ul>
                        <li>Generates modular points artificially</li>
                        <li>Maps them through Cayley transform</li>
                        <li>Shows known zeros from pre-existing tables</li>
                    </ul>
                    <p><strong>Missing:</strong> Computing ζ(1/2 + it) to see where it actually approaches zero</p>
                </div>

                <div class="warning-box">
                    <strong>2. No Functional Equation Integration</strong>
                    <p>The functional equation ζ(s) = 2^s π^(s-1) sin(πs/2) Γ(1-s) ζ(1-s) is completely ignored.</p>
                    <p><strong>Missing:</strong> Showing how this symmetry constrains zero locations</p>
                </div>

                <div class="warning-box">
                    <strong>3. No Prime Number Connection</strong>
                    <p>RH is important because of the Euler product: ζ(s) = ∏(1 - p^(-s))^(-1)</p>
                    <p><strong>Missing:</strong> Visualization of how prime distribution relates to zeros</p>
                </div>

                <div class="warning-box">
                    <strong>4. No Rigorous Distance Metrics</strong>
                    <p><strong>Missing:</strong> Quantitative analysis of:</p>
                    <ul>
                        <li>How close modular points come to actual zeros</li>
                        <li>Statistical significance of apparent clustering</li>
                        <li>Comparison with random point distributions</li>
                        <li>Error bounds and confidence intervals</li>
                    </ul>
                </div>

                <h3>Maximum Modulus: Computational Limits</h3>
                <table>
                    <tr>
                        <th>M_max</th>
                        <th>Total Points</th>
                        <th>Coprime Points</th>
                        <th>Render Time</th>
                        <th>Practical?</th>
                    </tr>
                    <tr>
                        <td>500</td>
                        <td>~125,000</td>
                        <td>~78,000</td>
                        <td>&lt;1s</td>
                        <td>✓ Excellent</td>
                    </tr>
                    <tr>
                        <td>1,000</td>
                        <td>~500,000</td>
                        <td>~304,000</td>
                        <td>2-3s</td>
                        <td>✓ Good</td>
                    </tr>
                    <tr>
                        <td>2,000</td>
                        <td>~2,000,000</td>
                        <td>~1,216,000</td>
                        <td>8-12s</td>
                        <td>⚠ Slow</td>
                    </tr>
                    <tr>
                        <td>5,000</td>
                        <td>~12,500,000</td>
                        <td>~7,600,000</td>
                        <td>45-60s</td>
                        <td>✗ Too slow</td>
                    </tr>
                </table>

                <p><strong>Recommended maximum:</strong> M = 1000-2000 for browser-based rendering. Beyond this:</p>
                <ul>
                    <li>Canvas becomes oversaturated</li>
                    <li>Individual points become indistinguishable</li>
                    <li>No new mathematical insight gained</li>
                    <li>Better to use heatmap/density visualization</li>
                </ul>

                <h3>Why Higher M Doesn't Help</h3>
                <p>Increasing M reveals:</p>
                <ul>
                    <li><strong>Denser rings</strong> at predictable radii (by construction)</li>
                    <li><strong>Finer angular structure</strong> (from more residue classes)</li>
                    <li><strong>Same fundamental patterns</strong> (no new phenomena)</li>
                </ul>
                
                <div class="info-box">
                    <p><strong>Asymptotic density:</strong> As M → ∞, the proportion of coprime points approaches 6/π² ≈ 0.608. This is a known result (sum of 1/n² over coprimes), not a discovery about zeta zeros.</p>
                </div>

                <h3>Why RH Is Actually Hard</h3>
                <p>The Riemann Hypothesis resists this approach because:</p>
                <ul>
                    <li>The <strong>analytic continuation</strong> of ζ(s) is subtle</li>
                    <li>The <strong>functional equation</strong> creates complex constraints not visible here</li>
                    <li>The <strong>distribution of primes</strong> connects to zeros in non-obvious ways</li>
                    <li>Modular arithmetic has no known direct connection to zero locations</li>
                </ul>

                <h3>What Would Make This Meaningful?</h3>
                <ol>
                    <li><strong>Compute actual ζ(1/2 + it)</strong> values and plot |ζ| approaching zero</li>
                    <li><strong>Implement functional equation</strong> to show symmetry constraints</li>
                    <li><strong>Statistical hypothesis testing</strong>: Do modular points cluster near zeros more than random points?</li>
                    <li><strong>Connect to prime distribution</strong> via explicit formulas</li>
                    <li><strong>Theoretical justification</strong> for why modular arithmetic would matter</li>
                </ol>
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
            const scaleFactor = parseFloat(document.getElementById('scale-slider').value);
            const pointSize = parseInt(document.getElementById('size-slider').value);
            const opacity = parseFloat(document.getElementById('opacity-slider').value);
            const coprimeOnly = document.getElementById('coprime').checked;
            const showZeros = document.getElementById('zeros').checked;
            const densityColor = document.getElementById('density-color').checked;
            const radialLines = document.getElementById('radial-lines').checked;

            document.getElementById('m-val').textContent = M;
            document.getElementById('scale-val').textContent = scaleFactor;
            document.getElementById('size-val').textContent = pointSize;
            document.getElementById('opacity-val').textContent = opacity;

            let points = 0, coprimeCount = 0;
            let minDist = Infinity, maxDist = 0;
            const distToZeros = [];

            const startTime = performance.now();

            // Radial structure
            if(radialLines) {
                ctx.strokeStyle = 'rgba(52,152,219,0.2)';
                ctx.lineWidth = 0.5;
                for(let angle=0; angle<Math.PI*2; angle+=Math.PI/12) {
                    ctx.beginPath();
                    ctx.moveTo(cx, cy);
                    ctx.lineTo(cx + Math.cos(angle)*scale, cy + Math.sin(angle)*scale);
                    ctx.stroke();
                }
            }

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
                    
                    const radius = Math.sqrt(g.x*g.x + g.y*g.y);
                    const distFromCircle = Math.abs(radius - 1);
                    minDist = Math.min(minDist, distFromCircle);
                    maxDist = Math.max(maxDist, distFromCircle);
                    
                    const px = cx + g.x * scale;
                    const py = cy - g.y * scale;
                    
                    let color;
                    if(densityColor) {
                        const hue = 60 + density*180;
                        color = `hsla(${hue},70%,55%,${opacity})`;
                    } else {
                        const hue = (m/M)*360;
                        color = `hsla(${hue},70%,55%,${opacity})`;
                    }
                    
                    ctx.fillStyle = color;
                    ctx.fillRect(px-pointSize/2, py-pointSize/2, pointSize, pointSize);
                    
                    // Track distance to nearest zero
                    if(showZeros) {
                        let minZeroDist = Infinity;
                        zeros.forEach(zt => {
                            const gz = cayley(0.5, zt);
                            const d = Math.sqrt((g.x-gz.x)**2 + (g.y-gz.y)**2);
                            minZeroDist = Math.min(minZeroDist, d);
                        });
                        distToZeros.push(minZeroDist);
                    }
                }
            }

            const renderTime = ((performance.now() - startTime)/1000).toFixed(2);

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

            const avgDist = distToZeros.length > 0 ? 
                (distToZeros.reduce((a,b)=>a+b,0)/distToZeros.length).toFixed(4) : 'N/A';

            document.getElementById('stats-text').innerHTML = `
                <strong>Computational Performance:</strong><br>
                • Modulus range: 1 to ${M}<br>
                • Render time: ${renderTime}s<br>
                • Total points: ${points.toLocaleString()}<br>
                • Coprime points: ${coprimeCount.toLocaleString()} (${(100*coprimeCount/points).toFixed(1)}%)<br>
                • Asymptotic coprime density: ${(coprimeCount/points).toFixed(4)} (expect ~0.608 as M→∞)<br><br>
                
                <strong>Distance Analysis:</strong><br>
                • Min distance from |Γ|=1: ${minDist.toFixed(6)}<br>
                • Max distance from |Γ|=1: ${maxDist.toFixed(6)}<br>
                • Avg distance to nearest zero: ${avgDist}<br><br>
                
                <strong>Parameters:</strong><br>
                • Scaling factor: ${scaleFactor} (arbitrary, affects t-values)<br>
                • Point size: ${pointSize}px<br>
                • Opacity: ${opacity}<br>
                • Known zeros: ${showZeros ? '20 shown' : 'Hidden'}
            `;
        }

        function analyze() {
            const M = parseInt(document.getElementById('m-slider').value);
            let analysis = '<h3>Deep Analysis</h3>';
            
            // Density histogram
            const densities = {};
            for(let m=1; m<=Math.min(M, 100); m++) {
                const d = (phi(m)/m).toFixed(2);
                densities[d] = (densities[d] || 0) + 1;
            }
            
            analysis += '<strong>Density Distribution (first 100 moduli):</strong><br>';
            Object.keys(densities).sort().forEach(d => {
                analysis += `ρ=${d}: ${densities[d]} moduli<br>`;
            });
            
            analysis += '<br><strong>Prime Moduli Analysis:</strong><br>';
            const primes = [2,3,5,7,11,13,17,19,23,29,31,37,41,43,47];
            primes.forEach(p => {
                if(p <= M) {
                    analysis += `M=${p} (prime): ρ=${(phi(p)/p).toFixed(4)} = ${((p-1)/p).toFixed(4)}<br>`;
                }
            });
            
            analysis += '<br><strong>Highly Composite Examples:</strong><br>';
            [12,24,30,60,120].forEach(n => {
                if(n <= M) {
                    analysis += `M=${n}: ρ=${(phi(n)/n).toFixed(4)}<br>`;
                }
            });
            
            analysis += '<br><em>Note: Higher ρ means more coprime residues, creating denser rings in visualization.</em>';
            
            document.getElementById('stats-text').innerHTML = analysis;
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

        document.getElementById('size-slider').addEventListener('input', () => {
            document.getElementById('size-val').textContent = document.getElementById('size-slider').value;
        });

        document.getElementById('opacity-slider').addEventListener('input', () => {
            document.getElementById('opacity-val').textContent = document.getElementById('opacity-slider').value;
        });

        draw();
    </script>
</body>
</html>
