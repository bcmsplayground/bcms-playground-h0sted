<script>
  import GenitivePractice from "./GenitivePractice.svelte";
  import VocabularyPractice from "./VocabularyPractice.svelte";
  import { fade } from "svelte/transition"; // Built-in Svelte transition
  import { onMount } from "svelte"; // Import onMount lifecycle hook
  let showPractice = false;
  let showVocabularyPractice = false;

  let bubbles = [];
  const flags = ["🇭🇷", "🇧🇦", "🇷🇸", "🇲🇪"]; // Croatia, Bosnia, Serbia, Montenegro flags
  const colors = ["#FF0000", "#FF7F00", "#FFFF00"]; // Rainbow colors

  // Generate bubbles and flags logic
  const generateBubbles = () => {
    bubbles = Array.from({ length: 50 }, (_, i) => {
      const isFlag = i % 10 === 0; // Every 10th bubble is a flag
      return {
        id: i,
        content: isFlag ? flags[Math.floor(Math.random() * flags.length)] : "",
        color: isFlag ? "" : colors[Math.floor(Math.random() * colors.length)], // Random color for bubbles
        size: Math.random() * 30 + 10, // Random size for bubbles
        left: Math.random() * 100, // Random horizontal position
        duration: Math.random() * 8 + 5, // Random duration for float
      };
    });
  };

  // Run bubble generation when the app starts
  onMount(() => {
    generateBubbles();
  });

  function loadGenitivePractice() {
    showPractice = true; // Set state to load the Genitive Practice
    showVocabularyPractice = false; // Ensure we don't show both at the same time
  }

  function loadVocabularyPractice() {
    showVocabularyPractice = true;
    showPractice = false; // Ensure we don't show both at the same time
  }

  function goBackToMain() {
    showPractice = false; // Set state to return to the main page
    showVocabularyPractice = false; // Reset both when going back to main
  }
</script>

<!-- Main page content -->
{#if !showPractice && !showVocabularyPractice}
  <div
    class="main-container"
    in:fade={{ duration: 300 }}
    out:fade={{ duration: 300 }}
  >
    <h1 class="title">BCMS Playground</h1>
    <button class="cutesy-button" on:click={loadGenitivePractice}>
      Start Genitive Practice
    </button>
    <button class="cutesy-button" on:click={loadVocabularyPractice}>
      Start Vocabulary Practice
    </button>

    <!-- Bubble animation background -->
    <div class="bubbles-container">
      {#each bubbles as bubble (bubble.id)}
        {#if bubble.content}
          <!-- Flag bubbles -->
          <div
            class="bubble flag"
            style="
				left: {bubble.left}%;
				font-size: {bubble.size}px;
				animation-duration: {bubble.duration}s;
			  "
          >
            {bubble.content}
          </div>
        {:else}
          <!-- Regular colorful bubbles -->
          <div
            class="bubble"
            style="
				background-color: {bubble.color};
				width: {bubble.size}px;
				height: {bubble.size}px;
				left: {bubble.left}%;
				animation-duration: {bubble.duration}s;
			  "
          ></div>
        {/if}
      {/each}
    </div>
  </div>
{/if}

<!-- Wrap the GenitivePractice component in a div for transitions and ensure vertical layout -->
{#if showPractice}
  <div
    class="genitive-container"
    in:fade={{ duration: 300 }}
    out:fade={{ duration: 300 }}
  >
    <GenitivePractice {goBackToMain} />
  </div>
{/if}

<!-- Wrap the VocabularyPractice component -->
{#if showVocabularyPractice}
  <div
    class="vocab-container"
    in:fade={{ duration: 300 }}
    out:fade={{ duration: 300 }}
  >
    <VocabularyPractice {goBackToMain} />
  </div>
{/if}

<style>
  /* Styling for the main page */
  .main-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100%; /* Full viewport height */
    width: 100%; /* Full viewport width */
    background: linear-gradient(
      to right,
      #ffb6c1,
      #ffe4e1
    ); /* Pastel background */
    text-align: center;
    position: fixed;
    top: 0px;
    left: 0px;
    z-index: 2; /* Ensure main content stays in front of bubbles */
  }

  .title {
    font-size: 2.5em;
    color: #ff6f61;
    margin-bottom: 2rem;
    font-weight: bold;
  }

  .cutesy-button {
    background-color: #ffcbcb;
    color: #ff6969;
    font-size: 1.2em;
    font-weight: bold;
    padding: 1em 2em;
    border-radius: 12px;
    cursor: pointer;
    transition:
      transform 0.2s,
      background-color 0.2s;
  }

  .cutesy-button:hover {
    background-color: #ffe4e4;
    transform: scale(1.05);
  }

  .cutesy-button:active {
    background-color: #ffabab;
    transform: scale(0.95);
  }

  /* Bubble container and animations */
  .bubbles-container {
    position: absolute;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    z-index: 1; /* Bubbles appear behind main content */
    pointer-events: none; /* Allow interaction with main content */
    overflow: hidden;
  }

  /* Common bubble style */
  .bubble {
    position: absolute;
    bottom: -50px; /* Start just below the screen */
    border-radius: 50%;
    opacity: 0.7;
    animation: float-up ease-in infinite;
    z-index: 1;
  }

  /* Animation for floating bubbles */
  @keyframes float-up {
    0% {
      transform: translateY(0);
      opacity: 0.7;
    }
    100% {
      transform: translateY(-110vh); /* Float past the top */
      opacity: 0;
    }
  }

  /* Flag-specific styling */
  .flag {
    font-size: 1.5em;
    background: none; /* No background for flags */
  }

  /* Fix to center content in column layout for Genitive Practice */
  .genitive-container,
  .vocab-container {
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0px;
    left: 0px;
    display: flex;
    flex-direction: column; /* Ensure vertical layout */
    align-items: center;
    justify-content: flex-start; /* Align content from the top */
  }
</style>
