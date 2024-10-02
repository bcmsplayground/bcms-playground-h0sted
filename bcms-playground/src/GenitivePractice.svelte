<script>
  export let goBackToMain; // Function passed from App.svelte
  // Constants for grammatical number and gender
  const MASCULINE = "masculine";
  const FEMININE = "feminine";
  const SINGULAR = "singular";
  const PLURAL = "plural";

  // Constants for feedback
  const CORRECT = "correct";
  const INCORRECT = "incorrect";

  // List of sentences
  let sentences = [
    {
      sentence: "Molim vas, pitu od sir_.",
      nominative: "sir",
      answer: "a",
      grammaticalNumber: SINGULAR,
      gender: MASCULINE,
    },
    {
      sentence: "Oprostite, može li sok od jabuk_.",
      answer: "e",
      nominative: "jabuka",
      grammaticalNumber: SINGULAR,
      gender: FEMININE,
    },
  ];

  // Track the remaining sentences and answered questions
  let remainingSentences = [...sentences];
  let answeredCorrectly = 0;
  let totalQuestions = sentences.length;

  // Track first try correctness
  let firstTry = true;

  // Initialize the first sentence
  let currentIndex = Math.floor(Math.random() * remainingSentences.length);
  let currentSentence = remainingSentences[currentIndex];

  // Track user's answer and feedback
  let selectedAnswer = null;
  let feedback = "";

  // Array of choices for genitive case (restricted to 'e' and 'a')
  let vowelChoices = ["a", "e"];

  // Array of cute phrases for the restart button
  let restartPhrases = [
    "Go Again? For me? 😊",
    "One More Time? 😘",
    "How about another round? 💖",
    "Shall we go again? 💫",
  ];

  // Randomly select a cute restart phrase
  let selectedRestartPhrase =
    restartPhrases[Math.floor(Math.random() * restartPhrases.length)];

  // Track animation state
  let transitioning = false;

  // Function to handle user's choice
  function chooseAnswer(choice) {
    selectedAnswer = choice;

    // Check if the choice matches the correct answer
    if (choice === currentSentence.answer) {
      feedback = CORRECT;
      // Increment counter only if correct on first try
      if (firstTry) {
        answeredCorrectly += 1;
      }
      setTimeout(() => {
        transitionToNext();
      }, 1000); // Move to the next sentence after 1 second
    } else {
      feedback = INCORRECT;
      firstTry = false; // User guessed wrong, disable first try bonus
    }
  }

  // Animate the transition to the next sentence
  function transitionToNext() {
    transitioning = true;
    setTimeout(() => {
      nextSentence();
      transitioning = false;
    }, 500); // Half-second slide animation
  }

  // Move to the next sentence and ensure no repeats
  function nextSentence() {
    feedback = "";
    selectedAnswer = null;
    firstTry = true; // Reset first try for new question

    // Remove the current sentence after answering
    remainingSentences.splice(currentIndex, 1);

    // Check if there are remaining sentences
    if (remainingSentences.length > 0) {
      // Pick a new random sentence
      currentIndex = Math.floor(Math.random() * remainingSentences.length);
      currentSentence = remainingSentences[currentIndex];
    } else {
      // No more sentences available (game is finished)
      currentSentence = null;
      // Randomly select a new cute restart phrase for the button
      selectedRestartPhrase =
        restartPhrases[Math.floor(Math.random() * restartPhrases.length)];
    }
  }

  // Function to restart the quiz
  function restartQuiz() {
    remainingSentences = [...sentences];
    answeredCorrectly = 0;
    currentIndex = Math.floor(Math.random() * remainingSentences.length);
    currentSentence = remainingSentences[currentIndex];
  }

  // Reactive statement to update the sentence parts whenever the sentence changes
  $: parts = currentSentence
    ? getSentenceParts(currentSentence.sentence)
    : { beforeBlank: "", afterBlank: "" };

  // Helper function to split sentence into parts
  function getSentenceParts(sentence) {
    const [beforeBlank, afterBlank] = sentence.split("_");
    return { beforeBlank, afterBlank };
  }
</script>

<!-- Permanent gradient header for the page -->
<div class="page-header">Genitive Practice</div>

<!-- Counter in the top right corner -->
<div class="counter">
  {answeredCorrectly}/{totalQuestions} correct
</div>

<!-- Main container for centering -->
<div class="container">
  <!-- Header with instructions -->
  {#if currentSentence}
    <h3>
      Complete the genitive form of <span class="header-word"
        >'{currentSentence.nominative}'</span
      >. It is a {currentSentence.gender} noun in its {currentSentence.grammaticalNumber}
      form.
    </h3>

    <!-- Add classes for sliding out/in based on transition state -->
    <div class="sentence-box {transitioning ? 'slide-out' : 'default'}">
      <!-- Render sentence with dynamic answer and color -->
      <p>
        {parts.beforeBlank}<span
          class={feedback === INCORRECT ? "wrong-answer" : ""}
          >{selectedAnswer ? selectedAnswer : "_"}</span
        >{parts.afterBlank}
      </p>
    </div>

    <div class="choices">
      {#each vowelChoices as choice}
        <button
          class="choice-button {feedback === CORRECT &&
          choice === currentSentence.answer
            ? 'correct'
            : ''} {feedback === INCORRECT && selectedAnswer === choice
            ? 'incorrect shake'
            : ''}"
          on:click={() => chooseAnswer(choice)}
          disabled={feedback === CORRECT}
        >
          {choice}
        </button>
      {/each}
    </div>
  {:else}
    <!-- Message when all questions have been answered -->
    <h3>Well done! You've completed all the questions.</h3>

    <!-- Cutesy Restart Button with Random Phrase -->
    <button class="restart-button" on:click={restartQuiz}>
      {selectedRestartPhrase}
    </button>
  {/if}
  <button class="back-button" on:click={goBackToMain}>
    Back to Main Page
  </button>
</div>

<style>
  /* Full-width banner for the header */
  .page-header {
    width: 100%; /* Make sure it takes the full width */
    background: linear-gradient(to right, #ff9a9e, #fecfef);
    text-align: center;
    padding: 1em 0;
    font-size: 2em;
    font-weight: bold;
    color: #fff;
    border-radius: 0 0 10px 10px;
    margin-bottom: 20px; /* Add some space below the header */
  }

  /* Styling for the back button */
  .back-button {
    background-color: #ffcbcb;
    color: #ff6969;
    font-size: 1em;
    font-weight: bold;
    padding: 0.75em 1.5em;
    border-radius: 8px;
    cursor: pointer;
    transition:
      transform 0.2s,
      background-color 0.2s;
    margin-top: 20px; /* Space below the practice section */
  }

  .back-button:hover {
    background-color: #ffe4e4;
    transform: scale(1.05);
  }

  .back-button:active {
    background-color: #ffabab;
    transform: scale(0.95);
  }

  /* Center align everything */
  .container {
    text-align: center;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  /* Center the sentence box properly */
  .sentence-box {
    border: 1px solid #ffc8dd;
    background-color: #ffefff;
    padding: 1.5em;
    margin: 1.5em 0;
    border-radius: 12px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    width: 80%; /* Adjust width for centering */
    max-width: 600px;
    transition: transform 0.5s ease-out;
  }

  /* Default class to ensure the box is centered when no transition is happening */
  .default {
    transform: translateX(0);
  }

  /* Slide out to the left */
  .slide-out {
    transform: translateX(-100%);
  }

  /* Slide in from the right */
  .slide-in {
    transform: translateX(100%);
  }

  .choices {
    display: flex;
    gap: 10px;
    justify-content: center;
  }

  .choice-button {
    padding: 10px;
    border-radius: 5px;
    cursor: pointer;
    border: 1px solid #ffc8dd;
    background-color: #ffcbcb;
    transition:
      transform 0.2s,
      background-color 0.2s;
  }

  .choice-button:hover {
    background-color: #ffe4e4;
  }

  .correct {
    background-color: #28a745;
    color: white;
  }

  .incorrect {
    background-color: #dc3545;
    color: white;
  }

  .shake {
    animation: shake 0.5s;
  }

  @keyframes shake {
    0% {
      transform: translateX(0);
    }
    25% {
      transform: translateX(-5px);
    }
    50% {
      transform: translateX(5px);
    }
    75% {
      transform: translateX(-5px);
    }
    100% {
      transform: translateX(0);
    }
  }

  /* Softer colors for the header */
  h3 {
    color: #7f8c8d; /* A soft gray */
    font-weight: normal;
  }

  /* Complementary color for the nominative word */
  .header-word {
    color: #ff6f61; /* Soft coral pink to complement the header text */
    font-weight: bold;
  }

  /* If the user guessed wrong, color the answer red */
  .wrong-answer {
    color: red;
  }

  .counter {
    position: absolute;
    top: 10px;
    right: 10px;
    font-size: 1.2em;
    font-weight: bold;
  }

  /* Pastel and Cutesy Styling for the Restart Button */
  .restart-button {
    background-color: #ffcbcb;
    border: none;
    color: #ff6969;
    font-size: 1.2em;
    font-weight: bold;
    padding: 0.75em 1.5em;
    border-radius: 12px;
    cursor: pointer;
    transition:
      transform 0.2s,
      background-color 0.2s;
    margin-top: 20px;
  }

  .restart-button:hover {
    background-color: #ffe4e4;
    transform: scale(1.05);
  }

  .restart-button:active {
    background-color: #ffabab;
    transform: scale(0.95);
  }
</style>
