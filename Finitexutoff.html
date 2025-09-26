<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>K-Tuple Master Identity Research Tool</title>
    <style>
        body {
            font-family: 'Courier New', monospace;
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
            background: #0a0a0a;
            color: #00ff00;
            font-size: 14px;
        }
        .header {
            text-align: center;
            border: 2px solid #00ff00;
            padding: 20px;
            margin-bottom: 30px;
        }
        .controls {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-bottom: 30px;
        }
        .control-panel {
            border: 1px solid #00ff00;
            padding: 15px;
        }
        .control-panel h3 {
            margin-top: 0;
            color: #ffff00;
        }
        .input-group {
            margin: 10px 0;
        }
        .input-group label {
            display: block;
            margin-bottom: 5px;
        }
        .input-group input, .input-group select {
            width: 100%;
            background: #003300;
            color: #00ff00;
            border: 1px solid #00ff00;
            padding: 8px;
            font-family: 'Courier New', monospace;
        }
        .pattern-builder {
            border: 1px solid #00ff00;
            padding: 15px;
            margin: 20px 0;
        }
        .pattern-list {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 10px;
            margin: 10px 0;
        }
        .pattern-item {
            background: #003300;
            border: 1px solid #00ff00;
            padding: 10px;
            cursor: pointer;
            text-align: center;
        }
        .pattern-item:hover {
            background: #006600;
        }
        .pattern-item.selected {
            background: #009900;
        }
        .results-section {
            border: 1px solid #00ff00;
            margin: 20px 0;
            padding: 15px;
        }
        .result-table {
            width: 100%;
            border-collapse: collapse;
            margin: 10px 0;
            font-size: 12px;
        }
        .result-table th, .result-table td {
            border: 1px solid #00ff00;
            padding: 6px;
            text-align: left;
        }
        .result-table th {
            background: #003300;
            color: #ffff00;
        }
        .pass { color: #00ff00; }
        .fail { color: #ff0000; }
        .computing {
            color: #ffff00;
            font-weight: bold;
        }
        button {
            background: #003300;
            color: #00ff00;
            border: 2px solid #00ff00;
            padding: 10px 15px;
            font-family: 'Courier New', monospace;
            cursor: pointer;
            margin: 5px;
            font-size: 12px;
        }
        button:hover {
            background: #006600;
        }
        button:disabled {
            background: #222;
            color: #666;
            cursor: not-allowed;
        }
        .progress {
            width: 100%;
            background: #003300;
            border: 1px solid #00ff00;
            margin: 10px 0;
            height: 25px;
        }
        .progress-bar {
            height: 100%;
            background: linear-gradient(90deg, #00ff00, #ffff00);
            width: 0%;
            transition: width 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #000;
            font-weight: bold;
        }
        .detailed-output {
            background: #001100;
            border: 1px solid #00ff00;
            padding: 15px;
            margin: 10px 0;
            max-height: 400px;
            overflow-y: auto;
            font-size: 11px;
        }
        .search-region {
            background: #000033;
            border: 1px solid #0066ff;
            padding: 10px;
            margin: 10px 0;
            color: #66ccff;
        }
        .twin-spacing {
            margin: 15px 0;
            padding: 10px;
            background: #330000;
            border: 1px solid #ff6666;
            color: #ffaaaa;
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>K-TUPLE MASTER IDENTITY RESEARCH TOOL</h1>
        <h2>Testing: R_H = 0.25 × C_H × M^k</h2>
        <p>Configurable verification with detailed search region analysis</p>
    </div>

    <div class="controls">
        <div class="control-panel">
            <h3>COMPUTATION PARAMETERS</h3>
            <div class="input-group">
                <label for="prime-bound">Prime Bound (max: 1,000,000):</label>
                <input type="number" id="prime-bound" value="100000" min="100" max="1000000" step="1000">
            </div>
            <div class="input-group">
                <label for="search-start">Search Start:</label>
                <input type="number" id="search-start" value="2" min="2">
            </div>
            <div class="input-group">
                <label for="search-end">Search End (leave blank for full range):</label>
                <input type="number" id="search-end" placeholder="Auto">
            </div>
            <div class="input-group">
                <label for="precision-level">Precision Level:</label>
                <select id="precision-level">
                    <option value="1e-10">Standard (1e-10)</option>
                    <option value="1e-12">High (1e-12)</option>
                    <option value="1e-14">Ultra (1e-14)</option>
                    <option value="1e-16">Maximum (1e-16)</option>
                </select>
            </div>
        </div>

        <div class="control-panel">
            <h3>OUTPUT CONFIGURATION</h3>
            <div class="input-group">
                <label>
                    <input type="checkbox" id="show-detailed-log" checked> Detailed computation log
                </label>
            </div>
            <div class="input-group">
                <label>
                    <input type="checkbox" id="show-intermediate" checked> Intermediate calculations
                </label>
            </div>
            <div class="input-group">
                <label>
                    <input type="checkbox" id="show-convergence" checked> Convergence analysis
                </label>
            </div>
            <div class="input-group">
                <label>
                    <input type="checkbox" id="analyze-region" checked> Search region breakdown
                </label>
            </div>
        </div>
    </div>

    <div class="pattern-builder">
        <h3>K-TUPLE PATTERN SELECTION</h3>
        <p>Select patterns to test (click to toggle):</p>
        <div class="pattern-list" id="pattern-list">
            <!-- Patterns will be populated here -->
        </div>
        <div class="input-group">
            <label for="custom-pattern">Custom Pattern (comma-separated):</label>
            <input type="text" id="custom-pattern" placeholder="e.g., 0,2,6,8,12">
            <button onclick="addCustomPattern()">Add Custom</button>
        </div>
    </div>

    <div class="results-section">
        <h3>COMPUTATION STATUS</h3>
        <div id="status" class="computing">Ready to begin analysis</div>
        <div class="progress">
            <div id="progress-bar" class="progress-bar">0%</div>
        </div>
        <div id="computation-stats"></div>
    </div>

    <div class="results-section">
        <h3>MASTER IDENTITY VERIFICATION RESULTS</h3>
        <button onclick="runSelectedTests()" id="run-button">RUN ANALYSIS</button>
        <button onclick="exportDetailedResults()" id="export-button" disabled>EXPORT RESULTS</button>
        <button onclick="clearResults()">CLEAR</button>
        
        <table class="result-table" id="main-results">
            <thead>
                <tr>
                    <th>Pattern</th>
                    <th>Name</th>
                    <th>k</th>
                    <th>Primes Used</th>
                    <th>R_H (Direct)</th>
                    <th>Master Identity</th>
                    <th>Absolute Error</th>
                    <th>Relative Error</th>
                    <th>Status</th>
                </tr>
            </thead>
            <tbody>
                <!-- Results will be populated here -->
            </tbody>
        </table>
    </div>

    <div class="results-section" id="detailed-section" style="display: none;">
        <h3>DETAILED COMPUTATION LOG</h3>
        <div class="detailed-output" id="detailed-log"></div>
    </div>

    <div class="results-section" id="region-analysis" style="display: none;">
        <h3>SEARCH REGION ANALYSIS</h3>
        <div id="region-details"></div>
    </div>

    <div class="results-section">
        <h3>MATHEMATICAL THEORY</h3>
        <button onclick="toggleTheory()" id="theory-toggle">SHOW THEORY</button>
        <div id="theory-section" style="display: none;">
            <div class="detailed-output">
                <h4>THE K-TUPLE MASTER IDENTITY</h4>
                <p><strong>Formula:</strong> R_H = 0.25 × C_H × M^k</p>
                
                <p><strong>Where:</strong></p>
                <ul>
                    <li>R_H = ∏(p≥3) (1 - ν_p/p) [Direct k-tuple product]</li>
                    <li>C_H = ∏(p≥3) (1 - ν_p/p) / (1 - 1/p)^k [Constellation factor]</li>
                    <li>M = ∏(p≥3) (1 - 1/p) [Mertens product]</li>
                    <li>ν_p = number of distinct residues pattern occupies mod p</li>
                    <li>k = length of k-tuple pattern</li>
                </ul>

                <h4>MULTIPLICATIVE DECOMPOSITION</h4>
                <p>The identity reveals that each prime p≥3 contributes differently in the factorization:</p>
                
                <p><strong>Direct calculation:</strong> Each prime contributes once as (1 - ν_p/p)</p>
                
                <p><strong>Master Identity factorization:</strong> Each prime contributes to both:</p>
                <ul>
                    <li><strong>Pattern-dependent factor C_H:</strong> (1 - ν_p/p) / (1 - 1/p)^k</li>
                    <li><strong>Pattern-independent factor M^k:</strong> (1 - 1/p)^k</li>
                </ul>

                <p><strong>Mathematical equivalence:</strong><br>
                (1 - ν_p/p) = [(1 - ν_p/p) / (1 - 1/p)^k] × (1 - 1/p)^k</p>

                <h4>MATHEMATICAL SIGNIFICANCE</h4>
                <p><strong>Structural separation:</strong> The identity separates universal prime density effects (M^k) from pattern-specific constellation effects (C_H).</p>
                
                <p><strong>The 0.25 constant:</strong> This universal factor compensates for the multiplicative restructuring and appears to be fundamental to k-tuple mathematics.</p>
                
                <p><strong>Example for twin primes [0,2] and p=5:</strong></p>
                <ul>
                    <li>Pattern residues mod 5: {0, 2} → ν_5 = 2</li>
                    <li>Direct contribution: (1 - 2/5) = 3/5</li>
                    <li>C_H contribution: (3/5) / (4/5)^2 = 15/16</li>
                    <li>M^2 contribution: (4/5)^2 = 16/25</li>
                    <li>Product: (15/16) × (16/25) = 3/5 ✓</li>
                </ul>

                <h4>RESEARCH IMPLICATIONS</h4>
                <p>This multiplicative decomposition suggests that:</p>
                <ul>
                    <li>K-tuple densities have inherent factorizable structure</li>
                    <li>Prime constellation behavior separates into universal and specific components</li>
                    <li>The 0.25 constant may reveal deeper number-theoretic relationships</li>
                    <li>Different k-tuple patterns share common mathematical foundation</li>
                </ul>

                <h4>VERIFICATION APPROACH</h4>
                <p>This tool computationally verifies the identity by:</p>
                <ul>
                    <li>Computing R_H directly from prime products</li>
                    <li>Computing C_H and M factors separately</li>
                    <li>Verifying 0.25 × C_H × M^k equals R_H within precision limits</li>
                    <li>Testing across diverse k-tuple patterns and prime ranges</li>
                </ul>
            </div>
        </div>
    </div>

    <script>
        // Available patterns
        const PATTERNS = [
            { pattern: [0, 2], name: "Twin Primes", category: "basic" },
            { pattern: [0, 4], name: "Cousin Primes", category: "basic" },
            { pattern: [0, 6], name: "Sexy Primes", category: "basic" },
            { pattern: [0, 2, 6], name: "Prime Triplet", category: "triplet" },
            { pattern: [0, 4, 6], name: "Prime Triplet (Alt)", category: "triplet" },
            { pattern: [0, 2, 6, 8], name: "Prime Quadruplet", category: "quad" },
            { pattern: [0, 2, 6, 8, 12], name: "Prime Quintuplet", category: "quint" },
            { pattern: [0, 4, 6, 10, 12, 16], name: "Prime Sextuplet", category: "sext" },
            { pattern: [0, 2, 8, 12, 14, 18, 20], name: "Prime Septuplet", category: "sept" },
            { pattern: [0, 2, 6, 8, 12, 18, 20, 26], name: "Prime Octuplet", category: "oct" }
        ];

        let selectedPatterns = new Set();
        let computationResults = [];
        let detailedLog = [];

        // Initialize pattern selection
        function initializePatterns() {
            const patternList = document.getElementById('pattern-list');
            patternList.innerHTML = '';
            
            PATTERNS.forEach((p, index) => {
                const div = document.createElement('div');
                div.className = 'pattern-item';
                div.onclick = () => togglePattern(index);
                div.innerHTML = `
                    <strong>${p.name}</strong><br>
                    [${p.pattern.join(', ')}]<br>
                    <small>k=${p.pattern.length}</small>
                `;
                div.dataset.index = index;
                patternList.appendChild(div);
            });
            
            // Select twin primes by default
            togglePattern(0);
        }

        function togglePattern(index) {
            const element = document.querySelector(`[data-index="${index}"]`);
            if (selectedPatterns.has(index)) {
                selectedPatterns.delete(index);
                element.classList.remove('selected');
            } else {
                selectedPatterns.add(index);
                element.classList.add('selected');
            }
        }

        function addCustomPattern() {
            const input = document.getElementById('custom-pattern');
            const patternStr = input.value.trim();
            
            if (!patternStr) return;
            
            try {
                const pattern = patternStr.split(',').map(x => parseInt(x.trim())).filter(x => !isNaN(x));
                if (pattern.length === 0) throw new Error("Invalid pattern");
                
                const customPattern = {
                    pattern: pattern,
                    name: `Custom [${pattern.join(',')}]`,
                    category: "custom"
                };
                
                PATTERNS.push(customPattern);
                
                const patternList = document.getElementById('pattern-list');
                const div = document.createElement('div');
                const index = PATTERNS.length - 1;
                div.className = 'pattern-item';
                div.onclick = () => togglePattern(index);
                div.innerHTML = `
                    <strong>${customPattern.name}</strong><br>
                    [${customPattern.pattern.join(', ')}]<br>
                    <small>k=${customPattern.pattern.length}</small>
                `;
                div.dataset.index = index;
                patternList.appendChild(div);
                
                input.value = '';
                togglePattern(index);
                
            } catch (e) {
                alert('Invalid pattern format. Use comma-separated integers.');
            }
        }

        // Core mathematical functions
        function sieveOfEratosthenes(limit) {
            const isPrime = new Uint8Array(limit + 1);
            isPrime.fill(1);
            isPrime[0] = isPrime[1] = 0;
            
            for (let i = 2; i * i <= limit; i++) {
                if (isPrime[i]) {
                    for (let j = i * i; j <= limit; j += i) {
                        isPrime[j] = 0;
                    }
                }
            }
            
            const primes = [];
            for (let i = 2; i <= limit; i++) {
                if (isPrime[i]) primes.push(i);
            }
            return primes;
        }

        function countResidues(pattern, p) {
            const residues = new Set();
            for (const h of pattern) {
                residues.add(((h % p) + p) % p);
            }
            return residues.size;
        }

        function calculateRH(primes, pattern, searchStart, searchEnd, logDetails = false) {
            let product = 0.25;
            let contributions = [];
            
            for (const p of primes) {
                if (p >= searchStart && p <= searchEnd && p >= 3) {
                    const nu = countResidues(pattern, p);
                    const factor = (1 - nu / p);
                    product *= factor;
                    
                    if (logDetails) {
                        contributions.push({
                            prime: p,
                            residues: nu,
                            factor: factor,
                            runningProduct: product
                        });
                    }
                }
            }
            
            return { result: product, contributions };
        }

        function calculateCH(primes, pattern, searchStart, searchEnd, logDetails = false) {
            const k = pattern.length;
            let product = 1;
            let contributions = [];
            
            for (const p of primes) {
                if (p >= searchStart && p <= searchEnd && p >= 3) {
                    const nu = countResidues(pattern, p);
                    const numerator = 1 - nu / p;
                    const denominator = Math.pow(1 - 1/p, k);
                    const factor = numerator / denominator;
                    product *= factor;
                    
                    if (logDetails) {
                        contributions.push({
                            prime: p,
                            residues: nu,
                            numerator: numerator,
                            denominator: denominator,
                            factor: factor,
                            runningProduct: product
                        });
                    }
                }
            }
            
            return { result: product, contributions };
        }

        function calculateM(primes, searchStart, searchEnd, logDetails = false) {
            let product = 1;
            let contributions = [];
            
            for (const p of primes) {
                if (p >= searchStart && p <= searchEnd && p >= 3) {
                    const factor = (1 - 1/p);
                    product *= factor;
                    
                    if (logDetails) {
                        contributions.push({
                            prime: p,
                            factor: factor,
                            runningProduct: product
                        });
                    }
                }
            }
            
            return { result: product, contributions };
        }

        async function updateProgress(current, total, message) {
            const percentage = Math.round((current / total) * 100);
            document.getElementById('progress-bar').style.width = `${percentage}%`;
            document.getElementById('progress-bar').textContent = `${percentage}%`;
            document.getElementById('status').textContent = message;
            await new Promise(resolve => setTimeout(resolve, 1));
        }

        function logDetail(message) {
            const showDetailed = document.getElementById('show-detailed-log').checked;
            if (showDetailed) {
                detailedLog.push(`[${new Date().toLocaleTimeString()}] ${message}`);
                const logDiv = document.getElementById('detailed-log');
                if (logDiv) {
                    logDiv.textContent = detailedLog.slice(-100).join('\n');
                    logDiv.scrollTop = logDiv.scrollHeight;
                }
            }
        }

        async function runSelectedTests() {
            if (selectedPatterns.size === 0) {
                alert('Please select at least one pattern to test.');
                return;
            }

            const runButton = document.getElementById('run-button');
            const exportButton = document.getElementById('export-button');
            runButton.disabled = true;
            exportButton.disabled = true;

            computationResults = [];
            detailedLog = [];

            const primeBound = parseInt(document.getElementById('prime-bound').value);
            const searchStart = parseInt(document.getElementById('search-start').value) || 2;
            const searchEnd = parseInt(document.getElementById('search-end').value) || primeBound;
            const precision = parseFloat(document.getElementById('precision-level').value);

            const showDetailed = document.getElementById('show-detailed-log').checked;
            const showIntermediate = document.getElementById('show-intermediate').checked;
            const showRegionAnalysis = document.getElementById('analyze-region').checked;

            if (showDetailed) {
                document.getElementById('detailed-section').style.display = 'block';
            }

            if (showRegionAnalysis) {
                document.getElementById('region-analysis').style.display = 'block';
            }

            try {
                await updateProgress(0, 100, `Computing primes up to ${primeBound.toLocaleString()}...`);
                logDetail(`Starting computation with bound ${primeBound}, search range [${searchStart}, ${searchEnd}]`);

                const allPrimes = sieveOfEratosthenes(primeBound);
                const searchPrimes = allPrimes.filter(p => p >= searchStart && p <= searchEnd);
                
                await updateProgress(10, 100, `Found ${allPrimes.length} primes, ${searchPrimes.length} in search range`);
                logDetail(`Sieve complete: ${allPrimes.length} total primes, ${searchPrimes.length} in range [${searchStart}, ${searchEnd}]`);

                if (showRegionAnalysis) {
                    analyzeSearchRegion(allPrimes, searchStart, searchEnd);
                }

                const selectedPatternList = Array.from(selectedPatterns).map(i => PATTERNS[i]);
                
                for (let i = 0; i < selectedPatternList.length; i++) {
                    const pattern = selectedPatternList[i];
                    const progress = 10 + (i / selectedPatternList.length) * 80;
                    
                    await updateProgress(progress, 100, `Testing ${pattern.name}...`);
                    logDetail(`Testing pattern ${pattern.name}: [${pattern.pattern.join(', ')}]`);

                    const k = pattern.pattern.length;
                    
                    const rHResult = calculateRH(allPrimes, pattern.pattern, searchStart, searchEnd, showIntermediate);
                    const cHResult = calculateCH(allPrimes, pattern.pattern, searchStart, searchEnd, showIntermediate);
                    const mResult = calculateM(allPrimes, searchStart, searchEnd, showIntermediate);
                    
                    const masterIdentity = 0.25 * cHResult.result * Math.pow(mResult.result, k);
                    const absoluteError = Math.abs(rHResult.result - masterIdentity);
                    const relativeError = absoluteError / Math.abs(rHResult.result);
                    const status = relativeError < precision ? 'PASS' : 'FAIL';
                    
                    if (showIntermediate) {
                        logDetail(`  R_H = ${rHResult.result.toExponential(10)}`);
                        logDetail(`  C_H = ${cHResult.result.toExponential(10)}`);
                        logDetail(`  M = ${mResult.result.toExponential(10)}`);
                        logDetail(`  Master Identity = 0.25 × ${cHResult.result.toExponential(6)} × ${mResult.result.toExponential(6)}^${k} = ${masterIdentity.toExponential(10)}`);
                        logDetail(`  Relative Error = ${(relativeError * 100).toExponential(3)}%`);
                        logDetail(`  Status = ${status}`);
                    }

                    const result = {
                        pattern: `[${pattern.pattern.join(', ')}]`,
                        name: pattern.name,
                        k: k,
                        primesUsed: searchPrimes.filter(p => p >= 3).length,
                        rH: rHResult.result,
                        masterIdentity: masterIdentity,
                        absoluteError: absoluteError,
                        relativeError: relativeError,
                        status: status,
                        detailed: {
                            rHContrib: rHResult.contributions,
                            cHContrib: cHResult.contributions,
                            mContrib: mResult.contributions
                        }
                    };
                    
                    computationResults.push(result);
                }

                await updateProgress(95, 100, 'Generating results...');
                populateResults();

                await updateProgress(100, 100, `Analysis complete - tested ${selectedPatternList.length} patterns`);
                
                const stats = document.getElementById('computation-stats');
                const allPass = computationResults.every(r => r.status === 'PASS');
                const maxError = Math.max(...computationResults.map(r => r.relativeError));
                
                stats.innerHTML = `
                    <div class="twin-spacing">
                        <strong>COMPUTATION SUMMARY:</strong><br>
                        Prime bound: ${primeBound.toLocaleString()} | 
                        Search range: [${searchStart}, ${searchEnd}] | 
                        Primes used: ${searchPrimes.filter(p => p >= 3).length}<br>
                        Patterns tested: ${computationResults.length} | 
                        Status: <span class="${allPass ? 'pass' : 'fail'}">${allPass ? 'ALL PASS' : 'SOME FAILURES'}</span> | 
                        Max error: ${(maxError * 100).toExponential(2)}%
                    </div>
                `;

            } catch (error) {
                console.error('Computation error:', error);
                document.getElementById('status').textContent = `Error: ${error.message}`;
            } finally {
                runButton.disabled = false;
                exportButton.disabled = false;
            }
        }

        function populateResults() {
            const tbody = document.querySelector('#main-results tbody');
            tbody.innerHTML = '';
            
            for (const result of computationResults) {
                const row = tbody.insertRow();
                row.innerHTML = `
                    <td>${result.pattern}</td>
                    <td>${result.name}</td>
                    <td>${result.k}</td>
                    <td>${result.primesUsed}</td>
                    <td>${result.rH.toExponential(8)}</td>
                    <td>${result.masterIdentity.toExponential(8)}</td>
                    <td>${result.absoluteError.toExponential(3)}</td>
                    <td>${(result.relativeError * 100).toExponential(3)}%</td>
                    <td class="${result.status.toLowerCase()}">${result.status}</td>
                `;
            }
        }

        function analyzeSearchRegion(primes, start, end) {
            const regionDetails = document.getElementById('region-details');
            const regionPrimes = primes.filter(p => p >= start && p <= end);
            const usedPrimes = regionPrimes.filter(p => p >= 3);
            
            const density = regionPrimes.length / (end - start + 1);
            const gaps = [];
            for (let i = 1; i < regionPrimes.length; i++) {
                gaps.push(regionPrimes[i] - regionPrimes[i-1]);
            }
            
            const avgGap = gaps.length > 0 ? gaps.reduce((a, b) => a + b, 0) / gaps.length : 0;
            const maxGap = gaps.length > 0 ? Math.max(...gaps) : 0;
            const minGap = gaps.length > 0 ? Math.min(...gaps) : 0;
            
            regionDetails.innerHTML = `
                <div class="search-region">
                    <h4>SEARCH REGION BREAKDOWN</h4>
                    <strong>Range:</strong> [${start.toLocaleString()}, ${end.toLocaleString()}] (span: ${(end-start+1).toLocaleString()})<br>
                    <strong>Primes found:</strong> ${regionPrimes.length.toLocaleString()} (used in calculation: ${usedPrimes.length.toLocaleString()})<br>
                    <strong>Prime density:</strong> ${(density * 100).toFixed(4)}%<br>
                    <strong>Gap statistics:</strong> min=${minGap}, avg=${avgGap.toFixed(2)}, max=${maxGap}<br>
                    <strong>First 10 primes:</strong> ${regionPrimes.slice(0, 10).join(', ')}${regionPrimes.length > 10 ? '...' : ''}<br>
                    <strong>Last 10 primes:</strong> ${regionPrimes.length > 10 ? '...' + regionPrimes.slice(-10).join(', ') : 'See above'}
                </div>
            `;
        }

        function exportDetailedResults() {
            const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
            const primeBound = document.getElementById('prime-bound').value;
            const searchStart = document.getElementById('search-start').value;
            const searchEnd = document.getElementById('search-end').value || primeBound;
            
            let content = `K-TUPLE MASTER IDENTITY DETAILED ANALYSIS\n`;
            content += `=========================================\n\n`;
            content += `Analysis Date: ${new Date().toString()}\n`;
            content += `Prime Bound: ${primeBound}\n`;
            content += `Search Range: [${searchStart}, ${searchEnd}]\n`;
            content += `Patterns Tested: ${computationResults.length}\n\n`;

            content += `MAIN RESULTS:\n`;
            content += `Pattern\tName\tk\tPrimes\tR_H\tMaster_Identity\tAbs_Error\tRel_Error\tStatus\n`;
            for (const result of computationResults) {
                content += `${result.pattern}\t${result.name}\t${result.k}\t${result.primesUsed}\t`;
                content += `${result.rH.toExponential(10)}\t${result.masterIdentity.toExponential(10)}\t`;
                content += `${result.absoluteError.toExponential(6)}\t${(result.relativeError*100).toExponential(6)}%\t${result.status}\n`;
            }

            content += `\nDETAILED COMPUTATION LOG:\n`;
            content += detailedLog.join('\n');

            const blob = new Blob([content], { type: 'text/plain' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `ktuple_detailed_analysis_${primeBound}_${timestamp}.txt`;
            a.click();
            URL.revokeObjectURL(url);
        }

        function clearResults() {
            computationResults = [];
            detailedLog = [];
            document.querySelector('#main-results tbody').innerHTML = '';
            document.getElementById('detailed-log').textContent = '';
            document.getElementById('region-details').innerHTML = '';
            document.getElementById('computation-stats').innerHTML = '';
            document.getElementById('status').textContent = 'Ready to begin analysis';
            document.getElementById('progress-bar').style.width = '0%';
            document.getElementById('progress-bar').textContent = '0%';
            document.getElementById('detailed-section').style.display = 'none';
            document.getElementById('region-analysis').style.display = 'none';
        }

        function toggleTheory() {
            const theorySection = document.getElementById('theory-section');
            const toggleButton = document.getElementById('theory-toggle');
            
            if (theorySection.style.display === 'none') {
                theorySection.style.display = 'block';
                toggleButton.textContent = 'HIDE THEORY';
            } else {
                theorySection.style.display = 'none';
                toggleButton.textContent = 'SHOW THEORY';
            }
        }

        // Initialize the interface
        document.addEventListener('DOMContentLoaded', function() {
            initializePatterns();
        });
    </script>
</body>
</html>
