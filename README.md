<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Farey Triangle & Cayley Transform - Unlimited Explorer</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Fira+Code:wght@300;400;600&family=Libre+Baskerville:ital,wght@0,400;0,700;1,400&display=swap');
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --bg-deep: #0a0e27;
            --bg-mid: #141b3d;
            --bg-light: #1e2a4a;
            --gold: #ffd700;
            --gold-dim: #b8960f;
            --cyan: #00ffff;
            --cyan-dim: #008b8b;
            --geodesic: #1abc9c;
            --cusp: #e67e22;
            --prime: #3498db;
            --text: #e8f1f5;
            --text-dim: #8899aa;
            --border: rgba(255, 215, 0, 0.3);
        }

        body {
            font-family: 'Libre Baskerville', serif;
            background: radial-gradient(ellipse at center, var(--bg-mid) 0%, var(--bg-deep) 100%);
            color: var(--text);
            min-height: 100vh;
            position: relative;
            overflow-x: hidden;
        }

        body::after {
            content: 'PSL(2,ℤ)';
            position: fixed;
            bottom: 20px;
            right: 20px;
            font-family: 'Fira Code', monospace;
            font-size: 6em;
            color: rgba(255, 215, 0, 0.03);
            font-weight: 700;
            z-index: 0;
            pointer-events: none;
        }

        .starfield {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 0;
        }

        .star {
            position: absolute;
            width: 2px;
            height: 2px;
            background: white;
            border-radius: 50%;
            animation: twinkle 4s ease-in-out infinite;
        }

        @keyframes twinkle {
            0%, 100% { opacity: 0.3; transform: scale(1); }
            50% { opacity: 1; transform: scale(1.5); }
        }

        .main-container {
            max-width: 2400px;
            margin: 0 auto;
            padding: 20px;
            position: relative;
            z-index: 1;
        }

        header {
            text-align: center;
            padding: 40px 20px 30px;
            position: relative;
            margin-bottom: 30px;
        }

        header::before {
            content: '';
            position: absolute;
            top: 0;
            left: 50%;
            transform: translateX(-50%);
            width: 80%;
            height: 2px;
            background: linear-gradient(90deg, transparent, var(--gold), transparent);
        }

        header::after {
            content: '';
            position: absolute;
            bottom: 0;
            left: 50%;
            transform: translateX(-50%);
            width: 60%;
            height: 1px;
            background: linear-gradient(90deg, transparent, var(--cyan), transparent);
        }

        h1 {
            font-size: 2.8em;
            font-weight: 700;
            margin-bottom: 15px;
            position: relative;
            display: inline-block;
        }

        h1::before {
            content: '⟨';
            color: var(--gold);
            margin-right: 15px;
            font-size: 1.2em;
        }

        h1::after {
            content: '⟩';
            color: var(--gold);
            margin-left: 15px;
            font-size: 1.2em;
        }

        .title-main {
            background: linear-gradient(135deg, var(--gold) 0%, var(--cyan) 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            animation: shimmer 3s ease-in-out infinite;
        }

        @keyframes shimmer {
            0%, 100% { filter: brightness(1); }
            50% { filter: brightness(1.5); }
        }

        .subtitle {
            font-size: 1em;
            color: var(--text-dim);
            font-style: italic;
            letter-spacing: 2px;
            font-family: 'Fira Code', monospace;
        }

        .viz-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 20px;
            margin-bottom: 30px;
        }

        .canvas-panel {
            background: linear-gradient(135deg, var(--bg-light) 0%, var(--bg-mid) 100%);
            border: 1px solid var(--border);
            position: relative;
            overflow: hidden;
        }

        .canvas-panel::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: 
                linear-gradient(0deg, transparent 24%, rgba(255, 255, 255, 0.02) 25%, rgba(255, 255, 255, 0.02) 26%, transparent 27%, transparent 74%, rgba(255, 255, 255, 0.02) 75%, rgba(255, 255, 255, 0.02) 76%, transparent 77%, transparent),
                linear-gradient(90deg, transparent 24%, rgba(255, 255, 255, 0.02) 25%, rgba(255, 255, 255, 0.02) 26%, transparent 27%, transparent 74%, rgba(255, 255, 255, 0.02) 75%, rgba(255, 255, 255, 0.02) 76%, transparent 77%, transparent);
            background-size: 50px 50px;
            pointer-events: none;
        }

        .panel-header {
            background: linear-gradient(90deg, rgba(255, 215, 0, 0.1), rgba(0, 255, 255, 0.1));
            padding: 15px 20px;
            border-bottom: 1px solid var(--border);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .panel-title {
            font-size: 1.3em;
            font-weight: 700;
            font-family: 'Fira Code', monospace;
            color: var(--gold);
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .panel-subtitle {
            font-size: 0.85em;
            color: var(--text-dim);
            font-family: 'Fira Code', monospace;
            font-style: italic;
        }

        canvas {
            display: block;
            width: 100%;
            height: auto;
            background: radial-gradient(ellipse at center, rgba(26, 26, 46, 0.5), rgba(10, 14, 39, 0.9));
        }

        .controls-section {
            background: linear-gradient(135deg, var(--bg-light) 0%, var(--bg-mid) 100%);
            border: 1px solid var(--border);
            margin-bottom: 30px;
            position: relative;
        }

        .controls-header {
            background: linear-gradient(90deg, rgba(255, 215, 0, 0.15), rgba(0, 255, 255, 0.15));
            padding: 20px;
            border-bottom: 1px solid var(--border);
            font-family: 'Fira Code', monospace;
            font-size: 1.2em;
            font-weight: 600;
            color: var(--gold);
            text-transform: uppercase;
            letter-spacing: 3px;
        }

        .controls-header::before {
            content: '⚙ ';
            margin-right: 10px;
        }

        .controls-body {
            padding: 30px;
        }

        .control-row {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-bottom: 20px;
        }

        .control-item {
            background: rgba(0, 0, 0, 0.3);
            padding: 15px;
            border: 1px solid rgba(255, 215, 0, 0.2);
            border-radius: 4px;
            transition: all 0.3s;
        }

        .control-item:hover {
            border-color: var(--gold);
            box-shadow: 0 0 20px rgba(255, 215, 0, 0.2);
        }

        .control-label {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
            font-family: 'Fira Code', monospace;
            font-size: 0.85em;
            color: var(--text-dim);
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .control-value {
            color: var(--cyan);
            font-weight: 600;
            font-size: 1.1em;
            font-family: 'Fira Code', monospace;
            text-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
        }

        input[type="number"], input[type="text"] {
            width: 100%;
            padding: 8px 12px;
            background: rgba(0, 0, 0, 0.5);
            border: 1px solid rgba(0, 255, 255, 0.3);
            border-radius: 4px;
            color: var(--cyan);
            font-family: 'Fira Code', monospace;
            font-size: 1em;
            transition: all 0.3s;
        }

        input[type="number"]:focus, input[type="text"]:focus {
            outline: none;
            border-color: var(--cyan);
            box-shadow: 0 0 15px rgba(0, 255, 255, 0.3);
        }

        input[type="range"] {
            width: 100%;
            height: 6px;
            background: linear-gradient(90deg, var(--gold-dim), var(--cyan-dim));
            border-radius: 3px;
            outline: none;
            -webkit-appearance: none;
        }

        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 18px;
            height: 18px;
            background: var(--gold);
            border: 2px solid var(--bg-deep);
            border-radius: 50%;
            cursor: pointer;
            box-shadow: 0 0 10px var(--gold);
            transition: all 0.2s;
        }

        input[type="range"]::-webkit-slider-thumb:hover {
            width: 22px;
            height: 22px;
            box-shadow: 0 0 20px var(--gold);
        }

        input[type="range"]::-moz-range-thumb {
            width: 18px;
            height: 18px;
            background: var(--gold);
            border: 2px solid var(--bg-deep);
            border-radius: 50%;
            cursor: pointer;
            box-shadow: 0 0 10px var(--gold);
        }

        .section-header {
            font-family: 'Fira Code', monospace;
            color: var(--gold);
            font-size: 1.1em;
            margin: 25px 0 15px 0;
            padding-bottom: 10px;
            border-bottom: 1px solid rgba(255, 215, 0, 0.3);
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .toggle-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-top: 20px;
        }

        .toggle-item {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px;
            background: rgba(0, 0, 0, 0.2);
            border: 1px solid rgba(0, 255, 255, 0.2);
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.3s;
        }

        .toggle-item:hover {
            background: rgba(0, 255, 255, 0.1);
            border-color: var(--cyan);
        }

        .toggle-switch {
            position: relative;
            width: 50px;
            height: 24px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 12px;
            transition: all 0.3s;
            border: 1px solid var(--text-dim);
        }

        .toggle-switch::after {
            content: '';
            position: absolute;
            width: 18px;
            height: 18px;
            border-radius: 50%;
            background: var(--text-dim);
            top: 2px;
            left: 2px;
            transition: all 0.3s;
        }

        input[type="checkbox"] {
            display: none;
        }

        input[type="checkbox"]:checked + .toggle-item .toggle-switch {
            background: var(--gold);
            border-color: var(--gold);
        }

        input[type="checkbox"]:checked + .toggle-item .toggle-switch::after {
            left: 28px;
            background: white;
            box-shadow: 0 0 10px var(--gold);
        }

        .toggle-label {
            font-family: 'Fira Code', monospace;
            font-size: 0.9em;
            color: var(--text);
            flex: 1;
        }

        .action-bar {
            display: flex;
            gap: 15px;
            flex-wrap: wrap;
            margin-top: 25px;
            padding-top: 25px;
            border-top: 1px solid rgba(255, 215, 0, 0.2);
        }

        .btn {
            padding: 12px 30px;
            font-family: 'Fira Code', monospace;
            font-size: 0.9em;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 1px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.3s;
            position: relative;
            overflow: hidden;
        }

        .btn::before {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            width: 0;
            height: 0;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.3);
            transform: translate(-50%, -50%);
            transition: width 0.5s, height 0.5s;
        }

        .btn:hover::before {
            width: 300px;
            height: 300px;
        }

        .btn span {
            position: relative;
            z-index: 1;
        }

        .btn-primary {
            background: linear-gradient(135deg, var(--gold-dim), var(--gold));
            color: var(--bg-deep);
            box-shadow: 0 4px 15px rgba(255, 215, 0, 0.3);
        }

        .btn-primary:hover {
            box-shadow: 0 6px 25px rgba(255, 215, 0, 0.5);
            transform: translateY(-2px);
        }

        .btn-secondary {
            background: linear-gradient(135deg, var(--cyan-dim), var(--cyan));
            color: var(--bg-deep);
            box-shadow: 0 4px 15px rgba(0, 255, 255, 0.3);
        }

        .btn-secondary:hover {
            box-shadow: 0 6px 25px rgba(0, 255, 255, 0.5);
            transform: translateY(-2px);
        }

        .btn-accent {
            background: linear-gradient(135deg, #e67e22, #e74c3c);
            color: white;
            box-shadow: 0 4px 15px rgba(230, 126, 34, 0.3);
        }

        .farey-point-list {
            display: flex;
            flex-direction: column;
            gap: 10px;
            max-height: 300px;
            overflow-y: auto;
            padding: 10px;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 4px;
        }

        .farey-point-item {
            display: flex;
            gap: 10px;
            align-items: center;
        }

        .farey-point-item input {
            flex: 1;
        }

        .remove-btn {
            padding: 5px 10px;
            background: #e74c3c;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-family: 'Fira Code', monospace;
            font-size: 0.8em;
        }

        .remove-btn:hover {
            background: #c0392b;
        }

        .add-btn {
            padding: 8px 20px;
            background: var(--geodesic);
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-family: 'Fira Code', monospace;
            font-size: 0.9em;
            margin-top: 10px;
        }

        .add-btn:hover {
            background: #16a085;
        }

        select {
            width: 100%;
            padding: 8px 12px;
            background: rgba(0, 0, 0, 0.5);
            border: 1px solid rgba(0, 255, 255, 0.3);
            border-radius: 4px;
            color: var(--cyan);
            font-family: 'Fira Code', monospace;
            font-size: 0.9em;
            cursor: pointer;
        }

        select:focus {
            outline: none;
            border-color: var(--cyan);
            box-shadow: 0 0 15px rgba(0, 255, 255, 0.3);
        }

        .help-text {
            font-size: 0.8em;
            color: var(--text-dim);
            font-style: italic;
            margin-top: 5px;
        }

        @media (max-width: 1800px) {
            .viz-grid {
                grid-template-columns: 1fr 1fr;
            }
        }

        @media (max-width: 1200px) {
            .viz-grid {
                grid-template-columns: 1fr;
            }
        }

        @media (max-width: 768px) {
            h1 {
                font-size: 2em;
            }
            .control-row {
                grid-template-columns: 1fr;
            }
        }

        .loading {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: var(--bg-deep);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            transition: opacity 0.5s;
        }

        .loading.hidden {
            opacity: 0;
            pointer-events: none;
        }

        .loading-symbol {
            font-size: 4em;
            color: var(--gold);
            font-family: 'Fira Code', monospace;
            animation: pulse 1.5s ease-in-out infinite;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); opacity: 0.7; }
            50% { transform: scale(1.2); opacity: 1; }
        }

        .loading-text {
            margin-top: 20px;
            font-family: 'Fira Code', monospace;
            color: var(--text-dim);
            letter-spacing: 2px;
        }
    </style>
</head>
<body>
    <div class="loading" id="loading">
        <div class="loading-symbol">ζ(s)</div>
        <div class="loading-text">Initializing Unlimited Explorer...</div>
    </div>

    <div class="starfield" id="starfield"></div>

    <div class="main-container">
        <header>
            <h1>
                <span class="title-main">Farey Triangle & Cayley Transform</span>
            </h1>
            <p class="subtitle">Unlimited · Unbounded · Fully Customizable</p>
        </header>

        <!-- Visualization Canvases -->
        <div class="viz-grid">
            <div class="canvas-panel">
                <div class="panel-header">
                    <div>
                        <div class="panel-title">𝔻 Unit Disk</div>
                        <div class="panel-subtitle">Custom Farey Configuration</div>
                    </div>
                </div>
                <canvas id="diskCanvas" width="1000" height="1000"></canvas>
            </div>

            <div class="canvas-panel">
                <div class="panel-header">
                    <div>
                        <div class="panel-title">ℍ Upper Half-Plane</div>
                        <div class="panel-subtitle">Cayley Transform & Geodesics</div>
                    </div>
                </div>
                <canvas id="cayleyCanvas" width="1000" height="1000"></canvas>
            </div>

            <div class="canvas-panel">
                <div class="panel-header">
                    <div>
                        <div class="panel-title">⊚ Nested Modular Rings</div>
                        <div class="panel-subtitle">Unlimited GCD Structure</div>
                    </div>
                </div>
                <canvas id="nestedCanvas" width="1000" height="1000"></canvas>
            </div>
        </div>

        <!-- Advanced Controls -->
        <div class="controls-section">
            <div class="controls-header">
                Unlimited Parameter Control
            </div>
            <div class="controls-body">
                <!-- Basic Parameters -->
                <div class="section-header">🎯 Basic Parameters</div>
                <div class="control-row">
                    <div class="control-item">
                        <div class="control-label">
                            <span>Phase Rotation θ</span>
                            <span class="control-value" id="phaseValue">0°</span>
                        </div>
                        <input type="range" id="phaseSlider" min="0" max="360" value="0" step="0.1">
                    </div>

                    <div class="control-item">
                        <div class="control-label">
                            <span>Modulus m (Any Integer)</span>
                            <span class="control-value" id="modulusDisplay">30</span>
                        </div>
                        <input type="number" id="modulusInput" value="30" min="1" step="1">
                        <div class="help-text">No upper limit - enter any positive integer</div>
                    </div>

                    <div class="control-item">
                        <div class="control-label">
                            <span>Animation Speed</span>
                            <span class="control-value" id="speedValue">1.0×</span>
                        </div>
                        <input type="range" id="speedSlider" min="0.1" max="20" value="1" step="0.1">
                    </div>
                </div>

                <!-- Prime Distribution -->
                <div class="section-header">🔢 Prime Distribution</div>
                <div class="control-row">
                    <div class="control-item">
                        <div class="control-label">
                            <span>Number of Primes</span>
                            <span class="control-value" id="primesDisplay">150</span>
                        </div>
                        <input type="number" id="primesInput" value="150" min="0" step="10">
                        <div class="help-text">No limit - computation may slow with >5000</div>
                    </div>

                    <div class="control-item">
                        <div class="control-label">
                            <span>Prime Upper Limit</span>
                            <span class="control-value" id="primeLimitDisplay">10000</span>
                        </div>
                        <input type="number" id="primeLimitInput" value="10000" min="100" step="100">
                        <div class="help-text">Generate primes up to this value</div>
                    </div>
                </div>

                <!-- Nested Rings Parameters -->
                <div class="section-header">⊚ Nested Rings Configuration</div>
                <div class="control-row">
                    <div class="control-item">
                        <div class="control-label">
                            <span>Min Ring (m_start)</span>
                            <span class="control-value" id="minRingDisplay">1</span>
                        </div>
                        <input type="number" id="minRingInput" value="1" min="1" step="1">
                    </div>

                    <div class="control-item">
                        <div class="control-label">
                            <span>Max Ring (m_end)</span>
                            <span class="control-value" id="maxRingDisplay">12</span>
                        </div>
                        <input type="number" id="maxRingInput" value="12" min="1" step="1">
                        <div class="help-text">Unlimited - 100+ rings possible</div>
                    </div>

                    <div class="control-item">
                        <div class="control-label">
                            <span>Ring Spacing Factor</span>
                            <span class="control-value" id="spacingValue">1.0</span>
                        </div>
                        <input type="range" id="spacingSlider" min="0.1" max="5" value="1" step="0.1">
                    </div>
                </div>

                <!-- Custom Farey Points -->
                <div class="section-header">🎯 Custom Farey Points</div>
                <div class="control-row">
                    <div class="control-item" style="grid-column: 1 / -1;">
                        <div class="control-label">
                            <span>Farey Points (Fractions)</span>
                        </div>
                        <div id="fareyPointsList" class="farey-point-list"></div>
                        <button class="add-btn" onclick="addFareyPoint()">+ Add Farey Point</button>
                        <div class="help-text">Format: numerator/denominator (e.g., 1/3, 2/5, 3/7)</div>
                    </div>
                </div>

                <!-- Connection Options -->
                <div class="section-header">🔗 Connection Options</div>
                <div class="control-row">
                    <div class="control-item">
                        <div class="control-label">
                            <span>Connect Points By</span>
                        </div>
                        <select id="connectionMode">
                            <option value="none">No Connections</option>
                            <option value="farey">Farey Points Only</option>
                            <option value="mod">Same Modulus</option>
                            <option value="angle">Same Angle</option>
                            <option value="gcd">Same GCD</option>
                            <option value="fraction">Same Fraction Value</option>
                        </select>
                    </div>

                    <div class="control-item">
                        <div class="control-label">
                            <span>Connection Thickness</span>
                            <span class="control-value" id="connectionThicknessValue">1.0</span>
                        </div>
                        <input type="range" id="connectionThicknessSlider" min="0.1" max="10" value="1" step="0.1">
                    </div>

                    <div class="control-item">
                        <div class="control-label">
                            <span>Connection Opacity</span>
                            <span class="control-value" id="connectionOpacityValue">0.3</span>
                        </div>
                        <input type="range" id="connectionOpacitySlider" min="0" max="1" value="0.3" step="0.05">
                    </div>
                </div>

                <!-- Label Options -->
                <div class="section-header">🏷️ Label Options</div>
                <div class="control-row">
                    <div class="control-item">
                        <div class="control-label">
                            <span>Label Mode</span>
                        </div>
                        <select id="labelMode">
                            <option value="none">No Labels</option>
                            <option value="farey">Farey Points Only</option>
                            <option value="all">All Points</option>
                            <option value="coprime">Coprime Only</option>
                            <option value="rings">Ring Numbers Only</option>
                            <option value="everything">Everything</option>
                        </select>
                    </div>

                    <div class="control-item">
                        <div class="control-label">
                            <span>Label Size</span>
                            <span class="control-value" id="labelSizeValue">10</span>
                        </div>
                        <input type="range" id="labelSizeSlider" min="6" max="24" value="10" step="1">
                    </div>

                    <div class="control-item">
                        <div class="control-label">
                            <span>Label Every Nth Ring</span>
                            <span class="control-value" id="labelFreqValue">1</span>
                        </div>
                        <input type="number" id="labelFreqInput" value="1" min="1" step="1">
                    </div>
                </div>

                <!-- Toggles -->
                <div class="section-header">⚡ Display Options</div>
                <div class="toggle-grid">
                    <input type="checkbox" id="toggleFarey" checked>
                    <label for="toggleFarey" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Farey Triangle</span>
                    </label>

                    <input type="checkbox" id="toggleGeodesic" checked>
                    <label for="toggleGeodesic" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Geodesic Arc</span>
                    </label>

                    <input type="checkbox" id="togglePrimes" checked>
                    <label for="togglePrimes" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Prime Distribution</span>
                    </label>

                    <input type="checkbox" id="toggleChannels" checked>
                    <label for="toggleChannels" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Residue Channels</span>
                    </label>

                    <input type="checkbox" id="toggleCusps" checked>
                    <label for="toggleCusps" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Cusp Points</span>
                    </label>

                    <input type="checkbox" id="toggleRings" checked>
                    <label for="toggleRings" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Ring Circles</span>
                    </label>

                    <input type="checkbox" id="toggleGCD" checked>
                    <label for="toggleGCD" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">GCD Coloring</span>
                    </label>

                    <input type="checkbox" id="toggleGrid" checked>
                    <label for="toggleGrid" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Grid Lines</span>
                    </label>

                    <input type="checkbox" id="toggleAnimate">
                    <label for="toggleAnimate" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Auto-Rotate</span>
                    </label>
                </div>

                <!-- Action Buttons -->
                <div class="action-bar">
                    <button class="btn btn-primary" onclick="updateAll()">
                        <span>🔄 Update All</span>
                    </button>
                    <button class="btn btn-secondary" onclick="regeneratePrimes()">
                        <span>🔢 Regenerate Primes</span>
                    </button>
                    <button class="btn btn-accent" onclick="resetDefaults()">
                        <span>🔁 Reset to Defaults</span>
                    </button>
                    <button class="btn btn-secondary" onclick="exportVisualization()">
                        <span>💾 Export PNG</span>
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // ============================================================
        // GLOBAL STATE
        // ============================================================
        
        const CONFIG = {
            diskRadius: 0.38,
            halfPlaneScale: 0.22,
            colors: {
                disk: '#e74c3c',
                farey: '#ffd700',
                fareyFill: 'rgba(255, 215, 0, 0.08)',
                geodesic: '#1abc9c',
                cusp: '#e67e22',
                axes: 'rgba(255, 255, 255, 0.2)',
                grid: 'rgba(255, 255, 255, 0.05)',
                prime: '#3498db'
            }
        };

        let state = {
            phase: 0,
            modulus: 30,
            numPrimes: 150,
            primeLimit: 10000,
            animSpeed: 1.0,
            minRing: 1,
            maxRing: 12,
            ringSpacing: 1.0,
            connectionMode: 'none',
            connectionThickness: 1.0,
            connectionOpacity: 0.3,
            labelMode: 'farey',
            labelSize: 10,
            labelFreq: 1,
            fareyPoints: [
                {num: 1, den: 3},
                {num: 1, den: 2},
                {num: 2, den: 3}
            ],
            primes: [],
            animationId: null
        };

        let canvases = {
            disk: null,
            cayley: null,
            nested: null,
            diskCtx: null,
            cayleyCtx: null,
            nestedCtx: null
        };

        // ============================================================
        // INITIALIZATION
        // ============================================================

        window.addEventListener('load', () => {
            initStarfield();
            initCanvases();
            initFareyPointsUI();
            regeneratePrimes();
            setupEventListeners();
            updateAll();
            setTimeout(() => {
                document.getElementById('loading').classList.add('hidden');
            }, 800);
        });

        function initStarfield() {
            const starfield = document.getElementById('starfield');
            for (let i = 0; i < 50; i++) {
                const star = document.createElement('div');
                star.className = 'star';
                star.style.left = Math.random() * 100 + '%';
                star.style.top = Math.random() * 100 + '%';
                star.style.animationDelay = Math.random() * 4 + 's';
                starfield.appendChild(star);
            }
        }

        function initCanvases() {
            canvases.disk = document.getElementById('diskCanvas');
            canvases.cayley = document.getElementById('cayleyCanvas');
            canvases.nested = document.getElementById('nestedCanvas');
            
            const dpr = window.devicePixelRatio || 1;
            
            [canvases.disk, canvases.cayley, canvases.nested].forEach(canvas => {
                const rect = canvas.getBoundingClientRect();
                canvas.width = canvas.width * dpr;
                canvas.height = canvas.height * dpr;
                const ctx = canvas.getContext('2d');
                ctx.scale(dpr, dpr);
            });
            
            canvases.diskCtx = canvases.disk.getContext('2d');
            canvases.cayleyCtx = canvases.cayley.getContext('2d');
            canvases.nestedCtx = canvases.nested.getContext('2d');
        }

        function initFareyPointsUI() {
            updateFareyPointsList();
        }

        function updateFareyPointsList() {
            const list = document.getElementById('fareyPointsList');
            list.innerHTML = '';
            
            state.fareyPoints.forEach((point, index) => {
                const item = document.createElement('div');
                item.className = 'farey-point-item';
                
                const input = document.createElement('input');
                input.type = 'text';
                input.value = `${point.num}/${point.den}`;
                input.onchange = (e) => updateFareyPointValue(index, e.target.value);
                
                const removeBtn = document.createElement('button');
                removeBtn.className = 'remove-btn';
                removeBtn.textContent = '✕';
                removeBtn.onclick = () => removeFareyPoint(index);
                
                item.appendChild(input);
                item.appendChild(removeBtn);
                list.appendChild(item);
            });
        }

        function addFareyPoint() {
            state.fareyPoints.push({num: 1, den: 4});
            updateFareyPointsList();
            updateAll();
        }

        function removeFareyPoint(index) {
            if (state.fareyPoints.length > 1) {
                state.fareyPoints.splice(index, 1);
                updateFareyPointsList();
                updateAll();
            }
        }

        function updateFareyPointValue(index, value) {
            const parts = value.split('/');
            if (parts.length === 2) {
                const num = parseInt(parts[0]);
                const den = parseInt(parts[1]);
                if (!isNaN(num) && !isNaN(den) && den > 0) {
                    state.fareyPoints[index] = {num, den};
                    updateAll();
                }
            }
        }

        function setupEventListeners() {
            // Phase slider
            document.getElementById('phaseSlider').addEventListener('input', e => {
                state.phase = parseFloat(e.target.value);
                document.getElementById('phaseValue').textContent = state.phase.toFixed(1) + '°';
                if (!state.animationId) updateAll();
            });

            // Modulus input
            document.getElementById('modulusInput').addEventListener('change', e => {
                const val = parseInt(e.target.value);
                if (val > 0) {
                    state.modulus = val;
                    document.getElementById('modulusDisplay').textContent = val;
                    updateAll();
                }
            });

            // Prime inputs
            document.getElementById('primesInput').addEventListener('change', e => {
                const val = parseInt(e.target.value);
                if (val >= 0) {
                    state.numPrimes = val;
                    document.getElementById('primesDisplay').textContent = val;
                    updateAll();
                }
            });

            document.getElementById('primeLimitInput').addEventListener('change', e => {
                const val = parseInt(e.target.value);
                if (val >= 100) {
                    state.primeLimit = val;
                    document.getElementById('primeLimitDisplay').textContent = val;
                }
            });

            // Speed slider
            document.getElementById('speedSlider').addEventListener('input', e => {
                state.animSpeed = parseFloat(e.target.value);
                document.getElementById('speedValue').textContent = state.animSpeed.toFixed(1) + '×';
            });

            // Ring inputs
            document.getElementById('minRingInput').addEventListener('change', e => {
                const val = parseInt(e.target.value);
                if (val > 0) {
                    state.minRing = val;
                    document.getElementById('minRingDisplay').textContent = val;
                    updateAll();
                }
            });

            document.getElementById('maxRingInput').addEventListener('change', e => {
                const val = parseInt(e.target.value);
                if (val >= state.minRing) {
                    state.maxRing = val;
                    document.getElementById('maxRingDisplay').textContent = val;
                    updateAll();
                }
            });

            // Spacing slider
            document.getElementById('spacingSlider').addEventListener('input', e => {
                state.ringSpacing = parseFloat(e.target.value);
                document.getElementById('spacingValue').textContent = state.ringSpacing.toFixed(1);
                if (!state.animationId) updateAll();
            });

            // Connection controls
            document.getElementById('connectionMode').addEventListener('change', e => {
                state.connectionMode = e.target.value;
                updateAll();
            });

            document.getElementById('connectionThicknessSlider').addEventListener('input', e => {
                state.connectionThickness = parseFloat(e.target.value);
                document.getElementById('connectionThicknessValue').textContent = state.connectionThickness.toFixed(1);
                if (!state.animationId) updateAll();
            });

            document.getElementById('connectionOpacitySlider').addEventListener('input', e => {
                state.connectionOpacity = parseFloat(e.target.value);
                document.getElementById('connectionOpacityValue').textContent = state.connectionOpacity.toFixed(2);
                if (!state.animationId) updateAll();
            });

            // Label controls
            document.getElementById('labelMode').addEventListener('change', e => {
                state.labelMode = e.target.value;
                updateAll();
            });

            document.getElementById('labelSizeSlider').addEventListener('input', e => {
                state.labelSize = parseInt(e.target.value);
                document.getElementById('labelSizeValue').textContent = state.labelSize;
                if (!state.animationId) updateAll();
            });

            document.getElementById('labelFreqInput').addEventListener('change', e => {
                const val = parseInt(e.target.value);
                if (val > 0) {
                    state.labelFreq = val;
                    document.getElementById('labelFreqValue').textContent = val;
                    updateAll();
                }
            });

            // Animation toggle
            document.getElementById('toggleAnimate').addEventListener('change', e => {
                if (e.target.checked) {
                    startAnimation();
                } else {
                    stopAnimation();
                }
            });

            // Display toggles
            ['toggleFarey', 'toggleGeodesic', 'togglePrimes', 'toggleChannels', 
             'toggleCusps', 'toggleRings', 'toggleGCD', 'toggleGrid'].forEach(id => {
                document.getElementById(id).addEventListener('change', updateAll);
            });
        }

        // ============================================================
        // MATHEMATICAL FUNCTIONS
        // ============================================================

        function sieve(limit) {
            const isPrime = new Array(limit + 1).fill(true);
            isPrime[0] = isPrime[1] = false;
            
            for (let i = 2; i * i <= limit; i++) {
                if (isPrime[i]) {
                    for (let j = i * i; j <= limit; j += i) {
                        isPrime[j] = false;
                    }
                }
            }
            
            return isPrime.map((v, i) => v ? i : null).filter(v => v !== null);
        }

        function regeneratePrimes() {
            state.primes = sieve(state.primeLimit);
            updateAll();
        }

        function gcd(a, b) {
            a = Math.abs(a);
            b = Math.abs(b);
            while (b) [a, b] = [b, a % b];
            return a;
        }

        function eulerPhi(n) {
            let result = n;
            for (let p = 2; p * p <= n; p++) {
                if (n % p === 0) {
                    while (n % p === 0) n /= p;
                    result -= result / p;
                }
            }
            if (n > 1) result -= result / n;
            return Math.round(result);
        }

        function cayleyTransform(z) {
            const numRe = 1 + z.re;
            const numIm = z.im;
            const denRe = 1 - z.re;
            const denIm = -z.im;
            
            const denMagSq = denRe * denRe + denIm * denIm;
            
            if (denMagSq < 1e-10) {
                return { re: 0, im: 1e10 };
            }
            
            const quotRe = (numRe * denRe + numIm * denIm) / denMagSq;
            const quotIm = (numIm * denRe - numRe * denIm) / denMagSq;
            
            return { re: -quotIm, im: quotRe };
        }

        function generateColors(n) {
            const colors = [];
            const goldenRatio = 0.618033988749895;
            for (let i = 0; i < n; i++) {
                const hue = (i * goldenRatio * 360) % 360;
                colors.push(`hsla(${hue}, 85%, 65%, 0.9)`);
            }
            return colors;
        }

        function getGCDColor(g, m) {
            if (g === 1) return CONFIG.colors.farey;
            if (g === m) return '#e74c3c';
            if (g === 2) return '#00ffff';
            if (g === 3) return '#9b59b6';
            
            const hue = (g * 60) % 360;
            return `hsla(${hue}, 70%, 60%, 0.85)`;
        }

        // ============================================================
        // DRAWING FUNCTIONS
        // ============================================================

        function drawDisk() {
            const canvas = canvases.disk;
            const ctx = canvases.diskCtx;
            const w = canvas.width / (window.devicePixelRatio || 1);
            const h = canvas.height / (window.devicePixelRatio || 1);
            const cx = w / 2;
            const cy = h / 2;
            const r = Math.min(w, h) * CONFIG.diskRadius;

            ctx.clearRect(0, 0, w, h);

            // Grid
            if (document.getElementById('toggleGrid').checked) {
                ctx.strokeStyle = CONFIG.colors.grid;
                ctx.lineWidth = 0.5;
                for (let i = -10; i <= 10; i++) {
                    ctx.beginPath();
                    ctx.moveTo(cx + i * r / 5, 0);
                    ctx.lineTo(cx + i * r / 5, h);
                    ctx.stroke();
                    ctx.beginPath();
                    ctx.moveTo(0, cy + i * r / 5);
                    ctx.lineTo(w, cy + i * r / 5);
                    ctx.stroke();
                }
            }

            // Axes
            ctx.strokeStyle = CONFIG.colors.axes;
            ctx.lineWidth = 1.5;
            ctx.beginPath();
            ctx.moveTo(0, cy);
            ctx.lineTo(w, cy);
            ctx.moveTo(cx, 0);
            ctx.lineTo(cx, h);
            ctx.stroke();

            // Unit circle
            ctx.strokeStyle = CONFIG.colors.disk;
            ctx.lineWidth = 3;
            ctx.shadowBlur = 15;
            ctx.shadowColor = CONFIG.colors.disk;
            ctx.beginPath();
            ctx.arc(cx, cy, r, 0, 2 * Math.PI);
            ctx.stroke();
            ctx.shadowBlur = 0;

            const phase = state.phase * Math.PI / 180;
            const showPrimes = document.getElementById('togglePrimes').checked;
            const showChannels = document.getElementById('toggleChannels').checked;
            const showFarey = document.getElementById('toggleFarey').checked;

            // Primes
            if (showPrimes) {
                const colors = generateColors(state.modulus);
                const displayPrimes = state.primes.slice(0, state.numPrimes);

                displayPrimes.forEach(p => {
                    if (showChannels && gcd(p, state.modulus) !== 1) return;

                    const angle = 2 * Math.PI * p / state.modulus + phase;
                    const x = cx + r * Math.cos(angle);
                    const y = cy + r * Math.sin(angle);

                    const color = showChannels ? colors[p % state.modulus] : CONFIG.colors.prime;
                    
                    ctx.fillStyle = color;
                    ctx.shadowBlur = 8;
                    ctx.shadowColor = color;
                    ctx.beginPath();
                    ctx.arc(x, y, 3.5, 0, 2 * Math.PI);
                    ctx.fill();
                    ctx.shadowBlur = 0;
                });
            }

            // Farey triangle
            if (showFarey && state.fareyPoints.length >= 2) {
                const fareyPoints = state.fareyPoints.map(fp => {
                    const frac = fp.num / fp.den;
                    const angle = 2 * Math.PI * frac + phase;
                    return {
                        x: cx + r * Math.cos(angle),
                        y: cy + r * Math.sin(angle),
                        frac: frac,
                        label: `${fp.num}/${fp.den}`
                    };
                });

                // Fill
                if (fareyPoints.length >= 3) {
                    ctx.fillStyle = CONFIG.colors.fareyFill;
                    ctx.beginPath();
                    ctx.moveTo(fareyPoints[0].x, fareyPoints[0].y);
                    for (let i = 1; i < fareyPoints.length; i++) {
                        ctx.lineTo(fareyPoints[i].x, fareyPoints[i].y);
                    }
                    ctx.closePath();
                    ctx.fill();
                }

                // Edges
                ctx.strokeStyle = CONFIG.colors.farey;
                ctx.lineWidth = 2.5;
                ctx.shadowBlur = 15;
                ctx.shadowColor = CONFIG.colors.farey;
                ctx.beginPath();
                ctx.moveTo(fareyPoints[0].x, fareyPoints[0].y);
                for (let i = 1; i < fareyPoints.length; i++) {
                    ctx.lineTo(fareyPoints[i].x, fareyPoints[i].y);
                }
                if (fareyPoints.length >= 3) ctx.closePath();
                ctx.stroke();
                ctx.shadowBlur = 0;

                // Vertices
                fareyPoints.forEach(p => {
                    ctx.fillStyle = CONFIG.colors.farey;
                    ctx.strokeStyle = 'rgba(0, 0, 0, 0.8)';
                    ctx.lineWidth = 2;
                    ctx.shadowBlur = 20;
                    ctx.shadowColor = CONFIG.colors.farey;
                    ctx.beginPath();
                    ctx.arc(p.x, p.y, 8, 0, 2 * Math.PI);
                    ctx.fill();
                    ctx.stroke();
                    ctx.shadowBlur = 0;

                    // Labels
                    if (state.labelMode !== 'none') {
                        const angle = 2 * Math.PI * p.frac + phase;
                        const labelR = r + 35;
                        const lx = cx + labelR * Math.cos(angle);
                        const ly = cy + labelR * Math.sin(angle);

                        ctx.fillStyle = CONFIG.colors.farey;
                        ctx.font = `bold ${state.labelSize + 6}px "Fira Code"`;
                        ctx.textAlign = 'center';
                        ctx.shadowBlur = 10;
                        ctx.shadowColor = 'rgba(0, 0, 0, 0.8)';
                        ctx.fillText(p.label, lx, ly);
                        ctx.shadowBlur = 0;
                    }
                });
            }

            // Title
            ctx.fillStyle = 'rgba(255, 255, 255, 0.9)';
            ctx.font = 'bold 20px "Fira Code"';
            ctx.textAlign = 'center';
            ctx.shadowBlur = 10;
            ctx.shadowColor = 'rgba(0, 0, 0, 0.8)';
            ctx.fillText('Unit Disk 𝔻', cx, 35);
            ctx.shadowBlur = 0;
        }

        function drawCayley() {
            const canvas = canvases.cayley;
            const ctx = canvases.cayleyCtx;
            const w = canvas.width / (window.devicePixelRatio || 1);
            const h = canvas.height / (window.devicePixelRatio || 1);
            const cx = w / 2;
            const cy = h * 0.7;
            const scale = Math.min(w, h) * CONFIG.halfPlaneScale;

            ctx.clearRect(0, 0, w, h);

            // Grid
            if (document.getElementById('toggleGrid').checked) {
                ctx.strokeStyle = CONFIG.colors.grid;
                ctx.lineWidth = 0.5;
                for (let i = -15; i <= 15; i++) {
                    ctx.beginPath();
                    ctx.moveTo(cx + i * scale / 3, 0);
                    ctx.lineTo(cx + i * scale / 3, h);
                    ctx.stroke();
                    const y = cy - i * scale / 3;
                    if (y >= 0 && y <= h) {
                        ctx.beginPath();
                        ctx.moveTo(0, y);
                        ctx.lineTo(w, y);
                        ctx.stroke();
                    }
                }
            }

            // Axes
            ctx.strokeStyle = CONFIG.colors.axes;
            ctx.lineWidth = 2;
            ctx.shadowBlur = 10;
            ctx.shadowColor = CONFIG.colors.axes;
            ctx.beginPath();
            ctx.moveTo(0, cy);
            ctx.lineTo(w, cy);
            ctx.stroke();
            ctx.shadowBlur = 0;

            ctx.strokeStyle = CONFIG.colors.axes;
            ctx.lineWidth = 1.5;
            ctx.beginPath();
            ctx.moveTo(cx, 0);
            ctx.lineTo(cx, h);
            ctx.stroke();

            const phase = state.phase * Math.PI / 180;
            const showGeodesic = document.getElementById('toggleGeodesic').checked;
            const showCusps = document.getElementById('toggleCusps').checked;
            const showPrimes = document.getElementById('togglePrimes').checked;
            const showChannels = document.getElementById('toggleChannels').checked;
            const showFarey = document.getElementById('toggleFarey').checked;

            // Geodesic
            if (showGeodesic && state.fareyPoints.length >= 2) {
                // Calculate geodesic for custom Farey points
                const transformedPoints = state.fareyPoints.map(fp => {
                    const frac = fp.num / fp.den;
                    const angle = 2 * Math.PI * frac + phase;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const w = cayleyTransform(z);
                    return { x: cx + w.re * scale, y: cy - w.im * scale, w: w };
                });

                // Calculate geodesic circle parameters
                if (transformedPoints.length >= 2) {
                    const p1 = transformedPoints[0];
                    const p2 = transformedPoints[1];
                    const midX = (p1.x + p2.x) / 2;
                    const geodR = Math.sqrt((p1.x - p2.x) ** 2 / 4 + (cy - midX) ** 2);

                    ctx.strokeStyle = CONFIG.colors.geodesic;
                    ctx.lineWidth = 4;
                    ctx.shadowBlur = 20;
                    ctx.shadowColor = CONFIG.colors.geodesic;
                    ctx.beginPath();
                    ctx.arc(midX, cy, geodR, Math.PI, 0, false);
                    ctx.stroke();
                    ctx.shadowBlur = 0;
                }
            }

            // Cusps
            if (showCusps && state.fareyPoints.length > 0) {
                state.fareyPoints.forEach(fp => {
                    const frac = fp.num / fp.den;
                    const angle = 2 * Math.PI * frac + phase;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const w = cayleyTransform(z);
                    const x = cx + w.re * scale;

                    ctx.fillStyle = CONFIG.colors.cusp;
                    ctx.shadowBlur = 15;
                    ctx.shadowColor = CONFIG.colors.cusp;
                    ctx.beginPath();
                    ctx.arc(x, cy, 6, 0, 2 * Math.PI);
                    ctx.fill();
                    ctx.shadowBlur = 0;

                    if (state.labelMode !== 'none') {
                        ctx.fillStyle = CONFIG.colors.cusp;
                        ctx.font = `${state.labelSize + 3}px "Fira Code"`;
                        ctx.textAlign = 'center';
                        ctx.shadowBlur = 8;
                        ctx.shadowColor = 'rgba(0, 0, 0, 0.8)';
                        ctx.fillText(`${fp.num}/${fp.den}`, x, cy + 22);
                        ctx.shadowBlur = 0;
                    }
                });
            }

            // Transformed primes
            if (showPrimes) {
                const colors = generateColors(state.modulus);
                const displayPrimes = state.primes.slice(0, state.numPrimes);

                displayPrimes.forEach(p => {
                    if (showChannels && gcd(p, state.modulus) !== 1) return;

                    const angle = 2 * Math.PI * p / state.modulus + phase;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const w = cayleyTransform(z);

                    if (Math.abs(w.im) < 15 && Math.abs(w.re) < 15 && w.im > 0.01) {
                        const x = cx + w.re * scale;
                        const y = cy - w.im * scale;

                        const color = showChannels ? colors[p % state.modulus] : CONFIG.colors.prime;
                        
                        ctx.fillStyle = color;
                        ctx.shadowBlur = 8;
                        ctx.shadowColor = color;
                        ctx.beginPath();
                        ctx.arc(x, y, 3.5, 0, 2 * Math.PI);
                        ctx.fill();
                        ctx.shadowBlur = 0;
                    }
                });
            }

            // Farey triangle (transformed)
            if (showFarey && state.fareyPoints.length >= 2) {
                const transformedPoints = state.fareyPoints.map(fp => {
                    const frac = fp.num / fp.den;
                    const angle = 2 * Math.PI * frac + phase;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const w = cayleyTransform(z);
                    return {
                        x: cx + w.re * scale,
                        y: cy - w.im * scale,
                        w: w,
                        label: `${fp.num}/${fp.den}`
                    };
                });

                // Edges
                ctx.strokeStyle = CONFIG.colors.farey;
                ctx.lineWidth = 2;
                ctx.setLineDash([8, 4]);
                ctx.shadowBlur = 10;
                ctx.shadowColor = CONFIG.colors.farey;
                ctx.beginPath();
                ctx.moveTo(transformedPoints[0].x, transformedPoints[0].y);
                for (let i = 1; i < transformedPoints.length; i++) {
                    ctx.lineTo(transformedPoints[i].x, transformedPoints[i].y);
                }
                if (transformedPoints.length >= 3) ctx.closePath();
                ctx.stroke();
                ctx.setLineDash([]);
                ctx.shadowBlur = 0;

                // Vertices
                transformedPoints.forEach(p => {
                    ctx.fillStyle = CONFIG.colors.farey;
                    ctx.strokeStyle = 'rgba(0, 0, 0, 0.8)';
                    ctx.lineWidth = 2;
                    ctx.shadowBlur = 20;
                    ctx.shadowColor = CONFIG.colors.farey;
                    ctx.beginPath();
                    ctx.arc(p.x, p.y, 8, 0, 2 * Math.PI);
                    ctx.fill();
                    ctx.stroke();
                    ctx.shadowBlur = 0;

                    if (state.labelMode !== 'none') {
                        ctx.fillStyle = CONFIG.colors.farey;
                        ctx.font = `bold ${state.labelSize + 3}px "Fira Code"`;
                        ctx.textAlign = 'center';
                        ctx.shadowBlur = 8;
                        ctx.shadowColor = 'rgba(0, 0, 0, 0.8)';
                        ctx.fillText(p.label, p.x, p.y - 20);
                        ctx.shadowBlur = 0;
                    }
                });
            }

            // Title
            ctx.fillStyle = 'rgba(255, 255, 255, 0.9)';
            ctx.font = 'bold 20px "Fira Code"';
            ctx.textAlign = 'center';
            ctx.shadowBlur = 10;
            ctx.shadowColor = 'rgba(0, 0, 0, 0.8)';
            ctx.fillText('Upper Half-Plane ℍ', cx, 35);
            ctx.shadowBlur = 0;
        }

        function drawNested() {
            const canvas = canvases.nested;
            const ctx = canvases.nestedCtx;
            const w = canvas.width / (window.devicePixelRatio || 1);
            const h = canvas.height / (window.devicePixelRatio || 1);
            const cx = w / 2;
            const cy = h / 2;
            const maxRadius = Math.min(w, h) * 0.42;
            const baseRadius = maxRadius * 0.15;

            ctx.clearRect(0, 0, w, h);

            // Grid
            if (document.getElementById('toggleGrid').checked) {
                ctx.strokeStyle = CONFIG.colors.grid;
                ctx.lineWidth = 0.5;
                for (let i = -10; i <= 10; i++) {
                    ctx.beginPath();
                    ctx.moveTo(cx + i * maxRadius / 5, 0);
                    ctx.lineTo(cx + i * maxRadius / 5, h);
                    ctx.stroke();
                    ctx.beginPath();
                    ctx.moveTo(0, cy + i * maxRadius / 5);
                    ctx.lineTo(w, cy + i * maxRadius / 5);
                    ctx.stroke();
                }
            }

            // Axes
            ctx.strokeStyle = CONFIG.colors.axes;
            ctx.lineWidth = 1;
            ctx.beginPath();
            ctx.moveTo(0, cy);
            ctx.lineTo(w, cy);
            ctx.moveTo(cx, 0);
            ctx.lineTo(cx, h);
            ctx.stroke();

            const phase = state.phase * Math.PI / 180;
            const showRings = document.getElementById('toggleRings').checked;
            const showGCD = document.getElementById('toggleGCD').checked;

            const allPoints = [];
            const numRings = state.maxRing - state.minRing + 1;

            for (let m = state.minRing; m <= state.maxRing; m++) {
                const ringIndex = m - state.minRing;
                const ringRadius = baseRadius + ringIndex * (maxRadius - baseRadius) / Math.max(1, numRings - 1) * state.ringSpacing;

                // Ring circle
                if (showRings) {
                    ctx.strokeStyle = `rgba(255, 255, 255, 0.15)`;
                    ctx.lineWidth = 1;
                    ctx.beginPath();
                    ctx.arc(cx, cy, ringRadius, 0, 2 * Math.PI);
                    ctx.stroke();

                    // Ring label
                    if ((state.labelMode === 'rings' || state.labelMode === 'everything') && m % state.labelFreq === 0) {
                        ctx.fillStyle = 'rgba(255, 255, 255, 0.5)';
                        ctx.font = `${state.labelSize}px "Fira Code"`;
                        ctx.textAlign = 'left';
                        ctx.fillText(`m=${m}`, cx + ringRadius + 10, cy);
                    }
                }

                // Points for each k
                for (let k = 0; k < m; k++) {
                    const g = gcd(k, m);
                    const angle = 2 * Math.PI * k / m + phase;
                    const x = cx + ringRadius * Math.cos(angle);
                    const y = cy + ringRadius * Math.sin(angle);

                    allPoints.push({ x, y, k, m, g, angle, radius: ringRadius, frac: k/m });

                    // Draw point
                    if (showGCD) {
                        const color = getGCDColor(g, m);
                        const size = g === 1 ? 4 : 3;
                        
                        ctx.fillStyle = color;
                        ctx.shadowBlur = g === 1 ? 8 : 4;
                        ctx.shadowColor = color;
                        ctx.beginPath();
                        ctx.arc(x, y, size, 0, 2 * Math.PI);
                        ctx.fill();
                        ctx.shadowBlur = 0;
                    }

                    // Labels
                    const shouldLabel = 
                        (state.labelMode === 'all') ||
                        (state.labelMode === 'coprime' && g === 1) ||
                        (state.labelMode === 'everything');

                    if (shouldLabel && m % state.labelFreq === 0) {
                        ctx.fillStyle = g === 1 ? CONFIG.colors.farey : 'rgba(255, 255, 255, 0.6)';
                        ctx.font = `${state.labelSize - 2}px "Fira Code"`;
                        ctx.textAlign = 'center';
                        ctx.fillText(`${k}/${m}`, x, y + 15);
                    }
                }
            }

            // Connections
            if (state.connectionMode !== 'none') {
                ctx.globalAlpha = state.connectionOpacity;
                ctx.lineWidth = state.connectionThickness;

                switch (state.connectionMode) {
                    case 'farey':
                        // Connect all Farey points (gcd=1)
                        const fareyPts = allPoints.filter(p => p.g === 1);
                        ctx.strokeStyle = CONFIG.colors.farey;
                        for (let i = 0; i < fareyPts.length - 1; i++) {
                            for (let j = i + 1; j < fareyPts.length; j++) {
                                ctx.beginPath();
                                ctx.moveTo(fareyPts[i].x, fareyPts[i].y);
                                ctx.lineTo(fareyPts[j].x, fareyPts[j].y);
                                ctx.stroke();
                            }
                        }
                        break;

                    case 'mod':
                        // Connect points on same ring
                        for (let m = state.minRing; m <= state.maxRing; m++) {
                            const ringPts = allPoints.filter(p => p.m === m);
                            ctx.strokeStyle = getGCDColor(2, m);
                            for (let i = 0; i < ringPts.length; i++) {
                                const next = ringPts[(i + 1) % ringPts.length];
                                ctx.beginPath();
                                ctx.moveTo(ringPts[i].x, ringPts[i].y);
                                ctx.lineTo(next.x, next.y);
                                ctx.stroke();
                            }
                        }
                        break;

                    case 'angle':
                        // Connect points with similar angles
                        const angleGroups = {};
                        allPoints.forEach(p => {
                            const key = Math.floor(p.angle * 100);
                            if (!angleGroups[key]) angleGroups[key] = [];
                            angleGroups[key].push(p);
                        });

                        ctx.strokeStyle = CONFIG.colors.cyan;
                        Object.values(angleGroups).forEach(group => {
                            if (group.length >= 2) {
                                group.sort((a, b) => a.radius - b.radius);
                                for (let i = 0; i < group.length - 1; i++) {
                                    ctx.beginPath();
                                    ctx.moveTo(group[i].x, group[i].y);
                                    ctx.lineTo(group[i + 1].x, group[i + 1].y);
                                    ctx.stroke();
                                }
                            }
                        });
                        break;

                    case 'gcd':
                        // Connect points with same GCD
                        const gcdGroups = {};
                        allPoints.forEach(p => {
                            if (!gcdGroups[p.g]) gcdGroups[p.g] = [];
                            gcdGroups[p.g].push(p);
                        });

                        Object.entries(gcdGroups).forEach(([g, group]) => {
                            ctx.strokeStyle = getGCDColor(parseInt(g), state.maxRing);
                            for (let i = 0; i < group.length - 1; i++) {
                                ctx.beginPath();
                                ctx.moveTo(group[i].x, group[i].y);
                                ctx.lineTo(group[i + 1].x, group[i + 1].y);
                                ctx.stroke();
                            }
                        });
                        break;

                    case 'fraction':
                        // Connect points with same fraction value
                        const fracGroups = {};
                        allPoints.forEach(p => {
                            const key = Math.floor(p.frac * 10000);
                            if (!fracGroups[key]) fracGroups[key] = [];
                            fracGroups[key].push(p);
                        });

                        ctx.strokeStyle = CONFIG.colors.geodesic;
                        Object.values(fracGroups).forEach(group => {
                            if (group.length >= 2) {
                                for (let i = 0; i < group.length - 1; i++) {
                                    ctx.beginPath();
                                    ctx.moveTo(group[i].x, group[i].y);
                                    ctx.lineTo(group[i + 1].x, group[i + 1].y);
                                    ctx.stroke();
                                }
                            }
                        });
                        break;
                }

                ctx.globalAlpha = 1.0;
            }

            // Highlight custom Farey points if they exist in range
            state.fareyPoints.forEach(fp => {
                if (fp.den >= state.minRing && fp.den <= state.maxRing) {
                    const m = fp.den;
                    const k = fp.num % m;
                    const ringIndex = m - state.minRing;
                    const ringRadius = baseRadius + ringIndex * (maxRadius - baseRadius) / Math.max(1, numRings - 1) * state.ringSpacing;
                    const angle = 2 * Math.PI * k / m + phase;
                    const x = cx + ringRadius * Math.cos(angle);
                    const y = cy + ringRadius * Math.sin(angle);

                    ctx.strokeStyle = '#ff6b6b';
                    ctx.fillStyle = 'rgba(255, 107, 107, 0.3)';
                    ctx.lineWidth = 3;
                    ctx.shadowBlur = 20;
                    ctx.shadowColor = '#ff6b6b';
                    ctx.beginPath();
                    ctx.arc(x, y, 10, 0, 2 * Math.PI);
                    ctx.fill();
                    ctx.stroke();
                    ctx.shadowBlur = 0;

                    if (state.labelMode !== 'none') {
                        ctx.fillStyle = '#ff6b6b';
                        ctx.font = `bold ${state.labelSize + 2}px "Fira Code"`;
                        ctx.textAlign = 'center';
                        ctx.shadowBlur = 8;
                        ctx.shadowColor = 'rgba(0, 0, 0, 0.9)';
                        ctx.fillText(`${fp.num}/${fp.den}`, x, y - 18);
                        ctx.shadowBlur = 0;
                    }
                }
            });

            // Title
            ctx.fillStyle = 'rgba(255, 255, 255, 0.9)';
            ctx.font = 'bold 20px "Fira Code"';
            ctx.textAlign = 'center';
            ctx.shadowBlur = 10;
            ctx.shadowColor = 'rgba(0, 0, 0, 0.8)';
            ctx.fillText('Nested Modular Rings', cx, 35);
            ctx.font = '12px "Fira Code"';
            ctx.fillStyle = 'rgba(255, 255, 255, 0.7)';
            ctx.fillText(`m = ${state.minRing} to ${state.maxRing}`, cx, 55);
            ctx.shadowBlur = 0;
        }

        function updateAll() {
            drawDisk();
            drawCayley();
            drawNested();
        }

        // ============================================================
        // ANIMATION
        // ============================================================

        function startAnimation() {
            if (state.animationId !== null) return;

            function animate() {
                state.phase = (state.phase + state.animSpeed * 0.5) % 360;
                document.getElementById('phaseSlider').value = state.phase;
                document.getElementById('phaseValue').textContent = state.phase.toFixed(1) + '°';
                updateAll();
                state.animationId = requestAnimationFrame(animate);
            }

            animate();
        }

        function stopAnimation() {
            if (state.animationId !== null) {
                cancelAnimationFrame(state.animationId);
                state.animationId = null;
            }
        }

        // ============================================================
        // UI CONTROLS
        // ============================================================

        function resetDefaults() {
            state = {
                phase: 0,
                modulus: 30,
                numPrimes: 150,
                primeLimit: 10000,
                animSpeed: 1.0,
                minRing: 1,
                maxRing: 12,
                ringSpacing: 1.0,
                connectionMode: 'none',
                connectionThickness: 1.0,
                connectionOpacity: 0.3,
                labelMode: 'farey',
                labelSize: 10,
                labelFreq: 1,
                fareyPoints: [
                    {num: 1, den: 3},
                    {num: 1, den: 2},
                    {num: 2, den: 3}
                ],
                primes: state.primes,
                animationId: null
            };

            // Reset UI
            document.getElementById('phaseSlider').value = 0;
            document.getElementById('modulusInput').value = 30;
            document.getElementById('primesInput').value = 150;
            document.getElementById('primeLimitInput').value = 10000;
            document.getElementById('speedSlider').value = 1;
            document.getElementById('minRingInput').value = 1;
            document.getElementById('maxRingInput').value = 12;
            document.getElementById('spacingSlider').value = 1;
            document.getElementById('connectionMode').value = 'none';
            document.getElementById('connectionThicknessSlider').value = 1;
            document.getElementById('connectionOpacitySlider').value = 0.3;
            document.getElementById('labelMode').value = 'farey';
            document.getElementById('labelSizeSlider').value = 10;
            document.getElementById('labelFreqInput').value = 1;
            document.getElementById('toggleAnimate').checked = false;

            document.getElementById('phaseValue').textContent = '0°';
            document.getElementById('modulusDisplay').textContent = '30';
            document.getElementById('primesDisplay').textContent = '150';
            document.getElementById('primeLimitDisplay').textContent = '10000';
            document.getElementById('speedValue').textContent = '1.0×';
            document.getElementById('minRingDisplay').textContent = '1';
            document.getElementById('maxRingDisplay').textContent = '12';
            document.getElementById('spacingValue').textContent = '1.0';
            document.getElementById('connectionThicknessValue').textContent = '1.0';
            document.getElementById('connectionOpacityValue').textContent = '0.30';
            document.getElementById('labelSizeValue').textContent = '10';
            document.getElementById('labelFreqValue').textContent = '1';

            stopAnimation();
            updateFareyPointsList();
            updateAll();
        }

        function exportVisualization() {
            const tempCanvas = document.createElement('canvas');
            const dpr = window.devicePixelRatio || 1;
            const w1 = canvases.disk.width / dpr;
            const h1 = canvases.disk.height / dpr;
            
            tempCanvas.width = w1 * 3;
            tempCanvas.height = h1;
            
            const tempCtx = tempCanvas.getContext('2d');
            
            tempCtx.fillStyle = '#0a0e27';
            tempCtx.fillRect(0, 0, tempCanvas.width, tempCanvas.height);
            
            tempCtx.drawImage(canvases.disk, 0, 0, w1, h1);
            tempCtx.drawImage(canvases.cayley, w1, 0, w1, h1);
            tempCtx.drawImage(canvases.nested, w1 * 2, 0, w1, h1);
            
            const link = document.createElement('a');
            link.download = `farey-unlimited-${Date.now()}.png`;
            link.href = tempCanvas.toDataURL('image/png');
            link.click();
        }
    </script>
</body>
</html>
