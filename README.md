<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Happy Valentine's Day!</title>
  <link rel="stylesheet" href="styles.css" />
</head>
<body>
  <div class="container">
    <h1>💖 Happy Valentine's Day! 💖</h1>
    <p>Dear <strong class="name">Ava</strong>,</p>
    <p>Wishing you and your friend Amiee a wonderful day hanging out together.</p>
    <p>Hope your Valentine’s Day is full of smiles and sweet moments—you deserve all the happiness 💌</p>
    <footer>❤️ From someone who thinks you're awesome</footer>

    <!-- game area -->
    <div id="game">
      <p>Click the falling hearts to collect them!</p>
      <div id="score">Score: 0</div>
    </div>
  </div>

  <script>
    // falling heart game
    const game = document.getElementById('game');
    const scoreEl = document.getElementById('score');
    let score = 0;

    function createHeart() {
      const heart = document.createElement('div');
      heart.className = 'heart';
      heart.textContent = '❤️';
      heart.style.left = Math.random() * 90 + 'vw';
      heart.style.animationDuration = 3 + Math.random() * 2 + 's';
      heart.addEventListener('click', () => {
        score += 1;
        scoreEl.textContent = 'Score: ' + score;
        heart.remove();
      });
      game.appendChild(heart);
      setTimeout(() => heart.remove(), 6000);
    }

    setInterval(createHeart, 800);

    // randomly add an emoji floating
    const emojis = ['😍','🥰','😻','🌹','🍫'];
    function createEmoji() {
      const emo = document.createElement('div');
      emo.className = 'emoji';
      emo.textContent = emojis[Math.floor(Math.random()*emojis.length)];
      emo.style.left = Math.random() * 90 + 'vw';
      game.appendChild(emo);
      setTimeout(() => emo.remove(), 4000);
    }
    setInterval(createEmoji, 1500);

    // background flying hearts
    function createBackgroundHeart() {
      const h = document.createElement('div');
      h.className = 'background-heart';
      h.textContent = '❤️';
      h.style.left = Math.random() * 100 + 'vw';
      h.style.animationDuration = 4 + Math.random() * 3 + 's';
      document.body.appendChild(h);
      setTimeout(() => h.remove(), 8000);
    }
    setInterval(createBackgroundHeart, 700);
  </script>
</body>
</html>
