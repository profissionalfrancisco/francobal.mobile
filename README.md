<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, maximum-scale=1.0">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="mobile-web-app-capable" content="yes">
<title>Francobol Mobile</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Roboto:wght@400;700&display=swap');

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

  body {
    background: #0a0a0a;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    font-family: 'Roboto', sans-serif;
    overflow: hidden;
    touch-action: none;
    -webkit-user-select: none;
    user-select: none;
  }

  #game-container {
    position: relative;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
  }

  canvas {
    display: block;
    width: 100%;
    height: 100%;
    background: #1a5f1a;
  }

  /* Scoreboard */
  #scoreboard {
    position: absolute;
    top: 8px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 12px;
    padding: 6px 16px;
    background: rgba(0,0,0,0.5);
    backdrop-filter: blur(8px);
    border-radius: 20px;
    border: 1px solid rgba(255,255,255,0.1);
    pointer-events: none;
    z-index: 10;
  }

  .team-display {
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .team-color {
    width: 14px; height: 14px;
    border-radius: 50%;
    border: 1.5px solid rgba(255,255,255,0.5);
    box-shadow: 0 0 4px currentColor;
  }

  .team-name {
    color: white;
    font-family: 'Orbitron', sans-serif;
    font-size: 10px;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 1px;
  }

  .score {
    font-family: 'Orbitron', sans-serif;
    font-size: 24px;
    font-weight: 900;
    color: #fff;
    text-shadow: 0 0 10px rgba(255,255,255,0.4);
    min-width: 30px;
    text-align: center;
  }

  .vs {
    font-family: 'Orbitron', sans-serif;
    font-size: 10px;
    color: #888;
    font-weight: 700;
  }

  #match-info {
    position: absolute;
    top: 48px;
    left: 50%;
    transform: translateX(-50%);
    font-family: 'Orbitron', sans-serif;
    font-size: 9px;
    color: #ffd700;
    background: rgba(0,0,0,0.4);
    padding: 2px 10px;
    border-radius: 10px;
    letter-spacing: 1px;
    pointer-events: none;
    z-index: 10;
  }

  #goal-message {
    position: absolute;
    top: 40%;
    left: 50%;
    transform: translate(-50%, -50%) scale(0);
    font-family: 'Orbitron', sans-serif;
    font-size: 42px;
    font-weight: 900;
    color: #ffd700;
    text-shadow: 0 0 20px rgba(255,215,0,0.8);
    z-index: 20;
    transition: transform 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    pointer-events: none;
    white-space: nowrap;
  }

  #goal-message.show {
    transform: translate(-50%, -50%) scale(1);
  }

  #countdown {
    position: absolute;
    top: 45%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-family: 'Orbitron', sans-serif;
    font-size: 60px;
    font-weight: 900;
    color: #fff;
    text-shadow: 0 0 20px rgba(255,255,255,0.5);
    z-index: 18;
    pointer-events: none;
    display: none;
  }

  #player-indicator {
    position: absolute;
    top: 48px;
    left: 12px;
    color: white;
    font-size: 10px;
    background: rgba(0,0,0,0.5);
    padding: 4px 10px;
    border-radius: 6px;
    pointer-events: none;
    z-index: 10;
  }

  /* Touch Controls */
  #touch-controls {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 180px;
    pointer-events: none;
    z-index: 15;
  }

  /* Joystick */
  #joystick-zone {
    position: absolute;
    bottom: 20px;
    left: 20px;
    width: 140px;
    height: 140px;
    pointer-events: all;
  }

  #joystick-base {
    position: absolute;
    width: 120px;
    height: 120px;
    border-radius: 50%;
    background: rgba(255,255,255,0.1);
    border: 2px solid rgba(255,255,255,0.2);
    top: 10px;
    left: 10px;
  }

  #joystick-knob {
    position: absolute;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    background: rgba(0, 212, 255, 0.6);
    border: 2px solid rgba(255,255,255,0.4);
    top: 45px;
    left: 45px;
    box-shadow: 0 0 15px rgba(0, 212, 255, 0.4);
    transition: none;
  }

  #joystick-knob.active {
    background: rgba(0, 212, 255, 0.9);
    box-shadow: 0 0 25px rgba(0, 212, 255, 0.7);
  }

  /* Action Buttons */
  #action-buttons {
    position: absolute;
    bottom: 20px;
    right: 20px;
    display: flex;
    flex-direction: column;
    gap: 12px;
    align-items: flex-end;
    pointer-events: all;
  }

  .action-btn {
    width: 70px;
    height: 70px;
    border-radius: 50%;
    border: 2px solid rgba(255,255,255,0.3);
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: 'Orbitron', sans-serif;
    font-size: 11px;
    font-weight: 700;
    color: white;
    cursor: pointer;
    -webkit-touch-callout: none;
    touch-action: none;
  }

  .action-btn:active {
    transform: scale(0.92);
    opacity: 0.8;
  }

  #btn-kick {
    background: rgba(255, 51, 102, 0.6);
    box-shadow: 0 0 15px rgba(255, 51, 102, 0.3);
    font-size: 13px;
  }

  #btn-kick:active {
    background: rgba(255, 51, 102, 0.9);
    box-shadow: 0 0 25px rgba(255, 51, 102, 0.6);
  }

  #btn-switch {
    background: rgba(255, 215, 0, 0.5);
    box-shadow: 0 0 15px rgba(255, 215, 0, 0.3);
    width: 55px;
    height: 55px;
    font-size: 9px;
  }

  #btn-switch:active {
    background: rgba(255, 215, 0, 0.8);
    box-shadow: 0 0 20px rgba(255, 215, 0, 0.5);
  }

  #btn-sprint {
    background: rgba(0, 255, 136, 0.4);
    box-shadow: 0 0 15px rgba(0, 255, 136, 0.2);
    width: 55px;
    height: 55px;
    font-size: 9px;
  }

  #btn-sprint:active, #btn-sprint.pressed {
    background: rgba(0, 255, 136, 0.8);
    box-shadow: 0 0 20px rgba(0, 255, 136, 0.5);
  }

  /* Start Screen */
  #start-screen {
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    background: rgba(0,0,0,0.88);
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    z-index: 30;
    pointer-events: all;
    padding: 20px;
  }

  #start-screen h1 {
    font-family: 'Orbitron', sans-serif;
    font-size: 42px;
    color: #00ff88;
    text-shadow: 0 0 30px rgba(0,255,136,0.5);
    margin-bottom: 8px;
    text-align: center;
  }

  #start-screen p {
    color: #aaa;
    font-size: 14px;
    margin-bottom: 25px;
    text-align: center;
  }

  #start-btn {
    font-family: 'Orbitron', sans-serif;
    font-size: 18px;
    padding: 14px 40px;
    background: #00ff88;
    color: #000;
    border: none;
    border-radius: 40px;
    cursor: pointer;
    font-weight: 700;
    transition: all 0.3s;
    box-shadow: 0 0 25px rgba(0,255,136,0.4);
    -webkit-tap-highlight-color: transparent;
  }

  #start-btn:active {
    transform: scale(0.95);
    box-shadow: 0 0 40px rgba(0,255,136,0.7);
  }

  .mobile-controls-demo {
    margin-top: 20px;
    display: flex;
    gap: 30px;
    align-items: center;
  }

  .demo-joystick {
    width: 80px;
    height: 80px;
    border-radius: 50%;
    background: rgba(255,255,255,0.1);
    border: 2px solid rgba(255,255,255,0.2);
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
  }

  .demo-joystick::after {
    content: '';
    width: 30px;
    height: 30px;
    border-radius: 50%;
    background: rgba(0, 212, 255, 0.6);
    border: 2px solid rgba(255,255,255,0.4);
  }

  .demo-btn {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    background: rgba(255, 51, 102, 0.6);
    border: 2px solid rgba(255,255,255,0.3);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 10px;
    color: white;
    font-family: 'Orbitron', sans-serif;
  }

  .demo-label {
    color: #888;
    font-size: 11px;
    margin-top: 8px;
    text-align: center;
  }

  /* Settings */
  #settings-btn-top {
    position: absolute;
    top: 8px;
    right: 12px;
    width: 32px;
    height: 32px;
    border-radius: 50%;
    background: rgba(0,0,0,0.5);
    border: 1px solid rgba(255,255,255,0.3);
    color: white;
    font-size: 16px;
    cursor: pointer;
    pointer-events: all;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 15;
    -webkit-tap-highlight-color: transparent;
  }

  #settings-panel {
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    background: rgba(0,0,0,0.9);
    display: none;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    z-index: 25;
    pointer-events: all;
    padding: 20px;
  }

  #settings-panel h2 {
    font-family: 'Orbitron', sans-serif;
    font-size: 24px;
    color: #00ff88;
    margin-bottom: 20px;
  }

  #settings-panel .controls-list {
    color: #ccc;
    font-size: 14px;
    line-height: 2.2;
    text-align: center;
    margin-bottom: 25px;
  }

  #close-settings {
    font-family: 'Orbitron', sans-serif;
    font-size: 14px;
    padding: 12px 35px;
    background: #00ff88;
    color: #000;
    border: none;
    border-radius: 25px;
    cursor: pointer;
    font-weight: 700;
    -webkit-tap-highlight-color: transparent;
  }

  #close-settings:active {
    transform: scale(0.95);
  }

  #game-over-screen {
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    background: rgba(0,0,0,0.85);
    display: none;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    z-index: 28;
    pointer-events: all;
  }

  #game-over-screen h2 {
    font-family: 'Orbitron', sans-serif;
    font-size: 36px;
    color: #ffd700;
    margin-bottom: 15px;
  }

  #game-over-screen p {
    color: #aaa;
    font-size: 16px;
    margin-bottom: 25px;
  }

  #restart-btn {
    font-family: 'Orbitron', sans-serif;
    font-size: 16px;
    padding: 12px 35px;
    background: #00ff88;
    color: #000;
    border: none;
    border-radius: 25px;
    cursor: pointer;
    font-weight: 700;
    -webkit-tap-highlight-color: transparent;
  }
</style>
<base target="_blank">
</head>
<body>

<div id="game-container">
  <canvas id="gameCanvas"></canvas>

  <!-- UI Overlay -->
  <div id="scoreboard">
    <div class="team-display">
      <div class="team-color" style="background:#00d4ff;"></div>
      <span class="team-name">AZUL</span>
    </div>
    <span class="score" id="score-blue">0</span>
    <span class="vs">VS</span>
    <span class="score" id="score-red">0</span>
    <div class="team-display">
      <span class="team-name">VERMELHO</span>
      <div class="team-color" style="background:#ff3366;"></div>
    </div>
  </div>

  <div id="match-info">PRIMEIRO A 3 GOLS</div>
  <div id="goal-message">GOOOOL!</div>
  <div id="countdown"></div>
  <div id="player-indicator">Jogador: <span id="player-name">Zagueiro</span></div>
  <button id="settings-btn-top">&#9881;</button>

  <!-- Touch Controls -->
  <div id="touch-controls">
    <div id="joystick-zone">
      <div id="joystick-base"></div>
      <div id="joystick-knob"></div>
    </div>

    <div id="action-buttons">
      <div class="action-btn" id="btn-sprint">RUN</div>
      <div class="action-btn" id="btn-switch">TROCAR</div>
      <div class="action-btn" id="btn-kick">CHUTAR</div>
    </div>
  </div>

  <!-- Settings Panel -->
  <div id="settings-panel">
    <h2>FRANCOBOL MOBILE</h2>
    <div class="controls-list">
      <b>Joystick esquerdo</b> — Mover jogador<br>
      <b>CHUTAR</b> — Chutar / Passar / Roubar<br>
      <b>RUN</b> — Correr (segure)<br>
      <b>TROCAR</b> — Trocar de jogador<br><br>
      O controle vai automaticamente<br>para quem pegar a bola!
    </div>
    <button id="close-settings">VOLTAR AO JOGO</button>
  </div>

  <!-- Start Screen -->
  <div id="start-screen">
    <h1>FRANCOBOL</h1>
    <p>Arena 4x4 — Primeiro a 3 Gols</p>
    <button id="start-btn">JOGAR AGORA</button>
    <div class="mobile-controls-demo">
      <div>
        <div class="demo-joystick"></div>
        <div class="demo-label">Mover</div>
      </div>
      <div>
        <div class="demo-btn">CHUTAR</div>
        <div class="demo-label">Ação</div>
      </div>
    </div>
  </div>

  <!-- Game Over Screen -->
  <div id="game-over-screen">
    <h2 id="winner-text">AZUL VENCEU!</h2>
    <p>Placar final: <span id="final-score">0 - 0</span></p>
    <button id="restart-btn">JOGAR NOVAMENTE</button>
  </div>
</div>

<script>
// ==================== CONFIGURAÇÕES ====================
const CFG = {
  W: 1100, H: 700,
  PR: 13, BR: 7,
  PS: 2.6, SS: 3.8,
  BF: 0.97, PF: 0.78,
  KP: 7.0, KR: 28,
  GW: 130, GD: 35,
  WB: 0.5,
  WIN: 3,
  GRT: 90,
  MIN_PLAYER_DIST: 32,
  DRIBBLE_DIST: 16,
};

// ==================== VETOR ====================
class V2 {
  constructor(x, y) { this.x = x; this.y = y; }
  add(v) { return new V2(this.x + v.x, this.y + v.y); }
  sub(v) { return new V2(this.x - v.x, this.y - v.y); }
  mult(s) { return new V2(this.x * s, this.y * s); }
  mag() { return Math.sqrt(this.x*this.x + this.y*this.y); }
  norm() { const m = this.mag(); return m > 0.001 ? new V2(this.x/m, this.y/m) : new V2(0,0); }
  dist(v) { return Math.sqrt((this.x-v.x)**2 + (this.y-v.y)**2); }
  lim(max) { const m = this.mag(); return m > max ? this.norm().mult(max) : new V2(this.x,this.y); }
  copy() { return new V2(this.x, this.y); }
}

function clamp(v, a, b) { return Math.max(a, Math.min(b, v)); }

// ==================== BOLA ====================
class Ball {
  constructor(x, y) {
    this.pos = new V2(x, y);
    this.vel = new V2(0, 0);
    this.r = CFG.BR;
    this.trail = [];
    this.owner = null;
  }

  update() {
    if (this.owner) {
      const off = new V2(Math.cos(this.owner.angle)*(this.owner.r+this.r+3), Math.sin(this.owner.angle)*(this.owner.r+this.r+3));
      this.pos = this.owner.pos.add(off);
      this.vel = this.owner.vel.copy();
      return;
    }
    this.pos = this.pos.add(this.vel);
    this.vel = this.vel.mult(CFG.BF);
    if (this.vel.mag() > 2) {
      this.trail.push({x:this.pos.x, y:this.pos.y, a:1, r:this.r});
    }
    if (this.trail.length > 10) this.trail.shift();
    for (let t of this.trail) { t.a *= 0.88; t.r *= 0.95; }
    this.trail = this.trail.filter(t => t.a > 0.05);
    if (this.vel.mag() < 0.06) this.vel = new V2(0,0);
  }

  draw(ctx) {
    for (let t of this.trail) {
      ctx.beginPath(); ctx.arc(t.x, t.y, t.r, 0, Math.PI*2);
      ctx.fillStyle = `rgba(255,255,200,${t.a*0.25})`; ctx.fill();
    }
    ctx.beginPath();
    ctx.ellipse(this.pos.x+1, this.pos.y+2, this.r*1.1, this.r*0.8, 0, 0, Math.PI*2);
    ctx.fillStyle = 'rgba(0,0,0,0.3)'; ctx.fill();
    ctx.beginPath(); ctx.arc(this.pos.x, this.pos.y, this.r, 0, Math.PI*2);
    const g = ctx.createRadialGradient(this.pos.x-2, this.pos.y-2, 1, this.pos.x, this.pos.y, this.r);
    g.addColorStop(0, '#fff'); g.addColorStop(0.7, '#f0f0f0'); g.addColorStop(1, '#ccc');
    ctx.fillStyle = g; ctx.fill();
    ctx.strokeStyle = '#bbb'; ctx.lineWidth = 1; ctx.stroke();
    ctx.beginPath(); ctx.arc(this.pos.x-2, this.pos.y-2, 2.5, 0, Math.PI*2);
    ctx.fillStyle = '#333'; ctx.fill();
  }

  release() { this.owner = null; }
}

// ==================== JOGADOR ====================
class Player {
  constructor(x, y, team, role, name) {
    this.pos = new V2(x, y);
    this.vel = new V2(0, 0);
    this.team = team;
    this.role = role;
    this.name = name;
    this.r = CFG.PR;
    this.color = team === 'blue' ? '#00d4ff' : '#ff3366';
    this.dark = team === 'blue' ? '#0088aa' : '#cc2244';
    this.controlled = false;
    this.angle = 0;
    this.anim = 0;
    this.kickCD = 0;
    this.home = new V2(x, y);
  }

  update() {
    this.vel = this.vel.mult(CFG.PF);
    this.pos = this.pos.add(this.vel);
    this.pos.x = clamp(this.pos.x, this.r+8, CFG.W-this.r-8);
    this.pos.y = clamp(this.pos.y, this.r+8, CFG.H-this.r-8);
    if (this.vel.mag() > 0.3) this.anim += 0.12;
    else this.anim = 0;
    if (this.vel.mag() > 0.2) this.angle = Math.atan2(this.vel.y, this.vel.x);
    if (this.kickCD > 0) this.kickCD--;
  }

  move(dir, speed) {
    if (dir.mag() > 0) {
      dir = dir.norm();
      this.vel = this.vel.add(dir.mult(speed * 0.18));
      this.vel = this.vel.lim(speed);
    }
  }

  moveToward(target, speed) {
    const dir = target.sub(this.pos);
    if (dir.mag() > 1) {
      this.vel = this.vel.add(dir.norm().mult(speed * 0.15));
      this.vel = this.vel.lim(speed);
    }
  }

  draw(ctx) {
    const x = this.pos.x, y = this.pos.y, r = this.r;
    ctx.beginPath();
    ctx.ellipse(x+2, y+3, r*1.2, r*0.9, 0, 0, Math.PI*2);
    ctx.fillStyle = 'rgba(0,0,0,0.2)'; ctx.fill();
    ctx.beginPath(); ctx.arc(x, y, r, 0, Math.PI*2);
    const g = ctx.createRadialGradient(x-3, y-3, 3, x, y, r);
    g.addColorStop(0, this.color); g.addColorStop(0.8, this.dark); g.addColorStop(1, this.dark);
    ctx.fillStyle = g; ctx.fill();
    ctx.strokeStyle = 'rgba(255,255,255,0.5)'; ctx.lineWidth = 1.5; ctx.stroke();
    const er = r*0.45;
    const ex = x + Math.cos(this.angle)*er;
    const ey = y + Math.sin(this.angle)*er;
    ctx.beginPath(); ctx.arc(ex, ey, 3.5, 0, Math.PI*2);
    ctx.fillStyle = 'rgba(255,255,255,0.95)'; ctx.fill();
    ctx.beginPath(); ctx.arc(ex+Math.cos(this.angle), ey+Math.sin(this.angle), 1.5, 0, Math.PI*2);
    ctx.fillStyle = '#111'; ctx.fill();
    ctx.fillStyle = 'rgba(255,255,255,0.7)';
    ctx.font = 'bold 8px Roboto';
    ctx.textAlign = 'center'; ctx.textBaseline = 'middle';
    const rm = {goalkeeper:'G', defender:'D', midfielder:'M', forward:'A'};
    ctx.fillText(rm[this.role] || '?', x, y+0.5);
    if (this.controlled) {
      const pulse = 1 + Math.sin(Date.now()*0.005)*0.15;
      ctx.beginPath(); ctx.arc(x, y, (r+5)*pulse, 0, Math.PI*2);
      ctx.strokeStyle = '#ffd700'; ctx.lineWidth = 2.5;
      ctx.setLineDash([5,4]); ctx.stroke(); ctx.setLineDash([]);
      const ay = y - r - 10 - Math.sin(Date.now()*0.008)*3;
      ctx.beginPath(); ctx.moveTo(x, ay); ctx.lineTo(x-7, ay-10); ctx.lineTo(x+7, ay-10); ctx.closePath();
      ctx.fillStyle = '#ffd700'; ctx.fill();
      ctx.fillStyle = 'rgba(0,0,0,0.6)';
      ctx.fillRect(x-35, y+r+6, 70, 16);
      ctx.fillStyle = '#ffd700';
      ctx.font = 'bold 10px Roboto';
      ctx.fillText(this.name, x, y+r+14);
    }
  }
}

// ==================== IA ====================
class AI {
  think(p, ball, all) {
    if (p.controlled) return;
    const myGoal = p.team === 'blue' ? new V2(30, CFG.H/2) : new V2(CFG.W-30, CFG.H/2);
    const enemyGoal = p.team === 'blue' ? new V2(CFG.W-30, CFG.H/2) : new V2(30, CFG.H/2);
    const dBall = p.pos.dist(ball.pos);
    const dMyGoal = ball.pos.dist(myGoal);
    const dEnemyGoal = ball.pos.dist(enemyGoal);
    const myTeamHas = ball.owner && ball.owner.team === p.team;
    const enemyHas = ball.owner && ball.owner.team !== p.team;
    let target, speed = CFG.PS;

    if (p.role === 'goalkeeper') {
      const gx = p.team === 'blue' ? 45 : CFG.W-45;
      const gt = CFG.H/2 - CFG.GW/2 + 15;
      const gb = CFG.H/2 + CFG.GW/2 - 15;
      const coverage = clamp((ball.pos.y - CFG.H/2) / 200, -1, 1);
      target = new V2(gx, CFG.H/2 + coverage*(CFG.GW/2-20));
      if (dMyGoal < 200 && ball.vel.mag() > 1) {
        const approach = ball.pos.add(myGoal.sub(ball.pos).norm().mult(30));
        target = new V2(clamp(approach.x, gx-20, gx+20), clamp(approach.y, gt, gb));
        speed = CFG.SS;
      }
      if (dBall < 50 && dMyGoal < 180) { target = ball.pos; speed = CFG.SS*1.2; }
      target.x = p.team === 'blue' ? clamp(target.x, 20, 120) : clamp(target.x, CFG.W-120, CFG.W-20);
      target.y = clamp(target.y, gt-10, gb+10);
    }
    else if (p.role === 'defender') {
      const dl = p.team === 'blue' ? 180 : CFG.W-180;
      if (dMyGoal < 300 && !myTeamHas) { target = ball.pos; speed = CFG.SS; }
      else if (myTeamHas) { target = new V2(dl+(p.team==='blue'?80:-80), ball.pos.y); speed = CFG.PS*0.7; }
      else { target = new V2(dl+(p.team==='blue'?-60:60), CFG.H/2+(ball.pos.y-CFG.H/2)*0.4); }
    }
    else if (p.role === 'midfielder') {
      if (dBall < 90) { target = ball.pos; speed = CFG.SS; }
      else if (myTeamHas) { target = new V2(ball.pos.x+(p.team==='blue'?100:-100), ball.pos.y+(p.pos.y>ball.pos.y?60:-60)); }
      else if (enemyHas) { target = new V2(ball.pos.x+(p.team==='blue'?-60:60), ball.pos.y); speed = CFG.SS*0.9; }
      else { target = new V2(CFG.W/2+(ball.pos.x-CFG.W/2)*0.3, ball.pos.y+Math.sin(Date.now()*0.001+p.pos.x)*50); }
    }
    else if (p.role === 'forward') {
      if (dBall < 80) { target = ball.pos; speed = CFG.SS; }
      else if (myTeamHas) { target = new V2(enemyGoal.x+(p.team==='blue'?-80:80), CFG.H/2+Math.sin(Date.now()*0.003+p.pos.x*0.1)*80); }
      else if (dEnemyGoal < 350) { target = ball.pos; speed = CFG.SS; }
      else { target = new V2(enemyGoal.x+(p.team==='blue'?-150:150), CFG.H/2+(p.pos.y>CFG.H/2?80:-80)); }
    }

    const mates = all.filter(pl => pl.team === p.team && pl !== p);
    for (let m of mates) {
      if (p.pos.dist(m.pos) < 35) {
        target = target.add(p.pos.sub(m.pos).norm().mult(3));
      }
    }
    target.x = clamp(target.x, 30, CFG.W-30);
    target.y = clamp(target.y, 30, CFG.H-30);
    p.moveToward(target, speed);
  }

  shouldKick(p, ball) {
    if (p.kickCD > 0) return false;
    return p.pos.dist(ball.pos) <= CFG.KR + 3;
  }

  kickDir(p, ball) {
    const eg = p.team === 'blue' ? new V2(CFG.W-20, CFG.H/2) : new V2(20, CFG.H/2);
    const toGoal = eg.sub(ball.pos).norm();
    if (p.role === 'goalkeeper') return new V2(p.team==='blue'?1:-1, (Math.random()-0.5)*0.4).norm();
    const dist = ball.pos.dist(eg);
    const acc = clamp(1-dist/500, 0.3, 1);
    const noise = (Math.random()-0.5)*(1-acc)*0.8;
    return toGoal.add(new V2(noise, noise*0.6)).norm();
  }
}

// ==================== TOUCH CONTROLS ====================
class TouchControls {
  constructor(game) {
    this.game = game;
    this.joystickActive = false;
    this.joystickCenter = {x: 0, y: 0};
    this.joystickPos = {x: 0, y: 0};
    this.joystickDir = new V2(0, 0);
    this.sprintPressed = false;

    this.setupJoystick();
    this.setupButtons();
  }

  setupJoystick() {
    const zone = document.getElementById('joystick-zone');
    const knob = document.getElementById('joystick-knob');
    const base = document.getElementById('joystick-base');

    const startJoystick = (e) => {
      e.preventDefault();
      const touch = e.touches ? e.touches[0] : e;
      const rect = zone.getBoundingClientRect();
      this.joystickCenter = {
        x: rect.left + rect.width / 2,
        y: rect.top + rect.height / 2
      };
      this.joystickActive = true;
      knob.classList.add('active');
      this.updateJoystick(touch);
    };

    const moveJoystick = (e) => {
      if (!this.joystickActive) return;
      e.preventDefault();
      const touch = e.touches ? e.touches[0] : e;
      this.updateJoystick(touch);
    };

    const endJoystick = (e) => {
      e.preventDefault();
      this.joystickActive = false;
      knob.classList.remove('active');
      knob.style.left = '45px';
      knob.style.top = '45px';
      this.joystickDir = new V2(0, 0);
    };

    zone.addEventListener('touchstart', startJoystick, {passive: false});
    zone.addEventListener('touchmove', moveJoystick, {passive: false});
    zone.addEventListener('touchend', endJoystick, {passive: false});
    zone.addEventListener('touchcancel', endJoystick, {passive: false});
  }

  updateJoystick(touch) {
    const knob = document.getElementById('joystick-knob');
    const maxDist = 45;

    let dx = touch.clientX - this.joystickCenter.x;
    let dy = touch.clientY - this.joystickCenter.y;
    const dist = Math.sqrt(dx*dx + dy*dy);

    if (dist > maxDist) {
      dx = (dx / dist) * maxDist;
      dy = (dy / dist) * maxDist;
    }

    knob.style.left = (45 + dx) + 'px';
    knob.style.top = (45 + dy) + 'px';

    // Normalize direction (-1 to 1)
    this.joystickDir = new V2(dx / maxDist, dy / maxDist);
  }

  setupButtons() {
    const kickBtn = document.getElementById('btn-kick');
    const switchBtn = document.getElementById('btn-switch');
    const sprintBtn = document.getElementById('btn-sprint');

    // Kick button
    const kickStart = (e) => {
      e.preventDefault();
      if (this.game.state === 'playing') {
        this.game.playerAction();
      }
    };

    kickBtn.addEventListener('touchstart', kickStart, {passive: false});
    kickBtn.addEventListener('mousedown', kickStart);

    // Switch button
    const switchStart = (e) => {
      e.preventDefault();
      if (this.game.state === 'playing') {
        this.game.switchPlayerManual();
      }
    };

    switchBtn.addEventListener('touchstart', switchStart, {passive: false});
    switchBtn.addEventListener('mousedown', switchStart);

    // Sprint button
    const sprintStart = (e) => {
      e.preventDefault();
      this.sprintPressed = true;
      sprintBtn.classList.add('pressed');
    };

    const sprintEnd = (e) => {
      e.preventDefault();
      this.sprintPressed = false;
      sprintBtn.classList.remove('pressed');
    };

    sprintBtn.addEventListener('touchstart', sprintStart, {passive: false});
    sprintBtn.addEventListener('touchend', sprintEnd, {passive: false});
    sprintBtn.addEventListener('touchcancel', sprintEnd, {passive: false});
    sprintBtn.addEventListener('mousedown', sprintStart);
    sprintBtn.addEventListener('mouseup', sprintEnd);
    sprintBtn.addEventListener('mouseleave', sprintEnd);
  }

  getInput() {
    return {
      dir: this.joystickDir,
      sprint: this.sprintPressed
    };
  }
}

// ==================== JOGO ====================
class Game {
  constructor() {
    this.cv = document.getElementById('gameCanvas');
    this.ctx = this.cv.getContext('2d');
    this.resizeCanvas();

    this.keys = {};
    this.ball = new Ball(CFG.W/2, CFG.H/2);
    this.players = [];
    this.ai = new AI();
    this.score = {blue:0, red:0};
    this.running = false;
    this.paused = false;
    this.goalCD = 0;
    this.gameOver = false;
    this.winner = null;
    this.effects = [];
    this.lastT = 0;
    this.state = 'playing';
    this.celebrateTimer = 0;
    this.countdownValue = 0;
    this.lastControlled = null;
    this.touchControls = null;

    this.setupPlayers();
    this.setupEvents();
    this.setupTouch();
    this.drawField();

    window.addEventListener('resize', () => this.resizeCanvas());

    document.getElementById('start-btn').addEventListener('click', () => {
      document.getElementById('start-screen').style.display = 'none';
      this.running = true;
      this.lastT = performance.now();
      requestAnimationFrame(t => this.loop(t));
    });

    document.getElementById('restart-btn').addEventListener('click', () => {
      location.reload();
    });

    const settingsBtn = document.getElementById('settings-btn-top');
    const settingsPanel = document.getElementById('settings-panel');
    const closeSettings = document.getElementById('close-settings');

    settingsBtn.addEventListener('click', () => {
      this.paused = true;
      settingsPanel.style.display = 'flex';
    });

    closeSettings.addEventListener('click', () => {
      settingsPanel.style.display = 'none';
      this.paused = false;
      this.lastT = performance.now();
    });
  }

  resizeCanvas() {
    const container = document.getElementById('game-container');
    const w = container.clientWidth;
    const h = container.clientHeight;
    this.cv.width = CFG.W;
    this.cv.height = CFG.H;
    this.cv.style.width = w + 'px';
    this.cv.style.height = h + 'px';
    this.scaleX = w / CFG.W;
    this.scaleY = h / CFG.H;
  }

  setupTouch() {
    this.touchControls = new TouchControls(this);
  }

  setupPlayers() {
    const bp = [
      {x:50, y:CFG.H/2, role:'goalkeeper', name:'Goleiro'},
      {x:200, y:CFG.H/2-100, role:'defender', name:'Zagueiro'},
      {x:380, y:CFG.H/2, role:'midfielder', name:'Meia'},
      {x:520, y:CFG.H/2-80, role:'forward', name:'Atacante'},
    ];
    const rp = [
      {x:CFG.W-50, y:CFG.H/2, role:'goalkeeper', name:'Goleiro'},
      {x:CFG.W-200, y:CFG.H/2+100, role:'defender', name:'Zagueiro'},
      {x:CFG.W-380, y:CFG.H/2, role:'midfielder', name:'Meia'},
      {x:CFG.W-520, y:CFG.H/2+80, role:'forward', name:'Atacante'},
    ];
    bp.forEach((p,i) => {
      const pl = new Player(p.x, p.y, 'blue', p.role, p.name);
      if (i===1) pl.controlled = true;
      this.players.push(pl);
    });
    rp.forEach((p,i) => {
      const pl = new Player(p.x, p.y, 'red', p.role, p.name);
      this.players.push(pl);
    });
    this.updateIndicator();
  }

  setupEvents() {
    window.addEventListener('keydown', e => {
      this.keys[e.key.toLowerCase()] = true;
      if (e.key === ' ' && this.running && !this.paused && this.state === 'playing') {
        e.preventDefault();
        this.playerAction();
      }
      if (e.key === 'Tab' && this.running && !this.gameOver && this.state === 'playing') {
        e.preventDefault();
        this.switchPlayerManual();
      }
    });
    window.addEventListener('keyup', e => {
      this.keys[e.key.toLowerCase()] = false;
    });
  }

  switchPlayerManual() {
    const bp = this.players.filter(p => p.team === 'blue');
    const cur = bp.findIndex(p => p.controlled);
    if (cur === -1) return;
    bp[cur].controlled = false;
    const n = (cur + 1) % bp.length;
    bp[n].controlled = true;
    this.updateIndicator();
  }

  autoSwitchOnBallPickup() {
    if (this.ball.owner && this.ball.owner.team === 'blue') {
      const current = this.players.find(p => p.controlled);
      if (current !== this.ball.owner) {
        if (current) current.controlled = false;
        this.ball.owner.controlled = true;
        this.updateIndicator();
      }
    }
  }

  autoSwitchOnLooseBall() {
    if (this.ball.owner) return;

    const bp = this.players.filter(p => p.team === 'blue');
    const current = bp.find(p => p.controlled);
    if (!current) return;

    if (current.pos.dist(this.ball.pos) < 120) return;

    let nearest = null;
    let nearestDist = Infinity;
    for (let p of bp) {
      const d = p.pos.dist(this.ball.pos);
      if (d < nearestDist) {
        nearestDist = d;
        nearest = p;
      }
    }

    if (nearest && nearest !== current && nearestDist < 180) {
      current.controlled = false;
      nearest.controlled = true;
      this.updateIndicator();
    }
  }

  updateIndicator() {
    const cur = this.players.find(p => p.controlled);
    document.getElementById('player-name').textContent = cur ? cur.name : 'Zagueiro';
  }

  playerAction() {
    const p = this.players.find(p => p.controlled);
    if (!p || p.kickCD > 0) return;

    const d = p.pos.dist(this.ball.pos);

    if (d <= CFG.KR + 10) {
      const dir = this.getKickDir(p);
      const power = this.touchControls && this.touchControls.sprintPressed ? CFG.KP * 1.5 : CFG.KP;

      this.ball.release();
      this.ball.vel = dir.mult(power);
      p.kickCD = 15;
      this.effects.push({x:this.ball.pos.x, y:this.ball.pos.y, f:15, mf:15});
      return;
    }

    const toBall = this.ball.pos.sub(p.pos);
    if (toBall.mag() > 5) {
      p.vel = p.vel.add(toBall.norm().mult(5));
      p.kickCD = 25;
    }
  }

  getKickDir(p) {
    if (p.vel.mag() > 0.5) {
      return p.vel.norm();
    }
    const eg = new V2(CFG.W - 20, CFG.H/2);
    return eg.sub(this.ball.pos).norm();
  }

  handleInput() {
    if (this.state !== 'playing') return;
    const p = this.players.find(p => p.controlled);
    if (!p) return;

    let dir = new V2(0, 0);
    let speed = CFG.PS;

    // Touch controls
    if (this.touchControls) {
      const touchInput = this.touchControls.getInput();
      dir = touchInput.dir;
      if (touchInput.sprint) speed = CFG.SS;
    }

    // Keyboard fallback
    if (this.keys['w'] || this.keys['arrowup']) dir.y -= 1;
    if (this.keys['s'] || this.keys['arrowdown']) dir.y += 1;
    if (this.keys['a'] || this.keys['arrowleft']) dir.x -= 1;
    if (this.keys['d'] || this.keys['arrowright']) dir.x += 1;
    if (this.keys['shift']) speed = CFG.SS;

    p.move(dir, speed);

    if (!this.ball.owner && p.pos.dist(this.ball.pos) < p.r + this.ball.r + CFG.DRIBBLE_DIST) {
      if (dir.mag() > 0.1) {
        this.ball.owner = p;
      }
    }
  }

  separatePlayers() {
    for (let i = 0; i < this.players.length; i++) {
      for (let j = i + 1; j < this.players.length; j++) {
        const p1 = this.players[i];
        const p2 = this.players[j];
        const d = p1.pos.dist(p2.pos);
        const minDist = CFG.MIN_PLAYER_DIST;

        if (d < minDist && d > 0.1) {
          const n = p2.pos.sub(p1.pos).norm();
          const push = (minDist - d) * 0.5;
          p1.pos = p1.pos.sub(n.mult(push));
          p2.pos = p2.pos.add(n.mult(push));
          p1.vel = p1.vel.sub(n.mult(0.3));
          p2.vel = p2.vel.add(n.mult(0.3));
        }
      }
    }
  }

  resolveCollisions() {
    for (let p of this.players) {
      const d = p.pos.dist(this.ball.pos);
      const md = p.r + this.ball.r + 2;
      if (d < md && d > 0 && !this.ball.owner) {
        const n = this.ball.pos.sub(p.pos).norm();
        const ov = md - d;
        this.ball.pos = this.ball.pos.add(n.mult(ov*0.6));
        p.pos = p.pos.sub(n.mult(ov*0.4));
        const rv = this.ball.vel.sub(p.vel);
        const vn = rv.x*n.x + rv.y*n.y;
        if (vn < 0) {
          const iv = n.mult(-(1+0.6)*vn/2.5);
          this.ball.vel = this.ball.vel.add(iv);
          p.vel = p.vel.sub(iv.mult(0.2));
        }
      }
    }
    for (let i=0; i<this.players.length; i++) {
      for (let j=i+1; j<this.players.length; j++) {
        const p1 = this.players[i], p2 = this.players[j];
        const d = p1.pos.dist(p2.pos);
        const md = p1.r + p2.r + 4;
        if (d < md && d > 0) {
          const n = p2.pos.sub(p1.pos).norm();
          const ov = md - d;
          p1.pos = p1.pos.sub(n.mult(ov*0.5));
          p2.pos = p2.pos.add(n.mult(ov*0.5));
          const rv = p2.vel.sub(p1.vel);
          const vn = rv.x*n.x + rv.y*n.y;
          if (vn < 0) {
            const iv = n.mult(-vn*0.4);
            p1.vel = p1.vel.sub(iv);
            p2.vel = p2.vel.add(iv);
          }
        }
      }
    }
  }

  checkGoals() {
    if (this.state !== 'playing') return;
    const gt = CFG.H/2 - CFG.GW/2;
    const gb = CFG.H/2 + CFG.GW/2;
    let scoringTeam = null;
    if (this.ball.pos.x < CFG.GD && this.ball.pos.y > gt && this.ball.pos.y < gb) {
      scoringTeam = 'red';
    } else if (this.ball.pos.x > CFG.W-CFG.GD && this.ball.pos.y > gt && this.ball.pos.y < gb) {
      scoringTeam = 'blue';
    }
    if (scoringTeam) {
      this.triggerGoal(scoringTeam);
    }
  }

  triggerGoal(team) {
    this.score[team]++;
    document.getElementById('score-blue').textContent = this.score.blue;
    document.getElementById('score-red').textContent = this.score.red;

    const msg = document.getElementById('goal-message');
    msg.textContent = team === 'blue' ? 'GOOOOL AZUL!' : 'GOOOOL VERMELHO!';
    msg.style.color = team === 'blue' ? '#00d4ff' : '#ff3366';
    msg.classList.add('show');

    this.state = 'celebrating';
    this.celebrateTimer = 90;

    if (this.score.blue >= CFG.WIN || this.score.red >= CFG.WIN) {
      this.gameOver = true;
      this.winner = this.score.blue >= CFG.WIN ? 'AZUL' : 'VERMELHO';
    }
  }

  startCountdown() {
    this.state = 'countdown';
    this.countdownValue = 3;
    const cd = document.getElementById('countdown');
    cd.style.display = 'block';
    cd.textContent = '3';

    const interval = setInterval(() => {
      this.countdownValue--;
      if (this.countdownValue > 0) {
        cd.textContent = this.countdownValue.toString();
      } else if (this.countdownValue === 0) {
        cd.textContent = 'VAI!';
      } else {
        clearInterval(interval);
        cd.style.display = 'none';
        this.state = 'playing';
      }
    }, 800);
  }

  resetPositions(scoringTeam) {
    const teamThatConceded = scoringTeam === 'blue' ? 'red' : 'blue';

    this.ball.pos = new V2(CFG.W/2, CFG.H/2);
    this.ball.vel = new V2(0,0);
    this.ball.release();

    const bp = [{x:50,y:CFG.H/2},{x:200,y:CFG.H/2-100},{x:380,y:CFG.H/2},{x:520,y:CFG.H/2-80}];
    const rp = [{x:CFG.W-50,y:CFG.H/2},{x:CFG.W-200,y:CFG.H/2+100},{x:CFG.W-380,y:CFG.H/2},{x:CFG.W-520,y:CFG.H/2+80}];
    const bpl = this.players.filter(p => p.team === 'blue');
    const rpl = this.players.filter(p => p.team === 'red');
    bpl.forEach((p,i) => { p.pos = new V2(bp[i].x, bp[i].y); p.vel = new V2(0,0); });
    rpl.forEach((p,i) => { p.pos = new V2(rp[i].x, rp[i].y); p.vel = new V2(0,0); });

    const concedingPlayers = this.players.filter(p => p.team === teamThatConceded);
    const kickoffPlayer = concedingPlayers.find(p => p.role === 'midfielder') || 
                          concedingPlayers.find(p => p.role === 'forward') ||
                          concedingPlayers[0];

    if (kickoffPlayer) {
      kickoffPlayer.pos = new V2(CFG.W/2 + (teamThatConceded === 'blue' ? -30 : 30), CFG.H/2);
      this.ball.owner = kickoffPlayer;
    }

    this.autoSwitchOnBallPickup();
  }

  checkBoundaries() {
    const b = this.ball;
    const gt = CFG.H/2 - CFG.GW/2;
    const gb = CFG.H/2 + CFG.GW/2;
    if (b.pos.y < b.r) { b.pos.y = b.r; b.vel.y = Math.abs(b.vel.y)*CFG.WB; }
    if (b.pos.y > CFG.H-b.r) { b.pos.y = CFG.H-b.r; b.vel.y = -Math.abs(b.vel.y)*CFG.WB; }
    if (b.pos.x < b.r) {
      if (b.pos.y < gt || b.pos.y > gb) { b.pos.x = b.r; b.vel.x = Math.abs(b.vel.x)*CFG.WB; }
    }
    if (b.pos.x > CFG.W-b.r) {
      if (b.pos.y < gt || b.pos.y > gb) { b.pos.x = CFG.W-b.r; b.vel.x = -Math.abs(b.vel.x)*CFG.WB; }
    }
  }

  aiTick() {
    if (this.state !== 'playing') return;
    for (let p of this.players) {
      if (p.controlled) continue;
      this.ai.think(p, this.ball, this.players);
      if (this.ai.shouldKick(p, this.ball)) {
        const dir = this.ai.kickDir(p, this.ball);
        const power = p.role === 'goalkeeper' ? 10 : CFG.KP*0.85;
        this.ball.release();
        this.ball.vel = dir.mult(power);
        p.kickCD = 20;
        this.effects.push({x:this.ball.pos.x, y:this.ball.pos.y, f:12, mf:12});
      }
      if (!this.ball.owner && p.pos.dist(this.ball.pos) < p.r + this.ball.r + 8) {
        const moving = p.vel.mag() > 0.5;
        const towardEnemy = (p.team === 'blue' && p.vel.x > 0) || (p.team === 'red' && p.vel.x < 0);
        if (moving && (towardEnemy || p.vel.mag() < 1)) this.ball.owner = p;
      }
    }
  }

  drawField() {
    const ctx = this.ctx, w = CFG.W, h = CFG.H;
    ctx.fillStyle = '#1a5f1a'; ctx.fillRect(0,0,w,h);
    ctx.fillStyle = 'rgba(255,255,255,0.025)';
    for (let i=0; i<w; i+=90) ctx.fillRect(i,0,45,h);
    ctx.strokeStyle = 'rgba(255,255,255,0.35)'; ctx.lineWidth = 3;
    ctx.strokeRect(3,3,w-6,h-6);
    ctx.beginPath(); ctx.moveTo(w/2,0); ctx.lineTo(w/2,h);
    ctx.strokeStyle = 'rgba(255,255,255,0.4)'; ctx.lineWidth = 2.5; ctx.stroke();
    ctx.beginPath(); ctx.arc(w/2,h/2,65,0,Math.PI*2); ctx.stroke();
    ctx.beginPath(); ctx.arc(w/2,h/2,4,0,Math.PI*2); ctx.fillStyle = 'rgba(255,255,255,0.6)'; ctx.fill();
    const gh = CFG.GW/2;
    ctx.fillStyle = 'rgba(0,150,255,0.12)'; ctx.fillRect(0,h/2-gh,CFG.GD,CFG.GW);
    ctx.strokeStyle = 'rgba(0,200,255,0.4)'; ctx.lineWidth = 2.5;
    ctx.strokeRect(0,h/2-gh,CFG.GD,CFG.GW);
    ctx.fillStyle = 'rgba(255,50,80,0.12)'; ctx.fillRect(w-CFG.GD,h/2-gh,CFG.GD,CFG.GW);
    ctx.strokeStyle = 'rgba(255,80,100,0.4)'; ctx.strokeRect(w-CFG.GD,h/2-gh,CFG.GD,CFG.GW);
    ctx.strokeStyle = 'rgba(255,255,255,0.12)'; ctx.lineWidth = 1;
    for (let i=0; i<CFG.GW; i+=12) {
      ctx.beginPath(); ctx.moveTo(0,h/2-gh+i); ctx.lineTo(CFG.GD,h/2-gh+i); ctx.stroke();
      ctx.beginPath(); ctx.moveTo(w-CFG.GD,h/2-gh+i); ctx.lineTo(w,h/2-gh+i); ctx.stroke();
    }
    for (let i=0; i<CFG.GD; i+=10) {
      ctx.beginPath(); ctx.moveTo(i,h/2-gh); ctx.lineTo(i,h/2+gh); ctx.stroke();
      ctx.beginPath(); ctx.moveTo(w-i,h/2-gh); ctx.lineTo(w-i,h/2+gh); ctx.stroke();
    }
    ctx.strokeStyle = 'rgba(255,255,255,0.22)'; ctx.lineWidth = 2;
    ctx.strokeRect(0,h/2-140,150,280); ctx.strokeRect(w-150,h/2-140,150,280);
    ctx.beginPath(); ctx.arc(0,0,20,0,Math.PI/2); ctx.stroke();
    ctx.beginPath(); ctx.arc(w,0,20,Math.PI/2,Math.PI); ctx.stroke();
    ctx.beginPath(); ctx.arc(0,h,20,-Math.PI/2,0); ctx.stroke();
    ctx.beginPath(); ctx.arc(w,h,20,Math.PI,-Math.PI/2); ctx.stroke();
  }

  drawEffects() {
    const ctx = this.ctx;
    for (let i=this.effects.length-1; i>=0; i--) {
      const e = this.effects[i];
      const prog = 1-(e.f/e.mf);
      const r = prog*25;
      const a = e.f/e.mf;
      ctx.beginPath(); ctx.arc(e.x, e.y, r, 0, Math.PI*2);
      ctx.strokeStyle = `rgba(255,255,200,${a*0.6})`; ctx.lineWidth = 2; ctx.stroke();
      e.f--; if (e.f <= 0) this.effects.splice(i,1);
    }
  }

  showGameOver() {
    const screen = document.getElementById('game-over-screen');
    const winnerText = document.getElementById('winner-text');
    const finalScore = document.getElementById('final-score');
    winnerText.textContent = `${this.winner} VENCEU!`;
    winnerText.style.color = this.winner === 'AZUL' ? '#00d4ff' : '#ff3366';
    finalScore.textContent = `${this.score.blue} - ${this.score.red}`;
    screen.style.display = 'flex';
  }

  loop(t) {
    if (!this.running) return;
    this.lastT = t;

    if (this.state === 'playing') {
      if (!this.paused) {
        this.handleInput();
        this.autoSwitchOnBallPickup();
        this.autoSwitchOnLooseBall();
        this.aiTick();
        this.separatePlayers();
        this.ball.update();
        for (let p of this.players) p.update();
        this.resolveCollisions();
        this.checkBoundaries();
        this.checkGoals();
      }
    } else if (this.state === 'celebrating') {
      this.celebrateTimer--;
      for (let p of this.players) {
        p.vel = p.vel.mult(0.9);
        p.update();
      }
      this.ball.update();
      this.resolveCollisions();
      this.checkBoundaries();
      if (this.celebrateTimer <= 0) {
        document.getElementById('goal-message').classList.remove('show');
        if (this.gameOver) {
          this.showGameOver();
          this.state = 'gameover';
          return;
        }
        const scoringTeam = this.score.blue > this.score.red ? 'blue' : 'red';
        this.resetPositions(scoringTeam);
        this.startCountdown();
      }
    } else if (this.state === 'countdown') {
      for (let p of this.players) p.update();
    }

    this.ctx.clearRect(0,0,CFG.W,CFG.H);
    this.drawField();
    const sorted = [...this.players].sort((a,b) => a.pos.y - b.pos.y);
    for (let p of sorted) p.draw(this.ctx);
    this.ball.draw(this.ctx);
    this.drawEffects();

    if (this.paused && document.getElementById('settings-panel').style.display !== 'flex') {
      this.ctx.fillStyle = 'rgba(0,0,0,0.6)'; this.ctx.fillRect(0,0,CFG.W,CFG.H);
      this.ctx.fillStyle = '#fff'; this.ctx.font = 'bold 36px Orbitron';
      this.ctx.textAlign = 'center'; this.ctx.fillText('PAUSADO', CFG.W/2, CFG.H/2);
      this.ctx.font = '16px Roboto'; this.ctx.fillStyle = '#aaa';
      this.ctx.fillText('Toque para continuar', CFG.W/2, CFG.H/2+30);
    }

    requestAnimationFrame(t => this.loop(t));
  }
}

window.onload = () => new Game();
</script>
</body>
</html>
