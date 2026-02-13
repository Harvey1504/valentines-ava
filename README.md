<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Happy Valentine's Day!</title>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      /* pink to red fade */
      background: linear-gradient(135deg, #ff9a9e 0%, #fad0c4 50%, #ff6b81 100%);
      color: #333;
      margin: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      height: 100vh;
      overflow: hidden;
      position: relative;
    }

    /* flying hearts in background */
    @keyframes floatUp {
      0% { transform: translateY(100vh) scale(0.5); opacity: 0; }
      50% { opacity: 1; }
      100% { transform: translateY(-20vh) scale(1.2); opacity: 0; }
    }

    .background-heart {
      position: absolute;
      font-size: 3rem;
      color: rgba(255, 255, 255, 0.7);
      animation: floatUp 5s linear infinite;
    }

    .container {
      text-align: center;
      background: white;
      padding: 2rem;
      border-radius: 10px;
      box-shadow: 0 0 20px rgba(0,0,0,0.1);
      max-width: 600px;
      position: relative;
    }

    .name {
      color: #cc0000;
      font-weight: bold;
    }

    #game {
      margin-top: 20px;
      height: 200px;
      position: relative;
      overflow: hidden;
      border: 2px dashed #ff6b81;
      border-radius: 10px;
      background: #fff0f0;
    }

    #score {
      font-size: 1.2em;
      margin-top: 5px;
      color: #cc0000;
    }

    .heart {
      position: absolute;
      font-size: 2rem;
      cursor: pointer;
      animation: fall 4s linear forwards;
    }

    .emoji {
      position: absolute;
      font-size: 2rem;
      animation: float 3s ease-in-out forwards;
    }

    @keyframes float {
      0% { transform: translateY(0); opacity:1; }
      100% { transform: translateY(-150px); opacity:0; }
    }

    h1 {
      color: #cc0000;
      margin-bottom: 1rem;
    }

    footer {
      margin-top: 2rem;
      font-size: 0.9rem;
      color: #555;
    }
  </style>
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
