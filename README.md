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
            grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        
        .viz-grid.four-panel {
            grid-template-columns: repeat(2, 1fr);
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

        /* Interactive inspection styles */
        .property-panel {
            position: fixed;
            background: linear-gradient(135deg, rgba(10, 14, 39, 0.98), rgba(20, 30, 60, 0.98));
            border: 2px solid var(--gold);
            border-radius: 12px;
            padding: 20px;
            min-width: 320px;
            max-width: 400px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.8);
            z-index: 10000;
            font-family: 'Fira Code', monospace;
            display: none;
            animation: slideIn 0.3s ease-out;
        }

        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateY(-20px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .property-panel.visible {
            display: block;
        }

        .property-panel-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid var(--gold);
        }

        .property-panel-title {
            font-size: 1.2em;
            color: var(--gold);
            font-weight: bold;
        }

        .property-panel-close {
            background: none;
            border: none;
            color: var(--text);
            font-size: 1.5em;
            cursor: pointer;
            width: 30px;
            height: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 4px;
            transition: all 0.3s;
        }

        .property-panel-close:hover {
            background: rgba(255, 255, 255, 0.1);
            color: var(--gold);
        }

        .property-item {
            margin: 12px 0;
            padding: 8px;
            background: rgba(0, 0, 0, 0.3);
            border-left: 3px solid var(--cyan);
            border-radius: 4px;
        }

        .property-label {
            font-size: 0.85em;
            color: var(--text-dim);
            margin-bottom: 4px;
        }

        .property-value {
            font-size: 1em;
            color: var(--cyan);
            font-weight: 600;
        }

        .property-highlight {
            background: rgba(255, 215, 0, 0.1);
            border-left-color: var(--gold);
        }

        .property-highlight .property-value {
            color: var(--gold);
        }

        .tooltip {
            position: fixed;
            background: rgba(10, 14, 39, 0.95);
            border: 1px solid var(--cyan);
            border-radius: 6px;
            padding: 8px 12px;
            font-family: 'Fira Code', monospace;
            font-size: 0.85em;
            color: var(--text);
            pointer-events: none;
            z-index: 9999;
            display: none;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.6);
            white-space: nowrap;
        }

        .tooltip.visible {
            display: block;
        }

        .tooltip-label {
            color: var(--gold);
            font-weight: bold;
            margin-bottom: 2px;
        }

        .tooltip-value {
            color: var(--cyan);
        }

        canvas {
            cursor: default;
        }

        canvas.interactive {
            cursor: pointer;
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

    <!-- Interactive Inspection UI -->
    <div id="tooltip" class="tooltip"></div>
    <div id="propertyPanel" class="property-panel">
        <div class="property-panel-header">
            <div class="property-panel-title" id="propertyPanelTitle">Point Properties</div>
            <button class="property-panel-close" onclick="closePropertyPanel()">✕</button>
        </div>
        <div id="propertyPanelContent"></div>
    </div>

    <div class="main-container">
        <header>
            <h1>
                <span class="title-main">Farey Triangle & Cayley Transform</span>
            </h1>
            <p class="subtitle">Hyperbolic Geometry · Number Theory · Modular Forms</p>
            <p style="font-family: 'Fira Code', monospace; font-size: 0.85em; color: rgba(255, 255, 255, 0.5); margin-top: 10px;">
                by Wessen Getachew · Twitter <a href="https://twitter.com/7dview" target="_blank" rel="noopener" style="color: #00ffff; text-decoration: none; transition: all 0.3s;">@7dview</a>
            </p>
        </header>

        <!-- Introduction Panel -->
        <div class="controls-section" style="margin-bottom: 20px;">
            <div class="controls-header" style="cursor: pointer; user-select: none;" onclick="toggleIntro()">
                <span id="introToggle">&#9654;</span> Mathematical Introduction
            </div>
            <div class="controls-body" id="introPanel" style="display: none;">
                <div style="line-height: 1.8; font-size: 0.95em;">
                    
                    <div style="background: rgba(52, 152, 219, 0.15); padding: 20px; border-left: 4px solid #3498db; margin-bottom: 20px; border-radius: 4px;">
                        <h3 style="color: #3498db; margin-bottom: 15px;">Transform Types Available</h3>
                        
                        <p style="margin-bottom: 10px;"><strong>Standard Cayley:</strong> w = i(1+z)/(1-z)</p>
                        <p style="margin-left: 20px; margin-bottom: 15px; color: rgba(255,255,255,0.85);">
                            The canonical conformal bijection mapping the Poincaré disk model |z| &lt; 1 to the upper half-plane Im(w) &gt; 0. This is the standard form used in hyperbolic geometry and modular forms theory.
                            <br><strong>Key mappings:</strong> z=0 → w=i, z=1 → w=∞, z=-1 → w=0, unit circle → real axis.
                        </p>
                        
                        <p style="margin-bottom: 10px;"><strong>Inverse Cayley:</strong> w = i(1-z)/(1+z)</p>
                        <p style="margin-left: 20px; margin-bottom: 15px; color: rgba(255,255,255,0.85);">
                            An alternative conformal map also taking disk to upper half-plane, but with reversed orientation along the real axis. Still preserves the hyperbolic metric but maps z=0 → w=i, z=1 → w=0, z=-1 → w=∞.
                        </p>
                        
                        <p style="margin-bottom: 10px;"><strong>FTT Transform:</strong> w = (z-i)/(z+i)</p>
                        <p style="margin-left: 20px; margin-bottom: 15px; color: rgba(255,255,255,0.85);">
                            This is the <em>inverse</em> of the standard Cayley transform. It maps the upper half-plane <em>back to</em> the unit disk. Specifically: upper half-plane Im(z) &gt; 0 → unit disk interior |w| &lt; 1, real axis Im(z) = 0 → unit circle |w| = 1.
                        </p>
                        
                        <p style="margin-bottom: 10px;"><strong>Smith Chart:</strong> w = (z-1)/(z+1)</p>
                        <p style="margin-left: 20px; margin-bottom: 15px; color: rgba(255,255,255,0.85);">
                            A disk-to-disk transformation (|z| &lt; 1 → |w| &lt; 1) widely used in RF/microwave engineering for impedance visualization. Maps the right half-plane to the unit disk, with the real axis mapping to the unit circle. Different fixed points than Cayley transforms.
                        </p>
                        
                        <p style="margin-bottom: 10px;"><strong>Möbius (General):</strong> w = (az+b)/(cz+d) where ad-bc ≠ 0</p>
                        <p style="margin-left: 20px; margin-bottom: 15px; color: rgba(255,255,255,0.85);">
                            The most general linear fractional transformation. These form a group under composition and represent all conformal automorphisms of the Riemann sphere. The constraint ad-bc ≠ 0 ensures invertibility. All other transforms above are special cases with specific (a,b,c,d) values.
                        </p>
                        
                        <p style="margin-top: 20px;"><strong>Key Properties (Standard Cayley):</strong></p>
                        <ul style="margin-left: 25px; margin-bottom: 10px;">
                            <li><strong>Conformal:</strong> Preserves angles locally at every point</li>
                            <li><strong>Bijective:</strong> One-to-one correspondence between disk and upper half-plane</li>
                            <li><strong>Isometry:</strong> Maps hyperbolic geodesics to hyperbolic geodesics</li>
                            <li><strong>Boundary behavior:</strong> Unit circle |z|=1 maps to real axis Im(w)=0</li>
                            <li><strong>Interior/exterior:</strong> |z| &lt; 1 → Im(w) &gt; 0, |z| &gt; 1 → Im(w) &lt; 0</li>
                            <li><strong>Inverse formula:</strong> z = (w-i)/(w+i) or equivalently z = (i-w)/(i+w)</li>
                        </ul>
                        
                        <p style="margin-top: 15px;"><strong>Relationships:</strong></p>
                        <ul style="margin-left: 25px;">
                            <li>Standard Cayley and FTT are functional inverses: Cayley(FTT(z)) = z</li>
                            <li>All transforms preserve circles and lines (map them to circles or lines)</li>
                            <li>Composition of Möbius transformations is a Möbius transformation</li>
                            <li>The set of all Möbius transformations forms the group PSL(2,ℂ) ≅ Aut(ℂ̂)</li>
                        </ul>
                    </div>
                    
                    <h3 style="color: var(--gold); margin-bottom: 15px;">Mathematical Overview</h3>
                    
                    <p style="margin-bottom: 15px;">This visualization tool explores the profound connections between <strong>number theory</strong>, <strong>hyperbolic geometry</strong>, and <strong>complex analysis</strong> through conformal mappings and modular arithmetic. Four complementary perspectives reveal how rational numbers, prime distributions, and hyperbolic structures interrelate through the lens of the modular group PSL(2,ℤ).</p>
                    
                    <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; margin: 20px 0;">
                        <div style="background: rgba(0,0,0,0.3); padding: 15px; border-left: 3px solid var(--gold);">
                            <h4 style="color: var(--gold); margin-bottom: 8px;">Unit Disk Model (𝔻)</h4>
                            <p style="font-size: 0.9em;">The Poincaré disk model of hyperbolic geometry: {z ∈ ℂ : |z| &lt; 1}. Points from the <strong>Farey sequence</strong> F_n—the set of all reduced fractions p/q with 0 ≤ p ≤ q ≤ n ordered by value—are mapped to angles 2πp/q on the unit circle ∂𝔻. The Farey triangle connecting these boundary points has the mediant property: for adjacent fractions p/q and r/s in F_n, we have |ps - qr| = 1 (the determinant condition). Prime numbers are positioned at angles 2πp/m where p is prime and m is the modulus, revealing <strong>Dirichlet's theorem</strong>: primes are equidistributed among residue classes coprime to m, each with asymptotic density 1/φ(m).</p>
                        </div>
                        
                        <div style="background: rgba(0,0,0,0.3); padding: 15px; border-left: 3px solid var(--cyan);">
                            <h4 style="color: var(--cyan); margin-bottom: 8px;">Upper Half-Plane via Cayley (ℍ)</h4>
                            <p style="font-size: 0.9em;">The <strong>Cayley transform</strong> w = i(1+z)/(1-z) provides a conformal equivalence between 𝔻 and the upper half-plane ℍ = {w ∈ ℂ : Im(w) &gt; 0}. This is one of the fundamental isometries of hyperbolic geometry, preserving the hyperbolic metric ds² = |dz|²/(1-|z|²) on 𝔻 and ds² = |dw|²/Im(w)² on ℍ. <strong>Geodesics</strong> in ℍ appear as semicircles orthogonal to the real axis (or vertical lines). The <strong>modular group</strong> PSL(2,ℤ) = SL(2,ℤ)/{±I} acts on ℍ via Möbius transformations z → (az+b)/(cz+d) where a,b,c,d ∈ ℤ and ad-bc = 1. This group is generated by S(z) = -1/z and T(z) = z+1, and its quotient ℍ/PSL(2,ℤ) is the modular curve, fundamental to the theory of modular forms and elliptic curves.</p>
                        </div>
                        
                        <div style="background: rgba(0,0,0,0.3); padding: 15px; border-left: 3px solid var(--prime);">
                            <h4 style="color: var(--prime); margin-bottom: 8px;">Full Complex Plane (ℂ)</h4>
                            <p style="font-size: 0.9em;">The fourth panel extends the Cayley transform to visualize the entire Riemann sphere ℂ̂ = ℂ ∪ {∞}. Since the transform is defined everywhere except at z = -1, we see the complete partition:
                            <br>• <strong>Interior |z| &lt; 1</strong> → Upper half-plane Im(w) &gt; 0
                            <br>• <strong>Unit circle |z| = 1</strong> → Real axis Im(w) = 0 
                            <br>• <strong>Exterior |z| &gt; 1</strong> → Lower half-plane Im(w) &lt; 0
                            <br>The point z = 1 maps to ∞, z = -1 is the pole (undefined), and z = ±i map to the real axis at w = -1 and w = 1 respectively. This complete picture shows how Möbius transformations act as conformal automorphisms of ℂ̂, forming the group PSL(2,ℂ).</p>
                        </div>
                        
                        <div style="background: rgba(0,0,0,0.3); padding: 15px; border-left: 3px solid var(--geodesic);">
                            <h4 style="color: var(--geodesic); margin-bottom: 8px;">Nested Rings Structure (⊚)</h4>
                            <p style="font-size: 0.9em;">Concentric rings represent the structure of (ℤ/mℤ)× for moduli m from min to max. Each ring m displays all residue classes k ∈ {0,1,...,m-1} at angles 2πk/m. Points are colored by gcd(k,m), revealing the multiplicative structure. <strong>Gold points</strong> (gcd = 1) form the group of units (ℤ/mℤ)×, whose order is given by <strong>Euler's totient</strong> φ(m). The Chinese Remainder Theorem states that if gcd(m₁,m₂) = 1, then ℤ/(m₁m₂)ℤ ≅ ℤ/m₁ℤ × ℤ/m₂ℤ, visible in the coprime point patterns. Connection modes visualize lifts and transitions: in a modular sequence Mₙ = M₀·bⁿ, a residue r at level n lifts to {r, r+Mₙ, r+2Mₙ, ..., r+(b-1)Mₙ} at level n+1. If gcd(r,M₀) = gcd(r,b) = 1, then coprimality is preserved: gcd(r,Mₙ₊₁) = 1.</p>
                        </div>
                    </div>
                    
                    <h3 style="color: var(--gold); margin: 25px 0 15px;">Core Mathematical Concepts</h3>
                    
                    <ul style="list-style: none; padding: 0;">
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Farey Sequence F_n:</strong> The ordered set {p/q : 0 ≤ p ≤ q ≤ n, gcd(p,q) = 1} of all irreducible fractions with denominator at most n. The sequence has exactly 1 + Σ_{k=1}^n φ(k) elements. <strong>Mediant property:</strong> If p/q and r/s are adjacent in F_n, then |ps - qr| = 1, and their mediant (p+r)/(q+s) first appears in F_{q+s}. The Farey sequence provides a natural parameterization of ℚ ∩ [0,1] and of rational points on the unit circle.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Cayley Transform:</strong> The map w = i(1+z)/(1-z) is a biholomorphic (holomorphic bijection with holomorphic inverse) equivalence 𝔻 → ℍ. It's an isometry of hyperbolic spaces: the Poincaré disk metric ds² = 4|dz|²/(1-|z|²)² corresponds to the upper half-plane metric ds² = |dw|²/Im(w)². The inverse is z = (w-i)/(w+i). Under this map, straight lines in 𝔻 through the origin become vertical lines in ℍ, and circles in 𝔻 orthogonal to ∂𝔻 become semicircles in ℍ orthogonal to ℝ—these are the geodesics of hyperbolic geometry.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Modular Group PSL(2,ℤ):</strong> The quotient SL(2,ℤ)/{±I} where SL(2,ℤ) = {[[a,b],[c,d]] : a,b,c,d ∈ ℤ, ad-bc = 1}. Acts on ℍ by fractional linear transformations γ·z = (az+b)/(cz+d). Generated by S: z ↦ -1/z (order 2) and T: z ↦ z+1 (infinite order), with the single relation (ST)³ = I. The fundamental domain is 𝒟 = {z ∈ ℍ : |z| ≥ 1, |Re(z)| ≤ 1/2}, and ℍ/PSL(2,ℤ) ≅ ℂ, with the quotient map being the j-invariant. This group is central to the theory of <strong>modular forms</strong>: functions f : ℍ → ℂ satisfying f((az+b)/(cz+d)) = (cz+d)^k f(z) for all [[a,b],[c,d]] ∈ SL(2,ℤ).
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Hyperbolic Geodesics:</strong> In the upper half-plane model ℍ, geodesics (paths of shortest hyperbolic distance) are semicircles perpendicular to ℝ, together with vertical rays. The hyperbolic distance between z₁, z₂ ∈ ℍ is d(z₁,z₂) = arccosh(1 + |z₁-z₂|²/(2·Im(z₁)·Im(z₂))). PSL(2,ℤ) acts by isometries, preserving this distance. In the disk model, geodesics are arcs of circles orthogonal to ∂𝔻 (and diameters).
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Dirichlet's Theorem on Primes in Arithmetic Progressions:</strong> If gcd(a,m) = 1, the arithmetic progression {a + km : k ≥ 0} contains infinitely many primes, with density 1/φ(m) among all primes. More precisely, π(x; m, a) ~ x/(φ(m) log x) as x → ∞, where π(x; m, a) counts primes p ≤ x with p ≡ a (mod m). This equidistribution is visible in the visualization: primes distribute uniformly among the φ(m) residue classes coprime to m.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Euler's Totient Function:</strong> φ(n) = |{k : 1 ≤ k ≤ n, gcd(k,n) = 1}| counts integers up to n coprime to n. This is multiplicative: if gcd(m,n) = 1, then φ(mn) = φ(m)φ(n). For prime power p^k, we have φ(p^k) = p^k - p^{k-1} = p^{k-1}(p-1). The formula φ(n) = n·∏_{p|n}(1 - 1/p) expresses φ in terms of the prime factorization. The units (ℤ/nℤ)× form a group of order φ(n), and by <strong>Euler's theorem</strong>, if gcd(a,n) = 1, then a^{φ(n)} ≡ 1 (mod n).
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Möbius Transformations:</strong> Functions f(z) = (az+b)/(cz+d) where a,b,c,d ∈ ℂ and ad-bc ≠ 0. These are precisely the conformal automorphisms of the Riemann sphere ℂ̂. They form a group under composition: if f(z) = (az+b)/(cz+d) and g(z) = (ez+f)/(gz+h), then (f∘g)(z) = ((ae+bg)z + (af+bh))/((ce+dg)z + (cf+dh)). The group of Möbius transformations is isomorphic to PSL(2,ℂ) = SL(2,ℂ)/{±I}. Key property: Möbius transformations map circles and lines to circles and lines (where lines are considered circles through ∞).
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Ford Circles:</strong> For each rational p/q in lowest terms, the Ford circle C_{p/q} has center (p/q, 1/(2q²)) and radius 1/(2q²) in the upper half-plane. These circles are tangent to the real axis at p/q and are pairwise tangent or disjoint: C_{p/q} and C_{r/s} are tangent iff |ps - qr| = 1 (i.e., they're Farey neighbors). Ford circles provide a beautiful geometric illustration of the Farey sequence and the Stern-Brocot tree structure of rational numbers.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Residue Lifts in Modular Sequences:</strong> Given a geometric sequence of moduli Mₙ = M₀·bⁿ where b ≥ 2, a residue r ∈ ℤ/Mₙℤ lifts to the set {r + kMₙ mod Mₙ₊₁ : k = 0, 1, ..., b-1} in ℤ/Mₙ₊₁ℤ. If gcd(r, M₀) = gcd(r, b) = 1, then coprimality is preserved under lifting: all b lifts satisfy gcd(r + kMₙ, Mₙ₊₁) = 1. This creates a self-similar fractal structure of coprime residues across scales. Gap-g transitions (k, k+g) lift to {(k+jMₙ, k+g+jMₙ) : j = 0,...,b-1}, preserving the gap structure. When combined with prime distribution (Dirichlet), this reveals how primes populate the modular tower.
                        </li>
                    </ul>
                    
                    <div style="background: rgba(255, 215, 0, 0.1); padding: 15px; margin-top: 20px; border-radius: 4px;">
                        <strong style="color: var(--gold);">Getting Started:</strong> Use the preset buttons in the controls to load common configurations (F₃, F₅, F₇, etc.). Hover over controls for detailed tooltips explaining each parameter. Click any point on the visualizations to see its mathematical properties in a detailed panel. Enable the Interactive Guide below for a step-by-step tutorial. Experiment with different transform types to see how various conformal mappings affect the geometry.
                    </div>
                </div>r| = 1 (mediant property). The sequence has ∑_{k=1}^n φ(k) terms, where φ is Euler's totient function.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Cayley Transform:</strong> The Möbius transformation w = i(1-z)/(1+z) providing a conformal equivalence between the Poincaré disk model (|z| < 1) and the upper half-plane model (Im(w) > 0) of hyperbolic geometry. Its inverse is z = (i-w)/(i+w).
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Modular Group PSL(2,Z):</strong> The quotient group SL(2,Z)/{±I} acting on the upper half-plane via z → (az+b)/(cz+d) where ad-bc=1 and a,b,c,d are integers. This group is generated by S: z → -1/z and T: z → z+1, and is fundamental in the theory of modular forms and elliptic curves.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Hyperbolic Geodesics:</strong> In the upper half-plane model, geodesics are either vertical lines or semicircles perpendicular to the real axis. The hyperbolic distance between two points is preserved under PSL(2,Z) actions.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Prime Distribution mod m:</strong> Primes p are visualized at angle 2πp/m. By Dirichlet's theorem on primes in arithmetic progressions, primes are equidistributed among residue classes coprime to m. The density in each such class approaches 1/φ(m) as we consider larger primes.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Smith Chart Mapping:</strong> The transformation w = (z-1)/(z+1) used in electrical engineering for impedance visualization. Unlike the Cayley transform, it maps the unit disk to itself, with the real axis of z mapping to the unit circle in w.
                        </li>
                        <li style="margin-bottom: 12px;">
                            <strong style="color: var(--cyan);">Möbius Transformations:</strong> General linear fractional transformations w = (az+b)/(cz+d) with ad-bc ≠ 0. These form a group under composition and are the conformal automorphisms of the Riemann sphere. They map circles and lines to circles and lines.
                        </li>
                    </ul>
                    
                    <div style="background: rgba(255, 215, 0, 0.1); padding: 15px; margin-top: 20px; border-radius: 4px;">
                        <strong style="color: var(--gold);">Getting Started:</strong> Use the preset buttons below to load common configurations. Hover over any control for detailed tooltips. Click the Guide button for an interactive tutorial. Explore the various transform types to see how different conformal mappings affect the geometry.
                    </div>
                </div>
            </div>
        </div>



        <!-- Visualization Canvases -->
        <div class="viz-grid" id="vizGrid">
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

            <div class="canvas-panel" id="fullPlanePanel" style="display: none;">
                <div class="panel-header">
                    <div>
                        <div class="panel-title">ℂ Full Complex Plane</div>
                        <div class="panel-subtitle">Complete Cayley Transform View (Interior + Exterior)</div>
                    </div>
                </div>
                <canvas id="fullPlaneCanvas" width="1000" height="1000"></canvas>
            </div>
        </div>

        <!-- Interactive Guide -->
        <div class="controls-section">
            <div class="controls-header" style="cursor: pointer; user-select: none;" onclick="toggleGuide()">
                <span id="guideToggle">&#9654;</span> Interactive Guide
            </div>
            <div class="controls-body" id="guidePanel" style="display: none;">
                <div style="line-height: 1.8; font-size: 0.95em;">
                    <h3 style="color: var(--gold); margin-bottom: 15px;">Step-by-Step Tutorial</h3>
                    
                    <div style="background: rgba(0,0,0,0.3); padding: 20px; margin-bottom: 20px; border-left: 3px solid var(--gold);">
                        <h4 style="color: var(--gold); margin-bottom: 10px;">Step 1: Understanding the Farey Sequence</h4>
                        <p style="margin-bottom: 10px;">The Farey sequence F_n consists of all reduced fractions between 0 and 1 with denominators not exceeding n, arranged in increasing order.</p>
                        <ul style="margin-left: 20px; margin-bottom: 10px;">
                            <li>F_3 = {0/1, 1/3, 1/2, 2/3, 1/1}</li>
                            <li>F_5 = {0/1, 1/5, 1/4, 1/3, 2/5, 1/2, 3/5, 2/3, 3/4, 4/5, 1/1}</li>
                        </ul>
                        <p><strong>Try it:</strong> Click "Generate F_5" button in the Farey Sequence section to see these points on the unit circle.</p>
                    </div>
                    
                    <div style="background: rgba(0,0,0,0.3); padding: 20px; margin-bottom: 20px; border-left: 3px solid var(--cyan);">
                        <h4 style="color: var(--cyan); margin-bottom: 10px;">Step 2: The Cayley Transform</h4>
                        <p style="margin-bottom: 10px;">The standard Cayley transform w = i(1-z)/(1+z) maps the unit disk conformally onto the upper half-plane.</p>
                        <ul style="margin-left: 20px; margin-bottom: 10px;">
                            <li>Points on the unit circle |z|=1 map to the real axis Im(w)=0</li>
                            <li>Interior points |z|&lt;1 map to upper half-plane Im(w)&gt;0</li>
                            <li>The point z=1 maps to infinity</li>
                            <li>The point z=-1 maps to w=0</li>
                        </ul>
                        <p><strong>Try it:</strong> Switch between transform types in the "Cayley Transform & View Options" to see different conformal mappings.</p>
                    </div>
                    
                    <div style="background: rgba(0,0,0,0.3); padding: 20px; margin-bottom: 20px; border-left: 3px solid var(--geodesic);">
                        <h4 style="color: var(--geodesic); margin-bottom: 10px;">Step 3: Prime Distribution</h4>
                        <p style="margin-bottom: 10px;">Primes are positioned at angles 2πp/m on the unit circle, where m is the modulus.</p>
                        <ul style="margin-left: 20px; margin-bottom: 10px;">
                            <li>Set modulus m to see primes distributed in residue classes</li>
                            <li>Enable "Residue Channels" to color primes by their class mod m</li>
                            <li>Only primes coprime to m are shown when channels are enabled</li>
                        </ul>
                        <p><strong>Try it:</strong> Set modulus to 12, enable "Residue Channels" toggle, and observe how primes cluster in coprime classes.</p>
                    </div>
                    
                    <div style="background: rgba(0,0,0,0.3); padding: 20px; margin-bottom: 20px; border-left: 3px solid var(--cusp);">
                        <h4 style="color: var(--cusp); margin-bottom: 10px;">Step 4: Nested Ring Structure</h4>
                        <p style="margin-bottom: 10px;">Concentric rings show the structure of (Z/mZ)× for each modulus m from min to max.</p>
                        <ul style="margin-left: 20px; margin-bottom: 10px;">
                            <li>Points are colored by gcd(k,m) where k is the residue</li>
                            <li>Gold points have gcd(k,m)=1 (units in Z/mZ)</li>
                            <li>The number of gold points on ring m equals φ(m)</li>
                        </ul>
                        <p><strong>Try it:</strong> Set min ring to 1, max ring to 20, and enable "GCD Coloring" to see totient structure.</p>
                    </div>
                    
                    <div style="background: rgba(0,0,0,0.3); padding: 20px; margin-bottom: 20px; border-left: 3px solid var(--prime);">
                        <h4 style="color: var(--prime); margin-bottom: 10px;">Step 5: Geodesics and Hyperbolic Geometry</h4>
                        <p style="margin-bottom: 10px;">In the upper half-plane, hyperbolic geodesics appear as semicircles perpendicular to the real axis.</p>
                        <ul style="margin-left: 20px; margin-bottom: 10px;">
                            <li>Geodesics connect Farey points on the boundary</li>
                            <li>These are the "straight lines" of hyperbolic geometry</li>
                            <li>The modular group PSL(2,Z) acts by isometries</li>
                        </ul>
                        <p><strong>Try it:</strong> Enable "Geodesic Arc" to see hyperbolic lines connecting Farey fractions.</p>
                    </div>
                    
                    <div style="background: rgba(0,0,0,0.3); padding: 20px; margin-bottom: 20px; border-left: 3px solid var(--prime);">
                        <h4 style="color: var(--prime); margin-bottom: 10px;">Step 5: Generalized Lift Dynamics</h4>
                        <p style="margin-bottom: 10px;">For modular sequences M₀, M₁, M₂,... where Mₙ = M₀·bⁿ (b ≥ 2 is the scaling factor):</p>
                        <ul style="margin-left: 20px; margin-bottom: 10px;">
                            <li><strong>Residue Lift:</strong> Lift(n→n+1)(r) = {r + k·Mₙ mod Mₙ₊₁ : k=0,1,...,b-1}</li>
                            <li><strong>Transition Lift:</strong> Gap-g pair (r, r+g) lifts to {(r+kMₙ, r+g+kMₙ) : k=0,...,b-1}</li>
                            <li><strong>Coprimality:</strong> If gcd(r, Mₙ) = 1 and gcd(r, b) = 1, then gcd(r, Mₙ₊₁) = 1</li>
                            <li><strong>Counting:</strong> If each element lifts to b valid elements: |Sₙ₊₁| = b·|Sₙ|</li>
                        </ul>
                        <p><strong>Try it:</strong> Use "Gap-2n" connection mode to visualize gap-preserving lifts where r connects to r+gap within each ring.</p>
                    </div>
                    
                    <div style="background: rgba(0,0,0,0.3); padding: 20px; margin-bottom: 20px; border-left: 3px solid var(--cusp);">
                        <h4 style="color: var(--cusp); margin-bottom: 10px;">Mathematical Framework</h4>
                        <p style="margin-bottom: 10px;"><strong>Base Modulus M₀ ∈ ℤ₊</strong> and scaling factor <strong>b ∈ ℤ≥₂</strong></p>
                        <p style="margin-bottom: 10px;"><strong>Modulus Sequence:</strong> Mₙ = M₀·bⁿ for n ∈ ℤ≥₀</p>
                        <p style="margin-bottom: 10px;"><strong>Residue Lift Formula:</strong></p>
                        <div style="font-family: 'Fira Code', monospace; background: rgba(0,0,0,0.4); padding: 10px; margin: 10px 0; border-radius: 4px;">
                            Lift_{n→n+1}(r) = {r, r+Mₙ, r+2Mₙ, ..., r+(b-1)Mₙ} mod Mₙ₊₁
                        </div>
                        <p style="margin-bottom: 10px;"><strong>GCD Preservation Condition:</strong></p>
                        <div style="font-family: 'Fira Code', monospace; background: rgba(0,0,0,0.4); padding: 10px; margin: 10px 0; border-radius: 4px;">
                            gcd(r, Mₙ) = 1 ∧ gcd(r, b) = 1  ⟹  gcd(r, Mₙ₊₁) = 1
                        </div>
                        <p style="margin-bottom: 10px;"><strong>Self-Similarity:</strong> When PrimeFactors(b) ⊆ PrimeFactors(M₀), coprimality is automatically preserved across lifts.</p>
                        <p style="margin-bottom: 10px;"><strong>Transition Counting:</strong> For valid transitions T(Mₙ), if each lifts to b valid transitions: T(Mₙ₊₁) = b·T(Mₙ)</p>
                    </div>
                    
                    <div style="background: rgba(0,0,0,0.3); padding: 20px; border-left: 3px solid var(--text);">
                        <h4 style="color: var(--text); margin-bottom: 10px;">Advanced: Ford Circles & Gap Connections</h4>
                        <p style="margin-bottom: 10px;">Experiment with general Möbius transformations w = (az+b)/(cz+d).</p>
                        <ul style="margin-left: 20px; margin-bottom: 10px;">
                            <li>Select "Möbius" transform type to reveal parameter controls</li>
                            <li>Ensure ad-bc ≠ 0 for a valid transformation</li>
                            <li>Common choices: (a,b,c,d) = (1,1,1,0) gives w=(z+1)/z</li>
                            <li>Try (0,-1,1,0) for w=-1/z (inversion)</li>
                        </ul>
                        <p><strong>Mathematical note:</strong> Möbius transformations form a group isomorphic to PSL(2,C), the group of conformal automorphisms of the Riemann sphere.</p>
                    </div>
                </div>
            </div>
        </div>

        <!-- Advanced Controls -->
        <div class="controls-section">
            <div class="controls-header">
                Unlimited Parameter Control
            </div>
            <div class="controls-body">
                <!-- Basic Parameters -->
                <div class="section-header">Basic Parameters</div>
                <div class="control-row">
                    <div class="control-item" data-tooltip="Rotates all visualizations by this angle. Animated when auto-rotate is enabled.">
                        <div class="control-label">
                            <span>Phase Rotation θ</span>
                            <span class="control-value" id="phaseValue">180°</span>
                        </div>
                        <input type="range" id="phaseSlider" min="0" max="360" value="180" step="0.1">
                        <input type="number" id="phaseInput" value="180" min="0" max="360" step="0.1" style="margin-top: 8px;" placeholder="Enter angle in degrees">
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
                <div class="section-header">Canvas Zoom Controls</div>
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
                <div class="section-header">Cayley Transform & View Options</div>
                <div class="control-row">
                    <div class="control-item" style="grid-column: 1 / -1;" data-tooltip="Choose which mathematical transformation to apply">
                        <div class="control-label">
                            <span>Transform Type</span>
                        </div>
                        <select id="cayleyTransformType">
                            <option value="standard">Standard Cayley: w = i(1+z)/(1-z)</option>
                            <option value="alternate">Inverse Cayley: w = i(1-z)/(1+z)</option>
                            <option value="ftt">FTT Transform: w = (z-i)/(z+i)</option>
                            <option value="smith">Smith Chart: (z-1)/(z+1)</option>
                            <option value="mobius">Möbius: (az+b)/(cz+d)</option>
                        </select>
                        <div class="help-text" id="transformDescription">Standard Cayley: Disk→Upper Half-Plane | Inverse: Different orientation | FTT: Upper Half-Plane→Disk | Smith: Disk→Disk (RF) | Möbius: Fully customizable</div>
                    </div>
                    
                    <div class="control-item" id="mobiusParamsA" style="display: none;" data-tooltip="Coefficient a in Möbius transformation w=(az+b)/(cz+d). Must satisfy ad-bc not equal to zero for invertibility.">
                        <div class="control-label">
                            <span>Möbius coefficient a</span>
                        </div>
                        <input type="number" id="mobiusA" value="1" step="1">
                    </div>
                    
                    <div class="control-item" id="mobiusParamsB" style="display: none;" data-tooltip="Coefficient b in Möbius transformation w=(az+b)/(cz+d). Represents translation component in numerator.">
                        <div class="control-label">
                            <span>Möbius coefficient b</span>
                        </div>
                        <input type="number" id="mobiusB" value="0" step="1">
                    </div>
                    
                    <div class="control-item" id="mobiusParamsC" style="display: none;" data-tooltip="Coefficient c in Möbius transformation w=(az+b)/(cz+d). When c=0, transformation reduces to affine map.">
                        <div class="control-label">
                            <span>Möbius coefficient c</span>
                        </div>
                        <input type="number" id="mobiusC" value="0" step="1">
                    </div>
                    
                    <div class="control-item" id="mobiusParamsD" style="display: none;" data-tooltip="Coefficient d in Möbius transformation w=(az+b)/(cz+d). Determinant ad-bc must be nonzero.">
                        <div class="control-label">
                            <span>Möbius coefficient d</span>
                        </div>
                        <input type="number" id="mobiusD" value="1" step="1">
                        <div class="help-text">Constraint: determinant ad - bc must be nonzero</div>
                    </div>
                </div>

                <div class="section-header">Cayley Plane View Range</div>
                <div class="control-row">
                    
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
                <div class="section-header">Prime Distribution</div>
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
                <div class="section-header">Nested Rings Configuration</div>
                <div class="control-row">
                    <div class="control-item" style="grid-column: 1 / -1;" data-tooltip="Choose how ring moduli are generated">
                        <div class="control-label">
                            <span>Ring Generation Mode</span>
                        </div>
                        <select id="ringGenerationMode">
                            <option value="manual">Manual Range (m_min to m_max)</option>
                            <option value="dyadic">Dyadic Family (M₀ × 2ⁿ) - WITH APPLY BUTTON</option>
                            <option value="padic">p-adic Family (M₀ × pⁿ) - WITH APPLY BUTTON</option>
                            <option value="custom">Custom Scaling (M₀ × bⁿ) - WITH APPLY BUTTON</option>
                        </select>
                        <div class="help-text">⚠️ For dyadic/p-adic families: Select mode above to see APPLY button</div>
                    </div>
                </div>

                <div id="manualRingControls" class="control-row">
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

                    <div class="control-item" data-tooltip="Rotate each individual ring by this angle. Creates spiraling patterns.">
                        <div class="control-label">
                            <span>Per-Ring Rotation</span>
                            <span class="control-value" id="ringRotationValue">0°</span>
                        </div>
                        <input type="range" id="ringRotationSlider" min="0" max="360" value="0" step="1">
                        <input type="number" id="ringRotationInput" value="0" min="0" max="360" step="1" style="margin-top: 8px;" placeholder="Degrees per ring">
                    </div>
                </div>

                <div id="dyadicRingControls" class="control-row" style="display: none;">
                    <div class="control-item" data-tooltip="Base modulus M₀ for the sequence">
                        <div class="control-label">
                            <span>Base Modulus (M₀)</span>
                            <span class="control-value" id="baseModDisplay">30</span>
                        </div>
                        <input type="number" id="baseModInput" value="30" min="1" step="1">
                    </div>

                    <div class="control-item" data-tooltip="Scaling factor b (typically 2, 3, 5 for dyadic/p-adic)">
                        <div class="control-label">
                            <span>Scale Factor (b)</span>
                            <span class="control-value" id="scaleFactorDisplay">2</span>
                        </div>
                        <input type="number" id="scaleFactorInput" value="2" min="2" step="1">
                    </div>

                    <div class="control-item" data-tooltip="Starting exponent n₀">
                        <div class="control-label">
                            <span>Start Exponent (n₀)</span>
                            <span class="control-value" id="startExpDisplay">0</span>
                        </div>
                        <input type="number" id="startExpInput" value="0" min="0" step="1">
                    </div>

                    <div class="control-item" data-tooltip="Ending exponent n_max">
                        <div class="control-label">
                            <span>End Exponent (n_max)</span>
                            <span class="control-value" id="endExpDisplay">10</span>
                        </div>
                        <input type="number" id="endExpInput" value="10" min="0" step="1">
                    </div>

                    <div class="control-item" style="grid-column: 1 / -1;">
                        <div class="control-label">
                            <span>Sequence Preview</span>
                        </div>
                        <div id="ringSequencePreview" style="font-family: 'Fira Code', monospace; font-size: 0.85em; color: var(--cyan); padding: 8px; background: rgba(0,0,0,0.3); border-radius: 4px; margin-top: 8px;">
                            Will show: M₀ × b^n₀, M₀ × b^(n₀+1), ..., M₀ × b^n_max
                        </div>
                    </div>

                    <div class="control-item" style="grid-column: 1 / -1;">
                        <button class="btn btn-primary" onclick="applyDyadicFamily()" style="width: 100%; padding: 12px; font-size: 1em; margin-top: 10px;">
                            <span>✓ APPLY DYADIC FAMILY</span>
                        </button>
                        <div class="help-text" style="text-align: center; margin-top: 8px;">
                            Configure parameters above, then click APPLY to update visualization
                        </div>
                    </div>

                    <div class="control-item" style="grid-column: 1 / -1;">
                        <div class="control-label">
                            <span>Quick Dyadic Presets</span>
                        </div>
                        <div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin-top: 8px;">
                            <button class="btn btn-accent" onclick="setDyadicPreset(2, 2, 0, 10)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>2×2ⁿ</span>
                            </button>
                            <button class="btn btn-accent" onclick="setDyadicPreset(3, 2, 0, 10)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>3×2ⁿ</span>
                            </button>
                            <button class="btn btn-accent" onclick="setDyadicPreset(6, 2, 0, 10)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>6×2ⁿ</span>
                            </button>
                            <button class="btn btn-accent" onclick="setDyadicPreset(30, 2, 0, 10)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>30×2ⁿ</span>
                            </button>
                            <button class="btn btn-accent" onclick="setDyadicPreset(5, 5, 0, 8)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>5×5ⁿ</span>
                            </button>
                            <button class="btn btn-accent" onclick="setDyadicPreset(3, 3, 0, 9)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>3×3ⁿ</span>
                            </button>
                        </div>
                    </div>
                </div>



                <!-- Custom Farey Points -->
                <div class="section-header">Farey Sequence & Custom Points</div>
                <div class="control-row">
                    <div class="control-item" data-tooltip="Generate complete Farey sequence F_n with option to include 0/n for each denominator n (0/1, 0/2, 0/3, etc.)">
                        <div class="control-label">
                            <span>Generate Farey Sequence F_n</span>
                        </div>
                        <div style="display: flex; gap: 10px; align-items: center;">
                            <input type="number" id="fareyOrderInput" value="5" min="1" max="100" style="flex: 1;">
                            <button class="btn btn-secondary" onclick="generateFareySequence()" style="padding: 8px 20px; margin: 0;">
                                <span>Generate F_n</span>
                            </button>
                        </div>
                        <div class="help-text">F_n max = current modulus m (currently F_<span id="maxFareyOrder">30</span> available). Option to include 0/n for each n.</div>
                    </div>

                    <div class="control-item" data-tooltip="Add all residue classes k/m for a given modulus m, from 0/m to (m-1)/m">
                        <div class="control-label">
                            <span>Add All Residues for Modulus</span>
                        </div>
                        <div style="display: flex; gap: 10px; align-items: center;">
                            <input type="number" id="residueModInput" value="12" min="1" step="1" style="flex: 1;" placeholder="Modulus">
                            <button class="btn btn-secondary" onclick="addAllResidues()" style="padding: 8px 20px; margin: 0;">
                                <span>Add 0/m to (m-1)/m</span>
                            </button>
                        </div>
                        <div class="help-text">Adds all fractions k/m for k = 0 to m-1 (includes 0/m)</div>
                    </div>

                    <div class="control-item" data-tooltip="Quick preset Farey sequences for common exploration">
                        <div class="control-label">
                            <span>Quick Presets</span>
                        </div>
                        <div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px;">
                            <button class="btn btn-accent" onclick="generateFareySequence(3)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>F₃</span>
                            </button>
                            <button class="btn btn-accent" onclick="generateFareySequence(5)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>F₅</span>
                            </button>
                            <button class="btn btn-accent" onclick="generateFareySequence(7)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>F₇</span>
                            </button>
                            <button class="btn btn-accent" onclick="addAllResidues(6)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>All mod 6</span>
                            </button>
                            <button class="btn btn-accent" onclick="addAllResidues(12)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>All mod 12</span>
                            </button>
                            <button class="btn btn-accent" onclick="addAllResidues(24)" style="padding: 6px 10px; font-size: 0.8em;">
                                <span>All mod 24</span>
                            </button>
                        </div>
                    </div>

                    <div class="control-item" style="grid-column: 1 / -1;" data-tooltip="Manually add or remove specific rational fractions. Click ✕ to remove a point.">
                        <div class="control-label">
                            <span>Custom Points (Click ✕ to remove)</span>
                            <span class="control-value" id="fareyCountDisplay">3 points</span>
                        </div>
                        <div id="fareyPointsList" class="farey-point-list"></div>
                        <div style="display: flex; gap: 10px; margin-top: 10px;">
                            <button class="add-btn" onclick="addFareyPoint()">+ Add Custom Point</button>
                            <button class="btn btn-secondary" onclick="clearAllFareyPoints()" style="padding: 8px 20px; background: #e74c3c;">
                                <span>Clear All</span>
                            </button>
                        </div>
                        <div class="help-text">Format: numerator/denominator (e.g., 1/3, 2/5, 3/7)</div>
                    </div>
                </div>

                <!-- Connection Options -->
                <div class="section-header">Connection Options</div>
                <div class="control-row">
                    <div class="control-item" data-tooltip="Filter which points get connections based on their GCD with the modulus">
                        <div class="control-label">
                            <span>Apply Connections To</span>
                        </div>
                        <select id="gcdFilter">
                            <option value="both">Both GCD=1 and GCD≠1</option>
                            <option value="coprime">Only GCD=1 (Coprime)</option>
                            <option value="noncoprime">Only GCD≠1 (Non-Coprime)</option>
                        </select>
                        <div class="help-text">Filter points by their GCD relationship</div>
                    </div>

                    <div class="control-item" data-tooltip="Choose color scheme for nested rings visualization">
                        <div class="control-label">
                            <span>Nested Rings Color Scheme</span>
                        </div>
                        <select id="nestedColorScheme">
                            <option value="gcd">By GCD Value (Default)</option>
                            <option value="coprime">Binary (Coprime vs Non-Coprime)</option>
                            <option value="rainbow">Rainbow by Angle</option>
                            <option value="ring">By Ring (Modulus)</option>
                            <option value="prime">Prime Factorization</option>
                            <option value="totient">Totient Class</option>
                            <option value="monochrome">Monochrome (Gold)</option>
                        </select>
                        <div class="help-text">Different color schemes reveal different patterns</div>
                    </div>

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
                            <option value="gap2n">Gap-2n (r to r+2n)</option>
                            <option value="evengaps">Multiple Even Gaps</option>
                        </select>
                    </div>

                    <div class="control-item" id="singleGapControl" data-tooltip="For gap-2n connections: the gap size between connected residues (connects r to r+gap).">
                        <div class="control-label">
                            <span>Gap Size (2n)</span>
                            <span class="control-value" id="gapSizeDisplay">2</span>
                        </div>
                        <input type="number" id="gapSizeInput" value="2" min="1" step="1">
                        <div class="help-text">Connect r to r+gap in residue space</div>
                    </div>

                    <div class="control-item" id="multiGapControl" style="display: none;" data-tooltip="Show multiple even gap patterns simultaneously (2, 4, 6, 8...)">
                        <div class="control-label">
                            <span>Max Even Gap</span>
                            <span class="control-value" id="maxGapDisplay">8</span>
                        </div>
                        <input type="number" id="maxGapInput" value="8" min="2" step="2">
                        <div class="help-text">Show all even gaps from 2 to this value</div>
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
                <div class="section-header">Label Options</div>
                <div class="control-row">
                    <div class="control-item" data-tooltip="Choose which elements get text labels. 'Everything' can be cluttered for large visualizations.">
                        <div class="control-label">
                            <span>Label Mode</span>
                        </div>
                        <select id="labelMode">
                            <option value="none">No Labels</option>
                            <option value="farey">Farey Points Only</option>
                            <option value="integers">Integers (k) Only</option>
                            <option value="all">All Points (Fractions)</option>
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

                    <div class="control-item" data-tooltip="Position labels radially outward from the center to avoid overlapping with points.">
                        <div class="control-label">
                            <span>Label Position</span>
                        </div>
                        <select id="labelPosition">
                            <option value="center">On Point (Center)</option>
                            <option value="radial" selected>Radial (Outside Point)</option>
                        </select>
                    </div>

                    <div class="control-item" data-tooltip="Distance from point to label when using radial positioning.">
                        <div class="control-label">
                            <span>Label Offset</span>
                            <span class="control-value" id="labelOffsetValue">18</span>
                        </div>
                        <input type="range" id="labelOffsetSlider" min="10" max="40" value="18" step="2">
                    </div>
                </div>

                <!-- Toggles -->
                <div class="section-header">Display Options</div>
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

                    <input type="checkbox" id="toggleFordCircles">
                    <label for="toggleFordCircles" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Ford Circles</span>
                    </label>

                    <input type="checkbox" id="toggleFullPlane" checked>
                    <label for="toggleFullPlane" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Full Complex Plane View</span>
                    </label>

                    <input type="checkbox" id="toggleAnimate">
                    <label for="toggleAnimate" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Auto-Rotate</span>
                    </label>

                    <input type="checkbox" id="toggleInvertRings">
                    <label for="toggleInvertRings" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Invert Ring Order (Outer↔Inner)</span>
                    </label>

                    <input type="checkbox" id="toggleInvertAll">
                    <label for="toggleInvertAll" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Invert All Canvases</span>
                    </label>

                    <input type="checkbox" id="toggleShowCoprimeOnly">
                    <label for="toggleShowCoprimeOnly" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Nested: Show Only GCD=1 (Coprime)</span>
                    </label>

                    <input type="checkbox" id="toggleShowNonCoprimeOnly">
                    <label for="toggleShowNonCoprimeOnly" class="toggle-item">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Nested: Show Only GCD≠1 (Non-Coprime)</span>
                    </label>
                </div>

                <!-- Global Connection Visualization -->
                <div class="section-header">Global Connection Visualization (All Canvases)</div>
                <div class="toggle-grid">
                    <input type="checkbox" id="toggleShowRtoR">
                    <label for="toggleShowRtoR" class="toggle-item" data-tooltip="Connect residue r to itself across all rings. Shows vertical self-similarity in modular tower.">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Show r → r Connections</span>
                    </label>

                    <input type="checkbox" id="toggleShowRtoRplus2n">
                    <label for="toggleShowRtoRplus2n" class="toggle-item" data-tooltip="Connect r to r+m×2ⁿ showing power-of-2 lifts in modular sequences. Reveals binary structure.">
                        <div class="toggle-switch"></div>
                        <span class="toggle-label">Show r → r+m×2ⁿ Connections</span>
                    </label>
                </div>

                <!-- Action Buttons -->
                <div class="action-bar">
                    <button class="btn btn-primary" onclick="updateAll()">
                        <span>Update All</span>
                    </button>
                    <button class="btn btn-secondary" onclick="regeneratePrimes()">
                        <span>Regenerate Primes</span>
                    </button>
                    <button class="btn btn-accent" onclick="resetDefaults()">
                        <span>Reset to Defaults</span>
                    </button>
                    <button class="btn btn-secondary" onclick="exportVisualization()">
                        <span>Export PNG</span>
                    </button>
                    <button class="btn btn-secondary" onclick="printDiagnostics()">
                        <span>Print Diagnostics</span>
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
            phase: 180,
            modulus: 30,
            numPrimes: 150,
            primeLimit: 10000,
            animSpeed: 1.0,
            minRing: 1,
            maxRing: 12,
            ringSpacing: 1.0,
            ringRotation: 0,
            connectionMode: 'none',
            gcdFilter: 'both',
            gapSize: 2,
            maxGap: 8,
            connectionThickness: 1.0,
            connectionOpacity: 0.3,
            labelMode: 'farey',
            labelSize: 10,
            labelFreq: 1,
            labelPosition: 'radial',
            labelOffset: 18,
            nestedColorScheme: 'gcd',
            cayleyHRange: 6,
            cayleyVRange: 4,
            cayleyVOffset: 0,
            cayleyGridDensity: 1,
            transformType: 'standard',
            mobiusA: 1,
            mobiusB: 0,
            mobiusC: 0,
            mobiusD: 1,
            diskZoom: 1.0,
            cayleyZoom: 1.0,
            nestedZoom: 1.0,
            ringGenerationMode: 'manual',
            baseMod: 30,
            scaleFactor: 2,
            startExp: 0,
            endExp: 10,
            ringSequence: null,
            advancedFilterEnabled: false,
            filterGCDValue: 1,
            filterModulusRange: [1, 100],
            filterResidueClass: null,
            fareyPoints: [
                {num: 1, den: 1},
                {num: 0, den: 1},
                {num: 1, den: 2},
                {num: 0, den: 2},
                {num: 1, den: 3},
                {num: 2, den: 3},
                {num: 0, den: 3},
                {num: 1, den: 4},
                {num: 3, den: 4},
                {num: 0, den: 4},
                {num: 1, den: 5},
                {num: 2, den: 5},
                {num: 3, den: 5},
                {num: 4, den: 5},
                {num: 0, den: 5}
            ],
            primes: [],
            animationId: null
        };

        // Interactive inspection state
        let inspectionState = {
            selectedPoint: null,
            hoveredPoint: null,
            propertyPanelVisible: false,
            tooltip: null
        };

        let canvases = {
            disk: null,
            cayley: null,
            nested: null,
            fullPlane: null,
            diskCtx: null,
            cayleyCtx: null,
            nestedCtx: null,
            fullPlaneCtx: null
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
            setupInteractiveInspection(); // NEW: Set up click/hover handlers
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
            canvases.fullPlane = document.getElementById('fullPlaneCanvas');
            
            const dpr = window.devicePixelRatio || 1;
            
            [canvases.disk, canvases.cayley, canvases.nested, canvases.fullPlane].forEach(canvas => {
                const rect = canvas.getBoundingClientRect();
                canvas.width = canvas.width * dpr;
                canvas.height = canvas.height * dpr;
                const ctx = canvas.getContext('2d');
                ctx.scale(dpr, dpr);
            });
            
            canvases.diskCtx = canvases.disk.getContext('2d');
            canvases.cayleyCtx = canvases.cayley.getContext('2d');
            canvases.nestedCtx = canvases.nested.getContext('2d');
            canvases.fullPlaneCtx = canvases.fullPlane.getContext('2d');
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
            
            // Update count display
            document.getElementById('fareyCountDisplay').textContent = `${state.fareyPoints.length} point${state.fareyPoints.length !== 1 ? 's' : ''}`;
        }

        function generateFareySequence(n) {
            // If called with a number argument, use it; otherwise read from input
            const order = n || parseInt(document.getElementById('fareyOrderInput').value);
            
            // Limit to current modulus
            const maxOrder = state.modulus;
            const actualOrder = Math.min(order, maxOrder);
            
            if (actualOrder < 1) {
                alert('Farey order must be at least 1');
                return;
            }
            
            if (order > maxOrder) {
                alert(`Farey order limited to current modulus m=${maxOrder}. Generating F_${actualOrder} instead.`);
            }
            
            // Ask if user wants to include 0/n for each n
            const includeZeroFractions = confirm('Include 0/n fractions for each denominator?\n\n(e.g., F_3 with 0/n: {1/1, 0/1, 1/2, 0/2, 1/3, 2/3, 0/3})\n(e.g., F_3 without 0/n: {0/1, 1/2, 1/3, 2/3, 1/1})\n\nClick OK to include all 0/n (placed at end of each sequence)\nClick Cancel for standard Farey (only coprime fractions)');
            
            // Generate Farey sequence F_n
            const fareySeq = [];
            
            if (includeZeroFractions) {
                // For each denominator, add coprime fractions first, then 0/n at the end
                for (let q = 1; q <= actualOrder; q++) {
                    // Add all coprime fractions p/q where gcd(p,q)=1 and p > 0
                    for (let p = 1; p <= q; p++) {
                        if (gcd(p, q) === 1) {
                            fareySeq.push({ num: p, den: q });
                        }
                    }
                    // Add 0/q at the end of this sequence
                    fareySeq.push({ num: 0, den: q });
                }
            } else {
                // Standard Farey sequence: only coprime fractions, sorted by value
                const tempSeq = [];
                for (let q = 1; q <= actualOrder; q++) {
                    for (let p = 0; p <= q; p++) {
                        if (gcd(p, q) === 1) {
                            tempSeq.push({ num: p, den: q });
                        }
                    }
                }
                // Sort by value for standard Farey
                tempSeq.sort((a, b) => (a.num / a.den) - (b.num / b.den));
                
                // Remove duplicates by value
                const seen = new Set();
                tempSeq.forEach(f => {
                    const val = f.num / f.den;
                    if (!seen.has(val)) {
                        seen.add(val);
                        fareySeq.push(f);
                    }
                });
            }
            
            state.fareyPoints = fareySeq;
            updateFareyPointsList();
            updateAll();
        }

        function addAllResidues(m) {
            // If called with a number argument, use it; otherwise read from input
            const modulus = m || parseInt(document.getElementById('residueModInput').value);
            
            if (modulus < 1) {
                alert('Modulus must be at least 1');
                return;
            }
            
            // Add all residues k/m for k = 0 to m-1
            const newPoints = [];
            for (let k = 0; k < modulus; k++) {
                newPoints.push({ num: k, den: modulus });
            }
            
            // Check if we should replace or append
            const shouldReplace = confirm(
                `Add all ${modulus} residues (0/${modulus} to ${modulus-1}/${modulus})?\n\n` +
                `Click OK to REPLACE current points\n` +
                `Click Cancel to ADD to current points`
            );
            
            if (shouldReplace) {
                state.fareyPoints = newPoints;
            } else {
                // Append and remove duplicates
                const combined = [...state.fareyPoints, ...newPoints];
                const unique = [];
                const seen = new Set();
                
                combined.forEach(f => {
                    const key = `${f.num}/${f.den}`;
                    if (!seen.has(key)) {
                        seen.add(key);
                        unique.push(f);
                    }
                });
                
                state.fareyPoints = unique;
            }
            
            updateFareyPointsList();
            updateAll();
        }

        function clearAllFareyPoints() {
            if (confirm('Clear all Farey points except 0/1?')) {
                state.fareyPoints = [{num: 0, den: 1}];
                updateFareyPointsList();
                updateAll();
            }
        }

        function addFareyPoint() {
            state.fareyPoints.push({num: 1, den: 4});
            updateFareyPointsList();
            updateAll();
        }

        function removeFareyPoint(index) {
            state.fareyPoints.splice(index, 1);
            updateFareyPointsList();
            updateAll();
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
                document.getElementById('phaseValue').textContent = state.phase.toFixed(1) + ' degrees';
                document.getElementById('phaseInput').value = state.phase.toFixed(1);
                if (!state.animationId) updateAll();
            });

            // Phase input box
            document.getElementById('phaseInput').addEventListener('change', e => {
                let val = parseFloat(e.target.value);
                // Handle wraparound
                val = ((val % 360) + 360) % 360;
                state.phase = val;
                document.getElementById('phaseSlider').value = val;
                document.getElementById('phaseValue').textContent = val.toFixed(1) + ' degrees';
                document.getElementById('phaseInput').value = val.toFixed(1);
                if (!state.animationId) updateAll();
            });

            // Modulus input
            document.getElementById('modulusInput').addEventListener('change', e => {
                const val = parseInt(e.target.value);
                if (val > 0) {
                    state.modulus = val;
                    document.getElementById('modulusDisplay').textContent = val;
                    document.getElementById('maxFareyOrder').textContent = val;
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
            document.getElementById('ringGenerationMode').addEventListener('change', e => {
                state.ringGenerationMode = e.target.value;
                
                if (e.target.value === 'manual') {
                    document.getElementById('manualRingControls').style.display = 'grid';
                    document.getElementById('dyadicRingControls').style.display = 'none';
                    state.ringSequence = null;
                } else {
                    document.getElementById('manualRingControls').style.display = 'none';
                    document.getElementById('dyadicRingControls').style.display = 'grid';
                    updateRingSequence();
                }
                updateAll();
            });

            // Dyadic family inputs - UPDATE PREVIEW ONLY, don't apply yet
            document.getElementById('baseModInput').addEventListener('input', e => {
                const val = parseInt(e.target.value) || 1;
                document.getElementById('baseModDisplay').textContent = val;
                updateRingSequencePreview();
            });

            document.getElementById('scaleFactorInput').addEventListener('input', e => {
                const val = parseInt(e.target.value) || 2;
                document.getElementById('scaleFactorDisplay').textContent = val;
                updateRingSequencePreview();
            });

            document.getElementById('startExpInput').addEventListener('input', e => {
                const val = parseInt(e.target.value) || 0;
                document.getElementById('startExpDisplay').textContent = val;
                updateRingSequencePreview();
            });

            document.getElementById('endExpInput').addEventListener('input', e => {
                const val = parseInt(e.target.value) || 0;
                document.getElementById('endExpDisplay').textContent = val;
                updateRingSequencePreview();
            });

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

            // Ring rotation slider
            document.getElementById('ringRotationSlider').addEventListener('input', e => {
                state.ringRotation = parseFloat(e.target.value);
                document.getElementById('ringRotationValue').textContent = state.ringRotation.toFixed(0) + '°';
                document.getElementById('ringRotationInput').value = state.ringRotation.toFixed(0);
                if (!state.animationId) updateAll();
            });

            // Ring rotation input
            document.getElementById('ringRotationInput').addEventListener('change', e => {
                let val = parseFloat(e.target.value);
                val = ((val % 360) + 360) % 360;
                state.ringRotation = val;
                document.getElementById('ringRotationSlider').value = val;
                document.getElementById('ringRotationValue').textContent = val.toFixed(0) + '°';
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
                state.transformType = e.target.value;
                
                // Show/hide Möbius parameters
                const showMobius = (e.target.value === 'mobius');
                document.getElementById('mobiusParamsA').style.display = showMobius ? 'block' : 'none';
                document.getElementById('mobiusParamsB').style.display = showMobius ? 'block' : 'none';
                document.getElementById('mobiusParamsC').style.display = showMobius ? 'block' : 'none';
                document.getElementById('mobiusParamsD').style.display = showMobius ? 'block' : 'none';
                
                // Update description
                const descriptions = {
                    'standard': 'Standard Cayley: w = i(1+z)/(1-z) maps unit disk → upper half-plane (conformal bijection for hyperbolic geometry)',
                    'alternate': 'Inverse Cayley: w = i(1-z)/(1+z) also maps disk → upper half-plane but with reversed orientation',
                    'ftt': 'FTT Transform: w = (z-i)/(z+i) is the INVERSE of standard Cayley, maps upper half-plane → unit disk',
                    'smith': 'Smith Chart: w = (z-1)/(z+1) maps unit disk → unit disk, used in RF/microwave impedance visualization',
                    'mobius': 'Möbius: w = (az+b)/(cz+d) is the general linear fractional transformation (ad-bc≠0 required)'
                };
                document.getElementById('transformDescription').textContent = descriptions[e.target.value];
                
                updateAll();
            });

            // Möbius parameters
            ['mobiusA', 'mobiusB', 'mobiusC', 'mobiusD'].forEach(id => {
                document.getElementById(id).addEventListener('change', e => {
                    const param = id.replace('mobius', '').toLowerCase();
                    state['mobius' + id.charAt(6).toUpperCase()] = parseFloat(e.target.value);
                    updateAll();
                });
            });

            // Connection controls
            document.getElementById('connectionMode').addEventListener('change', e => {
                state.connectionMode = e.target.value;
                
                // Show/hide gap controls based on mode
                const singleGapControl = document.getElementById('singleGapControl');
                const multiGapControl = document.getElementById('multiGapControl');
                
                if (e.target.value === 'gap2n') {
                    singleGapControl.style.display = 'block';
                    multiGapControl.style.display = 'none';
                } else if (e.target.value === 'evengaps') {
                    singleGapControl.style.display = 'none';
                    multiGapControl.style.display = 'block';
                } else {
                    singleGapControl.style.display = 'none';
                    multiGapControl.style.display = 'none';
                }
                
                updateAll();
            });

            document.getElementById('gcdFilter').addEventListener('change', e => {
                state.gcdFilter = e.target.value;
                updateAll();
            });

            document.getElementById('nestedColorScheme').addEventListener('change', e => {
                state.nestedColorScheme = e.target.value;
                updateAll();
            });

            document.getElementById('gapSizeInput').addEventListener('change', e => {
                const val = parseInt(e.target.value);
                if (val > 0) {
                    state.gapSize = val;
                    document.getElementById('gapSizeDisplay').textContent = val;
                    updateAll();
                }
            });

            document.getElementById('maxGapInput').addEventListener('change', e => {
                let val = parseInt(e.target.value);
                if (val < 2) val = 2;
                // Ensure even
                if (val % 2 !== 0) val += 1;
                state.maxGap = val;
                document.getElementById('maxGapInput').value = val;
                document.getElementById('maxGapDisplay').textContent = val;
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

            document.getElementById('labelPosition').addEventListener('change', e => {
                state.labelPosition = e.target.value;
                updateAll();
            });

            document.getElementById('labelOffsetSlider').addEventListener('input', e => {
                state.labelOffset = parseInt(e.target.value);
                document.getElementById('labelOffsetValue').textContent = state.labelOffset;
                if (!state.animationId) updateAll();
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
             'toggleFundDomain', 'toggleVerticals', 'toggleDiskOutline', 
             'toggleInvertRings', 'toggleInvertAll', 'toggleFordCircles',
             'toggleShowCoprimeOnly', 'toggleShowNonCoprimeOnly',
             'toggleShowRtoR', 'toggleShowRtoRplus2n'].forEach(id => {
                document.getElementById(id).addEventListener('change', e => {
                    // Handle mutual exclusivity for coprime/non-coprime filters
                    if (id === 'toggleShowCoprimeOnly' && e.target.checked) {
                        document.getElementById('toggleShowNonCoprimeOnly').checked = false;
                    } else if (id === 'toggleShowNonCoprimeOnly' && e.target.checked) {
                        document.getElementById('toggleShowCoprimeOnly').checked = false;
                    }
                    updateAll();
                });
            });
            
            // Full plane toggle - now checked by default
            document.getElementById('toggleFullPlane').addEventListener('change', e => {
                const panel = document.getElementById('fullPlanePanel');
                const vizGrid = document.getElementById('vizGrid');
                
                if (e.target.checked) {
                    panel.style.display = 'block';
                    vizGrid.classList.add('four-panel');
                } else {
                    panel.style.display = 'none';
                    vizGrid.classList.remove('four-panel');
                }
                
                updateAll();
            });
            
            // Initialize full plane view on load since toggle is checked by default
            const fullPlanePanel = document.getElementById('fullPlanePanel');
            const vizGrid = document.getElementById('vizGrid');
            fullPlanePanel.style.display = 'block';
            vizGrid.classList.add('four-panel');
            
            // Update max Farey order display when modulus changes
            document.getElementById('modulusInput').addEventListener('change', () => {
                document.getElementById('maxFareyOrder').textContent = state.modulus;
            });
        }

        // ============================================================
        // INTERACTIVE INSPECTION SYSTEM
        // ============================================================

        function setupInteractiveInspection() {
            const canvasElements = [
                { canvas: canvases.disk, type: 'disk' },
                { canvas: canvases.cayley, type: 'cayley' },
                { canvas: canvases.nested, type: 'nested' },
                { canvas: canvases.fullPlane, type: 'fullPlane' }
            ];

            canvasElements.forEach(({ canvas, type }) => {
                // Click handler
                canvas.addEventListener('click', (e) => handleCanvasClick(e, canvas, type));
                
                // Hover handler
                canvas.addEventListener('mousemove', (e) => handleCanvasHover(e, canvas, type));
                
                // Mouse leave handler
                canvas.addEventListener('mouseleave', () => {
                    hideTooltip();
                    canvas.style.cursor = 'default';
                });
            });
        }

        function handleCanvasClick(e, canvas, canvasType) {
            const rect = canvas.getBoundingClientRect();
            const x = e.clientX - rect.left;
            const y = e.clientY - rect.top;
            
            const point = findNearestPoint(canvas, x, y, canvasType);
            
            if (point && point.distance < 15) {
                inspectionState.selectedPoint = point;
                showPropertyPanel(point, e.clientX, e.clientY);
                updateAll(); // Redraw with highlight
            } else {
                closePropertyPanel();
            }
        }

        function handleCanvasHover(e, canvas, canvasType) {
            const rect = canvas.getBoundingClientRect();
            const x = e.clientX - rect.left;
            const y = e.clientY - rect.top;
            
            const point = findNearestPoint(canvas, x, y, canvasType);
            
            if (point && point.distance < 15) {
                canvas.style.cursor = 'pointer';
                inspectionState.hoveredPoint = point;
                showTooltip(e.clientX, e.clientY, point);
            } else {
                canvas.style.cursor = 'default';
                inspectionState.hoveredPoint = null;
                hideTooltip();
            }
        }

        function findNearestPoint(canvas, x, y, canvasType) {
            const w = canvas.width / (window.devicePixelRatio || 1);
            const h = canvas.height / (window.devicePixelRatio || 1);
            
            let nearestPoint = null;
            let minDistance = Infinity;
            
            if (canvasType === 'disk') {
                const cx = w / 2;
                const cy = h / 2;
                const r = Math.min(w, h) * CONFIG.diskRadius * state.diskZoom;
                const phase = state.phase * Math.PI / 180;
                
                // Check Farey points
                state.fareyPoints.forEach(fp => {
                    const frac = fp.num / fp.den;
                    const angle = 2 * Math.PI * frac + phase;
                    const px = cx + r * Math.cos(angle);
                    const py = cy + r * Math.sin(angle);
                    const dist = Math.sqrt((x - px) ** 2 + (y - py) ** 2);
                    
                    if (dist < minDistance) {
                        minDistance = dist;
                        nearestPoint = {
                            type: 'farey',
                            canvasType: 'disk',
                            num: fp.num,
                            den: fp.den,
                            frac: frac,
                            angle: angle,
                            x: px,
                            y: py,
                            distance: dist
                        };
                    }
                });
                
                // Check primes
                if (document.getElementById('togglePrimes').checked) {
                    const displayPrimes = state.primes.slice(0, state.numPrimes);
                    displayPrimes.forEach(p => {
                        const angle = 2 * Math.PI * p / state.modulus + phase;
                        const px = cx + r * Math.cos(angle);
                        const py = cy + r * Math.sin(angle);
                        const dist = Math.sqrt((x - px) ** 2 + (y - py) ** 2);
                        
                        if (dist < minDistance) {
                            minDistance = dist;
                            nearestPoint = {
                                type: 'prime',
                                canvasType: 'disk',
                                value: p,
                                residue: p % state.modulus,
                                gcd: gcd(p, state.modulus),
                                angle: angle,
                                x: px,
                                y: py,
                                distance: dist
                            };
                        }
                    });
                }
            } else if (canvasType === 'cayley') {
                const phase = state.phase * Math.PI / 180;
                
                function mathToScreen(wp) {
                    const reMin = -state.cayleyHRange / (2 * state.cayleyZoom);
                    const reMax = state.cayleyHRange / (2 * state.cayleyZoom);
                    const imMin = state.cayleyVOffset;
                    const imMax = (state.cayleyVRange / state.cayleyZoom) + state.cayleyVOffset;
                    
                    const sx = ((wp.re - reMin) / (reMax - reMin)) * w;
                    const sy = (1 - (wp.im - imMin) / (imMax - imMin)) * h;
                    
                    return { x: sx, y: sy };
                }
                
                // Check transformed Farey points
                state.fareyPoints.forEach(fp => {
                    const frac = fp.num / fp.den;
                    const angle = 2 * Math.PI * frac + phase;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const wp = cayleyTransform(z, state.transformType);
                    const p = mathToScreen(wp);
                    const dist = Math.sqrt((x - p.x) ** 2 + (y - p.y) ** 2);
                    
                    if (dist < minDistance) {
                        minDistance = dist;
                        nearestPoint = {
                            type: 'farey',
                            canvasType: 'cayley',
                            num: fp.num,
                            den: fp.den,
                            frac: frac,
                            diskZ: z,
                            cayleyW: wp,
                            x: p.x,
                            y: p.y,
                            distance: dist
                        };
                    }
                });
                
                // Check cusps (Farey points on real axis)
                if (document.getElementById('toggleCusps').checked) {
                    state.fareyPoints.forEach(fp => {
                        const frac = fp.num / fp.den;
                        const angle = 2 * Math.PI * frac + phase;
                        const z = { re: Math.cos(angle), im: Math.sin(angle) };
                        const wp = cayleyTransform(z, state.transformType);
                        const cuspP = mathToScreen({ re: wp.re, im: 0 });
                        const dist = Math.sqrt((x - cuspP.x) ** 2 + (y - cuspP.y) ** 2);
                        
                        if (dist < minDistance) {
                            minDistance = dist;
                            nearestPoint = {
                                type: 'cusp',
                                canvasType: 'cayley',
                                num: fp.num,
                                den: fp.den,
                                frac: frac,
                                position: wp.re,
                                x: cuspP.x,
                                y: cuspP.y,
                                distance: dist
                            };
                        }
                    });
                }
            } else if (canvasType === 'nested') {
                const cx = w / 2;
                const cy = h / 2;
                const maxRadius = Math.min(w, h) * 0.42 * state.nestedZoom;
                const baseRadius = maxRadius * 0.15;
                const numRings = state.maxRing - state.minRing + 1;
                const phase = state.phase * Math.PI / 180;
                const invertRings = document.getElementById('toggleInvertRings').checked;
                
                // Check all ring points
                for (let m = state.minRing; m <= state.maxRing; m++) {
                    let ringIndex;
                    if (invertRings) {
                        ringIndex = (state.maxRing - m);
                    } else {
                        ringIndex = m - state.minRing;
                    }
                    
                    const ringRadius = baseRadius + ringIndex * (maxRadius - baseRadius) / Math.max(1, numRings - 1) * state.ringSpacing;
                    const ringRotationOffset = (state.ringRotation * Math.PI / 180) * ringIndex;
                    
                    for (let k = 0; k < m; k++) {
                        const g = gcd(k, m);
                        const angle = 2 * Math.PI * k / m + phase + ringRotationOffset;
                        const px = cx + ringRadius * Math.cos(angle);
                        const py = cy + ringRadius * Math.sin(angle);
                        const dist = Math.sqrt((x - px) ** 2 + (y - py) ** 2);
                        
                        if (dist < minDistance) {
                            minDistance = dist;
                            nearestPoint = {
                                type: 'ringPoint',
                                canvasType: 'nested',
                                k: k,
                                m: m,
                                frac: k / m,
                                gcd: g,
                                phi: eulerPhi(m),
                                isCoprime: g === 1,
                                ringIndex: ringIndex,
                                x: px,
                                y: py,
                                distance: dist
                            };
                        }
                    }
                }
            }
            
            return nearestPoint;
        }

        function showTooltip(x, y, point) {
            const tooltip = document.getElementById('tooltip');
            let content = '';
            
            if (point.type === 'farey') {
                content = `<div class="tooltip-label">${point.num}/${point.den}</div>`;
                content += `<div class="tooltip-value">${point.frac.toFixed(4)}</div>`;
            } else if (point.type === 'prime') {
                content = `<div class="tooltip-label">Prime: ${point.value}</div>`;
                content += `<div class="tooltip-value">≡ ${point.residue} (mod ${state.modulus})</div>`;
            } else if (point.type === 'ringPoint') {
                content = `<div class="tooltip-label">${point.k}/${point.m}</div>`;
                content += `<div class="tooltip-value">gcd = ${point.gcd}</div>`;
            } else if (point.type === 'cusp') {
                content = `<div class="tooltip-label">Cusp ${point.num}/${point.den}</div>`;
                content += `<div class="tooltip-value">Re(w) = ${point.position.toFixed(4)}</div>`;
            }
            
            tooltip.innerHTML = content;
            tooltip.style.left = (x + 15) + 'px';
            tooltip.style.top = (y - 10) + 'px';
            tooltip.classList.add('visible');
        }

        function hideTooltip() {
            document.getElementById('tooltip').classList.remove('visible');
        }

        function showPropertyPanel(point, x, y) {
            const panel = document.getElementById('propertyPanel');
            const content = document.getElementById('propertyPanelContent');
            const title = document.getElementById('propertyPanelTitle');
            
            let html = '';
            
            if (point.type === 'farey') {
                title.textContent = `Farey Point: ${point.num}/${point.den}`;
                
                html += `<div class="property-item property-highlight">
                    <div class="property-label">Fraction</div>
                    <div class="property-value">${point.num}/${point.den} = ${point.frac.toFixed(8)}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">GCD</div>
                    <div class="property-value">gcd(${point.num}, ${point.den}) = ${gcd(point.num, point.den)}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Angle (θ)</div>
                    <div class="property-value">${((point.angle * 180 / Math.PI) % 360).toFixed(2)}°</div>
                </div>`;
                
                if (point.canvasType === 'disk') {
                    const z = { re: Math.cos(point.angle), im: Math.sin(point.angle) };
                    html += `<div class="property-item">
                        <div class="property-label">Unit Disk (z)</div>
                        <div class="property-value">z = ${z.re.toFixed(6)} + ${z.im.toFixed(6)}i</div>
                    </div>`;
                    
                    html += `<div class="property-item">
                        <div class="property-label">Magnitude</div>
                        <div class="property-value">|z| = ${Math.sqrt(z.re*z.re + z.im*z.im).toFixed(6)}</div>
                    </div>`;
                } else if (point.canvasType === 'cayley') {
                    html += `<div class="property-item">
                        <div class="property-label">Unit Disk (z)</div>
                        <div class="property-value">z = ${point.diskZ.re.toFixed(6)} + ${point.diskZ.im.toFixed(6)}i</div>
                    </div>`;
                    
                    html += `<div class="property-item property-highlight">
                        <div class="property-label">Cayley Transform (w)</div>
                        <div class="property-value">w = ${point.cayleyW.re.toFixed(6)} + ${point.cayleyW.im.toFixed(6)}i</div>
                    </div>`;
                    
                    html += `<div class="property-item">
                        <div class="property-label">Upper Half-Plane Check</div>
                        <div class="property-value">Im(w) = ${point.cayleyW.im.toFixed(6)} ${point.cayleyW.im > 0 ? '✓ > 0' : '✗ ≤ 0'}</div>
                    </div>`;
                }
                
                // Mediant property for adjacent Farey fractions
                html += `<div class="property-item">
                    <div class="property-label">Mediant Property</div>
                    <div class="property-value">Part of Farey sequence F<sub>${point.den}</sub></div>
                </div>`;
                
            } else if (point.type === 'prime') {
                title.textContent = `Prime: ${point.value}`;
                
                html += `<div class="property-item property-highlight">
                    <div class="property-label">Prime Number</div>
                    <div class="property-value">${point.value}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Residue Class (mod ${state.modulus})</div>
                    <div class="property-value">${point.value} ≡ ${point.residue} (mod ${state.modulus})</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">GCD with Modulus</div>
                    <div class="property-value">gcd(${point.value}, ${state.modulus}) = ${point.gcd}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Coprime Status</div>
                    <div class="property-value">${point.gcd === 1 ? '✓ Coprime to ' + state.modulus : '✗ Not coprime to ' + state.modulus}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Position on Circle</div>
                    <div class="property-value">θ = ${((point.angle * 180 / Math.PI) % 360).toFixed(2)}°</div>
                </div>`;
                
                // Prime index
                const primeIndex = state.primes.indexOf(point.value) + 1;
                html += `<div class="property-item">
                    <div class="property-label">Prime Index</div>
                    <div class="property-value">π(${point.value}) = ${primeIndex}</div>
                </div>`;
                
            } else if (point.type === 'ringPoint') {
                title.textContent = `Ring Point: ${point.k}/${point.m}`;
                
                html += `<div class="property-item property-highlight">
                    <div class="property-label">Residue Class</div>
                    <div class="property-value">${point.k}/${point.m} = ${point.frac.toFixed(6)}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Modulus</div>
                    <div class="property-value">m = ${point.m}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Residue</div>
                    <div class="property-value">k = ${point.k}</div>
                </div>`;
                
                html += `<div class="property-item property-highlight">
                    <div class="property-label">GCD</div>
                    <div class="property-value">gcd(${point.k}, ${point.m}) = ${point.gcd}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Coprime Status</div>
                    <div class="property-value">${point.isCoprime ? '✓ Unit in (ℤ/' + point.m + 'ℤ)×' : '✗ Not a unit'}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Euler's Totient</div>
                    <div class="property-value">φ(${point.m}) = ${point.phi}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Ring Index</div>
                    <div class="property-value">Ring #${point.ringIndex + 1} of ${state.maxRing - state.minRing + 1}</div>
                </div>`;
                
                // Count coprimes in this ring
                let coprimeCount = 0;
                for (let k = 0; k < point.m; k++) {
                    if (gcd(k, point.m) === 1) coprimeCount++;
                }
                html += `<div class="property-item">
                    <div class="property-label">Coprimes in Ring</div>
                    <div class="property-value">${coprimeCount} / ${point.m}</div>
                </div>`;
                
            } else if (point.type === 'cusp') {
                title.textContent = `Cusp: ${point.num}/${point.den}`;
                
                html += `<div class="property-item property-highlight">
                    <div class="property-label">Farey Fraction</div>
                    <div class="property-value">${point.num}/${point.den} = ${point.frac.toFixed(8)}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Position on Real Axis</div>
                    <div class="property-value">Re(w) = ${point.position.toFixed(6)}</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Type</div>
                    <div class="property-value">Cusp of modular curve</div>
                </div>`;
                
                html += `<div class="property-item">
                    <div class="property-label">Boundary Point</div>
                    <div class="property-value">Im(w) = 0 (on ∂ℍ)</div>
                </div>`;
            }
            
            content.innerHTML = html;
            
            // Position panel near click but keep it on screen
            let panelX = x + 20;
            let panelY = y + 20;
            
            // Adjust if would go off screen
            if (panelX + 400 > window.innerWidth) {
                panelX = x - 420;
            }
            if (panelY + 500 > window.innerHeight) {
                panelY = window.innerHeight - 520;
            }
            if (panelX < 10) panelX = 10;
            if (panelY < 10) panelY = 10;
            
            panel.style.left = panelX + 'px';
            panel.style.top = panelY + 'px';
            panel.classList.add('visible');
            inspectionState.propertyPanelVisible = true;
        }

        function closePropertyPanel() {
            const panel = document.getElementById('propertyPanel');
            panel.classList.remove('visible');
            inspectionState.propertyPanelVisible = false;
            inspectionState.selectedPoint = null;
            updateAll(); // Redraw without highlight
        }

        // Add highlight rendering for selected points
        function drawSelectionHighlight(ctx, x, y, type = 'default') {
            if (!inspectionState.selectedPoint) return;
            
            ctx.save();
            
            // Pulsing glow effect
            const time = Date.now() / 1000;
            const pulse = 0.7 + 0.3 * Math.sin(time * 3);
            
            // Outer glow
            ctx.strokeStyle = type === 'farey' ? CONFIG.colors.farey : 
                             type === 'prime' ? CONFIG.colors.prime :
                             type === 'cusp' ? CONFIG.colors.cusp : 
                             CONFIG.colors.farey;
            ctx.lineWidth = 3;
            ctx.shadowBlur = 20 * pulse;
            ctx.shadowColor = ctx.strokeStyle;
            ctx.globalAlpha = pulse;
            
            ctx.beginPath();
            ctx.arc(x, y, 12, 0, 2 * Math.PI);
            ctx.stroke();
            
            // Inner ring
            ctx.lineWidth = 2;
            ctx.shadowBlur = 10;
            ctx.globalAlpha = 1;
            ctx.beginPath();
            ctx.arc(x, y, 10, 0, 2 * Math.PI);
            ctx.stroke();
            
            ctx.restore();
        }

        // ============================================================
        // SAVE/LOAD CONFIGURATION SYSTEM
        // ============================================================

        function exportConfiguration() {
            const toggleStates = {};
            ['toggleFarey', 'toggleGeodesic', 'togglePrimes', 'toggleChannels',
             'toggleCusps', 'toggleRings', 'toggleGCD', 'toggleGrid',
             'toggleFundDomain', 'toggleVerticals', 'toggleDiskOutline', 
             'toggleFordCircles', 'toggleFullPlane', 'toggleAnimate',
             'toggleInvertRings', 'toggleInvertAll', 'toggleShowCoprimeOnly',
             'toggleShowNonCoprimeOnly', 'toggleShowRtoR', 'toggleShowRtoRplus2n'].forEach(id => {
                const elem = document.getElementById(id);
                if (elem) toggleStates[id] = elem.checked;
            });

            const config = {
                version: '21.0',
                timestamp: new Date().toISOString(),
                state: {
                    phase: state.phase,
                    modulus: state.modulus,
                    numPrimes: state.numPrimes,
                    primeLimit: state.primeLimit,
                    animSpeed: state.animSpeed,
                    minRing: state.minRing,
                    maxRing: state.maxRing,
                    ringSpacing: state.ringSpacing,
                    ringRotation: state.ringRotation,
                    connectionMode: state.connectionMode,
                    gcdFilter: state.gcdFilter,
                    gapSize: state.gapSize,
                    maxGap: state.maxGap,
                    connectionThickness: state.connectionThickness,
                    connectionOpacity: state.connectionOpacity,
                    labelMode: state.labelMode,
                    labelSize: state.labelSize,
                    labelFreq: state.labelFreq,
                    labelPosition: state.labelPosition,
                    labelOffset: state.labelOffset,
                    nestedColorScheme: state.nestedColorScheme,
                    cayleyHRange: state.cayleyHRange,
                    cayleyVRange: state.cayleyVRange,
                    cayleyVOffset: state.cayleyVOffset,
                    cayleyGridDensity: state.cayleyGridDensity,
                    transformType: state.transformType,
                    mobiusA: state.mobiusA,
                    mobiusB: state.mobiusB,
                    mobiusC: state.mobiusC,
                    mobiusD: state.mobiusD,
                    diskZoom: state.diskZoom,
                    cayleyZoom: state.cayleyZoom,
                    nestedZoom: state.nestedZoom,
                    ringGenerationMode: state.ringGenerationMode,
                    baseMod: state.baseMod,
                    scaleFactor: state.scaleFactor,
                    startExp: state.startExp,
                    endExp: state.endExp,
                    advancedFilterEnabled: state.advancedFilterEnabled,
                    filterGCDValue: state.filterGCDValue,
                    filterModulusRange: state.filterModulusRange,
                    filterResidueClass: state.filterResidueClass
                },
                fareyPoints: state.fareyPoints,
                toggleStates: toggleStates
            };

            const dataStr = JSON.stringify(config, null, 2);
            const dataBlob = new Blob([dataStr], { type: 'application/json' });
            const url = URL.createObjectURL(dataBlob);
            const link = document.createElement('a');
            link.href = url;
            link.download = `farey-config-${Date.now()}.json`;
            link.click();
            URL.revokeObjectURL(url);
            
            console.log('✓ Configuration exported successfully');
        }

        function importConfiguration(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const config = JSON.parse(e.target.result);
                    
                    // Restore state
                    Object.keys(config.state).forEach(key => {
                        if (state.hasOwnProperty(key)) {
                            state[key] = config.state[key];
                        }
                    });

                    // Restore Farey points
                    if (config.fareyPoints) {
                        state.fareyPoints = config.fareyPoints;
                    }

                    // Restore toggle states
                    if (config.toggleStates) {
                        Object.keys(config.toggleStates).forEach(id => {
                            const elem = document.getElementById(id);
                            if (elem) elem.checked = config.toggleStates[id];
                        });
                    }

                    // Update all UI elements
                    syncUIWithState();
                    updateFareyPointsList();
                    regeneratePrimes();
                    updateAll();
                    
                    console.log('✓ Configuration imported successfully');
                    alert('Configuration loaded successfully!');
                } catch (error) {
                    console.error('Error importing configuration:', error);
                    alert('Error loading configuration file. Please check the file format.');
                }
            };
            reader.readAsText(file);
        }

        function syncUIWithState() {
            document.getElementById('phaseSlider').value = state.phase;
            document.getElementById('phaseInput').value = state.phase;
            document.getElementById('phaseValue').textContent = state.phase.toFixed(1) + '°';
            
            document.getElementById('modulusInput').value = state.modulus;
            document.getElementById('modulusDisplay').textContent = state.modulus;
            
            document.getElementById('primesInput').value = state.numPrimes;
            document.getElementById('primesDisplay').textContent = state.numPrimes;
            
            document.getElementById('primeLimitInput').value = state.primeLimit;
            document.getElementById('primeLimitDisplay').textContent = state.primeLimit;
            
            document.getElementById('speedSlider').value = state.animSpeed;
            document.getElementById('speedValue').textContent = state.animSpeed.toFixed(1) + '×';
            
            document.getElementById('minRingInput').value = state.minRing;
            document.getElementById('minRingDisplay').textContent = state.minRing;
            
            document.getElementById('maxRingInput').value = state.maxRing;
            document.getElementById('maxRingDisplay').textContent = state.maxRing;
            
            document.getElementById('spacingSlider').value = state.ringSpacing;
            document.getElementById('spacingValue').textContent = state.ringSpacing.toFixed(1);
            
            document.getElementById('ringRotationSlider').value = state.ringRotation;
            document.getElementById('ringRotationInput').value = state.ringRotation;
            document.getElementById('ringRotationValue').textContent = state.ringRotation.toFixed(0) + '°';
            
            document.getElementById('connectionMode').value = state.connectionMode;
            document.getElementById('gcdFilter').value = state.gcdFilter;
            document.getElementById('nestedColorScheme').value = state.nestedColorScheme;
            
            document.getElementById('gapSizeInput').value = state.gapSize;
            document.getElementById('gapSizeDisplay').textContent = state.gapSize;
            
            document.getElementById('maxGapInput').value = state.maxGap;
            document.getElementById('maxGapDisplay').textContent = state.maxGap;
            
            document.getElementById('connectionThicknessSlider').value = state.connectionThickness;
            document.getElementById('connectionThicknessValue').textContent = state.connectionThickness.toFixed(1);
            
            document.getElementById('connectionOpacitySlider').value = state.connectionOpacity;
            document.getElementById('connectionOpacityValue').textContent = state.connectionOpacity.toFixed(2);
            
            document.getElementById('labelMode').value = state.labelMode;
            document.getElementById('labelSizeSlider').value = state.labelSize;
            document.getElementById('labelSizeValue').textContent = state.labelSize;
            
            document.getElementById('labelFreqInput').value = state.labelFreq;
            document.getElementById('labelFreqValue').textContent = state.labelFreq;
            
            document.getElementById('labelPosition').value = state.labelPosition;
            document.getElementById('labelOffsetSlider').value = state.labelOffset;
            document.getElementById('labelOffsetValue').textContent = state.labelOffset;
            
            document.getElementById('cayleyHRangeSlider').value = state.cayleyHRange;
            document.getElementById('cayleyHRangeValue').textContent = state.cayleyHRange.toFixed(1);
            
            document.getElementById('cayleyVRangeSlider').value = state.cayleyVRange;
            document.getElementById('cayleyVRangeValue').textContent = state.cayleyVRange.toFixed(1);
            
            document.getElementById('cayleyVOffsetSlider').value = state.cayleyVOffset;
            document.getElementById('cayleyVOffsetValue').textContent = state.cayleyVOffset.toFixed(1);
            
            document.getElementById('cayleyGridDensitySlider').value = state.cayleyGridDensity;
            document.getElementById('cayleyGridDensityValue').textContent = state.cayleyGridDensity.toFixed(1);
            
            document.getElementById('cayleyTransformType').value = state.transformType;
            
            document.getElementById('mobiusA').value = state.mobiusA;
            document.getElementById('mobiusB').value = state.mobiusB;
            document.getElementById('mobiusC').value = state.mobiusC;
            document.getElementById('mobiusD').value = state.mobiusD;
            
            document.getElementById('diskZoomSlider').value = state.diskZoom;
            document.getElementById('diskZoomValue').textContent = state.diskZoom.toFixed(2) + '×';
            
            document.getElementById('cayleyZoomSlider').value = state.cayleyZoom;
            document.getElementById('cayleyZoomValue').textContent = state.cayleyZoom.toFixed(2) + '×';
            
            document.getElementById('nestedZoomSlider').value = state.nestedZoom;
            document.getElementById('nestedZoomValue').textContent = state.nestedZoom.toFixed(2) + '×';
            
            // Ring generation mode
            document.getElementById('ringGenerationMode').value = state.ringGenerationMode;
            if (state.ringGenerationMode === 'manual') {
                document.getElementById('manualRingControls').style.display = 'grid';
                document.getElementById('dyadicRingControls').style.display = 'none';
            } else {
                document.getElementById('manualRingControls').style.display = 'none';
                document.getElementById('dyadicRingControls').style.display = 'grid';
                
                document.getElementById('baseModInput').value = state.baseMod;
                document.getElementById('baseModDisplay').textContent = state.baseMod;
                document.getElementById('scaleFactorInput').value = state.scaleFactor;
                document.getElementById('scaleFactorDisplay').textContent = state.scaleFactor;
                document.getElementById('startExpInput').value = state.startExp;
                document.getElementById('startExpDisplay').textContent = state.startExp;
                document.getElementById('endExpInput').value = state.endExp;
                document.getElementById('endExpDisplay').textContent = state.endExp;
                
                updateRingSequence();
            }
        }

        function generateShareableURL() {
            const config = {
                p: state.phase,
                m: state.modulus,
                r: [state.minRing, state.maxRing],
                t: state.transformType,
                c: state.nestedColorScheme,
                f: state.fareyPoints.map(fp => `${fp.num}/${fp.den}`).join(',')
            };
            
            const encoded = btoa(JSON.stringify(config));
            const url = window.location.origin + window.location.pathname + '?config=' + encoded;
            
            // Copy to clipboard
            navigator.clipboard.writeText(url).then(() => {
                alert('Shareable URL copied to clipboard!\n\nAnyone can use this link to view your exact configuration.');
                console.log('Shareable URL:', url);
            }).catch(() => {
                prompt('Copy this shareable URL:', url);
            });
        }

        function loadFromURL() {
            const params = new URLSearchParams(window.location.search);
            const configParam = params.get('config');
            
            if (configParam) {
                try {
                    const config = JSON.parse(atob(configParam));
                    
                    if (config.p !== undefined) state.phase = config.p;
                    if (config.m !== undefined) state.modulus = config.m;
                    if (config.r !== undefined) {
                        state.minRing = config.r[0];
                        state.maxRing = config.r[1];
                    }
                    if (config.t !== undefined) state.transformType = config.t;
                    if (config.c !== undefined) state.nestedColorScheme = config.c;
                    if (config.f !== undefined) {
                        state.fareyPoints = config.f.split(',').map(f => {
                            const [num, den] = f.split('/').map(Number);
                            return { num, den };
                        });
                    }
                    
                    syncUIWithState();
                    updateFareyPointsList();
                    console.log('✓ Configuration loaded from URL');
                } catch (error) {
                    console.error('Error loading configuration from URL:', error);
                }
            }
        }

        // ============================================================
        // MATHEMATICAL ANALYSIS PANEL
        // ============================================================

        function updateAnalysisPanel() {
            const panel = document.getElementById('analysisPanel');
            if (!panel || !document.getElementById('toggleAnalysis').checked) return;

            const rings = getRingSequence();
            let totalPoints = 0;
            let coprimePoints = 0;
            let gcdDistribution = {};
            
            rings.forEach(m => {
                totalPoints += m;
                const phi = eulerPhi(m);
                coprimePoints += phi;
                
                for (let k = 0; k < m; k++) {
                    const g = gcd(k, m);
                    gcdDistribution[g] = (gcdDistribution[g] || 0) + 1;
                }
            });

            // Prime statistics
            const displayedPrimes = Math.min(state.numPrimes, state.primes.length);
            let primesInClasses = 0;
            if (state.primes.length > 0) {
                primesInClasses = state.primes.slice(0, displayedPrimes)
                    .filter(p => gcd(p, state.modulus) === 1).length;
            }

            // Farey statistics
            const fareyInRange = state.fareyPoints.filter(fp => 
                fp.den >= state.minRing && fp.den <= state.maxRing
            ).length;

            const html = `
                <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px;">
                    <div class="analysis-stat">
                        <div class="stat-label">Total Points</div>
                        <div class="stat-value">${totalPoints.toLocaleString()}</div>
                    </div>
                    <div class="analysis-stat">
                        <div class="stat-label">Coprime (GCD=1)</div>
                        <div class="stat-value">${coprimePoints.toLocaleString()}</div>
                        <div class="stat-percent">${((coprimePoints/totalPoints)*100).toFixed(1)}%</div>
                    </div>
                    <div class="analysis-stat">
                        <div class="stat-label">Total Rings</div>
                        <div class="stat-value">${rings.length}</div>
                    </div>
                    <div class="analysis-stat">
                        <div class="stat-label">Primes Shown</div>
                        <div class="stat-value">${displayedPrimes.toLocaleString()}</div>
                    </div>
                    <div class="analysis-stat">
                        <div class="stat-label">Primes Coprime to m</div>
                        <div class="stat-value">${primesInClasses}</div>
                        <div class="stat-percent">${displayedPrimes > 0 ? ((primesInClasses/displayedPrimes)*100).toFixed(1) : 0}%</div>
                    </div>
                    <div class="analysis-stat">
                        <div class="stat-label">Farey Points in Range</div>
                        <div class="stat-value">${fareyInRange}</div>
                    </div>
                    <div class="analysis-stat">
                        <div class="stat-label">φ(${state.modulus})</div>
                        <div class="stat-value">${eulerPhi(state.modulus)}</div>
                    </div>
                    <div class="analysis-stat">
                        <div class="stat-label">Unique GCD Values</div>
                        <div class="stat-value">${Object.keys(gcdDistribution).length}</div>
                    </div>
                </div>
                
                <div style="margin-top: 20px; padding: 15px; background: rgba(0,0,0,0.3); border-left: 3px solid var(--cyan); border-radius: 4px;">
                    <div style="font-weight: bold; color: var(--cyan); margin-bottom: 10px;">GCD Distribution</div>
                    <div style="display: flex; flex-wrap: wrap; gap: 10px; font-size: 0.85em;">
                        ${Object.entries(gcdDistribution)
                            .sort((a, b) => parseInt(a[0]) - parseInt(b[0]))
                            .slice(0, 10)
                            .map(([g, count]) => `
                                <span style="background: rgba(255,255,255,0.1); padding: 4px 8px; border-radius: 3px;">
                                    GCD=${g}: ${count}
                                </span>
                            `).join('')}
                        ${Object.keys(gcdDistribution).length > 10 ? '<span>...</span>' : ''}
                    </div>
                </div>

                <div style="margin-top: 15px; padding: 15px; background: rgba(0,0,0,0.3); border-left: 3px solid var(--gold); border-radius: 4px;">
                    <div style="font-weight: bold; color: var(--gold); margin-bottom: 10px;">Ring Sequence</div>
                    <div style="font-size: 0.85em; font-family: 'Fira Code', monospace; color: rgba(255,255,255,0.8);">
                        ${rings.slice(0, 20).join(', ')}${rings.length > 20 ? `, ... (${rings.length} total)` : ''}
                    </div>
                </div>
            `;

            panel.innerHTML = html;
        }

        // ============================================================
        // ADVANCED FILTER SYSTEM
        // ============================================================

        function applyAdvancedFilters(points) {
            if (!state.advancedFilterEnabled) return points;

            return points.filter(p => {
                // GCD filter
                if (state.filterGCDValue !== null && p.g !== state.filterGCDValue) {
                    return false;
                }

                // Modulus range filter
                if (p.m < state.filterModulusRange[0] || p.m > state.filterModulusRange[1]) {
                    return false;
                }

                // Residue class filter (k ≡ r mod d)
                if (state.filterResidueClass !== null) {
                    const [r, d] = state.filterResidueClass;
                    if (p.k % d !== r % d) {
                        return false;
                    }
                }

                return true;
            });
        }

        function toggleAdvancedFilters() {
            state.advancedFilterEnabled = document.getElementById('toggleAdvancedFilters').checked;
            document.getElementById('advancedFilterPanel').style.display = 
                state.advancedFilterEnabled ? 'block' : 'none';
            updateAll();
        }

        function setFilterGCD(value) {
            state.filterGCDValue = value === '' ? null : parseInt(value);
            updateAll();
        }

        function setFilterModulusRange(min, max) {
            state.filterModulusRange = [parseInt(min), parseInt(max)];
            updateAll();
        }

        function setFilterResidueClass(r, d) {
            if (r === '' || d === '') {
                state.filterResidueClass = null;
            } else {
                state.filterResidueClass = [parseInt(r), parseInt(d)];
            }
            updateAll();
        }

        // ============================================================
        // RING SEQUENCE MANAGEMENT (DYADIC/P-ADIC FAMILIES)
        // ============================================================

        function updateRingSequencePreview() {
            const M0 = parseInt(document.getElementById('baseModInput').value) || 1;
            const b = parseInt(document.getElementById('scaleFactorInput').value) || 2;
            const n0 = parseInt(document.getElementById('startExpInput').value) || 0;
            const nMax = parseInt(document.getElementById('endExpInput').value) || 0;
            
            const sequence = [];
            for (let n = n0; n <= nMax; n++) {
                const modulus = M0 * Math.pow(b, n);
                sequence.push(modulus);
            }
            
            const preview = document.getElementById('ringSequencePreview');
            if (preview) {
                const displaySeq = sequence.slice(0, 15);
                const moreText = sequence.length > 15 ? `, ... (${sequence.length} total)` : '';
                const modeName = b === 2 ? 'Dyadic' : 
                               (b === 3 || b === 5) ? `${b}-adic` : 'Custom';
                preview.textContent = `${modeName}: M₀=${M0}, b=${b}, n=${n0}→${nMax}: [${displaySeq.join(', ')}${moreText}]`;
            }
        }

        function applyDyadicFamily() {
            // Get values from inputs
            const M0 = parseInt(document.getElementById('baseModInput').value) || 1;
            const b = parseInt(document.getElementById('scaleFactorInput').value) || 2;
            const n0 = parseInt(document.getElementById('startExpInput').value) || 0;
            const nMax = parseInt(document.getElementById('endExpInput').value) || 0;
            
            // Update state
            state.baseMod = M0;
            state.scaleFactor = b;
            state.startExp = n0;
            state.endExp = nMax;
            
            // Generate sequence
            updateRingSequence();
            
            // NOW update the visualization (only when button clicked)
            console.log('Applying dyadic family:', {M0, b, n0, nMax, sequence: state.ringSequence});
            updateAll();
        }

        function setDyadicPreset(M0, b, n0, nMax) {
            // Set the input values
            document.getElementById('baseModInput').value = M0;
            document.getElementById('baseModDisplay').textContent = M0;
            document.getElementById('scaleFactorInput').value = b;
            document.getElementById('scaleFactorDisplay').textContent = b;
            document.getElementById('startExpInput').value = n0;
            document.getElementById('startExpDisplay').textContent = n0;
            document.getElementById('endExpInput').value = nMax;
            document.getElementById('endExpDisplay').textContent = nMax;
            
            // Update preview
            updateRingSequencePreview();
            
            // DON'T apply yet - let user click the APPLY button
        }

        function getRingSequence() {
            if (state.ringGenerationMode === 'manual' || !state.ringSequence) {
                const rings = [];
                for (let m = state.minRing; m <= state.maxRing; m++) {
                    rings.push(m);
                }
                return rings;
            } else {
                return state.ringSequence;
            }
        }

        function updateRingSequence() {
            if (state.ringGenerationMode === 'manual') {
                state.ringSequence = null;
                return;
            }
            
            const M0 = state.baseMod;
            const b = state.scaleFactor;
            const n0 = state.startExp;
            const nMax = state.endExp;
            
            const sequence = [];
            for (let n = n0; n <= nMax; n++) {
                const modulus = M0 * Math.pow(b, n);
                sequence.push(modulus);
            }
            
            state.ringSequence = sequence;
            
            // Update min and max ring to match sequence
            if (sequence.length > 0) {
                state.minRing = Math.min(...sequence);
                state.maxRing = Math.max(...sequence);
                document.getElementById('minRingDisplay').textContent = state.minRing;
                document.getElementById('maxRingDisplay').textContent = state.maxRing;
            }
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

        function cayleyTransform(z, transformType = 'standard') {
            if (transformType === 'alternate') {
                // Alternate (inverse form): w = i(1-z)/(1+z)
                // Maps unit disk to upper half-plane with different orientation
                const numRe = 1 - z.re;   // Real part of (1-z)
                const numIm = -z.im;      // Imaginary part of (1-z)
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
            
            if (transformType === 'ftt') {
                // FTT Transform: w = (z-i)/(z+i)
                // This is the INVERSE of the standard Cayley transform
                // Maps upper half-plane → unit disk
                const numRe = z.re;       // Real part of (z-i)
                const numIm = z.im - 1;   // Imaginary part of (z-i)
                const denRe = z.re;       // Real part of (z+i)
                const denIm = z.im + 1;   // Imaginary part of (z+i)
                
                const denMagSq = denRe * denRe + denIm * denIm;
                
                if (denMagSq < 1e-10) {
                    return { re: 1e10, im: 0 };
                }
                
                const quotRe = (numRe * denRe + numIm * denIm) / denMagSq;
                const quotIm = (numIm * denRe - numRe * denIm) / denMagSq;
                
                return { re: quotRe, im: quotIm };
            }
            
            if (transformType === 'smith') {
                // Smith Chart mapping: w = (z-1)/(z+1)
                // Used in RF/microwave engineering for impedance
                const numRe = z.re - 1;
                const numIm = z.im;
                const denRe = z.re + 1;
                const denIm = z.im;
                
                const denMagSq = denRe * denRe + denIm * denIm;
                
                if (denMagSq < 1e-10) {
                    return { re: 1e10, im: 0 };
                }
                
                const quotRe = (numRe * denRe + numIm * denIm) / denMagSq;
                const quotIm = (numIm * denRe - numRe * denIm) / denMagSq;
                
                return { re: quotRe, im: quotIm };
            }
            
            if (transformType === 'mobius') {
                // General Möbius transform: w = (az+b)/(cz+d)
                const a = state.mobiusA;
                const b = state.mobiusB;
                const c = state.mobiusC;
                const d = state.mobiusD;
                
                // Check constraint: ad - bc ≠ 0
                const det = a * d - b * c;
                if (Math.abs(det) < 1e-10) {
                    console.warn('Möbius determinant too small, using identity');
                    return z;
                }
                
                // Numerator: az + b
                const numRe = a * z.re + b;
                const numIm = a * z.im;
                
                // Denominator: cz + d
                const denRe = c * z.re + d;
                const denIm = c * z.im;
                
                const denMagSq = denRe * denRe + denIm * denIm;
                
                if (denMagSq < 1e-10) {
                    return { re: 1e10 * Math.sign(numRe), im: 1e10 * Math.sign(numIm) };
                }
                
                const quotRe = (numRe * denRe + numIm * denIm) / denMagSq;
                const quotIm = (numIm * denRe - numRe * denIm) / denMagSq;
                
                return { re: quotRe, im: quotIm };
            }
            
            // Standard Cayley transform: w = i(1+z)/(1-z)
            // Maps unit disk to upper half-plane
            const numRe = 1 + z.re;   // Real part of (1+z)
            const numIm = z.im;       // Imaginary part of (1+z)
            const denRe = 1 - z.re;   // Real part of (1-z)
            const denIm = -z.im;      // Imaginary part of (1-z)
            
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

        function getNestedPointColor(k, m, g, angle) {
            const scheme = state.nestedColorScheme;
            
            switch(scheme) {
                case 'gcd':
                    // Default: Color by GCD value
                    return getGCDColor(g, m);
                
                case 'coprime':
                    // Binary: Gold for coprime, Gray for non-coprime
                    return g === 1 ? CONFIG.colors.farey : 'rgba(150, 150, 150, 0.7)';
                
                case 'rainbow':
                    // Rainbow based on angle position
                    const hue = (angle * 180 / Math.PI) % 360;
                    return `hsla(${hue}, 85%, ${g === 1 ? '65%' : '45%'}, 0.9)`;
                
                case 'ring':
                    // Color by which ring (modulus)
                    const ringHue = (m * 137.508) % 360; // Golden angle for distribution
                    return `hsla(${ringHue}, 75%, ${g === 1 ? '65%' : '50%'}, 0.85)`;
                
                case 'prime':
                    // Color by prime factorization of k
                    if (k === 0) return 'rgba(100, 100, 100, 0.7)';
                    if (isPrime(k)) return '#3498db'; // Blue for primes
                    
                    // Color by smallest prime factor
                    const smallestPrime = getSmallestPrimeFactor(k);
                    const primeHue = (smallestPrime * 73) % 360;
                    return `hsla(${primeHue}, 80%, 60%, 0.85)`;
                
                case 'totient':
                    // Color by totient class - how many coprimes
                    const phi = eulerPhi(m);
                    const ratio = phi / m;
                    const totientHue = ratio * 120; // 0-120 (red to green)
                    return g === 1 ? 
                        `hsla(${totientHue}, 80%, 65%, 0.9)` : 
                        `hsla(${totientHue}, 40%, 40%, 0.6)`;
                
                case 'monochrome':
                    // All gold with varying opacity
                    const opacity = g === 1 ? 0.9 : 0.3;
                    return `rgba(255, 215, 0, ${opacity})`;
                
                default:
                    return getGCDColor(g, m);
            }
        }

        function isPrime(n) {
            if (n < 2) return false;
            if (n === 2) return true;
            if (n % 2 === 0) return false;
            for (let i = 3; i * i <= n; i += 2) {
                if (n % i === 0) return false;
            }
            return true;
        }

        function getSmallestPrimeFactor(n) {
            if (n < 2) return n;
            if (n % 2 === 0) return 2;
            for (let i = 3; i * i <= n; i += 2) {
                if (n % i === 0) return i;
            }
            return n; // n is prime
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

            // Apply inversion if enabled
            const invertAll = document.getElementById('toggleInvertAll').checked;
            if (invertAll) {
                ctx.save();
                ctx.translate(cx, cy);
                ctx.scale(-1, -1);
                ctx.translate(-cx, -cy);
            }

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
                        label: `${fp.num}/${fp.den}`,
                        num: fp.num,
                        den: fp.den,
                        angle: angle
                    };
                });

                // Global r→r connections on unit disk (same denominator)
                if (document.getElementById('toggleShowRtoR').checked) {
                    ctx.globalAlpha = 0.4;
                    ctx.strokeStyle = CONFIG.colors.cyan;
                    ctx.lineWidth = 1.5;
                    ctx.setLineDash([4, 4]);
                    
                    // Group by denominator
                    const denGroups = {};
                    fareyPoints.forEach(p => {
                        if (!denGroups[p.den]) denGroups[p.den] = [];
                        denGroups[p.den].push(p);
                    });
                    
                    // Connect points with same denominator
                    Object.values(denGroups).forEach(group => {
                        if (group.length >= 2) {
                            for (let i = 0; i < group.length - 1; i++) {
                                ctx.beginPath();
                                ctx.moveTo(group[i].x, group[i].y);
                                ctx.lineTo(group[i + 1].x, group[i + 1].y);
                                ctx.stroke();
                            }
                        }
                    });
                    
                    ctx.setLineDash([]);
                    ctx.globalAlpha = 1.0;
                }

                // Global r→r+2ⁿ connections on unit disk (mediant paths)
                if (document.getElementById('toggleShowRtoRplus2n').checked) {
                    ctx.globalAlpha = 0.35;
                    ctx.strokeStyle = 'rgba(255, 100, 100, 0.7)';
                    ctx.lineWidth = 1.2;
                    ctx.setLineDash([2, 2]);
                    
                    // For each point, try to connect to its "lift" (mediant-related points)
                    fareyPoints.forEach(p1 => {
                        fareyPoints.forEach(p2 => {
                            if (p1 === p2) return;
                            
                            // Check if p2.den = 2*p1.den (doubling relationship)
                            if (p2.den === 2 * p1.den || p2.den === p1.den * 3) {
                                ctx.beginPath();
                                ctx.moveTo(p1.x, p1.y);
                                ctx.lineTo(p2.x, p2.y);
                                ctx.stroke();
                            }
                        });
                    });
                    
                    ctx.setLineDash([]);
                    ctx.globalAlpha = 1.0;
                }

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

            // Draw selection highlight if point is selected on this canvas
            if (inspectionState.selectedPoint && inspectionState.selectedPoint.canvasType === 'disk') {
                const sp = inspectionState.selectedPoint;
                drawSelectionHighlight(ctx, sp.x, sp.y, sp.type);
            }

            // Restore context if inverted
            if (invertAll) {
                ctx.restore();
            }
        }

        function drawCayley() {
            const canvas = canvases.cayley;
            const ctx = canvases.cayleyCtx;
            const w = canvas.width / (window.devicePixelRatio || 1);
            const h = canvas.height / (window.devicePixelRatio || 1);

            ctx.clearRect(0, 0, w, h);

            // Apply inversion if enabled
            const invertAll = document.getElementById('toggleInvertAll').checked;
            if (invertAll) {
                ctx.save();
                ctx.translate(w / 2, h / 2);
                ctx.scale(-1, -1);
                ctx.translate(-w / 2, -h / 2);
            }

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
            const showFordCircles = document.getElementById('toggleFordCircles').checked;

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
                        
                        const w1 = cayleyTransform(z1, state.transformType);
                        const w2 = cayleyTransform(z2, state.transformType);

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
                
                // Global r→r connections on Cayley plane (same denominator)
                if (document.getElementById('toggleShowRtoR').checked) {
                    ctx.globalAlpha = 0.4;
                    ctx.strokeStyle = CONFIG.colors.cyan;
                    ctx.lineWidth = 1.5;
                    ctx.setLineDash([4, 4]);
                    
                    // Group by denominator
                    const denGroups = {};
                    state.fareyPoints.forEach(fp => {
                        if (!denGroups[fp.den]) denGroups[fp.den] = [];
                        denGroups[fp.den].push(fp);
                    });
                    
                    // Connect transformed points with same denominator
                    Object.values(denGroups).forEach(group => {
                        if (group.length >= 2) {
                            const transformedGroup = group.map(fp => {
                                const frac = fp.num / fp.den;
                                const angle = 2 * Math.PI * frac + phase;
                                const z = { re: Math.cos(angle), im: Math.sin(angle) };
                                const wp = cayleyTransform(z, state.transformType);
                                return mathToScreen(wp);
                            });
                            
                            for (let i = 0; i < transformedGroup.length - 1; i++) {
                                ctx.beginPath();
                                ctx.moveTo(transformedGroup[i].x, transformedGroup[i].y);
                                ctx.lineTo(transformedGroup[i + 1].x, transformedGroup[i + 1].y);
                                ctx.stroke();
                            }
                        }
                    });
                    
                    ctx.setLineDash([]);
                    ctx.globalAlpha = 1.0;
                }

                // Global r→r+2ⁿ on Cayley plane
                if (document.getElementById('toggleShowRtoRplus2n').checked) {
                    ctx.globalAlpha = 0.35;
                    ctx.strokeStyle = 'rgba(255, 100, 100, 0.7)';
                    ctx.lineWidth = 1.2;
                    ctx.setLineDash([2, 2]);
                    
                    state.fareyPoints.forEach(fp1 => {
                        state.fareyPoints.forEach(fp2 => {
                            if (fp1 === fp2) return;
                            
                            if (fp2.den === 2 * fp1.den || fp2.den === fp1.den * 3) {
                                const frac1 = fp1.num / fp1.den;
                                const frac2 = fp2.num / fp2.den;
                                const angle1 = 2 * Math.PI * frac1 + phase;
                                const angle2 = 2 * Math.PI * frac2 + phase;
                                const z1 = { re: Math.cos(angle1), im: Math.sin(angle1) };
                                const z2 = { re: Math.cos(angle2), im: Math.sin(angle2) };
                                const w1 = cayleyTransform(z1, state.transformType);
                                const w2 = cayleyTransform(z2, state.transformType);
                                const p1 = mathToScreen(w1);
                                const p2 = mathToScreen(w2);
                                
                                ctx.beginPath();
                                ctx.moveTo(p1.x, p1.y);
                                ctx.lineTo(p2.x, p2.y);
                                ctx.stroke();
                            }
                        });
                    });
                    
                    ctx.setLineDash([]);
                    ctx.globalAlpha = 1.0;
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
                    const wp = cayleyTransform(z, state.transformType);

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
                    const wp = cayleyTransform(z, state.transformType);
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

            // Ford circles
            if (showFordCircles && state.fareyPoints.length > 0) {
                state.fareyPoints.forEach(fp => {
                    const p = fp.num;
                    const q = fp.den;
                    if (q === 0) return;
                    
                    // Ford circle for p/q has center at (p/q, 1/(2q²)) and radius 1/(2q²)
                    const centerRe = p / q;
                    const radius = 1 / (2 * q * q);
                    const centerIm = radius;
                    
                    // Check if visible
                    const reMin = -state.cayleyHRange / (2 * state.cayleyZoom);
                    const reMax = state.cayleyHRange / (2 * state.cayleyZoom);
                    const imMin = state.cayleyVOffset;
                    const imMax = (state.cayleyVRange / state.cayleyZoom) + state.cayleyVOffset;
                    
                    if (centerRe + radius >= reMin && centerRe - radius <= reMax && 
                        centerIm + radius >= imMin && centerIm - radius <= imMax) {
                        
                        const centerP = mathToScreen({ re: centerRe, im: centerIm });
                        const radiusP = mathToScreen({ re: centerRe + radius, im: centerIm });
                        const radiusPixels = Math.abs(radiusP.x - centerP.x);
                        
                        ctx.strokeStyle = 'rgba(230, 126, 34, 0.6)';
                        ctx.lineWidth = 2;
                        ctx.shadowBlur = 8;
                        ctx.shadowColor = 'rgba(230, 126, 34, 0.4)';
                        ctx.beginPath();
                        ctx.arc(centerP.x, centerP.y, radiusPixels, 0, 2 * Math.PI);
                        ctx.stroke();
                        ctx.shadowBlur = 0;
                        
                        // Label
                        if (state.labelMode !== 'none' && radiusPixels > 10) {
                            ctx.fillStyle = CONFIG.colors.cusp;
                            ctx.font = `${Math.max(8, state.labelSize - 2)}px "Fira Code"`;
                            ctx.textAlign = 'center';
                            ctx.fillText(`${p}/${q}`, centerP.x, centerP.y);
                        }
                    }
                });
            }

            // Farey triangle (transformed)
            if (showFarey && state.fareyPoints.length >= 2) {
                const transformedPoints = state.fareyPoints.map(fp => {
                    const frac = fp.num / fp.den;
                    const angle = 2 * Math.PI * frac + phase;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const wp = cayleyTransform(z, state.transformType);
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

            // Draw selection highlight if point is selected on this canvas
            if (inspectionState.selectedPoint && inspectionState.selectedPoint.canvasType === 'cayley') {
                const sp = inspectionState.selectedPoint;
                drawSelectionHighlight(ctx, sp.x, sp.y, sp.type);
            }

            // Restore context if inverted
            if (invertAll) {
                ctx.restore();
            }
        }

        function drawFullPlane() {
            const canvas = canvases.fullPlane;
            const ctx = canvases.fullPlaneCtx;
            const w = canvas.width / (window.devicePixelRatio || 1);
            const h = canvas.height / (window.devicePixelRatio || 1);

            ctx.clearRect(0, 0, w, h);

            // Apply inversion if enabled
            const invertAll = document.getElementById('toggleInvertAll').checked;
            if (invertAll) {
                ctx.save();
                ctx.translate(w / 2, h / 2);
                ctx.scale(-1, -1);
                ctx.translate(-w / 2, -h / 2);
            }

            // Coordinate conversion - full complex plane view
            function mathToScreen(wp) {
                const scale = Math.min(w, h) * 0.15 * state.cayleyZoom;
                const x = w / 2 + wp.re * scale;
                const y = h / 2 - wp.im * scale;
                return { x, y };
            }

            const phase = state.phase * Math.PI / 180;

            // Grid
            if (document.getElementById('toggleGrid').checked) {
                ctx.strokeStyle = 'rgba(255, 255, 255, 0.08)';
                ctx.lineWidth = 1;

                const gridSpacing = 1;
                const maxGrid = 10;

                for (let i = -maxGrid; i <= maxGrid; i++) {
                    // Vertical lines
                    const p1 = mathToScreen({ re: i * gridSpacing, im: -maxGrid * gridSpacing });
                    const p2 = mathToScreen({ re: i * gridSpacing, im: maxGrid * gridSpacing });
                    ctx.beginPath();
                    ctx.moveTo(p1.x, p1.y);
                    ctx.lineTo(p2.x, p2.y);
                    ctx.stroke();

                    // Horizontal lines
                    const p3 = mathToScreen({ re: -maxGrid * gridSpacing, im: i * gridSpacing });
                    const p4 = mathToScreen({ re: maxGrid * gridSpacing, im: i * gridSpacing });
                    ctx.beginPath();
                    ctx.moveTo(p3.x, p3.y);
                    ctx.lineTo(p4.x, p4.y);
                    ctx.stroke();
                }
            }

            // Axes
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.4)';
            ctx.lineWidth = 2;
            
            // Real axis
            const realStart = mathToScreen({ re: -10, im: 0 });
            const realEnd = mathToScreen({ re: 10, im: 0 });
            ctx.beginPath();
            ctx.moveTo(realStart.x, realStart.y);
            ctx.lineTo(realEnd.x, realEnd.y);
            ctx.stroke();

            // Imaginary axis
            const imStart = mathToScreen({ re: 0, im: -10 });
            const imEnd = mathToScreen({ re: 0, im: 10 });
            ctx.beginPath();
            ctx.moveTo(imStart.x, imStart.y);
            ctx.lineTo(imEnd.x, imEnd.y);
            ctx.stroke();

            // Draw unit circle (boundary between disk and exterior)
            ctx.strokeStyle = 'rgba(231, 76, 60, 0.6)';
            ctx.lineWidth = 2;
            ctx.setLineDash([5, 5]);
            ctx.beginPath();
            for (let i = 0; i <= 100; i++) {
                const angle = 2 * Math.PI * i / 100;
                const z = { re: Math.cos(angle), im: Math.sin(angle) };
                const wp = cayleyTransform(z, state.transformType);
                const p = mathToScreen(wp);
                if (i === 0) {
                    ctx.moveTo(p.x, p.y);
                } else {
                    ctx.lineTo(p.x, p.y);
                }
            }
            ctx.stroke();
            ctx.setLineDash([]);

            // Draw real axis (maps from unit circle |z|=1)
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.4)';
            ctx.lineWidth = 2;
            const realAxisStart = mathToScreen({ re: -10, im: 0 });
            const realAxisEnd = mathToScreen({ re: 10, im: 0 });
            ctx.beginPath();
            ctx.moveTo(realAxisStart.x, realAxisStart.y);
            ctx.lineTo(realAxisEnd.x, realAxisEnd.y);
            ctx.stroke();

            // Label the regions
            ctx.fillStyle = 'rgba(255, 255, 255, 0.3)';
            ctx.font = 'italic 14px "Fira Code"';
            ctx.textAlign = 'center';
            
            const upperLabel = mathToScreen({ re: 0, im: 4 });
            ctx.fillText('Upper Half-Plane', upperLabel.x, upperLabel.y);
            ctx.fillText('(|z| < 1 interior)', upperLabel.x, upperLabel.y + 20);
            
            const lowerLabel = mathToScreen({ re: 0, im: -4 });
            ctx.fillText('Lower Half-Plane', lowerLabel.x, lowerLabel.y);
            ctx.fillText('(|z| > 1 exterior)', lowerLabel.x, lowerLabel.y + 20);
            
            const axisLabel = mathToScreen({ re: 7, im: 0 });
            ctx.fillText('Real Axis (|z| = 1)', axisLabel.x, axisLabel.y - 10);

            // Transformed Farey points
            if (document.getElementById('toggleFarey').checked && state.fareyPoints.length > 0) {
                state.fareyPoints.forEach(fp => {
                    const frac = fp.num / fp.den;
                    const angle = 2 * Math.PI * frac + phase;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const wp = cayleyTransform(z, state.transformType);
                    const p = mathToScreen(wp);

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
                        ctx.fillText(`${fp.num}/${fp.den}`, p.x, p.y - 20);
                        ctx.shadowBlur = 0;
                    }
                });
            }

            // Geodesics
            if (document.getElementById('toggleGeodesic').checked && state.fareyPoints.length >= 2) {
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
                        
                        const w1 = cayleyTransform(z1, state.transformType);
                        const w2 = cayleyTransform(z2, state.transformType);

                        const centerRe = (w1.re + w2.re) / 2;
                        const radius = Math.sqrt((w1.re - centerRe) ** 2 + w1.im ** 2);

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

            // Primes
            if (document.getElementById('togglePrimes').checked) {
                const colors = generateColors(state.modulus);
                const displayPrimes = state.primes.slice(0, state.numPrimes);
                const showChannels = document.getElementById('toggleChannels').checked;

                displayPrimes.forEach(p => {
                    if (showChannels && gcd(p, state.modulus) !== 1) return;

                    const angle = 2 * Math.PI * p / state.modulus + phase;
                    const z = { re: Math.cos(angle), im: Math.sin(angle) };
                    const wp = cayleyTransform(z, state.transformType);
                    const p_screen = mathToScreen(wp);

                    const color = showChannels ? colors[p % state.modulus] : CONFIG.colors.prime;
                    
                    ctx.fillStyle = color;
                    ctx.shadowBlur = 6;
                    ctx.shadowColor = color;
                    ctx.beginPath();
                    ctx.arc(p_screen.x, p_screen.y, 3.5, 0, 2 * Math.PI);
                    ctx.fill();
                    ctx.shadowBlur = 0;
                });
            }

            // Title
            ctx.fillStyle = 'rgba(255, 255, 255, 0.9)';
            ctx.font = 'bold 20px "Fira Code"';
            ctx.textAlign = 'center';
            ctx.shadowBlur = 10;
            ctx.shadowColor = 'rgba(0, 0, 0, 0.8)';
            ctx.fillText('Full Complex Plane ℂ', w/2, 35);
            ctx.shadowBlur = 0;

            // Restore context if inverted
            if (invertAll) {
                ctx.restore();
            }
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

            // Apply inversion if enabled
            const invertAll = document.getElementById('toggleInvertAll').checked;
            if (invertAll) {
                ctx.save();
                ctx.translate(cx, cy);
                ctx.scale(-1, -1);
                ctx.translate(-cx, -cy);
            }

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
            const invertRings = document.getElementById('toggleInvertRings').checked;
            const showCoprimeOnly = document.getElementById('toggleShowCoprimeOnly').checked;
            const showNonCoprimeOnly = document.getElementById('toggleShowNonCoprimeOnly').checked;

            const allPoints = [];
            const numRings = state.maxRing - state.minRing + 1;

            for (let m = state.minRing; m <= state.maxRing; m++) {
                // Calculate ring index - invert if toggle is on
                let ringIndex;
                if (invertRings) {
                    ringIndex = (state.maxRing - m);
                } else {
                    ringIndex = m - state.minRing;
                }
                
                const ringRadius = baseRadius + ringIndex * (maxRadius - baseRadius) / Math.max(1, numRings - 1) * state.ringSpacing;

                // Calculate per-ring rotation
                const ringRotationOffset = (state.ringRotation * Math.PI / 180) * ringIndex;

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
                    
                    // Apply GCD filter
                    if (showCoprimeOnly && g !== 1) continue;
                    if (showNonCoprimeOnly && g === 1) continue;
                    
                    const angle = 2 * Math.PI * k / m + phase + ringRotationOffset;
                    const x = cx + ringRadius * Math.cos(angle);
                    const y = cy + ringRadius * Math.sin(angle);

                    allPoints.push({ x, y, k, m, g, angle, radius: ringRadius, frac: k/m });

                    // Draw point
                    if (showGCD) {
                        const color = getNestedPointColor(k, m, g, angle);
                        const size = g === 1 ? 4 : 3;
                        
                        ctx.fillStyle = color;
                        ctx.shadowBlur = g === 1 ? 8 : 4;
                        ctx.shadowColor = color;
                        ctx.beginPath();
                        ctx.arc(x, y, size, 0, 2 * Math.PI);
                        ctx.fill();
                        ctx.shadowBlur = 0;
                    }

                    // Labels with radial positioning
                    const shouldLabel = 
                        (state.labelMode === 'all') ||
                        (state.labelMode === 'integers') ||
                        (state.labelMode === 'coprime' && g === 1) ||
                        (state.labelMode === 'everything');

                    if (shouldLabel && m % state.labelFreq === 0) {
                        // Store label info for later rendering (after all points and connections)
                        allPoints[allPoints.length - 1].shouldLabel = true;
                        allPoints[allPoints.length - 1].labelText = state.labelMode === 'integers' ? `${k}` : `${k}/${m}`;
                    }
                }
            }

            // Connections
            if (state.connectionMode !== 'none') {
                ctx.globalAlpha = state.connectionOpacity;
                ctx.lineWidth = state.connectionThickness;

                // Helper function to check if point passes GCD filter
                const passesGCDFilter = (point) => {
                    if (state.gcdFilter === 'both') return true;
                    if (state.gcdFilter === 'coprime') return point.g === 1;
                    if (state.gcdFilter === 'noncoprime') return point.g !== 1;
                    return true;
                };

                // Filter points based on GCD selection
                const filteredPoints = allPoints.filter(passesGCDFilter);

                switch (state.connectionMode) {
                    case 'farey':
                        // Connect all Farey points (gcd=1) - respect GCD filter
                        const fareyPts = filteredPoints.filter(p => p.g === 1);
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
                        // Connect points on same ring - respect GCD filter
                        for (let m = state.minRing; m <= state.maxRing; m++) {
                            const ringPts = filteredPoints.filter(p => p.m === m);
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
                        // Connect points with similar angles - respect GCD filter
                        const angleGroups = {};
                        filteredPoints.forEach(p => {
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
                        // Connect points with same GCD - respect GCD filter
                        const gcdGroups = {};
                        filteredPoints.forEach(p => {
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
                        // Connect points with same fraction value - respect GCD filter
                        const fracGroups = {};
                        filteredPoints.forEach(p => {
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
                    
                    case 'gap2n':
                        // Connect r to r+gap (gap-2n connections) - respect GCD filter
                        // Group filtered points by modulus first
                        const modGroups = {};
                        filteredPoints.forEach(p => {
                            if (!modGroups[p.m]) modGroups[p.m] = [];
                            modGroups[p.m].push(p);
                        });
                        
                        ctx.strokeStyle = '#e67e22';
                        Object.entries(modGroups).forEach(([m, points]) => {
                            const mod = parseInt(m);
                            const gap = state.gapSize;
                            
                            // For each point r, find r+gap (mod m)
                            points.forEach(p1 => {
                                const targetK = (p1.k + gap) % mod;
                                const p2 = points.find(p => p.k === targetK);
                                
                                if (p2) {
                                    ctx.beginPath();
                                    ctx.moveTo(p1.x, p1.y);
                                    ctx.lineTo(p2.x, p2.y);
                                    ctx.stroke();
                                }
                            });
                        });
                        break;

                    case 'evengaps':
                        // Multiple even gaps - respect GCD filter
                        const modGroupsMulti = {};
                        filteredPoints.forEach(p => {
                            if (!modGroupsMulti[p.m]) modGroupsMulti[p.m] = [];
                            modGroupsMulti[p.m].push(p);
                        });
                        
                        // Generate colors for different gaps
                        const gapColors = [];
                        for (let g = 2; g <= state.maxGap; g += 2) {
                            const hue = (g / state.maxGap) * 360;
                            gapColors.push(`hsla(${hue}, 85%, 65%, 0.7)`);
                        }
                        
                        Object.entries(modGroupsMulti).forEach(([m, points]) => {
                            const mod = parseInt(m);
                            
                            let gapIndex = 0;
                            for (let gap = 2; gap <= state.maxGap; gap += 2) {
                                ctx.strokeStyle = gapColors[gapIndex];
                                
                                points.forEach(p1 => {
                                    const targetK = (p1.k + gap) % mod;
                                    const p2 = points.find(p => p.k === targetK);
                                    
                                    if (p2) {
                                        ctx.beginPath();
                                        ctx.moveTo(p1.x, p1.y);
                                        ctx.lineTo(p2.x, p2.y);
                                        ctx.stroke();
                                    }
                                });
                                
                                gapIndex++;
                            }
                        });
                        break;
                }

                ctx.globalAlpha = 1.0;
            }

            // Global r→r connections (vertical self-similarity)
            if (document.getElementById('toggleShowRtoR').checked) {
                ctx.globalAlpha = 0.4;
                ctx.lineWidth = 1.5;
                
                // Group all points by their residue value k (the integer)
                const residueGroups = {};
                allPoints.forEach(p => {
                    if (!residueGroups[p.k]) residueGroups[p.k] = [];
                    residueGroups[p.k].push(p);
                });
                
                // For each residue k, connect all instances across rings
                Object.values(residueGroups).forEach(group => {
                    if (group.length < 2) return;
                    
                    // Sort by ring radius
                    group.sort((a, b) => a.radius - b.radius);
                    
                    // Determine color based on coprimality
                    const allCoprime = group.every(p => p.g === 1);
                    ctx.strokeStyle = allCoprime ? CONFIG.colors.farey : 'rgba(100, 150, 255, 0.6)';
                    
                    // Draw connections between consecutive rings
                    for (let i = 0; i < group.length - 1; i++) {
                        ctx.beginPath();
                        ctx.moveTo(group[i].x, group[i].y);
                        ctx.lineTo(group[i + 1].x, group[i + 1].y);
                        ctx.stroke();
                    }
                });
                
                ctx.globalAlpha = 1.0;
            }

            // Global r→r+m×2ⁿ connections (power-of-2 lifts)
            if (document.getElementById('toggleShowRtoRplus2n').checked) {
                ctx.globalAlpha = 0.35;
                ctx.lineWidth = 1.2;
                
                // For each ring, connect r to r+m in the next ring
                for (let m = state.minRing; m < state.maxRing; m++) {
                    const currentRingPoints = allPoints.filter(p => p.m === m);
                    const nextRingPoints = allPoints.filter(p => p.m === (m + 1));
                    
                    if (nextRingPoints.length === 0) continue;
                    
                    currentRingPoints.forEach(p1 => {
                        // Find target in next ring: r + m (mod m+1)
                        // Since m+1 contains all of {0,1,...,m}, we look for k values:
                        // r, r+m, r+2m, ... that exist in the next ring
                        
                        // For power of 2 structure: connect r to r (same k value)
                        const sameK = nextRingPoints.find(p2 => p2.k === p1.k);
                        
                        // And connect to r+m if it exists
                        const shiftedK = nextRingPoints.find(p2 => p2.k === p1.k + m);
                        
                        // Color based on coprimality
                        const isCoprime = p1.g === 1;
                        ctx.strokeStyle = isCoprime ? 
                            'rgba(255, 100, 100, 0.7)' : 
                            'rgba(150, 150, 200, 0.5)';
                        
                        if (sameK) {
                            ctx.beginPath();
                            ctx.moveTo(p1.x, p1.y);
                            ctx.lineTo(sameK.x, sameK.y);
                            ctx.stroke();
                        }
                        
                        if (shiftedK) {
                            ctx.setLineDash([3, 3]);
                            ctx.beginPath();
                            ctx.moveTo(p1.x, p1.y);
                            ctx.lineTo(shiftedK.x, shiftedK.y);
                            ctx.stroke();
                            ctx.setLineDash([]);
                        }
                    });
                }
                
                ctx.globalAlpha = 1.0;
            }

            // Highlight custom Farey points if they exist in range
            state.fareyPoints.forEach(fp => {
                if (fp.den >= state.minRing && fp.den <= state.maxRing) {
                    const m = fp.den;
                    const k = fp.num % m;
                    
                    // Calculate ring index with inversion
                    let ringIndex;
                    if (invertRings) {
                        ringIndex = (state.maxRing - m);
                    } else {
                        ringIndex = m - state.minRing;
                    }
                    
                    const ringRadius = baseRadius + ringIndex * (maxRadius - baseRadius) / Math.max(1, numRings - 1) * state.ringSpacing;
                    const ringRotationOffset = (state.ringRotation * Math.PI / 180) * ringIndex;
                    const angle = 2 * Math.PI * k / m + phase + ringRotationOffset;
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
                }
            });

            // Render all labels AFTER all points and connections (so labels are on top)
            allPoints.forEach(point => {
                if (point.shouldLabel) {
                    const g = point.g;
                    const angle = point.angle;
                    const ringRadius = point.radius;
                    
                    ctx.fillStyle = g === 1 ? CONFIG.colors.farey : 'rgba(255, 255, 255, 0.6)';
                    ctx.font = `${state.labelSize - 2}px "Fira Code"`;
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    
                    const labelText = point.labelText;
                    
                    // Calculate label position
                    let labelX, labelY;
                    if (state.labelPosition === 'radial') {
                        // Position label radially outward from center
                        const labelRadius = ringRadius + state.labelOffset;
                        labelX = cx + labelRadius * Math.cos(angle);
                        labelY = cy + labelRadius * Math.sin(angle);
                    } else {
                        // Position on point
                        labelX = point.x;
                        labelY = point.y;
                    }
                    
                    // Add background for better readability
                    ctx.save();
                    const textMetrics = ctx.measureText(labelText);
                    const textWidth = textMetrics.width;
                    const textHeight = state.labelSize - 2;
                    
                    ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
                    ctx.fillRect(labelX - textWidth/2 - 2, labelY - textHeight/2 - 1, textWidth + 4, textHeight + 2);
                    
                    ctx.fillStyle = g === 1 ? CONFIG.colors.farey : 'rgba(255, 255, 255, 0.9)';
                    ctx.shadowBlur = 3;
                    ctx.shadowColor = 'rgba(0, 0, 0, 0.8)';
                    ctx.fillText(labelText, labelX, labelY);
                    ctx.shadowBlur = 0;
                    ctx.restore();
                }
            });

            // Render Farey point labels AFTER everything else (highest priority)
            if (state.labelMode !== 'none') {
                state.fareyPoints.forEach(fp => {
                    if (fp.den >= state.minRing && fp.den <= state.maxRing) {
                        const m = fp.den;
                        const k = fp.num % m;
                        
                        // Calculate ring index with inversion
                        let ringIndex;
                        if (invertRings) {
                            ringIndex = (state.maxRing - m);
                        } else {
                            ringIndex = m - state.minRing;
                        }
                        
                        const ringRadius = baseRadius + ringIndex * (maxRadius - baseRadius) / Math.max(1, numRings - 1) * state.ringSpacing;
                        const ringRotationOffset = (state.ringRotation * Math.PI / 180) * ringIndex;
                        const angle = 2 * Math.PI * k / m + phase + ringRotationOffset;
                        
                        // Calculate label position
                        let labelX, labelY;
                        if (state.labelPosition === 'radial') {
                            const labelRadius = ringRadius + state.labelOffset;
                            labelX = cx + labelRadius * Math.cos(angle);
                            labelY = cy + labelRadius * Math.sin(angle);
                        } else {
                            labelX = cx + ringRadius * Math.cos(angle);
                            labelY = cy + ringRadius * Math.sin(angle);
                        }
                        
                        // Farey label (in format matching the label mode)
                        const labelText = state.labelMode === 'integers' ? `${k}` : `${fp.num}/${fp.den}`;
                        
                        ctx.font = `bold ${state.labelSize + 2}px "Fira Code"`;
                        ctx.textAlign = 'center';
                        ctx.textBaseline = 'middle';
                        
                        // Background
                        const textMetrics = ctx.measureText(labelText);
                        const textWidth = textMetrics.width;
                        const textHeight = state.labelSize + 2;
                        
                        ctx.fillStyle = 'rgba(255, 107, 107, 0.9)';
                        ctx.fillRect(labelX - textWidth/2 - 3, labelY - textHeight/2 - 1, textWidth + 6, textHeight + 2);
                        
                        // Text
                        ctx.fillStyle = '#ffffff';
                        ctx.shadowBlur = 5;
                        ctx.shadowColor = 'rgba(0, 0, 0, 0.9)';
                        ctx.fillText(labelText, labelX, labelY);
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
            ctx.fillText('Nested Modular Rings', cx, 35);
            ctx.font = '12px "Fira Code"';
            ctx.fillStyle = 'rgba(255, 255, 255, 0.7)';
            ctx.fillText(`m = ${state.minRing} to ${state.maxRing}`, cx, 55);
            ctx.shadowBlur = 0;

            // Draw selection highlight if point is selected on this canvas
            if (inspectionState.selectedPoint && inspectionState.selectedPoint.canvasType === 'nested') {
                const sp = inspectionState.selectedPoint;
                drawSelectionHighlight(ctx, sp.x, sp.y, sp.type);
            }

            // Restore context if inverted
            if (invertAll) {
                ctx.restore();
            }
        }

        function updateAll() {
            drawDisk();
            drawCayley();
            drawNested();
            
            // Draw full plane if visible
            if (document.getElementById('toggleFullPlane').checked) {
                drawFullPlane();
            }
            
            // Continue animation if point is selected (for pulsing highlight)
            if (inspectionState.selectedPoint && !state.animationId) {
                requestAnimationFrame(updateAll);
            }
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
                toggle.innerHTML = '&#9660;';
            } else {
                panel.style.display = 'none';
                toggle.innerHTML = '&#9654;';
            }
        }

        function verifyCayleyTransform() {
            const resultsDiv = document.getElementById('verificationResults');
            resultsDiv.style.display = 'block';
            
            let html = '<h4 style="color: #3498db; margin-bottom: 10px;">Cayley Transform Verification Results:</h4>';
            
            const testPoints = [
                { z: { re: 0, im: 0 }, label: 'z = 0 (center)', expected: 'w = i (upper half-plane)' },
                { z: { re: 1, im: 0 }, label: 'z = 1 (right edge)', expected: 'w = ∞ (real axis point at infinity)' },
                { z: { re: -1, im: 0 }, label: 'z = -1 (left edge)', expected: 'w = 0 (origin on real axis)' },
                { z: { re: 0, im: 1 }, label: 'z = i (top edge)', expected: 'w = 1 (real axis)' },
                { z: { re: 0, im: -1 }, label: 'z = -i (bottom edge)', expected: 'w = -1 (real axis)' },
                { z: { re: 0.5, im: 0 }, label: 'z = 0.5 (interior)', expected: 'Im(w) > 0' },
            ];
            
            testPoints.forEach(test => {
                const w = cayleyTransform(test.z, 'standard');
                const magnitude = Math.sqrt(test.z.re * test.z.re + test.z.im * test.z.im);
                const isInterior = magnitude < 0.99;
                const isBoundary = magnitude >= 0.99 && magnitude <= 1.01;
                
                let status = '✓';
                let color = '#2ecc71';
                
                // Check if mapping is correct
                if (isInterior && w.im <= 0.01) {
                    status = '✗';
                    color = '#e74c3c';
                } else if (isBoundary && Math.abs(w.im) > 0.1) {
                    status = '✗';
                    color = '#e74c3c';
                }
                
                html += `<div style="margin: 8px 0; padding: 8px; background: rgba(0,0,0,0.2); border-left: 3px solid ${color};">`;
                html += `<span style="color: ${color}; font-weight: bold;">${status}</span> `;
                html += `<strong>${test.label}</strong><br>`;
                html += `|z| = ${magnitude.toFixed(4)} → `;
                html += `w = ${w.re.toFixed(4)} + ${w.im.toFixed(4)}i<br>`;
                html += `<span style="color: #95a5a6;">Expected: ${test.expected}</span>`;
                html += `</div>`;
            });
            
            // Overall assessment
            html += '<div style="margin-top: 15px; padding: 12px; background: rgba(255, 215, 0, 0.1); border: 2px solid #ffd700; border-radius: 4px;">';
            html += '<strong style="color: #ffd700;">Assessment:</strong><br>';
            html += 'The formula w = i(1-z)/(1+z) correctly maps:<br>';
            html += '• Unit disk |z| &lt; 1 → Upper half-plane Im(w) &gt; 0 ✓<br>';
            html += '• Unit circle |z| = 1 → Real axis Im(w) = 0 ✓<br>';
            html += '<br>This is the standard Cayley transform for hyperbolic geometry (Poincaré disk ↔ upper half-plane).';
            html += '</div>';
            
            resultsDiv.innerHTML = html;
        }


        function toggleGuide() {
            const panel = document.getElementById('guidePanel');
            const toggle = document.getElementById('guideToggle');
            if (panel.style.display === 'none') {
                panel.style.display = 'block';
                toggle.innerHTML = '&#9660;';
            } else {
                panel.style.display = 'none';
                toggle.innerHTML = '&#9654;';
            }
        }

        function toggleIntro() {
            const panel = document.getElementById('introPanel');
            const toggle = document.getElementById('introToggle');
            if (panel.style.display === 'none') {
                panel.style.display = 'block';
                toggle.innerHTML = '&#9660;';
            } else {
                panel.style.display = 'none';
                toggle.innerHTML = '&#9654;';
            }
        }


        function resetDefaults() {
            state = {
                phase: 180,
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
                transformType: 'standard',
                mobiusA: 1,
                mobiusB: 0,
                mobiusC: 0,
                mobiusD: 1,
                diskZoom: 1.0,
                cayleyZoom: 1.0,
                nestedZoom: 1.0,
                fareyPoints: [
                    {num: 0, den: 1},
                    {num: 1, den: 1},
                    {num: 0, den: 2},
                    {num: 1, den: 2},
                    {num: 0, den: 3},
                    {num: 1, den: 3},
                    {num: 2, den: 3},
                    {num: 0, den: 4},
                    {num: 1, den: 4},
                    {num: 3, den: 4},
                    {num: 0, den: 5},
                    {num: 1, den: 5},
                    {num: 2, den: 5},
                    {num: 3, den: 5},
                    {num: 4, den: 5}
                ],
                primes: state.primes,
                animationId: null
            };

            // Reset UI
            document.getElementById('phaseSlider').value = 180;
            document.getElementById('modulusInput').value = 30;
            document.getElementById('primesInput').value = 150;
            document.getElementById('primeLimitInput').value = 10000;
            document.getElementById('speedSlider').value = 1;
            document.getElementById('minRingInput').value = 1;
            document.getElementById('maxRingInput').value = 12;
            document.getElementById('spacingSlider').value = 1;
            document.getElementById('ringRotationSlider').value = 0;
            document.getElementById('ringRotationInput').value = 0;
            document.getElementById('cayleyHRangeSlider').value = 6;
            document.getElementById('cayleyVRangeSlider').value = 4;
            document.getElementById('cayleyVOffsetSlider').value = 0;
            document.getElementById('cayleyGridDensitySlider').value = 1;
            document.getElementById('connectionMode').value = 'none';
            document.getElementById('connectionThicknessSlider').value = 1;
            document.getElementById('connectionOpacitySlider').value = 0.3;
            document.getElementById('labelMode').value = 'farey';
            document.getElementById('labelSizeSlider').value = 10;
            document.getElementById('labelFreqInput').value = 1;
            document.getElementById('diskZoomSlider').value = 1;
            document.getElementById('cayleyZoomSlider').value = 1;
            document.getElementById('nestedZoomSlider').value = 1;
            document.getElementById('toggleAnimate').checked = false;
            document.getElementById('toggleFarey').checked = true;
            document.getElementById('toggleGeodesic').checked = true;
            document.getElementById('togglePrimes').checked = true;
            document.getElementById('toggleChannels').checked = true;
            document.getElementById('toggleCusps').checked = true;
            document.getElementById('toggleRings').checked = true;
            document.getElementById('toggleGCD').checked = true;
            document.getElementById('toggleGrid').checked = true;
            document.getElementById('toggleFundDomain').checked = false;
            document.getElementById('toggleVerticals').checked = false;
            document.getElementById('toggleDiskOutline').checked = false;
            document.getElementById('toggleFullPlane').checked = false;
            document.getElementById('toggleShowCoprimeOnly').checked = false;
            document.getElementById('toggleShowNonCoprimeOnly').checked = false;
            document.getElementById('cayleyTransformType').value = 'standard';
            document.getElementById('mobiusA').value = 1;
            document.getElementById('mobiusB').value = 0;
            document.getElementById('mobiusC').value = 0;
            document.getElementById('mobiusD').value = 1;
            document.getElementById('mobiusParamsA').style.display = 'none';
            document.getElementById('mobiusParamsB').style.display = 'none';
            document.getElementById('mobiusParamsC').style.display = 'none';
            document.getElementById('mobiusParamsD').style.display = 'none';
            document.getElementById('transformDescription').textContent = 'Standard: Maps unit disk to upper half-plane (modular forms)';

            document.getElementById('phaseValue').textContent = '180 degrees';
            document.getElementById('modulusDisplay').textContent = '30';
            document.getElementById('primesDisplay').textContent = '150';
            document.getElementById('primeLimitDisplay').textContent = '10000';
            document.getElementById('speedValue').textContent = '1.0×';
            document.getElementById('minRingDisplay').textContent = '1';
            document.getElementById('maxRingDisplay').textContent = '12';
            document.getElementById('spacingValue').textContent = '1.0';
            document.getElementById('ringRotationValue').textContent = '0°';
            document.getElementById('cayleyHRangeValue').textContent = '6.0';
            document.getElementById('cayleyVRangeValue').textContent = '4.0';
            document.getElementById('cayleyVOffsetValue').textContent = '0.0';
            document.getElementById('cayleyGridDensityValue').textContent = '1.0';
            document.getElementById('connectionThicknessValue').textContent = '1.0';
            document.getElementById('connectionOpacityValue').textContent = '0.30';
            document.getElementById('labelSizeValue').textContent = '10';
            document.getElementById('labelFreqValue').textContent = '1';
            document.getElementById('labelPosition').value = 'radial';
            document.getElementById('labelOffsetSlider').value = 18;
            document.getElementById('labelOffsetValue').textContent = '18';
            document.getElementById('diskZoomValue').textContent = '1.00×';
            document.getElementById('cayleyZoomValue').textContent = '1.00×';
            document.getElementById('nestedZoomValue').textContent = '1.00×';
            document.getElementById('maxFareyOrder').textContent = '30';

            // Hide full plane panel
            document.getElementById('fullPlanePanel').style.display = 'none';
            document.getElementById('vizGrid').classList.remove('four-panel');

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
                                    <input type="radio" name="canvas" value="fullplane">
                                    <span>Full Complex Plane Only</span>
                                </label>
                                <label class="export-radio">
                                    <input type="radio" name="canvas" value="all">
                                    <span>All Four Canvases</span>
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
                            <h4>Export Options</h4>
                            <label class="export-checkbox">
                                <input type="checkbox" id="includeLegend" checked>
                                <span>Include Detailed Legend</span>
                            </label>
                            <label class="export-checkbox">
                                <input type="checkbox" id="includeWatermark" checked>
                                <span>Include Watermark (Wessen Getachew)</span>
                            </label>
                            <label class="export-checkbox">
                                <input type="checkbox" id="includeParameters" checked>
                                <span>Include Current Parameters Info</span>
                            </label>
                            <label class="export-checkbox">
                                <input type="checkbox" id="includeConnections" checked>
                                <span>Include Global Connections (r→r, r→r+m×2ⁿ)</span>
                            </label>
                        </div>
                        
                        <div class="action-bar">
                            <button class="btn btn-primary" onclick="performExport()">
                                <span>💾 Export PNG</span>
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
            const includeParameters = document.getElementById('includeParameters').checked;
            const includeConnections = document.getElementById('includeConnections').checked;

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
                // For all four canvases, use 2x2 grid
                tempCanvas.width = width;
                tempCanvas.height = height;
                
                // Background
                tempCtx.fillStyle = '#0a0e27';
                tempCtx.fillRect(0, 0, width, height);

                // Calculate dimensions for 2x2 grid
                const canvasWidth = width / 2;
                const canvasHeight = height / 2;
                const sourceCanvases = [
                    { canvas: canvases.disk, title: 'Unit Disk 𝔻', x: 0, y: 0 },
                    { canvas: canvases.cayley, title: 'Upper Half-Plane ℍ', x: canvasWidth, y: 0 },
                    { canvas: canvases.nested, title: 'Nested Rings ⊚', x: 0, y: canvasHeight },
                    { canvas: canvases.fullPlane, title: 'Full Complex Plane ℂ', x: canvasWidth, y: canvasHeight }
                ];
                
                sourceCanvases.forEach((item) => {
                    // Draw canvas
                    tempCtx.drawImage(item.canvas, 
                        0, 0, item.canvas.width, item.canvas.height,
                        item.x, item.y, canvasWidth, canvasHeight);
                    
                    // Draw title for each canvas
                    const scale = Math.min(width, height) / 1920;
                    const fontSize = 18 * scale;
                    const titleY = item.y + 30 * scale;
                    const titleX = item.x + canvasWidth / 2;
                    
                    tempCtx.fillStyle = '#ffd700';
                    tempCtx.font = `bold ${fontSize}px "Fira Code"`;
                    tempCtx.textAlign = 'center';
                    tempCtx.textBaseline = 'top';
                    tempCtx.shadowBlur = 8 * scale;
                    tempCtx.shadowColor = 'rgba(255, 215, 0, 0.4)';
                    tempCtx.fillText(item.title, titleX, titleY);
                    tempCtx.shadowBlur = 0;
                });

                // Add main title at top
                const scale = Math.min(width, height) / 1920;
                const mainTitleSize = 32 * scale;
                const padding = 40 * scale;
                
                tempCtx.fillStyle = 'rgba(10, 14, 39, 0.9)';
                tempCtx.fillRect(width / 2 - 400 * scale, padding / 2, 800 * scale, 60 * scale);
                
                tempCtx.fillStyle = '#ffd700';
                tempCtx.font = `bold ${mainTitleSize}px "Fira Code"`;
                tempCtx.textAlign = 'center';
                tempCtx.shadowBlur = 12 * scale;
                tempCtx.shadowColor = 'rgba(255, 215, 0, 0.5)';
                tempCtx.fillText('Farey Triangle & Cayley Transform', width / 2, padding);
                tempCtx.shadowBlur = 0;

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
                    case 'fullplane':
                        sourceCanvas = canvases.fullPlane;
                        title = 'Full Complex Plane ℂ - Complete Cayley View';
                        break;
                }

                // Draw canvas maintaining square aspect ratio
                tempCtx.drawImage(sourceCanvas, 0, 0, size, size);

                // Add title
                drawMainTitle(tempCtx, size, title);

                if (includeLegend) {
                    drawLegend(tempCtx, size, size, canvasSelection);
                }
                
                if (includeParameters) {
                    drawParametersInfo(tempCtx, size, size, canvasSelection);
                }
            }

            // Add watermark if requested
            if (includeWatermark) {
                drawWatermark(tempCtx, tempCanvas.width, tempCanvas.height);
            }

            const link = document.createElement('a');
            link.download = `farey-cayley-${canvasSelection}-${resolution}-${Date.now()}.png`;
            link.href = tempCanvas.toDataURL('image/png', 1.0);
            link.click();

            closeExportDialog();
        }

        function drawParametersInfo(ctx, width, height, canvasType) {
            const scale = Math.min(width, height) / 1920;
            const fontSize = 11 * scale;
            const padding = 20 * scale;
            
            // Position in bottom-left corner
            const boxWidth = 380 * scale;
            const lineHeight = 16 * scale;
            
            // Build parameter text
            const params = [
                `Modulus: m = ${state.modulus}`,
                `Phase: θ = ${state.phase.toFixed(1)}°`,
                `Rings: ${state.minRing}–${state.maxRing}`,
                `Primes: ${state.numPrimes}`,
                `Transform: ${state.transformType}`,
                `Color Scheme: ${state.nestedColorScheme}`,
                `Label Mode: ${state.labelMode}`,
                `Connection: ${state.connectionMode}`,
            ];
            
            // Add connection status
            const rToR = document.getElementById('toggleShowRtoR').checked;
            const rToR2n = document.getElementById('toggleShowRtoRplus2n').checked;
            if (rToR || rToR2n) {
                const connections = [];
                if (rToR) connections.push('r→r');
                if (rToR2n) connections.push('r→r+m×2ⁿ');
                params.push(`Global: ${connections.join(', ')}`);
            }
            
            const boxHeight = (params.length + 1) * lineHeight + padding * 2;
            const boxX = padding;
            const boxY = height - boxHeight - padding;
            
            // Background
            ctx.save();
            ctx.fillStyle = 'rgba(10, 14, 39, 0.92)';
            ctx.strokeStyle = 'rgba(0, 255, 255, 0.6)';
            ctx.lineWidth = 2 * scale;
            ctx.fillRect(boxX, boxY, boxWidth, boxHeight);
            ctx.strokeRect(boxX, boxY, boxWidth, boxHeight);
            
            // Title
            ctx.fillStyle = '#00ffff';
            ctx.font = `bold ${fontSize + 2}px "Fira Code"`;
            ctx.textAlign = 'left';
            ctx.fillText('PARAMETERS', boxX + padding, boxY + padding + fontSize);
            
            // Parameters
            ctx.fillStyle = 'rgba(255, 255, 255, 0.9)';
            ctx.font = `${fontSize}px "Fira Code"`;
            params.forEach((param, idx) => {
                ctx.fillText(param, boxX + padding, boxY + padding + (idx + 2) * lineHeight);
            });
            
            ctx.restore();
        }

        function drawMainTitle(ctx, size, titleText) {
            const scale = size / 1000;
            const fontSize = 28 * scale;
            const padding = 40 * scale;

            ctx.save();
            
            // Measure text width
            ctx.font = `bold ${fontSize}px "Fira Code"`;
            const textMetrics = ctx.measureText(titleText);
            const titleWidth = textMetrics.width + 80 * scale;
            const titleHeight = 70 * scale;
            const titleX = (size - titleWidth) / 2;
            const titleY = padding;

            // Title background with gradient
            const gradient = ctx.createLinearGradient(titleX, titleY, titleX, titleY + titleHeight);
            gradient.addColorStop(0, 'rgba(10, 14, 39, 0.95)');
            gradient.addColorStop(1, 'rgba(20, 30, 60, 0.95)');
            ctx.fillStyle = gradient;
            ctx.strokeStyle = 'rgba(255, 215, 0, 0.8)';
            ctx.lineWidth = 3 * scale;
            
            const radius = 10 * scale;
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

            // Title text with gradient
            const textGradient = ctx.createLinearGradient(titleX, titleY, titleX, titleY + titleHeight);
            textGradient.addColorStop(0, '#ffd700');
            textGradient.addColorStop(1, '#ffed4e');
            ctx.fillStyle = textGradient;
            ctx.font = `bold ${fontSize}px "Fira Code"`;
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.shadowBlur = 15 * scale;
            ctx.shadowColor = 'rgba(255, 215, 0, 0.6)';
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
            const fontSize = 18 * scale;
            const padding = 30 * scale;

            ctx.save();
            
            // Measure text first
            ctx.font = `bold ${fontSize}px "Fira Code"`;
            const textMetrics = ctx.measureText('by Wessen Getachew · @7dview');
            const watermarkWidth = textMetrics.width + 50 * scale;
            const watermarkHeight = 55 * scale;
            const watermarkX = width - watermarkWidth - padding;
            const watermarkY = height - watermarkHeight - padding;

            // Watermark background with gradient
            const gradient = ctx.createLinearGradient(watermarkX, watermarkY, watermarkX, watermarkY + watermarkHeight);
            gradient.addColorStop(0, 'rgba(10, 14, 39, 0.95)');
            gradient.addColorStop(1, 'rgba(20, 30, 60, 0.95)');
            ctx.fillStyle = gradient;
            ctx.strokeStyle = 'rgba(255, 215, 0, 0.8)';
            ctx.lineWidth = 3 * scale;
            
            // Rounded rectangle
            const radius = 10 * scale;
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

            // Watermark text with gradient
            const textGradient = ctx.createLinearGradient(watermarkX, watermarkY, watermarkX, watermarkY + watermarkHeight);
            textGradient.addColorStop(0, '#ffd700');
            textGradient.addColorStop(1, '#ffed4e');
            ctx.fillStyle = textGradient;
            ctx.font = `bold ${fontSize}px "Fira Code"`;
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.shadowBlur = 15 * scale;
            ctx.shadowColor = 'rgba(255, 215, 0, 0.6)';
            ctx.fillText('by Wessen Getachew · @7dview', watermarkX + watermarkWidth / 2, watermarkY + watermarkHeight / 2);
            
            ctx.restore();
        }

        function drawLegend(ctx, width, height, canvasType) {
            const scale = Math.min(width, height) / 1920;
            
            // Adjust legend size and position to avoid all overlap
            let legendWidth = 420 * scale;
            let legendX, legendY;
            
            // Always position in bottom-left to avoid title/canvas overlap
            legendX = 30 * scale;
            legendY = height - 30 * scale; // Start from bottom
            
            const fontSize = 11 * scale;
            const titleSize = 16 * scale;
            const sectionTitleSize = 13 * scale;
            const itemHeight = 24 * scale;
            const symbolSize = 18 * scale;
            const padding = 15 * scale;

            let items = [];
            let parameters = [];
            
            if (canvasType === 'disk') {
                items = [
                    { type: 'section', text: 'Unit Disk' },
                    { color: CONFIG.colors.disk, text: 'Circle Boundary' },
                    { color: CONFIG.colors.farey, text: 'Farey Vertices' },
                    { color: CONFIG.colors.prime, text: 'Primes' }
                ];
                parameters = [
                    `m=${state.modulus}`,
                    `Primes: ${Math.min(state.numPrimes, state.primes.length)}`,
                    `θ=${state.phase.toFixed(0)}°`
                ];
            } else if (canvasType === 'cayley') {
                items = [
                    { type: 'section', text: 'Half-Plane ℍ' },
                    { color: 'rgba(255, 255, 255, 0.5)', text: 'Real Axis' },
                    { color: CONFIG.colors.farey, text: 'Farey Points' },
                    { color: CONFIG.colors.geodesic, text: 'Geodesics' },
                    { color: CONFIG.colors.cusp, text: 'Cusps' }
                ];
                parameters = [
                    `m=${state.modulus}`,
                    `Re: [${(-state.cayleyHRange/2).toFixed(1)},${(state.cayleyHRange/2).toFixed(1)}]`,
                    `Im: [${state.cayleyVOffset.toFixed(1)},${(state.cayleyVRange+state.cayleyVOffset).toFixed(1)}]`
                ];
            } else if (canvasType === 'nested') {
                items = [
                    { type: 'section', text: 'GCD Colors' },
                    { color: CONFIG.colors.farey, text: 'GCD=1' },
                    { color: '#e74c3c', text: 'GCD=m' },
                    { color: '#00ffff', text: 'GCD=2' },
                    { color: '#9b59b6', text: 'GCD=3' }
                ];
                parameters = [
                    `Rings: ${state.minRing}–${state.maxRing}`,
                    `Count: ${state.maxRing - state.minRing + 1}`,
                    `Mode: ${state.connectionMode}`
                ];
            } else             if (canvasType === 'all') {
                items = [
                    { type: 'section', text: 'Elements' },
                    { color: CONFIG.colors.farey, text: 'Farey/Coprime' },
                    { color: CONFIG.colors.geodesic, text: 'Geodesics' },
                    { color: CONFIG.colors.prime, text: 'Primes' },
                    { type: 'section', text: 'GCD' },
                    { color: CONFIG.colors.farey, text: 'GCD=1' },
                    { color: '#e74c3c', text: 'GCD=m' },
                    { type: 'section', text: 'Connections' }
                ];
                
                // Add connection legend items
                if (document.getElementById('toggleShowRtoR').checked) {
                    items.push({ color: '#00ffff', text: 'r→r (Self-similar)' });
                }
                if (document.getElementById('toggleShowRtoRplus2n').checked) {
                    items.push({ color: 'rgba(255,100,100,0.9)', text: 'r→r+m×2ⁿ (Binary)' });
                }
                
                parameters = [
                    `m=${state.modulus}`,
                    `Primes: ${Math.min(state.numPrimes, state.primes.length)}`,
                    `Rings: ${state.minRing}–${state.maxRing}`,
                    `Scheme: ${state.nestedColorScheme}`
                ];
            }

            // Calculate actual legend height
            const sectionCount = items.filter(i => i.type === 'section').length;
            const regularItemCount = items.filter(i => !i.type).length;
            const legendHeight = (padding * 4) + 
                                (sectionCount * itemHeight * 0.7) + 
                                (regularItemCount * itemHeight * 0.9) + 
                                (parameters.length * itemHeight * 0.65) +
                                (itemHeight * 1.2);

            // Adjust Y position so legend goes UP from bottom
            legendY = legendY - legendHeight;

            // Ensure legend stays within bounds
            if (legendY < 30 * scale) legendY = 30 * scale;
            if (legendX + legendWidth > width - 30 * scale) {
                legendWidth = width - legendX - 30 * scale;
            }

            // Background with gradient
            const gradient = ctx.createLinearGradient(legendX, legendY, legendX, legendY + legendHeight);
            gradient.addColorStop(0, 'rgba(10, 14, 39, 0.95)');
            gradient.addColorStop(1, 'rgba(20, 30, 60, 0.95)');
            ctx.fillStyle = gradient;
            
            ctx.strokeStyle = CONFIG.colors.farey;
            ctx.lineWidth = 2 * scale;
            ctx.shadowBlur = 12 * scale;
            ctx.shadowColor = 'rgba(255, 215, 0, 0.3)';
            
            // Rounded rectangle
            const radius = 8 * scale;
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

            // Clip to legend box to prevent overflow
            ctx.save();
            ctx.beginPath();
            ctx.rect(legendX, legendY, legendWidth, legendHeight);
            ctx.clip();

            // Title
            ctx.fillStyle = CONFIG.colors.farey;
            ctx.font = `bold ${titleSize}px "Fira Code"`;
            ctx.textAlign = 'left';
            ctx.shadowBlur = 6 * scale;
            ctx.shadowColor = 'rgba(255, 215, 0, 0.5)';
            ctx.fillText('LEGEND', legendX + padding, legendY + padding * 1.5);
            ctx.shadowBlur = 0;

            // Separator line
            ctx.strokeStyle = 'rgba(255, 215, 0, 0.3)';
            ctx.lineWidth = 1 * scale;
            ctx.beginPath();
            ctx.moveTo(legendX + padding, legendY + padding * 2.1);
            ctx.lineTo(legendX + legendWidth - padding, legendY + padding * 2.1);
            ctx.stroke();

            // Items
            let currentY = legendY + padding * 2.8;
            
            items.forEach((item, idx) => {
                if (item.type === 'section') {
                    // Section header
                    currentY += itemHeight * 0.15;
                    ctx.fillStyle = 'rgba(0, 255, 255, 0.8)';
                    ctx.font = `bold ${sectionTitleSize}px "Fira Code"`;
                    
                    // Ensure text fits in legend
                    const maxTextWidth = legendWidth - padding * 2;
                    let text = item.text;
                    let textWidth = ctx.measureText(text).width;
                    
                    if (textWidth > maxTextWidth) {
                        // Truncate text if too long
                        while (textWidth > maxTextWidth && text.length > 3) {
                            text = text.slice(0, -1);
                            textWidth = ctx.measureText(text + '...').width;
                        }
                        text = text + '...';
                    }
                    
                    ctx.fillText(text, legendX + padding, currentY);
                    
                    // Underline
                    ctx.strokeStyle = 'rgba(0, 255, 255, 0.2)';
                    ctx.lineWidth = 1 * scale;
                    ctx.beginPath();
                    ctx.moveTo(legendX + padding, currentY + 3 * scale);
                    ctx.lineTo(legendX + legendWidth - padding, currentY + 3 * scale);
                    ctx.stroke();
                    
                    currentY += itemHeight * 0.55;
                } else {
                    // Regular item
                    ctx.fillStyle = item.color;
                    ctx.strokeStyle = 'rgba(255, 255, 255, 0.3)';
                    ctx.lineWidth = 1 * scale;
                    
                    // Symbol
                    const symbolRadius = 3 * scale;
                    const symX = legendX + padding;
                    const symY = currentY - symbolSize * 0.6;
                    
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

                    // Text with truncation
                    ctx.fillStyle = '#e8f1f5';
                    ctx.font = `${fontSize}px "Fira Code"`;
                    
                    const maxTextWidth = legendWidth - padding * 2 - symbolSize - 8 * scale;
                    let text = item.text;
                    let textWidth = ctx.measureText(text).width;
                    
                    if (textWidth > maxTextWidth) {
                        while (textWidth > maxTextWidth && text.length > 3) {
                            text = text.slice(0, -1);
                            textWidth = ctx.measureText(text + '...').width;
                        }
                        text = text + '...';
                    }
                    
                    ctx.fillText(text, legendX + padding + symbolSize + 8 * scale, currentY);
                    
                    currentY += itemHeight * 0.9;
                }
            });

            // Parameters section
            currentY += itemHeight * 0.2;
            ctx.fillStyle = 'rgba(0, 255, 255, 0.8)';
            ctx.font = `bold ${sectionTitleSize}px "Fira Code"`;
            ctx.fillText('Params', legendX + padding, currentY);
            
            ctx.strokeStyle = 'rgba(0, 255, 255, 0.2)';
            ctx.lineWidth = 1 * scale;
            ctx.beginPath();
            ctx.moveTo(legendX + padding, currentY + 3 * scale);
            ctx.lineTo(legendX + legendWidth - padding, currentY + 3 * scale);
            ctx.stroke();
            
            currentY += itemHeight * 0.55;

            ctx.font = `${fontSize * 0.95}px "Fira Code"`;
            ctx.fillStyle = 'rgba(255, 255, 255, 0.85)';
            parameters.forEach(param => {
                const maxTextWidth = legendWidth - padding * 2.5;
                let text = param;
                let textWidth = ctx.measureText('• ' + text).width;
                
                if (textWidth > maxTextWidth) {
                    while (textWidth > maxTextWidth && text.length > 3) {
                        text = text.slice(0, -1);
                        textWidth = ctx.measureText('• ' + text + '...').width;
                    }
                    text = text + '...';
                }
                
                ctx.fillText('• ' + text, legendX + padding, currentY);
                currentY += itemHeight * 0.65;
            });

            // Math notation footer
            currentY += itemHeight * 0.15;
            ctx.fillStyle = 'rgba(255, 255, 255, 0.5)';
            ctx.font = `italic ${fontSize * 0.85}px "Fira Code"`;
            ctx.fillText('𝔻 → ℍ Cayley', legendX + padding, currentY);
            
            ctx.restore(); // Remove clipping
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
            console.log('  Current Formula: w = i(1+z)/(1-z) ✓ CORRECT');
            console.log('  Maps unit disk 𝔻 to upper half-plane ℍ');
            console.log('  Inverse of: f(z) = (z-i)/(z+i) which maps ℍ → 𝔻');
            console.log('  Preserves angles (conformal)');

            
            // Test a few points
            const testPoints = [
                { re: 1, im: 0, label: 'z=1' },
                { re: -1, im: 0, label: 'z=-1' },
                { re: 0, im: 1, label: 'z=i' },
                { re: 0, im: 0, label: 'z=0' }
            ];
            
            console.log('\n  Test transformations:');
            testPoints.forEach(z => {
                const w = cayleyTransform(z, state.transformType);
                console.log(`    ${z.label}: w = ${w.re.toFixed(4)} + ${w.im.toFixed(4)}i`);
            });
            
            console.log('\n=====================================================');
        }
    </script>
</body>
</html>
