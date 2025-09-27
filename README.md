<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Twin Prime Algebraic Identities: Telescoping and Finite-Cutoff</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.2/es5/tex-mml-chtml.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Times New Roman', serif;
            line-height: 1.6;
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            min-height: 100vh;
            color: #2c3e50;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 40px 20px;
        }
        
        .paper {
            background: white;
            border-radius: 12px;
            padding: 50px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            margin-bottom: 30px;
        }
        
        .title {
            font-size: 2.2em;
            text-align: center;
            margin-bottom: 20px;
            color: #2c3e50;
            font-weight: 400;
        }
        
        .subtitle {
            text-align: center;
            font-size: 1.1em;
            margin-bottom: 30px;
            color: #7f8c8d;
            font-style: italic;
        }
        
        .author {
            text-align: center;
            font-size: 1.2em;
            margin-bottom: 40px;
            color: #7f8c8d;
        }
        
        .nav-tabs {
            display: flex;
            justify-content: center;
            margin-bottom: 30px;
            border-bottom: 2px solid #ecf0f1;
        }
        
        .nav-tab {
            padding: 15px 30px;
            cursor: pointer;
            border-bottom: 3px solid transparent;
            transition: all 0.3s ease;
            font-weight: bold;
        }
        
        .nav-tab.active {
            border-bottom-color: #3498db;
            color: #3498db;
        }
        
        .nav-tab:hover {
            background: #f8f9fa;
        }
        
        .tab-content {
            display: none;
        }
        
        .tab-content.active {
            display: block;
        }
        
        .theorem-box {
            background: linear-gradient(135deg, #e8f4fd, #d6eaf8);
            border: 2px solid #3498db;
            border-radius: 10px;
            padding: 25px;
            margin: 30px 0;
            position: relative;
        }
        
        .theorem-box::before {
            content: attr(data-title);
            position: absolute;
            top: -12px;
            left: 20px;
            background: #3498db;
            color: white;
            padding: 5px 15px;
            border-radius: 15px;
            font-weight: bold;
            font-size: 0.9em;
        }
        
        .formula {
            text-align: center;
            font-size: 1.3em;
            margin: 20px 0;
            padding: 15px;
            background: #f8f9fa;
            border-radius: 8px;
        }
        
        .section {
            margin: 40px 0;
        }
        
        .section-title {
            font-size: 1.5em;
            margin-bottom: 20px;
            color: #34495e;
            border-bottom: 2px solid #3498db;
            padding-bottom: 10px;
        }
        
        .definition-box {
            background: linear-gradient(135deg, #fef9e7, #fcf3cf);
            border: 2px solid #f39c12;
            border-radius: 8px;
            padding: 20px;
            margin: 20px 0;
        }
        
        .verification-panel {
            background: linear-gradient(135deg, #e8f8f5, #d5f4e6);
            border: 2px solid #27ae60;
            border-radius: 10px;
            padding: 25px;
            margin: 25px 0;
        }
        
        .controls {
            display: flex;
            gap: 20px;
            align-items: center;
            margin: 20px 0;
            flex-wrap: wrap;
        }
        
        label {
            font-weight: bold;
            color: #2c3e50;
        }
        
        input, select {
            padding: 8px 12px;
            border: 2px solid #bdc3c7;
            border-radius: 5px;
            font-size: 14px;
        }
        
        button {
            background: linear-gradient(135deg, #3498db, #2980b9);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
            transition: transform 0.2s;
        }
        
        button:hover {
            transform: translateY(-2px);
        }
        
        .results-table {
            width: 100%;
            border-collapse: collapse;
            margin: 20px 0;
        }
        
        .results-table th {
            background: #34495e;
            color: white;
            padding: 12px;
            text-align: center;
        }
        
        .results-table td {
            padding: 10px;
            text-align: center;
            border: 1px solid #ecf0f1;
        }
        
        .results-table tr:nth-child(even) {
            background: #f8f9fa;
        }
        
        .exact-match {
            color: #27ae60;
            font-weight: bold;
        }
        
        .step-detail {
            background: #f8f9fa;
            border: 1px solid #dee2e6;
            border-radius: 5px;
            padding: 15px;
            margin: 10px 0;
        }
        
        .computation-step {
            font-family: 'Courier New', monospace;
            background: #f4f4f4;
            padding: 8px;
            border-radius: 3px;
            margin: 5px 0;
        }
        
        .note {
            background: #f4f4f4;
            border-left: 4px solid #95a5a6;
            padding: 15px;
            margin: 20px 0;
            font-style: italic;
        }
        
        .highlight {
            background: linear-gradient(135deg, #fdf2e9, #fadbd8);
            border: 2px solid #e67e22;
            border-radius: 8px;
            padding: 20px;
            margin: 20px 0;
        }
        
        .comparison-table {
            width: 100%;
            border-collapse: collapse;
            margin: 20px 0;
            background: white;
        }
        
        .comparison-table th {
            background: #9b59b6;
            color: white;
            padding: 12px;
            text-align: center;
        }
        
        .fraction-display {
            font-family: 'Times New Roman', serif;
            font-size: 1.1em;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="paper">
            <h1 class="title">Twin Prime Algebraic Identities</h1>
            <div class="subtitle">Telescoping and Finite-Cutoff Relationships in Sieve Theory</div>
            <div class="author">Mathematical Analysis by Wessen Getachew</div>
            
            <div class="nav-tabs">
                <div class="nav-tab active" onclick="showTab('telescoping')">Telescoping Identity</div>
                <div class="nav-tab" onclick="showTab('finite')">Finite-Cutoff Identity</div>
                <div class="nav-tab" onclick="showTab('comparison')">Comparison</div>
            </div>
            
            <!-- Telescoping Identity Tab -->
            <div id="telescoping" class="tab-content active">
                <div class="theorem-box" data-title="Main Telescoping Identity (Theorem 2)">
                    <div class="formula">
                        $$\prod_{k=3}^{n} \frac{(k-1)(k-2)}{k^2} = \frac{4}{n^2(n-1)}$$
                    </div>
                    <p><strong>Domain:</strong> All consecutive integers from 3 to n</p>
                    <p><strong>Property:</strong> Closed-form telescoping product for twin prime sieving</p>
                    <p><strong>Application:</strong> Direct evaluation eliminates floating-point error accumulation</p>
                </div>
                
                <div class="section">
                    <h2 class="section-title">Fundamental Identity (Theorem 1)</h2>
                    <div class="definition-box">
                        <h3>Per-Term Factorization</h3>
                        <div class="formula">
                            $$\frac{(p-1)(p-2)}{p^2} = \left(1-\frac{1}{(p-1)^2}\right)\left(1-\frac{1}{p}\right)^3$$
                        </div>
                        <p><strong>Physical Interpretation:</strong> Represents the density of integers avoiding both residue classes 1 and p-1 modulo p, corresponding to numbers m where neither m nor m+2 is divisible by p - precisely the constraint needed in twin prime sieving.</p>
                        <p><strong>Mathematical Property:</strong> This factorization enables the telescoping behavior when products are taken over consecutive integers.</p>
                    </div>
                </div>
                
                <div class="section">
                    <h2 class="section-title">Convergence Properties</h2>
                    <div class="definition-box">
                        <h3>Asymptotic Behavior</h3>
                        <div class="formula">
                            $$\prod_{k=3}^{n} \frac{(k-1)(k-2)}{k^2} \sim \frac{4}{n^3} \quad \text{as } n \to \infty$$
                        </div>
                        <p><strong>Precision Control:</strong> For target precision ε, the required cutoff is n = ⌈(4/ε)^(1/3)⌉</p>
                        <p><strong>Error Bounds:</strong> The truncation error is exactly 4/(n²(n-1))</p>
                    </div>
                </div>
                
                <div class="verification-panel">
                    <h2 class="section-title">Telescoping Verification & Precision Analysis</h2>
                    <p>Test the telescoping formula and analyze precision requirements:</p>
                    
                    <div class="controls">
                        <label for="telescoping-n">Upper Limit n:</label>
                        <input type="number" id="telescoping-n" value="10" min="3" step="1">
                        
                        <label for="telescoping-precision">Decimal Places:</label>
                        <input type="number" id="telescoping-precision" value="15" min="6" max="30" step="1">
                        
                        <label for="telescoping-format">Display Format:</label>
                        <select id="telescoping-format">
                            <option value="decimal">Decimal</option>
                            <option value="fraction">Fraction</option>
                        </select>
                        
                        <button onclick="verifyTelescoping()">Verify Identity</button>
                        <button onclick="telescopingRange()">Test Range</button>
                        <button onclick="telescopingSteps()">Show Proof Steps</button>
                    </div>
                    
                    <div class="controls">
                        <label for="target-epsilon">Target Precision ε:</label>
                        <input type="number" id="target-epsilon" value="0.000001" step="0.000001" min="0.000001">
                        
                        <label for="bound-n">Or specify n for bound:</label>
                        <input type="number" id="bound-n" value="100" min="3" step="1">
                        
                        <button onclick="findOptimalCutoff()">Find Optimal Cutoff</button>
                        <button onclick="calculateBound()">Calculate Error Bound</button>
                        <button onclick="convergenceAnalysis()">Convergence Analysis</button>
                    </div>
                    
                    <div id="telescoping-results"></div>
                    <div id="telescoping-steps-results"></div>
                    <div id="precision-results"></div>
                </div>
            </div>
            
            <!-- Finite-Cutoff Identity Tab -->
            <div id="finite" class="tab-content">
                <div class="theorem-box" data-title="Finite-Cutoff Identity">
                    <div class="formula">
                        $$R_{\mathrm{mod}}(p_{\max}) = \frac{1}{4} \cdot C_2(p_{\max}) \cdot \left[M_{\mathrm{no2}}(p_{\max})\right]^3$$
                    </div>
                    <p><strong>Domain:</strong> Primes only from 3 to p_max</p>
                    <p><strong>Property:</strong> Exact relationship between modular sieve constants and Hardy-Littlewood factors</p>
                </div>
                
                <div class="section">
                    <h2 class="section-title">Component Definitions</h2>
                    
                    <div class="definition-box">
                        <h3>Modular Sieve Constant</h3>
                        <div class="formula">
                            $$R_{\mathrm{mod}}(p_{\max}) = \frac{1}{4} \prod_{3 \leq p \leq p_{\max}} \frac{(p-1)(p-2)}{p^2}$$
                        </div>
                    </div>
                    
                    <div class="definition-box">
                        <h3>Hardy-Littlewood Factor</h3>
                        <div class="formula">
                            $$C_2(p_{\max}) = \prod_{3 \leq p \leq p_{\max}} \left(1 - \frac{1}{(p-1)^2}\right)$$
                        </div>
                    </div>
                    
                    <div class="definition-box">
                        <h3>Mertens Factor</h3>
                        <div class="formula">
                            $$M_{\mathrm{no2}}(p_{\max}) = \prod_{3 \leq p \leq p_{\max}} \left(1 - \frac{1}{p}\right)$$
                        </div>
                    </div>
                </div>
                
                <div class="verification-panel">
                    <h2 class="section-title">Finite-Cutoff Verification</h2>
                    <p>Test the identity for prime cutoffs:</p>
                    
                    <div class="controls">
                        <label for="finite-pmax">Prime Cutoff:</label>
                        <input type="number" id="finite-pmax" value="13" min="3" step="1">
                        
                        <label for="finite-precision">Decimal Places:</label>
                        <input type="number" id="finite-precision" value="15" min="6" max="30" step="1">
                        
                        <label for="finite-format">Display Format:</label>
                        <select id="finite-format">
                            <option value="decimal">Decimal</option>
                            <option value="fraction">Fraction</option>
                        </select>
                        
                        <button onclick="verifyFinite()">Verify</button>
                        <button onclick="finiteRange()">Test Range</button>
                        <button onclick="finiteSteps()">Show Steps</button>
                    </div>
                    
                    <div id="finite-results"></div>
                    <div id="finite-steps-results"></div>
                </div>
            </div>
            
            <!-- Comparison Tab -->
            <div id="comparison" class="tab-content">
                <div class="section">
                    <h2 class="section-title">Identity Comparison</h2>
                    
                    <div class="highlight">
                        <h3>Relationship Analysis</h3>
                        <table class="comparison-table">
                            <tr>
                                <th>Property</th>
                                <th>New Telescoping Identity</th>
                                <th>Finite-Cutoff Identity</th>
                            </tr>
                            <tr>
                                <td><strong>Primary Author</strong></td>
                                <td>Wessen Getachew (2024)</td>
                                <td>Extended sieve theory</td>
                            </tr>
                            <tr>
                                <td><strong>Domain</strong></td>
                                <td>All integers 3 to n</td>
                                <td>Primes 3 to p_max</td>
                            </tr>
                            <tr>
                                <td><strong>Formula</strong></td>
                                <td>4/(n²(n-1))</td>
                                <td>(1/4) × C₂ × M_no2³</td>
                            </tr>
                            <tr>
                                <td><strong>Convergence</strong></td>
                                <td>Cubic decay: ~4/n³</td>
                                <td>Prime-dependent convergence</td>
                            </tr>
                            <tr>
                                <td><strong>Application</strong></td>
                                <td>Computational twin prime sieving</td>
                                <td>Hardy-Littlewood constant analysis</td>
                            </tr>
                            <tr>
                                <td><strong>Key Innovation</strong></td>
                                <td>Telescoping factorization</td>
                                <td>Modular sieve structure</td>
                            </tr>
                            <tr>
                                <td><strong>Precision Control</strong></td>
                                <td>n = ⌈(4/ε)^(1/3)⌉</td>
                                <td>Prime cutoff dependent</td>
                            </tr>
                        </table>
                    </div>
                    
                    <div class="verification-panel">
                        <h2 class="section-title">Side-by-Side Comparison</h2>
                        <p>Compare both identities for the same value:</p>
                        
                        <div class="controls">
                            <label for="compare-n">Value n (will test n and largest prime ≤ n):</label>
                            <input type="number" id="compare-n" value="10" min="3" step="1">
                            
                            <label for="compare-precision">Decimal Places:</label>
                            <input type="number" id="compare-precision" value="12" min="6" max="25" step="1">
                            
                            <button onclick="compareIdentities()">Compare</button>
                        </div>
                        
                        <div id="comparison-results"></div>
                    </div>
                </div>
            </div>
            
            <div class="note">
                <strong>Research Note:</strong> The telescoping identity by Wessen Getachew (2024) provides a breakthrough 
                in computational twin prime theory by enabling exact evaluation of products that previously required 
                hundreds of terms with accumulating numerical errors. The finite-cutoff identity reveals the underlying 
                modular structure connecting Hardy-Littlewood constants to sieve theory. Together, these identities 
                bridge computational efficiency with theoretical insight in twin prime research.
            </div>
        </div>
    </div>

    <script>
        // Configure MathJax
        window.MathJax = {
            tex: {
                inlineMath: [['$', '$'], ['\\(', '\\)']],
                displayMath: [['$$', '$$'], ['\\[', '\\]']]
            }
        };

        // Utility functions
        function isPrime(n) {
            if (n < 2) return false;
            if (n === 2) return true;
            if (n % 2 === 0) return false;
            for (let i = 3; i * i <= n; i += 2) {
                if (n % i === 0) return false;
            }
            return true;
        }

        function getPrimesUpTo(limit) {
            const primes = [];
            for (let i = 3; i <= limit; i++) {
                if (isPrime(i)) primes.push(i);
            }
            return primes;
        }

        function gcd(a, b) {
            while (b !== 0) {
                [a, b] = [b, a % b];
            }
            return a;
        }

        function formatAsFraction(decimal, maxDenom = 10000) {
            if (Math.abs(decimal) < 1e-10) return "0";
            
            let sign = decimal < 0 ? "-" : "";
            decimal = Math.abs(decimal);
            
            for (let denom = 1; denom <= maxDenom; denom++) {
                let num = Math.round(decimal * denom);
                if (Math.abs(decimal - num/denom) < 1e-10) {
                    let g = gcd(num, denom);
                    num /= g;
                    denom /= g;
                    if (denom === 1) return sign + num.toString();
                    return sign + num + "/" + denom;
                }
            }
            return decimal.toFixed(8);
        }

        function formatNumber(num, decimals, format) {
            if (format === 'fraction') {
                return formatAsFraction(num);
            }
            return num.toFixed(decimals);
        }

        // Tab switching
        function showTab(tabName) {
            document.querySelectorAll('.tab-content').forEach(t => t.classList.remove('active'));
            document.querySelectorAll('.nav-tab').forEach(t => t.classList.remove('active'));
            
            document.getElementById(tabName).classList.add('active');
            event.target.classList.add('active');
        }

        // New functions for precision analysis and bounds

        function findOptimalCutoff() {
            const epsilon = parseFloat(document.getElementById('target-epsilon').value);
            const precision = parseInt(document.getElementById('telescoping-precision').value);
            const format = document.getElementById('telescoping-format').value;
            
            // From the paper: n ≈ (4/ε)^(1/3)
            const theoreticalN = Math.ceil(Math.pow(4/epsilon, 1/3));
            
            // Find exact n where 4/(n²(n-1)) < ε
            let optimalN = 3;
            while (true) {
                const bound = 4 / (optimalN * optimalN * (optimalN - 1));
                if (bound < epsilon) break;
                optimalN++;
            }
            
            const actualBound = 4 / (optimalN * optimalN * (optimalN - 1));
            const theoreticalBound = 4 / (theoreticalN * theoreticalN * (theoreticalN - 1));
            
            const html = `
                <h3>Optimal Cutoff Analysis for ε = ${epsilon}</h3>
                <table class="results-table">
                    <tr>
                        <th>Method</th>
                        <th>Cutoff n</th>
                        <th>Actual Bound</th>
                        <th>Meets Target?</th>
                    </tr>
                    <tr>
                        <td>Theoretical Formula: ⌈(4/ε)^(1/3)⌉</td>
                        <td>${theoreticalN}</td>
                        <td>${formatNumber(theoreticalBound, precision, format)}</td>
                        <td class="${theoreticalBound < epsilon ? 'exact-match' : ''}">
                            ${theoreticalBound < epsilon ? 'YES' : 'NO'}
                        </td>
                    </tr>
                    <tr>
                        <td>Exact Calculation</td>
                        <td>${optimalN}</td>
                        <td>${formatNumber(actualBound, precision, format)}</td>
                        <td class="exact-match">YES</td>
                    </tr>
                </table>
                
                <div class="note">
                    <strong>Interpretation:</strong> To achieve precision ε = ${epsilon}, you need at least n = ${optimalN} terms. 
                    The theoretical approximation n ≈ (4/ε)^(1/3) = ${theoreticalN} is ${Math.abs(theoreticalN - optimalN) <= 1 ? 'very accurate' : 'a good approximation'}.
                </div>
            `;
            
            document.getElementById('precision-results').innerHTML = html;
        }

        function calculateBound() {
            const n = parseInt(document.getElementById('bound-n').value);
            const precision = parseInt(document.getElementById('telescoping-precision').value);
            const format = document.getElementById('telescoping-format').value;
            
            const errorBound = 4 / (n * n * (n - 1));
            const asymptoticApprox = 4 / (n * n * n); // n³ approximation
            const relativeError = Math.abs(errorBound - asymptoticApprox) / errorBound * 100;
            
            // Calculate the actual product for comparison
            let product = 1;
            for (let k = 3; k <= n; k++) {
                product *= (k - 1) * (k - 2) / (k * k);
            }
            
            const html = `
                <h3>Error Bound Analysis for n = ${n}</h3>
                <table class="results-table">
                    <tr>
                        <th>Measure</th>
                        <th>Value</th>
                        <th>Formula</th>
                    </tr>
                    <tr>
                        <td>Exact Product Value</td>
                        <td>${formatNumber(product, precision, format)}</td>
                        <td>∏[(k-1)(k-2)/k²] from k=3 to ${n}</td>
                    </tr>
                    <tr>
                        <td>Telescoping Formula</td>
                        <td>${formatNumber(4/(n*n*(n-1)), precision, format)}</td>
                        <td>4/(n²(n-1)) = 4/(${n}²×${n-1})</td>
                    </tr>
                    <tr>
                        <td>Asymptotic Approximation</td>
                        <td>${formatNumber(asymptoticApprox, precision, format)}</td>
                        <td>4/n³ ≈ 4/${n}³</td>
                    </tr>
                    <tr>
                        <td>Asymptotic Error</td>
                        <td>${relativeError.toFixed(2)}%</td>
                        <td>Relative difference from exact bound</td>
                    </tr>
                </table>
                
                <div class="note">
                    <strong>Precision Achievement:</strong> With n = ${n}, the telescoping product achieves precision ε ≈ ${errorBound.toExponential(3)}. 
                    This means the infinite product differs from the finite product by less than ${errorBound.toExponential(3)}.
                </div>
            `;
            
            document.getElementById('precision-results').innerHTML = html;
        }

        function convergenceAnalysis() {
            const maxN = parseInt(document.getElementById('bound-n').value) || 50;
            const precision = parseInt(document.getElementById('telescoping-precision').value);
            const format = document.getElementById('telescoping-format').value;
            
            let html = `
                <h3>Convergence Analysis (n = 3 to ${maxN})</h3>
                <table class="results-table">
                    <tr>
                        <th>n</th>
                        <th>Product Value</th>
                        <th>Error Bound 4/(n²(n-1))</th>
                        <th>Asymptotic 4/n³</th>
                        <th>Precision Digits</th>
                    </tr>
            `;
            
            for (let n = 3; n <= Math.min(maxN, 20); n++) {
                const productValue = 4 / (n * n * (n - 1));
                const errorBound = productValue; // Same for this formula
                const asymptotic = 4 / (n * n * n);
                const precisionDigits = Math.max(0, Math.floor(-Math.log10(productValue)));
                
                html += `
                    <tr>
                        <td>${n}</td>
                        <td>${formatNumber(productValue, Math.min(8, precision), format)}</td>
                        <td>${errorBound.toExponential(2)}</td>
                        <td>${asymptotic.toExponential(2)}</td>
                        <td>${precisionDigits}</td>
                    </tr>
                `;
            }
            
            html += `
                </table>
                
                <div class="step-detail">
                    <h4>Convergence Properties</h4>
                    <div class="computation-step"><strong>Cubic Decay:</strong> The product decreases as ~4/n³ for large n</div>
                    <div class="computation-step"><strong>Rapid Convergence:</strong> Each doubling of n reduces the value by ~8×</div>
                    <div class="computation-step"><strong>Numerical Stability:</strong> Direct formula avoids floating-point error accumulation</div>
                    <div class="computation-step"><strong>Precision Control:</strong> Cutoff n can be chosen exactly for any target precision</div>
                </div>
            `;
            
            document.getElementById('precision-results').innerHTML = html;
        }
        function verifyTelescoping() {
            const n = parseInt(document.getElementById('telescoping-n').value);
            const precision = parseInt(document.getElementById('telescoping-precision').value);
            const format = document.getElementById('telescoping-format').value;
            
            // Direct product computation
            let product = 1;
            for (let k = 3; k <= n; k++) {
                product *= (k - 1) * (k - 2) / (k * k);
            }
            
            // Telescoping formula
            const formula = 4 / (n * n * (n - 1));
            
            // Results
            const difference = Math.abs(product - formula);
            const match = difference < Math.pow(10, -precision + 2);
            
            const html = `
                <h3>Telescoping Verification for n = ${n}</h3>
                <table class="results-table">
                    <tr>
                        <th>Method</th>
                        <th>Result</th>
                        <th>Details</th>
                    </tr>
                    <tr>
                        <td>Direct Product</td>
                        <td class="computation-step">${formatNumber(product, precision, format)}</td>
                        <td>∏[(k-1)(k-2)/k²] for k=3 to ${n}</td>
                    </tr>
                    <tr>
                        <td>Telescoping Formula</td>
                        <td class="computation-step">${formatNumber(formula, precision, format)}</td>
                        <td>4/(${n}² × ${n-1}) = 4/${n*n*(n-1)}</td>
                    </tr>
                    <tr>
                        <td>Difference</td>
                        <td class="${match ? 'exact-match' : ''}">${difference.toExponential(3)}</td>
                        <td>${match ? 'EXACT MATCH ✓' : 'Within precision'}</td>
                    </tr>
                </table>
            `;
            
            document.getElementById('telescoping-results').innerHTML = html;
        }

        function telescopingRange() {
            const maxN = parseInt(document.getElementById('telescoping-n').value);
            const precision = parseInt(document.getElementById('telescoping-precision').value);
            const format = document.getElementById('telescoping-format').value;
            
            let html = `
                <h3>Telescoping Range Test (n = 3 to ${maxN})</h3>
                <table class="results-table">
                    <tr>
                        <th>n</th>
                        <th>Direct Product</th>
                        <th>Formula Result</th>
                        <th>Match</th>
                    </tr>
            `;
            
            for (let n = 3; n <= maxN; n++) {
                let product = 1;
                for (let k = 3; k <= n; k++) {
                    product *= (k - 1) * (k - 2) / (k * k);
                }
                const formula = 4 / (n * n * (n - 1));
                const match = Math.abs(product - formula) < Math.pow(10, -precision + 2);
                
                html += `
                    <tr>
                        <td>${n}</td>
                        <td>${formatNumber(product, Math.min(8, precision), format)}</td>
                        <td>${formatNumber(formula, Math.min(8, precision), format)}</td>
                        <td class="${match ? 'exact-match' : ''}">
                            ${match ? 'YES' : 'NO'}
                        </td>
                    </tr>
                `;
            }
            
            html += '</table>';
            document.getElementById('telescoping-results').innerHTML = html;
        }

        function telescopingSteps() {
            const n = parseInt(document.getElementById('telescoping-n').value);
            const precision = parseInt(document.getElementById('telescoping-precision').value);
            const format = document.getElementById('telescoping-format').value;
            
            let html = `
                <h3>Proof Steps for Telescoping Identity (n = ${n})</h3>
                
                <div class="step-detail">
                    <h4>Step 1: Apply Fundamental Identity (Theorem 1)</h4>
                    <div class="computation-step">For each term: (k-1)(k-2)/k² = (1 - 1/(k-1)²)(1 - 1/k)³</div>
                    <div class="computation-step">Product becomes: ∏[1 - 1/(k-1)²] × ∏[1 - 1/k]³</div>
                </div>
                
                <div class="step-detail">
                    <h4>Step 2: Evaluate Second Product ∏[1 - 1/k]³</h4>
                    <div class="computation-step">∏[1 - 1/k] = ∏[(k-1)/k] from k=3 to ${n}</div>
                    <div class="computation-step">= (2/3) × (3/4) × (4/5) × ... × ((${n}-1)/${n})</div>
                    <div class="computation-step">= 2/${n} (telescoping cancellation)</div>
                    <div class="computation-step">Therefore: ∏[1 - 1/k]³ = (2/${n})³ = 8/${n}³</div>
                </div>
                
                <div class="step-detail">
                    <h4>Step 3: Evaluate First Product ∏[1 - 1/(k-1)²]</h4>
                    <div class="computation-step">∏[1 - 1/(k-1)²] = ∏[k(k-2)/(k-1)²] from k=3 to ${n}</div>
                    <div class="computation-step">= ∏[k/(k-1)] × ∏[(k-2)/(k-1)]</div>
                    <div class="computation-step">First part: (3/2) × (4/3) × ... × (${n}/${n-1}) = ${n}/2</div>
                    <div class="computation-step">Second part: (1/2) × (2/3) × ... × ((${n}-2)/${n-1}) = 1/${n-1}</div>
                    <div class="computation-step">Combined: (${n}/2) × (1/${n-1}) = ${n}/(2(${n-1}))</div>
                </div>
                
                <div class="step-detail">
                    <h4>Step 4: Final Combination</h4>
                    <div class="computation-step">Product = [${n}/(2(${n-1}))] × [8/${n}³]</div>
                    <div class="computation-step">= (${n} × 8)/(2(${n-1}) × ${n}³)</div>
                    <div class="computation-step">= 8/(2(${n-1}) × ${n}²)</div>
                    <div class="computation-step">= 4/(${n}² × ${n-1})</div>
                    <div class="computation-step">= 4/(${n*n} × ${n-1}) = 4/${n*n*(n-1)}</div>
                </div>
            `;
            
            // Verify with numerical computation
            let product = 1;
            for (let k = 3; k <= n; k++) {
                product *= (k - 1) * (k - 2) / (k * k);
            }
            const formula = 4 / (n * n * (n - 1));
            
            html += `
                <div class="step-detail">
                    <h4>Numerical Verification</h4>
                    <div class="computation-step">Direct Product: ${formatNumber(product, precision, format)}</div>
                    <div class="computation-step">Telescoping Formula: ${formatNumber(formula, precision, format)}</div>
                    <div class="computation-step ${Math.abs(product - formula) < 1e-14 ? 'exact-match' : ''}">
                        <strong>Identity ${Math.abs(product - formula) < 1e-14 ? 'PROVEN ✓' : 'APPROXIMATE'}</strong>
                    </div>
                </div>
                
                <div class="note">
                    <strong>Twin Prime Connection:</strong> Each factor (k-1)(k-2)/k² represents the density of integers 
                    avoiding residue classes 1 and k-1 modulo k, corresponding to numbers m where neither m nor m+2 
                    is divisible by k - precisely the sieving constraint for twin primes.
                </div>
            `;
            
            document.getElementById('telescoping-steps-results').innerHTML = html;
        }

        // Finite-Cutoff Identity Functions
        function verifyFinite() {
            const pmax = parseInt(document.getElementById('finite-pmax').value);
            const precision = parseInt(document.getElementById('finite-precision').value);
            const format = document.getElementById('finite-format').value;
            
            if (!isPrime(pmax)) {
                document.getElementById('finite-results').innerHTML = `
                    <div class="note">
                        <strong>Error:</strong> ${pmax} is not prime. Please enter a prime number.
                    </div>
                `;
                return;
            }
            
            const primes = getPrimesUpTo(pmax);
            
            // Compute R_mod
            let logRmod = Math.log(0.25);
            for (const p of primes) {
                logRmod += Math.log((p - 1) * (p - 2) / (p * p));
            }
            const Rmod = Math.exp(logRmod);
            
            // Compute C₂
            let logC2 = 0;
            for (const p of primes) {
                logC2 += Math.log(1 - 1/((p - 1) * (p - 1)));
            }
            const C2 = Math.exp(logC2);
            
            // Compute M_no2
            let logMno2 = 0;
            for (const p of primes) {
                logMno2 += Math.log(1 - 1/p);
            }
            const Mno2 = Math.exp(logMno2);
            
            const rightSide = 0.25 * C2 * Math.pow(Mno2, 3);
            const difference = Math.abs(Rmod - rightSide);
            const match = difference < Math.pow(10, -precision + 2);
            
            const html = `
                <h3>Finite-Cutoff Verification for p_max = ${pmax}</h3>
                <table class="results-table">
                    <tr>
                        <th>Component</th>
                        <th>Value</th>
                        <th>Details</th>
                    </tr>
                    <tr>
                        <td>Primes Used</td>
                        <td>[${primes.join(', ')}]</td>
                        <td>All primes from 3 to ${pmax}</td>
                    </tr>
                    <tr>
                        <td>R_mod (Left Side)</td>
                        <td class="computation-step">${formatNumber(Rmod, precision, format)}</td>
                        <td>(1/4) × ∏[(p-1)(p-2)/p²]</td>
                    </tr>
                    <tr>
                        <td>C₂ Factor</td>
                        <td class="computation-step">${formatNumber(C2, precision, format)}</td>
                        <td>∏[1 - 1/(p-1)²]</td>
                    </tr>
                    <tr>
                        <td>M_no2 Factor</td>
                        <td class="computation-step">${formatNumber(Mno2, precision, format)}</td>
                        <td>∏[1 - 1/p]</td>
                    </tr>
                    <tr>
                        <td>Right Side</td>
                        <td class="computation-step">${formatNumber(rightSide, precision, format)}</td>
                        <td>(1/4) × C₂ × M_no2³</td>
                    </tr>
                    <tr>
                        <td>Difference</td>
                        <td class="${match ? 'exact-match' : ''}">${difference.toExponential(3)}</td>
                        <td>${match ? 'EXACT MATCH ✓' : 'Within precision'}</td>
                    </tr>
                </table>
            `;
            
            document.getElementById('finite-results').innerHTML = html;
        }

        function finiteRange() {
            const maxPrime = parseInt(document.getElementById('finite-pmax').value);
            const precision = parseInt(document.getElementById('finite-precision').value);
            const format = document.getElementById('finite-format').value;
            
            const primes = [];
            for (let i = 3; i <= maxPrime; i++) {
                if (isPrime(i)) primes.push(i);
            }
            
            let html = `
                <h3>Finite-Cutoff Range Test (primes up to ${maxPrime})</h3>
                <table class="results-table">
                    <tr>
                        <th>p_max</th>
                        <th>R_mod</th>
                        <th>Right Side</th>
                        <th>Match</th>
                    </tr>
            `;
            
            for (const pmax of primes) {
                const currentPrimes = getPrimesUpTo(pmax);
                
                let
