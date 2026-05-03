<!DOCTYPE html>
<html>
<head>
  <title>Territory War - Mobile</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <style>
    body {
      text-align: center;
      font-family: Arial;
      background: #111;
      color: white;
    }

    h1 { font-size: 24px; }

    #grid {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      gap: 8px;
      width: 95vw;
      max-width: 400px;
      margin: auto;
    }

    .cell {
      aspect-ratio: 1;
      background: #555;
      border-radius: 10px;
      touch-action: manipulation;
      transition: transform 0.1s, background 0.2s;
    }

    .cell:active {
      transform: scale(0.9);
    }

    .player { background: #2196f3; }
    .enemy { background: #f44336; }

    #status {
      margin-top: 15px;
      font-size: 18px;
    }
  </style>
</head>

<body>

<h1>⚔️ Territory War</h1>
<p>Tap a tile to capture</p>

<div id="grid"></div>
<p id="status"></p>

<script>
const grid = document.getElementById("grid");
const size = 5;
let cells = [];
let playerTurn = true;

// Create grid
for (let i = 0; i < size * size; i++) {
  let div = document.createElement("div");
  div.classList.add("cell");
  div.dataset.index = i;

  // Better touch handling
  div.addEventListener("touchstart", handleTouch, { passive: true });
  div.addEventListener("click", handleClick);

  grid.appendChild(div);
  cells.push("empty");
}

// Initial positions
cells[0] = "player";
cells[24] = "enemy";
updateUI();

function updateUI() {
  document.querySelectorAll(".cell").forEach((cell, i) => {
    cell.className = "cell";
    if (cells[i] === "player") cell.classList.add("player");
    if (cells[i] === "enemy") cell.classList.add("enemy");
  });
}

function handleTouch(e) {
  e.preventDefault();
  let index = e.target.dataset.index;
  playerMove(parseInt(index));
}

function handleClick(e) {
  let index = e.target.dataset.index;
  playerMove(parseInt(index));
}

function playerMove(index) {
  if (!playerTurn) return;
  if (cells[index] !== "empty") return;

  playerTurn = false;

  cells[index] = "player";
  updateUI();

  setTimeout(() => {
    enemyMove();
    playerTurn = true;
  }, 400);
}

function enemyMove() {
  let empty = cells.map((c, i) => c === "empty" ? i : null).filter(v => v !== null);

  if (empty.length === 0) {
    checkWinner();
    return;
  }

  let move = empty[Math.floor(Math.random() * empty.length)];
  cells[move] = "enemy";

  updateUI();
  checkWinner();
}

function checkWinner() {
  let playerCount = cells.filter(c => c === "player").length;
  let enemyCount = cells.filter(c => c === "enemy").length;

  if (playerCount + enemyCount === size * size) {
    let status = document.getElementById("status");

    if (playerCount > enemyCount) status.innerText = "🎉 You Win!";
    else if (enemyCount > playerCount) status.innerText = "😢 You Lose!";
    else status.innerText = "🤝 Draw!";
  }
}
</script>

</body>
</html>
