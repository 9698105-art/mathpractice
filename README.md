# mathpractice
coolmath
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>AbsolutelyMath | Interactive Algebra & Geometry Practice</title>
  <style>
    :root {
      --bg: #0f172a;
      --surface: #1e293b;
      --surface-hover: #334155;
      --accent: #38bdf8;
      --accent-dark: #0284c7;
      --text: #f8fafc;
      --muted: #94a3b8;
      --danger: #ef4444;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; font-family: system-ui, -apple-system, sans-serif; }

    body {
      background-color: var(--bg);
      color: var(--text);
      min-height: 100vh;
      display: flex;
      flex-direction: column;
    }

    /* Header & Controls */
    header {
      background-color: var(--surface);
      padding: 1rem 2rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 1px solid #334155;
      flex-wrap: wrap;
      gap: 1rem;
    }

    .logo {
      font-size: 1.4rem;
      font-weight: 800;
      color: var(--accent);
      letter-spacing: -0.5px;
    }

    .logo span { color: var(--text); }

    .controls {
      display: flex;
      gap: 0.75rem;
      align-items: center;
    }

    input[type="text"] {
      background: var(--bg);
      border: 1px solid #475569;
      color: var(--text);
      padding: 0.5rem 0.9rem;
      border-radius: 6px;
      outline: none;
    }

    input[type="text"]:focus { border-color: var(--accent); }

    button {
      background: var(--accent);
      color: #0f172a;
      border: none;
      padding: 0.5rem 1rem;
      border-radius: 6px;
      font-weight: 700;
      cursor: pointer;
      transition: background 0.2s;
    }

    button:hover { background: #7dd3fc; }

    .panic-btn {
      background: var(--danger);
      color: white;
    }

    .panic-btn:hover { background: #dc2626; }

    /* Category Bar */
    .categories {
      display: flex;
      gap: 0.5rem;
      padding: 1rem 2rem;
      overflow-x: auto;
    }

    .cat-btn {
      background: var(--surface);
      color: var(--muted);
      border: 1px solid #334155;
    }

    .cat-btn.active, .cat-btn:hover {
      background: var(--accent-dark);
      color: white;
      border-color: var(--accent);
    }

    /* Game Grid */
    main {
      padding: 0 2rem 2rem;
      flex: 1;
    }

    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
      gap: 1.25rem;
    }

    .card {
      background: var(--surface);
      border-radius: 10px;
      overflow: hidden;
      border: 1px solid #334155;
      transition: transform 0.2s, border-color 0.2s;
      cursor: pointer;
      display: flex;
      flex-direction: column;
    }

    .card:hover {
      transform: translateY(-4px);
      border-color: var(--accent);
    }

    .thumb {
      height: 130px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 3rem;
      background: linear-gradient(135deg, #1e293b, #0f172a);
    }

    .card-body { padding: 1rem; }
    .card-title { font-weight: 700; font-size: 1.05rem; margin-bottom: 0.25rem; }
    .card-tag {
      font-size: 0.75rem;
      color: var(--accent);
      text-transform: uppercase;
      font-weight: 700;
    }

    /* Game Modal */
    #player-modal {
      display: none;
      position: fixed;
      inset: 0;
      background: rgba(15, 23, 42, 0.95);
      z-index: 50;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 1rem;
    }

    .player-header {
      width: 100%;
      max-width: 640px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 0.75rem;
    }

    canvas {
      background: #020617;
      border: 2px solid var(--accent);
      border-radius: 8px;
      max-width: 100%;
    }

    /* Panic Disguise Screen (Fake Study Notes) */
    #panic-screen {
      display: none;
      position: fixed;
      inset: 0;
      background: #ffffff;
      color: #111827;
      z-index: 100;
      padding: 3rem;
      overflow-y: auto;
    }

    #panic-screen h1 { font-size: 1.8rem; margin-bottom: 1rem; border-bottom: 2px solid #e5e7eb; padding-bottom: 0.5rem; }
    #panic-screen p { margin-bottom: 1rem; line-height: 1.6; max-width: 750px; }
    .formula-box {
      background: #f3f4f6;
      padding: 1rem;
      border-left: 4px solid #2563eb;
      font-family: monospace;
      margin-bottom: 1.5rem;
      max-width: 750px;
    }
  </style>
</head>
<body>

  <!-- Main Header -->
  <header>
    <div class="logo">Absolutely<span>Math</span></div>
    <div class="controls">
      <input type="text" id="search" placeholder="Search modules..." onkeyup="filterGames()" />
      <button class="panic-btn" onclick="togglePanic()" title="Shortcut: Press ESC">Panic (ESC)</button>
    </div>
  </header>

  <!-- Category Filters -->
  <div class="categories">
    <button class="cat-btn active" onclick="setCategory('All', this)">All</button>
    <button class="cat-btn" onclick="setCategory('Arcade', this)">Arcade</button>
    <button class="cat-btn" onclick="setCategory('Puzzle', this)">Puzzle</button>
    <button class="cat-btn" onclick="setCategory('Reflex', this)">Reflex</button>
  </div>

  <!-- Game Cards Grid -->
  <main>
    <div class="grid" id="game-grid"></div>
  </main>

  <!-- Playable Canvas Modal -->
  <div id="player-modal">
    <div class="player-header">
      <h2 id="active-game-title">Game</h2>
      <div>
        <span id="score-display" style="margin-right: 1rem; color: var(--accent); font-weight: bold;">Score: 0</span>
        <button onclick="closeGame()">Close</button>
      </div>
    </div>
    <canvas id="gameCanvas" width="600" height="400"></canvas>
    <p id="game-instructions" style="margin-top: 0.75rem; color: var(--muted); font-size: 0.9rem;"></p>
  </div>

  <!-- Fake Educational Disguise Screen -->
  <div id="panic-screen">
    <h1>Unit 4: Quadratic Functions & Polynomial Factoring</h1>
    <p><strong>Objective:</strong> Students will solve quadratic equations by factoring, completing the square, and applying the quadratic formula.</p>
    <div class="formula-box">
      Standard Form: ax² + bx + c = 0<br>
      Quadratic Formula: x = (-b ± √(b² - 4ac)) / 2a
    </div>
    <p><strong>Practice Problem 1:</strong> Factor the expression x² - 5x + 6 = 0. Identify two integers whose product is 6 and whose sum is -5.</p>
    <p style="color: #6b7280; font-size: 0.85rem; margin-top: 3rem;">Press <strong>ESC</strong> again to return to dashboard.</p>
  </div>

  <script>
    const games = [
      { id: 'snake', title: 'Vector Snake', category: 'Arcade', icon: '🐍', instructions: 'Use Arrow Keys to steer and collect blocks.' },
      { id: 'pong', title: 'Parabola Pong', category: 'Reflex', icon: '🏓', instructions: 'Move your mouse up and down to deflect the ball.' },
      { id: 'clicker', title: ' Prime Target', category: 'Puzzle', icon: '🎯', instructions: 'Click the shrinking targets before they vanish!' },
      { id: 'dodge', title: 'Matrix Dodge', category: 'Reflex', icon: '🚀', instructions: 'Use Left/Right Arrow Keys to dodge falling blocks.' }
    ];

    let currentCategory = 'All';
    let gameLoop = null;
    let isPanic = false;

    const grid = document.getElementById('game-grid');
    const modal = document.getElementById('player-modal');
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const scoreEl = document.getElementById('score-display');

    function renderGrid() {
      const query = document.getElementById('search').value.toLowerCase();
      grid.innerHTML = '';
      games
        .filter(g => (currentCategory === 'All' || g.category === currentCategory) && g.title.toLowerCase().includes(query))
        .forEach(game => {
          const card = document.createElement('div');
          card.className = 'card';
          card.innerHTML = `
            <div class="thumb">${game.icon}</div>
            <div class="card-body">
              <div class="card-tag">${game.category}</div>
              <div class="card-title">${game.title}</div>
            </div>
          `;
          card.onclick = () => launchGame(game);
          grid.appendChild(card);
        });
    }

    function setCategory(cat, btn) {
      currentCategory = cat;
      document.querySelectorAll('.cat-btn').forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
      renderGrid();
    }

    function filterGames() { renderGrid(); }

    /* Panic Key Feature */
    function togglePanic() {
      isPanic = !isPanic;
      document.getElementById('panic-screen').style.display = isPanic ? 'block' : 'none';
      document.title = isPanic ? 'Algebra II - Unit 4 Notes' : 'AbsolutelyMath | Interactive Algebra & Geometry Practice';
      if (isPanic) closeGame();
    }

    window.addEventListener('keydown', (e) => {
      if (e.key === 'Escape') togglePanic();
    });

    /* Game Engine */
    function closeGame() {
      clearInterval(gameLoop);
      cancelAnimationFrame(gameLoop);
      modal.style.display = 'none';
      canvas.onmousemove = null;
      canvas.onclick = null;
    }

    function launchGame(game) {
      closeGame();
      document.getElementById('active-game-title').innerText = game.title;
      document.getElementById('game-instructions').innerText = game.instructions;
      modal.style.display = 'flex';
      let score = 0;
      scoreEl.innerText = 'Score: 0';

      if (game.id === 'snake') startSnake();
      if (game.id === 'pong') startPong();
      if (game.id === 'clicker') startClicker();
      if (game.id === 'dodge') startDodge();
    }

    // 1. Snake
    function startSnake() {
      let snake = [{ x: 10, y: 10 }], food = { x: 15, y: 15 }, dx = 1, dy = 0, score = 0;
      document.onkeydown = (e) => {
