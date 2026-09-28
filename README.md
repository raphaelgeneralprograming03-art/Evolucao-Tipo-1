
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador de Civilização Tipo I - Escala Kardashev</title>
    <style>
        :root {
            --bg-color: #020617;
            --panel-bg: rgba(15, 23, 42, 0.88);
            --border-color: rgba(56, 189, 248, 0.35);
            --accent-cyan: #38bdf8;
            --accent-gold: #f59e0b;
            --accent-green: #10b981;
            --accent-purple: #c084fc;
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            user-select: none;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            height: 100vh;
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        header {
            height: 60px;
            background: linear-gradient(180deg, rgba(15, 23, 42, 0.95) 0%, rgba(2, 6, 23, 0.7) 100%);
            border-bottom: 1px solid var(--border-color);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 25px;
            z-index: 10;
        }

        header h1 {
            font-size: 1.05rem;
            letter-spacing: 2px;
            color: var(--accent-cyan);
            text-transform: uppercase;
        }

        .status-badge {
            font-size: 0.75rem;
            padding: 4px 12px;
            border-radius: 12px;
            background: rgba(16, 185, 129, 0.15);
            border: 1px solid var(--accent-green);
            color: var(--accent-green);
            letter-spacing: 1px;
        }

        .main-container {
            display: grid;
            grid-template-columns: 360px 1fr;
            height: calc(100vh - 60px);
            position: relative;
        }

        /* PAINEL LATERAL DE CONTROLE */
        .control-panel {
            background: var(--panel-bg);
            border-right: 1px solid var(--border-color);
            backdrop-filter: blur(12px);
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 16px;
            overflow-y: auto;
            z-index: 5;
        }

        .kardashev-box {
            background: radial-gradient(circle, rgba(56, 189, 248, 0.18) 0%, rgba(2, 6, 23, 0.6) 100%);
            border: 1px solid var(--accent-cyan);
            border-radius: 10px;
            padding: 16px;
            text-align: center;
            box-shadow: 0 0 20px rgba(56, 189, 248, 0.15);
        }

        .kardashev-score {
            font-size: 2.3rem;
            font-family: monospace;
            font-weight: bold;
            color: #fff;
            text-shadow: 0 0 15px var(--accent-cyan);
            margin: 4px 0;
        }

        .section-title {
            font-size: 0.78rem;
            text-transform: uppercase;
            letter-spacing: 1.5px;
            color: var(--accent-cyan);
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 6px;
        }

        .metric-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .metric-header {
            display: flex;
            justify-content: space-between;
            font-size: 0.8rem;
        }

        .metric-value {
            font-family: monospace;
            font-weight: bold;
            color: var(--accent-cyan);
        }

        input[type="range"] {
            width: 100%;
            height: 6px;
            border-radius: 3px;
            background: rgba(255, 255, 255, 0.1);
            outline: none;
            accent-color: var(--accent-cyan);
            cursor: pointer;
        }

        .toggle-box {
            display: flex;
            align-items: center;
            justify-content: space-between;
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 6px;
            padding: 8px 12px;
            font-size: 0.78rem;
        }

        .switch {
            position: relative;
            display: inline-block;
            width: 36px;
            height: 18px;
        }

        .switch input { opacity: 0; width: 0; height: 0; }

        .slider {
            position: absolute; cursor: pointer; top: 0; left: 0; right: 0; bottom: 0;
            background-color: rgba(255,255,255,0.2); transition: .3s; border-radius: 18px;
        }

        .slider:before {
            position: absolute; content: ""; height: 12px; width: 12px; left: 3px; bottom: 3px;
            background-color: white; transition: .3s; border-radius: 50%;
        }

        input:checked + .slider { background-color: var(--accent-green); }
        input:checked + .slider:before { transform: translateX(18px); }

        .info-card {
            background: rgba(0, 0, 0, 0.35);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 8px;
            padding: 12px;
            font-size: 0.8rem;
            line-height: 1.45;
            color: var(--text-muted);
        }

        .info-card strong {
            color: var(--text-main);
        }

        /* VIEWPORT CANVAS */
        .viewport {
            position: relative;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle at center, #0B132B 0%, #020617 100%);
            overflow: hidden;
        }

        canvas {
            width: 100%;
            height: 100%;
            display: block;
        }

        .hud-overlay {
            position: absolute;
            bottom: 20px;
            right: 20px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 15px;
            font-family: monospace;
            font-size: 0.75rem;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            gap: 6px;
            backdrop-filter: blur(10px);
        }

        .hud-line {
            display: flex;
            justify-content: space-between;
            gap: 20px;
        }

        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: var(--border-color); border-radius: 3px; }
    </style>
</head>
<body>

    <header>
        <h1>CIVILIZAÇÃO TIPO I: DOMÍNIO PLANETÁRIO</h1>
        <div class="status-badge">REDE ENERGÉTICA: UNIFICADA</div>
    </header>

    <div class="main-container">
        <!-- PAINEL LATERAL -->
        <aside class="control-panel">
            <div class="kardashev-box">
                <div style="font-size: 0.72rem; color: var(--text-muted); text-transform: uppercase;">Métrica de Kardashev</div>
                <div class="kardashev-score" id="k-score">K 1.000</div>
                <div style="font-size: 0.78rem; color: var(--accent-gold);" id="power-display">1.00 × 10¹⁶ W</div>
            </div>

            <div class="section-title">1. Captação de Insolamento Solar</div>
            <div class="metric-group">
                <div class="metric-header">
                    <span>Aproveitamento Térmico Orbital</span>
                    <span class="metric-value" id="val-solar">100%</span>
                </div>
                <input type="range" id="input-solar" min="10" max="100" value="100">
            </div>

            <div class="metric-group">
                <div class="metric-header">
                    <span>Ajuste da Demanda (x10¹⁵ W)</span>
                    <span class="metric-value" id="val-demand">10.0 W</span>
                </div>
                <input type="range" id="input-demand" min="0.1" max="20.0" step="0.1" value="10.0">
            </div>

            <div class="section-title">2. Estabilização Geotécnica</div>
            
            <div class="toggle-box">
                <span>Atenuação Vulcânica e Tectônica</span>
                <label class="switch"><input type="checkbox" id="sw-tectonic" checked><span class="slider"></span></label>
            </div>

            <div class="toggle-box">
                <span>Harmonização Climática Ativa</span>
                <label class="switch"><input type="checkbox" id="sw-climate" checked><span class="slider"></span></label>
            </div>

            <div class="toggle-box">
                <span>Escudo Geomagnético Direcionado</span>
                <label class="switch"><input type="checkbox" id="sw-shield" checked><span class="slider"></span></label>
            </div>

            <div class="section-title">3. Diagnóstico da Civilização</div>
            <div class="info-card">
                <strong>Status do Ecossistema:</strong><br>
                • Matriz energética 100% limpa e renovável.<br>
                • Neutralização total de catástrofes climáticas.<br>
                • Eliminação absoluta da escassez de energia.<br>
                • Gestão unificada dos recursos térmicos globais.
            </div>
        </aside>

        <!-- VIEWPORT CANVAS SIMULAÇÃO -->
        <main class="viewport">
            <canvas id="simCanvas"></canvas>

            <div class="hud-overlay">
                <div class="hud-line"><span>TEMPERATURA PLANETÁRIA:</span><span id="hud-temp" style="color: var(--accent-green)">14.2 °C (ESTÁVEL)</span></div>
                <div class="hud-line"><span>RISCO DE ANOMALIAS:</span><span id="hud-risk" style="color: var(--accent-green)">0.0%</span></div>
                <div class="hud-line"><span>EFICIÊNCIA DA REDE:</span><span id="hud-eff" style="color: var(--accent-cyan)">99.8%</span></div>
                <div class="hud-line"><span>ESCASSEZ ENERGÉTICA:</span><span style="color: var(--accent-green)">ERRADICADA</span></div>
            </div>
        </main>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        // Estado da Simulação
        const state = {
            powerWatts: 1e16,
            solarEfficiency: 100,
            kardashevScale: 1.0,
            tectonicControl: true,
            climateHarmonization: true,
            geomagneticShield: true,
            rotationAngle: 0,
            beamPulse: 0
        };

        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Elementos de Entrada
        const inputSolar = document.getElementById('input-solar');
        const inputDemand = document.getElementById('input-demand');
        const swTectonic = document.getElementById('sw-tectonic');
        const swClimate = document.getElementById('sw-climate');
        const swShield = document.getElementById('sw-shield');

        // Coletores orbitais e partículas de energia
        const orbitalSatellites = [];
        const NUM_SATS = 16;
        for (let i = 0; i < NUM_SATS; i++) {
            orbitalSatellites.push({
                angle: (i / NUM_SATS) * Math.PI * 2,
                distance: 175
            });
        }

        const energyBeams = [];
        const NUM_BEAMS = 40;
        for (let i = 0; i < NUM_BEAMS; i++) {
            energyBeams.push({
                angle: Math.random() * Math.PI * 2,
                progress: Math.random(),
                speed: 0.005 + Math.random() * 0.008
            });
        }

        // Atualização dos Cálculos Físicos
        function updatePhysics() {
            state.solarEfficiency = parseInt(inputSolar.value);
            state.powerWatts = parseFloat(inputDemand.value) * 1e15 * (state.solarEfficiency / 100);

            // Fórmula de Kardashev: K = (log10(P) - 6) / 10
            state.kardashevScale = (Math.log10(state.powerWatts) - 6) / 10;

            state.tectonicControl = swTectonic.checked;
            state.climateHarmonization = swClimate.checked;
            state.geomagneticShield = swShield.checked;

            // Interface
            document.getElementById('k-score').innerText = `K ${state.kardashevScale.toFixed(3)}`;
            
            const powerScaled = (state.powerWatts / 1e15).toFixed(2);
            document.getElementById('power-display').innerText = `${powerScaled} × 10¹⁵ W`;

            document.getElementById('val-solar').innerText = `${state.solarEfficiency}%`;
            document.getElementById('val-demand').innerText = `${parseFloat(inputDemand.value).toFixed(1)} W`;

            // HUD
            let risk = 0;
            if (!state.tectonicControl) risk += 35;
            if (!state.climateHarmonization) risk += 45;
            if (!state.geomagneticShield) risk += 20;

            const riskHUD = document.getElementById('hud-risk');
            riskHUD.innerText = `${risk.toFixed(1)}%`;
            riskHUD.style.color = risk === 0 ? 'var(--accent-green)' : (risk < 50 ? 'var(--accent-gold)' : '#ef4444');

            document.getElementById('hud-temp').innerText = state.climateHarmonization ? '14.2 °C (ESTÁVEL)' : '18.6 °C (FLUTUANTE)';
            document.getElementById('hud-eff').innerText = `${(98 + (state.solarEfficiency * 0.018)).toFixed(1)}%`;
        }

        // Eventos
        inputSolar.addEventListener('input', updatePhysics);
        inputDemand.addEventListener('input', updatePhysics);
        swTectonic.addEventListener('change', updatePhysics);
        swClimate.addEventListener('change', updatePhysics);
        swShield.addEventListener('change', updatePhysics);

        // Loop de Renderização no Canvas
        function render() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            const cx = canvas.width / 2;
            const cy = canvas.height / 2;
            const earthRadius = 110;

            state.rotationAngle += 0.0025;
            state.beamPulse += 0.03;

            // 1. Grade de Fundo Sci-Fi
            ctx.strokeStyle = 'rgba(56, 189, 248, 0.04)';
            ctx.lineWidth = 1;
            const gridSize = 45;
            for (let x = 0; x < canvas.width; x += gridSize) {
                ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
            }
            for (let y = 0; y < canvas.height; y += gridSize) {
                ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke();
            }

            // 2. Escudo Geomagnético Ativo
            if (state.geomagneticShield) {
                const shieldRadius = earthRadius + 90;
                const shieldGrad = ctx.createRadialGradient(cx, cy, earthRadius + 40, cx, cy, shieldRadius);
                shieldGrad.addColorStop(0, 'rgba(56, 189, 248, 0)');
                shieldGrad.addColorStop(0.8, 'rgba(56, 189, 248, 0.08)');
                shieldGrad.addColorStop(1, 'rgba(56, 189, 248, 0.35)');

                ctx.beginPath();
                ctx.arc(cx, cy, shieldRadius, 0, Math.PI * 2);
                ctx.fillStyle = shieldGrad;
                ctx.fill();

                ctx.strokeStyle = `rgba(56, 189, 248, ${0.3 + Math.sin(state.beamPulse) * 0.15})`;
                ctx.lineWidth = 1.5;
                ctx.stroke();
            }

            // 3. Planeta Terra
            const earthGrad = ctx.createRadialGradient(cx - 25, cy - 25, 15, cx, cy, earthRadius);
            earthGrad.addColorStop(0, '#0284c7');
            earthGrad.addColorStop(0.65, '#0369a1');
            earthGrad.addColorStop(1, '#0f172a');

            ctx.beginPath();
            ctx.arc(cx, cy, earthRadius, 0, Math.PI * 2);
            ctx.fillStyle = earthGrad;
            ctx.fill();

            // Continentes e Matriz Planetária Noturna (Luzes Tecnológicas)
            ctx.save();
            ctx.translate(cx, cy);
            ctx.rotate(state.rotationAngle);

            ctx.fillStyle = '#15803d';
            ctx.beginPath();
            ctx.arc(-35, -25, 45, 0, Math.PI * 2);
            ctx.arc(35, 40, 35, 0, Math.PI * 2);
            ctx.arc(40, -45, 28, 0, Math.PI * 2);
            ctx.fill();

            // Malha de Distribuição Global (Conexões)
            ctx.strokeStyle = 'rgba(245, 158, 11, 0.45)';
            ctx.lineWidth = 0.8;
            ctx.beginPath();
            ctx.arc(0, 0, earthRadius - 2, 0, Math.PI * 2);
            ctx.moveTo(-40, -20); ctx.lineTo(30, 40);
            ctx.moveTo(30, 40); ctx.lineTo(40, -40);
            ctx.moveTo(40, -40); ctx.lineTo(-40, -20);
            ctx.stroke();

            ctx.restore();

            // Atmosfera Límpida e Estável
            const atmosGrad = ctx.createRadialGradient(cx, cy, earthRadius - 5, cx, cy, earthRadius + 22);
            atmosGrad.addColorStop(0, 'rgba(0,0,0,0)');
            atmosGrad.addColorStop(0.7, 'rgba(56, 189, 248, 0.25)');
            atmosGrad.addColorStop(1, 'rgba(56, 189, 248, 0.0)');

            ctx.beginPath();
            ctx.arc(cx, cy, earthRadius + 22, 0, Math.PI * 2);
            ctx.fillStyle = atmosGrad;
            ctx.fill();

            // 4. Anel e Coletores Orbitais
            const orbitRadius = earthRadius + 60;
            ctx.beginPath();
            ctx.arc(cx, cy, orbitRadius, 0, Math.PI * 2);
            ctx.strokeStyle = 'rgba(56, 189, 248, 0.25)';
            ctx.setLineDash([6, 6]);
            ctx.stroke();
            ctx.setLineDash([]);

            // Satélites Orbitais
            orbitalSatellites.forEach(sat => {
                sat.angle += 0.004;
                const sx = cx + Math.cos(sat.angle) * orbitRadius;
                const sy = cy + Math.sin(sat.angle) * orbitRadius;

                ctx.beginPath();
                ctx.arc(sx, sy, 3.5, 0, Math.PI * 2);
                ctx.fillStyle = '#f59e0b';
                ctx.shadowColor = '#f59e0b';
                ctx.shadowBlur = 8;
                ctx.fill();
                ctx.shadowBlur = 0;
            });

            // 5. Transmissão Sem Fio de Energia (Feixes da Órbita para a Superfície)
            energyBeams.forEach(beam => {
                beam.progress += beam.speed;
                if (beam.progress > 1) {
                    beam.progress = 0;
                    beam.angle = Math.random() * Math.PI * 2;
                }

                const rStart = orbitRadius;
                const rEnd = earthRadius;
                const currentR = rStart - (rStart - rEnd) * beam.progress;

                const bx = cx + Math.cos(beam.angle) * currentR;
                const by = cy + Math.sin(beam.angle) * currentR;

                ctx.beginPath();
                ctx.arc(bx, by, 1.8, 0, Math.PI * 2);
                ctx.fillStyle = `rgba(56, 189, 248, ${1 - beam.progress})`;
                ctx.fill();
            });

            requestAnimationFrame(render);
        }

        // Inicialização
        updatePhysics();
        render();
    </script>
</body>
</html>
