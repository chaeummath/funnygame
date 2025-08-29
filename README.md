# funnygame

<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>카드 뒤집기 게임</title>
  <style>
    body { font-family: Arial, sans-serif; text-align: center; background: #f4f4f4; }
    h1 { margin: 20px; }
    .controls { margin: 20px; }
    select, button { padding: 10px; margin: 5px; }
    #game-board { display: grid; gap: 10px; justify-content: center; margin-top: 20px; }
    .card {
      width: 100px; height: 120px;
      background: #4CAF50; color: white;
      display: flex; justify-content: center; align-items: center;
      font-size: 18px; cursor: pointer;
      border-radius: 10px;
      user-select: none;
    }
    .flipped, .matched { background: #fff; color: #000; cursor: default; }
    .scoreboard { margin-top: 20px; }
    .player-score { margin: 5px; font-size: 18px; }
    .active { font-weight: bold; color: red; }
  </style>
</head>
<body>
  <h1>카드 뒤집기 게임</h1>
  <div class="controls">
    <label>카드 개수 선택: </label>
    <select id="card-count">
      <option value="16">16개 (4x4)</option>
      <option value="24">24개 (4x6)</option>
    </select>
    <label>플레이어 수 선택: </label>
    <select id="player-count">
      <option value="2">2명</option>
      <option value="3">3명</option>
      <option value="4">4명</option>
      <option value="5">5명</option>
      <option value="6">6명</option>
    </select>
    <button onclick="startGame()">게임 시작</button>
  </div>

  <div id="game-board"></div>
  <div class="scoreboard" id="scoreboard"></div>

  <script>
    const words = [
      {en: "apple", ko: "사과"},
      {en: "book", ko: "책"},
      {en: "dog", ko: "개"},
      {en: "cat", ko: "고양이"},
      {en: "car", ko: "자동차"},
      {en: "house", ko: "집"},
      {en: "water", ko: "물"},
      {en: "sun", ko: "태양"},
      {en: "moon", ko: "달"},
      {en: "star", ko: "별"},
      {en: "flower", ko: "꽃"},
      {en: "tree", ko: "나무"}
    ];

    let gameBoard, cardCount, playerCount, scores, currentPlayer;
    let flippedCards = [];

    function startGame() {
      cardCount = parseInt(document.getElementById('card-count').value);
      playerCount = parseInt(document.getElementById('player-count').value);
      gameBoard = document.getElementById('game-board');
      gameBoard.innerHTML = '';
      flippedCards = [];

      // 보드 크기 설정
      gameBoard.style.gridTemplateColumns = `repeat(${cardCount === 16 ? 4 : 6}, 100px)`;

      // 단어 섞기
      let selected = words.slice(0, cardCount/2);
      let cards = [];
      selected.forEach(w => {
        cards.push({text: w.en, pair: w.ko});
        cards.push({text: w.ko, pair: w.en});
      });
      cards.sort(() => Math.random() - 0.5);

      // 카드 생성
      cards.forEach((c, i) => {
        let card = document.createElement('div');
        card.className = 'card';
        card.dataset.text = c.text;
        card.dataset.pair = c.pair;
        card.dataset.index = i;
        card.innerText = '';
        card.onclick = () => flipCard(card);
        gameBoard.appendChild(card);
      });

      // 점수판 초기화
      scores = Array(playerCount).fill(0);
      currentPlayer = 0;
      updateScoreboard();
    }

    function flipCard(card) {
      if (card.classList.contains('flipped') || card.classList.contains('matched')) return;
      if (flippedCards.length === 2) return;

      card.classList.add('flipped');
      card.innerText = card.dataset.text;
      flippedCards.push(card);

      if (flippedCards.length === 2) {
        setTimeout(checkMatch, 800);
      }
    }

    function checkMatch() {
      let [c1, c2] = flippedCards;
      if (c1.dataset.pair === c2.dataset.text && c2.dataset.pair === c1.dataset.text) {
        c1.classList.add('matched');
        c2.classList.add('matched');
        scores[currentPlayer]++;
        updateScoreboard();
        flippedCards = [];
        return; // 정답 맞추면 턴 유지
      } else {
        c1.classList.remove('flipped');
        c2.classList.remove('flipped');
        c1.innerText = '';
        c2.innerText = '';
        flippedCards = [];
        currentPlayer = (currentPlayer + 1) % playerCount;
        updateScoreboard();
      }
    }

    function updateScoreboard() {
      let board = document.getElementById('scoreboard');
      board.innerHTML = '';
      scores.forEach((score, i) => {
        let div = document.createElement('div');
        div.className = 'player-score' + (i === currentPlayer ? ' active' : '');
        div.innerText = `플레이어 ${i+1}: ${score}점`;
        board.appendChild(div);
      });
    }
  </script>
</body>
</html>
