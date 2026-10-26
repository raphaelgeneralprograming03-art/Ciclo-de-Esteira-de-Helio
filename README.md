
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

        // Dados textuais e métricas completas das 6 ameaças
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
                text: "A deformação extrema do espaço-tempo puxa a esteira. A Tríade compensa a atração acelerando a recirculação cinemática de Hélio, mantendo o vetor de empuxo limpo e imune ao horizonte de eventos.",
                scale: "Magnitude: Singularidade", neg: "1.4 x 10^26 kg/s", dark: "5.5 x 10^27 kg/s"
            },
            gravwave: {
                name: "4. Onda Gravitacional Extrema (Magnitude: Flutuação de Métrica)",
                text: "Distorções ondulares comprimem o espaço. O canal magnético de recaptura de Hélio adapta sua frequência ressonante para absorver os pulsos e manter a rota das naves estável.",
                scale: "Magnitude: Ripple G", neg: "3.2 x 10^23 kg/s", dark: "7.1 x 10^24 kg/s"
            },
            vacuum: {
                name: "5. Decaimento do Vácuo Quântico (Magnitude: Bolha de Falso Vácuo)",
                text: "A barreira de fase quântica é repelida pelo invólucro de Matéria Negativa. O Hélio recircula no ciclo fechado garantindo força motriz sem contaminação do vácuo instável.",
                scale: "Magnitude: Transição de Fase", neg: "9.1 x 10^25 kg/s", dark: "3.3 x 10^26 kg/s"
            },
            blueshift: {
                name: "6. Radiação com Desvio Azul (Magnitude: Frequência Ultravioleta Extrema)",
                text: "A compressão Doppler da luz de alta energia é defletida pela geometria da Tríade, preservando a linha de transferência do combustível com 99.84% de retenção de massa.",
                scale: "Magnitude: Doppler Z-High", neg: "6.7 x 10^22 kg/s", dark: "8.4 x 10^25 kg/s"
            }
        };

        function switchDanger(key) {
            currentDanger = key;
            document.querySelectorAll('.danger-btn').forEach(btn => btn.classList.remove('active'));
            
            const btnKeys = ['supernova', 'grb', 'blackhole', 'gravwave', 'vacuum', 'blueshift'];
            const btnIndex = btnKeys.indexOf(key);
            if (btnIndex !== -1) {
                document.querySelectorAll('.danger-btn')[btnIndex].classList.add('active');
            }

            updateInfo();
        }

        function updateInfo() {
            const data = dangerData[currentDanger];
            if (!data) return;
            document.getElementById('hud-name').innerText = data.name;
            document.getElementById('hud-text').innerText = data.text;
            document.getElementById('scale-indicator').innerText = data.scale;
            document.getElementById('tel-neg').innerText = data.neg;
            document.getElementById('tel-dark').innerText = data.dark;
        }

        // Loop de Renderização e Animação do Canvas
        function render() {
            time += 0.02;
            const w = canvas.width;
            const h = canvas.height;
            const cx = w / 2;
            const cy = h / 2;

            ctx.fillStyle = '#010105';
            ctx.fillRect(0, 0, w, h);

            // Renderizar campo de estrelas
            ctx.fillStyle = '#ffffff';
            stars.forEach(s => {
                ctx.beginPath();
                ctx.arc(s.x * w, s.y * h, s.size, 0, Math.PI * 2);
                ctx.fill();
            });

            // 1. Desenhar Animação Visual Específica da Ameaça Ativa
            if (currentDanger === 'supernova') {
                const radius = (time * 40) % (Math.min(w, h) * 0.45);
                ctx.beginPath();
                ctx.arc(cx, cy, radius + 10, 0, Math.PI * 2);
                ctx.strokeStyle = `rgba(255, 0, 85, ${1 - radius / (Math.min(w, h) * 0.45)})`;
                ctx.lineWidth = 4;
                ctx.stroke();

                ctx.beginPath();
                ctx.arc(cx, cy, radius * 0.5, 0, Math.PI * 2);
                ctx.fillStyle = `rgba(255, 170, 0, ${0.3 * (1 - radius / (Math.min(w, h) * 0.45))})`;
                ctx.fill();
            } else if (currentDanger === 'grb') {
                const beamY = cy + Math.sin(time) * 30;
                ctx.fillStyle = 'rgba(0, 243, 255, 0.25)';
                ctx.fillRect(0, beamY - 15, w, 30);
                ctx.fillStyle = 'rgba(255, 255, 255, 0.8)';
                ctx.fillRect(0, beamY - 3, w, 6);
            } else if (currentDanger === 'blackhole') {
                ctx.beginPath();
                ctx.arc(cx, cy, 45, 0, Math.PI * 2);
                ctx.fillStyle = '#000000';
                ctx.fill();
                ctx.strokeStyle = '#bd00ff';
                ctx.lineWidth = 3;
                ctx.stroke();

                ctx.beginPath();
                ctx.ellipse(cx, cy, 80, 25, time, 0, Math.PI * 2);
                ctx.strokeStyle = 'rgba(255, 170, 0, 0.6)';
                ctx.stroke();
            } else if (currentDanger === 'gravwave') {
                for (let r = 20; r < w; r += 40) {
                    const waveR = (r + time * 30) % w;
                    ctx.beginPath();
                    ctx.arc(cx, cy, waveR, 0, Math.PI * 2);
                    ctx.strokeStyle = `rgba(0, 243, 255, ${0.15 * (1 - waveR / w)})`;
                    ctx.lineWidth = 2;
                    ctx.stroke();
                }
            } else if (currentDanger === 'vacuum') {
                const vacR = 60 + Math.sin(time * 2) * 20;
                ctx.beginPath();
                ctx.arc(cx, cy, vacR, 0, Math.PI * 2);
                ctx.fillStyle = 'rgba(189, 0, 255, 0.15)';
                ctx.fill();
                ctx.strokeStyle = '#bd00ff';
                ctx.setLineDash([5, 5]);
                ctx.stroke();
                ctx.setLineDash([]);
            } else if (currentDanger === 'blueshift') {
                const grad = ctx.createRadialGradient(cx, cy, 10, cx, cy, w * 0.4);
                grad.addColorStop(0, 'rgba(0, 243, 255, 0.4)');
                grad.addColorStop(1, 'transparent');
                ctx.fillStyle = grad;
                ctx.fillRect(0, 0, w, h);
            }

            // 2. Renderizar a Tríade de Naves e a Esteira de Reciclagem de Hélio
            const radiusTriad = 120;
            const shipA = { x: cx + Math.cos(time) * radiusTriad, y: cy + Math.sin(time) * radiusTriad };
            const shipB = { x: cx + Math.cos(time + (Math.PI * 2 / 3)) * radiusTriad, y: cy + Math.sin(time + (Math.PI * 2 / 3)) * radiusTriad };
            const shipC = { x: cx + Math.cos(time + (Math.PI * 4 / 3)) * radiusTriad, y: cy + Math.sin(time + (Math.PI * 4 / 3)) * radiusTriad };

            // Trilho magnético conectando as naves
            ctx.beginPath();
            ctx.moveTo(shipA.x, shipA.y);
            ctx.lineTo(shipB.x, shipB.y);
            ctx.lineTo(shipC.x, shipC.y);
            ctx.closePath();
            ctx.strokeStyle = 'rgba(57, 255, 20, 0.6)';
            ctx.lineWidth = 2;
            ctx.setLineDash([6, 6]);
            ctx.stroke();
            ctx.setLineDash([]);

            // Partículas de Hélio em circulação na esteira
            for(let p = 0; p < 3; p++) {
                const pTime = time * 2 + (p * Math.PI * 2 / 3);
                const px = cx + Math.cos(pTime) * radiusTriad;
                const py = cy + Math.sin(pTime) * radiusTriad;
                ctx.beginPath();
                ctx.arc(px, py, 4, 0, Math.PI * 2);
                ctx.fillStyle = '#39ff14';
                ctx.shadowColor = '#39ff14';
                ctx.shadowBlur = 10;
                ctx.fill();
                ctx.shadowBlur = 0;
            }

            // Desenhar as 3 Naves da Tríade
            [shipA, shipB, shipC].forEach((ship, idx) => {
                ctx.beginPath();
                ctx.arc(ship.x, ship.y, 8, 0, Math.PI * 2);
                ctx.fillStyle = idx === 2 ? '#ff0055' : '#00f3ff';
                ctx.fill();
                ctx.strokeStyle = '#ffffff';
                ctx.lineWidth = 1.5;
                ctx.stroke();
            });

            requestAnimationFrame(render);
        }

        // Inicialização do aplicativo
        updateInfo();
        render();
    </script>
</body>
</html>
