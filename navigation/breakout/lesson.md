---
layout: post
title: Breakout Blocks Lesson
author: Nikhil, Rohan, Pranav, Aditya, Shriya, Samhita
permalink: breakoutLesson
---

```mermaid
flowchart TD
    A[Start: Breakout Blocks Lesson] --> B[Lesson 1: Paddle and Base Blocks]
    
    B --> B1[Make the Paddle]
    B1 --> B2[Move the Paddle]
    
    B --> C[Interactive Demos]
    C --> C1[Paddle Movement]
    C1 --> C2[Ball Bouncing]
    C2 --> C3[Paddle + Ball]
    C3 --> C4[Mini Breakout]
    
    B --> D[Lesson 2: Power-Up Block + Timer]
    
    D --> D1[Add Special Bricks]
    D1 --> D2[Draw and Drop Power-Up]
    D2 --> D3[Show Timer]
    
    C4 --> E[Full Power-Up Demo]
    
    D --> F[Exploration Activities]
    
    E --> G[Complete Breakout Game]
    
    style A fill:#ffffff,stroke:#000000,stroke-width:3px,color:#000000
    style B fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#000000
    style B1 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000000
    style B2 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000000
    style C fill:#e8f5e8,stroke:#388e3c,stroke-width:2px,color:#000000
    style C1 fill:#f1f8e9,stroke:#66bb6a,stroke-width:2px,color:#000000
    style C2 fill:#f1f8e9,stroke:#66bb6a,stroke-width:2px,color:#000000
    style C3 fill:#f1f8e9,stroke:#66bb6a,stroke-width:2px,color:#000000
    style C4 fill:#f1f8e9,stroke:#66bb6a,stroke-width:2px,color:#000000
    style D fill:#fff3e0,stroke:#f57c00,stroke-width:2px,color:#000000
    style D1 fill:#fff8e1,stroke:#ffb74d,stroke-width:2px,color:#000000
    style D2 fill:#fff8e1,stroke:#ffb74d,stroke-width:2px,color:#000000
    style D3 fill:#fff8e1,stroke:#ffb74d,stroke-width:2px,color:#000000
    style E fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#000000
    style F fill:#f9fbe7,stroke:#827717,stroke-width:2px,color:#000000
    style G fill:#ffebee,stroke:#d32f2f,stroke-width:3px,color:#000000
```

<br>

### [👉 Click this for full source code](https://github.com/code259/curators/blob/main/navigation/breakout/full_breakout.md?plain=1)


## **Lesson 1: Paddle and Base Blocks**

**Goal:** Learn how to move the paddle (player) left and right and create basic bricks.

### Step 1: Make the paddle

We draw a rectangle at the bottom of the canvas.

```js
let paddleHeight = 10;
let basePaddleWidth = 75;
let paddleWidth = basePaddleWidth;
let paddleX = (canvas.width - paddleWidth) / 2;

function drawPaddle() {
  ctx.beginPath();
  ctx.rect(paddleX, canvas.height - paddleHeight, paddleWidth, paddleHeight);
  ctx.fillStyle = "#0095DD";
  ctx.fill();
  ctx.closePath();
}
```

### Step 2: Move the paddle

We listen for keyboard input and update the paddleX position.

```js
let rightPressed = false;
let leftPressed = false;

document.addEventListener("keydown", keyDownHandler);
document.addEventListener("keyup", keyUpHandler);

function keyDownHandler(e) {
  if (e.key === "Right" || e.key === "ArrowRight") rightPressed = true;
  else if (e.key === "Left" || e.key === "ArrowLeft") leftPressed = true;
}

function keyUpHandler(e) {
  if (e.key === "Right" || e.key === "ArrowRight") rightPressed = false;
  else if (e.key === "Left" || e.key === "ArrowLeft") leftPressed = false;
}

function updatePaddle() {
  if (rightPressed && paddleX < canvas.width - paddleWidth) paddleX += 7;
  else if (leftPressed && paddleX > 0) paddleX -= 7;
}
```

**Explore:**

* Make your own block that moves left/right with the arrow keys.
* Change its width/height and test how it feels.

---

## **Lesson 2: Power-Up Block + Timer**

**Goal:** Add a special block that gives a temporary boost when hit.

### Step 1: Add special bricks that drop a power-up when broken.

* We randomly give some bricks a powerUp property:

```js
let bricks = [];
const powerUpChance = 0.3; // 30% chance
for (let c = 0; c < brickColumnCount; c++) {
  bricks[c] = [];
  for (let r = 0; r < brickRowCount; r++) {
    const hasPowerUp = Math.random() < powerUpChance;
    bricks[c][r] = { x: 0, y: 0, status: 1, powerUp: hasPowerUp };
  }
}
```

When a power-up brick is hit, it spawns a falling power-up:
```js
if (b.powerUp) {
  powerUps.push({ x: b.x + brickWidth/2, y: b.y, active: true });
}
```

### Step 2: Draw and drop the power-up

* Each power-up is drawn as a glowing circle with “P” inside, and it falls down.

```js
let powerUps = [];
let activePowerUp = null;
let powerUpTimer = 0;
const powerUpDuration = 5000; // 5 seconds

function drawPowerUps() {
  for (let p of powerUps) {
    if (p.active) {
      ctx.beginPath();
      ctx.arc(p.x, p.y, 10, 0, Math.PI * 2);
      ctx.fillStyle = "gold";
      ctx.fill();
      ctx.closePath();

      ctx.fillStyle = "black";
      ctx.font = "bold 14px Arial";
      ctx.textAlign = "center";
      ctx.fillText("P", p.x, p.y);

      // Make it fall
      p.y += 1.5;

      // If paddle catches it
      if (p.y >= canvas.height - paddleHeight &&
          p.x > paddleX && p.x < paddleX + paddleWidth) {
        p.active = false;
        paddleWidth = basePaddleWidth + 40; // widen paddle
        activePowerUp = "Wide Paddle";
        powerUpTimer = Date.now(); // start timer
      }
    }
  }
}
```

### Step 3: Show timer

* Draw the time left on screen.

```js
function drawPowerUpTimer() {
  if (activePowerUp) {
    let elapsed = Date.now() - powerUpTimer;
    let remaining = Math.max(0, powerUpDuration - elapsed);

    // Draw bar
    ctx.fillStyle = "gray";
    ctx.fillRect(canvas.width - 20, 20, 10, 100);

    ctx.fillStyle = "lime";
    let fillHeight = (remaining / powerUpDuration) * 100;
    ctx.fillRect(canvas.width - 20, 120 - fillHeight, 10, fillHeight);

    if (remaining <= 0) {
      activePowerUp = null;
      paddleWidth = basePaddleWidth; // reset
    }
  }
}

```

**Explore:**
* Make the timer 10 seconds instead of 5..
* Try different effects (ex: double player speed).

<h2>Interactive Demos Progression</h2>
<h4>Until base functionality - does not include advanced features</h4>
<h4>Use the right and left arrows to move the breaker.<h4>

<h3>1. Paddle Movement</h3>

<!-- Toggle -->
<label style="display:block; text-align:center; margin:6px 0;">
  <input type="checkbox" id="toggle-paddle"> Show code
</label>

<!-- Canvas wrapper (shown by default) -->
<div id="wrap-paddle">
  <!-- Canvas 1: Paddle Movement -->

  <canvas id="paddleDemo" width="300" height="150" style="background:white; border:2px solid #333; display:block; margin:0 auto;"></canvas>

  <script>
  const pdCanvas = document.getElementById("paddleDemo");
  const pdCtx = pdCanvas.getContext("2d");
  let pdX = (pdCanvas.width - 75) / 2;
  let pdRight = false, pdLeft = false;

  document.addEventListener("keydown", e => {
    if (e.key === "ArrowRight") pdRight = true;
    if (e.key === "ArrowLeft") pdLeft = true;
  });
  document.addEventListener("keyup", e => {
    if (e.key === "ArrowRight") pdRight = false;
    if (e.key === "ArrowLeft") pdLeft = false;
  });

  function drawPaddleDemo() {
    pdCtx.clearRect(0,0,pdCanvas.width,pdCanvas.height);
    pdCtx.fillStyle = "#0095DD";
    pdCtx.fillRect(pdX, pdCanvas.height-10, 75, 10);
    if (pdRight && pdX < pdCanvas.width-75) pdX += 5;
    if (pdLeft && pdX > 0) pdX -= 5;
    requestAnimationFrame(drawPaddleDemo);
  }
  drawPaddleDemo();
  </script>
</div>

<!-- Read-only code view (hidden by default) -->
<pre id="code-paddle" style="display:none; max-width:820px; margin:8px auto; overflow:auto;"><code>&lt;!-- Canvas 1: Paddle Movement --&gt;
&lt;h3&gt;1. Paddle Movement&lt;/h3&gt;
&lt;canvas id="paddleDemo" width="300" height="150" style="background:white; border:2px solid #333; display:block; margin:0 auto;"&gt;&lt;/canvas&gt;

&lt;script&gt;
const pdCanvas = document.getElementById("paddleDemo");
const pdCtx = pdCanvas.getContext("2d");
let pdX = (pdCanvas.width - 75) / 2;
let pdRight = false, pdLeft = false;

document.addEventListener("keydown", e =&gt; {
  if (e.key === "ArrowRight") pdRight = true;
  if (e.key === "ArrowLeft") pdLeft = true;
});
document.addEventListener("keyup", e =&gt; {
  if (e.key === "ArrowRight") pdRight = false;
  if (e.key === "ArrowLeft") pdLeft = false;
});

function drawPaddleDemo() {
  pdCtx.clearRect(0,0,pdCanvas.width,pdCanvas.height);
  pdCtx.fillStyle = "#0095DD";
  pdCtx.fillRect(pdX, pdCanvas.height-10, 75, 10);
  if (pdRight &amp;&amp; pdX &lt; pdCanvas.width-75) pdX += 5;
  if (pdLeft &amp;&amp; pdX &gt; 0) pdX -= 5;
  requestAnimationFrame(drawPaddleDemo);
}
drawPaddleDemo();
&lt;/script&gt;
</code></pre>

<!-- Tiny toggle wiring for Canvas 1 -->
<script>
(function(){
  const toggle = document.getElementById("toggle-paddle");
  const wrap = document.getElementById("wrap-paddle");
  const code = document.getElementById("code-paddle");

  // default: unchecked → canvas visible
  toggle.checked = false;

  toggle.addEventListener("change", () => {
    if (toggle.checked) {
      wrap.style.display = "none";
      code.style.display = "block";
    } else {
      code.style.display = "none";
      wrap.style.display = "block";
    }
  });
})();
</script>

<!-- ========================= Canvas 2 ========================= -->
<h3>2. Ball Bouncing</h3>
<label style="display:block; text-align:center; margin:6px 0;">
  <input type="checkbox" id="toggle-ball"> Show code
</label>

<div id="wrap-ball">
  <!-- Canvas 2: Ball Bouncing -->
  <canvas id="ballDemo" width="300" height="150" style="background:white; border:2px solid #333; display:block; margin:0 auto;"></canvas>

  <script>
  const bCanvas = document.getElementById("ballDemo");
  const bCtx = bCanvas.getContext("2d");
  let bx = bCanvas.width/2, by = bCanvas.height/2, bvx = 2, bvy = 2, br = 8;

  function drawBallDemo() {
    bCtx.clearRect(0,0,bCanvas.width,bCanvas.height);
    bCtx.beginPath();
    bCtx.arc(bx, by, br, 0, Math.PI*2);
    bCtx.fillStyle = "#DD0000";
    bCtx.fill();
    bCtx.closePath();
    bx += bvx; by += bvy;
    if (bx+br > bCanvas.width || bx-br < 0) bvx = -bvx;
    if (by+br > bCanvas.height || by-br < 0) bvy = -bvy;
    requestAnimationFrame(drawBallDemo);
  }
  drawBallDemo();
  </script>
</div>

<pre id="code-ball" style="display:none; max-width:820px; margin:8px auto; overflow:auto;"><code>&lt;!-- Canvas 2: Ball Bouncing --&gt;
&lt;h3&gt;2. Ball Bouncing&lt;/h3&gt;
&lt;canvas id="ballDemo" width="300" height="150" style="background:white; border:2px solid #333; display:block; margin:0 auto;"&gt;&lt;/canvas&gt;

&lt;script&gt;
const bCanvas = document.getElementById("ballDemo");
const bCtx = bCanvas.getContext("2d");
let bx = bCanvas.width/2, by = bCanvas.height/2, bvx = 2, bvy = 2, br = 8;

function drawBallDemo() {
  bCtx.clearRect(0,0,bCanvas.width,bCanvas.height);
  bCtx.beginPath();
  bCtx.arc(bx, by, br, 0, Math.PI*2);
  bCtx.fillStyle = "#DD0000";
  bCtx.fill();
  bCtx.closePath();
  bx += bvx; by += bvy;
  if (bx+br &gt; bCanvas.width || bx-br &lt; 0) bvx = -bvx;
  if (by+br &gt; bCanvas.height || by-br &lt; 0) bvy = -bvy;
  requestAnimationFrame(drawBallDemo);
}
drawBallDemo();
&lt;/script&gt;
</code></pre>

<script>
(function(){
  const toggle = document.getElementById("toggle-ball");
  const wrap = document.getElementById("wrap-ball");
  const code = document.getElementById("code-ball");
  toggle.checked = false;
  toggle.addEventListener("change", () => {
    if (toggle.checked) { wrap.style.display = "none"; code.style.display = "block"; }
    else { code.style.display = "none"; wrap.style.display = "block"; }
  });
})();
</script>

<!-- ========================= Canvas 3 ========================= -->
<h3>3. Paddle + Ball</h3>
<label style="display:block; text-align:center; margin:6px 0;">
  <input type="checkbox" id="toggle-combo"> Show code
</label>

<div id="wrap-combo">
  <!-- Canvas 3: Paddle + Ball -->
  <canvas id="comboDemo" width="300" height="150" style="background:white; border:2px solid #333; display:block; margin:0 auto;"></canvas>

  <script>
  const cCanvas = document.getElementById("comboDemo");
  const cCtx = cCanvas.getContext("2d");
  let cx = cCanvas.width/2, cy = cCanvas.height-30, cvx = 2, cvy = -2, cr = 8;
  let cpX = (cCanvas.width - 75)/2, cRight = false, cLeft = false;

  document.addEventListener("keydown", e => {
    if (e.key === "ArrowRight") cRight = true;
    if (e.key === "ArrowLeft") cLeft = true;
  });
  document.addEventListener("keyup", e => {
    if (e.key === "ArrowRight") cRight = false;
    if (e.key === "ArrowLeft") cLeft = false;
  });

  function drawCombo() {
    cCtx.clearRect(0,0,cCanvas.width,cCanvas.height);
    // Ball
    cCtx.beginPath();
    cCtx.arc(cx, cy, cr, 0, Math.PI*2);
    cCtx.fillStyle = "#DD0000";
    cCtx.fill();
    cCtx.closePath();
    // Paddle
    cCtx.fillStyle = "#0095DD";
    cCtx.fillRect(cpX, cCanvas.height-10, 75, 10);
    // Update ball
    cx += cvx; cy += cvy;
    if (cx+cr > cCanvas.width || cx-cr < 0) cvx = -cvx;
    if (cy-cr < 0) cvy = -cvy;
    else if (cy+cr > cCanvas.height-10 && cx > cpX && cx < cpX+75) cvy = -cvy;
    else if (cy+cr > cCanvas.height) { cx = cCanvas.width/2; cy = cCanvas.height/2; }
    // Update paddle
    if (cRight && cpX < cCanvas.width-75) cpX += 5;
    if (cLeft && cpX > 0) cpX -= 5;
    requestAnimationFrame(drawCombo);
  }
  drawCombo();
  </script>
</div>

<pre id="code-combo" style="display:none; max-width:820px; margin:8px auto; overflow:auto;"><code>&lt;!-- Canvas 3: Paddle + Ball --&gt;
&lt;h3&gt;3. Paddle + Ball&lt;/h3&gt;
&lt;canvas id="comboDemo" width="300" height="150" style="background:white; border:2px solid #333; display:block; margin:0 auto;"&gt;&lt;/canvas&gt;

&lt;script&gt;
const cCanvas = document.getElementById("comboDemo");
const cCtx = cCanvas.getContext("2d");
let cx = cCanvas.width/2, cy = cCanvas.height-30, cvx = 2, cvy = -2, cr = 8;
let cpX = (cCanvas.width - 75)/2, cRight = false, cLeft = false;

document.addEventListener("keydown", e =&gt; {
  if (e.key === "ArrowRight") cRight = true;
  if (e.key === "ArrowLeft") cLeft = true;
});
document.addEventListener("keyup", e =&gt; {
  if (e.key === "ArrowRight") cRight = false;
  if (e.key === "ArrowLeft") cLeft = false;
});

function drawCombo() {
  cCtx.clearRect(0,0,cCanvas.width,cCanvas.height);
  // Ball
  cCtx.beginPath();
  cCtx.arc(cx, cy, cr, 0, Math.PI*2);
  cCtx.fillStyle = "#DD0000";
  cCtx.fill();
  cCtx.closePath();
  // Paddle
  cCtx.fillStyle = "#0095DD";
  cCtx.fillRect(cpX, cCanvas.height-10, 75, 10);
  // Update ball
  cx += cvx; cy += cvy;
  if (cx+cr &gt; cCanvas.width || cx-cr &lt; 0) cvx = -cvx;
  if (cy-cr &lt; 0) cvy = -cvy;
  else if (cy+cr &gt; cCanvas.height-10 &amp;&amp; cx &gt; cpX &amp;&amp; cx &lt; cpX+75) cvy = -cvy;
  else if (cy+cr &gt; cCanvas.height) { cx = cCanvas.width/2; cy = cCanvas.height/2; }
  // Update paddle
  if (cRight &amp;&amp; cpX &lt; cCanvas.width-75) cpX += 5;
  if (cLeft &amp;&amp; cpX &gt; 0) cpX -= 5;
  requestAnimationFrame(drawCombo);
}
drawCombo();
&lt;/script&gt;
</code></pre>

<script>
(function(){
  const toggle = document.getElementById("toggle-combo");
  const wrap = document.getElementById("wrap-combo");
  const code = document.getElementById("code-combo");
  toggle.checked = false;
  toggle.addEventListener("change", () => {
    if (toggle.checked) { wrap.style.display = "none"; code.style.display = "block"; }
    else { code.style.display = "none"; wrap.style.display = "block"; }
  });
})();
</script>

<!-- ========================= Canvas 4 ========================= -->
<h3>4. Mini Breakout (Ball + Paddle + Bricks)</h3>
<label style="display:block; text-align:center; margin:6px 0;">
  <input type="checkbox" id="toggle-breakout"> Show code
</label>

<div id="wrap-breakout">
  <!-- Canvas 4: Full Mini Breakout -->
  <canvas id="breakoutDemo" width="300" height="200" style="background:white; border:2px solid #333; display:block; margin:0 auto;"></canvas>

  <script>
  const brCanvas = document.getElementById("breakoutDemo");
  const brCtx = brCanvas.getContext("2d");
  // Ball
  let brX = brCanvas.width/2, brY = brCanvas.height-30, brVX = 2, brVY = -2, brR = 8;
  // Paddle
  let brPW = 75, brPH = 10, brPX = (brCanvas.width-brPW)/2, brRight = false, brLeft = false;
  // Bricks
  const rowCount=3, colCount=5, bw=50, bh=15, bp=10, bo=30, bt=30;
  let bricks = [];
  for(let c=0;c<colCount;c++){ bricks[c]=[]; for(let r=0;r<rowCount;r++){ bricks[c][r]={x:0,y:0,status:1}; } }

  document.addEventListener("keydown",e=>{ if(e.key==="ArrowRight")brRight=true; if(e.key==="ArrowLeft")brLeft=true; });
  document.addEventListener("keyup",e=>{ if(e.key==="ArrowRight")brRight=false; if(e.key==="ArrowLeft")brLeft=false; });

  function drawBricks() {
    for(let c=0;c<colCount;c++){ for(let r=0;r<rowCount;r++){
      if(bricks[c][r].status==1){
        let bx=(c*(bw+bp))+bo, by=(r*(bh+bp))+bt;
        bricks[c][r].x=bx; bricks[c][r].y=by;
        brCtx.fillStyle="#00AA00";
        brCtx.fillRect(bx,by,bw,bh);
      }
    }}
  }

  function drawBreakout() {
    brCtx.clearRect(0,0,brCanvas.width,brCanvas.height);
    // Ball
    brCtx.beginPath(); brCtx.arc(brX,brY,brR,0,Math.PI*2); brCtx.fillStyle="#DD0000"; brCtx.fill(); brCtx.closePath();
    // Paddle
    brCtx.fillStyle="#0095DD"; brCtx.fillRect(brPX, brCanvas.height-brPH, brPW, brPH);
    // Bricks
    drawBricks();
    // Collision
    for(let c=0;c<colCount;c++){ for(let r=0;r<rowCount;r++){
      let b=bricks[c][r];
      if(b.status==1 && brX> b.x && brX< b.x+bw && brY> b.y && brY< b.y+bh){
        brVY=-brVY; b.status=0;
      }
    }}
    // Ball move
    brX+=brVX; brY+=brVY;
    if(brX+brR>brCanvas.width||brX-brR<0) brVX=-brVX;
    if(brY-brR<0) brVY=-brVY;
    else if(brY+brR>brCanvas.height-brPH && brX>brPX && brX<brPX+brPW) brVY=-brVY;
    else if(brY+brR>brCanvas.height){ brX=brCanvas.width/2; brY=brCanvas.height-30; brVY=-2; }
    // Paddle move
    if(brRight && brPX<brCanvas.width-brPW) brPX+=5;
    if(brLeft && brPX>0) brPX-=5;
    requestAnimationFrame(drawBreakout);
  }
  drawBreakout();
  </script>
</div>

<pre id="code-breakout" style="display:none; max-width:820px; margin:8px auto; overflow:auto;"><code>&lt;!-- Canvas 4: Full Mini Breakout --&gt;
&lt;h3&gt;4. Mini Breakout (Ball + Paddle + Bricks)&lt;/h3&gt;
&lt;canvas id="breakoutDemo" width="300" height="200" style="background:white; border:2px solid #333; display:block; margin:0 auto;"&gt;&lt;/canvas&gt;

&lt;script&gt;
const brCanvas = document.getElementById("breakoutDemo");
const brCtx = brCanvas.getContext("2d");
// Ball
let brX = brCanvas.width/2, brY = brCanvas.height-30, brVX = 2, brVY = -2, brR = 8;
// Paddle
let brPW = 75, brPH = 10, brPX = (brCanvas.width-brPW)/2, brRight = false, brLeft = false;
// Bricks
const rowCount=3, colCount=5, bw=50, bh=15, bp=10, bo=30, bt=30;
let bricks = [];
for(let c=0;c&lt;colCount;c++){ bricks[c]=[]; for(let r=0;r&lt;rowCount;r++){ bricks[c][r]={x:0,y:0,status:1}; } }

document.addEventListener("keydown",e=&gt;{ if(e.key==="ArrowRight")brRight=true; if(e.key==="ArrowLeft")brLeft=true; });
document.addEventListener("keyup",e=&gt;{ if(e.key==="ArrowRight")brRight=false; if(e.key==="ArrowLeft")brLeft=false; });

function drawBricks() {
  for(let c=0;c&lt;colCount;c++){ for(let r=0;r&lt;rowCount;r++){
    if(bricks[c][r].status==1){
      let bx=(c*(bw+bp))+bo, by=(r*(bh+bp))+bt;
      bricks[c][r].x=bx; bricks[c][r].y=by;
      brCtx.fillStyle="#00AA00";
      brCtx.fillRect(bx,by,bw,bh);
    }
  }}
}

function drawBreakout() {
  brCtx.clearRect(0,0,brCanvas.width,brCanvas.height);
  // Ball
  brCtx.beginPath(); brCtx.arc(brX,brY,brR,0,Math.PI*2); brCtx.fillStyle="#DD0000"; brCtx.fill(); brCtx.closePath();
  // Paddle
  brCtx.fillStyle="#0095DD"; brCtx.fillRect(brPX, brCanvas.height-brPH, brPW, brPH);
  // Bricks
  drawBricks();
  // Collision
  for(let c=0;c&lt;colCount;c++){ for(let r=0;r&lt;rowCount;r++){
    let b=bricks[c][r];
    if(b.status==1 &amp;&amp; brX&gt; b.x &amp;&amp; brX&lt; b.x+bw &amp;&amp; brY&gt; b.y &amp;&amp; brY&lt; b.y+bh){
      brVY=-brVY; b.status=0;
    }
  }}
  // Ball move
  brX+=brVX; brY+=brVY;
  if(brX+brR&gt;brCanvas.width||brX-brR&lt;0) brVX=-brVX;
  if(brY-brR&lt;0) brVY=-brVY;
  else if(brY+brR&gt;brCanvas.height-brPH &amp;&amp; brX&gt;brPX &amp;&amp; brX&lt;brPX+brPW) brVY=-brVY;
  else if(brY+brR&gt;brCanvas.height){ brX=brCanvas.width/2; brY=brCanvas.height-30; brVY=-2; }
  // Paddle move
  if(brRight &amp;&amp; brPX&lt;brCanvas.width-brPW) brPX+=5;
  if(brLeft &amp;&amp; brPX&gt;0) brPX-=5;
  requestAnimationFrame(drawBreakout);
}
drawBreakout();
&lt;/script&gt;
</code></pre>

<script>
(function(){
  const toggle = document.getElementById("toggle-breakout");
  const wrap = document.getElementById("wrap-breakout");
  const code = document.getElementById("code-breakout");
  toggle.checked = false;
  toggle.addEventListener("change", () => {
    if (toggle.checked) { wrap.style.display = "none"; code.style.display = "block"; }
    else { code.style.display = "none"; wrap.style.display = "block"; }
  });
})();
</script>

<!-- ===================== Breakout Blocks: Checkpoint Quizzes ===================== -->
<div id="breakout-blocks-quizzes">
<style>
  #breakout-blocks-quizzes { --ok:#118a00; --bad:#b00020; font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif; }
  #breakout-blocks-quizzes .quiz-card{
    background:#fff;border:2px solid #ddd;border-radius:14px;
    padding:1.2rem;margin:1.2rem 0;box-shadow:0 4px 12px rgba(0,0,0,.05);
    color:#000;
  }
  #breakout-blocks-quizzes .quiz-title{font-size:1.2rem;font-weight:700;margin-bottom:.25rem}
  #breakout-blocks-quizzes .quiz-sub{margin-bottom:.9rem;color:#222}
  #breakout-blocks-quizzes .q{border-radius:10px;padding:.9rem;margin:.7rem 0;border:1px solid #eee}
  #breakout-blocks-quizzes .q:nth-child(odd){background:#f7f3ff;}  /* lilac */
  #breakout-blocks-quizzes .q:nth-child(even){background:#f3fff7;} /* mint  */
  #breakout-blocks-quizzes .prompt{font-weight:700;margin-bottom:.4rem}
  #breakout-blocks-quizzes .option{display:flex;gap:.45rem;align-items:flex-start;margin:.3rem 0}
  #breakout-blocks-quizzes button{
    background:#f7f7f7;color:#000;border:2px solid #000;
    border-radius:999px;padding:.45rem 1rem;
    font-weight:700;cursor:pointer;margin-top:.6rem;margin-right:.4rem
  }
  #breakout-blocks-quizzes button:hover{background:#000;color:#fff}
  #breakout-blocks-quizzes .feedback{margin-top:.6rem;font-weight:800}
  #breakout-blocks-quizzes .feedback.ok{color:var(--ok)}
  #breakout-blocks-quizzes .feedback.bad{color:var(--bad)}
  #breakout-blocks-quizzes code{
    background:#f4f4f4;color:#000;padding:2px 5px;border-radius:4px
  }
</style>

  <!-- Quiz A: Lesson 1 — Paddle & Base Blocks -->
  <div class="quiz-card" data-quiz="A">
    <div class="quiz-title">Lesson 1 Checkpoint</div>
    <div class="quiz-sub">Paddle setup, keyboard input, and bounds</div>

    <div class="q">
      <div class="prompt">1) Which variables track keyboard input for moving the paddle?</div>
      <label class="option"><input type="checkbox" value="rightPressed">rightPressed</label>
      <label class="option"><input type="checkbox" value="leftPressed">leftPressed</label>
      <label class="option"><input type="checkbox" value="paddleHeight">paddleHeight</label>
      <label class="option"><input type="checkbox" value="paddleWidth">paddleWidth</label>
    </div>

    <div class="q">
      <div class="prompt">2) What prevents the paddle from leaving the canvas?</div>
      <label class="option"><input type="radio" name="A2">Increasing <code>paddleWidth</code> when near the edge</label>
      <label class="option"><input type="radio" name="A2">Conditional checks on <code>paddleX</code> against 0 and <code>canvas.width - paddleWidth</code></label>
      <label class="option"><input type="radio" name="A2">Calling <code>ctx.closePath()</code> in <code>drawPaddle()</code></label>
    </div>

    <div class="q">
      <div class="prompt">3) Which events are used to update movement flags?</div>
      <label class="option"><input type="checkbox" value="keydown">keydown</label>
      <label class="option"><input type="checkbox" value="keyup">keyup</label>
      <label class="option"><input type="checkbox" value="wheel">wheel</label>
      <label class="option"><input type="checkbox" value="resize">resize</label>
    </div>

    <button class="check">Check Answers</button>
    <button class="clear">Clear</button>
    <div class="feedback"></div>
  </div>

  <!-- Quiz B: Lesson 2 — Power-Up Block + Timer -->
  <div class="quiz-card" data-quiz="B">
    <div class="quiz-title">Lesson 2 Checkpoint</div>
    <div class="quiz-sub">Spawning, catching, and timing a power-up</div>

    <div class="q">
      <div class="prompt">1) When a power-up brick is hit, what happens immediately?</div>
      <label class="option"><input type="radio" name="B1">All bricks in the row disappear</label>
      <label class="option"><input type="radio" name="B1">A falling power-up object is pushed into <code>powerUps</code></label>
      <label class="option"><input type="radio" name="B1">Ball speed is permanently doubled</label>
    </div>

    <div class="q">
      <div class="prompt">2) What effect is applied when the paddle catches the power-up in the sample code?</div>
      <label class="option"><input type="radio" name="B2">The paddle becomes narrower</label>
      <label class="option"><input type="radio" name="B2">The paddle becomes wider temporarily</label>
      <label class="option"><input type="radio" name="B2">The ball changes color</label>
    </div>

    <div class="q">
      <div class="prompt">3) Which variables manage the power-up’s duration?</div>
      <label class="option"><input type="checkbox" value="activePowerUp">activePowerUp</label>
      <label class="option"><input type="checkbox" value="powerUpTimer">powerUpTimer</label>
      <label class="option"><input type="checkbox" value="powerUpDuration">powerUpDuration</label>
      <label class="option"><input type="checkbox" value="basePaddleWidth">basePaddleWidth</label>
    </div>

    <div class="q">
      <div class="prompt">4) What UI element communicates remaining time to the player?</div>
      <label class="option"><input type="radio" name="B4">A vertical bar that fills/shrinks</label>
      <label class="option"><input type="radio" name="B4">A blinking paddle outline</label>
      <label class="option"><input type="radio" name="B4">An alert popup every second</label>
    </div>

    <button class="check">Check Answers</button>
    <button class="clear">Clear</button>
    <div class="feedback"></div>
  </div>

  <!-- Quiz C: Interactive Demos — Ball Bouncing Logic -->
  <div class="quiz-card" data-quiz="C">
    <div class="quiz-title">Demos Checkpoint</div>
    <div class="quiz-sub">Ball bouncing conditions</div>

    <div class="q">
      <div class="prompt">1) Which condition flips the ball’s horizontal velocity?</div>
      <label class="option"><input type="radio" name="C1"><code>bx + br &gt; canvas.width || bx - br &lt; 0</code></label>
      <label class="option"><input type="radio" name="C1"><code>by - br &lt; 0</code></label>
      <label class="option"><input type="radio" name="C1"><code>by + br &gt; canvas.height - paddleHeight</code></label>
    </div>

    <div class="q">
      <div class="prompt">2) In the Paddle + Ball demo, what causes a bounce off the paddle?</div>
      <label class="option"><input type="radio" name="C2">Reaching the exact center of the canvas</label>
      <label class="option"><input type="radio" name="C2">Ball’s bottom touching paddle’s top while x is between paddle edges</label>
      <label class="option"><input type="radio" name="C2">Calling <code>ctx.closePath()</code> after drawing the paddle</label>
    </div>

    <button class="check">Check Answers</button>
    <button class="clear">Clear</button>
    <div class="feedback"></div>
  </div>

  <!-- Quiz D: Mini Breakout — Bricks & Collision -->
  <div class="quiz-card" data-quiz="D">
    <div class="quiz-title">Mini Breakout Checkpoint</div>
    <div class="quiz-sub">Brick grid and collision clearing</div>

    <div class="q">
      <div class="prompt">1) How are brick positions assigned each frame in the sample?</div>
      <label class="option"><input type="radio" name="D1">They are randomized every loop</label>
      <label class="option"><input type="radio" name="D1">Computed from row/column indices using offsets and padding, then stored to each brick’s <code>x,y</code></label>
      <label class="option"><input type="radio" name="D1">Hardcoded pixel coordinates for each brick</label>
    </div>

    <div class="q">
      <div class="prompt">2) What happens when the ball’s position overlaps a brick’s rectangle?</div>
      <label class="option"><input type="radio" name="D2">Vertical velocity inverts and the brick’s <code>status</code> becomes 0</label>
      <label class="option"><input type="radio" name="D2">The ball teleports to center</label>
      <label class="option"><input type="radio" name="D2">All bricks immediately reset</label>
    </div>

    <div class="q">
      <div class="prompt">3) Select all parameters that define the brick layout grid:</div>
      <label class="option"><input type="checkbox" value="rowCount">rowCount</label>
      <label class="option"><input type="checkbox" value="colCount">colCount</label>
      <label class="option"><input type="checkbox" value="bw">bw (brick width)</label>
      <label class="option"><input type="checkbox" value="bt">bt (top offset)</label>
      <label class="option"><input type="checkbox" value="gravity">gravity</label>
    </div>

    <button class="check">Check Answers</button>
    <button class="clear">Clear</button>
    <div class="feedback"></div>
  </div>
</div>

<script>
/* Answer key for Breakout Blocks quizzes */
(function(){
  const key = {
    A: {
      multi1: ["rightPressed","leftPressed"],
      single2: "Conditional checks on paddleX against 0 and canvas.width - paddleWidth",
      multi3: ["keydown","keyup"]
    },
    B: {
      single1: "A falling power-up object is pushed into powerUps",
      single2: "The paddle becomes wider temporarily",
      multi3: ["activePowerUp","powerUpTimer","powerUpDuration"],
      single4: "A vertical bar that fills/shrinks"
    },
    C: {
      single1: "bx + br > canvas.width || bx - br < 0",
      single2: "Ball’s bottom touching paddle’s top while x is between paddle edges"
    },
    D: {
      single1: "Computed from row/column indices using offsets and padding, then stored to each brick’s x,y",
      single2: "Vertical velocity inverts and the brick’s status becomes 0",
      multi3: ["rowCount","colCount","bw","bt"]
    }
  };

  function textOf(label){
    return label.textContent.replace(/\s+/g,' ').trim();
  }

  function getCheckedValues(scope, selector){
    return [...scope.querySelectorAll(selector)]
      .filter(i=>i.checked)
      .map(i=>i.value || textOf(i.parentNode));
  }

  function arraysEqual(a,b){
    const A=[...a].sort(); const B=[...b].sort();
    return A.length===B.length && A.every((v,i)=>v===B[i]);
  }

  document.querySelectorAll('#breakout-blocks-quizzes .quiz-card').forEach(card=>{
    const id = card.dataset.quiz;

    card.querySelector('.check').addEventListener('click', ()=>{
      let ok = true;

      if(id==="A"){
        const chosen1 = getCheckedValues(card,'input[type=checkbox]');
        // Map Q1 & Q3 separately:
        const qBlocks = card.querySelectorAll('.q');
        const q1 = [...qBlocks[0].querySelectorAll('input[type=checkbox]:checked')].map(i=>i.value);
        const q2 = qBlocks[1].querySelector('input[type=radio]:checked');
        const q3 = [...qBlocks[2].querySelectorAll('input[type=checkbox]:checked')].map(i=>i.value);

        if(!arraysEqual(q1, key.A.multi1)) ok = false;
        if(!q2 || textOf(q2.parentNode) !== key.A.single2) ok = false;
        if(!arraysEqual(q3, key.A.multi3)) ok = false;
      }

      if(id==="B"){
        const qBlocks = card.querySelectorAll('.q');
        const b1 = qBlocks[0].querySelector('input[type=radio]:checked');
        const b2 = qBlocks[1].querySelector('input[type=radio]:checked');
        const b3 = [...qBlocks[2].querySelectorAll('input[type=checkbox]:checked')].map(i=>i.value);
        const b4 = qBlocks[3].querySelector('input[type=radio]:checked');

        if(!b1 || textOf(b1.parentNode)!==key.B.single1) ok=false;
        if(!b2 || textOf(b2.parentNode)!==key.B.single2) ok=false;
        if(!arraysEqual(b3, key.B.multi3)) ok=false;
        if(!b4 || textOf(b4.parentNode)!==key.B.single4) ok=false;
      }

      if(id==="C"){
        const qBlocks = card.querySelectorAll('.q');
        const c1 = qBlocks[0].querySelector('input[type=radio]:checked');
        const c2 = qBlocks[1].querySelector('input[type=radio]:checked');

        if(!c1 || textOf(c1.parentNode)!==key.C.single1) ok=false;
        if(!c2 || textOf(c2.parentNode)!==key.C.single2) ok=false;
      }

      if(id==="D"){
        const qBlocks = card.querySelectorAll('.q');
        const d1 = qBlocks[0].querySelector('input[type=radio]:checked');
        const d2 = qBlocks[1].querySelector('input[type=radio]:checked');
        const d3 = [...qBlocks[2].querySelectorAll('input[type=checkbox]:checked')].map(i=>i.value);

        if(!d1 || textOf(d1.parentNode)!==key.D.single1) ok=false;
        if(!d2 || textOf(d2.parentNode)!==key.D.single2) ok=false;
        if(!arraysEqual(d3, key.D.multi3)) ok=false;
      }

      const fb = card.querySelector('.feedback');
      fb.textContent = ok ? "✅ Correct!" : "❌ Try again.";
      fb.className = "feedback " + (ok ? "ok" : "bad");
    });

    card.querySelector('.clear').addEventListener('click', ()=>{
      card.querySelectorAll('input').forEach(i=>{ i.checked=false; });
      const fb = card.querySelector('.feedback');
      fb.textContent = "";
      fb.className = "feedback";
    });
  });
})();
</script>
<!-- =================== /End Breakout Blocks: Checkpoint Quizzes =================== -->
