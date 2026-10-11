
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Centro de Comando Unificado: PINN Neural & Defesa Tática (SMTA & CAMPIS)</title>
    <style>
        :root {
            --bg-color: #020617;
            --panel-bg: rgba(8, 18, 35, 0.92);
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
            padding: 8px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        header h1 {
            font-size: 1rem;
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
            gap: 10px;
            padding: 10px;
            overflow: hidden;
        }

        /* Painel de Controle Esquerdo (Abas / Seções) */
        .control-panel {
            width: 330px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 10px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            overflow-y: auto;
            box-shadow: 0 0 15px rgba(0, 240, 255, 0.1);
        }

        .panel-section h3 {
            font-size: 0.75rem;
            color: var(--border-color);
            margin-bottom: 5px;
            border-bottom: 1px solid rgba(0, 240, 255, 0.3);
            padding-bottom: 2px;
            letter-spacing: 1px;
        }

        .btn-group {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }

        button {
            background: rgba(0, 240, 255, 0.1);
            border: 1px solid var(--border-color);
            color: var(--text-main);
            padding: 7px 10px;
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

        button.action-danger {
            border-color: var(--accent-red);
            background: rgba(255, 51, 51, 0.15);
            color: #ff9999;
        }
        button.action-danger:hover {
            background: rgba(255, 51, 51, 0.35);
        }

        button.action-pinn {
            border-color: var(--accent-green);
            background: rgba(0, 255, 102, 0.15);
            color: #99ffbb;
        }
        button.action-pinn:hover {
            background: rgba(0, 255, 102, 0.35);
        }

        /* Área Central (Canvas + Logs PINN) */
        .center-container {
            flex: 1;
            display: flex;
            flex-direction: column;
            gap: 10px;
            overflow: hidden;
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

        /* Painel de Log da PINN & Sistema */
        .log-panel {
            flex: 0.8;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 8px;
            display: flex;
            flex-direction: column;
            gap: 4px;
            font-size: 0.68rem;
            overflow-y: auto;
        }

        .log-title {
            color: var(--accent-green);
            font-weight: bold;
            border-bottom: 1px solid rgba(0, 255, 102, 0.3);
            padding-bottom: 2px;
        }

        .log-content {
            color: #a5d8ff;
            line-height: 1.35;
        }

        /* Painel de Telemetria Direito */
        .telemetry-panel {
            width: 310px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 10px;
            display: flex;
            flex-direction: column;
            gap: 8px;
            overflow-y: auto;
        }

        .metric-box {
            background: rgba(2, 12, 38, 0.7);
            border: 1px solid rgba(0, 240, 255, 0.3);
            border-radius: 4px;
            padding: 6px 8px;
        }

        .metric-title {
            font-size: 0.62rem;
            color: var(--border-color);
            margin-bottom: 2px;
        }

        .metric-value {
            font-size: 0.9rem;
            font-weight: bold;
            color: #fff;
        }

        .status-ok { color: var(--accent-green); }
        .status-alert { color: var(--accent-red); }
        .status-warn { color: var(--accent-amber); }

        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.4; }
            100% { opacity: 1; }
        }
    </style>
</head>
<body>

    <header>
        <h1>CENTRO DE COMANDO UNIFICADO: PINN & DEFESA TÁTICA (v5.0)</h1>
        <div class="badges">
            <div class="badge">SMTA CONE 600x350mm</div>
            <div class="badge">CAMPIS v4.2.6</div>
            <div class="badge badge-danger">SIGILO DE DEFESA NACIONAL</div>
        </div>
    </header>

    <div class="main-container">
        <!-- 1. Painel de Controle Esquerdo -->
        <div class="control-panel">
            <div class="panel-section">
                <h3>1. MOTOR NEURAL PINN (EQUAÇÃO DO CALOR)</h3>
                <div class="btn-group">
                    <button class="action-pinn" onclick="trainPINNStep()">Treinar Épocas PINN (PyTorch Engine)</button>
                    <button onclick="togglePINNMode()">Alternar Visualização Térmica PDE</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>2. TESTE HIPERSÔNICO (SMTA - ARC JET)</h3>
                <div class="btn-group">
                    <button class="action-danger" onclick="startArcJetTest()">Acionar Plasma Mach 8 (2.100°C)</button>
                    <button onclick="runXRayScan()">Varredura Preventiva Raio-X</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>3. EFEITO DE MEMÓRIA DE FORMA (NITINOL)</h3>
                <div class="btn-group">
                    <button onclick="triggerNitinolImpact()">Simular Impacto Mola (Martensita)</button>
                    <button style="color: #fed7aa;" onclick="startJouleHeating()">Injetar Corrente Joule (500°C)</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>4. KROPYVA-LINK & BIOSSEGURANÇA MESH</h3>
                <div class="btn-group">
                    <button onclick="toggleEWJamming()">Ativar Interferência GPS (EW)</button>
                    <button onclick="fireCounterBattery()">Disparar Fogo Sensor-to-Shooter</button>
                    <button onclick="toggleBiosafetyCrisis()">Simular Surto Bioaerossol (CHONPS)</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>5. SISTEMA CAMPIS (MASSAS HÍBRIDAS)</h3>
                <div class="btn-group">
                    <button class="action-danger" onclick="triggerCampisImpact()">Simular Impacto Hiperveloz (Nave 1/2)</button>
                    <button onclick="resetAllSystems()">Resetar Subsistemas</button>
                </div>
            </div>
        </div>

        <!-- 2. Centro: Canvas de Renderização & Logs PINN -->
        <div class="center-container">
            <div class="viewport-container">
                <div class="viewport-header">
                    <span id="view-status">VISUALIZAÇÃO TÁTICA MULTICAMADA & MAPA TÉRMICO PINN</span>
                    <span id="canvas-fps">120 FPS [FPGA XILINX ACTIVE]</span>
                </div>
                <canvas id="simCanvas"></canvas>
            </div>

            <div class="log-panel">
                <div class="log-title">[ LOG DE EXECUÇÃO DA PINN (ThermalPINN) & TELEMETRIA DO SISTEMA ]</div>
                <div class="log-content" id="unified-log-text">
                    [00:00:01] ThermalPINN inicializado (Arquitetura MLP 2x40x40x40x1).<br>
                    [00:00:02] Restrição física acoplada: dT/dt - alpha * d²T/dx² = 0 (alpha = 0.000023 m²/s C/SiC).<br>
                    [00:00:03] Sistema SMTA, C.A.M.P.I.S., Kropyva-Link e Biossegurança em operação síncrona.
                </div>
            </div>
        </div>

        <!-- 3. Painel de Telemetria Direito -->
        <div class="telemetry-panel">
            <div class="panel-section">
                <h3>ESTATÍSTICAS DA PINN</h3>
            </div>
            <div class="metric-box">
                <div class="metric-title">ÉPOCAS DE TREINAMENTO</div>
                <div class="metric-value status-ok" id="pinn-epochs">0 Épocas</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">PERDA TOTAL (LOSS DATA + PHYSICS)</div>
                <div class="metric-value status-ok" id="pinn-loss">0.000142</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">RESIDUAL DA EQUAÇÃO DO CALOR (PDE)</div>
                <div class="metric-value status-ok" id="pinn-pde">0.000018</div>
            </div>

            <div class="panel-section" style="margin-top: 5px;">
                <h3>TELEMETRIA FÍSICA & TÁTICA</h3>
            </div>
            <div class="metric-box">
                <div class="metric-title">NARIZ SMTA (TERMOCOPAR S)</div>
                <div class="metric-value status-ok" id="stat-smta-temp">24.5 °C</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">MOLA NITINOL (COMPRESSÃO / FASE)</div>
                <div class="metric-value" id="stat-nitinol">12% [AUSTENITA]</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">FLUXO MAGNÉTICO CAMPIS (ÍMÃS)</div>
                <div class="metric-value status-ok" id="stat-campis-mag">1.4 Tesla (Estável)</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">LINK KROPYVA / STARLINK</div>
                <div class="metric-value status-ok" id="stat-kropyva">0.38 ms [ZERO-LATENCY]</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">EXCLUSÕES CHONPS (BIOSSEGURANÇA)</div>
                <div class="metric-value status-ok" id="stat-chonps">0 inértes excluídos</div>
            </div>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        // Estado Unificado do Sistema
        let pinnEpochs = 0;
        let pinnLoss = 0.00124;
        let pinnPDE = 0.00015;
        let showPINNHeatmap = false;

        let smtaActive = false;
        let smtaTemp = 24.5;
        
        let nitinolState = 'NORMAL'; // NORMAL, CRUSHED, HEATING, RESTORING
        let nitinolTemp = 25.0;
        let nitinolCrush = 0;

        let campisActive = false;
        let campisVibe = 0.02;
        let campisOffset = 0;

        let ewActive = false;
        let bioCrisis = false;
        let chonpsCount = 0;

        let particles = [];

        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight - 30;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        function logUnified(msg) {
            let logDiv = document.getElementById('unified-log-text');
            let timeStr = new Date().toTimeString().split(' ')[0];
            logDiv.innerHTML += `<br>[${timeStr}] ${msg}`;
            logDiv.scrollTop = logDiv.scrollHeight;
        }

        // Simulação da PINN (Physics-Informed Neural Network em JS espelhando o código Python)
        function trainPINNStep() {
            pinnEpochs += 50;
            pinnLoss = Math.max(0.00001, pinnLoss * 0.92);
            pinnPDE = Math.max(0.000001, pinnPDE * 0.89);

            document.getElementById('pinn-epochs').innerText = `${pinnEpochs} Épocas`;
            document.getElementById('pinn-loss').innerText = pinnLoss.toFixed(6);
            document.getElementById('pinn-pde').innerText = pinnPDE.toFixed(6);

            logUnified(`🧠 ThermalPINN iterada por +50 épocas. Loss Data: ${(pinnLoss*0.8).toFixed(6)} | Loss Physics (PDE residual): ${pinnPDE.toFixed(6)}.`);
        }

        function togglePINNMode() {
            showPINNHeatmap = !showPINNHeatmap;
            logUnified(`📊 Modo de visualização térmica PINN alterado para: ${showPINNHeatmap ? 'Gradiente PDE Ativo' : 'Estrutura Padrão'}`);
        }

        // Funções de Acionamento dos Cenários
        function startArcJetTest() {
            smtaActive = true;
            smtaTemp = 2120.0;
            document.getElementById('stat-smta-temp').innerText = '2120.0 °C';
            document.getElementById('stat-smta-tempclassName = metric-value status-alert';
            document.getElementById('stat-smta-temp').className = 'metric-value status-alert';
            logUnified("🔥 Teste Arc Jet Plasma Mach 8 acionado no Cone SMTA. Nariz a 2.120°C (Isolamento Aerogel mantendo núcleo interno nominal).");
        }

        function runXRayScan() {
            logUnified("🔍 Varredura preventiva por difração de Raio-X concluída. 0 micro-fissuras no Nitinol e na casca C/SiC.");
        }

        function triggerNitinolImpact() {
            nitinolState = 'CRUSHED';
            nitinolCrush = 1;
            document.getElementById('stat-nitinol').innerText = '68% [MARTENSITA DEFORMADA]';
            document.getElementById('stat-nitinol').className = 'metric-value status-alert';
            logUnified("💥 Impacto hiperveloz detectado na mola de Nitinol. Deformação plástica na fase Martensita fria.");
        }

        function startJouleHeating() {
            if(nitinolState === 'CRUSHED') {
                nitinolState = 'HEATING';
                nitinolTemp = 500.0;
                document.getElementById('stat-nitinol').innerText = '500°C [AUSTENITA - EF. JOULE]';
                document.getElementById('stat-nitinol').className = 'metric-value status-warn';
                logUnified("⚡ IA fechou circuito elétrico (Efeito Joule). Nitinol aquecido a 500°C para transição de fase Austenita.");
                
                setTimeout(() => {
                    nitinolState = 'NORMAL';
                    nitinolCrush = 0;
                    nitinolTemp = 25.0;
                    document.getElementById('stat-nitinol').innerText = '12% [AUSTENITA - ORIGINAL]';
                    document.getElementById('stat-nitinol').className = 'metric-value status-ok';
                    logUnified("✨ Memória de forma acionada: Mola desamassada perfeitamente sozinha e resfriada.");
                }, 3000);
            } else {
                logUnified("ℹ️ Mola de Nitinol já está em operação normal.");
            }
        }

        function toggleEWJamming() {
            ewActive = !ewActive;
            logUnified(`⚠️ Interferência EW (Spoofing GPS) ${ewActive ? 'ATIVADA' : 'DESLIGADA'}. Navegação Óptica assumiu o controle.`);
            document.getElementById('stat-kropyva').innerText = ewActive ? '0.42 ms [ÓPTICO ANTI-EW]' : '0.38 ms [ZERO-LATENCY]';
        }

        function fireCounterBattery() {
            logUnified("🎯 Fogo de contrabateria disparado instantaneamente via overlay do Kropyva-Link.");
        }

        function toggleBiosafetyCrisis() {
            bioCrisis = !bioCrisis;
            if(bioCrisis) {
                chonpsCount += 142;
                document.getElementById('stat-chonps').innerText = `${chonpsCount} inértes filtrados`;
                logUnified("🦠 Surto de bioaerossol injetado. Núcleo CHONPS excluiu instantaneamente materiais inértes.");
            } else {
                logUnified("🟢 Matriz de Biossegurança estabilizada.");
            }
        }

        function triggerCampisImpact() {
            campisActive = true;
            campisOffset = -50;
            campisVibe = 1.85;
            document.getElementById('stat-campis-mag').innerText = '2.8 Tesla (Damping Ativo)';
            document.getElementById('stat-campis-mag').className = 'metric-value status-alert';
            logUnified("💥 C.A.M.P.I.S.: Impacto absorvido pelo escudo de sacrifício (Nave 2) via campo magnético e molas.");

            setTimeout(() => {
                campisActive = false;
                campisOffset = 0;
                campisVibe = 0.02;
                document.getElementById('stat-campis-mag').innerText = '1.4 Tesla (Estável)';
                document.getElementById('stat-campis-mag').className = 'metric-value status-ok';
                logUnified("🛡️ C.A.M.P.I.S.: Sistema estabilizado com sucesso. Zero deformação permanente.");
            }, 3500);
        }

        function resetAllSystems() {
            smtaActive = false;
            smtaTemp = 24.5;
            nitinolState = 'NORMAL';
            nitinolTemp = 25.0;
            nitinolCrush = 0;
            campisActive = false;
            campisOffset = 0;
            campisVibe = 0.02;
            ewActive = false;
            bioCrisis = false;
            document.getElementById('stat-smta-temp').innerText = '24.5 °C';
            document.getElementById('stat-smta-temp').className = 'metric-value status-ok';
            document.getElementById('stat-nitinol').innerText = '12% [AUSTENITA]';
            document.getElementById('stat-nitinol').className = 'metric-value status-ok';
            document.getElementById('stat-campis-mag').innerText = '1.4 Tesla (Estável)';
            document.getElementById('stat-campis-mag').className = 'metric-value status-ok';
            logUnified("🔄 Todos os subsistemas unificados foram reinicializados.");
        }

        // Loop de Renderização Canvas 2D Unificado
        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Grid cibernético de fundo
            ctx.strokeStyle = 'rgba(0, 240, 255, 0.04)';
            ctx.lineWidth = 1;
            let gridSize = 40;
            for (let x = 0; x < canvas.width; x += gridSize) {
                ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
            }
            for (let y = 0; y < canvas.height; y += gridSize) {
                ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke();
            }

            let cx = canvas.width / 2;
            let cy = canvas.height / 2;

            // --- 1. Renderizar Cone SMTA (Esquerda) ---
            ctx.save();
            ctx.translate(cx - 220, cy);

            // Casca Externa C/SiC
            ctx.fillStyle = smtaActive ? '#ef4444' : '#1e293b';
            ctx.strokeStyle = '#00f0ff';
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.moveTo(0, 0);
            ctx.quadraticCurveTo(50, -25, 180, -70);
            ctx.lineTo(180, 70);
            ctx.quadraticCurveTo(50, 25, 0, 0);
            ctx.closePath();
            ctx.fill();
            ctx.stroke();

            // Se gradiente PINN ativo, pintar calor PDE
            if (showPINNHeatmap) {
                let grad = ctx.createLinearGradient(0, 0, 180, 0);
                grad.addColorStop(0, 'rgba(255, 51, 51, 0.7)');
                grad.addColorStop(0.5, 'rgba(245, 158, 11, 0.5)');
                grad.addColorStop(1, 'rgba(0, 255, 102, 0.2)');
                ctx.fillStyle = grad;
                ctx.fill();
            }

            ctx.fillStyle = '#fff';
            ctx.font = '10px Courier New';
            ctx.fillText('SMTA CONE (600x350mm)', 10, -85);

            ctx.restore();

            // --- 2. Renderizar Sistema C.A.M.P.I.S. (Direita) ---
            ctx.save();
            campisOffset += (0 - campisOffset) * 0.08;
            let cX = cx + 120;
            let cY = cy;

            // Nave 1
            ctx.fillStyle = '#0f172a';
            ctx.strokeStyle = '#38bdf8';
            ctx.lineWidth = 2;
            ctx.fillRect(cX - 80, cY - 45, 100, 90);
            ctx.strokeRect(cX - 80, cY - 45, 100, 90);

            // Nave 2 (Escudo com offset de impacto)
            let n2X = cX + 60 + campisOffset;
            ctx.fillStyle = campisActive ? 'rgba(255,51,51,0.3)' : '#1e293b';
            ctx.strokeStyle = campisActive ? '#ff3333' : '#f59e0b';
            ctx.fillRect(n2X, cY - 55, 50, 110);
            ctx.strokeRect(n2X, cY - 55, 50, 110);

            // Molas de Nitinol conectando Nave 1 e Nave 2
            ctx.strokeStyle = nitinolState === 'HEATING' ? '#f59e0b' : '#00ff66';
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.moveTo(cX + 20, cY - 20);
            ctx.lineTo(n2X, cY - 20);
            ctx.moveTo(cX + 20, cY + 20);
            ctx.lineTo(n2X, cY + 20);
            ctx.stroke();

            ctx.fillStyle = '#c0d8ef';
            ctx.font = '9px Courier New';
            ctx.fillText('C.A.M.P.I.S. [MASSAS]', cX - 70, cY - 60);

            ctx.restore();

            requestAnimationFrame(animate);
        }

        animate();
    </script>
</body>
</html>
