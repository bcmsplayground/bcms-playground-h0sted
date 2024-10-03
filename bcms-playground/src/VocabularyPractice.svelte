<script>
  export let goBackToMain;

  // Sample data for vocabulary sets
  const VOCABULARY_SETS = {
    ADJECTIVES_2_JSON: [
      { english: "Few", latin: "Nekolicini" },
      { english: "Public", latin: "Državni" },
      { english: "Bad", latin: "Loš" },
      { english: "Able", latin: "Sposoban" },
      { english: "Specific", latin: "Naročit" },
      { english: "General", latin: "Opšti" },
      { english: "Certain", latin: "Siguran" },
    ],
    ADJECTIVES_3_JSON: [
      { english: "Common", latin: "Uobičajen" },
      { english: "Poor", latin: "Jadan" },
      { english: "Major", latin: "Glavan" },
      { english: "Happy", latin: "Sretan" },
      { english: "Serious", latin: "Ozbiljan" },
    ],
  };

  const ENGLISH = "english";
  const BCMS = "bcms";

  let useCyrillic = false; // Toggle between Latin and Cyrillic scripts
  let currentLang = ENGLISH; // Toggle between English and BCMS (Latin/Cyrillic)
  let selectedSet = VOCABULARY_SETS["ADJECTIVES_2_JSON"]; // Default set

  const selectSet = (set) => {
    selectedSet = set;
  };

  // Track flip states for each card
  let flipStates = Array(selectedSet.length).fill(ENGLISH);

  // Cyrillic map for conversion
  const JUGOSLAV_SINGLE_CHARACTER_MAP = {
    A: "А",
    a: "а",
    B: "Б",
    b: "б",
    V: "В",
    v: "в",
    G: "Г",
    g: "г",
    D: "Д",
    d: "д",
    Đ: "Ђ",
    đ: "ђ",
    E: "Е",
    e: "е",
    Ž: "Ж",
    ž: "ж",
    Z: "З",
    z: "з",
    I: "И",
    i: "и",
    J: "Ј",
    j: "ј",
    K: "К",
    k: "к",
    L: "Л",
    l: "л",
    M: "М",
    m: "м",
    N: "Н",
    n: "н",
    O: "О",
    o: "о",
    P: "П",
    p: "п",
    R: "Р",
    r: "р",
    S: "С",
    s: "с",
    T: "Т",
    t: "т",
    Ć: "Ћ",
    ć: "ћ",
    U: "У",
    u: "у",
    F: "Ф",
    f: "ф",
    H: "Х",
    h: "х",
    C: "Ц",
    c: "ц",
    Č: "Ч",
    č: "ч",
    Š: "Ш",
    š: "ш",
  };

  // Function to convert Latin to Cyrillic
  const latinToCyrillic = (word) => {
    return word
      .split("")
      .map((char) => JUGOSLAV_SINGLE_CHARACTER_MAP[char] || char)
      .join("");
  };

  // Toggle script between Latin and Cyrillic
  const toggleScript = () => {
    useCyrillic = !useCyrillic;
  };

  // Toggle language between English and BCMS
  const toggleLanguage = () => {
    currentLang = currentLang === ENGLISH ? BCMS : ENGLISH;
    resetAllCardsToStartLanguage(currentLang);
  };

  // Reset all cards to starting language
  const resetAllCardsToStartLanguage = (language) => {
    flipStates = Array(selectedSet.length).fill(language); // Reset all cards
  };

  // Flip a card
  const flipCard = (index) => {
    flipStates[index] = flipStates[index] === ENGLISH ? BCMS : ENGLISH;
  };

  // Determine content for each side of the card
  const getCardContent = (word, language) => {
    const cyrillicForm = latinToCyrillic(word.latin);
    const bcmsForm = useCyrillic ? cyrillicForm : word.latin;
    return (word = language === ENGLISH ? word.english : bcmsForm);
  };
</script>

<!-- Layout and UI -->
<div class="vocab-container">
  <div class="header">
    <h1>Vocabulary Practice</h1>

    <!-- Options Buttons -->
    <div class="header-options">
      <button on:click={toggleScript}>
        {useCyrillic ? "Switch to Latin" : "Switch to Cyrillic"}
      </button>

      <button on:click={toggleLanguage}>
        {(currentLang =
          currentLang === ENGLISH ? "English to BCMS" : "BCMS to English")}
      </button>

      <select
        on:change={(event) => selectSet(VOCABULARY_SETS[event.target.value])}
      >
        {#each Object.keys(VOCABULARY_SETS) as title}
          <option value={title}>{title}</option>
        {/each}
      </select>
    </div>
  </div>

  <!-- Flashcards -->
  <div class="flashcards">
    {#each selectedSet as word, index}
      <div class="flashcard" on:click={() => flipCard(index)}>
        <div class="flashcard-inner">
          <!-- Front side of the card -->
          <div
            class="flashcard-front"
            class:hidden={flipStates[index] === currentLang}
          >
            {getCardContent(word, flipStates[index])}
          </div>
          <!-- Back side of the card -->
          <div
            class="flashcard-back"
            class:hidden={flipStates[index] !== currentLang}
          >
            {getCardContent(word, flipStates[index])}
          </div>
        </div>
      </div>
    {/each}
  </div>

  <!-- Return to Main Page button -->
  <button class="back-button" on:click={goBackToMain}>
    Return to Main Page
  </button>
</div>

<!-- Styles -->
<style>
  .vocab-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-start;
    height: 100vh;
    background-color: white;
    padding-top: 180px;
  }

  .header {
    display: flex;
    justify-content: center;
    width: 100%;
    background-color: #ff9a9e;
    padding: 20px 0;
    position: fixed;
    top: 0;
    left: 0;
    z-index: 2;
  }

  .header-options {
    position: absolute;
    right: 20px;
    top: 20px;
    display: flex;
    gap: 10px;
  }

  .flashcards {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    justify-content: center;
  }

  .flashcard {
    width: 150px;
    height: 150px;
    background-color: #ffefff;
    border: 2px solid #ffc8dd;
    perspective: 1000px;
  }

  .flashcard-inner {
    width: 100%;
    height: 100%;
    position: relative;
    text-align: center;
    transition: transform 0.6s;
    transform-style: preserve-3d;
  }

  .flipped {
    transform: rotateY(180deg);
  }

  .flashcard-front,
  .flashcard-back {
    position: absolute;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.2em;
    font-weight: bold;
  }

  .flashcard-back {
    transform: rotateY(180deg); /* Flip the back side */
  }

  .hidden {
    display: none;
  }

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
    margin-top: 20px;
  }

  .back-button:hover {
    background-color: #ffe4e4;
    transform: scale(1.05);
  }

  .back-button:active {
    background-color: #ffabab;
    transform: scale(0.95);
  }
</style>
