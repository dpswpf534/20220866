# 20220866
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>My P5 Dream Gallery</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.6.0/p5.min.js"></script>

<style>
body {
  margin: 0;
  background: linear-gradient(180deg, #faf9ff, #eeeaff);
  font-family: 'Pretendard', sans-serif;
  overflow: hidden;
}
header {
  width: 100%;
  text-align: center;
  padding: 20px 0;
  font-size: 28px;
  font-weight: 600;
  color: #6d4aff;
  text-shadow: 0 0 8px rgba(109, 74, 255, 0.4);
  letter-spacing: 2px;
  position: fixed;
  top: 0;
}
#canvas-wrap {
  margin-top: 80px;
  width: 100%;
  display: flex;
  justify-content: center;
}
</style>
</head>

<body>
<header>✨ MY P5 DREAM GALLERY ✨</header>
<div id="canvas-wrap"></div>

<script>
// -------------------------------------------------------------
// ✔ 글로벌 변수
// -------------------------------------------------------------
let stars = [];
let cx = 300, cy = 210;

let dragging = false, offsetX = 0, offsetY = 0;

let smile = false;
let blink = 0;
let blinkTimer = 0;
let shirtBright = 0;
let bangLong = true;
let glassesOn = true;
let heartTimer = 0;
let shineTimer = 0;
let swingBoost = 0;
let demoMode = false;
let demoStart = 0;

let walkPhase = 0;  
let walkAmpX = 20;  
let bobAmpY = 10;   

// -------------------------------------------------------------
// ✔ setup
// -------------------------------------------------------------
function setup() {
  let canvas = createCanvas(600, 400);
  canvas.parent("canvas-wrap");

  // 별 생성
  for (let i = 0; i < 70; i++) {
    stars.push({
      x: random(width),
      y: random(160),
      size: random(2, 4)
    });
  }
}

// -------------------------------------------------------------
// ✔ draw 루프
// -------------------------------------------------------------
function draw() {
  background(240, 238, 255);

  let parallax = sin(frameCount * 0.01) * 8;

  drawBackground(parallax);
  drawNeon(parallax);

  walkPhase += 0.05;

  let headDY = 6 * sin(walkPhase * 2);

  drawCharacter(cx, cy, headDY);

  if (heartTimer > 0) {
    heartTimer--;
    drawHeart(cx + 40, cy - 30, 14, color(255, 140, 170));
  }

  if (blink && frameCount - blinkTimer > 5) blink = 0;
  if (shineTimer > 0) shineTimer--;
}

// -------------------------------------------------------------
// ✔ 배경(과제 1 + 4) -----------------------------------------------------
function drawBackground(px) {
  noStroke();

  fill(135, 180, 255, 180);
  ellipse(200 + px, 180, 180, 180);

  fill(255, 120, 120, 170);
  rect(360 + px, 120, 180, 130, 15);

  fill(120, 220, 160, 160);
  triangle(230 + px, 300, 380 + px, 300, 305 + px, 220);

  fill(255, 220, 100);
  ellipse(520 + px, 60, 40, 40);

  // 별
  for (let s of stars) {
    let flicker = random(0.6, 1);
    fill(255, 255, 255, 180 * flicker);
    ellipse(s.x, s.y, s.size * flicker, s.size * flicker);
  }
}

// -------------------------------------------------------------
// ✔ 네온 요소(과제 4) -----------------------------------------------------
function drawNeon(px) {
  let t = frameCount * 0.02;

  let c1 = color(255, 180, 140);
  let c2 = color(180, 200, 255);
  let mix = sin(t) * 0.5 + 0.5;

  fill(lerpColor(c1, c2, mix));
  ellipse(300 + px, 230, 240 + sin(t) * 8, 240 + sin(t) * 8);

  fill(lerpColor(c2, c1, mix));
  ellipse(330 + px, 250, 170 + cos(t) * 8, 170 + cos(t) * 8);

  let up1 = sin(t * 1.5) * 8;
  fill(140, 140, 255, 80);
  rect(350 + px, 240 + up1, 30, 130, 10);

  let up2 = sin(t * 1.9) * 10;
  fill(255, 140, 200, 80);
  rect(390 + px, 220 + up2, 30, 160, 10);

  let up3 = sin(t * 1.2) * 6;
  fill(120, 220, 200, 80);
  rect(430 + px, 260 + up3, 30, 110, 10);

  stroke(200, 100, 255, 80);
  strokeWeight(10);
  noFill();
  arc(300 + px, 360, 480, 180, PI, TWO_PI);

  stroke(100, 180, 255, 80);
  strokeWeight(7);
  arc(320 + px, 380, 420, 150, PI, TWO_PI - 0.4);
}

// -------------------------------------------------------------
// ✔ 캐릭터 — 몸 + 얼굴 + 장식 -----------------------------------------------------
function drawCharacter(bx, by, headDY) {
  const SKIN = color(250, 224, 205);
  const HAIR = color(55, 40, 30);
  const SHIRT_BASE = color(135, 206, 250);

  let walkX = walkAmpX * sin(walkPhase);
  let walkY = bobAmpY * sin(walkPhase * 2);

  let x = bx + walkX;
  let y = by + walkY;

  let SHIRT = color(
    red(SHIRT_BASE) + shirtBright,
    green(SHIRT_BASE) + shirtBright,
    blue(SHIRT_BASE) + shirtBright
  );

  noStroke();
  fill(HAIR);
  ellipse(x, y - 30 + headDY, 260, 320);

  stroke(90, 70, 70, 150);
  strokeWeight(2);
  fill(SKIN);
  ellipse(x, y - 40 + headDY, 160, 200);

  noStroke();
  fill(HAIR);
  let bH = bangLong ? 70 : 45;
  rect(x - 90, y - 160 + headDY, 180, bH, 20);
  arc(x, y - 160 + bH + headDY, 180, 80, PI, TWO_PI);

  fill(SKIN);
  rect(x - 16, y + 20, 32, 26, 8);

  fill(SHIRT);
  rect(x - 120, y + 60, 240, 120, 20);

  fill(255);
  for (let i = 0; i < 4; i++) ellipse(x, y + 80 + i * 28, 8, 8);

  // 귀
  fill(SKIN);
  ellipse(x - 85, y - 40 + headDY, 18, 28);
  ellipse(x + 85, y - 40 + headDY, 18, 28);

  // 귀걸이
  drawFlower(x - 85, y - 15 + headDY, 7);
  drawFlower(x + 85, y - 15 + headDY, 7);

  // 눈
  fill(0);
  let eyeH = blink ? 3 : 30;
  ellipse(x - 40, y - 70 + headDY, 40, eyeH);
  ellipse(x + 40, y - 70 + headDY, 40, eyeH);

  fill(255);
  ellipse(x - 48, y - 75 + headDY, 6, 6);
  ellipse(x + 32, y - 75 + headDY, 6, 6);

  if (glassesOn) {
    noFill();
    stroke(40);
    strokeWeight(2);
    ellipse(x - 40, y - 70 + headDY, 60, 40);
    ellipse(x + 40, y - 70 + headDY, 60, 40);
    line(x - 10, y - 70 + headDY, x + 10, y - 70 + headDY);
  }

  noStroke();
  fill(255, 150, 170);
  ellipse(x - 65, y - 40 + headDY, 16, 16);
  ellipse(x + 65, y - 40 + headDY, 16, 16);

  fill(220, 80, 110);
  if (smile) arc(x, y - 25 + headDY, 40, 20, 0, PI);
  else rect(x - 16, y - 30 + headDY, 32, 10, 6);
}

// -------------------------------------------------------------
// ✔ 꽃귀걸이
// -------------------------------------------------------------
function drawFlower(x, y, r) {
  fill(255, 200, 200);
  for (let i = 0; i < 5; i++) {
    let a = TWO_PI * i / 5;
    ellipse(x + cos(a) * r, y + sin(a) * r, r, r);
  }
  fill(255, 180, 0);
  ellipse(x, y, r, r);
}

// -------------------------------------------------------------
// ✔ 하트
// -------------------------------------------------------------
function drawHeart(x, y, s, c) {
  noStroke();
  fill(c);
  ellipse(x - s * 0.5, y, s, s);
  ellipse(x + s * 0.5, y, s, s);
  triangle(x - s, y, x + s, y, x, y + s * 1.4);
}

// -------------------------------------------------------------
// ✔ 인터랙션
// -------------------------------------------------------------
function mousePressed() {
  let d = dist(mouseX, mouseY, cx, cy);
  if (d < 140) {
    dragging = true;
    offsetX = cx - mouseX;
    offsetY = cy - mouseY;
  }
  smile = !smile;
  heartTimer = 20;
}

function mouseDragged() {
  if (dragging) {
    cx = mouseX + offsetX;
    cy = mouseY + offsetY;
  }
}

function mouseReleased() {
  dragging = false;
}

function doubleClicked() {
  glassesOn = !glassesOn;
}

function keyPressed() {
  if (key === ' ') { blink = 1; blinkTimer = frameCount; }
  if (key === 'h' || key === 'H') heartTimer = 30;
  if (key === 'b' || key === 'B') bangLong = !bangLong;
  if (key === 's') saveGif("p5_gallery.gif", 10);
  if (keyIsDown(SHIFT) && key === 'S') saveCanvas('dream_gallery', 'png');
}

</script>
</body>
</html>
