<script>
  import { onMount } from "svelte";
  let inputShieldValue = 0;
  let inputArmorValue = 0;
  let outputTime = 0;
  let outputDR = 0;
  let heat = false;
  let corroStacks = 10;
  const corroStackList = Array.from({ length: 15 }, (_, i) => i);
  let catalyzingShields = false;
  let maxCatalyzeValue = 100;

  function calculateTime() {
    if (catalyzingShields) {
      if (inputShieldValue > maxCatalyzeValue) {
        inputShieldValue = maxCatalyzeValue;
      }
      const scaledTime = inputShieldValue / maxCatalyzeValue;
      outputTime = Math.max(1.3333 * scaledTime, 0.3333);
      if (isNaN(outputTime)) {
        outputTime = 0;
      }
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

  function corroStacksToStripPercent(stacks) {
    if (stacks === 0) {
      return 0;
    } else if (stacks === 14) {
      return 1;
    } else if (stacks === 1) {
      return 0.26;
    } else if (stacks > 1) {
      return 0.26 + (stacks - 1) * 0.06;
    }
  }

  function quickArmorCalc() {
    inputArmorValue = heat ? 1350 : 2700;
    if (corroStacks > 0) {
      inputArmorValue *= 1 - corroStacksToStripPercent(corroStacks);
    }
    inputArmorValue = Math.round(inputArmorValue);
    calculateDR();
  }

  function calculateDR() {
    if (inputArmorValue == 0) {
      outputDR = 0;
      return;
    } else if (inputArmorValue > 2700) {
      inputArmorValue = 2700;
    }
    outputDR = Math.sqrt(inputArmorValue * 3);
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
  <section>
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
      Shield Gate Duration: <span class="outputNum"
        >{outputTime.toFixed(2)}</span
      >
      seconds
    </p>
    {#if outputTime == 0}
      <div class="warntext">
        If you have no shields, you will not get a shieldgate.
      </div>
    {/if}
  </section>
  <section>
    <h1>Enemy Armor -> DR% Calculator</h1>
    <label for="armorValue">Armor Amount:</label>
    <input
      type="number"
      inputmode="numeric"
      id="armorValue"
      bind:value={inputArmorValue}
      on:input={calculateDR}
    />
    <input
      type="range"
      id="shieldSlider"
      min="0"
      max="2700"
      bind:value={inputArmorValue}
      on:input={calculateDR}
    />
    Quick Max Armor Calc:
    <div>
      Corrosive: <select bind:value={corroStacks}>
        {#each corroStackList as number}
          <option value={number}>{number}</option>
        {/each}
      </select>
      <label>
        <input type="checkbox" bind:checked={heat} />
        Heat
      </label>
      <button on:click={quickArmorCalc}>Quick Calc</button>
    </div>
    <p>
      Enemy DR: <span class="outputNum">{outputDR.toFixed(2)}</span>%
    </p>
  </section>
</main>

<style>
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
  }

  p {
    text-align: center;
  }

  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 10px;
    max-width: 298px;
    margin: 0 auto;
    background-color: #222627;
    padding-right: 40px;
    padding-left: 40px;
    padding-top: 20px;
    padding-bottom: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .warntext {
    text-align: center;
    font-size: 12px;
    color: red;
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
