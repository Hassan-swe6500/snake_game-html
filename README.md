# snake_game-html

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="theme-color" content="#0b0f14">
  <title>Snake — Neon Arcade</title>
  <style>
    :root {
      color-scheme: dark;
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      font-synthesis: none;
      text-rendering: optimizeLegibility;
      --page: #0b0f14;
      --surface: #111820;
      --surface-raised: #151e28;
      --canvas: #0a1118;
      --line: #24313d;
      --text: #edf5f7;
      --muted: #82919e;
      --green: #8cff6b;
      --pink: #ff5ca8;
      --cyan: #55e7f2;
      --shadow: 0 28px 80px rgb(0 0 0 / 38%);
    }

    @media (prefers-color-scheme: light) {
      :root {
        --page: #10151b;
        --surface: #151d25;
        --surface-raised: #1b2630;
        --canvas: #101a20;
        --line: #2b3945;
        --text: #edf5f7;
        --muted: #93a1aa;
        --shadow: 0 28px 80px rgb(0 0 0 / 32%);
      }
    }

    * { box-sizing: border-box; }

    body {
      min-width: 320px;
      min-height: 100vh;
      margin: 0;
      padding: 48px 20px 32px;
      background:
        radial-gradient(ellipse at 50% 0%, color-mix(in srgb, var(--cyan) 8%, transparent), transparent 42rem),
        var(--page);
      color: var(--text);
      font-size: 15px;
      line-height: 1.5;
    }

    button { font: inherit; }

    .app {
      width: min(100%, 760px);
      margin: 0 auto;
    }

    .eyebrow {
      display: flex;
      align-items: center;
      gap: 9px;
      margin: 0 0 8px;
      color: var(--muted);
      font-size: 12px;
      font-weight: 700;
      letter-spacing: .14em;
      text-transform: uppercase;
    }

    .eyebrow-mark {
      width: 8px;
      height: 8px;
      border-radius: 50%;
      background: var(--green);
      box-shadow: 0 0 12px color-mix(in srgb, var(--green) 75%, transparent);
    }

    h1 {
      margin: 0;
      font-size: clamp(30px, 7vw, 42px);
      font-weight: 750;
      letter-spacing: -.055em;
      line-height: 1.08;
    }

    .subtitle {
      margin: 10px 0 26px;
      color: var(--muted);
      font-size: 15px;
    }

    .game-card {
      overflow: hidden;
      padding: 18px;
      border: 1px solid var(--line);
      border-radius: 18px;
      background: var(--surface);
      box-shadow: var(--shadow);
    }

    .scorebar {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 12px;
      padding: 0 2px 16px;
    }

    .scores {
      display: flex;
      gap: 28px;
    }

    .score-label {
      display: block;
      margin-bottom: 2px;
      color: var(--muted);
      font-size: 11px;
      font-weight: 700;
      letter-spacing: .1em;
      text-transform: uppercase;
    }

    .score-value {
      font-size: 21px;
      font-weight: 750;
      font-variant-numeric: tabular-nums;
      letter-spacing: -.035em;
    }

    .score-value.current { color: var(--green); }

    .speed-badge {
      display: inline-flex;
      align-items: center;
      gap: 7px;
      padding: 7px 10px;
      border: 1px solid var(--line);
      border-radius: 999px;
      color: var(--muted);
      font-size: 12px;
      font-weight: 650;
      white-space: nowrap;
    }

    .speed-dot {
      width: 7px;
      height: 7px;
      border-radius: 50%;
      background: var(--cyan);
      box-shadow: 0 0 10px color-mix(in srgb, var(--cyan) 70%, transparent);
    }

    .board-wrap {
      position: relative;
      overflow: hidden;
      border: 1px solid #273740;
      border-radius: 11px;
      background: var(--canvas);
      isolation: isolate;
    }

    canvas {
      display: block;
      width: 100%;
      aspect-ratio: 3 / 2;
      touch-action: none;
    }

    .game-overlay {
      position: absolute;
      inset: 0;
      z-index: 2;
      display: grid;
      place-items: center;
      padding: 20px;
      background: rgb(5 10 14 / 58%);
      text-align: center;
      backdrop-filter: blur(5px);
      transition: opacity 180ms ease;
    }

    .game-overlay[hidden] { display: none; }

    .overlay-content { max-width: 320px; }

    .overlay-kicker {
      margin: 0 0 6px;
      color: var(--green);
      font-size: 11px;
      font-weight: 750;
      letter-spacing: .15em;
      text-transform: uppercase;
    }

    .overlay-title {
      margin: 0;
      color: #f3fbfc;
      font-size: clamp(26px, 6vw, 34px);
      font-weight: 750;
      letter-spacing: -.045em;
      line-height: 1.15;
    }

    .overlay-copy {
      margin: 9px 0 20px;
      color: #afbec4;
      font-size: 14px;
    }

    .primary-button {
      min-height: 44px;
      padding: 0 20px;
      border: 0;
      border-radius: 9px;
      background: var(--green);
      color: #10200e;
      cursor: pointer;
      font-size: 14px;
      font-weight: 750;
      box-shadow: 0 0 24px color-mix(in srgb, var(--green) 20%, transparent);
      transition: transform 120ms ease, filter 120ms ease;
    }

    .primary-button:hover { filter: brightness(1.08); transform: translateY(-1px); }
    .primary-button:active { transform: translateY(0); }
    button:focus-visible { outline: 3px solid var(--cyan); outline-offset: 3px; }

    .game-footer {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 12px;
      padding: 15px 2px 0;
      color: var(--muted);
      font-size: 12px;
    }

    .keys { display: flex; align-items: center; gap: 5px; }
    kbd {
      min-width: 23px;
      padding: 2px 5px;
      border: 1px solid var(--line);
      border-radius: 5px;
      background: var(--surface-raised);
      color: var(--text);
      font-family: inherit;
      font-size: 11px;
      text-align: center;
    }

    .mobile-controls {
      display: none;
      grid-template: repeat(2, 42px) / repeat(3, 48px);
      justify-content: center;
      gap: 5px;
      margin: 16px auto 0;
    }

    .direction-button {
      border: 1px solid var(--line);
      border-radius: 9px;
      background: var(--surface-raised);
      color: var(--text);
      cursor: pointer;
      font-size: 18px;
      touch-action: manipulation;
    }

    .direction-button[data-dir="up"] { grid-area: 1 / 2; }
    .direction-button[data-dir="left"] { grid-area: 2 / 1; }
    .direction-button[data-dir="down"] { grid-area: 2 / 2; }
    .direction-button[data-dir="right"] { grid-area: 2 / 3; }

    @media (pointer: coarse), (max-width: 540px) {
      body { padding: 30px 14px 24px; }
      .game-card { padding: 13px; }
      .mobile-controls { display: grid; }
      .game-footer { align-items: flex-start; }
      .keys { display: none; }
    }

    @media (prefers-reduced-motion: reduce) {
      *, *::before, *::after { scroll-behavior: auto !important; transition-duration: .01ms !important; }
    }
  </style>
</head>
<body>
  <main class="app">
    <p class="eyebrow"><span class="eyebrow-mark" aria-hidden="true"></span>Neon arcade</p>
    <h1>Snake</h1>
    <p class="subtitle">A little focus. A lot of fruit.</p>

    <section class="game-card" aria-label="Snake game">
      <div class="scorebar">
        <div class="scores" aria-live="polite">
          <div>
            <span class="score-label">Score</span>
            <span class="score-value current" id="score">0</span>
          </div>
          <div>
            <span class="score-label">Best</span>
            <span class="score-value" id="best">0</span>
          </div>
        </div>
        <span class="speed-badge"><span class="speed-dot" aria-hidden="true"></span><span id="speed">Chill</span></span>
      </div>

      <div class="board-wrap">
        <canvas id="game" aria-label="Snake game board. Use the arrow keys or WASD to move."></canvas>
        <div class="game-overlay" id="overlay">
          <div class="overlay-content">
            <p class="overlay-kicker" id="overlay-kicker">Ready when you are</p>
            <h2 class="overlay-title" id="overlay-title">Make your move</h2>
            <p class="overlay-copy" id="overlay-copy">Collect the glowing orbs. Don't run into yourself.</p>
            <button class="primary-button" id="start-button" type="button">Start game</button>
          </div>
        </div>
      </div>

      <div class="mobile-controls" aria-label="Touch controls">
        <button class="direction-button" data-dir="up" type="button" aria-label="Move up">↑</button>
        <button class="direction-button" data-dir="left" type="button" aria-label="Move left">←</button>
        <button class="direction-button" data-dir="down" type="button" aria-label="Move down">↓</button>
        <button class="direction-button" data-dir="right" type="button" aria-label="Move right">→</button>
      </div>

      <div class="game-footer">
        <span>Eat, grow, and beat your best.</span>
        <span class="keys"><kbd>←</kbd><kbd>↑</kbd><kbd>↓</kbd><kbd>→</kbd><span>or</span><kbd>W</kbd><kbd>A</kbd><kbd>S</kbd><kbd>D</kbd></span>
        <span>Space to pause</span>
      </div>
    </section>
  </main>

  <script>
    (() => {
      const canvas = document.querySelector("#game");
      const context = canvas.getContext("2d");
      const scoreElement = document.querySelector("#score");
      const bestElement = document.querySelector("#best");
      const speedElement = document.querySelector("#speed");
      const overlay = document.querySelector("#overlay");
      const overlayKicker = document.querySelector("#overlay-kicker");
      const overlayTitle = document.querySelector("#overlay-title");
      const overlayCopy = document.querySelector("#overlay-copy");
      const startButton = document.querySelector("#start-button");
      const columns = 24;
      const rows = 16;
      const directions = {
        ArrowUp: { x: 0, y: -1 },
        w: { x: 0, y: -1 },
        ArrowDown: { x: 0, y: 1 },
        s: { x: 0, y: 1 },
        ArrowLeft: { x: -1, y: 0 },
        a: { x: -1, y: 0 },
        ArrowRight: { x: 1, y: 0 },
        d: { x: 1, y: 0 }
      };
      const storedBest = Number(localStorage.getItem("neon-snake-best")) || 0;

      let snake;
      let food;
      let direction;
      let nextDirection;
      let score = 0;
      let best = storedBest;
      let state = "ready";
      let lastFrame = 0;
      let stepTime = 155;
      let accumulator = 0;
      let boardWidth = 0;
      let boardHeight = 0;

      bestElement.textContent = best;

      function resetGame() {
        snake = [
          { x: 10, y: 8 },
          { x: 9, y: 8 },
          { x: 8, y: 8 },
          { x: 7, y: 8 }
        ];
        direction = { x: 1, y: 0 };
        nextDirection = direction;
        score = 0;
        stepTime = 155;
        accumulator = 0;
        scoreElement.textContent = score;
        updateSpeed();
        food = placeFood();
        draw();
      }

      function placeFood() {
        const emptyCells = [];
        for (let y = 0; y < rows; y += 1) {
          for (let x = 0; x < columns; x += 1) {
            if (!snake.some((segment) => segment.x === x && segment.y === y)) {
              emptyCells.push({ x, y });
            }
          }
        }
        return emptyCells[Math.floor(Math.random() * emptyCells.length)];
      }

      function updateSpeed() {
        const level = Math.floor(score / 3);
        stepTime = Math.max(62, 155 - level * 12);
        speedElement.textContent = level === 0 ? "Chill" : `Level ${level + 1}`;
      }

      function showOverlay(mode) {
        overlay.hidden = false;
        if (mode === "ready") {
          overlayKicker.textContent = "Ready when you are";
          overlayTitle.textContent = "Make your move";
          overlayCopy.textContent = "Collect the glowing orbs. Don't run into yourself.";
          startButton.textContent = "Start game";
        } else if (mode === "paused") {
          overlayKicker.textContent = "Taking a breather";
          overlayTitle.textContent = "Game paused";
          overlayCopy.textContent = "Your run is right where you left it.";
          startButton.textContent = "Keep playing";
        } else {
          overlayKicker.textContent = "Run complete";
          overlayTitle.textContent = "Game over";
          overlayCopy.textContent = `You scored ${score} ${score === 1 ? "point" : "points"}. Ready for another round?`;
          startButton.textContent = "Play again";
        }
      }

      function startGame() {
        if (state === "gameover") resetGame();
        state = "running";
        overlay.hidden = true;
        lastFrame = 0;
        accumulator = 0;
        requestAnimationFrame(frame);
      }

      function setDirection(newDirection) {
        if (state === "ready") startGame();
        if (state !== "running") return;
        if (newDirection.x === -direction.x && newDirection.y === -direction.y) return;
        nextDirection = newDirection;
      }

      function endGame() {
        state = "gameover";
        showOverlay("gameover");
        draw();
      }

      function advance() {
        direction = nextDirection;
        const head = { x: snake[0].x + direction.x, y: snake[0].y + direction.y };
        const eating = head.x === food.x && head.y === food.y;
        const bodyToCheck = eating ? snake : snake.slice(0, -1);

        if (
          head.x < 0 || head.x >= columns || head.y < 0 || head.y >= rows ||
          bodyToCheck.some((segment) => segment.x === head.x && segment.y === head.y)
        ) {
          endGame();
          return;
        }

        snake.unshift(head);
        if (eating) {
          score += 1;
          scoreElement.textContent = score;
          if (score > best) {
            best = score;
            bestElement.textContent = best;
            localStorage.setItem("neon-snake-best", String(best));
          }
          updateSpeed();
          food = placeFood();
          if (!food) {
            endGame();
            return;
          }
        } else {
          snake.pop();
        }
        draw();
      }

      function roundedRect(x, y, width, height, radius) {
        context.beginPath();
        context.roundRect(x, y, width, height, radius);
        context.fill();
      }

      function draw(timestamp = 0) {
        if (!boardWidth || !boardHeight) return;
        const cellWidth = boardWidth / columns;
        const cellHeight = boardHeight / rows;

        context.clearRect(0, 0, boardWidth, boardHeight);
        context.fillStyle = "#0a1118";
        context.fillRect(0, 0, boardWidth, boardHeight);

        context.strokeStyle = "rgba(118, 159, 171, 0.075)";
        context.lineWidth = 1;
        context.beginPath();
        for (let x = 0; x <= columns; x += 1) {
          const px = Math.round(x * cellWidth) + 0.5;
          context.moveTo(px, 0);
          context.lineTo(px, boardHeight);
        }
        for (let y = 0; y <= rows; y += 1) {
          const py = Math.round(y * cellHeight) + 0.5;
          context.moveTo(0, py);
          context.lineTo(boardWidth, py);
        }
        context.stroke();

        if (food) {
          const pulse = 1 + Math.sin(timestamp / 180) * 0.08;
          const fx = (food.x + 0.5) * cellWidth;
          const fy = (food.y + 0.5) * cellHeight;
          context.save();
          context.shadowColor = "#ff5ca8";
          context.shadowBlur = 18;
          context.fillStyle = "#ff5ca8";
          context.beginPath();
          context.arc(fx, fy, Math.min(cellWidth, cellHeight) * 0.29 * pulse, 0, Math.PI * 2);
          context.fill();
          context.restore();
          context.fillStyle = "#ffe0ef";
          context.beginPath();
          context.arc(fx - cellWidth * 0.07, fy - cellHeight * 0.08, Math.min(cellWidth, cellHeight) * 0.07, 0, Math.PI * 2);
          context.fill();
        }

        snake.forEach((segment, index) => {
          const inset = index === 0 ? 2.1 : 2.7;
          const x = segment.x * cellWidth + inset;
          const y = segment.y * cellHeight + inset;
          context.save();
          context.fillStyle = index === 0 ? "#b5ff91" : `hsl(${118 - Math.min(index, 12) * 2} 94% ${index < 4 ? 61 : 51}%)`;
          if (index === 0) {
            context.shadowColor = "#8cff6b";
            context.shadowBlur = 13;
          }
          roundedRect(x, y, cellWidth - inset * 2, cellHeight - inset * 2, Math.min(cellWidth, cellHeight) * 0.26);
          context.restore();
        });

        const head = snake[0];
        if (head) {
          const cx = (head.x + 0.5) * cellWidth;
          const cy = (head.y + 0.5) * cellHeight;
          const eyeOffset = Math.min(cellWidth, cellHeight) * 0.13;
          const eyeForward = Math.min(cellWidth, cellHeight) * 0.15;
          const eyeRadius = Math.min(cellWidth, cellHeight) * 0.055;
          const perp = { x: -direction.y, y: direction.x };
          context.fillStyle = "#143216";
          for (const side of [-1, 1]) {
            const ex = cx + direction.x * eyeForward + perp.x * eyeOffset * side;
            const ey = cy + direction.y * eyeForward + perp.y * eyeOffset * side;
            context.beginPath();
            context.arc(ex, ey, eyeRadius, 0, Math.PI * 2);
            context.fill();
          }
        }
      }

      function frame(timestamp) {
        if (state !== "running") return;
        if (!lastFrame) lastFrame = timestamp;
        accumulator += Math.min(timestamp - lastFrame, 100);
        lastFrame = timestamp;
        while (accumulator >= stepTime && state === "running") {
          advance();
          accumulator -= stepTime;
        }
        draw(timestamp);
        if (state === "running") requestAnimationFrame(frame);
      }

      function resizeCanvas() {
        const rect = canvas.getBoundingClientRect();
        const pixelRatio = Math.min(window.devicePixelRatio || 1, 2);
        boardWidth = rect.width;
        boardHeight = rect.height;
        canvas.width = Math.round(boardWidth * pixelRatio);
        canvas.height = Math.round(boardHeight * pixelRatio);
        context.setTransform(pixelRatio, 0, 0, pixelRatio, 0, 0);
        draw();
      }

      startButton.addEventListener("click", startGame);
      window.addEventListener("resize", resizeCanvas);
      window.addEventListener("keydown", (event) => {
        const key = event.key.length === 1 ? event.key.toLowerCase() : event.key;
        const requestedDirection = directions[key];
        if (requestedDirection) {
          event.preventDefault();
          setDirection(requestedDirection);
          return;
        }
        if (event.code === "Space") {
          event.preventDefault();
          if (state === "running") {
            state = "paused";
            showOverlay("paused");
          } else if (state === "paused") {
            startGame();
          } else if (state === "ready" || state === "gameover") {
            startGame();
          }
        }
      });
      document.querySelectorAll(".direction-button").forEach((button) => {
        button.addEventListener("click", () => setDirection(directions[{
          up: "ArrowUp",
          down: "ArrowDown",
          left: "ArrowLeft",
          right: "ArrowRight"
        }[button.dataset.dir]]));
      });
      document.addEventListener("visibilitychange", () => {
        if (document.hidden && state === "running") {
          state = "paused";
          showOverlay("paused");
        }
      });

      resetGame();
      resizeCanvas();
      showOverlay("ready");
    })();
  </script>
</body>
</html>
