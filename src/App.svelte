<script>
  import { onMount } from "svelte";
  let inputShieldValue = 0;
  let outputTime = 0;
  let catalyzingShields = false;
  let maxCatalyzeValue = 100;

  function calculateTime() {
    if (catalyzingShields) {
      if (inputShieldValue > maxCatalyzeValue) {
        inputShieldValue = maxCatalyzeValue;
      }
      const scaledTime = inputShieldValue / maxCatalyzeValue;
      outputTime = Math.max(1.3333 * scaledTime, 0.3333);
    } else {
      if (inputShieldValue < 52.5) {
        outputTime = inputShieldValue / 180 + 1 / 3;
      } else if (inputShieldValue > 1150) {
        outputTime = 2.5;
      } else {
        outputTime = (1 / 45) * Math.pow(inputShieldValue, 0.65) + 1 / 3;
      }
    }
  }

  function handleKeyPress(e) {
    if (e.target.tagName === "INPUT" && e.target.type === "number") {
      if ("0123456789".indexOf(String.fromCharCode(e.which)) === -1) {
        e.preventDefault();
      }
    }
  }

  onMount(() => {
    inputShieldValue = 0;
    calculateTime();
    document.addEventListener("keypress", handleKeyPress);
  });
</script>

<main>
  <h1>Shield Gate Calculator</h1>

  <label for="shieldValue">Shield Amount:</label>
  <input
    type="number"
    inputmode="numeric"
    id="shieldValue"
    bind:value={inputShieldValue}
    on:input={calculateTime}
  />

  <input
    type="range"
    id="shieldSlider"
    min="0"
    max={catalyzingShields ? maxCatalyzeValue : 1150}
    bind:value={inputShieldValue}
    on:input={calculateTime}
  />

  <label>
    <input
      type="checkbox"
      bind:checked={catalyzingShields}
      on:change={calculateTime}
    />
    Catalyzing Shields
  </label>
  {#if catalyzingShields}
    <br />
    <br />
    <label for="maxCatalyzeValue">Max Shields:</label>
    <input
      type="number"
      inputmode="numeric"
      id="maxCatalyzeValue"
      disabled={!catalyzingShields}
      bind:value={maxCatalyzeValue}
      on:input={calculateTime}
    />
  {/if}

  <p>
    Shield Gate Duration: <span class="outputNum">{outputTime.toFixed(2)}</span>
    seconds
  </p>
</main>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Jost:ital,wght@0,100..900;1,100..900&family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");

  :global(body) {
    display: flex;
    justify-content: center;
    align-items: center;
    font-family: Arial, sans-serif;
    background-color: #17191a;
    color: #eee;
  }

  main {
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 10px;
    max-width: 400px;
    margin: 0 auto;
    background-color: #222627;
    padding-right: 40px;
    padding-left: 40px;
    padding-top: 20px;
    padding-bottom: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .outputNum {
    font-size: 24px;
    font-weight: bold;
  }

  h1 {
    text-align: center;
    margin-bottom: 20px;
    font-size: 24px;
  }

  label {
    display: inline-block;
    vertical-align: middle;
  }

  input[type="number"],
  input[type="range"] {
    width: 100%;
    padding: 8px;
    border-radius: 4px;
    border: 1px solid #3a3e41;
    box-sizing: border-box;
    background-color: #3a3e41;
    color: #eee;
  }

  input[type="number"]:disabled {
    color: #909199;
  }

  input[type="checkbox"] {
    margin: 0;
    margin-bottom: 3px;
    vertical-align: middle;
  }

  p {
    font-size: 18px;
    margin-top: 15px;
  }
</style>
