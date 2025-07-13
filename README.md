# Gloomy-Sunday-
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Pixel Grunge Coffee Shop</title>
<style>
  body {
    margin: 0; 
    background: #121212;
    display: flex; 
    justify-content: center; 
    align-items: center; 
    height: 100vh;
    font-family: monospace;
    color: #e0e0e0;
  }
  #game {
    image-rendering: pixelated;
    border: 4px solid #006241;
    background: #222;
    position: relative;
  }
  #ui {
    margin-top: 10px;
    text-align: center;
  }
  button {
    background: #006241;
    border: none;
    color: #e0e0e0;
    padding: 0.5rem 1rem;
    font-weight: bold;
    cursor: pointer;
    margin: 0 0.5rem;
    border-radius: 6px;
  }
  button:hover {
    background: #00ff6a;
    color: #121212;
  }
</style>
</head>
<body>

<div>
  <canvas id="game" width="320" height="320"></canvas>
  <div id="ui">
    <div id="roleSelection">
      <p>Choose your role:</p>
      <button id="workerBtn">Worker ☕️</button>
      <button id="customerBtn">Customer 💀</button>
    </div>
    <div id="gameText" style="margin-top:1rem; min-height: 40px;"></div>
  </div>
</div>

<script>
  const canvas = document.getElementById('game');
  const ctx = canvas.getContext('2d');
  ctx.imageSmoothingEnabled = false;

  const roleSelection = document.getElementById('roleSelection');
  const gameText = document.getElementById('gameText');

  // Player sprite (simple pixel block)
  const playerSize = 16;
  let player = {
    x: 5 * playerSize,
    y: 12 * playerSize,
    color: '#00ff6a'
  };

  // Role: null, 'worker', or 'customer'
  let role = null;

  // Map tiles (20x20 tiles for 320x320 canvas, tile size 16)
  const tileSize = 16;
  const mapWidth = 20;
  const mapHeight = 20;

  // Simple map layout: 0 = floor, 1 = counter, 2 = window
  // For pixel grunge vibes, counter is at y=10, window at y=0
  const map = [];
  for(let y=0; y<mapHeight; y++) {
    let row = [];
    for(let x=0; x<mapWidth; x++) {
      if(y === 10 && x > 5 && x < 14) row.push(1); // Counter
      else if(y === 0 && x > 8 && x < 12) row.push(2); // Window
      else row.push(0); // Floor
    }
    map.push(row);
  }

  // Draw tiles
  function drawMap() {
    for(let y=0; y<mapHeight; y++) {
      for(let x=0; x<mapWidth; x++) {
        let tile = map[y][x];
        switch(tile) {
          case 0: // floor - dark gray
            ctx.fillStyle = '#222';
            ctx.fillRect(x*tileSize, y*tileSize, tileSize, tileSize);
            break;
          case 1: // counter - dark green
            ctx.fillStyle = '#004d25';
            ctx.fillRect(x*tileSize, y*tileSize, tileSize, tileSize);
            break;
          case 2: // window - blueish with rain
            ctx.fillStyle = '#003344';
            ctx.fillRect(x*tileSize, y*tileSize, tileSize, tileSize);
            // draw rain lines inside window tile
            ctx.strokeStyle = '#55aaff55';
            ctx.lineWidth = 1;
            for(let i=0; i<tileSize; i+=4) {
              ctx.beginPath();
              ctx.moveTo(x*tileSize + i, y*tileSize);
              ctx.lineTo(x*tileSize + i - 2, y*tileSize + tileSize);
              ctx.stroke();
            }
            break;
        }
      }
    }
  }

  // Draw player
  function drawPlayer() {
    ctx.fillStyle = player.color;
    ctx.fillRect(player.x, player.y, playerSize, playerSize);
  }

  // Text update
  function updateText(msg) {
    gameText.textContent = msg;
  }

  // Check interaction
  function checkInteraction() {
    // If player is near counter tiles (y=10)
    let px = player.x / tileSize;
    let py = player.y / tileSize;
    if(py === 9 || py === 10) {
      if(px >= 6 && px <= 13) {
        if(role === 'worker') {
          updateText('You’re behind the counter, time to make some bitter coffee ☕️.');
        } else if(role === 'customer') {
          updateText('You approach the counter, free coffee awaits 🍵.');
        }
        return;
      }
    }
    updateText('');
  }

  // Movement logic (no walk through counter tiles)
  function canMove(x, y) {
    if(x < 0 || y < 0 || x >= mapWidth || y >= mapHeight) return false;
    if(map[y][x] === 1) return false; // counter blocks customers
    return true;
  }

  // Keyboard control
  window.addEventListener('keydown', e => {
    if(!role) return; // no movement before role selection
    let newX = player.x / tileSize;
    let newY = player.y / tileSize;
    switch(e.key) {
      case 'ArrowUp': newY -= 1; break;
      case 'ArrowDown': newY += 1; break;
      case 'ArrowLeft': newX -= 1; break;
      case 'ArrowRight': newX += 1; break;
      default: return;
    }
    if(canMove(newX, newY)) {
      player.x = newX * tileSize;
      player.y = newY * tileSize;
      draw();
      checkInteraction();
    }
  });

  function draw() {
    drawMap();
    drawPlayer();
  }

  // Role selection
  document.getElementById('workerBtn').addEventListener('click', () => {
    role = 'worker';
    player.color = '#00ff6a'; // green
    roleSelection.style.display = 'none';
    updateText('You chose to be a Worker. Use arrow keys to move around the shop.');
    draw();
  });

  document.getElementById('customerBtn').addEventListener('click', () => {
    role = 'customer';
    player.color = '#b22222'; // dark red
    roleSelection.style.display = 'none';
    updateText('You chose to be a Customer. Use arrow keys to move around and approach the counter.');
    draw();
  });

  // Initial draw
  draw();
</script>

</body>
</html>
