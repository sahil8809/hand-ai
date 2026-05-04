# hand-ai

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Handy AI — Gesture Intelligence Platform</title>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;700;800&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
:root {
  --accent: #7cffc4;
  --accent2: #ff6bca;
  --accent3: #6bb5ff;
  --bg: #03020a;
  --glass: rgba(255,255,255,0.06);
  --glass-border: rgba(255,255,255,0.10);
  --mode-accent: var(--accent);
}
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html, body { width: 100%; height: 100%; overflow: hidden; background: var(--bg); color: #fff; font-family: 'DM Mono', monospace; }

/* ── NOISE OVERLAY ── */
#noise {
  position: fixed; inset: 0; z-index: 100; pointer-events: none;
  opacity: 0.025;
}

/* ── TOP STRIPE ── */
#stripe {
  position: fixed; top: 0; left: 0; right: 0; height: 2px; z-index: 200;
  background: linear-gradient(90deg, var(--mode-accent), transparent, var(--mode-accent));
  animation: stripeFlow 3s linear infinite;
}
@keyframes stripeFlow { 0%{background-position:0% 50%} 100%{background-position:200% 50%} }

/* ── SPLASH ── */
#splash {
  position: fixed; inset: 0; z-index: 500;
  background: var(--bg);
  display: flex; flex-direction: column; align-items: center; justify-content: center;
  gap: 24px;
  transition: opacity 0.8s ease, transform 0.8s ease;
}
#splash.hide { opacity: 0; transform: scale(1.04); pointer-events: none; }
.splash-rings {
  position: absolute; width: 420px; height: 420px;
  top: 50%; left: 50%; transform: translate(-50%,-50%);
  pointer-events: none;
}
.ring {
  position: absolute; border-radius: 50%;
  border: 1px solid rgba(124,255,196,0.18);
  top: 50%; left: 50%; transform: translate(-50%,-50%);
  animation: ringPulse 3s ease-in-out infinite;
}
.ring:nth-child(1){ width:160px;height:160px;animation-delay:0s; }
.ring:nth-child(2){ width:280px;height:280px;animation-delay:0.6s; border-color:rgba(107,181,255,0.14); }
.ring:nth-child(3){ width:400px;height:400px;animation-delay:1.2s; border-color:rgba(255,107,202,0.10); }
@keyframes ringPulse {
  0%,100%{ opacity:0.3; transform:translate(-50%,-50%) scale(0.95); }
  50%{ opacity:1; transform:translate(-50%,-50%) scale(1.05); }
}
.splash-logo {
  font-family: 'Syne', sans-serif; font-weight: 800; font-size: clamp(56px,10vw,96px);
  background: linear-gradient(135deg, var(--accent) 0%, var(--accent3) 50%, var(--accent2) 100%);
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
  background-clip: text; line-height: 1; position: relative; z-index: 1;
  filter: drop-shadow(0 0 40px rgba(124,255,196,0.3));
}
.splash-sub {
  font-family: 'DM Mono', monospace; font-size: 11px; letter-spacing: 0.25em;
  text-transform: uppercase; color: rgba(255,255,255,0.4);
}
.splash-pills {
  display: flex; gap: 10px; flex-wrap: wrap; justify-content: center;
}
.pill {
  padding: 7px 16px; border-radius: 100px;
  border: 1px solid rgba(255,255,255,0.12);
  background: rgba(255,255,255,0.05);
  font-size: 11px; font-family: 'DM Mono'; color: rgba(255,255,255,0.6);
  backdrop-filter: blur(8px);
}
.cta {
  margin-top: 8px; padding: 14px 40px; border-radius: 100px; border: none;
  background: var(--accent); color: #03020a;
  font-family: 'Syne', sans-serif; font-weight: 800; font-size: 15px;
  cursor: pointer; letter-spacing: 0.05em;
  box-shadow: 0 0 30px rgba(124,255,196,0.4), 0 0 80px rgba(124,255,196,0.15);
  transition: transform 0.15s, box-shadow 0.15s;
}
.cta:hover { transform: scale(1.04); box-shadow: 0 0 50px rgba(124,255,196,0.6), 0 0 100px rgba(124,255,196,0.2); }
.splash-note { font-size: 10px; color: rgba(255,255,255,0.25); letter-spacing: 0.08em; }

/* ── LAYERS ── */
video, canvas { position: fixed; inset: 0; width: 100%; height: 100%; }
video { z-index: 1; transform: scaleX(-1); filter: brightness(0.3) saturate(0.6); object-fit: cover; }
#bgCanvas { z-index: 2; }
#fxCanvas { z-index: 3; }
#drawCanvas { z-index: 4; }

/* ── TOP BAR ── */
#topbar {
  position: fixed; top: 12px; left: 50%; transform: translateX(-50%);
  z-index: 150; display: flex; align-items: center; gap: 12px;
  padding: 8px 16px; border-radius: 100px;
  background: rgba(3,2,10,0.6); backdrop-filter: blur(20px);
  border: 1px solid var(--glass-border);
  box-shadow: 0 4px 30px rgba(0,0,0,0.4);
}
.brand {
  font-family: 'Syne', sans-serif; font-weight: 800; font-size: 15px;
  background: linear-gradient(90deg, var(--accent), var(--accent3));
  -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;
  white-space: nowrap;
}
.beta-badge {
  font-size: 8px; padding: 2px 7px; border-radius: 100px;
  background: rgba(124,255,196,0.15); color: var(--accent);
  border: 1px solid rgba(124,255,196,0.3); letter-spacing: 0.1em;
  text-transform: uppercase;
}
.mode-switcher {
  display: flex; background: rgba(255,255,255,0.06); border-radius: 100px;
  padding: 3px; gap: 2px;
}
.mode-btn {
  border: none; background: transparent; color: rgba(255,255,255,0.4);
  font-size: 13px; padding: 6px 14px; border-radius: 100px; cursor: pointer;
  transition: all 0.2s; display: flex; align-items: center; gap: 5px;
  font-family: 'DM Mono'; font-size: 11px; white-space: nowrap;
}
.mode-btn.active {
  background: rgba(255,255,255,0.1); color: #fff;
  box-shadow: 0 0 12px color-mix(in srgb, var(--mode-accent) 40%, transparent);
}
.chip {
  display: flex; align-items: center; gap: 6px; padding: 5px 12px;
  border-radius: 100px; background: rgba(255,255,255,0.06);
  border: 1px solid rgba(255,255,255,0.08); font-size: 10px;
  letter-spacing: 0.05em;
}
.chip-dot { width: 6px; height: 6px; border-radius: 50%; background: #444; }
.chip-dot.green { background: var(--accent); box-shadow: 0 0 8px var(--accent); }

/* ── BOTTOM HUD ── */
#bottomHud {
  position: fixed; bottom: 24px; left: 50%; transform: translateX(-50%);
  z-index: 150; display: flex; align-items: center; gap: 10px;
  padding: 10px 20px; border-radius: 100px;
  background: rgba(3,2,10,0.7); backdrop-filter: blur(20px);
  border: 1px solid var(--glass-border);
}
.gesture-label { font-family: 'DM Mono'; font-size: 11px; color: rgba(255,255,255,0.7); letter-spacing: 0.1em; }
.active-dot {
  width: 8px; height: 8px; border-radius: 50%;
  background: var(--accent); opacity: 0;
  box-shadow: 0 0 10px var(--accent);
  transition: opacity 0.2s;
  animation: dotPulse 1.5s ease-in-out infinite;
}
.active-dot.show { opacity: 1; }
@keyframes dotPulse { 0%,100%{transform:scale(1)} 50%{transform:scale(1.4)} }

/* ── TOAST ── */
#toast {
  position: fixed; inset: 0; z-index: 160; display: flex; align-items: center; justify-content: center;
  pointer-events: none;
}
.toast-inner {
  font-family: 'Syne', sans-serif; font-weight: 800; font-size: clamp(28px,5vw,48px);
  color: var(--mode-accent); opacity: 0; transform: scale(0.85);
  transition: opacity 0.2s, transform 0.3s cubic-bezier(0.34,1.56,0.64,1);
  text-shadow: 0 0 40px color-mix(in srgb, var(--mode-accent) 60%, transparent);
  text-align: center; padding: 0 20px;
}
.toast-inner.show { opacity: 1; transform: scale(1); }

/* ── HELP PANEL ── */
#helpPanel {
  position: fixed; bottom: 90px; right: 20px; z-index: 150;
  padding: 14px 18px; border-radius: 16px;
  background: rgba(3,2,10,0.7); backdrop-filter: blur(20px);
  border: 1px solid var(--glass-border);
  max-width: 220px; font-size: 10px; line-height: 1.8;
  color: rgba(255,255,255,0.45);
}
#helpPanel strong { color: rgba(255,255,255,0.7); font-family: 'Syne'; font-size: 11px; }

/* ── WRITE MODE UI ── */
#clearBtn {
  position: fixed; bottom: 24px; right: 20px; z-index: 150;
  padding: 10px 20px; border-radius: 100px; border: 1px solid rgba(107,181,255,0.3);
  background: rgba(3,2,10,0.7); backdrop-filter: blur(12px);
  color: var(--accent3); font-family: 'DM Mono'; font-size: 11px;
  cursor: pointer; display: none; letter-spacing: 0.08em;
  transition: all 0.2s;
}
#clearBtn:hover { background: rgba(107,181,255,0.1); }
#shakeRing {
  position: fixed; bottom: 70px; right: 24px; z-index: 151;
  display: none;
}
#shakeRing circle.track { fill: none; stroke: rgba(255,255,255,0.08); stroke-width: 3; }
#shakeRing circle.prog { fill: none; stroke-width: 3; stroke-linecap: round;
  transition: stroke-dashoffset 0.1s, stroke 0.3s;
  transform: rotate(-90deg); transform-origin: 22px 22px;
}

/* ── COPYRIGHT ── */
#copyright {
  position: fixed; bottom: 8px; left: 16px; z-index: 150;
  font-size: 9px; color: rgba(255,255,255,0.2); letter-spacing: 0.06em;
}
#copyright a { color: rgba(124,255,196,0.4); text-decoration: none; }
#copyright a:hover { color: var(--accent); }

/* ── RESPONSIVE ── */
@media (max-width: 600px) {
  .mode-btn span.label { display: none; }
  .splash-pills { flex-direction: column; align-items: center; }
  #helpPanel { max-width: 160px; font-size: 9px; }
}
</style>
</head>
<body>

<!-- NOISE -->
<svg id="noise" xmlns="http://www.w3.org/2000/svg" width="100%" height="100%">
  <filter id="fn"><feTurbulence type="fractalNoise" baseFrequency="0.65" numOctaves="3" stitchTiles="stitch"/><feColorMatrix type="saturate" values="0"/></filter>
  <rect width="100%" height="100%" filter="url(#fn)" opacity="1"/>
</svg>

<!-- TOP STRIPE -->
<div id="stripe"></div>

<!-- SPLASH -->
<div id="splash">
  <div class="splash-rings">
    <div class="ring"></div><div class="ring"></div><div class="ring"></div>
  </div>
  <div class="splash-logo">Handy AI</div>
  <div class="splash-sub">Gesture Intelligence Platform</div>
  <div class="splash-pills">
    <div class="pill">Real-time Tracking</div>
    <div class="pill">Gesture Physics</div>
    <div class="pill">Air Drawing</div>
  </div>
  <button class="cta" id="enterBtn">Enter Experience</button>
  <div class="splash-note">Camera access required · Best in Chrome / Edge</div>
</div>

<!-- VIDEO + CANVASES -->
<video id="video" autoplay muted playsinline></video>
<canvas id="bgCanvas"></canvas>
<canvas id="fxCanvas"></canvas>
<canvas id="drawCanvas"></canvas>

<!-- TOP BAR -->
<div id="topbar">
  <div class="brand">Handy AI</div>
  <div class="beta-badge">Beta</div>
  <div class="mode-switcher">
    <button class="mode-btn active" data-mode="play">▶ <span class="label">Play</span></button>
    <button class="mode-btn" data-mode="shapes">♥ <span class="label">Shapes</span></button>
    <button class="mode-btn" data-mode="write">✏ <span class="label">Write</span></button>
  </div>
  <div class="chip"><div class="chip-dot" id="fpsDot"></div><span id="fpsLabel">-- FPS</span></div>
  <div class="chip">🖐 <span id="handCount">0</span></div>
</div>

<!-- BOTTOM HUD -->
<div id="bottomHud">
  <div class="active-dot" id="activeDot"></div>
  <div class="gesture-label" id="gestureLabel">No hands detected</div>
</div>

<!-- TOAST -->
<div id="toast"><div class="toast-inner" id="toastInner"></div></div>

<!-- HELP PANEL -->
<div id="helpPanel" id="helpPanel">
  <strong>PLAY MODE</strong><br>
  Pinch thumb+index → ⚡ zap<br>
  Bring both hands close → ⚡ lightning<br>
  Two hands → mandala<br>
</div>

<!-- WRITE UI -->
<button id="clearBtn">Clear Canvas</button>
<svg id="shakeRing" width="44" height="44" viewBox="0 0 44 44">
  <circle class="track" cx="22" cy="22" r="18"/>
  <circle class="prog" id="shakeProg" cx="22" cy="22" r="18"
    stroke-dasharray="113" stroke-dashoffset="113" stroke="var(--accent3)"/>
</svg>

<!-- COPYRIGHT -->
<div id="copyright">Created by Mithun AI Lab · <a href="https://www.instagram.com/mithun.ai.lab/" target="_blank">@mithun.ai.lab</a></div>

<!-- MEDIAPIPE -->
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/camera_utils/camera_utils.js" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/drawing_utils/drawing_utils.js" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/hands/hands.js" crossorigin="anonymous"></script>

<script>
// ═══════════════════════════════════════════════════════════
//  HANDY AI — Main Script
//  Created by Mithun AI Lab
//  https://www.instagram.com/mithun.ai.lab/
// ═══════════════════════════════════════════════════════════

const video = document.getElementById('video');
const bgCanvas = document.getElementById('bgCanvas');
const fxCanvas = document.getElementById('fxCanvas');
const drawCanvas = document.getElementById('drawCanvas');
const bgCtx = bgCanvas.getContext('2d');
const fxCtx = fxCanvas.getContext('2d');
const drawCtx = drawCanvas.getContext('2d');

let W = window.innerWidth, H = window.innerHeight;

function resize() {
  W = window.innerWidth; H = window.innerHeight;
  [bgCanvas, fxCanvas, drawCanvas].forEach(c => { c.width = W; c.height = H; });
}
resize();
window.addEventListener('resize', resize);

// ── STATE ──
let currentMode = 'play';
let handsData = [];
let frameCount = 0, fps = 0, lastFpsTime = Date.now();
let particles = [], ripples = [];
let drawPaths = [], activePath = null, penDown = false;
let wristXHistory = [], shakeCount = 0, lastClearTime = 0;
let toastTimer = null;
let humOsc = null, humGain = null, audioCtx = null;
let hueOffset = 0;
let handVelocity = 0, prevWristX = 0;

const ACCENT = { play:'#7cffc4', shapes:'#ff6bca', write:'#6bb5ff' };

// ── AUDIO ──
function initAudio() {
  if (audioCtx) return;
  audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  // hum osc (play mode)
  humOsc = audioCtx.createOscillator();
  humGain = audioCtx.createGain();
  humOsc.type = 'sine';
  humOsc.frequency.value = 80;
  humGain.gain.value = 0;
  humOsc.connect(humGain); humGain.connect(audioCtx.destination);
  humOsc.start();
}
function playZap() {
  if (!audioCtx) return;
  const o = audioCtx.createOscillator(), g = audioCtx.createGain();
  o.type = 'sawtooth'; o.connect(g); g.connect(audioCtx.destination);
  o.frequency.setValueAtTime(700, audioCtx.currentTime);
  o.frequency.exponentialRampToValueAtTime(30, audioCtx.currentTime + 0.12);
  g.gain.setValueAtTime(0.3, audioCtx.currentTime);
  g.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.12);
  o.start(); o.stop(audioCtx.currentTime + 0.12);
}
function playPop() {
  if (!audioCtx) return;
  const o = audioCtx.createOscillator(), g = audioCtx.createGain();
  o.type = 'sine'; o.connect(g); g.connect(audioCtx.destination);
  const f = 300 + Math.random() * 200;
  o.frequency.setValueAtTime(f, audioCtx.currentTime);
  o.frequency.exponentialRampToValueAtTime(f * 0.5, audioCtx.currentTime + 0.08);
  g.gain.setValueAtTime(0.25, audioCtx.currentTime);
  g.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.08);
  o.start(); o.stop(audioCtx.currentTime + 0.1);
}

// ── MODE SWITCHER ──
const modeButtons = document.querySelectorAll('.mode-btn');
modeButtons.forEach(btn => {
  btn.addEventListener('click', () => {
    currentMode = btn.dataset.mode;
    modeButtons.forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    document.documentElement.style.setProperty('--mode-accent', ACCENT[currentMode]);
    updateHelp();
    // update stripe color
    document.getElementById('stripe').style.background = `linear-gradient(90deg, ${ACCENT[currentMode]}, transparent, ${ACCENT[currentMode]})`;
    document.getElementById('clearBtn').style.display = currentMode === 'write' ? 'block' : 'none';
    document.getElementById('shakeRing').style.display = currentMode === 'write' ? 'block' : 'none';
    if (currentMode !== 'play' && humGain) humGain.gain.setTargetAtTime(0, audioCtx.currentTime, 0.1);
  });
});

document.getElementById('clearBtn').addEventListener('click', clearDraw);

function clearDraw() {
  drawPaths = []; activePath = null;
  drawCtx.clearRect(0, 0, W, H);
  playPop(); showToast('🌊 Canvas cleared');
}

// ── HELP TEXT ──
const helpPanel = document.getElementById('helpPanel');
function updateHelp() {
  const tips = {
    play: '<strong>PLAY MODE</strong><br>Pinch thumb+index → ⚡<br>Two hands close → ⚡ lightning<br>Two hands → mandala',
    shapes: '<strong>SHAPES MODE</strong><br>Both hands open + close → ♥<br>Index+middle up → ✌<br>Both fists → 💥 Star Burst',
    write: '<strong>WRITE MODE</strong><br>Index up = draw<br>Pinch = pen up<br>Shake open palm → 🌊 clear'
  };
  helpPanel.innerHTML = tips[currentMode];
}
updateHelp();

// ── TOAST ──
function showToast(msg) {
  const ti = document.getElementById('toastInner');
  ti.textContent = msg;
  ti.classList.add('show');
  if (toastTimer) clearTimeout(toastTimer);
  toastTimer = setTimeout(() => ti.classList.remove('show'), 1600);
}

// ── GESTURE LABEL ──
let currentGesture = '';
function setGesture(g) {
  if (g === currentGesture) return;
  currentGesture = g;
  document.getElementById('gestureLabel').textContent = g || 'Waiting...';
  document.getElementById('activeDot').classList.toggle('show', !!g);
}

// ── MATRIX RAIN ──
const katakana = Array.from({length:96}, (_,i)=>String.fromCharCode(0x30A0+i));
const cols = Math.floor(W / 15) + 1;
const drops = Array.from({length:cols}, ()=>Math.random() * -100);
let bgHue = 0;

function drawMatrixRain() {
  const fade = Math.min(0.18, 0.12 + handVelocity * 0.002);
  bgCtx.globalCompositeOperation = 'destination-out';
  bgCtx.fillStyle = `rgba(0,0,0,${fade})`;
  bgCtx.fillRect(0, 0, W, H);
  bgCtx.globalCompositeOperation = 'source-over';
  bgHue = (bgHue + 0.3) % 360;
  bgCtx.font = '15px DM Mono';

  const speed = 1 + handVelocity * 0.04;
  for (let i = 0; i < drops.length; i++) {
    const char = katakana[Math.floor(Math.random() * katakana.length)];
    const alpha = 0.4 + Math.random() * 0.6;
    bgCtx.fillStyle = `hsla(${(bgHue + i * 3) % 360}, 70%, 70%, ${alpha})`;
    bgCtx.fillText(char, i * 15, drops[i] * 15);
    if (drops[i] * 15 > H && Math.random() > 0.975) drops[i] = 0;
    drops[i] += speed;
  }
}

// ── PARTICLES ──
function spawnParticles(x, y, count, color) {
  for (let i = 0; i < count; i++) {
    const angle = Math.random() * Math.PI * 2;
    const speed = 1 + Math.random() * 4;
    particles.push({ x, y, vx: Math.cos(angle) * speed, vy: Math.sin(angle) * speed - 1, life: 45, maxLife: 45, color, size: 2 + Math.random() * 3 });
  }
}
function spawnSparkle(x, y, hue) {
  particles.push({ x, y, vx: (Math.random()-0.5)*3, vy: Math.random()*-3, life: 30, maxLife: 30, color: `hsl(${hue},100%,75%)`, size: 1.5 });
}

// ── RIPPLES ──
function spawnRipple(x, y, color) {
  ripples.push({ x, y, r: 0, maxR: 80, life: 1, color });
}

// ── LM HELPERS ──
function lmToScreen(lm) { return { x: (1 - lm.x) * W, y: lm.y * H }; }

function dist2D(a, b) { return Math.hypot(a.x - b.x, a.y - b.y); }

function palmSize(lms) {
  const w = lmToScreen(lms[0]), m = lmToScreen(lms[9]);
  return dist2D(w, m) / W;
}

function fingerSpread(lms) {
  const tips = [4,8,12,16,20].map(i=>lmToScreen(lms[i]));
  let total = 0;
  for (let i=0;i<tips.length-1;i++) total += dist2D(tips[i],tips[i+1]);
  return total / W;
}

function isFist(lms) { return fingerSpread(lms) < 0.09; }
function isOpen(lms) { return fingerSpread(lms) > 0.09; }

function isPinch(lms, thresh=0.055) {
  const t = lmToScreen(lms[4]), i = lmToScreen(lms[8]);
  return dist2D(t,i) / W < thresh;
}

function indexExtended(lms) {
  const tip = lmToScreen(lms[8]), wrist = lmToScreen(lms[0]);
  return dist2D(tip,wrist) / W > 0.17;
}

function peaceSign(lms) {
  const i8 = lmToScreen(lms[8]), i12 = lmToScreen(lms[12]);
  const w = lmToScreen(lms[0]);
  const iDist = dist2D(i8, w) / W, mDist = dist2D(i12, w) / W;
  const r16 = dist2D(lmToScreen(lms[16]), w) / W;
  const r20 = dist2D(lmToScreen(lms[20]), w) / W;
  return iDist > 0.18 && mDist > 0.18 && r16 < 0.16 && r20 < 0.16;
}

// ── HAND SKELETON ──
const CONNECTIONS = [
  [0,1],[1,2],[2,3],[3,4],
  [0,5],[5,6],[6,7],[7,8],
  [5,9],[9,10],[10,11],[11,12],
  [9,13],[13,14],[14,15],[15,16],
  [13,17],[17,18],[18,19],[19,20],[0,17]
];
const SEGMENT_COLORS = ['#7cffc4','#7cffc4','#a8ffda','#d0ffe9',
  '#6bb5ff','#8ccbff','#aedaff','#d0ecff',
  '#8b9fff','#7caaff','#6d99ff','#6088ff',
  '#ff6bca','#ff85d5','#ffa0e0','#ffbbeb',
  '#ffaa6b','#ffc08b','#ffd5aa','#ffeacc','#6bb5ff'];

function drawSkeleton(ctx, lms, hueShift, alpha=1) {
  CONNECTIONS.forEach(([a,b],i) => {
    const pa = lmToScreen(lms[a]), pb = lmToScreen(lms[b]);
    const hue = (hueShift + i * 10) % 360;
    ctx.strokeStyle = `hsla(${hue},90%,70%,${alpha})`;
    ctx.lineWidth = 2;
    ctx.shadowBlur = 8; ctx.shadowColor = `hsl(${hue},90%,70%)`;
    ctx.beginPath(); ctx.moveTo(pa.x, pa.y); ctx.lineTo(pb.x, pb.y); ctx.stroke();
  });
  [4,8,12,16,20].forEach(i => {
    const p = lmToScreen(lms[i]);
    const hue = (hueShift + i * 15) % 360;
    ctx.beginPath(); ctx.arc(p.x, p.y, 6, 0, Math.PI*2);
    ctx.fillStyle = `hsla(${hue},100%,75%,${alpha})`;
    ctx.shadowBlur = 16; ctx.shadowColor = `hsl(${hue},100%,70%)`;
    ctx.fill();
  });
  ctx.shadowBlur = 0;
}

// ── LIGHTNING ──
function drawLightning(ctx, x1,y1,x2,y2,color) {
  const segs = 8;
  ctx.strokeStyle = color; ctx.lineWidth = 1.5;
  ctx.shadowBlur = 12; ctx.shadowColor = color;
  ctx.beginPath(); ctx.moveTo(x1,y1);
  const dx=(x2-x1)/segs, dy=(y2-y1)/segs;
  for(let i=1;i<segs;i++){
    ctx.lineTo(x1+dx*i+(Math.random()-0.5)*40, y1+dy*i+(Math.random()-0.5)*40);
  }
  ctx.lineTo(x2,y2); ctx.stroke();
  ctx.shadowBlur = 0;
}

// ── MANDALA ──
function drawMandala(ctx, tips, cx, cy, t) {
  ctx.save(); ctx.translate(cx,cy); ctx.rotate(t*0.01);
  ctx.strokeStyle = 'rgba(255,255,255,0.15)'; ctx.lineWidth = 1;
  tips.forEach(tip => {
    ctx.beginPath(); ctx.moveTo(0,0); ctx.lineTo(tip.x-cx, tip.y-cy); ctx.stroke();
  });
  const r = tips.reduce((acc,tip)=>acc+dist2D({x:cx,y:cy},{x:tip.x,y:tip.y}),0)/tips.length;
  ctx.beginPath(); ctx.arc(0,0,r,0,Math.PI*2);
  ctx.strokeStyle = `hsla(${(t*2)%360},80%,70%,0.2)`; ctx.stroke();
  ctx.restore();
}

// ── HEART ──
function drawHeart(ctx, cx, cy, scale, t) {
  const s = scale * (1 + 0.05 * Math.sin(t * 0.08));
  ctx.save(); ctx.translate(cx,cy); ctx.scale(s,s);
  const grd = ctx.createRadialGradient(0,-30,10,0,0,100);
  grd.addColorStop(0,'rgba(255,180,210,0.95)');
  grd.addColorStop(1,'rgba(255,107,202,0.7)');
  ctx.beginPath();
  for(let i=0;i<Math.PI*2;i+=0.05){
    const x=16*Math.pow(Math.sin(i),3);
    const y=-(13*Math.cos(i)-5*Math.cos(2*i)-2*Math.cos(3*i)-Math.cos(4*i));
    if(i<0.01) ctx.moveTo(x*4,y*4); else ctx.lineTo(x*4,y*4);
  }
  ctx.fillStyle=grd; ctx.fill();
  ctx.strokeStyle='rgba(255,107,202,0.9)'; ctx.lineWidth=2;
  ctx.shadowBlur=30; ctx.shadowColor='#ff6bca'; ctx.stroke();
  // shine
  ctx.beginPath(); ctx.ellipse(-20,-50,12,20,0.5,0,Math.PI*2);
  ctx.fillStyle='rgba(255,255,255,0.25)'; ctx.fill();
  ctx.restore();
}

// ── PEACE ──
function drawPeace(ctx, cx, cy, r) {
  ctx.save(); ctx.translate(cx,cy);
  ctx.strokeStyle='#ff6bca'; ctx.lineWidth=3;
  ctx.shadowBlur=20; ctx.shadowColor='#ff6bca';
  ctx.beginPath(); ctx.arc(0,0,r,0,Math.PI*2); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(0,-r); ctx.lineTo(0,r); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(0,0); ctx.lineTo(-r*Math.cos(Math.PI/6),-r*Math.sin(Math.PI/6)); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(0,0); ctx.lineTo(r*Math.cos(Math.PI/6),-r*Math.sin(Math.PI/6)); ctx.stroke();
  ctx.shadowBlur=0; ctx.restore();
}

// ── STAR ──
function drawStar(ctx, cx, cy, r, t) {
  ctx.save(); ctx.translate(cx,cy); ctx.rotate(t*0.02);
  const pts=8, inner=r*0.4;
  const grd=ctx.createRadialGradient(0,0,0,0,0,r);
  grd.addColorStop(0,'rgba(255,255,255,0.95)');
  grd.addColorStop(0.5,'rgba(255,210,80,0.8)');
  grd.addColorStop(1,'rgba(255,180,50,0)');
  ctx.beginPath();
  for(let i=0;i<pts*2;i++){
    const rad=(i*Math.PI)/pts;
    const rr=i%2===0?r:inner;
    i===0?ctx.moveTo(Math.cos(rad)*rr,Math.sin(rad)*rr):ctx.lineTo(Math.cos(rad)*rr,Math.sin(rad)*rr);
  }
  ctx.closePath();
  ctx.fillStyle=grd; ctx.fill();
  ctx.shadowBlur=40; ctx.shadowColor='rgba(255,210,80,0.8)'; ctx.fill();
  ctx.shadowBlur=0; ctx.restore();
}

// ── PINCH COOLDOWN ──
let lastPinch = [0,0];

// ── RENDER LOOP ──
let frameT = 0;
function renderFrame() {
  requestAnimationFrame(renderFrame);
  frameT++;
  hueOffset = (hueOffset + 1) % 360;

  // FPS
  frameCount++;
  const now = Date.now();
  if (now - lastFpsTime >= 1000) {
    fps = frameCount; frameCount = 0; lastFpsTime = now;
    document.getElementById('fpsLabel').textContent = fps + ' FPS';
    document.getElementById('fpsDot').className = 'chip-dot' + (fps > 5 ? ' green' : '');
    document.getElementById('handCount').textContent = handsData.length;
  }

  drawMatrixRain();
  fxCtx.clearRect(0,0,W,H);
  fxCtx.globalCompositeOperation = 'screen';

  const hands = handsData;
  const numHands = hands.length;

  // velocity tracking
  if (numHands > 0) {
    const wrist = lmToScreen(hands[0].landmarks[0]);
    handVelocity = Math.abs(wrist.x - prevWristX);
    prevWristX = wrist.x;
  } else {
    handVelocity *= 0.9;
  }

  // ─── PLAY MODE ───
  if (currentMode === 'play') {
    hands.forEach((hand, hi) => {
      const lms = hand.landmarks;
      const hue = (hueOffset + hi * 120) % 360;
      drawSkeleton(fxCtx, lms, hue);

      // sparkles from fingertips
      [4,8,12,16,20].forEach(i => {
        if (Math.random() < 0.3) {
          const p = lmToScreen(lms[i]);
          spawnSparkle(p.x, p.y, hue + i * 10);
        }
      });

      // pinch
      if (isPinch(lms) && now - lastPinch[hi] > 500) {
        lastPinch[hi] = now;
        const p = lmToScreen(lms[8]);
        spawnRipple(p.x, p.y, ACCENT.play);
        spawnParticles(p.x, p.y, 20, ACCENT.play);
        playZap(); showToast('⚡ Pinch'); setGesture('⚡ Pinch');
      }
    });

    // two-hand interaction
    if (numHands === 2) {
      const tips0 = [4,8,12,16,20].map(i=>lmToScreen(hands[0].landmarks[i]));
      const tips1 = [4,8,12,16,20].map(i=>lmToScreen(hands[1].landmarks[i]));

      // gradient lines between matching fingertips
      tips0.forEach((t0,i) => {
        const t1 = tips1[i];
        const grd = fxCtx.createLinearGradient(t0.x,t0.y,t1.x,t1.y);
        grd.addColorStop(0,`hsla(${hueOffset%360},90%,70%,0.5)`);
        grd.addColorStop(1,`hsla(${(hueOffset+60)%360},90%,70%,0.5)`);
        fxCtx.strokeStyle=grd; fxCtx.lineWidth=1.5;
        fxCtx.beginPath(); fxCtx.moveTo(t0.x,t0.y); fxCtx.lineTo(t1.x,t1.y); fxCtx.stroke();

        // lightning if close
        if (dist2D(t0,t1) < 140) {
          drawLightning(fxCtx, t0.x,t0.y,t1.x,t1.y,`hsla(${(hueOffset+i*30)%360},100%,80%,0.7)`);
        }
      });

      // mandala
      const allTips = [...tips0,...tips1];
      const cx = allTips.reduce((s,p)=>s+p.x,0)/10;
      const cy = allTips.reduce((s,p)=>s+p.y,0)/10;
      drawMandala(fxCtx, allTips, cx, cy, frameT);

      // hum
      if (audioCtx && humGain) {
        const w0 = lmToScreen(hands[0].landmarks[0]);
        const w1 = lmToScreen(hands[1].landmarks[0]);
        const d = dist2D(w0,w1);
        const freq = 80 + (d / W) * 280;
        humOsc.frequency.setTargetAtTime(freq, audioCtx.currentTime, 0.05);
        humGain.gain.setTargetAtTime(0.08, audioCtx.currentTime, 0.1);
      }
      setGesture('🌀 Two-hand Mandala');
    } else {
      if (audioCtx && humGain) humGain.gain.setTargetAtTime(0, audioCtx.currentTime, 0.1);
      if (numHands === 1) setGesture('👋 Hand Detected');
      else setGesture('');
    }
  }

  // ─── SHAPES MODE ───
  if (currentMode === 'shapes') {
    if (numHands < 2) {
      // check peace with 1 hand
      if (numHands === 1 && peaceSign(hands[0].landmarks)) {
        const wrist = lmToScreen(hands[0].landmarks[0]);
        drawPeace(fxCtx, wrist.x, wrist.y - 80, 70);
        if (currentGesture !== '✌ Peace') { showToast('✌ Peace'); playPop(); }
        setGesture('✌ Peace');
      } else {
        setGesture(numHands === 0 ? '' : '👋 Show 2 hands');
      }
    } else {
      const lms0 = hands[0].landmarks, lms1 = hands[1].landmarks;
      const w0 = lmToScreen(lms0[0]), w1 = lmToScreen(lms1[0]);
      const cx = (w0.x+w1.x)/2, cy = (w0.y+w1.y)/2;
      const d = dist2D(w0,w1)/W;

      const spread0 = fingerSpread(lms0), spread1 = fingerSpread(lms1);

      if (isFist(lms0) && isFist(lms1)) {
        // STAR BURST
        drawStar(fxCtx, cx, cy, 100, frameT);
        if (Math.random()<0.3) spawnParticles(cx,cy,3,'#ffcc44');
        if (currentGesture!=='💥 Star Burst') { showToast('💥 Star Burst!'); playZap(); spawnParticles(cx,cy,25,'#ffcc44'); }
        setGesture('💥 Star Burst');
      } else if (d < 0.35 && spread0 > 0.09 && spread1 > 0.09) {
        // HEART
        drawHeart(fxCtx, cx, cy, 3.5, frameT);
        if (currentGesture!=='♥ Heart') { showToast('♥ Heart'); playPop(); }
        setGesture('♥ Heart');
      } else if (peaceSign(lms0) || peaceSign(lms1)) {
        const pLms = peaceSign(lms0)?lms0:lms1;
        const pw = lmToScreen(pLms[0]);
        drawPeace(fxCtx, pw.x, pw.y-80, 70);
        if (currentGesture!=='✌ Peace') { showToast('✌ Peace'); playPop(); }
        setGesture('✌ Peace');
      } else {
        setGesture('🤲 Ready...');
      }
    }
  }

  // ─── WRITE MODE ───
  if (currentMode === 'write') {
    if (numHands > 0) {
      const lms = hands[0].landmarks;
      const wrist = lmToScreen(lms[0]);
      const indexTip = lmToScreen(lms[8]);
      const pinching = isPinch(lms, 0.06);
      const drawing = indexExtended(lms) && !pinching;

      // shake detection
      wristXHistory.push(wrist.x);
      if (wristXHistory.length > 30) wristXHistory.shift();
      let reversals = 0;
      for (let i=2;i<wristXHistory.length;i++){
        const prev = wristXHistory[i-1]-wristXHistory[i-2];
        const curr = wristXHistory[i]-wristXHistory[i-1];
        if(prev*curr<0 && Math.abs(curr)>5) reversals++;
      }
      const openPalmDraw = fingerSpread(lms) > 0.12;
      const shakeProgress = openPalmDraw ? Math.min(reversals/6, 1) : 0;

      // update ring
      const circ = 2*Math.PI*18;
      const sp = document.getElementById('shakeProg');
      sp.style.strokeDashoffset = circ*(1-shakeProgress);
      const hueP = 220 + shakeProgress*80;
      sp.style.stroke = `hsl(${hueP},100%,65%)`;

      if (reversals >= 6 && openPalmDraw && now - lastClearTime > 2000) {
        lastClearTime = now;
        clearDraw(); wristXHistory = [];
      }

      if (drawing) {
        setGesture('✍ Drawing');
        if (!penDown) {
          penDown = true;
          const hue = (frameT * 2) % 360;
          activePath = { points: [indexTip], hue };
          drawPaths.push(activePath);
        } else if (activePath) {
          activePath.points.push(indexTip);
        }

        // cursor
        fxCtx.beginPath(); fxCtx.arc(indexTip.x, indexTip.y, 8, 0, Math.PI*2);
        const ph = (frameT*3)%360;
        fxCtx.strokeStyle = `hsla(${ph},100%,70%,${0.5+0.5*Math.sin(frameT*0.2)})`;
        fxCtx.lineWidth = 2; fxCtx.shadowBlur = 12; fxCtx.shadowColor = `hsl(${ph},100%,70%)`;
        fxCtx.stroke(); fxCtx.shadowBlur = 0;

      } else {
        if (penDown) { penDown = false; activePath = null; }
        setGesture(pinching ? '✊ Pen Up' : '👆 Point to draw');
      }

      // redraw all paths
      drawCtx.clearRect(0,0,W,H);
      drawPaths.forEach(path => {
        if (path.points.length < 2) return;
        drawCtx.beginPath();
        drawCtx.moveTo(path.points[0].x, path.points[0].y);
        for (let i=1;i<path.points.length-1;i++){
          const mx=(path.points[i].x+path.points[i+1].x)/2;
          const my=(path.points[i].y+path.points[i+1].y)/2;
          drawCtx.quadraticCurveTo(path.points[i].x,path.points[i].y,mx,my);
        }
        const last = path.points[path.points.length-1];
        drawCtx.lineTo(last.x,last.y);
        drawCtx.strokeStyle = `hsl(${path.hue},90%,70%)`;
        drawCtx.lineWidth = 4.5;
        drawCtx.lineCap = 'round'; drawCtx.lineJoin = 'round';
        drawCtx.shadowBlur = 12; drawCtx.shadowColor = `hsl(${path.hue},90%,70%)`;
        drawCtx.stroke();
      });
      drawCtx.shadowBlur = 0;

    } else {
      if (penDown) { penDown = false; activePath = null; }
      setGesture('');
    }

    if (currentMode !== 'write') {
      drawCtx.clearRect(0,0,W,H);
    }
  } else {
    if (currentMode !== 'write') drawCtx.clearRect(0,0,W,H);
  }

  // ─── PARTICLES ───
  particles = particles.filter(p => p.life > 0);
  particles.forEach(p => {
    p.x += p.vx; p.y += p.vy; p.vy += 0.15;
    p.life--;
    const alpha = p.life / p.maxLife;
    const size = p.size * alpha;
    fxCtx.beginPath(); fxCtx.arc(p.x, p.y, size, 0, Math.PI*2);
    fxCtx.fillStyle = p.color.includes('hsl') ? p.color.replace(/[\d.]+\)$/, `${alpha})`) : p.color;
    fxCtx.globalAlpha = alpha;
    fxCtx.fill();
    fxCtx.globalAlpha = 1;
  });

  // ─── RIPPLES ───
  ripples = ripples.filter(r => r.life > 0);
  ripples.forEach(r => {
    r.r += (r.maxR - r.r) * 0.1;
    r.life -= 0.03;
    const a = r.life;
    fxCtx.beginPath(); fxCtx.arc(r.x, r.y, r.r, 0, Math.PI*2);
    fxCtx.strokeStyle = r.color; fxCtx.lineWidth = 2; fxCtx.globalAlpha = a;
    fxCtx.stroke();
    fxCtx.beginPath(); fxCtx.arc(r.x, r.y, r.r*0.6, 0, Math.PI*2);
    fxCtx.stroke();
    fxCtx.globalAlpha = 1;
  });

  fxCtx.globalCompositeOperation = 'source-over';
}

// ── MEDIAPIPE ──
function initMediaPipe() {
  const hands = new Hands({
    locateFile: f => `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${f}`
  });
  hands.setOptions({
    maxNumHands: 2,
    modelComplexity: 1,
    minDetectionConfidence: 0.72,
    minTrackingConfidence: 0.72
  });
  hands.onResults(results => {
    handsData = (results.multiHandLandmarks || []).map((lms,i) => ({
      landmarks: lms, handedness: results.multiHandedness?.[i]?.label || 'Right'
    }));
  });

  const camera = new Camera(video, {
    onFrame: async () => { await hands.send({ image: video }); },
    width: 1920, height: 1080
  });
  camera.start();
}

// ── ENTER BUTTON ──
document.getElementById('enterBtn').addEventListener('click', () => {
  initAudio();
  document.getElementById('splash').classList.add('hide');
  setTimeout(() => { document.getElementById('splash').style.display='none'; }, 900);
  initMediaPipe();
  renderFrame();
});
</script>
</body>
</html>
<address>localaddress</address>
