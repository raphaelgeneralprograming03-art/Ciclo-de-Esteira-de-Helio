<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel Tríade - Ciclo de Esteira de Hélio</title>
    <style>
        :root {
            --bg-color: #010103;
            --panel-bg: rgba(4, 4, 15, 0.94);
            --neon-blue: #00f3ff;
            --neon-green: #39ff14;
            --neon-purple: #bd00ff;
            --neon-red: #ff0055;
            --neon-amber: #ffaa00;
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
            background: linear-gradient(180deg, rgba(0,243,255,0.15) 0%, transparent 100%);
            border-bottom: 1px solid rgba(0, 243, 255, 0.3);
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            z-index: 30;
        }

        header h1 {
            font-size: 1.25rem;
            color: var(--neon-blue);
            text-shadow: 0 0 10px rgba(0, 243, 255, 0.5);
        }

        .system-status {
            font-size: 0.85rem;
            color: var(--neon-amber);
        }

        .main-container {
            display: flex;
            flex: 1;
            height: calc(100vh - 60px);
            position: relative;
        }

        /* Painel Lateral */
        .control-panel {
            width: 340px;
            background: var(--panel-bg);
            border-right: 1px solid rgba(0, 243, 255, 0.25);
            padding: 15px;
            display: flex;
            flex-direction: column;
            gap: 12px;
            overflow-y: auto;
            backdrop-filter: blur(15px);
            z-index: 30;
        }

        .section-title {
            font-size: 0.9rem;
            color: var(--neon-blue);
            border-bottom: 1px dashed rgba(0, 243, 255, 0.4);
            padding-bottom: 5px;
            margin-bottom: 5px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .danger-btn {
            background: rgba(255, 255, 255, 0.02);
            border: 1px solid rgba(0, 243, 255, 0.25);
            color: #b0b0d0;
            padding: 10px;
            text-align: left;
            cursor: pointer;
            border-radius: 4px;
            transition: all 0.2s ease;
            font-family: inherit;
            font-size: 0.75rem;
        }

        .danger-btn:hover {
            background: rgba(0, 243, 255, 0.08);
            border-color: var(--neon-blue);
        }

        .danger-btn.active {
            background: rgba(189, 0, 255, 0.15);
            border-color: var(--neon-purple);
            box-shadow: 0 0 12px rgba(189, 0, 255, 0.4);
            color: #fff;
            font-weight: bold;
        }

        .telemetry-box {
            background: rgba(0, 0, 0, 0.6);
            border: 1px solid rgba(0, 243, 255, 0.15);
            padding: 12px;
            border-radius: 4px;
            font-size: 0.72rem;
            line-height: 1.5;
        }

        .telemetry-row {
            display: flex;
            justify-content: space-between;
            margin-bottom: 4px;
        }

        .telemetry-value {
            color: var(--neon-blue);
            font-weight: bold;
        }

        /* Área do Espaço */
        .simulation-area {
            flex: 1;
            position: relative;
            background: #010105;
            height: 100%;
        }

        canvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: block;
            z-index: 10;
        }

        /* HUD Sobreposto */
        .hud-overlay {
            position: absolute;
            bottom: 15px;
            left: 15px;
            right: 15px;
            background: rgba(3, 3, 10, 0.94);
            border: 1px solid rgba(189, 0, 255, 0.35);
            padding: 12px;
            border-radius: 4px;
            pointer-events: none;
            backdrop-filter: blur(8px);
            z-index: 30;
        }

        .hud-title {
            color: var(--neon-purple);
            font-size: 0.9rem;
            margin-bottom: 4px;
            text-transform: uppercase;
        }

        .hud-desc {
            font-size: 0.75rem;
            color: #b5b5d5;
            line-height: 1.4;
        }

        .scale-badge {
            position: absolute;
            top: 15px;
            right: 15px;
            background: rgba(255, 0, 85, 0.15);
            border: 1px solid var(--neon-red);
            color: var(--neon-red);
            padding: 4px 8px;
            font-size: 0.65rem;
            border-radius: 3px;
            text-transform: uppercase;
            z-index: 30;
        }
    </style>
</head>
<body>

    <header>
        <h1>MATRIZ OPERACIONAL - ESTEIRA DE REUTILIZAÇÃO DE HÉLIO</h1>
        <div class="system-status">CONSERVAÇÃO DE HIDROGÊNIO (H): MÁXIMA (100%)</div>
    </header>

    <div class="main-container">
        <!-- Painel Lateral -->
        <div class="control-panel">
            <div class="section-title">Ameaças Espaciais</div>
            <button class="danger-btn active" onclick="switchDanger('supernova')">1. Supernova Cósmica</button>
            <button class="danger-btn" onclick="switchDanger('grb')">2. Surto de Raios Gama (GRB)</button>
            <button class="danger-btn" onclick="switchDanger('blackhole')">3. Buraco Negro Supermassivo</button>
            <button class="danger-btn" onclick="switchDanger('gravwave')">4. Onda Gravitacional Extrema</button>
            <button class="danger-btn" onclick="switchDanger('vacuum')">5. Decaimento do Vácuo Quântico</button>
            <button class="danger-btn" onclick="switchDanger('blueshift')">6. Radiação com Desvio Azul</button>

            <div class="section-title" style="margin-top: 5px;">Métrica de Reciclagem</div>
            <div class="telemetry-box">
                <div class="telemetry-row"><span>Status da Esteira:</span><span class="telemetry-value" style="color:var(--neon-green)">RECAPTURA ATIVA</span></div>
                <div class="telemetry-row"><span>Eficiência do He:</span><span class="telemetry-value">99.84% (Retorno)</span></div>
                <div class="telemetry-row"><span>H Reservado:</span><span class="telemetry-value" style="color:var(--neon-green)">PRESERVADO</span></div>
                <div class="telemetry-row"><span>Giro da Tríade:</span><span class="telemetry-value">0.9999991 c</span></div>
                <div class="telemetry-row"><span>Fator Lorentz:</span><span class="telemetry-value">745.35x</span></div>
                <div class="telemetry-row"><span>Matéria Negativa:</span><span id="tel-neg" class="telemetry-value">--</span></div>
                <div class="telemetry-row"><span>Matéria Escura:</span><span id="tel-dark" class="telemetry-value">--</span></div>
            </div>
        </div>

        <!-- Área Espacial -->
        <div class="simulation-area">
            <div id="scale-indicator" class="scale-badge">Carregando Métrica...</div>
            <canvas id="simCanvas"></canvas>
            
            <div class="hud-overlay">
                <div id="hud-name" class="hud-title">Carregando...</div>
                <div id="hud-text" class="hud-desc">Iniciando traqueamento da esteira cinemática de combustível...</div>
            </div>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        // Inicialização forçada e segura do tamanho do Canvas
        function initCanvasSize() {
            canvas.width = canvas.parentElement.clientWidth || 800;
            canvas.height = canvas.parentElement.clientHeight || 600;
        }
        initCanvasSize();
        window.addEventListener('resize', initCanvasSize);

        let currentDanger = 'supernova';
        let time = 0;

        // Campo de estrelas tridimensional
        const stars = [];
        for(let i = 0; i < 130; i++) {
            stars.push({ x: Math.random(), y: Math.random(), size: Math.random() * 1.6 + 0.4 });
        }

        // Dados textuais do painel
        const dangerData = {
            supernova: {
                name: "1. Supernova Cósmica (Magnitude: ExaJoules Estelares)",
                text: "A onda de choque colide contra o escudo. Enquanto a Tríade desvia a radiação via Lente Gravitacional de Matéria Escura, a frota realiza a rota de retorno na mesma trajetória, capturando o Hélio suspenso na esteira magnética e economizando todo o Hidrogênio estrutural.",
                scale: "Magnitude: ExaJoules", neg: "4.5 x 10^22 kg/s", dark: "9.8 x 10^26 kg/s"
            },
            grb: {
                name: "2. Surto de Raios Gama - GRB (Magnitude: Feixe Relativístico)",
                text: "O feixe linear de alta frequência é refratado pelos campos de Matéria Negativa gerados pelas naves A e B. Os propulsores MHD mantêm o trilho de reciclagem cinético de Hélio estável mesmo sob o estresse energético do surto gama.",
                scale: "Magnitude: YottaWatts", neg: "8.9 x 10^24 kg/s", dark: "1.2 x 10^22 kg/s"
            },
            blackhole: {
                name: "3. Buraco Negro Supermassivo (Magnitude: Bilhões de Massas Solares)",
