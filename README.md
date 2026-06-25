<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Syasya - Game Salon 3D</title>
    <link href="https://fonts.googleapis.com/css2?family=Dancing+Script:wght@700&family=Nunito:wght@400;600;700;800&family=Pacifico&display=swap" rel="stylesheet">
    <style>
        :root {
            --pink: #FF7EB3;
            --pink-deep: #E8588C;
            --pink-light: #FFD1E0;
            --purple: #C9A7EB;
            --gold: #F0C878;
            --white: #FFF8FA;
            --dark: #3D2A35;
            --bg-dark: #1F1219;
            --shadow: 0 6px 24px rgba(255, 126, 179, 0.3);
            --shadow-lg: 0 12px 40px rgba(0, 0, 0, 0.35);
            --safe-bottom: env(safe-area-inset-bottom, 8px);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        html,
        body {
            height: 100%;
            width: 100%;
            overflow: hidden;
            font-family: 'Nunito', sans-serif;
            background: #1F1219;
            touch-action: pan-x pan-y pinch-zoom;
            -webkit-tap-highlight-color: transparent;
            -webkit-user-select: none;
            user-select: none;
            position: fixed;
            top: 0;
            left: 0;
        }

        #canvas-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
        }
        canvas {
            display: block;
        }

        #ui-overlay {
            position: fixed;
            z-index: 10;
            pointer-events: none;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }

        #header {
            pointer-events: auto;
            text-align: center;
            padding: 8px 16px 10px;
            background: linear-gradient(180deg, rgba(255, 126, 179, 0.95) 0%, rgba(255, 126, 179, 0.7) 85%, transparent 100%);
            border-radius: 0 0 24px 24px;
            box-shadow: var(--shadow-lg);
        }
        #header .title {
            font-family: 'Pacifico', cursive;
            font-size: clamp(1.3rem, 3vw, 2rem);
            color: #fff;
            text-shadow: 2px 2px 0px rgba(0, 0, 0, 0.2), 0 0 25px rgba(255, 255, 255, 0.5);
            letter-spacing: 2px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
        }
        #header .title .sparkle {
            font-size: 0.8em;
            animation: sparkleBounce 0.7s ease-in-out infinite alternate;
            display: inline-block;
        }
        #header .title .sparkle:nth-child(1) {
            animation-delay: 0s;
        }
        #header .title .sparkle:nth-child(3) {
            animation-delay: 0.3s;
        }
        @keyframes sparkleBounce {
            0% {
                transform: translateY(0) scale(1);
            }
            100% {
                transform: translateY(-6px) scale(1.25);
            }
        }
        #score-display {
            font-weight: 800;
            font-size: 0.85rem;
            color: #FFEAA7;
            text-shadow: 0 0 12px rgba(255, 234, 167, 0.7);
            letter-spacing: 1px;
        }

        #bottom-panel {
            pointer-events: auto;
            background: linear-gradient(0deg, rgba(30, 12, 22, 0.96) 0%, rgba(40, 16, 30, 0.9) 75%, transparent 100%);
            padding: 10px 12px calc(12px + var(--safe-bottom));
            border-radius: 22px 22px 0 0;
            box-shadow: 0 -6px 28px rgba(0, 0, 0, 0.45);
            display: flex;
            flex-direction: column;
            gap: 6px;
            backdrop-filter: blur(8px);
            -webkit-backdrop-filter: blur(8px);
        }
        #service-row {
            display: flex;
            justify-content: center;
            gap: 8px;
            flex-wrap: wrap;
        }
        .service-btn {
            font-family: 'Nunito', sans-serif;
            font-weight: 700;
            font-size: 0.78rem;
            padding: 9px 13px;
            border-radius: 22px;
            border: 2.5px solid rgba(255, 255, 255, 0.25);
            cursor: pointer;
            transition: all 0.25s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            color: #fff;
            letter-spacing: 0.5px;
            display: flex;
            align-items: center;
            gap: 5px;
            background: rgba(255, 255, 255, 0.06);
            white-space: nowrap;
            -webkit-tap-highlight-color: transparent;
            min-height: 38px;
        }
        .service-btn.active {
            background: var(--pink-deep);
            border-color: #fff;
            box-shadow: 0 0 20px rgba(255, 126, 179, 0.6);
            transform: scale(1.04);
        }
        .service-btn:active {
            transform: scale(0.93);
            transition: all 0.08s ease;
        }
        .service-btn .s-emoji {
            font-size: 1.2em;
        }

        #color-palette {
            display: flex;
            justify-content: center;
            gap: 10px;
            flex-wrap: wrap;
            min-height: 42px;
            align-items: center;
            padding: 4px 0;
        }
        .color-dot {
            width: 34px;
            height: 34px;
            border-radius: 50%;
            cursor: pointer;
            border: 3px solid rgba(255, 255, 255, 0.4);
            transition: all 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            box-shadow: 0 3px 10px rgba(0, 0, 0, 0.3);
            position: relative;
            -webkit-tap-highlight-color: transparent;
            flex-shrink: 0;
        }
        .color-dot:hover,
        .color-dot:active {
            transform: scale(1.2);
            border-color: #fff;
            box-shadow: 0 0 18px currentColor;
        }
        .color-dot.selected {
            border-color: #fff;
            box-shadow: 0 0 22px currentColor, 0 0 0 4px rgba(255, 255, 255, 0.5);
            transform: scale(1.15);
        }
        .color-dot.selected::after {
            content: '✓';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 0.9rem;
            font-weight: 900;
            color: #fff;
            text-shadow: 0 0 4px rgba(0, 0, 0, 0.6);
        }

        #action-row {
            display: flex;
            justify-content: center;
            gap: 10px;
            flex-wrap: wrap;
        }
        #btn-random {
            font-family: 'Nunito', sans-serif;
            font-weight: 700;
            font-size: 0.8rem;
            padding: 10px 18px;
            border-radius: 22px;
            border: 2px solid rgba(255, 255, 255, 0.4);
            cursor: pointer;
            background: rgba(240, 200, 120, 0.2);
            color: #FFEAA7;
            letter-spacing: 1px;
            transition: all 0.3s ease;
            -webkit-tap-highlight-color: transparent;
            min-height: 38px;
        }
        #btn-random:active {
            transform: scale(0.9);
            background: rgba(240, 200, 120, 0.4);
        }
        #btn-reset {
            font-family: 'Nunito', sans-serif;
            font-weight: 700;
            font-size: 0.8rem;
            padding: 10px 18px;
            border-radius: 22px;
            border: 2px solid rgba(255, 255, 255, 0.35);
            cursor: pointer;
            background: rgba(255, 255, 255, 0.06);
            color: #fff;
            letter-spacing: 1px;
            transition: all 0.3s ease;
            -webkit-tap-highlight-color: transparent;
            min-height: 38px;
        }
        #btn-reset:active {
            transform: scale(0.9);
            background: rgba(255, 255, 255, 0.18);
        }

        #label-hint {
            color: #FFB6C1;
            font-size: 0.7rem;
            text-align: center;
            letter-spacing: 1px;
            text-transform: uppercase;
            opacity: 0.8;
        }

        @media (max-width: 480px) {
            #header {
                padding: 6px 10px 8px;
                border-radius: 0 0 18px 18px;
            }
            #header .title {
                font-size: 1.1rem;
                gap: 3px;
            }
            #bottom-panel {
                padding: 8px 8px calc(10px + var(--safe-bottom));
                border-radius: 18px 18px 0 0;
                gap: 4px;
            }
            .service-btn {
                font-size: 0.7rem;
                padding: 7px 9px;
                border-radius: 18px;
                gap: 3px;
                min-height: 32px;
            }
            .service-btn .s-emoji {
                font-size: 1em;
            }
            .color-dot {
                width: 28px;
                height: 28px;
                border-width: 2.5px;
            }
            #btn-random,
            #btn-reset {
                font-size: 0.7rem;
                padding: 8px 13px;
                border-radius: 18px;
                min-height: 32px;
            }
            #color-palette {
                gap: 7px;
                min-height: 34px;
            }
            #label-hint {
                font-size: 0.65rem;
            }
        }
    </style>
</head>
<body>

    <div id="canvas-container"></div>

    <div id="ui-overlay">
        <div id="header">
            <div class="title">
                <span class="sparkle">✨</span> Syasya Salon <span class="sparkle">💋</span>
            </div>
            <div id="score-display">⭐ Bintang: <span id="score-value">0</span></div>
        </div>

        <div id="bottom-panel">
            <div id="label-hint">💖 Pilih Layanan & Warna</div>
            <div id="service-row">
                <button class="service-btn active" data-service="hair">
                    <span class="s-emoji">💇‍♀️</span> Rambut
                </button>
                <button class="service-btn" data-service="lips">
                    <span class="s-emoji">💄</span> Bibir
                </button>
                <button class="service-btn" data-service="blush">
                    <span class="s-emoji">😊</span> Pipi
                </button>
                <button class="service-btn" data-service="nails">
                    <span class="s-emoji">💅</span> Kuku
                </button>
            </div>
            <div id="color-palette"></div>
            <div id="action-row">
                <button id="btn-random">🎲 Acak Total</button>
                <button id="btn-reset">🔄 Reset</button>
            </div>
        </div>
    </div>

    <script type="importmap">
        {
            "imports": {
                "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
                "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
            }
        }
    </script>

    <script type="module">
        import * as THREE from 'three';
        import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

        // ==================== DOM ====================
        const container = document.getElementById('canvas-container');
        const scoreValueEl = document.getElementById('score-value');
        const colorPaletteEl = document.getElementById('color-palette');
        const serviceBtns = document.querySelectorAll('.service-btn');
        const btnRandom = document.getElementById('btn-random');
        const btnReset = document.getElementById('btn-reset');
        const labelHint = document.getElementById('label-hint');

        // ==================== THREE.JS SETUP ====================
        const scene = new THREE.Scene();
        scene.background = new THREE.Color('#2D1A28');
        scene.fog = new THREE.Fog('#2D1A28', 3, 14);

        const camera = new THREE.PerspectiveCamera(48, container.clientWidth / Math.max(container.clientHeight, 1), 0.3,
        20);
        camera.position.set(0.8, 1.9, 2.8);
        camera.lookAt(0, 0.95, -0.2);

        const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
        renderer.setSize(container.clientWidth, container.clientHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2.2));
        renderer.shadowMap.enabled = true;
        renderer.shadowMap.type = THREE.PCFSoftShadowMap;
        renderer.toneMapping = THREE.ACESFilmicToneMapping;
        renderer.toneMappingExposure = 1.15;
        container.appendChild(renderer.domElement);

        const controls = new OrbitControls(camera, renderer.domElement);
        controls.target.set(0, 0.9, -0.15);
        controls.enableDamping = true;
        controls.dampingFactor = 0.1;
        controls.minDistance = 1.5;
        controls.maxDistance = 6;
        controls.maxPolarAngle = Math.PI * 0.6;
        controls.minPolarAngle = 0.2;
        controls.maxAzimuthAngle = Math.PI * 0.65;
        controls.minAzimuthAngle = -Math.PI * 0.65;
        controls.touches = { ONE: THREE.TOUCH.ROTATE, TWO: THREE.TOUCH.DOLLY };
        controls.update();

        // ==================== LIGHTING ====================
        scene.add(new THREE.AmbientLight('#FFF5EE', 1.2));
        const sunLight = new THREE.DirectionalLight('#FFFDF7', 3.2);
        sunLight.position.set(5, 8, 5);
        sunLight.castShadow = true;
        sunLight.shadow.mapSize.width = 1024;
        sunLight.shadow.mapSize.height = 1024;
        sunLight.shadow.camera.near = 0.5;
        sunLight.shadow.camera.far = 25;
        sunLight.shadow.camera.left = -6;
        sunLight.shadow.camera.right = 6;
        sunLight.shadow.camera.top = 6;
        sunLight.shadow.camera.bottom = -1;
        sunLight.shadow.bias = -0.0003;
        scene.add(sunLight);

        const fillLight = new THREE.DirectionalLight('#FFD1E8', 1.0);
        fillLight.position.set(-2, 2, -1);
        scene.add(fillLight);

        const rimLight = new THREE.DirectionalLight('#E8D5FF', 0.8);
        rimLight.position.set(0, 1.8, -3);
        scene.add(rimLight);

        const mirrorLight = new THREE.PointLight('#FFF8F0', 1.5, 4, 2);
        mirrorLight.position.set(0, 1.5, -1.0);
        scene.add(mirrorLight);

        // ==================== MATERIALS ====================
        const matFloor = new THREE.MeshStandardMaterial({ color: '#F5E6DD', roughness: 0.45, metalness: 0.03 });
        const matFloorTile = new THREE.MeshStandardMaterial({ color: '#FDF0E8', roughness: 0.4, metalness: 0.03 });
        const matWall = new THREE.MeshStandardMaterial({ color: '#FFF0EB', roughness: 0.6, metalness: 0.02 });
        const matWallSide = new THREE.MeshStandardMaterial({ color: '#FFE8E0', roughness: 0.55, metalness: 0.02 });
        const matMirrorFrame = new THREE.MeshStandardMaterial({ color: '#F8E8D0', roughness: 0.2, metalness: 0.5 });
        const matMirrorGlass = new THREE.MeshStandardMaterial({ color: '#D8E8F0', roughness: 0.05, metalness: 0.9 });
        const matCounter = new THREE.MeshStandardMaterial({ color: '#FEFAF6', roughness: 0.3, metalness: 0.06 });
        const matStand = new THREE.MeshStandardMaterial({ color: '#F0E0D5', roughness: 0.35, metalness: 0.1 });
        const matSkin = new THREE.MeshStandardMaterial({ color: '#F5D0C0', roughness: 0.55, metalness: 0.02 });
        const matEyeWhite = new THREE.MeshStandardMaterial({ color: '#FEFEFE', roughness: 0.1, metalness: 0.05 });
        const matPupil = new THREE.MeshStandardMaterial({ color: '#3D2015', roughness: 0.15, metalness: 0.1 });
        const matLipDefault = new THREE.MeshStandardMaterial({ color: '#D4878C', roughness: 0.3, metalness: 0.05 });
        const matBlushDefault = new THREE.MeshStandardMaterial({
            color: '#F0C8C0',
            roughness: 0.5,
            metalness: 0.02,
            transparent: true,
            opacity: 0.45
        });
        const matHairDefault = new THREE.MeshStandardMaterial({ color: '#5C3A2E', roughness: 0.55, metalness: 0.04 });
        const matNailDefault = new THREE.MeshStandardMaterial({ color: '#F5D5C8', roughness: 0.25, metalness: 0.08 });
        const matBottle = new THREE.MeshStandardMaterial({ roughness: 0.15, metalness: 0.5 });
        const matGold = new THREE.MeshStandardMaterial({ color: '#E8C960', roughness: 0.2, metalness: 0.8 });

        // ==================== SCENE OBJECTS ====================
        // --- Floor ---
        const floorGroup = new THREE.Group();
        const floorW = 5.5;
        const tileS = 0.75;
        const numT = Math.floor(floorW / tileS);
        for (let i = 0; i < numT; i++) {
            for (let j = 0; j < numT; j++) {
                const tile = new THREE.Mesh(
                    new THREE.BoxGeometry(tileS * 0.9, 0.04, tileS * 0.9),
                    (i + j) % 2 === 0 ? matFloor : matFloorTile
                );
                tile.position.set((i - numT / 2 + 0.5) * tileS, 0.02, (j - numT / 2 + 0.5) * tileS);
                tile.receiveShadow = true;
                tile.castShadow = false;
                floorGroup.add(tile);
            }
        }
        scene.add(floorGroup);

        // --- Rug ---
        const rug = new THREE.Mesh(
            new THREE.BoxGeometry(1.6, 0.02, 1.2),
            new THREE.MeshStandardMaterial({ color: '#FFD1E0', roughness: 0.7, metalness: 0.02 })
        );
        rug.position.set(0, 0.05, -0.1);
        rug.receiveShadow = true;
        scene.add(rug);

        // --- Walls ---
        function makeWall(w, h, d, x, y, z, mat) {
            const wall = new THREE.Mesh(new THREE.BoxGeometry(w, h, d), mat);
            wall.position.set(x, y, z);
            wall.receiveShadow = true;
            wall.castShadow = true;
            scene.add(wall);
            return wall;
        }
        makeWall(5.2, 3.2, 0.2, 0, 1.6, -2.4, matWall);
        makeWall(0.2, 3.2, 4.8, -2.6, 1.6, -0.2, matWallSide);
        makeWall(0.2, 3.2, 4.8, 2.6, 1.6, -0.2, matWallSide);

        // --- Mirror ---
        const mirrorGroup = new THREE.Group();
        const mirrorGlass = new THREE.Mesh(new THREE.BoxGeometry(1.0, 1.3, 0.04), matMirrorGlass);
        mirrorGlass.position.set(0, 1.7, -2.28);
        mirrorGlass.receiveShadow = true;
        mirrorGroup.add(mirrorGlass);

        const frameThick = 0.06;
        const frameDepth = 0.08;
        const frameMat = matMirrorFrame;
        const fw = 1.1;
        const fh = 1.4;
        // top/bottom frames
        for (let sy of [-1, 1]) {
            const fb = new THREE.Mesh(new THREE.BoxGeometry(fw, frameThick, frameDepth), frameMat);
            fb.position.set(0, 1.7 + sy * (fh / 2), -2.28);
            fb.castShadow = true;
            mirrorGroup.add(fb);
        }
        // left/right frames
        for (let sx of [-1, 1]) {
            const fb = new THREE.Mesh(new THREE.BoxGeometry(frameThick, fh, frameDepth), frameMat);
            fb.position.set(sx * (fw / 2), 1.7, -2.28);
            fb.castShadow = true;
            mirrorGroup.add(fb);
        }
        // ornate top
        const ornate = new THREE.Mesh(new THREE.TorusGeometry(0.22, 0.04, 8, 16, Math.PI), matGold);
        ornate.position.set(0, 2.38, -2.28);
        ornate.rotation.z = Math.PI;
        mirrorGroup.add(ornate);
        scene.add(mirrorGroup);

        // --- Counter / Meja ---
        const counterGroup = new THREE.Group();
        const cTop = new THREE.Mesh(new THREE.BoxGeometry(2.0, 0.08, 0.8), matCounter);
        cTop.position.set(1.5, 0.55, -0.4);
        cTop.castShadow = true;
        cTop.receiveShadow = true;
        counterGroup.add(cTop);
        const cFront = new THREE.Mesh(
            new THREE.BoxGeometry(2.0, 0.4, 0.06),
            new THREE.MeshStandardMaterial({ color: '#F5EDE4', roughness: 0.35, metalness: 0.04 })
        );
        cFront.position.set(1.5, 0.31, -0.01);
        cFront.receiveShadow = true;
        counterGroup.add(cFront);
        for (let lx of [-0.8, 0.8]) {
            const leg = new THREE.Mesh(new THREE.CylinderGeometry(0.04, 0.05, 0.48, 10), matStand);
            leg.position.set(1.5 + lx, 0.24, -0.3);
            leg.castShadow = true;
            counterGroup.add(leg);
            const leg2 = new THREE.Mesh(new THREE.CylinderGeometry(0.04, 0.05, 0.48, 10), matStand);
            leg2.position.set(1.5 + lx, 0.24, 0.3);
            leg2.castShadow = true;
            counterGroup.add(leg2);
        }
        scene.add(counterGroup);

        // --- Product Bottles on Counter ---
        const bottleColors = ['#FF7EB3', '#C9A7EB', '#FFD700', '#7ECB76', '#FF9A56', '#5DADE2', '#F4728A', '#A8D8C8'];
        const bottleMeshes = [];
        for (let i = 0; i < 6; i++) {
            const bGroup = new THREE.Group();
            const body = new THREE.Mesh(
                new THREE.CylinderGeometry(0.04, 0.05, 0.18, 14),
                new THREE.MeshStandardMaterial({ color: bottleColors[i], roughness: 0.15, metalness: 0.4 })
            );
            body.position.y = 0.09;
            bGroup.add(body);
            const cap = new THREE.Mesh(
                new THREE.CylinderGeometry(0.035, 0.04, 0.04, 12),
                matGold
            );
            cap.position.y = 0.19;
            bGroup.add(cap);
            bGroup.position.set(1.5 + (i - 2.5) * 0.22, 0.6, -0.4);
            bGroup.castShadow = true;
            scene.add(bGroup);
            bottleMeshes.push(bGroup);
        }

        // --- Mannequin Stand ---
        const standGroup = new THREE.Group();
        const basePlate = new THREE.Mesh(new THREE.CylinderGeometry(0.16, 0.18, 0.05, 20), matStand);
        basePlate.position.y = 0.07;
        basePlate.castShadow = true;
        basePlate.receiveShadow = true;
        standGroup.add(basePlate);
        const pole = new THREE.Mesh(new THREE.CylinderGeometry(0.04, 0.05, 0.55, 14), matStand);
        pole.position.y = 0.32;
        pole.castShadow = true;
        standGroup.add(pole);
        const neckBase = new THREE.Mesh(new THREE.CylinderGeometry(0.06, 0.05, 0.08, 14), matStand);
        neckBase.position.y = 0.58;
        standGroup.add(neckBase);
        standGroup.position.set(0, 0, -0.15);
        scene.add(standGroup);

        // --- Mannequin Head ---
        const headGroup = new THREE.Group();
        headGroup.position.set(0, 0.68, -0.15);

        // Neck
        const neck = new THREE.Mesh(new THREE.CylinderGeometry(0.06, 0.07, 0.16, 16), matSkin);
        neck.position.y = 0.08;
        neck.castShadow = true;
        headGroup.add(neck);

        // Head sphere
        const headSphere = new THREE.Mesh(new THREE.SphereGeometry(0.2, 36, 36), matSkin);
        headSphere.position.y = 0.28;
        headSphere.castShadow = true;
        headSphere.receiveShadow = true;
        headSphere.name = 'head';
        headGroup.add(headSphere);

        // Eyes
        for (let sx of [-1, 1]) {
            const eyeWhite = new THREE.Mesh(new THREE.SphereGeometry(0.04, 16, 16), matEyeWhite);
            eyeWhite.position.set(sx * 0.07, 0.32, 0.18);
            headGroup.add(eyeWhite);
            const pupil = new THREE.Mesh(new THREE.SphereGeometry(0.022, 10, 10), matPupil);
            pupil.position.set(sx * 0.07, 0.32, 0.215);
            headGroup.add(pupil);
            // tiny highlight
            const highlight = new THREE.Mesh(
                new THREE.SphereGeometry(0.008, 6, 6),
                new THREE.MeshBasicMaterial({ color: '#FFFFFF' })
            );
            highlight.position.set(sx * 0.07 + 0.012, 0.328, 0.225);
            headGroup.add(highlight);
        }

        // Nose
        const nose = new THREE.Mesh(new THREE.SphereGeometry(0.025, 12, 12),
            new THREE.MeshStandardMaterial({ color: '#F0C8B8', roughness: 0.5, metalness: 0.02 }));
        nose.position.set(0, 0.26, 0.19);
        nose.scale.set(1, 0.8, 0.7);
        headGroup.add(nose);

        // Lips
        const lipsMesh = new THREE.Mesh(
            new THREE.SphereGeometry(0.045, 20, 14, 0, Math.PI * 2, 0, Math.PI * 0.55),
            matLipDefault
        );
        lipsMesh.position.set(0, 0.21, 0.18);
        lipsMesh.scale.set(1.3, 0.45, 0.55);
        lipsMesh.name = 'lips';
        lipsMesh.castShadow = true;
        headGroup.add(lipsMesh);

        // Cheeks (blush)
        const cheekMeshes = [];
        for (let sx of [-1, 1]) {
            const cheek = new THREE.Mesh(new THREE.SphereGeometry(0.04, 16, 16), matBlushDefault.clone());
            cheek.position.set(sx * 0.1, 0.26, 0.16);
            cheek.name = 'cheek';
            cheek.castShadow = true;
            headGroup.add(cheek);
            cheekMeshes.push(cheek);
        }

        // Hair
        const hairMesh = new THREE.Mesh(
            new THREE.SphereGeometry(0.22, 32, 28),
            matHairDefault
        );
        hairMesh.position.set(0, 0.36, 0.0);
        hairMesh.scale.set(1.02, 0.5, 1.02);
        hairMesh.name = 'hair';
        hairMesh.castShadow = true;
        hairMesh.receiveShadow = true;
        headGroup.add(hairMesh);

        // Bangs (front hair)
        const bangsMesh = new THREE.Mesh(
            new THREE.SphereGeometry(0.18, 24, 20, 0, Math.PI * 2, 0, Math.PI * 0.45),
            matHairDefault
        );
        bangsMesh.position.set(0, 0.32, 0.14);
        bangsMesh.scale.set(1.0, 0.35, 0.5);
        bangsMesh.name = 'hair-bangs';
        bangsMesh.castShadow = true;
        headGroup.add(bangsMesh);

        scene.add(headGroup);

        // --- Hand Model for Nails (on counter) ---
        const handGroup = new THREE.Group();
        handGroup.position.set(0.8, 0.62, -0.38);
        handGroup.rotation.set(0, -0.3, -0.1);
        const palm = new THREE.Mesh(
            new THREE.BoxGeometry(0.12, 0.02, 0.14),
            new THREE.MeshStandardMaterial({ color: '#F5D0C0', roughness: 0.5, metalness: 0.02 })
        );
        palm.position.y = 0.01;
        handGroup.add(palm);
        const nailMeshes = [];
        const fingerLengths = [0.1, 0.13, 0.14, 0.12, 0.09];
        const fingerX = [-0.06, -0.03, 0, 0.03, 0.06];
        for (let i = 0; i < 5; i++) {
            const finger = new THREE.Mesh(
                new THREE.CylinderGeometry(0.012, 0.014, fingerLengths[i], 10),
                new THREE.MeshStandardMaterial({ color: '#F5D0C0', roughness: 0.5, metalness: 0.02 })
            );
            finger.position.set(fingerX[i], 0.02, 0.06 + fingerLengths[i] / 2);
            finger.rotation.x = -0.4;
            handGroup.add(finger);
            // Nail tip
            const nail = new THREE.Mesh(
                new THREE.SphereGeometry(0.016, 10, 8, 0, Math.PI * 2, 0, Math.PI * 0.6),
                matNailDefault
            );
            nail.position.set(fingerX[i], 0.03, 0.06 + fingerLengths[i] - 0.01);
            nail.name = 'nail';
            nail.scale.set(0.9, 0.5, 0.7);
            handGroup.add(nail);
            nailMeshes.push(nail);
        }
        scene.add(handGroup);

        // --- Sparkle Particles Pool ---
        const sparklePool = [];
        const sparkleGroup = new THREE.Group();
        sparkleGroup.position.set(0, 0.68, -0.15);
        scene.add(sparkleGroup);

        function createSparkleParticle() {
            const geo = new THREE.SphereGeometry(0.008 + Math.random() * 0.018, 6, 6);
            const mat = new THREE.MeshBasicMaterial({
                color: new THREE.Color().setHSL(Math.random() * 0.2 + 0.75, 0.9, 0.65 + Math.random() * 0.35),
                transparent: true,
                opacity: 1,
            });
            const p = new THREE.Mesh(geo, mat);
            p.visible = false;
            p.userData = {
                life: 0,
                maxLife: 0.6 + Math.random() * 1.0,
                velocity: new THREE.Vector3(
                    (Math.random() - 0.5) * 0.6,
                    Math.random() * 0.7 + 0.2,
                    (Math.random() - 0.5) * 0.6
                ),
                basePos: new THREE.Vector3(),
            };
            sparkleGroup.add(p);
            sparklePool.push(p);
            return p;
        }
        for (let i = 0; i < 50; i++) {
            createSparkleParticle();
        }

        function emitSparkles(worldPos, count = 20) {
            const localPos = sparkleGroup.worldToLocal(worldPos.clone());
            let emitted = 0;
            sparklePool.forEach(p => {
                if (!p.visible && emitted < count) {
                    p.visible = true;
                    p.userData.life = 0;
                    p.userData.maxLife = 0.5 + Math.random() * 1.0;
                    p.userData.basePos.copy(localPos).add(
                        new THREE.Vector3((Math.random() - 0.5) * 0.15, (Math.random() - 0.5) * 0.15, (Math
                            .random() - 0.5) * 0.15)
                    );
                    p.position.copy(p.userData.basePos);
                    p.userData.velocity.set(
                        (Math.random() - 0.5) * 0.5,
                        Math.random() * 0.6 + 0.15,
                        (Math.random() - 0.5) * 0.5
                    );
                    p.material.opacity = 1;
                    p.scale.setScalar(1);
                    emitted++;
                }
            });
        }

        // ==================== GAME STATE ====================
        let score = 0;
        let activeService = 'hair';
        let selectedColors = {
            hair: '#5C3A2E',
            lips: '#D4878C',
            blush: '#F0C8C0',
            nails: '#F5D5C8',
        };
        let currentColorSelection = {
            hair: '#5C3A2E',
            lips: '#D4878C',
            blush: '#F0C8C0',
            nails: '#F5D5C8',
        };

        // Color palettes per service
        const colorPalettes = {
            hair: [
                { name: 'Cokelat', hex: '#5C3A2E' }, { name: 'Hitam', hex: '#2C1810' }, { name: 'Blonde',
                hex: '#E8C98B' },
                { name: 'Pink', hex: '#F4A0B8' }, { name: 'Ungu', hex: '#9B59B6' }, { name: 'Biru',
            hex: '#5DADE2' },
                { name: 'Merah', hex: '#C0392B' },
            ],
            lips: [
                { name: 'Merah', hex: '#C0392B' }, { name: 'Pink', hex: '#F4728A' }, { name: 'Koral',
            hex: '#FF6F61' },
                { name: 'Plum', hex: '#8E4585' }, { name: 'Natural', hex: '#D4878C' }, { name: 'Berry',
                hex: '#9B2D4C' },
            ],
            blush: [
                { name: 'Rosy', hex: '#F8B8C8' }, { name: 'Peach', hex: '#FBC8A8' }, { name: 'Koral',
            hex: '#FF9A8C' },
                { name: 'Plum', hex: '#D4A0B8' }, { name: 'Natural', hex: '#F0C8C0' },
            ],
            nails: [
                { name: 'Merah', hex: '#D42F3B' }, { name: 'Pink', hex: '#F8A0B8' }, { name: 'Putih',
            hex: '#F5F5F5' },
                { name: 'Emas', hex: '#E8C960' }, { name: 'Lavender', hex: '#C9A8D4' }, { name: 'Mint',
                hex: '#A8D8C8' },
            ],
        };

        // ==================== APPLY COLOR ====================
        function applyColor(service, hexColor) {
            selectedColors[service] = hexColor;
            currentColorSelection[service] = hexColor;

            switch (service) {
                case 'hair':
                    hairMesh.material.color.set(hexColor);
                    bangsMesh.material.color.set(hexColor);
                    emitSparkles(new THREE.Vector3(0, 1.05, -0.15), 25);
                    break;
                case 'lips':
                    lipsMesh.material.color.set(hexColor);
                    emitSparkles(new THREE.Vector3(0, 0.89, 0.03), 18);
                    break;
                case 'blush':
                    cheekMeshes.forEach(c => {
                        c.material.color.set(hexColor);
                        c.material.opacity = hexColor === '#F0C8C0' ? 0.45 : 0.7;
                    });
                    emitSparkles(new THREE.Vector3(0, 0.94, 0.01), 15);
                    break;
                case 'nails':
                    nailMeshes.forEach(n => n.material.color.set(hexColor));
                    emitSparkles(new THREE.Vector3(0.8, 1.3, -0.53), 20);
                    break;
            }
            score += 10;
            updateUI();
        }

        function randomMakeover() {
            const services = ['hair', 'lips', 'blush', 'nails'];
            services.forEach(s => {
                const palette = colorPalettes[s];
                const randomColor = palette[Math.floor(Math.random() * palette.length)].hex;
                applyColor(s, randomColor);
            });
            score += 15;
            updateUI();
            labelHint.textContent = '🎉 Makeover acak! Keren!';
            setTimeout(() => { labelHint.textContent = '💖 Pilih Layanan & Warna'; }, 2000);
        }

        function resetAll() {
            const defaults = {
                hair: '#5C3A2E',
                lips: '#D4878C',
                blush: '#F0C8C0',
                nails: '#F5D5C8',
            };
            Object.keys(defaults).forEach(s => {
                selectedColors[s] = defaults[s];
                currentColorSelection[s] = defaults[s];
                applyColorImmediate(s, defaults[s]);
            });
            score = Math.max(0, score - 5);
            updateUI();
            labelHint.textContent = '🔄 Kembali ke tampilan awal';
            setTimeout(() => { labelHint.textContent = '💖 Pilih Layanan & Warna'; }, 1800);
        }

        function applyColorImmediate(service, hexColor) {
            switch (service) {
                case 'hair':
                    hairMesh.material.color.set(hexColor);
                    bangsMesh.material.color.set(hexColor);
                    break;
                case 'lips':
                    lipsMesh.material.color.set(hexColor);
                    break;
                case 'blush':
                    cheekMeshes.forEach(c => {
                        c.material.color.set(hexColor);
                        c.material.opacity = hexColor === '#F0C8C0' ? 0.45 : 0.7;
                    });
                    break;
                case 'nails':
                    nailMeshes.forEach(n => n.material.color.set(hexColor));
                    break;
            }
        }

        // ==================== UI ====================
        function updateUI() {
            scoreValueEl.textContent = score;
            renderColorPalette();
            updateServiceButtons();
        }

        function updateServiceButtons() {
            serviceBtns.forEach(btn => {
                btn.classList.toggle('active', btn.dataset.service === activeService);
            });
        }

        function renderColorPalette() {
            const palette = colorPalettes[activeService];
            const currentHex = currentColorSelection[activeService];
            colorPaletteEl.innerHTML = '';
            palette.forEach(color => {
                const dot = document.createElement('div');
                dot.className = 'color-dot';
                dot.style.backgroundColor = color.hex;
                dot.style.color = color.hex;
                dot.title = color.name;
                if (color.hex === currentHex) {
                    dot.classList.add('selected');
                }
                dot.addEventListener('click', (e) => {
                    e.stopPropagation();
                    currentColorSelection[activeService] = color.hex;
                    applyColor(activeService, color.hex);
                    renderColorPalette();
                });
                dot.addEventListener('touchend', (e) => {
                    e.stopPropagation();
                    e.preventDefault();
                    currentColorSelection[activeService] = color.hex;
                    applyColor(activeService, color.hex);
                    renderColorPalette();
                });
                colorPaletteEl.appendChild(dot);
            });
        }

        // Service button clicks
        serviceBtns.forEach(btn => {
            btn.addEventListener('click', () => {
                activeService = btn.dataset.service;
                currentColorSelection[activeService] = selectedColors[activeService];
                updateUI();
                labelHint.textContent = '💖 Pilih warna untuk ' + btn.textContent.trim();
            });
            btn.addEventListener('touchend', (e) => {
                e.preventDefault();
                activeService = btn.dataset.service;
                currentColorSelection[activeService] = selectedColors[activeService];
                updateUI();
                labelHint.textContent = '💖 Pilih warna untuk ' + btn.textContent.trim();
            });
        });

        btnRandom.addEventListener('click', randomMakeover);
        btnRandom.addEventListener('touchend', (e) => { e.preventDefault();
            randomMakeover(); });
        btnReset.addEventListener('click', resetAll);
        btnReset.addEventListener('touchend', (e) => { e.preventDefault();
            resetAll(); });

        // ==================== RAYCASTER ====================
        const raycaster = new THREE.Raycaster();
        raycaster.far = 7;
        const mouse = new THREE.Vector2();
        const clickable3D = [hairMesh, bangsMesh, lipsMesh, ...cheekMeshes, ...nailMeshes, headSphere];

        function getIntersection(event) {
            mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
            raycaster.setFromCamera(mouse, camera);
            return raycaster.intersectObjects(clickable3D, false);
        }

        renderer.domElement.addEventListener('click', (event) => {
            const intersects = getIntersection(event);
            if (intersects.length === 0) return;
            const obj = intersects[0].object;
            let serviceToApply = null;
            if (obj.name === 'hair' || obj.name === 'hair-bangs') serviceToApply = 'hair';
            else if (obj.name === 'lips') serviceToApply = 'lips';
            else if (obj.name === 'cheek') serviceToApply = 'blush';
            else if (obj.name === 'nail') serviceToApply = 'nails';

            if (serviceToApply) {
                activeService = serviceToApply;
                currentColorSelection[activeService] = selectedColors[activeService];
                updateUI();
                // Apply current color (re-affirm) or cycle? Let's apply the current selection
                applyColor(serviceToApply, currentColorSelection[serviceToApply]);
                labelHint.textContent = '👆 ' + serviceToApply + ' disentuh!';
                setTimeout(() => { labelHint.textContent = '💖 Pilih Layanan & Warna'; }, 1500);
            }
        });

        renderer.domElement.addEventListener('touchend', (event) => {
            if (event.target.tagName === 'BUTTON' || event.target.tagName === 'DIV' && event.target.closest(
                    '#bottom-panel')) {
                return;
            }
            const touch = event.changedTouches[0];
            if (!touch) return;
            const fakeEvent = { clientX: touch.clientX, clientY: touch.clientY };
            const intersects = getIntersection(fakeEvent);
            if (intersects.length === 0) return;
            const obj = intersects[0].object;
            let serviceToApply = null;
            if (obj.name === 'hair' || obj.name === 'hair-bangs') serviceToApply = 'hair';
            else if (obj.name === 'lips') serviceToApply = 'lips';
            else if (obj.name === 'cheek') serviceToApply = 'blush';
            else if (obj.name === 'nail') serviceToApply = 'nails';

            if (serviceToApply) {
                activeService = serviceToApply;
                currentColorSelection[activeService] = selectedColors[activeService];
                updateUI();
                applyColor(serviceToApply, currentColorSelection[serviceToApply]);
                labelHint.textContent = '👆 ' + serviceToApply + ' disentuh!';
                setTimeout(() => { labelHint.textContent = '💖 Pilih Layanan & Warna'; }, 1500);
            }
        });

        // ==================== ANIMATION LOOP ====================
        const clock = new THREE.Clock();
        let sparkleTime = 0;

        function animate(timestamp) {
            requestAnimationFrame(animate);
            const dt = Math.min(clock.getDelta(), 0.12);
            controls.update();
            sparkleTime += dt;

            // Update sparkle particles
            sparklePool.forEach(p => {
                if (p.visible) {
                    p.userData.life += dt;
                    if (p.userData.life > p.userData.maxLife) {
                        p.visible = false;
                    } else {
                        const progress = p.userData.life / p.userData.maxLife;
                        p.position.copy(p.userData.basePos).add(
                            p.userData.velocity.clone().multiplyScalar(p.userData.life)
                        );
                        p.position.y += Math.sin(sparkleTime * 12 + p.userData.life * 20) * 0.03;
                        p.material.opacity = Math.max(0, 1 - progress);
                        p.scale.setScalar(1 - progress * 0.8);
                    }
                }
            });

            // Gentle bottle bob
            bottleMeshes.forEach((b, i) => {
                b.position.y = 0.6 + Math.sin(sparkleTime * 1.8 + i * 0.9) * 0.015;
            });

            // Mirror light subtle pulse
            mirrorLight.intensity = 1.5 + Math.sin(sparkleTime * 0.7) * 0.3;

            renderer.render(scene, camera);
        }

        // ==================== WINDOW RESIZE ====================
        window.addEventListener('resize', () => {
            camera.aspect = container.clientWidth / Math.max(container.clientHeight, 1);
            camera.updateProjectionMatrix();
            renderer.setSize(container.clientWidth, container.clientHeight);
        });

        // Handle orientation change on mobile
        window.addEventListener('orientationchange', () => {
            setTimeout(() => {
                camera.aspect = container.clientWidth / Math.max(container.clientHeight, 1);
                camera.updateProjectionMatrix();
                renderer.setSize(container.clientWidth, container.clientHeight);
            }, 300);
        });

        // ==================== INIT ====================
        function init() {
            currentColorSelection = { ...selectedColors };
            updateUI();
            labelHint.textContent = '💖 Pilih Layanan & Warna untuk memulai!';
            requestAnimationFrame(animate);
        }

        init();

        console.log('💋 Syasya Salon 3D siap!');
        console.log('👩‍🎨 Cara bermain:');
        console.log('  - Pilih layanan: Rambut, Bibir, Pipi, atau Kuku');
        console.log('  - Pilih warna dari palette');
        console.log('  - Atau sentuh langsung bagian manekin di layar 3D!');
        console.log('  - Tekan "Acak Total" untuk makeover kejutan 🎲');
        console.log('  - Putar kamera dengan gestur sentuh ✨');
        console.log('💡 Sentuh rambut, bibir, pipi, atau kuku manekin langsung di tampilan 3D!');
    </script>
</body>
</html>
