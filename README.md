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

        .control-item:hover::after {
            content: attr(data-tooltip);
            position: absolute;
            bottom: 100%;
            left: 50%;
            transform: translateX(-50%);
            padding: 8px 12px;
            background: rgba(0, 0, 0, 0.95);
            color: var(--gold);
            font-size: 0.85em;
            border: 1px solid var(--gold);
            border-radius: 4px;
            white-space: normal;
            width: max-content;
            max-width: 300px;
            z-index: 1000;
            pointer-events: none;
            margin-bottom: 5px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.5);
        }

        .toggle-item:hover::after {
            content: attr(data-tooltip);
            position: absolute;
            bottom: 100%;
            left: 50%;
            transform: translateX(-50%);
            padding: 8px 12px;
            background: rgba(0, 0, 0, 0.95);
            color: var(--cyan);
            font-size: 0.85em;
            border: 1px solid var(--cyan);
            border-radius: 4px;
            white-space: normal;
            width: max-content;
            max-width: 300px;
            z-index: 1000;
            pointer-events: none;
            margin-bottom: 5px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.5);
        }

        .control-item, .toggle-item {
            position: relative;
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

        .export-dialog {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.85);
            z-index: 10000;
            display: flex;
            justify-content: center;
            align-items: center;
            backdrop-filter: blur(5px);
        }

        .export-dialog-content {
            background: linear-gradient(135deg, var(--bg-light) 0%, var(--bg-mid) 100%);
            border: 2px solid var(--gold);
            border-radius: 8px;
            width: 90%;
            max-width: 600px;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.8);
        }

        .export-dialog-header {
            background: linear-gradient(90deg, rgba(255, 215, 0, 0.2), rgba(0, 255, 255, 0.2));
            padding: 20px;
            border-bottom: 1px solid var(--border);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .export-dialog-header h3 {
            font-family: 'Fira Code', monospace;
            color: var(--gold);
            font-size: 1.4em;
            margin: 0;
        }

        .close-btn {
            background: none;
            border: none;
            color: var(--text);
            font-size: 1.5em;
            cursor: pointer;
            padding: 0;
            width: 30px;
            height: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 4px;
            transition: all 0.3s;
        }

        .close-btn:hover {
            background: rgba(255, 255, 255, 0.1);
            color: var(--gold);
        }

        .export-dialog-body {
            padding: 30px;
        }

        .export-section {
            margin-bottom: 25px;
        }

        .export-section h4 {
            font-family: 'Fira Code', monospace;
            color: var(--cyan);
            font-size: 1.1em;
            margin-bottom: 15px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .export-radio-group {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .export-radio {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px;
            background: rgba(0, 0, 0, 0.3);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.3s;
            font-family: 'Fira Code', monospace;
        }

        .export-radio:hover {
            border-color: var(--gold);
            background: rgba(255, 215, 0, 0.05);
        }

        .export-radio input[type="radio"] {
            width: 18px;
            height: 18px;
            cursor: pointer;
        }

        .export-radio input[type="radio"]:checked + span {
            color: var(--gold);
            font-weight: 600;
        }

        .export-checkbox {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px;
            background: rgba(0, 0, 0, 0.3);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.3s;
            font-family: 'Fira Code', monospace;
        }

        .export-checkbox:hover {
            border-color: var(--cyan);
            background: rgba(0, 255, 255, 0.05);
        }

        .export-checkbox input[type="checkbox"] {
            width: 18px;
            height: 18px;
            cursor: pointer;
        }

        .legend-container {
            position: absolute;
            background: rgba(10, 14, 39, 0.95);
            border: 2px solid var(--gold);
            border-radius: 8px;
            padding: 20px;
            font-family: 'Fira Code', monospace;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.8);
        }

        .legend-title {
            font-size: 1.2em;
            color: var(--gold);
            margin-bottom: 15px;
            text-transform: uppercase;
            letter-spacing: 2px;
            border-bottom: 2px solid var(--gold);
            padding-bottom: 8px;
        }

        .legend-item {
            display: flex;
            align-items: center;
            gap: 12px;
            margin-bottom: 10px;
            font-size: 0.9em;
        }

        .legend-symbol {
            width: 30px;
            height: 20px;
            border-radius: 3px;
            border: 1px solid rgba(255, 255, 255, 0.3);
        }

        .legend-text {
            color: var(--text);
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
            <p class="subtitle">Hyperbolic Geometry · Number Theory · Modular Forms</p>
        </header>

        <!-- Introduction Panel -->
        <div class="controls-section" style="margin-bottom: 20px;">
            <div class="controls-header" style="cursor: pointer; user-select: none;" onclick="toggleIntro()">
                <span id="introToggle">▼</span> Mathematical Introduction
            </div>
            <div class="controls-body" id="introPanel" style="display: block;">
                <div style="line-height: 1.8; font-size: 0.95em;">
                    <h3 style="color: var(--gold); margin-bottom: 15px;">What You're Seeing</h3>
                    
                    <p style="margin-bottom: 15px;">This visualization explores the deep connection between <strong>number theory</strong> and <strong>hyperbolic geometry</strong> through three perspectives:</p>
                    
                    <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; margin: 20px 0;">
                        <div style="background: rgba(0,0,0,0.3); padding: 15px; border-left: 3px solid var(--gold);">
                            <h4 style="color: var(--gold); margin-bottom: 8px;">Unit Disk (𝔻)</h4>
                            <p style="font-size: 0.9em;">The <strong>Farey sequence</strong> points (rational numbers p/q in lowest terms) are placed on the unit circle. Prime numbers are distributed according to their residue classes modulo <em>m</em>.</p>
                        </div>
                        
                        <div style="background: rgba(0,0,0,0.3); padding: 15px; border-left: 3px solid var(--cyan);">
                            <h4 style="color: var(--cyan); margin-bottom: 8px;">Upper Half-Plane (ℍ)</h4>
                            <p style="font-size: 0.9em;">The <strong>Cayley transform</strong> w = i(1-z)/(1+z) maps the disk to the half-plane, revealing geodesics as semicircles orthogonal to the real axis. The modular group PSL(2,ℤ) acts here naturally.</p>
                        </div>
                        
                        <div style="background: rgba(0,0,0,0.3); padding: 15px; border-left: 3px solid var(--geodesic);">
                            <h4 style="color: var(--geodesic); margin-bottom: 8px;">Nested Rings</h4>
                            <p style="font-size: 0.9em;">Each ring represents residues mod <em>m</em>. Points at angle 2πk/m are colored by their <strong>GCD(k,m)</strong>, revealing the structure of the multiplicative group (ℤ/mℤ)×.</p>
                        </div>
                    </div>
                    
                    <h3 style="color: var(--gold); margin: 25px 0 15px;">Key Mathematical Concepts</h3>
                    
                    <ul style="list-style: none; padding: 0;">
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Farey Sequence F_n:</strong> All reduced fractions p/q with 0 ≤ p ≤ q ≤ n. Adjacent fractions satisfy the mediant property: if p/q and r/s are neighbors, then |ps - qr| = 1.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Cayley Transform:</strong> The map w = i(1-z)/(1+z) is a Möbius transformation sending the unit disk 𝔻 = {z : |z| < 1} to the upper half-plane ℍ = {w : Im(w) > 0}.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Modular Group PSL(2,ℤ):</strong> Möbius transformations z → (az+b)/(cz+d) with integer coefficients and ad-bc=1. Acts on ℍ preserving hyperbolic geometry.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Geodesics in ℍ:</strong> Hyperbolic straight lines appear as semicircles perpendicular to the real axis (or vertical lines).
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Prime Distribution mod m:</strong> Primes p are placed at angle 2πp/m on the unit circle, visualizing their distribution among residue classes coprime to m (Euler's φ function).
                        </li>
                    </ul>
                    
                    <div style="background: rgba(255, 215, 0, 0.1); padding: 15px; margin-top: 20px; border-radius: 4px;">
                        <strong style="color: var(--gold);">Quick Start:</strong> Try the preset buttons below, hover over controls for tooltips, or explore the examples section for interesting configurations!
                    </div>
                </div>
            </div>
        </div>

        <!-- Presets Bar -->
        <div class="controls-section" style="margin-bottom: 20px;">
            <div class="controls-header">
                Quick Presets
            </div>
            <div class="controls-body">
                <div style="display: flex; gap: 15px; flex-wrap: wrap;">
                    <button class="btn btn-primary" onclick="applyPreset('minimal')">
                        <span>Minimal</span>
                    </button>
                    <button class="btn btn-primary" onclick="applyPreset('default')">
                        <span>Default</span>
                    </button>
                    <button class="btn btn-primary" onclick="applyPreset('maximum')">
                        <span>Maximum Detail</span>
                    </button>
                    <button class="btn btn-secondary" onclick="applyPreset('fundamental')">
                        <span>Fundamental Domain</span>
                    </button>
                    <button class="btn btn-secondary" onclick="applyPreset('primes')">
                        <span>Prime Focus</span>
                    </button>
                    <button class="btn btn-secondary" onclick="applyPreset('rings')">
                        <span>Ring Explorer</span>
                    </button>
                </div>
                <p style="margin-top: 15px; font-size: 0.85em; color: var(--text-dim); font-style: italic;">
                    Each preset optimizes different aspects of the visualization for specific mathematical insights.
                </p>
            </div>
        </div>

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
                <div class="section-header"> Basic Parameters</div>
                <div class="control-row">
                    <div class="control-item" data-tooltip="Rotates all visualizations by this angle. Animated when auto-rotate is enabled.">
                        <div class="control-label">
                            <span>Phase Rotation θ</span>
                            <span class="control-value" id="phaseValue">0°</span>
                        </div>
                        <input type="range" id="phaseSlider" min="0" max="360" value="0" step="0.1">
                    </div>

                    <div class="control-item" data-tooltip="The modulus for residue classes. Affects prime distribution and ring structure. No upper limit!">
                        <div class="control-label">
                            <span>Modulus m (Any Integer)</span>
                            <span class="control-value" id="modulusDisplay">30</span>
                        </div>
                        <input type="number" id="modulusInput" value="30" min="1" step="1">
                        <div class="help-text">No upper limit - enter any positive integer</div>
                    </div>

                    <div class="control-item" data-tooltip="Controls how fast the visualization rotates when auto-rotate is enabled.">
                        <div class="control-label">
                            <span>Animation Speed</span>
                            <span class="control-value" id="speedValue">1.0×</span>
                        </div>
                        <input type="range" id="speedSlider" min="0.1" max="20" value="1" step="0.1">
                    </div>
                </div>

                <!-- Zoom Controls -->
                <div class="section-header"> Canvas Zoom Controls</div>
                <div class="control-row">
                    <div class="control-item" data-tooltip="Zoom in or out on the Unit Disk visualization. 1.0 = default view.">
                        <div class="control-label">
                            <span>Unit Disk Zoom</span>
                            <span class="control-value" id="diskZoomValue">1.00×</span>
                        </div>
                        <input type="range" id="diskZoomSlider" min="0.1" max="5" value="1" step="0.05">
                    </div>

                    <div class="control-item" data-tooltip="Zoom in or out on the Cayley/Upper Half-Plane view. 1.0 = default view.">
                        <div class="control-label">
                            <span>Cayley Plane Zoom</span>
                            <span class="control-value" id="cayleyZoomValue">1.00×</span>
                        </div>
                        <input type="range" id="cayleyZoomSlider" min="0.1" max="5" value="1" step="0.05">
                    </div>

                    <div class="control-item" data-tooltip="Zoom in or out on the Nested Rings visualization. 1.0 = default view.">
                        <div class="control-label">
                            <span>Nested Rings Zoom</span>
                            <span class="control-value" id="nestedZoomValue">1.00×</span>
                        </div>
                        <input type="range" id="nestedZoomSlider" min="0.1" max="5" value="1" step="0.05">
                    </div>
                </div>

                <!-- Cayley View Controls -->
                <div class="section-header"> Cayley Plane View Range</div>
                <div class="control-row">
                    <div class="control-item" style="grid-column: 1 / -1;" data-tooltip="Switch between standard Cayley transform and alternative transformation">
                        <div class="control-label">
                            <span>Transform Type</span>
                        </div>
                        <select id="cayleyTransformType">
                            <option value="standard">Cayley Transform: w = i(1-z)/(1+z)</option>
                            <option value="alternate">Alternative Transform: w = i(1+z)/(1-z)</option>
                        </select>
                        <div class="help-text">Standard maps disk to upper half-plane. Alternative creates different geometric patterns.</div>
                    </div>
                    
                    <div class="control-item" data-tooltip="Width of the visible window in the upper half-plane. Increase to see more of the real axis.">
                        <div class="control-label">
                            <span>Horizontal Range (Re)</span>
                            <span class="control-value" id="cayleyHRangeValue">6.0</span>
                        </div>
                        <input type="range" id="cayleyHRangeSlider" min="2" max="20" value="6" step="0.5">
                        <div class="help-text">Width of visible area in ℍ</div>
                    </div>

                    <div class="control-item" data-tooltip="Height of the visible window. Increase to see more of the upper half-plane.">
                        <div class="control-label">
                            <span>Vertical Range (Im)</span>
                            <span class="control-value" id="cayleyVRangeValue">4.0</span>
                        </div>
                        <input type="range" id="cayleyVRangeSlider" min="1" max="15" value="4" step="0.5">
                        <div class="help-text">Height of visible area in ℍ</div>
                    </div>

                    <div class="control-item" data-tooltip="Shifts the viewing window up or down. Useful for focusing on different regions.">
                        <div class="control-label">
                            <span>Vertical Offset</span>
                            <span class="control-value" id="cayleyVOffsetValue">0.0</span>
                        </div>
                        <input type="range" id="cayleyVOffsetSlider" min="-5" max="5" value="0" step="0.1">
                        <div class="help-text">Shift view up/down</div>
                    </div>

                    <div class="control-item" data-tooltip="Controls spacing between grid lines. Lower values = more grid lines.">
                        <div class="control-label">
                            <span>Grid Density</span>
                            <span class="control-value" id="cayleyGridDensityValue">1.0</span>
                        </div>
                        <input type="range" id="cayleyGridDensitySlider" min="0.5" max="3" value="1" step="0.1">
                        <div class="help-text">Grid line spacing</div>
                    </div>
                </div>

                <!-- Prime Distribution -->
                <div class="section-header"> Prime Distribution</div>
                <div class="control-row">
                    <div class="control-item" data-tooltip="How many prime numbers to display. More primes = denser visualization but slower rendering.">
                        <div class="control-label">
                            <span>Number of Primes</span>
                            <span class="control-value" id="primesDisplay">150</span>
                        </div>
                        <input type="number" id="primesInput" value="150" min="0" step="10">
                        <div class="help-text">No limit - computation may slow with >5000</div>
                    </div>

                    <div class="control-item" data-tooltip="Generate all primes up to this value using the Sieve of Eratosthenes.">
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
                    <div class="control-item" data-tooltip="Starting modulus for the innermost ring. Usually 1 or 2.">
                        <div class="control-label">
                            <span>Min Ring (m_start)</span>
                            <span class="control-value" id="minRingDisplay">1</span>
                        </div>
                        <input type="number" id="minRingInput" value="1" min="1" step="1">
                    </div>

                    <div class="control-item" data-tooltip="Ending modulus for the outermost ring. No limit - try 50+ for impressive patterns!">
                        <div class="control-label">
                            <span>Max Ring (m_end)</span>
                            <span class="control-value" id="maxRingDisplay">12</span>
                        </div>
                        <input type="number" id="maxRingInput" value="12" min="1" step="1">
                        <div class="help-text">Unlimited - 100+ rings possible</div>
                    </div>

                    <div class="control-item" data-tooltip="Controls how spread out the rings are. Higher = more space between rings.">
                        <div class="control-label">
                            <span>Ring Spacing Factor</span>
                            <span class="control-value" id="spacingValue">1.0</span>
                        </div>
                        <input type="range" id="spacingSlider" min="0.1" max="5" value="1" step="0.1">
                    </div>
                </div>

                <!-- Custom Farey Points -->
                <div class="section-header"> Custom Farey Points</div>
                <div class="control-row">
                    <div class="control-item" style="grid-column: 1 / -1;" data-tooltip="Define specific rational fractions to highlight. These form the vertices of the Farey triangle.">
                        <div class="control-label">
                            <span>Farey Points (Fractions)</span>
                        </div>
                        <div id="fareyPointsList" class="farey-point-list"></div>
                        <button class="add-btn" onclick="addFareyPoint()">+ Add Farey Point</button>
                        <div class="help-text">Format: numerator/denominator (e.g., 1/3, 2/5, 3/7)</div>
                    </div>
                </div>

                <!-- Connection Options -->
                <div class="section-header"> Connection Options</div>
                <div class="control-row">
                    <div class="control-item" data-tooltip="Draw lines connecting points based on mathematical relationships in the nested rings view.">
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

                    <div class="control-item" data-tooltip="Line width for connections in the nested rings visualization.">
                        <div class="control-label">
                            <span>Connection Thickness</span>
                            <span class="control-value" id="connectionThicknessValue">1.0</span>
                        </div>
                        <input type="range" id="connectionThicknessSlider" min="0.1" max="10" value="1" step="0.1">
                    </div>

                    <div class="control-item" data-tooltip="Transparency of connection lines. Lower = more transparent, easier to see overlapping patterns.">
                        <div class="control-label">
                            <span>Connection Opacity</span>
                            <span class="control-value" id="connectionOpacityValue">0.3</span>
                        </div>
                        <input type="range" id="connectionOpacitySlider" min="0" max="1" value="0.3" step="0.05">
                    </div>
                </div>

                <!-- Label Options -->
                <div class="section-header"> Label Options</div>
                <div class="control-row">
                    <div class="control-item" data-tooltip="Choose which elements get text labels. 'Everything' can be cluttered for large visualizations.">
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

                    <div class="control-item" data-tooltip="Font size for labels in pixels.">
                        <div class="control-label">
                            <span>Label Size</span>
                            <span class="control-value" id="labelSizeValue">10</span>
                        </div>
                        <input type="range" id="labelSizeSlider" min="6" max="24" value="10" step="1">
                    </div>

                    <div class="control-item" data-tooltip="Label only every Nth ring to reduce clutter. Set to 1 to label all rings.">
                        <div class="control-label">
                            <span>Label Every Nth Ring</span>
                            <span class="control-value" id="labelFreqValue">1</span>
                        </div>
                        <input type="number" id="labelFreqInput" value="1" min="1" step="1">
                    </div>
                </div>

                <!-- Toggles -->
                <div class="section-header"> Display Options</div>
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

                    <input type="checkbox" id="toggleFundDomain">
                    <label for="toggleFundDomain" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Fundamental Domain</span>
                    </label>

                    <input type="checkbox" id="toggleVerticals">
                    <label for="toggleVerticals" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Vertical Geodesics</span>
                    </label>

                    <input type="checkbox" id="toggleDiskOutline">
                    <label for="toggleDiskOutline" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Unit Disk Outline</span>
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
                        <span> Update All</span>
                    </button>
                    <button class="btn btn-secondary" onclick="regeneratePrimes()">
                        <span> Regenerate Primes</span>
                    </button>
                    <button class="btn btn-accent" onclick="resetDefaults()">
                        <span> Reset to Defaults</span>
                    </button>
                    <button class="btn btn-secondary" onclick="exportVisualization()">
                        <span> Export PNG</span>
                    </button>
                    <button class="btn btn-secondary" onclick="printDiagnostics()">
                        <span> Print Diagnostics</span>
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
            cayleyHRange: 6,
            cayleyVRange: 4,
            cayleyVOffset: 0,
            cayleyGridDensity: 1,
            useAlternateCayley: false,
            diskZoom: 1.0,
            cayleyZoom: 1.0,
            nestedZoom: 1.0,
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

            // Zoom sliders
            document.getElementById('diskZoomSlider').addEventListener('input', e => {
                state.diskZoom = parseFloat(e.target.value);
                document.getElementById('diskZoomValue').textContent = state.diskZoom.toFixed(2) + '×';
                if (!state.animationId) updateAll();
            });

            document.getElementById('cayleyZoomSlider').addEventListener('input', e => {
                state.cayleyZoom = parseFloat(e.target.value);
                document.getElementById('cayleyZoomValue').textContent = state.cayleyZoom.toFixed(2) + '×';
                if (!state.animationId) updateAll();
            });

            document.getElementById('nestedZoomSlider').addEventListener('input', e => {
                state.nestedZoom = parseFloat(e.target.value);
                document.getElementById('nestedZoomValue').textContent = state.nestedZoom.toFixed(2) + '×';
                if (!state.animationId) updateAll();
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

            // Cayley view controls
            document.getElementById('cayleyHRangeSlider').addEventListener('input', e => {
                state.cayleyHRange = parseFloat(e.target.value);
                document.getElementById('cayleyHRangeValue').textContent = state.cayleyHRange.toFixed(1);
                if (!state.animationId) updateAll();
            });

            document.getElementById('cayleyVRangeSlider').addEventListener('input', e => {
                state.cayleyVRange = parseFloat(e.target.value);
                document.getElementById('cayleyVRangeValue').textContent = state.cayleyVRange.toFixed(1);
                if (!state.animationId) updateAll();
            });

            document.getElementById('cayleyVOffsetSlider').addEventListener('input', e => {
                state.cayleyVOffset = parseFloat(e.target.value);
                document.getElementById('cayleyVOffsetValue').textContent = state.cayleyVOffset.toFixed(1);
                if (!state.animationId) updateAll();
            });

            document.getElementById('cayleyGridDensitySlider').addEventListener('input', e => {
                state.cayleyGridDensity = parseFloat(e.target.value);
                document.getElementById('cayleyGridDensityValue').textContent = state.cayleyGridDensity.toFixed(1);
                if (!state.animationId) updateAll();
            });

            // Cayley transform type
            document.getElementById('cayleyTransformType').addEventListener('change', e => {
                state.useAlternateCayley = (e.target.value === 'alternate');
                updateAll();
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
             'toggleCusps', 'toggleRings', 'toggleGCD', 'toggleGrid',
             'toggleFundDomain', 'toggleVerticals', 'toggleDiskOutline'].forEach(id => {
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

        function cayleyTransform(z, useAlternate = false) {
            if (useAlternate) {
                // Alternative visualization: (1+z)/(1-z) transform
                // This was the original version - mathematically different but interesting
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
            
            // Standard Cayley transform: w = i(1-z)/(1+z)
            // Maps unit disk to upper half-plane
            const numRe = -z.re;      // Real part of (1-z)
            const numIm = 1 - z.im;   // Imaginary part of (1-z)
            const denRe = 1 + z.re;   // Real part of (1+z)
            const denIm = z.im;       // Imaginary part of (1+z)
            
            const denMagSq = denRe * denRe + denIm * denIm;
            
            if (denMagSq < 1e-10) {
                return { re: 0, im: 1e10 };
            }
            
            // Compute (1-z)/(1+z)
            const quotRe = (numRe * denRe + numIm * denIm) / denMagSq;
            const quotIm = (numIm * denRe - numRe * denIm) / denMagSq;
            
            // Multiply by i: i*(a+bi) = -b + ai
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
            const r = Math.min(w, h) * CONFIG.diskRadius * state.diskZoom;

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

            ctx.clearRect(0, 0, w, h);

            // Coordinate conversion functions for Cayley plane
            function mathToScreen(wp) {
                const reMin = -state.cayleyHRange / (2 * state.cayleyZoom);
                const reMax = state.cayleyHRange / (2 * state.cayleyZoom);
                const imMin = state.cayleyVOffset;
                const imMax = (state.cayleyVRange / state.cayleyZoom) + state.cayleyVOffset;
                
                const x = ((wp.re - reMin) / (reMax - reMin)) * w;
                const y = (1 - (wp.im - imMin) / (imMax - imMin)) * h;
                
                return { x, y };
            }

            const phase = state.phase * Math.PI / 180;
            const showGeodesic = document.getElementById('toggleGeodesic').checked;
            const showCusps = document.getElementById('toggleCusps').checked;
            const showPrimes = document.getElementById('togglePrimes').checked;
            const showChannels = document.getElementById('toggleChannels').checked;
            const showFarey = document.getElementById('toggleFarey').checked;
            const showGrid = document.getElementById('toggleGrid').checked;
            const showFundDomain = document.getElementById('toggleFundDomain').checked;
            const showVerticals = document.getElementById('toggleVerticals').checked;
            const showDiskOutline = document.getElementById('toggleDiskOutline').checked;

            // Draw grid
            if (showGrid) {
                ctx.strokeStyle = 'rgba(255, 255, 255, 0.08)';
                ctx.lineWidth = 1;

                const spacing = 0.5 / state.cayleyGridDensity;
                const reMin = -state.cayleyHRange / (2 * state.cayleyZoom);
                const reMax = state.cayleyHRange / (2 * state.cayleyZoom);
                const imMin = state.cayleyVOffset;
                const imMax = (state.cayleyVRange / state.cayleyZoom) + state.cayleyVOffset;

                // Vertical lines
                for (let re = Math.ceil(reMin / spacing) * spacing; re <= reMax; re += spacing) {
                    const p1 = mathToScreen({ re, im: imMin });
                    const p2 = mathToScreen({ re, im: imMax });
                    
                    ctx.beginPath();
                    ctx.moveTo(p1.x, p1.y);
                    ctx.lineTo(p2.x, p2.y);
                    ctx.stroke();

                    // Labels for major gridlines
                    if (Math.abs(re) > 0.01 && Math.abs(re % 1) < 0.01) {
                        const p = mathToScreen({ re, im: imMin });
                        ctx.fillStyle = 'rgba(255, 255, 255, 0.3)';
                        ctx.font = '10px Fira Code';
                        ctx.textAlign = 'center';
                        ctx.fillText(re.toFixed(0), p.x, p.y - 5);
                    }
                }

                // Horizontal lines
                for (let im = Math.ceil(imMin / spacing) * spacing; im <= imMax; im += spacing) {
                    if (im < 0.01) continue;
                    
                    const p1 = mathToScreen({ re: reMin, im });
                    const p2 = mathToScreen({ re: reMax, im });
                    
                    ctx.beginPath();
                    ctx.moveTo(p1.x, p1.y);
                    ctx.lineTo(p2.x, p2.y);
                    ctx.stroke();

                    // Labels for major gridlines
                    if (im > 0.1 && Math.abs(im % 1) < 0.01) {
                        const p = mathToScreen({ re: reMin, im });
                        ctx.fillStyle = 'rgba(255, 255, 255, 0.3)';
                        ctx.font = '10px Fira Code';
                        ctx.textAlign = 'right';
                        ctx.fillText(im.toFixed(0) + 'i', p.x + w - 5, p.y);
                    }
                }
            }

            // Real axis (boundary of ℍ)
            const axisP1 = mathToScreen({ re: -state.cayleyHRange / (2 * state.cayleyZoom), im: 0 });
            const axisP2 = mathToScreen({ re: state.cayleyHRange / (2 * state.cayleyZoom), im: 0 });
            
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.5)';
            ctx.lineWidth = 3;
            ctx.shadowBlur = 10;
            ctx.shadowColor = 'rgba(255, 255, 255, 0.3)';
            ctx.beginPath();
            ctx.moveTo(axisP1.x, axisP1.y);
            ctx.lineTo(axisP2.x, axisP2.y);
            ctx.stroke();
            ctx.shadowBlur = 0;

            // Imaginary axis
            const iAxisP1 = mathToScreen({ re: 0, im: state.cayleyVOffset });
            const iAxisP2 = mathToScreen({ re: 0, im: (state.cayleyVRange / state.cayleyZoom) + state.cayleyVOffset });
            
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.3)';
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.moveTo(iAxisP1.x, iAxisP1.y);
            ctx.lineTo(iAxisP2.x, iAxisP2.y);
            ctx.stroke();

            // Fundamental domain
            if (showFundDomain) {
                ctx.strokeStyle = 'rgba(230, 126, 34, 0.5)';
                ctx.lineWidth = 2;
                ctx.setLineDash([5, 5]);

                // Left boundary: Re = -1/2
                const leftTop = mathToScreen({ re: -0.5, im: (state.cayleyVRange / state.cayleyZoom) + state.cayleyVOffset });
                const leftBot = mathToScreen({ re: -0.5, im: Math.max(0, Math.sqrt(1 - 0.25)) });
                ctx.beginPath();
                ctx.moveTo(leftBot.x, leftBot.y);
                ctx.lineTo(leftTop.x, leftTop.y);
                ctx.stroke();

                // Right boundary: Re = 1/2
                const rightTop = mathToScreen({ re: 0.5, im: (state.cayleyVRange / state.cayleyZoom) + state.cayleyVOffset });
                const rightBot = mathToScreen({ re: 0.5, im: Math.max(0, Math.sqrt(1 - 0.25)) });
                ctx.beginPath();
                ctx.moveTo(rightBot.x, rightBot.y);
                ctx.lineTo(rightTop.x, rightTop.y);
                ctx.stroke();

                // Bottom arc: |z| = 1
                ctx.beginPath();
                let firstArc = true;
                for (let i = 0; i <= 50; i++) {
                    const angle = Math.PI * i / 50;
                    const re = Math.cos(angle);
                    const im = Math.sin(angle);
                    if (Math.abs(re) <= 0.5 && im >= 0) {
                        const p = mathToScreen({ re, im });
                        if (firstArc) {
                            ctx.moveTo(p.x, p.y);
                            firstArc = false;
                        } else {
                            ctx.lineTo(p.x, p.y);
                        }
                    }
                }
                ctx.stroke();
                ctx.setLineDash([]);
            }

            // Unit disk outline (where |z|=1 on disk maps under Cayley)
            if (showDiskOutline) {
                ctx.strokeStyle = 'rgba(231, 76, 60, 0.4)';
                ctx.lineWidth = 1.5;
                ctx.setLineDash([3, 3]);

                ctx.beginPath();
                let firstDisk = true;
                for (let i = 0; i <= 100; i++) {
                    const angle = 2 * Math.PI * i / 100;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const wp = cayleyTransform(z);
                    
                    const p = mathToScreen(wp);
                    if (firstDisk) {
                        ctx.moveTo(p.x, p.y);
                        firstDisk = false;
                    } else {
                        ctx.lineTo(p.x, p.y);
                    }
                }
                ctx.stroke();
                ctx.setLineDash([]);
            }

            // Vertical geodesics
            if (showVerticals) {
                ctx.strokeStyle = 'rgba(155, 89, 182, 0.3)';
                ctx.lineWidth = 1;
                
                const reMin = -state.cayleyHRange / (2 * state.cayleyZoom);
                const reMax = state.cayleyHRange / (2 * state.cayleyZoom);
                
                for (let re = Math.ceil(reMin); re <= reMax; re++) {
                    const p1 = mathToScreen({ re, im: state.cayleyVOffset });
                    const p2 = mathToScreen({ re, im: (state.cayleyVRange / state.cayleyZoom) + state.cayleyVOffset });
                    
                    ctx.beginPath();
                    ctx.moveTo(p1.x, p1.y);
                    ctx.lineTo(p2.x, p2.y);
                    ctx.stroke();
                }
            }

            // Geodesic
            if (showGeodesic && state.fareyPoints.length >= 2) {
                // Draw all geodesics between all pairs of Farey points
                for (let i = 0; i < state.fareyPoints.length; i++) {
                    for (let j = i + 1; j < state.fareyPoints.length; j++) {
                        const fp1 = state.fareyPoints[i];
                        const fp2 = state.fareyPoints[j];
                        
                        const frac1 = fp1.num / fp1.den;
                        const frac2 = fp2.num / fp2.den;
                        
                        const angle1 = 2 * Math.PI * frac1 + phase;
                        const angle2 = 2 * Math.PI * frac2 + phase;
                        
                        const z1 = { re: Math.cos(angle1), im: Math.sin(angle1) };
                        const z2 = { re: Math.cos(angle2), im: Math.sin(angle2) };
                        
                        const w1 = cayleyTransform(z1, state.useAlternateCayley);
                        const w2 = cayleyTransform(z2, state.useAlternateCayley);

                        const centerRe = (w1.re + w2.re) / 2;
                        const radius = Math.sqrt((w1.re - centerRe) ** 2 + w1.im ** 2);

                        // Highlight first geodesic
                        const isFirst = (i === 0 && j === 1);
                        ctx.strokeStyle = isFirst ? CONFIG.colors.geodesic : 'rgba(26, 188, 156, 0.3)';
                        ctx.lineWidth = isFirst ? 4 : 2;
                        if (isFirst) {
                            ctx.shadowBlur = 20;
                            ctx.shadowColor = CONFIG.colors.geodesic;
                        }
                        
                        ctx.beginPath();
                        let firstGeo = true;
                        for (let k = 0; k <= 100; k++) {
                            const angle = Math.PI * k / 100;
                            const re = centerRe + radius * Math.cos(angle);
                            const im = radius * Math.sin(angle);
                            
                            const p = mathToScreen({ re, im });
                            if (firstGeo) {
                                ctx.moveTo(p.x, p.y);
                                firstGeo = false;
                            } else {
                                ctx.lineTo(p.x, p.y);
                            }
                        }
                        ctx.stroke();
                        ctx.shadowBlur = 0;
                    }
                }
            }

            // Transformed primes
            if (showPrimes) {
                const colors = generateColors(state.modulus);
                const displayPrimes = state.primes.slice(0, state.numPrimes);

                const reMin = -state.cayleyHRange / (2 * state.cayleyZoom);
                const reMax = state.cayleyHRange / (2 * state.cayleyZoom);
                const imMin = state.cayleyVOffset;
                const imMax = (state.cayleyVRange / state.cayleyZoom) + state.cayleyVOffset;

                displayPrimes.forEach(p => {
                    if (showChannels && gcd(p, state.modulus) !== 1) return;

                    const angle = 2 * Math.PI * p / state.modulus + phase;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const wp = cayleyTransform(z, state.useAlternateCayley);

                    // Only draw if in visible range
                    if (wp.re >= reMin && wp.re <= reMax && wp.im >= imMin && wp.im <= imMax && wp.im > 0.01) {
                        const p_screen = mathToScreen(wp);
                        const color = showChannels ? colors[p % state.modulus] : CONFIG.colors.prime;
                        
                        ctx.fillStyle = color;
                        ctx.shadowBlur = 6;
                        ctx.shadowColor = color;
                        ctx.beginPath();
                        ctx.arc(p_screen.x, p_screen.y, 3.5, 0, 2 * Math.PI);
                        ctx.fill();
                        ctx.shadowBlur = 0;
                    }
                });
            }

            // Cusps on real axis
            if (showCusps && state.fareyPoints.length > 0) {
                state.fareyPoints.forEach(fp => {
                    const frac = fp.num / fp.den;
                    const angle = 2 * Math.PI * frac + phase;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const wp = cayleyTransform(z, state.useAlternateCayley);
                    const cuspP = mathToScreen({ re: wp.re, im: 0 });

                    ctx.fillStyle = CONFIG.colors.cusp;
                    ctx.shadowBlur = 15;
                    ctx.shadowColor = CONFIG.colors.cusp;
                    ctx.beginPath();
                    ctx.arc(cuspP.x, cuspP.y, 6, 0, 2 * Math.PI);
                    ctx.fill();
                    ctx.shadowBlur = 0;

                    if (state.labelMode !== 'none') {
                        ctx.fillStyle = CONFIG.colors.cusp;
                        ctx.font = `${state.labelSize + 3}px "Fira Code"`;
                        ctx.textAlign = 'center';
                        ctx.shadowBlur = 8;
                        ctx.shadowColor = 'rgba(0, 0, 0, 0.8)';
                        ctx.fillText(`${fp.num}/${fp.den}`, cuspP.x, cuspP.y + 22);
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
                    const wp = cayleyTransform(z, state.useAlternateCayley);
                    return {
                        ...mathToScreen(wp),
                        wp: wp,
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
            ctx.fillText('Upper Half-Plane ℍ', w/2, 35);
            ctx.shadowBlur = 0;
        }

        function drawNested() {
            const canvas = canvases.nested;
            const ctx = canvases.nestedCtx;
            const w = canvas.width / (window.devicePixelRatio || 1);
            const h = canvas.height / (window.devicePixelRatio || 1);
            const cx = w / 2;
            const cy = h / 2;
            const maxRadius = Math.min(w, h) * 0.42 * state.nestedZoom;
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

        function toggleIntro() {
            const panel = document.getElementById('introPanel');
            const toggle = document.getElementById('introToggle');
            if (panel.style.display === 'none') {
                panel.style.display = 'block';
                toggle.textContent = '▼';
            } else {
                panel.style.display = 'none';
                toggle.textContent = '▶';
            }
        }

        function applyPreset(preset) {
            stopAnimation();
            
            switch(preset) {
                case 'minimal':
                    state.modulus = 12;
                    state.numPrimes = 50;
                    state.minRing = 1;
                    state.maxRing = 6;
                    state.connectionMode = 'none';
                    state.labelMode = 'farey';
                    document.getElementById('toggleFarey').checked = true;
                    document.getElementById('toggleGeodesic').checked = true;
                    document.getElementById('togglePrimes').checked = false;
                    document.getElementById('toggleChannels').checked = false;
                    document.getElementById('toggleCusps').checked = true;
                    document.getElementById('toggleRings').checked = true;
                    document.getElementById('toggleGCD').checked = false;
                    document.getElementById('toggleGrid').checked = false;
                    document.getElementById('toggleFundDomain').checked = false;
                    document.getElementById('toggleVerticals').checked = false;
                    document.getElementById('toggleDiskOutline').checked = false;
                    break;
                    
                case 'default':
                    resetDefaults();
                    return;
                    
                case 'maximum':
                    state.modulus = 60;
                    state.numPrimes = 500;
                    state.minRing = 1;
                    state.maxRing = 30;
                    state.connectionMode = 'gcd';
                    state.connectionOpacity = 0.15;
                    state.labelMode = 'rings';
                    state.labelFreq = 3;
                    document.getElementById('toggleFarey').checked = true;
                    document.getElementById('toggleGeodesic').checked = true;
                    document.getElementById('togglePrimes').checked = true;
                    document.getElementById('toggleChannels').checked = true;
                    document.getElementById('toggleCusps').checked = true;
                    document.getElementById('toggleRings').checked = true;
                    document.getElementById('toggleGCD').checked = true;
                    document.getElementById('toggleGrid').checked = true;
                    document.getElementById('toggleFundDomain').checked = true;
                    document.getElementById('toggleVerticals').checked = true;
                    document.getElementById('toggleDiskOutline').checked = true;
                    break;
                    
                case 'fundamental':
                    state.modulus = 12;
                    state.numPrimes = 0;
                    state.cayleyHRange = 3;
                    state.cayleyVRange = 3;
                    state.cayleyVOffset = 0;
                    state.minRing = 1;
                    state.maxRing = 8;
                    document.getElementById('toggleFarey').checked = true;
                    document.getElementById('toggleGeodesic').checked = true;
                    document.getElementById('togglePrimes').checked = false;
                    document.getElementById('toggleChannels').checked = false;
                    document.getElementById('toggleCusps').checked = true;
                    document.getElementById('toggleRings').checked = false;
                    document.getElementById('toggleGCD').checked = false;
                    document.getElementById('toggleGrid').checked = true;
                    document.getElementById('toggleFundDomain').checked = true;
                    document.getElementById('toggleVerticals').checked = true;
                    document.getElementById('toggleDiskOutline').checked = true;
                    break;
                    
                case 'primes':
                    state.modulus = 24;
                    state.numPrimes = 1000;
                    state.primeLimit = 20000;
                    state.minRing = 1;
                    state.maxRing = 8;
                    regeneratePrimes();
                    document.getElementById('toggleFarey').checked = false;
                    document.getElementById('toggleGeodesic').checked = false;
                    document.getElementById('togglePrimes').checked = true;
                    document.getElementById('toggleChannels').checked = true;
                    document.getElementById('toggleCusps').checked = false;
                    document.getElementById('toggleRings').checked = false;
                    document.getElementById('toggleGCD').checked = false;
                    document.getElementById('toggleGrid').checked = false;
                    document.getElementById('toggleFundDomain').checked = false;
                    document.getElementById('toggleVerticals').checked = false;
                    document.getElementById('toggleDiskOutline').checked = false;
                    break;
                    
                case 'rings':
                    state.modulus = 24;
                    state.numPrimes = 0;
                    state.minRing = 1;
                    state.maxRing = 40;
                    state.connectionMode = 'mod';
                    state.connectionOpacity = 0.2;
                    state.labelMode = 'rings';
                    state.labelFreq = 5;
                    document.getElementById('toggleFarey').checked = false;
                    document.getElementById('toggleGeodesic').checked = false;
                    document.getElementById('togglePrimes').checked = false;
                    document.getElementById('toggleChannels').checked = false;
                    document.getElementById('toggleCusps').checked = false;
                    document.getElementById('toggleRings').checked = true;
                    document.getElementById('toggleGCD').checked = true;
                    document.getElementById('toggleGrid').checked = false;
                    document.getElementById('toggleFundDomain').checked = false;
                    document.getElementById('toggleVerticals').checked = false;
                    document.getElementById('toggleDiskOutline').checked = false;
                    break;
            }
            
            // Sync UI with state
            syncUIWithState();
            updateAll();
        }
        
        function syncUIWithState() {
            document.getElementById('phaseSlider').value = state.phase;
            document.getElementById('phaseInput').value = state.phase;
            document.getElementById('phaseValue').textContent = state.phase.toFixed(1) + '°';
            
            document.getElementById('modulusSlider').value = Math.min(state.modulus, 100);
            document.getElementById('modulusInput').value = state.modulus;
            document.getElementById('modulusDisplay').textContent = state.modulus;
            
            document.getElementById('speedSlider').value = state.animSpeed;
            document.getElementById('speedInput').value = state.animSpeed;
            document.getElementById('speedValue').textContent = state.animSpeed.toFixed(1) + '×';
            
            document.getElementById('primesInput').value = state.numPrimes;
            document.getElementById('primesDisplay').textContent = state.numPrimes;
            
            document.getElementById('minRingInput').value = state.minRing;
            document.getElementById('minRingDisplay').textContent = state.minRing;
            
            document.getElementById('maxRingInput').value = state.maxRing;
            document.getElementById('maxRingDisplay').textContent = state.maxRing;
            
            document.getElementById('spacingSlider').value = state.ringSpacing;
            document.getElementById('spacingInput').value = state.ringSpacing;
            document.getElementById('spacingValue').textContent = state.ringSpacing.toFixed(1);
            
            document.getElementById('connectionMode').value = state.connectionMode;
            
            document.getElementById('connectionThicknessSlider').value = state.connectionThickness;
            document.getElementById('connectionThicknessInput').value = state.connectionThickness;
            document.getElementById('connectionThicknessValue').textContent = state.connectionThickness.toFixed(1);
            
            document.getElementById('connectionOpacitySlider').value = state.connectionOpacity;
            document.getElementById('connectionOpacityInput').value = state.connectionOpacity;
            document.getElementById('connectionOpacityValue').textContent = state.connectionOpacity.toFixed(2);
            
            document.getElementById('labelMode').value = state.labelMode;
            
            document.getElementById('labelSizeSlider').value = state.labelSize;
            document.getElementById('labelSizeInput').value = state.labelSize;
            document.getElementById('labelSizeValue').textContent = state.labelSize;
            
            document.getElementById('labelFreqInput').value = state.labelFreq;
            document.getElementById('labelFreqValue').textContent = state.labelFreq;
            
            document.getElementById('cayleyHRangeSlider').value = state.cayleyHRange;
            document.getElementById('cayleyHRangeInput').value = state.cayleyHRange;
            document.getElementById('cayleyHRangeValue').textContent = state.cayleyHRange.toFixed(1);
            
            document.getElementById('cayleyVRangeSlider').value = state.cayleyVRange;
            document.getElementById('cayleyVRangeInput').value = state.cayleyVRange;
            document.getElementById('cayleyVRangeValue').textContent = state.cayleyVRange.toFixed(1);
            
            document.getElementById('cayleyVOffsetSlider').value = state.cayleyVOffset;
            document.getElementById('cayleyVOffsetInput').value = state.cayleyVOffset;
            document.getElementById('cayleyVOffsetValue').textContent = state.cayleyVOffset.toFixed(1);
            
            document.getElementById('cayleyGridDensitySlider').value = state.cayleyGridDensity;
            document.getElementById('cayleyGridDensityInput').value = state.cayleyGridDensity;
            document.getElementById('cayleyGridDensityValue').textContent = state.cayleyGridDensity.toFixed(1);
        }

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
                cayleyHRange: 6,
                cayleyVRange: 4,
                cayleyVOffset: 0,
                cayleyGridDensity: 1,
                useAlternateCayley: false,
                diskZoom: 1.0,
                cayleyZoom: 1.0,
                nestedZoom: 1.0,
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
            document.getElementById('phaseInput').value = 0;
            document.getElementById('modulusSlider').value = 30;
            document.getElementById('modulusInput').value = 30;
            document.getElementById('primesInput').value = 150;
            document.getElementById('primeLimitInput').value = 10000;
            document.getElementById('speedSlider').value = 1;
            document.getElementById('speedInput').value = 1;
            document.getElementById('minRingInput').value = 1;
            document.getElementById('maxRingInput').value = 12;
            document.getElementById('spacingSlider').value = 1;
            document.getElementById('spacingInput').value = 1;
            document.getElementById('cayleyHRangeSlider').value = 6;
            document.getElementById('cayleyHRangeInput').value = 6;
            document.getElementById('cayleyVRangeSlider').value = 4;
            document.getElementById('cayleyVRangeInput').value = 4;
            document.getElementById('cayleyVOffsetSlider').value = 0;
            document.getElementById('cayleyVOffsetInput').value = 0;
            document.getElementById('cayleyGridDensitySlider').value = 1;
            document.getElementById('cayleyGridDensityInput').value = 1;
            document.getElementById('connectionMode').value = 'none';
            document.getElementById('connectionThicknessSlider').value = 1;
            document.getElementById('connectionThicknessInput').value = 1;
            document.getElementById('connectionOpacitySlider').value = 0.3;
            document.getElementById('connectionOpacityInput').value = 0.3;
            document.getElementById('labelMode').value = 'farey';
            document.getElementById('labelSizeSlider').value = 10;
            document.getElementById('labelSizeInput').value = 10;
            document.getElementById('labelFreqInput').value = 1;
            document.getElementById('toggleAnimate').checked = false;
            document.getElementById('toggleFundDomain').checked = false;
            document.getElementById('toggleVerticals').checked = false;
            document.getElementById('toggleDiskOutline').checked = false;
            document.getElementById('cayleyTransformType').value = 'standard';

            document.getElementById('phaseValue').textContent = '0°';
            document.getElementById('modulusDisplay').textContent = '30';
            document.getElementById('primesDisplay').textContent = '150';
            document.getElementById('primeLimitDisplay').textContent = '10000';
            document.getElementById('speedValue').textContent = '1.0×';
            document.getElementById('minRingDisplay').textContent = '1';
            document.getElementById('maxRingDisplay').textContent = '12';
            document.getElementById('spacingValue').textContent = '1.0';
            document.getElementById('cayleyHRangeValue').textContent = '6.0';
            document.getElementById('cayleyVRangeValue').textContent = '4.0';
            document.getElementById('cayleyVOffsetValue').textContent = '0.0';
            document.getElementById('cayleyGridDensityValue').textContent = '1.0';
            document.getElementById('connectionThicknessValue').textContent = '1.0';
            document.getElementById('connectionOpacityValue').textContent = '0.30';
            document.getElementById('labelSizeValue').textContent = '10';
            document.getElementById('labelFreqValue').textContent = '1';
            document.getElementById('diskZoomValue').textContent = '1.00×';
            document.getElementById('cayleyZoomValue').textContent = '1.00×';
            document.getElementById('nestedZoomValue').textContent = '1.00×';

            stopAnimation();
            updateFareyPointsList();
            updateAll();
        }

        function exportVisualization() {
            // Create export dialog if it doesn't exist
            if (!document.getElementById('exportDialog')) {
                createExportDialog();
            }
            showExportDialog();
        }

        function createExportDialog() {
            const dialog = document.createElement('div');
            dialog.id = 'exportDialog';
            dialog.className = 'export-dialog';
            dialog.style.display = 'none';
            
            dialog.innerHTML = `
                <div class="export-dialog-content">
                    <div class="export-dialog-header">
                        <h3>Export Visualization</h3>
                        <button class="close-btn" onclick="closeExportDialog()">✕</button>
                    </div>
                    <div class="export-dialog-body">
                        <div class="export-section">
                            <h4>Select Canvas</h4>
                            <div class="export-radio-group">
                                <label class="export-radio">
                                    <input type="radio" name="canvas" value="disk" checked>
                                    <span>Unit Disk Only</span>
                                </label>
                                <label class="export-radio">
                                    <input type="radio" name="canvas" value="cayley">
                                    <span>Upper Half-Plane Only</span>
                                </label>
                                <label class="export-radio">
                                    <input type="radio" name="canvas" value="nested">
                                    <span>Nested Rings Only</span>
                                </label>
                                <label class="export-radio">
                                    <input type="radio" name="canvas" value="all">
                                    <span>All Three Canvases</span>
                                </label>
                            </div>
                        </div>
                        
                        <div class="export-section">
                            <h4>Resolution</h4>
                            <div class="export-radio-group">
                                <label class="export-radio">
                                    <input type="radio" name="resolution" value="1080" checked>
                                    <span>Full HD (1920×1080)</span>
                                </label>
                                <label class="export-radio">
                                    <input type="radio" name="resolution" value="1440">
                                    <span>2K (2560×1440)</span>
                                </label>
                                <label class="export-radio">
                                    <input type="radio" name="resolution" value="4k">
                                    <span>4K UHD (3840×2160)</span>
                                </label>
                                <label class="export-radio">
                                    <input type="radio" name="resolution" value="8k">
                                    <span>8K UHD (7680×4320)</span>
                                </label>
                            </div>
                        </div>
                        
                        <div class="export-section">
                            <h4>Options</h4>
                            <label class="export-checkbox">
                                <input type="checkbox" id="includeLegend" checked>
                                <span>Include Detailed Legend</span>
                            </label>
                            <label class="export-checkbox">
                                <input type="checkbox" id="includeWatermark" checked>
                                <span>Include Watermark (Wessen Getachew)</span>
                            </label>
                        </div>
                        
                        <div class="action-bar">
                            <button class="btn btn-primary" onclick="performExport()">
                                <span>
                                    Export PNG</span>
                            </button>
                            <button class="btn btn-secondary" onclick="closeExportDialog()">
                                <span>Cancel</span>
                            </button>
                        </div>
                    </div>
                </div>
            `;
            
            document.body.appendChild(dialog);
        }

        function showExportDialog() {
            document.getElementById('exportDialog').style.display = 'flex';
        }

        function closeExportDialog() {
            document.getElementById('exportDialog').style.display = 'none';
        }

        function performExport() {
            const canvasSelection = document.querySelector('input[name="canvas"]:checked').value;
            const resolution = document.querySelector('input[name="resolution"]:checked').value;
            const includeLegend = document.getElementById('includeLegend').checked;
            const includeWatermark = document.getElementById('includeWatermark').checked;

            let width, height;
            switch(resolution) {
                case '1080':
                    width = 1920;
                    height = 1080;
                    break;
                case '1440':
                    width = 2560;
                    height = 1440;
                    break;
                case '4k':
                    width = 3840;
                    height = 2160;
                    break;
                case '8k':
                    width = 7680;
                    height = 4320;
                    break;
            }

            const tempCanvas = document.createElement('canvas');
            const tempCtx = tempCanvas.getContext('2d');

            if (canvasSelection === 'all') {
                // For all three canvases, maintain aspect ratio
                tempCanvas.width = width;
                tempCanvas.height = height;
                
                // Background
                tempCtx.fillStyle = '#0a0e27';
                tempCtx.fillRect(0, 0, width, height);

                // Calculate dimensions to fit three square canvases
                const canvasSize = Math.min(width / 3, height);
                const offsetY = (height - canvasSize) / 2;
                const sourceCanvases = [canvases.disk, canvases.cayley, canvases.nested];
                
                sourceCanvases.forEach((canvas, idx) => {
                    const destX = idx * (width / 3);
                    const destWidth = width / 3;
                    
                    // Draw canvas maintaining aspect ratio (square)
                    tempCtx.drawImage(canvas, 
                        0, 0, canvas.width, canvas.height,
                        destX, offsetY, destWidth, canvasSize);
                });

                // Add titles for each canvas
                drawCanvasTitles(tempCtx, width, height, canvasSize, offsetY);

                if (includeLegend) {
                    drawLegend(tempCtx, width, height, 'all');
                }
            } else {
                // For single canvas, make it square to maintain aspect ratio
                const size = Math.min(width, height);
                tempCanvas.width = size;
                tempCanvas.height = size;

                // Background
                tempCtx.fillStyle = '#0a0e27';
                tempCtx.fillRect(0, 0, size, size);

                let sourceCanvas;
                let title;
                switch(canvasSelection) {
                    case 'disk':
                        sourceCanvas = canvases.disk;
                        title = 'Unit Disk 𝔻 - Farey Triangle';
                        break;
                    case 'cayley':
                        sourceCanvas = canvases.cayley;
                        title = 'Upper Half-Plane ℍ - Cayley Transform';
                        break;
                    case 'nested':
                        sourceCanvas = canvases.nested;
                        title = 'Nested Modular Rings';
                        break;
                }

                // Draw canvas maintaining square aspect ratio
                tempCtx.drawImage(sourceCanvas, 0, 0, size, size);

                // Add title
                drawMainTitle(tempCtx, size, title);

                if (includeLegend) {
                    drawLegend(tempCtx, size, size, canvasSelection);
                }
            }

            // Add watermark if requested (should be visible now)
            if (includeWatermark) {
                drawWatermark(tempCtx, tempCanvas.width, tempCanvas.height);
            }

            const link = document.createElement('a');
            link.download = `farey-cayley-${canvasSelection}-${resolution}-${Date.now()}.png`;
            link.href = tempCanvas.toDataURL('image/png', 1.0);
            link.click();

            closeExportDialog();
        }

        function drawMainTitle(ctx, size, titleText) {
            const scale = size / 1000;
            const fontSize = 24 * scale;
            const padding = 30 * scale;

            ctx.save();
            
            // Title background
            const textMetrics = ctx.measureText(titleText);
            const titleWidth = textMetrics.width + 60 * scale;
            const titleHeight = 60 * scale;
            const titleX = (size - titleWidth) / 2;
            const titleY = padding;

            ctx.fillStyle = 'rgba(10, 14, 39, 0.9)';
            ctx.strokeStyle = 'rgba(255, 215, 0, 0.6)';
            ctx.lineWidth = 2 * scale;
            
            const radius = 8 * scale;
            ctx.beginPath();
            ctx.moveTo(titleX + radius, titleY);
            ctx.lineTo(titleX + titleWidth - radius, titleY);
            ctx.quadraticCurveTo(titleX + titleWidth, titleY, titleX + titleWidth, titleY + radius);
            ctx.lineTo(titleX + titleWidth, titleY + titleHeight - radius);
            ctx.quadraticCurveTo(titleX + titleWidth, titleY + titleHeight, titleX + titleWidth - radius, titleY + titleHeight);
            ctx.lineTo(titleX + radius, titleY + titleHeight);
            ctx.quadraticCurveTo(titleX, titleY + titleHeight, titleX, titleY + titleHeight - radius);
            ctx.lineTo(titleX, titleY + radius);
            ctx.quadraticCurveTo(titleX, titleY, titleX + radius, titleY);
            ctx.closePath();
            ctx.fill();
            ctx.stroke();

            // Title text
            ctx.fillStyle = '#ffd700';
            ctx.font = `bold ${fontSize}px "Fira Code"`;
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.shadowBlur = 10 * scale;
            ctx.shadowColor = 'rgba(255, 215, 0, 0.5)';
            ctx.fillText(titleText, size / 2, titleY + titleHeight / 2);
            
            ctx.restore();
        }

        function drawCanvasTitles(ctx, width, height, canvasSize, offsetY) {
            const scale = width / 5760; // Scale based on combined width
            const fontSize = 20 * scale;
            const titles = [
                'Unit Disk 𝔻',
                'Upper Half-Plane ℍ',
                'Nested Rings ⊚'
            ];

            ctx.save();
            
            titles.forEach((title, idx) => {
                const centerX = (idx + 0.5) * (width / 3);
                const titleY = offsetY - 40 * scale;

                ctx.fillStyle = '#ffd700';
                ctx.font = `bold ${fontSize}px "Fira Code"`;
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.shadowBlur = 8 * scale;
                ctx.shadowColor = 'rgba(255, 215, 0, 0.4)';
                ctx.fillText(title, centerX, titleY);
            });
            
            ctx.restore();
        }

        function drawWatermark(ctx, width, height) {
            const scale = Math.min(width, height) / 1920;
            const fontSize = 16 * scale;
            const padding = 20 * scale;

            ctx.save();
            
            // Measure text first
            ctx.font = `bold ${fontSize}px "Fira Code"`;
            const textMetrics = ctx.measureText('Wessen Getachew');
            const watermarkWidth = textMetrics.width + 40 * scale;
            const watermarkHeight = 45 * scale;
            const watermarkX = width - watermarkWidth - padding;
            const watermarkY = height - watermarkHeight - padding;

            // Watermark background
            ctx.fillStyle = 'rgba(10, 14, 39, 0.9)';
            ctx.strokeStyle = 'rgba(255, 215, 0, 0.7)';
            ctx.lineWidth = 2 * scale;
            
            // Rounded rectangle
            const radius = 8 * scale;
            ctx.beginPath();
            ctx.moveTo(watermarkX + radius, watermarkY);
            ctx.lineTo(watermarkX + watermarkWidth - radius, watermarkY);
            ctx.quadraticCurveTo(watermarkX + watermarkWidth, watermarkY, watermarkX + watermarkWidth, watermarkY + radius);
            ctx.lineTo(watermarkX + watermarkWidth, watermarkY + watermarkHeight - radius);
            ctx.quadraticCurveTo(watermarkX + watermarkWidth, watermarkY + watermarkHeight, watermarkX + watermarkWidth - radius, watermarkY + watermarkHeight);
            ctx.lineTo(watermarkX + radius, watermarkY + watermarkHeight);
            ctx.quadraticCurveTo(watermarkX, watermarkY + watermarkHeight, watermarkX, watermarkY + watermarkHeight - radius);
            ctx.lineTo(watermarkX, watermarkY + radius);
            ctx.quadraticCurveTo(watermarkX, watermarkY, watermarkX + radius, watermarkY);
            ctx.closePath();
            ctx.fill();
            ctx.stroke();

            // Watermark text
            ctx.fillStyle = '#ffd700';
            ctx.font = `bold ${fontSize}px "Fira Code"`;
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.shadowBlur = 10 * scale;
            ctx.shadowColor = 'rgba(255, 215, 0, 0.5)';
            ctx.fillText('Wessen Getachew', watermarkX + watermarkWidth / 2, watermarkY + watermarkHeight / 2);
            
            ctx.restore();
        }

        function drawLegend(ctx, width, height, canvasType) {
            const scale = Math.min(width, height) / 1920;
            
            // Adjust legend size and position
            let legendWidth = 400 * scale;
            let legendX, legendY;
            
            // Position in top-left to avoid overlap
            legendX = 30 * scale;
            legendY = 120 * scale; // Lower to avoid title overlap
            
            const fontSize = 13 * scale;
            const titleSize = 20 * scale;
            const sectionTitleSize = 15 * scale;
            const itemHeight = 28 * scale;
            const symbolSize = 20 * scale;
            const padding = 20 * scale;

            let items = [];
            let parameters = [];
            
            if (canvasType === 'disk') {
                items = [
                    { type: 'section', text: 'Unit Disk Elements' },
                    { color: CONFIG.colors.disk, text: 'Unit Circle Boundary' },
                    { color: CONFIG.colors.farey, text: 'Farey Triangle Vertices' },
                    { color: CONFIG.colors.fareyFill, text: 'Farey Triangle Fill' },
                    { color: CONFIG.colors.prime, text: 'Prime Numbers' },
                    { type: 'section', text: 'Structure' },
                    { color: CONFIG.colors.axes, text: 'Coordinate Axes' },
                    { color: CONFIG.colors.grid, text: 'Grid Lines' }
                ];
                parameters = [
                    `m = ${state.modulus}`,
                    `Primes: ${Math.min(state.numPrimes, state.primes.length)}`,
                    `Farey pts: ${state.fareyPoints.length}`,
                    `θ = ${state.phase.toFixed(1)}°`
                ];
            } else if (canvasType === 'cayley') {
                items = [
                    { type: 'section', text: 'Upper Half-Plane ℍ' },
                    { color: 'rgba(255, 255, 255, 0.5)', text: 'Real Axis (∂ℍ)' },
                    { color: CONFIG.colors.farey, text: 'Farey Points' },
                    { color: CONFIG.colors.geodesic, text: 'Geodesics' },
                    { color: CONFIG.colors.cusp, text: 'Cusp Points' },
                    { type: 'section', text: 'Distribution' },
                    { color: CONFIG.colors.prime, text: 'Primes' },
                    { type: 'section', text: 'Geometry' },
                    { color: 'rgba(230, 126, 34, 0.5)', text: 'Fund. Domain' },
                    { color: 'rgba(155, 89, 182, 0.3)', text: 'Vert. Geodesics' }
                ];
                parameters = [
                    `Re: [${(-state.cayleyHRange/2).toFixed(1)}, ${(state.cayleyHRange/2).toFixed(1)}]`,
                    `Im: [${state.cayleyVOffset.toFixed(1)}, ${(state.cayleyVRange + state.cayleyVOffset).toFixed(1)}i]`,
                    `m = ${state.modulus}`,
                    `w = i(1-z)/(1+z)`
                ];
            } else if (canvasType === 'nested') {
                items = [
                    { type: 'section', text: 'GCD Coloring' },
                    { color: CONFIG.colors.farey, text: 'GCD = 1 (Coprime)' },
                    { color: '#e74c3c', text: 'GCD = m (Divisible)' },
                    { color: '#00ffff', text: 'GCD = 2' },
                    { color: '#9b59b6', text: 'GCD = 3' },
                    { color: 'hsla(240, 70%, 60%, 0.85)', text: 'GCD = 4' },
                    { type: 'section', text: 'Structure' },
                    { color: 'rgba(255, 255, 255, 0.15)', text: 'Ring Circles' },
                    { color: '#ff6b6b', text: 'Farey Points' }
                ];
                parameters = [
                    `Rings: m = ${state.minRing}–${state.maxRing}`,
                    `Count: ${state.maxRing - state.minRing + 1}`,
                    `Spacing: ${state.ringSpacing.toFixed(2)}`,
                    `Mode: ${state.connectionMode}`
                ];
            } else if (canvasType === 'all') {
                items = [
                    { type: 'section', text: 'Key Elements' },
                    { color: CONFIG.colors.farey, text: 'Farey/Coprime' },
                    { color: CONFIG.colors.geodesic, text: 'Geodesics' },
                    { color: CONFIG.colors.cusp, text: 'Cusps' },
                    { color: CONFIG.colors.prime, text: 'Primes' },
                    { type: 'section', text: 'Boundaries' },
                    { color: CONFIG.colors.disk, text: 'Unit Circle' },
                    { color: 'rgba(255, 255, 255, 0.5)', text: 'Axes/Real' },
                    { color: 'rgba(255, 255, 255, 0.15)', text: 'Rings' },
                    { type: 'section', text: 'GCD' },
                    { color: CONFIG.colors.farey, text: 'GCD=1' },
                    { color: '#e74c3c', text: 'GCD=m' }
                ];
                parameters = [
                    `m = ${state.modulus}`,
                    `Primes: ${Math.min(state.numPrimes, state.primes.length)}`,
                    `Rings: ${state.minRing}–${state.maxRing}`,
                    `Farey: ${state.fareyPoints.map(fp => `${fp.num}/${fp.den}`).join(', ')}`
                ];
            }

            // Calculate actual legend height based on content
            const sectionCount = items.filter(i => i.type === 'section').length;
            const regularItemCount = items.filter(i => !i.type).length;
            const legendHeight = (padding * 5) + 
                                (sectionCount * itemHeight * 0.8) + 
                                (regularItemCount * itemHeight) + 
                                (parameters.length * itemHeight * 0.7) +
                                (itemHeight * 1.5); // Extra space for footer

            // Background with gradient
            const gradient = ctx.createLinearGradient(legendX, legendY, legendX, legendY + legendHeight);
            gradient.addColorStop(0, 'rgba(10, 14, 39, 0.97)');
            gradient.addColorStop(1, 'rgba(20, 30, 60, 0.97)');
            ctx.fillStyle = gradient;
            
            ctx.strokeStyle = CONFIG.colors.farey;
            ctx.lineWidth = 2.5 * scale;
            ctx.shadowBlur = 15 * scale;
            ctx.shadowColor = 'rgba(255, 215, 0, 0.4)';
            
            // Rounded rectangle
            const radius = 10 * scale;
            ctx.beginPath();
            ctx.moveTo(legendX + radius, legendY);
            ctx.lineTo(legendX + legendWidth - radius, legendY);
            ctx.quadraticCurveTo(legendX + legendWidth, legendY, legendX + legendWidth, legendY + radius);
            ctx.lineTo(legendX + legendWidth, legendY + legendHeight - radius);
            ctx.quadraticCurveTo(legendX + legendWidth, legendY + legendHeight, legendX + legendWidth - radius, legendY + legendHeight);
            ctx.lineTo(legendX + radius, legendY + legendHeight);
            ctx.quadraticCurveTo(legendX, legendY + legendHeight, legendX, legendY + legendHeight - radius);
            ctx.lineTo(legendX, legendY + radius);
            ctx.quadraticCurveTo(legendX, legendY, legendX + radius, legendY);
            ctx.closePath();
            ctx.fill();
            ctx.stroke();
            ctx.shadowBlur = 0;

            // Title
            ctx.fillStyle = CONFIG.colors.farey;
            ctx.font = `bold ${titleSize}px "Fira Code"`;
            ctx.textAlign = 'left';
            ctx.shadowBlur = 8 * scale;
            ctx.shadowColor = 'rgba(255, 215, 0, 0.6)';
            ctx.fillText('LEGEND', legendX + padding, legendY + padding * 1.5);
            ctx.shadowBlur = 0;

            // Separator line after title
            ctx.strokeStyle = 'rgba(255, 215, 0, 0.3)';
            ctx.lineWidth = 1.5 * scale;
            ctx.beginPath();
            ctx.moveTo(legendX + padding, legendY + padding * 2.2);
            ctx.lineTo(legendX + legendWidth - padding, legendY + padding * 2.2);
            ctx.stroke();

            // Items
            let currentY = legendY + padding * 3;
            
            items.forEach((item, idx) => {
                if (item.type === 'section') {
                    // Section header
                    currentY += itemHeight * 0.2;
                    ctx.fillStyle = 'rgba(0, 255, 255, 0.85)';
                    ctx.font = `bold ${sectionTitleSize}px "Fira Code"`;
                    ctx.fillText(item.text, legendX + padding, currentY);
                    
                    // Underline
                    ctx.strokeStyle = 'rgba(0, 255, 255, 0.25)';
                    ctx.lineWidth = 1 * scale;
                    ctx.beginPath();
                    ctx.moveTo(legendX + padding, currentY + 4 * scale);
                    ctx.lineTo(legendX + legendWidth - padding, currentY + 4 * scale);
                    ctx.stroke();
                    
                    currentY += itemHeight * 0.6;
                } else {
                    // Regular item with color symbol
                    ctx.fillStyle = item.color;
                    ctx.strokeStyle = 'rgba(255, 255, 255, 0.35)';
                    ctx.lineWidth = 1.2 * scale;
                    
                    // Symbol (rounded rectangle)
                    const symbolRadius = 3 * scale;
                    const symX = legendX + padding;
                    const symY = currentY - symbolSize * 0.65;
                    
                    ctx.beginPath();
                    ctx.moveTo(symX + symbolRadius, symY);
                    ctx.lineTo(symX + symbolSize - symbolRadius, symY);
                    ctx.quadraticCurveTo(symX + symbolSize, symY, symX + symbolSize, symY + symbolRadius);
                    ctx.lineTo(symX + symbolSize, symY + symbolSize - symbolRadius);
                    ctx.quadraticCurveTo(symX + symbolSize, symY + symbolSize, symX + symbolSize - symbolRadius, symY + symbolSize);
                    ctx.lineTo(symX + symbolRadius, symY + symbolSize);
                    ctx.quadraticCurveTo(symX, symY + symbolSize, symX, symY + symbolSize - symbolRadius);
                    ctx.lineTo(symX, symY + symbolRadius);
                    ctx.quadraticCurveTo(symX, symY, symX + symbolRadius, symY);
                    ctx.closePath();
                    ctx.fill();
                    ctx.stroke();

                    // Text
                    ctx.fillStyle = '#e8f1f5';
                    ctx.font = `${fontSize}px "Fira Code"`;
                    ctx.fillText(item.text, legendX + padding + symbolSize + 10 * scale, currentY);
                    
                    currentY += itemHeight;
                }
            });

            // Parameters section
            currentY += itemHeight * 0.3;
            ctx.fillStyle = 'rgba(0, 255, 255, 0.85)';
            ctx.font = `bold ${sectionTitleSize}px "Fira Code"`;
            ctx.fillText('Parameters', legendX + padding, currentY);
            
            ctx.strokeStyle = 'rgba(0, 255, 255, 0.25)';
            ctx.lineWidth = 1 * scale;
            ctx.beginPath();
            ctx.moveTo(legendX + padding, currentY + 4 * scale);
            ctx.lineTo(legendX + legendWidth - padding, currentY + 4 * scale);
            ctx.stroke();
            
            currentY += itemHeight * 0.6;

            ctx.font = `${fontSize * 0.95}px "Fira Code"`;
            ctx.fillStyle = 'rgba(255, 255, 255, 0.85)';
            parameters.forEach(param => {
                ctx.fillText('• ' + param, legendX + padding, currentY);
                currentY += itemHeight * 0.7;
            });

            // Math notation footer
            currentY += itemHeight * 0.2;
            ctx.fillStyle = 'rgba(255, 255, 255, 0.5)';
            ctx.font = `italic ${fontSize * 0.85}px "Fira Code"`;
            ctx.fillText('𝔻 → ℍ via Cayley', legendX + padding, currentY);
            currentY += itemHeight * 0.55;
            ctx.fillText('PSL(2,ℤ) Action', legendX + padding, currentY);
        }

        function printDiagnostics() {
            console.log('=== FAREY TRIANGLE & CAYLEY TRANSFORM DIAGNOSTICS ===');
            console.log('\n BASIC PARAMETERS:');
            console.log('  Modulus m:', state.modulus);
            console.log('  Phase rotation:', state.phase, 'degrees');
            console.log('  Animation speed:', state.animSpeed + '×');
            
            console.log('\n CAYLEY PLANE VIEW:');
            console.log('  Horizontal range (Re):', -state.cayleyHRange / 2, 'to', state.cayleyHRange / 2);
            console.log('  Vertical range (Im):', state.cayleyVOffset, 'to', state.cayleyVRange + state.cayleyVOffset);
            console.log('  Vertical offset:', state.cayleyVOffset);
            console.log('  Grid density:', state.cayleyGridDensity);
            
            console.log('\n⊚ NESTED RINGS:');
            console.log('  Ring range: m =', state.minRing, 'to', state.maxRing);
            console.log('  Ring spacing factor:', state.ringSpacing);
            console.log('  Total rings:', state.maxRing - state.minRing + 1);
            
            console.log('\n FAREY POINTS:');
            state.fareyPoints.forEach((fp, idx) => {
                const frac = fp.num / fp.den;
                const angle = 2 * Math.PI * frac + phase;
                const z = { re: Math.cos(angle), im: Math.sin(angle) };
                const w = cayleyTransform(z, state.useAlternateCayley);
                console.log(`  ${idx + 1}. ${fp.num}/${fp.den} = ${frac.toFixed(6)}`);
                console.log(`     Unit Disk:     z = ${z.re.toFixed(6)} + ${z.im.toFixed(6)}i`);
                console.log(`     Upper Half-Plane: w = ${w.re.toFixed(6)} + ${w.im.toFixed(6)}i`);
                console.log(`     |z| = ${Math.sqrt(z.re*z.re + z.im*z.im).toFixed(6)}`);
                console.log(`     Im(w) = ${w.im.toFixed(6)}`);
            });
            
            console.log('\n PRIME DISTRIBUTION:');
            console.log('  Total primes available:', state.primes.length);
            console.log('  Displaying:', Math.min(state.numPrimes, state.primes.length));
            console.log('  Prime limit:', state.primeLimit);
            if (state.primes.length > 0) {
                console.log('  First 10 primes:', state.primes.slice(0, 10).join(', '));
                console.log('  Last 10 primes:', state.primes.slice(-10).join(', '));
            }
            
            console.log('\n CONNECTION MODE:', state.connectionMode);
            console.log('  Thickness:', state.connectionThickness);
            console.log('  Opacity:', state.connectionOpacity);
            
            console.log('\n LABEL MODE:', state.labelMode);
            console.log('  Size:', state.labelSize + 'px');
            console.log('  Frequency: every', state.labelFreq, 'ring(s)');
            
            console.log('\n DISPLAY TOGGLES:');
            const toggles = [
                'toggleFarey', 'toggleGeodesic', 'togglePrimes', 'toggleChannels',
                'toggleCusps', 'toggleRings', 'toggleGCD', 'toggleGrid',
                'toggleFundDomain', 'toggleVerticals', 'toggleDiskOutline', 'toggleAnimate'
            ];
            toggles.forEach(id => {
                const elem = document.getElementById(id);
                if (elem) {
                    console.log('  ' + id.replace('toggle', '') + ':', elem.checked ? '✓' : '✗');
                }
            });
            
            console.log('\n CAYLEY TRANSFORM VERIFICATION:');
            console.log('  Current Mode:', state.useAlternateCayley ? 'ALTERNATE (Original)' : 'STANDARD (Correct)');
            console.log('  Standard Formula: w = i(1-z)/(1+z)');
            console.log('  Maps unit disk 𝔻 to upper half-plane ℍ');
            console.log('  Inverse: z = (i-w)/(i+w)');
            console.log('  Preserves angles (conformal)');
            
            // Test a few points
            const testPoints = [
                { re: 1, im: 0, label: '1 → ∞' },
                { re: -1, im: 0, label: '-1 → 0' },
                { re: 0, im: 1, label: 'i → i' },
                { re: 0, im: 0, label: '0 → i' }
            ];
            
            console.log('\n  Test transformations:');
            testPoints.forEach(z => {
                const w = cayleyTransform(z, state.useAlternateCayley);
                console.log(`    z = (${z.re}, ${z.im}) ${z.label}: w = ${w.re.toFixed(4)} + ${w.im.toFixed(4)}i`);
            });
            
            console.log('\n=====================================================');
        }
    </script>
</body>
</html>
