<script>
  let round = $state(1);
  let score = $state(0);
  let phase = $state('math'); // 'math' or 'fishing'
  let catchLog = $state([]);

  // Math phase state
  let currentProblem = $state(null);
  let userAnswer = $state('');
  let mathMessage = $state('');
  let mathMessageType = $state('');
  let shaking = $state(false);

  // Fishing phase state
  let fishingState = $state('ready'); // 'ready', 'cast', 'waiting', 'bite', 'caught', 'missed'
  let fishCaught = $state(null);
  let biteTimeout = $state(null);
  let hookTimeout = $state(null);

  const fishEmojis = ['🐟', '🐠', '🐡', '🦈', '🐙', '🦑', '🦐', '🦞', '🐬', '🐳'];
  const fishNames = [
    'Glimmer', 'Bubbles', 'Captain Fins', 'Old Chompy',
    'The Silver Flash', 'Moonbeam', 'Tiny Terror',
    'Magnifico', 'Lord Splash', 'Zigzag'
  ];

  const encouragingMessages = [
    "Nice work, genius! 🎉",
    "You're on fire! 🔥",
    "Math wizard! ✨",
    "Brilliant! 🌟",
    "Nailed it! 💪",
    "Perfect! 🎯"
  ];

  const wrongMessages = [
    "Oops! Try again! 🤔",
    "Not quite... give it another shot! 💭",
    "So close! Think it through! 🧠",
    "Hmm, check your math! 📝",
    "Almost there! 🎲"
  ];

  function generateProblem(roundNum) {
    let num1, num2, operator, answer, question;

    if (roundNum <= 3) {
      // Simple addition/subtraction (1-10)
      num1 = Math.floor(Math.random() * 10) + 1;
      num2 = Math.floor(Math.random() * 10) + 1;
      operator = Math.random() > 0.5 ? '+' : '-';

      if (operator === '-' && num2 > num1) {
        [num1, num2] = [num2, num1];
      }

      answer = operator === '+' ? num1 + num2 : num1 - num2;
      question = `${num1} ${operator} ${num2} = ?`;

    } else if (roundNum <= 6) {
      // Multiplication and larger numbers (up to 20)
      num1 = Math.floor(Math.random() * 10) + 1;
      num2 = Math.floor(Math.random() * 10) + 1;
      const ops = ['+', '-', '×'];
      operator = ops[Math.floor(Math.random() * ops.length)];

      if (operator === '+') {
        num1 = Math.floor(Math.random() * 20) + 1;
        num2 = Math.floor(Math.random() * 20) + 1;
        answer = num1 + num2;
      } else if (operator === '-') {
        num1 = Math.floor(Math.random() * 20) + 10;
        num2 = Math.floor(Math.random() * num1);
        answer = num1 - num2;
      } else {
        answer = num1 * num2;
      }

      question = `${num1} ${operator} ${num2} = ?`;

    } else if (roundNum <= 9) {
      // Two-step problems
      num1 = Math.floor(Math.random() * 10) + 1;
      num2 = Math.floor(Math.random() * 10) + 1;
      const num3 = Math.floor(Math.random() * 10) + 1;

      answer = num1 * num2 + num3;
      question = `${num1} × ${num2} + ${num3} = ?`;

    } else {
      // Missing number problems
      const result = Math.floor(Math.random() * 20) + 10;
      const missing = Math.floor(Math.random() * 10) + 1;
      answer = missing;

      const ops = [
        { q: `? + ${result - missing} = ${result}`, a: missing },
        { q: `${result + missing} - ? = ${result}`, a: missing },
        { q: `? × ${Math.floor(result / missing)} = ${result}`, a: missing }
      ];

      const chosen = ops[Math.floor(Math.random() * Math.min(2, ops.length))];
      question = chosen.q;
      answer = chosen.a;
    }

    return { question, answer };
  }

  function startRound() {
    phase = 'math';
    currentProblem = generateProblem(round);
    userAnswer = '';
    mathMessage = '';
    mathMessageType = '';
  }

  function checkAnswer() {
    // userAnswer is already a number from type="number" input
    const answer = typeof userAnswer === 'number' ? userAnswer : parseInt(userAnswer);

    if (answer === currentProblem.answer) {
      mathMessage = encouragingMessages[Math.floor(Math.random() * encouragingMessages.length)];
      mathMessageType = 'success';
      score += 10;

      setTimeout(() => {
        phase = 'fishing';
        fishingState = 'ready';
        mathMessage = '';
      }, 1500);
    } else {
      mathMessage = wrongMessages[Math.floor(Math.random() * wrongMessages.length)];
      mathMessageType = 'error';
      shaking = true;

      setTimeout(() => {
        shaking = false;
        mathMessage = '';
      }, 800);

      userAnswer = '';
    }
  }

  function handleKeyPress(e) {
    if (e.key === 'Enter' && userAnswer !== '' && userAnswer !== null && userAnswer !== undefined) {
      checkAnswer();
    }
  }

  function castLine() {
    fishingState = 'cast';

    setTimeout(() => {
      fishingState = 'waiting';

      // Random delay between 1-3 seconds before fish bites
      const biteDelay = Math.random() * 2000 + 1000;

      biteTimeout = setTimeout(() => {
        fishingState = 'bite';

        // Player has 1.5 seconds to hook it
        hookTimeout = setTimeout(() => {
          fishingState = 'missed';

          setTimeout(() => {
            fishingState = 'ready';
          }, 2000);
        }, 1500);
      }, biteDelay);
    }, 500);
  }

  function hookFish() {
    clearTimeout(hookTimeout);
    fishingState = 'caught';

    // Generate random fish
    const fishEmoji = fishEmojis[Math.floor(Math.random() * fishEmojis.length)];
    const fishName = fishNames[Math.floor(Math.random() * fishNames.length)];

    fishCaught = { emoji: fishEmoji, name: fishName, round };
    catchLog = [...catchLog, fishCaught];
    score += 20;

    setTimeout(() => {
      round++;
      fishCaught = null;
      startRound();
    }, 2500);
  }

  function nextRound() {
    round++;
    startRound();
  }

  // Initialize first round
  startRound();
</script>

<div class="game-container">
  <div class="header">
    <h1>🎣 Math Fishing</h1>
    <div class="stats">
      <div class="stat">
        <span class="stat-label">Round</span>
        <span class="stat-value">{round}</span>
      </div>
      <div class="stat">
        <span class="stat-label">Score</span>
        <span class="stat-value">{score}</span>
      </div>
    </div>
  </div>

  <div class="main-area">
    <div class="game-phase">
      {#if phase === 'math'}
        <div class="phase-title">Solve to Unlock Fishing</div>
        <div class="math-equation" class:shake={shaking}>
          {currentProblem?.question}
        </div>

        <div class="input-group">
          <input
            type="number"
            bind:value={userAnswer}
            onkeypress={handleKeyPress}
            placeholder="Your answer"
            autofocus
          />
          <button onclick={checkAnswer} disabled={userAnswer === '' || userAnswer === null || userAnswer === undefined}>
            Submit
          </button>
        </div>

        {#if mathMessage}
          <div class="message {mathMessageType}">
            {mathMessage}
          </div>
        {/if}
      {:else if phase === 'fishing'}
        <div class="phase-title">Time to Fish!</div>

        {#if fishingState === 'ready'}
          <div class="water-container">
            <div class="water-shimmer"></div>
          </div>
          <button onclick={castLine}>🎣 Cast!</button>
        {:else if fishingState === 'cast'}
          <div class="water-container">
            <div class="water-shimmer"></div>
            <div class="bobber">🎣</div>
          </div>
          <p style="color: var(--text-muted);">Casting line...</p>
        {:else if fishingState === 'waiting'}
          <div class="water-container">
            <div class="water-shimmer"></div>
            <div class="bobber">🎣</div>
          </div>
          <p style="color: var(--text-muted);">Waiting for a bite...</p>
        {:else if fishingState === 'bite'}
          <div class="water-container">
            <div class="water-shimmer"></div>
            <div class="bobber bite">🎣</div>
          </div>
          <button class="hook-button" onclick={hookFish}>🪝 HOOK IT!</button>
        {:else if fishingState === 'caught'}
          <div class="fish-catch">{fishCaught?.emoji}</div>
          <div class="message success">
            You caught <strong>{fishCaught?.name}</strong>! 🎉
          </div>
        {:else if fishingState === 'missed'}
          <div class="water-container">
            <div class="water-shimmer"></div>
          </div>
          <div class="message error">
            The fish got away! 💨
          </div>
        {/if}
      {/if}
    </div>

    <div class="catch-log">
      <h2>🐠 Catch Log</h2>
      {#if catchLog.length === 0}
        <div class="empty-log">
          No catches yet. Solve math problems to start fishing!
        </div>
      {:else}
        <div class="catch-list">
          {#each catchLog.toReversed() as fish, i}
            <div class="catch-item">
              <span class="catch-item-emoji">{fish.emoji}</span>
              <div class="catch-item-info">
                <div class="catch-item-name">{fish.name}</div>
                <div class="catch-item-round">Round {fish.round}</div>
              </div>
            </div>
          {/each}
        </div>
      {/if}
    </div>
  </div>
</div>
