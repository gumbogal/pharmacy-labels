<script>
  let patientName = "";
  let medication = $state("");
  let dosage = $state("");
  let instructions = "";
  let oral = $state(false);
  let pillCount = $state();
</script>

<div class="container">
  <!-- FORM -->
  <div class="form">
    <h1>Pharmacy Label Generator</h1>

    <label>
      Is the medication orally administered?
      <select bind:value={oral}>
        <option value={false}>No</option>
        <option value={true}>Yes</option>
      </select>
    </label>

        {#if oral}
        <label>
            Pill Count
            <select bind:value={pillCount}>
                <option value={7}>1 Week</option>
                <option value={14}>Fortnightly</option>
                <option value={28}>Monthly (28 days)</option>
            </select>
        </label>
        {/if}

    <label>
      Patient Name
      <input bind:value={patientName} placeholder="e.g. Bob Cobb" />
    </label>

    <label>
      Medication
      <input bind:value={medication} placeholder="e.g. Progesterone" />
    </label>

    <label>
      Dosage
      <input bind:value={dosage} placeholder="e.g. 100mg" />
    </label>

    <label>
      Instructions
      <textarea
        bind:value={instructions}
        placeholder="e.g. TWO to be taken TWICE a day"
      ></textarea>
    </label>

    <button onclick={() => window.print()}>
      Print Label
    </button>
  </div>

  <!-- PREVIEW -->
  <div class="preview">
    <h2>Live Preview</h2>

    <div class="label">
      <div class="label-header">
        {#if oral}
          <span class="pill-count">{pillCount}</span> {medication.toUpperCase()} {dosage.toUpperCase()} CAPSULES
        {:else}
          LIQUID
        {/if}
      </div>

      <div class="dosing-instructions">
        {#if oral}
          ONE to be taken TWICE a day
        {:else}
          NOT FOR ORAL USE
        {/if}
      </div>

      <div class="label-body">
        <p><strong>Name:</strong> {patientName}</p>
        <p><strong>Medication:</strong> {medication}</p>
        <p><strong>Dosage:</strong> {dosage}</p>
        <p><strong>Instructions:</strong></p>
        <p class="instructions">{instructions}</p>
      </div>
    </div>
  </div>
</div>

<style>
  .container {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 2rem;
    padding: 2rem;
    font-family: system-ui, sans-serif;
  }

  .form {
    background: #f7f7f7;
    padding: 1rem;
    border-radius: 8px;
  }

  label {
    display: block;
    margin-bottom: 1rem;
    font-weight: 600;
  }

  input, textarea {
    width: 100%;
    margin-top: 0.25rem;
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  button {
    margin-top: 1rem;
    padding: 0.75rem 1rem;
    background: black;
    color: white;
    border: none;
    cursor: pointer;
  }

  /* 🏷️ LABEL PREVIEW */
  .preview {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
  }

  .label {
    width: 80mm;
    min-height: 50mm;
    border: 1px solid #000;
    padding: 6mm;
    background: white;
    font-size: 12px;
  }

  .pill-count {
    font-weight: normal;
  }

  .label-header, .dosing-instructions {
    font-weight: bold;
    text-align: center;
    margin: 0mm;
    padding: 0mm;
  }

  .instructions {
    margin-top: 2mm;
  }

  /* 🖨️ PRINT RULES */
  @media print {
    .form,
    h1,
    h2 {
      display: none;
    }

    .container {
      display: block;
      padding: 0;
    }

    .label {
      border: none;
      width: 100%;
      font-size: 14pt;
    }
  }
</style>