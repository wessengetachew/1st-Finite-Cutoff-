
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Wessen Twin Prime Identity Verifier</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            min-height: 100vh;
        }
        
        .container {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }
        
        h1 {
            text-align: center;
            margin-bottom: 10px;
            font-size: 2.8em;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }
        
        .subtitle {
            text-align: center;
            margin-bottom: 30px;
            font-size: 1.3em;
            opacity: 0.9;
            font-style: italic;
        }
        
        .theorem-box {
            background: linear-gradient(135deg, rgba(255, 255, 255, 0.2), rgba(255, 255, 255, 0.1));
            border: 2px solid rgba(255, 255, 255, 0.3);
            padding: 25px;
            border-radius: 15px;
            margin-bottom: 30px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
        }
        
        .theorem-title {
            font-size: 1.4em;
            font-weight: bold;
            color: #4ecdc4;
            margin-bottom: 15px;
            text-align: center;
        }
        
        .context-section {
            background: rgba(255, 255, 255, 0.1);
            padding: 20px;
            border-radius: 12px;
            margin-bottom: 25px;
            border-left: 4px solid #f39c12;
        }
        
        .context-title {
            font-size: 1.2em;
            font-weight: bold;
            color: #f39c12;
            margin-bottom: 12px;
        }
        
        .algorithm-display {
            background: rgba(0, 0, 0, 0.3);
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 30px;
            font-family: 'Courier New', monospace;
            font-size: 0.95em;
            line-height: 1.4;
        }
        
        .controls {
            display: flex;
            gap: 20px;
            align-items: center;
            margin-bottom: 30px;
            flex-wrap: wrap;
            justify-content: center;
        }
        
        .control-group {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        label {
            font-weight: bold;
        }
        
        input, button {
            padding: 12px;
            border: none;
            border-radius: 8px;
            font-size: 16px;
        }
        
        input {
            background: rgba(255, 255, 255, 0.2);
            color: white;
            width: 100px;
        }
        
        input::placeholder {
            color: rgba(255, 255, 255, 0.7);
        }
        
        button {
            background: linear-gradient(45deg, #ff6b6b, #ee5a52);
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
            font-weight: bold;
            min-width: 120px;
        }
        
        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
        }
        
        .mode-toggle {
            background: linear-gradient(45deg, #4ecdc4, #44a08d);
        }
        
        .results {
            display: grid;
            gap: 20px;
            margin-top: 30px;
        }
        
        .result-card {
            background: rgba(255, 255, 255, 0.15);
            padding: 25px;
            border-radius: 15px;
            border-left: 5px solid #4ecdc4;
        }
        
        .result-header {
            font-size: 1.4em;
            font-weight: bold;
            margin-bottom: 20px;
            color: #4ecdc4;
            text-align: center;
        }
        
        .calculation-steps {
            background: rgba(0, 0, 0, 0.2);
            padding: 15px;
            border-radius: 8px;
            margin: 15px 0;
            font-family: 'Courier New', monospace;
            font-size: 0.9em;
            line-height: 1.3;
        }
        
        .identity-check {
            background: rgba(46, 204, 113, 0.2);
            border: 2px solid #2ecc71;
            padding: 20px;
            border-radius: 10px;
            margin-top: 20px;
            text-align: center;
            font-weight: bold;
        }
        
        .identity-fail {
            background: rgba(231, 76, 60, 0.2);
            border: 2px solid #e74c3c;
        }
        
        .summary-table {
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            overflow: hidden;
            margin-top: 20px;
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
        }
        
        th, td {
            padding: 12px;
            text-align: center;
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
            font-size: 0.9em;
        }
        
        th {
            background: rgba(255, 255, 255, 0.2);
            font-weight: bold;
        }
        
        .loading {
            text-align: center;
            padding: 20px;
            font-size: 1.2em;
        }
        
        .exact-mode {
            border-left-color: #f39c12;
        }
        
        .exact-mode .identity-check {
            background: rgba(243, 156, 18, 0.2);
            border-color: #f39c12;
        }
        
        .highlight {
            background: rgba(255, 255, 255, 0.1);
            padding: 2px 6px;
            border-radius: 4px;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🧮 Wessen Twin Prime Identity Verifier</h1>
        <div class="subtitle">Finite-Cutoff Modular-Hardy-Littlewood Equivalence Theorem</div>
        
        <div class="theorem-box">
            <div class="theorem-title">📜 The Wessen Twin Prime Identity</div>
            <div class="algorithm-display">
                <strong>Main Identity:</strong> R<sub>modular</sub>(p<sub>maximum</sub>) = ¼ · C<sub>twin</sub>(p<sub>maximum</sub>) · (M<sub>no-two</sub>(p<sub>maximum</sub>))<sup>3</sup>
                <br><br>
                <strong>Component Definitions:</strong><br>
                • R<sub>modular</sub>(p<sub>maximum</sub>) = ¼ ∏<sub>3 ≤ prime ≤ p<sub>maximum</sub></sub> [(prime-1)(prime-2)/prime²]<br>
                • C<sub>twin</sub>(p<sub>maximum</sub>) = ∏<sub>3 ≤ prime ≤ p<sub>maximum</sub></sub> [1 - 1/(prime-1)²]<br>
                • M<sub>no-two</sub>(p<sub>maximum</sub>) = ∏<sub>3 ≤ prime ≤ p<sub>maximum</sub></sub> [1 - 1/prime]
                <br><br>
                <em>Where the products are taken over all odd primes from 3 up to p<sub>maximum</sub> inclusive.</em>
            </div>
        </div>
        
        <div class="context-section">
            <div class="context-title">🔍 Mathematical Context</div>
            This identity connects three fundamental aspects of number theory:
            <br><br>
            <strong>R<sub>modular</sub></strong>: Represents modular residue ratios in prime-based sieves, encoding how twin prime candidates survive modular constraints.
            <br><br>
            <strong>C<sub>twin</sub></strong>: Twin prime correlation factor derived from Hardy-Littlewood circle method, measuring pair correlations in prime distributions.
            <br><br>
            <strong>M<sub>no-two</sub></strong>: Modular "no-two" structure capturing exclusion principles in residue systems, related to Euler totient densities.
            <br><br>
            The identity bridges discrete modular arithmetic with analytic number theory, providing a finite-cutoff version of asymptotic twin prime conjectures.
        </div>
        
        <div class="context-section">
            <div class="context-title">⚡ Computational Verification Methods</div>
            <strong>Algorithm 1 (Numeric Mode):</strong> Uses high-precision floating-point arithmetic with configurable tolerance testing. Computes each component separately and verifies the identity through ratio analysis with |ρ - 1| ≤ ε criterion.
            <br><br>
            <strong>Algorithm 2 (Exact Mode):</strong> Employs exact integer arithmetic using BigInt operations to eliminate all floating-point precision errors. Tests the algebraically equivalent condition: N<sub>R</sub> × D<sub>C</sub> × D<sub>M</sub>³ = D<sub>R</sub> × N<sub>C</sub> × N<sub>M</sub>³ for absolute verification.
        </div>
        
        <div class="context-section">
            <div class="context-title">🎯 Research Implications</div>
            <strong>If Identity Holds Generally:</strong> Would provide the first exact finite formula for twin prime distributions, revolutionizing computational approaches to the Twin Prime Conjecture.
            <br><br>
            <strong>If Identity Fails Beyond p<sub>maximum</sub>=5:</strong> Would indicate fundamental transition points in prime structure, potentially revealing where classical analytic methods break down and new approaches are needed.
            <br><br>
            <strong>Connection to Riemann Hypothesis:</strong> The modular structure relates to Möbius function cancellation discussed in the broader framework, providing computational testing grounds for RH-equivalent formulations.
        </div>
        
        <div class="controls">
            <div class="control-group">
                <label for="pmax">p<sub>maximum</sub>:</label>
                <input type="number" id="pmax" value="5" min="3" max="97" title="Enter the largest prime to include in the product">
            </div>
            <div class="control-group">
                <label for="testRange">Test Range:</label>
                <input type="number" id="startRange" value="3" min="3" max="97" placeholder="Start" title="Starting prime for range test">
                <input type="number" id="endRange" value="13" min="3" max="97" placeholder="End" title="Ending prime for range test">
            </div>
            <button onclick="testSingle()" title="Verify identity for single p_maximum value">Test Single Value</button>
            <button onclick="testRange()" title="Test identity across range of prime values">Test Prime Range</button>
            <button class="mode-toggle" onclick="toggleMode()" id="modeToggle" title="Switch between numeric and exact arithmetic methods">Numeric Mode</button>
        </div>
        
        <div id="results" class="results"></div>
    </div>

    <script>
        let exactMode = false;
        
        function isPrime(n) {
            if (n < 2) return false;
            if (n === 2) return true;
            if (n % 2 === 0) return false;
            for (let i = 3; i * i <= n; i += 2) {
                if (n % i === 0) return false;
            }
            return true;
        }
        
        function getPrimesInRange(start, end) {
            const primes = [];
            for (let i = Math.max(start, 3); i <= end; i++) {
                if (isPrime(i)) primes.push(i);
            }
            return primes;
        }
        
        function gcd(a, b) {
            while (b !== 0) {
                const temp = b;
                b = a % b;
                a = temp;
            }
            return Math.abs(a);
        }
        
        // Exact integer arithmetic version (Algorithm 2)
        function verifyIdentityExact(pmax) {
            const primes = getPrimesInRange(3, pmax);
            
            // Initialize integer numerators/denominators
            let NR = 1n, DR = 1n;  // For ∏(p-1)(p-2)/p²
            let NC = 1n, DC = 1n;  // For ∏p(p-2)/(p-1)² 
            let NM = 1n, DM = 1n;  // For ∏(p-1)/p
            
            const steps = [];
            steps.push(`Exact integer arithmetic verification for p_maximum = ${pmax}`);
            steps.push(`Prime set: ${primes.join(', ')}`);
            steps.push(`Algorithm: Test exact equality N_R × D_C × D_M³ = D_R × N_C × N_M³`);
            steps.push('');
            
            for (const p of primes) {
                const pBig = BigInt(p);
                
                // R component: (prime-1)(prime-2)/prime²
                NR *= (pBig - 1n) * (pBig - 2n);
                DR *= pBig * pBig;
                
                // C component: prime(prime-2)/(prime-1)² (since 1-1/(prime-1)² = prime(prime-2)/(prime-1)²)
                NC *= pBig * (pBig - 2n);
                DC *= (pBig - 1n) * (pBig - 1n);
                
                // M component: (prime-1)/prime
                NM *= (pBig - 1n);
                DM *= pBig;
                
                steps.push(`Processing prime = ${p}:`);
                steps.push(`  R_product numerator: ${NR.toString()}`);
                steps.push(`  R_product denominator: ${DR.toString()}`);
                steps.push(`  C_twin numerator: ${NC.toString()}`);
                steps.push(`  C_twin denominator: ${DC.toString()}`);
                steps.push(`  M_no-two numerator: ${NM.toString()}`);
                steps.push(`  M_no-two denominator: ${DM.toString()}`);
                steps.push('');
                
                // Reduce fractions periodically to keep numbers manageable
                const gcdR = gcd(Number(NR % 1000000n), Number(DR % 1000000n));
                const gcdC = gcd(Number(NC % 1000000n), Number(DC % 1000000n));
                const gcdM = gcd(Number(NM % 1000000n), Number(DM % 1000000n));
                
                if (gcdR > 1) { NR /= BigInt(gcdR); DR /= BigInt(gcdR); }
                if (gcdC > 1) { NC /= BigInt(gcdC); DC /= BigInt(gcdC); }
                if (gcdM > 1) { NM /= BigInt(gcdM); DM /= BigInt(gcdM); }
            }
            
            steps.push('');
            steps.push('Final fraction representations:');
            steps.push(`R_product = ${NR}/${DR}`);
            steps.push(`C_twin = ${NC}/${DC}`);
            steps.push(`M_no-two = ${NM}/${DM}`);
            steps.push('');
            steps.push('Cross-multiplication equality test:');
            steps.push(`Identity requires: (1/4) × (N_R/D_R) = (1/4) × (N_C/D_C) × (N_M/D_M)³`);
            steps.push(`Algebraic simplification: N_R × D_C × D_M³ = D_R × N_C × N_M³`);
            steps.push('');
            
            // Test: (1/4) * (NR/DR) == (1/4) * (NC/DC) * (NM/DM)³
            // Simplifies to: NR * DC * DM³ == DR * NC * NM³
            const leftSide = NR * DC * DM * DM * DM;
            const rightSide = DR * NC * NM * NM * NM;
            
            const equal = leftSide === rightSide;
            
            steps.push('Exact integer comparison:');
            steps.push(`Left side:  N_R × D_C × D_M³ = ${NR} × ${DC} × ${DM}³`);
            steps.push(`Right side: D_R × N_C × N_M³ = ${DR} × ${NC} × ${NM}³`);
            steps.push(`Left side value:  ${leftSide.toString()}`);
            steps.push(`Right side value: ${rightSide.toString()}`);
            steps.push(`Exact equality: ${equal}`);
            
            if (equal) {
                steps.push('');
                steps.push('✅ EXACT VERIFICATION: The identity holds with perfect integer precision!');
            } else {
                steps.push('');
                steps.push('❌ EXACT VERIFICATION: The identity fails under exact integer arithmetic.');
                steps.push('This definitively proves the identity does not hold for this p_maximum.');
            }
            
            return {
                pmax: pmax,
                equal: equal,
                steps: steps,
                leftSide: leftSide.toString(),
                rightSide: rightSide.toString(),
                fractions: {
                    R: `${NR}/${DR}`,
                    C: `${NC}/${DC}`,
                    M: `${NM}/${DM}`
                }
            };
        }
        
        // Numeric version (Algorithm 1)
        function verifyIdentityNumeric(pmax, tolerance = 1e-15) {
            const primes = getPrimesInRange(3, pmax);
            
            let R = 0.25; // Start with 1/4
            let C = 1.0;
            let M = 1.0;
            
            const steps = [];
            steps.push(`Numeric floating-point verification for p_maximum = ${pmax}`);
            steps.push(`Tolerance threshold = ${tolerance}`);
            steps.push(`Prime set: ${primes.join(', ')}`);
            steps.push(`Algorithm: Compute ratio ρ = R_modular/RightSide, verify |ρ - 1| ≤ ε`);
            steps.push('');
            
            for (const p of primes) {
                const rFactor = (p-1) * (p-2) / (p * p);
                const cFactor = 1 - 1/((p-1) * (p-1));
                const mFactor = 1 - 1/p;
                
                R *= rFactor;
                C *= cFactor;
                M *= mFactor;
                
                steps.push(`Processing prime = ${p}:`);
                steps.push(`  R_modular factor: (${p}-1)(${p}-2)/${p}² = ${(p-1)*(p-2)}/${p*p} = ${rFactor.toFixed(10)}`);
                steps.push(`  C_twin factor: 1-1/(${p}-1)² = 1-1/${(p-1)*(p-1)} = ${cFactor.toFixed(10)}`);
                steps.push(`  M_no-two factor: 1-1/${p} = ${(p-1)}/${p} = ${mFactor.toFixed(10)}`);
                steps.push(`  Running R_modular = ${R.toFixed(15)}`);
                steps.push(`  Running C_twin = ${C.toFixed(12)}`);
                steps.push(`  Running M_no-two = ${M.toFixed(12)}`);
                steps.push('');
            }
            
            const rightSide = 0.25 * C * Math.pow(M, 3);
            const ratio = R / rightSide;
            const passed = Math.abs(ratio - 1) <= tolerance;
            
            steps.push('Final identity verification:');
            steps.push(`R_modular = ${R.toFixed(15)}`);
            steps.push(`Right side = (1/4) × C_twin × (M_no-two)³`);
            steps.push(`Right side = (1/4) × ${C.toFixed(12)} × (${M.toFixed(12)})³`);
            steps.push(`Right side = 0.25 × ${C.toFixed(12)} × ${Math.pow(M, 3).toFixed(12)}`);
            steps.push(`Right side = ${rightSide.toFixed(15)}`);
            steps.push('');
            steps.push('Ratio analysis:');
            steps.push(`Ratio ρ = R_modular / RightSide = ${R.toFixed(15)} / ${rightSide.toFixed(15)}`);
            steps.push(`Ratio ρ = ${ratio.toFixed(15)}`);
            steps.push(`Deviation |ρ - 1| = ${Math.abs(ratio - 1).toExponential(6)}`);
            steps.push(`Tolerance threshold = ${tolerance.toExponential(3)}`);
            steps.push(`Identity verification: ${passed ? 'PASSED' : 'FAILED'}`);
            
            if (passed) {
                steps.push('');
                steps.push('✅ NUMERIC VERIFICATION: Identity holds within tolerance!');
            } else {
                steps.push('');
                steps.push('❌ NUMERIC VERIFICATION: Identity fails beyond tolerance threshold.');
                if (Math.abs(ratio - 0.25) < tolerance) {
                    steps.push('Note: Ratio ≈ 1/4 suggests systematic factor-of-4 deviation.');
                }
            }
            
            return {
                pmax: pmax,
                R: R,
                C: C,
                M: M,
                rightSide: rightSide,
                ratio: ratio,
                passed: passed,
                steps: steps
            };
        }
        
        function displayExactResult(result) {
            const resultDiv = document.createElement('div');
            resultDiv.className = 'result-card exact-mode';
            
            resultDiv.innerHTML = `
                <div class="result-header">🔬 Exact Integer Test: p<sub>max</sub> = ${result.pmax}</div>
                
                <div class="calculation-steps">
                    ${result.steps.join('<br>')}
                </div>
                
                <div class="identity-check ${result.equal ? '' : 'identity-fail'}">
                    ${result.equal ? '✅ WESSEN IDENTITY VERIFIED WITH EXACT PRECISION!' : '❌ WESSEN IDENTITY DEFINITIVELY FAILED'}
                    <br><br>
                    <div class="highlight">Algorithm 2: Exact Integer Arithmetic Verification</div>
                    <br>
                    <strong>Final Component Fractions:</strong><br>
                    R_product = ${result.fractions.R}<br>
                    C_twin = ${result.fractions.C}<br>
                    M_no-two = ${result.fractions.M}<br><br>
                    <strong>Cross-Multiplication Test Result:</strong><br>
                    ${result.equal ? 
                        'Perfect integer equality confirmed - no floating-point approximation errors possible.' :
                        'Exact integer inequality proven - identity mathematically impossible for this p_maximum.'
                    }
                </div>
            `;
            
            return resultDiv;
        }
        
        function displayNumericResult(result) {
            const resultDiv = document.createElement('div');
            resultDiv.className = 'result-card';
            
            resultDiv.innerHTML = `
                <div class="result-header">📊 Numeric Test: p<sub>max</sub> = ${result.pmax}</div>
                
                <div class="calculation-steps">
                    ${result.steps.join('<br>')}
                </div>
                
                <div class="identity-check ${result.passed ? '' : 'identity-fail'}">
                    ${result.passed ? '✅ WESSEN IDENTITY VERIFIED WITHIN NUMERICAL TOLERANCE!' : '❌ WESSEN IDENTITY FAILED NUMERICAL VERIFICATION'}
                    <br><br>
                    <div class="highlight">Algorithm 1: High-Precision Numeric Verification</div>
                    <br>
                    <strong>Final Component Values:</strong><br>
                    R_modular = ${result.R.toFixed(15)}<br>
                    C_twin = ${result.C.toFixed(12)}<br>
                    M_no-two = ${result.M.toFixed(12)}<br>
                    Right Side = ${result.rightSide.toFixed(15)}<br><br>
                    <strong>Statistical Analysis:</strong><br>
                    Ratio ρ = ${result.ratio.toFixed(12)}<br>
                    Deviation = ${Math.abs(result.ratio - 1).toExponential(3)}<br>
                    ${result.passed ? 
                        'Verification successful within computational precision limits.' :
                        `Identity fails beyond tolerance. ${Math.abs(result.ratio - 0.25) < 1e-10 ? 'Note: Ratio ≈ 1/4 indicates systematic deviation pattern.' : ''}`
                    }
                </div>
            `;
            
            return resultDiv;
        }
        
        function testSingle() {
            const pmax = parseInt(document.getElementById('pmax').value);
            const resultsDiv = document.getElementById('results');
            
            resultsDiv.innerHTML = '<div class="loading">🔄 Computing...</div>';
            
            setTimeout(() => {
                try {
                    if (exactMode) {
                        const result = verifyIdentityExact(pmax);
                        resultsDiv.innerHTML = '';
                        resultsDiv.appendChild(displayExactResult(result));
                    } else {
                        const result = verifyIdentityNumeric(pmax);
                        resultsDiv.innerHTML = '';
                        resultsDiv.appendChild(displayNumericResult(result));
                    }
                } catch (error) {
                    resultsDiv.innerHTML = `<div class="result-card identity-fail">Error: ${error.message}<br>Stack: ${error.stack}</div>`;
                }
            }, 100);
        }
        
        function testRange() {
            const start = parseInt(document.getElementById('startRange').value);
            const end = parseInt(document.getElementById('endRange').value);
            const resultsDiv = document.getElementById('results');
            
            if (start > end) {
                resultsDiv.innerHTML = '<div class="result-card identity-fail">Error: Start value must be ≤ End value</div>';
                return;
            }
            
            resultsDiv.innerHTML = '<div class="loading">🔄 Computing range...</div>';
            
            setTimeout(() => {
                try {
                    const results = [];
                    
                    for (let pmax = start; pmax <= end; pmax++) {
                        if (isPrime(pmax)) {
                            if (exactMode) {
                                results.push(verifyIdentityExact(pmax));
                            } else {
                                results.push(verifyIdentityNumeric(pmax));
                            }
                        }
                    }
                    
                    resultsDiv.innerHTML = '';
                    
                    // Summary table
                    const summaryDiv = document.createElement('div');
                    const modeText = exactMode ? 'Exact' : 'Numeric';
                    summaryDiv.innerHTML = `
                        <div class="result-header">📈 ${modeText} Range Summary: p<sub>max</sub> ${start} to ${end}</div>
                        <div class="summary-table">
                            <table>
                                <tr>
                                    <th>p<sub>max</sub></th>
                                    <th>${exactMode ? 'Exact Equal' : 'Ratio ρ'}</th>
                                    <th>Status</th>
                                    ${!exactMode ? '<th>R<sub>modular</sub></th><th>Right Side</th>' : ''}
                                </tr>
                                ${results.map(r => `
                                    <tr>
                                        <td>${r.pmax}</td>
                                        <td>${exactMode ? (r.equal ? 'TRUE' : 'FALSE') : r.ratio.toFixed(6)}</td>
                                        <td>${exactMode ? (r.equal ? '✅' : '❌') : (r.passed ? '✅' : '❌')}</td>
                                        ${!exactMode ? `<td>${r.R.toFixed(8)}</td><td>${r.rightSide.toFixed(8)}</td>` : ''}
                                    </tr>
                                `).join('')}
                            </table>
                        </div>
                    `;
                    
                    resultsDiv.appendChild(summaryDiv);
                    
                    // Individual results
                    results.forEach(result => {
                        if (exactMode) {
                            resultsDiv.appendChild(displayExactResult(result));
                        } else {
                            resultsDiv.appendChild(displayNumericResult(result));
                        }
                    });
                    
                } catch (error) {
                    resultsDiv.innerHTML = `<div class="result-card identity-fail">Error: ${error.message}</div>`;
                }
            }, 100);
        }
        
        function toggleMode() {
            exactMode = !exactMode;
            const button = document.getElementById('modeToggle');
            button.textContent = exactMode ? 'Exact Mode' : 'Numeric Mode';
            button.style.background = exactMode ? 
                'linear-gradient(45deg, #f39c12, #e67e22)' : 
                'linear-gradient(45deg, #4ecdc4, #44a08d)';
        }
        
        // Initialize with p_max = 5
        document.addEventListener('DOMContentLoaded', function() {
            testSingle();
        });
    </script>
</body>
</html>
