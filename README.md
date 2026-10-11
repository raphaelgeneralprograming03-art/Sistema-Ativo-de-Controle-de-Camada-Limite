
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador: Controle MHD de Camada Limite Hipersônica</title>
    <style>
        :root {
            --bg-color: #020617;
            --panel-bg: rgba(10, 22, 40, 0.92);
            --border-color: #00f0ff;
            --accent-green: #00ff66;
            --accent-red: #ff3333;
            --accent-amber: #f59e0b;
            --accent-cyan: #38bdf8;
            --text-main: #c0d8ef;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Courier New', Courier, monospace;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            height: 100vh;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        header {
            background: linear-gradient(90deg, #021226, #0a2540, #021226);
            border-bottom: 2px solid var(--border-color);
            padding: 10px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        header h1 {
            font-size: 1.05rem;
            color: var(--border-color);
            letter-spacing: 2px;
        }

        .badges {
            display: flex;
            gap: 10px;
        }

        .badge {
            background: rgba(0, 240, 255, 0.15);
            border: 1px solid var(--border-color);
            color: var(--border-color);
            padding: 3px 8px;
            font-size: 0.7rem;
            font-weight: bold;
            border-radius: 3px;
        }

        .badge-danger {
            background: rgba(255, 51, 51, 0.2);
            border-color: var(--accent-red);
            color: var(--accent-red);
            animation: pulse 2s infinite;
        }

        .main-container {
            display: flex;
            flex: 1;
            gap: 12px;
            padding: 12px;
            overflow: hidden;
        }

        /* Painel de Controle Esquerdo */
        .control-panel {
            width: 330px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 12px;
            display: flex;
            flex-direction: column;
            gap: 12px;
            overflow-y: auto;
            box-shadow: 0 0 15px rgba(0, 240, 255, 0.1);
        }

        .panel-section h3 {
            font-size: 0.78rem;
            color: var(--border-color);
            margin-bottom: 6px;
            border-bottom: 1px solid rgba(0, 240, 255, 0.3);
            padding-bottom: 3px;
            letter-spacing: 1px;
        }

        .btn-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        button {
            background: rgba(0, 240, 255, 0.1);
            border: 1px solid var(--border-color);
            color: var(--text-main);
            padding: 8px 10px;
            text-align: left;
            font-size: 0.72rem;
            cursor: pointer;
            border-radius: 4px;
            transition: all 0.3s ease;
        }

        button:hover {
            background: rgba(0, 240, 255, 0.3);
            color: #fff;
            box-shadow: 0 0 8px var(--border-color);
        }

        button.action-active {
            border-color: var(--accent-green);
            background: rgba(0, 255, 102, 0.2);
            color: #99ffbb;
            font-weight: bold;
        }

        button.action-danger {
            border-color: var(--accent-red);
            background: rgba(255, 51, 51, 0.15);
            color: #ff9999;
        }

        /* Centro: Visão Geral + Canvas */
        .center-container {
            flex: 1;
            display: flex;
            flex-direction: column;
            gap: 12px;
            overflow: hidden;
        }

        .app-overview {
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 10px 15px;
            font-size: 0.72rem;
            line-height: 1.4;
            color: #cbd5e1;
        }

        .app-overview strong {
            color: var(--accent-cyan);
        }

        .viewport-container {
            flex: 1.5;
            background: #010409;
            border: 1px solid var(--border-color);
            border-radius: 6px;
            display: flex;
            flex-direction: column;
            position: relative;
            overflow: hidden;
        }

        .viewport-header {
            background: rgba(0, 240, 255, 0.05);
            padding: 6px 12px;
            font-size: 0.7rem;
            display: flex;
            justify-content: space-between;
            border-bottom: 1px solid rgba(0, 240, 255, 0.2);
        }

        canvas {
            flex: 1;
            display: block;
            width: 100%;
            height: 100%;
        }

        /* Telemetria Direita */
        .telemetry-panel {
            width: 310px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 12px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            overflow-y: auto;
        }

        .metric-box {
            background: rgba(2, 12, 38, 0.7);
            border: 1px solid rgba(0, 240, 255, 0.3);
            border-radius: 4px;
            padding: 8px 10px;
        }

        .metric-title {
            font-size: 0.65rem;
            color: var(--border-color);
            margin-bottom: 3px;
        }

        .metric-value {
            font-size: 0.95rem;
            font-weight: bold;
            color: #fff;
        }

        .status-ok { color: var(--accent-green); }
        .status-alert { color: var(--accent-red); }
        .status-warn { color: var(--accent-amber); }
    </style>
</head>
<body>

    <header>
        <h1>CONTROLE DE CAMADA LIMITE POR MAGNETOHIDRODINÂMICA (MHD)</h1>
        <div class="badges">
            <div class="badge">CAMPO HTS 3.5 T</div>
            <div class="badge badge-danger">HIPERSÔNICO MACH 8</div>
        </div>
    </header>

    <div class="main-container">
        <!-- 1. Controles Esquerdo -->
        <div class="control-panel">
            <div class="panel-section">
                <h3>SUBSISTEMAS MHD & IONIZAÇÃO</h3>
                <div class="btn-group">
                    <button id="btn-mhd" class="action-active" onclick="toggleMHDSystem()">Ativar Bobinas HTS & Ionização (MHD)</button>
                    <button onclick="injectSeedGas()">Injetar Gás Semente (Potássio/Césio)</button>
                    <button onclick="triggerPWMPulse()">Disparar Pulso PWM (18 MW / 20 kHz)</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>AJUSTE ELETROMECÂNICO (GIMBAL)</h3>
                <div class="btn-group">
                    <button onclick="adjustGimbalAngle()">Ajustar Ângulo Servo-Atuador (SMA)</button>
                    <button onclick="resetSimulation()">Resetar Sistema</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>PARÂMETROS ELÉTRICOS</h3>
                <div style="font-size: 0.68rem; line-height: 1.4; color: #94a3b8;">
                    • Campo Magnético ($B$): 3.5 Tesla<br>
                    • Tensão Eletrodos ($V$): 15 kV<br>
                    • Corrente de Pulso ($I$): 1.2 kA<br>
                    • Potência de Pico ($P$): 18 MW<br>
                    • Frequência PWM: 20 kHz<br>
                    • Criogenia HTS: 77 Kelvin (-196 °C)
                </div>
            </div>
        </div>

        <!-- 2. Centro: Visão Geral + Canvas -->
        <div class="center-container">
            <div class="app-overview">
                <strong>VISÃO GERAL DO APLICATIVO:</strong> Este aplicativo simula a mitigação de atrito e calor em veículos hipersônicos usando <strong>Magnetohidrodinâmica (MHD)</strong>. O ar ionizado (plasma) gerado pelo atrito a Mach 8 é defletido e desacelerado por campos magnéticos supercondutores (3.5 Tesla), gerando uma força de Lorentz que empurra a onda de choque para longe da estrutura física da ogiva, mantendo a temperatura nominal e arrasto zero.
            </div>

            <div class="viewport-container">
                <div class="viewport-header">
                    <span id="view-status">SIMULAÇÃO DE FLUXO HIPERSÔNICO [MHD ATIVO - FORÇA DE LORENTZ]</span>
                    <span id="canvas-fps">CANVAS 2D | 120 FPS</span>
                </div>
                <canvas id="simCanvas"></canvas>
            </div>
        </div>

        <!-- 3. Telemetria Direita -->
        <div class="telemetry-panel">
            <div class="panel-section">
                <h3>TELEMETRIA EM TEMPO REAL</h3>
            </div>
            <div class="metric-box">
                <div class="metric-title">ESTADO DO SISTEMA MHD</div>
                <div class="metric-value status-ok" id="stat-mhd">ATIVO (3.5 TESLA)</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">FORÇA DE LORENTZ ($\vec{F}$)</div>
                <div class="metric-value status-ok" id="stat-lorentz">4.8 kN (Contrapressão)</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">TEMPERATURA DA OGIVA</div>
                <div class="metric-value status-ok" id="stat-temp">320.4 °C (Controlada)</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">DENSIDADE DE ÍONS (LANGMUIR)</div>
                <div class="metric-value status-ok" id="stat-ions">2.4e19 m⁻³ (Semente Ativa)</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">CONSUMO DE POTÊNCIA MÉDIA</div>
                <div class="metric-value status-ok" id="stat-power">150 kW (Pulsado)</div>
            </div>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        let mhdActive = true;
        let seedGasActive = false;
        let gimbalAngle = 0;
        let plasmaParticles = [];

        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight - 30;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        function toggleMHDSystem() {
            mhdActive = !mhdActive;
            let btn = document.getElementById('btn-mhd');
            if (mhdActive) {
                btn.classList.add('action-active');
                btn.innerText = 'Ativar Bobinas HTS & Ionização (MHD)';
                document.getElementById('stat-mhd').innerText = 'ATIVO (3.5 TESLA)';
                document.getElementById('stat-mhd').className = 'metric-value status-ok';
                document.getElementById('stat-temp').innerText = '320.4 °C (Controlada)';
                document.getElementById('stat-temp').className = 'metric-value status-ok';
                document.getElementById('stat-lorentz').innerText = '4.8 kN (Contrapressão)';
            } else {
                btn.classList.remove('action-active');
                btn.innerText = 'Ativar Sistema MHD';
                document.getElementById('stat-mhd').innerText = 'DESLIGADO [ALERTA TÉRMICO]';
                document.getElementById('stat-mhd').className = 'metric-value status-alert';
                document.getElementById('stat-temp').innerText = '2850.0 °C [FUSÃO IMINENTE]';
                document.getElementById('stat-temp').className = 'metric-value status-alert';
                document.getElementById('stat-lorentz').innerText = '0.0 kN (Sem Deflexão)';
            }
        }

        function injectSeedGas() {
            seedGasActive = true;
            document.getElementById('stat-ions').innerText = '8.6e20 m⁻³ (Potássio/Césio)';
            setTimeout(() => { seedGasActive = false; }, 4000);
        }

        function triggerPWMPulse() {
            document.getElementById('stat-power').innerText = '18 MW (Pico 20 kHz)';
            setTimeout(() => {
                document.getElementById('stat-power').innerText = '150 kW (Pulsado)';
            }, 1500);
        }

        function adjustGimbalAngle() {
            gimbalAngle = (gimbalAngle + 2.5) % 10;
        }

        function resetSimulation() {
            mhdActive = true;
            seedGasActive = false;
            gimbalAngle = 0;
            plasmaParticles = [];
        }

        // Loop de Renderização Canvas 2D
        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Grid de fundo tático
            ctx.strokeStyle = 'rgba(0, 240, 255, 0.04)';
            ctx.lineWidth = 1;
            let gridSize = 40;
            for (let x = 0; x < canvas.width; x += gridSize) {
                ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
            }
            for (let y = 0; y < canvas.height; y += gridSize) {
                ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke();
            }

            let cx = 150;
            let cy = canvas.height / 2;

            // Gerar partículas de fluxo de ar hipersônico da esquerda para a direita
            if (Math.random() < 0.8) {
                plasmaParticles.push({
                    x: 10,
                    y: cy + (Math.random() - 0.5) * 260,
                    vx: 16 + Math.random() * 4,
                    vy: 0,
                    deflected: false
                });
            }

            // Atualizar e renderizar partículas
            for (let i = plasmaParticles.length - 1; i >= 0; i--) {
                let p = plasmaParticles[i];
                p.x += p.vx;

                // Interação com o campo magnético / ogiva (MHD)
                let distToTip = Math.sqrt((p.x - cx) ** 2 + (p.y - cy) ** 2);
                if (mhdActive && distToTip < 160 && !p.deflected) {
                    p.deflected = true;
                    // Força de Lorentz desvia a partícula para cima ou para baixo
                    p.vy = (p.y < cy ? -6 : 6) * (seedGasActive ? 1.5 : 1.0);
                    p.vx *= 0.6; // Desaceleração MHD
                }

                if (p.deflected) {
                    p.y += p.vy;
                }

                ctx.fillStyle = (mhdActive && p.deflected) ? '#38bdf8' : '#ff3333';
                ctx.beginPath();
                ctx.arc(p.x, p.y, 2.5, 0, Math.PI * 2);
                ctx.fill();

                if (p.x > canvas.width || p.y < 0 || p.y > canvas.height) {
                    plasmaParticles.splice(i, 1);
                }
            }

            // Desenhar Ogiva Hipersônica (Cone)
            ctx.save();
            ctx.translate(cx, cy);
            ctx.rotate(gimbalAngle * Math.PI / 180);

            ctx.fillStyle = mhdActive ? '#0f172a' : '#7f1d1d';
            ctx.strokeStyle = mhdActive ? '#00f0ff' : '#ff3333';
            ctx.lineWidth = 2.5;

            ctx.beginPath();
            ctx.moveTo(0, 0);
            ctx.lineTo(160, -75);
            ctx.lineTo(160, 75);
            ctx.closePath();
            ctx.fill();
            ctx.stroke();

            // Bobinas HTS & Eletrodos visuais no nariz
            ctx.fillStyle = '#00ff66';
            ctx.beginPath();
            ctx.arc(15, 0, 5, 0, Math.PI * 2);
            ctx.fill();

            ctx.fillStyle = '#fff';
            ctx.font = '10px Courier New';
            ctx.fillText('ELETRODOS & BOBINAS HTS (3.5T)', -20, -90);

            // Linhas de Campo Magnético se MHD Ativo
            if (mhdActive) {
                ctx.strokeStyle = 'rgba(0, 240, 255, 0.35)';
                ctx.lineWidth = 1.5;
                ctx.beginPath();
                ctx.arc(20, 0, 110, -Math.PI / 2, Math.PI / 2);
                ctx.stroke();
                ctx.beginPath();
                ctx.arc(20, 0, 150, -Math.PI / 2, Math.PI / 2);
                ctx.stroke();
            }

            ctx.restore();

            requestAnimationFrame(animate);
        }

        animate();
    </script>
</body>
</html>
