<script>
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
</script>

<main>
  <h1>Shield Gate Timer Calculator</h1>

  <label for="shieldValue">Shield Amount:</label>
  <input type="number" bind:value={inputShieldValue} on:input={calculateTime} />

  <!-- <label for="shieldSlider">Shield Value Slider:</label> -->
  <input
    type="range"
    id="shieldSlider"
    min="0"
    max={catalyzingShields ? maxCatalyzeValue : 1150}
    bind:value={inputShieldValue}
    on:input={calculateTime}
  />

  <div>
    <input
      type="checkbox"
      bind:checked={catalyzingShields}
      on:change={calculateTime}
    />
    <label for="catalyzingShields">Catalyzing Shields</label>

    {#if catalyzingShields}
      <label for="maxCatalyzeValue">(Max Shields:)</label>
      <input
        type="number"
        bind:value={maxCatalyzeValue}
        on:input={calculateTime}
      />
    {/if}
  </div>

  <!-- <button on:click={calculateTime}>Calculate Time</button> -->

  {#if outputTime > 0}
    <p>Shield Gate Duration: {outputTime.toFixed(2)} seconds</p>
  {/if}
</main>

<style>
  label {
    display: inline-block;
  }
  #shieldSlider {
    vertical-align: middle;
  }
</style>
