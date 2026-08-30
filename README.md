<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador Avançado - Tríade de Estabilização Cósmica</title>
    <style>
        :root {
            --bg-color: #05050a;
            --panel-bg: rgba(10, 10, 25, 0.85);
            --neon-blue: #00f3ff;
            --neon-green: #39ff14;
            --neon-purple: #bd00ff;
            --neon-red: #ff0055;
            --text-main: #e0e0ff;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            font-family: 'Courier New', Courier, monospace;
            overflow: hidden;
            height: 100vh;
            display: flex;
            flex-direction: column;
        }

        header {
            background: linear-gradient(180deg, rgba(0,243,255,0.1) 0%, transparent 100%);
            border-bottom: 1px solid rgba(0, 243, 255, 0.3);
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        header h1 {
            font-size: 1.4rem;
            color: var(--neon-blue);
            text-shadow: 0 0 10px rgba(0, 243, 255, 0.5);
        }

        .system-status {
            font-size: 0.9rem;
            color: var(--neon-green);
        }

        .main-container {
            display: flex;
            flex: 1;
            height: calc(100vh - 60px);
            overflow: hidden;
        }

        /* Painel Lateral de Controle */
        .control-panel {
            width: 320px;
            background: var(--panel-bg);
            border-right: 1px solid rgba(0, 243, 255, 0.2);
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 15px;
            overflow-y: auto;
            backdrop-filter: blur(10px);
        }

        .section-title {
            font-size: 1rem;
            color: var(--neon-blue);
            border-bottom: 1px dashed rgba(0, 243, 255, 0.4);
            padding-bottom: 5px;
            margin-bottom: 10px;
            text-transform: uppercase;
        }

        .danger-btn {
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(0, 243, 255, 0.3);
            color: var(--text-main);
            padding: 12px;
            text-align: left;
            cursor: pointer;
            border-radius: 4px;
            transition: all 0.3s ease;
            font-family: inherit;
        }

        .danger-btn:hover {
            background: rgba(0, 243, 255, 0.1);
            border-color: var(--neon-blue);
            box-shadow: 0 0 10px rgba(0, 243, 255, 0.2);
        }

        .danger-btn.active {
            background: rgba(189, 0, 255, 0.15);
            border-color: var(--neon-purple);
            box-shadow: 0 0 15px rgba(189, 0, 255, 0.4);
            color: #fff;
        }

        /* Painel de Telemetria */
        .telemetry-box {
            background: rgba(0, 0, 0, 0.4);
            border: 1px solid rgba(0, 243, 255, 0.15);
            padding: 12px;
            border-radius: 4px;
            font-size: 0.8rem;
            line-height: 1.4;
        }

        .telemetry-row {
            display: flex;
            justify-content: space-between;
            margin-bottom: 6px;
        }

        .telemetry-value {
            color: var(--neon-blue);
            font-weight: bold;
        }

        /* Área Central do Canvas */
        .simulation-area {
            flex: 1;
            position: relative;
            background: radial-gradient(circle at center, #0c0c1e 0%, #020205 100%);
        }

        canvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: block;
        }

        /* HUD Sobreposto ao Canvas */
        .hud-overlay {
            position: absolute;
            bottom: 20px;
            left: 20px;
            right: 20px;
            background: rgba(5, 5, 10, 0.85);
            border: 1px solid rgba(189, 0, 255, 0.3);
            padding: 15px;
            border-radius: 6px;
            pointer-events: none;
            backdrop-filter: blur(5px);
        }

        .hud-title {
            color: var(--neon-purple);
            font-size: 1rem;
            margin-bottom: 5px;
            text-transform: uppercase;
        }

        .hud-desc {
            font-size: 0.85rem;
            color: #b0b0d0;
            line-height: 1.5;
        }

        .scale-badge {
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(255, 0, 85, 0.1);
            border: 1px solid var(--neon-red);
            color: var(--neon-red);
            padding: 5px 10px;
            font-size: 0.75rem;
            border-radius: 3px;
            text-transform: uppercase;
            letter-spacing: 1px;
            box-shadow: 0 0 8px rgba(255, 0, 85, 0.3);
        }
    </style>
</head>
<body>

    <header>
        <h1>SISTEMA DE DEFESA ANALÍTICO DA TRÍADE (S.D.A.T.)</h1>
        <div class="system-status">SISTEMAS NAVE A/B/CENTRAL: OPERACIONAIS (99.99991% c)</div>
    </header>

    <div class="main-container">
        <!-- Controle Lateral -->
        <div class="control-panel">
            <div class="section-title">Selecionar Fenômeno</div>
            <button class="danger-btn active" onclick="switchDanger('supernova')">1. Supernova Cósmica</button>
            <button class="danger-btn" onclick="switchDanger('grb')">2. Surto de Raios Gama (GRB)</button>
            <button class="danger-btn" onclick="switchDanger('blackhole')">3. Buraco Negro Supermassivo</button>
            <button class="danger-btn" onclick="switchDanger('gravwave')">4. Onda Gravitacional Extrema</button>
            <button class="danger-btn" onclick="switchDanger('vacuum')">5. Decaimento do Vácuo Quântico</button>
            <button class="danger-btn" onclick="switchDanger('blueshift')">6. Radiação com Desvio Azul</button>

            <div class="section-title" style="margin-top: 15px;">Telemetria Exótica</div>
            <div class="telemetry-box">
                <div class="telemetry-row"><span>Velocidade Angular:</span><span class="telemetry-value">0.9999991 c</span></div>
                <div class="telemetry-row"><span>Fator Lorentz (γ):</span><span class="telemetry-value">745.35x</span></div>
                <div class="telemetry-row"><span>Matéria Negativa:</span><span id="tel-neg" class="telemetry-value">0.00 kg/s</span></div>
                <div class="telemetry-row"><span>Matéria Escura:</span><span id="tel-dark" class="telemetry-value">0.00 kg/s</span></div>
                <div class="telemetry-row"><span>Integridade Estrutural:</span><span id="tel-integ" class="telemetry-value" style="color:var(--neon-green)">100%</span></div>
                <div class="telemetry-row"><span>Proteção Planetária:</span><span id="tel-shield" class="telemetry-value" style="color:var(--neon-green)">ABSOLUTA</span></div>
            </div>
        </div>

        <!-- Área de Simulação Visual -->
        <div class="simulation-area">
            <div id="scale-indicator" class="scale-badge">Magnitude: ExaJoules</div>
            <canvas id="simCanvas"></canvas>
            
            <!-- HUD Explicativo Dinâmico -->
            <div class="hud-overlay">
                <div id="hud-name" class="hud-title">Supernova Cósmica</div>
                <div id="hud-text" class="hud-desc">Carregando dados táticos do escudo quântico...</div>
            </div>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        // Redimensionar canvas dinamicamente
        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Estado Global da Simulação
        let currentDanger = 'supernova';
        let time = 0;

        // Banco de Dados Táticos dos Perigos
        const dangerData = {
            supernova: {
                name: "1. Supernova Cósmica (Magnitude: ExaJoules Estelares)",
                text: "A onda de choque e radiação da Supernova colidem contra a barreira. A Tríade injeta Matéria Escura massiva na periferia do horizonte de eventos do portal, criando um poço gravitacional invisível que deforma o espaço. A explosão é forçada a realizar uma Lente Gravitacional Total, contornando a Terra e os planetas aliados com deflexão perfeita.",
                scale: "Magnitude: ExaJoules",
                neg: "4.5 x 10^22 kg/s",
                dark: "9.8 x 10^26 kg/s"
            },
            grb: {
                name: "2. Surto de Raios Gama - GRB (Magnitude: Feixe Relativístico)",
                text: "O feixe de energia pura colide em linha reta. A Nave A e a Nave B aceleram seu giro gerando densidade crítica de Matéria Negativa. Isso projeta uma 'colina gravitacional' de curvatura invertida (antigravidade). O feixe de fótons energéticos sofre refração espacial absoluta, espalhando a radiação fatal para o vácuo morto, blindando constelações inteiras.",
                scale: "Magnitude: YottaWatts",
                neg: "8.9 x 10^24 kg/s",
                dark: "1.2 x 10^22 kg/s"
            },
            blackhole: {
                name: "3. Buraco Negro Supermassivo (Magnitude: Bilhões de Massas Solares)",
