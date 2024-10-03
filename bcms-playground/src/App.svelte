<script>
  import GenitivePractice from "./GenitivePractice.svelte";
  import { fade } from "svelte/transition"; // Built-in Svelte transition

  let showPractice = false;

  function loadGenitivePractice() {
    showPractice = true; // Set state to load the Genitive Practice
  }

  function goBackToMain() {
    showPractice = false; // Set state to return to the main page
  }
</script>

<!-- Main page content -->
{#if !showPractice}
  <div
    class="main-container"
    in:fade={{ duration: 300 }}
    out:fade={{ duration: 300 }}
  >
    <h1 class="title">BCMS Playground</h1>
    <button class="cutesy-button" on:click={loadGenitivePractice}>
      Start Genitive Practice
    </button>
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

  /* Fix to center content in column layout for Genitive Practice */
  .genitive-container {
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
