<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Educational Quiz Game</title>
  <style>
    body { font-family: Arial; background: #f9f9f9; padding: 20px; max-width: 700px; margin: auto; }
    h1, h2 { color: #333; }
    .hidden { display: none; }
    .question { margin: 20px 0; font-weight: bold; }
    .option { display: block; padding: 10px; margin: 5px 0; background: #e0e0e0; border: none; width: 100%; text-align: left; cursor: pointer; }
    .option:hover { background: #d0d0d0; }
    .leaderboard { margin-top: 30px; }
    .leaderboard ul { padding-left: 20px; }
    img { max-width: 100%; margin: 10px 0; }
  </style>
</head>
<body>

  <h1>Educational Quiz Game</h1>

  <div id="start-screen">
    <label>Enter your name:</label><br>
    <input type="text" id="player-name" /><br><br>
    <label>Select level:</label><br>
    <select id="level">
      <option value="easy">Easy</option>
      <option value="hard">Hard</option>
    </select><br><br>
    <button onclick="startGame()">Start Quiz</button>
  </div>

  <div id="quiz-container" class="hidden">
    <div id="question" class="question"></div>
    <img id="question-image" class="hidden" />
    <div id="options"></div>
  </div>

  <div id="result" class="hidden"></div>

  <div class="leaderboard">
    <h2>Top Scores</h2>
    <ul id="score-list"></ul>
    <button onclick="resetScores()">Reset Scores</button>
  </div>

  <script>
    const easyQuestions = [
      { question: "What is 2 + 3?", options: ["4", "5", "6", "7"], answer: "5" },
      { question: "What is the capital of Bangladesh?", options: ["Chittagong", "Dhaka", "Cox's Bazar", "Rangpur"], answer: "Dhaka" },
      { question: "Which animal is known as man's best friend?", options: ["Cat", "Dog", "Cow", "Rabbit"], answer: "Dog", image: "https://upload.wikimedia.org/wikipedia/commons/6/6e/Golde33443.jpg" }
    ];

    const hardQuestions = [
      { question: "What is the square root of 81?", options: ["8", "9", "7", "6"], answer: "9" },
      { question: "Who developed the theory of relativity?", options: ["Newton", "Galileo", "Einstein", "Tesla"], answer: "Einstein" },
      { question: "What is the chemical symbol for water?", options: ["O", "H", "H2O", "CO2"], answer: "H2O", image: "https://upload.wikimedia.org/wikipedia/commons/2/23/Water_molecule_3D.svg" }
    ];

    let questions = [];
    let current = 0;
    let score = 0;
    let playerName = "";

    const questionEl = document.getElementById("question");
    const optionsEl = document.getElementById("options");
    const resultEl = document.getElementById("result");
    const scoreListEl = document.getElementById("score-list");
    const imageEl = document.getElementById("question-image");

    function startGame() {
      playerName = document.getElementById("player-name").value.trim();
      const level = document.getElementById("level").value;
      if (!playerName) {
        alert("Please enter your name.");
        return;
      }

      questions = level === "easy" ? easyQuestions : hardQuestions;
      questions.sort(() => Math.random() - 0.5);

      document.getElementById("start-screen").classList.add("hidden");
      document.getElementById("quiz-container").classList.remove("hidden");

      current = 0;
      score = 0;
      loadQuestion();
    }

    function loadQuestion() {
      if (current >= questions.length) return endGame();

      const q = questions[current];
      questionEl.textContent = `Q${current + 1}: ${q.question}`;
      optionsEl.innerHTML = "";

      // Show image if available
      if (q.image) {
        imageEl.src = q.image;
        imageEl.classList.remove("hidden");
      } else {
        imageEl.classList.add("hidden");
      }

      q.options.forEach(option => {
        const btn = document.createElement("button");
        btn.textContent = option;
        btn.className = "option";
        btn.onclick = () => {
          if (option === q.answer) score++;
          current++;
          loadQuestion();
        };
        optionsEl.appendChild(btn);
      });
    }

    function endGame() {
      document.getElementById("quiz-container").classList.add("hidden");
      resultEl.classList.remove("hidden");
      resultEl.innerHTML = `<h2>Well done, ${playerName}!</h2><p>Your score: ${score}/${questions.length}</p>`;
      saveScore(playerName, score);
      showScores();
    }

    function saveScore(name, score) {
      const scores = JSON.parse(localStorage.getItem("quizScores") || "[]");
      scores.push({ name, score });
      scores.sort((a, b) => b.score - a.score);
      localStorage.setItem("quizScores", JSON.stringify(scores.slice(0, 5))); // Keep top 5
    }

    function showScores() {
      const scores = JSON.parse(localStorage.getItem("quizScores") || "[]");
      scoreListEl.innerHTML = "";
      scores.forEach(entry => {
        const li = document.createElement("li");
        li.textContent = `${entry.name}: ${entry.score}`;
        scoreListEl.appendChild(li);
      });
    }

    function resetScores() {
      localStorage.removeItem("quizScores");
      showScores();
    }

    showScores(); // Show scores on page load
  </script>

</body>
</html>
