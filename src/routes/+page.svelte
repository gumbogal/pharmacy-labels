<script>
  import dataset from '../dataset.json';
  import './+page.css';

  let selectedCategory = $state('masculinising');
  let selectedMedicationIndex = $state(0);

  let medications = $derived(
    ({ masculinising: dataset.templates.masculinising, feminising: dataset.templates.feminising })[
      selectedCategory
    ] ?? []
  );
  let selectedMedication = $derived(
    medications[selectedMedicationIndex] ?? medications[0] ?? null
  );

  /**
   * @param {'masculinising' | 'feminising'} category
   */
  function chooseCategory(category) {
    selectedCategory = category;
    selectedMedicationIndex = 0;
  }

  /**
   * @param {{ dose?: number | string, dose_type?: string, frequency_amount?: number | string, frequency_period?: string }} label
   */
  function formatDoseInstruction(label) {
    const dose = Number(label?.dose ?? 1);
    const doseType = label?.dose_type?.trim();
    const frequencyAmount = Number(label?.frequency_amount ?? 1);
    const frequencyPeriod = label?.frequency_period?.trim() ?? 'day';

    const doseText = dose === 1 ? 'ONE' : String(dose).toUpperCase();
    const doseTypeText = doseType ? ` ${doseType.toUpperCase()}` : '';
    const frequencyText =
      frequencyAmount === 1
        ? 'ONCE'
        : frequencyAmount === 2
          ? 'TWICE'
          : String(frequencyAmount).toUpperCase();

    return `${doseText}${doseTypeText} to be taken ${frequencyText} a ${frequencyPeriod.toLowerCase()}`;
  }

  function random5DigitNumber() {
    return Math.floor(10000 + Math.random() * 90000);
  }
</script>

<div class="page">
  <section class="panel">
    <h1>Gender-affirming hormone label preview</h1>
    <p>Select a pathway to see example medications and a generated preview.</p>

    <div class="choices" role="group" aria-label="Treatment pathway">
      <button
        class:active={selectedCategory === 'masculinising'}
        onclick={() => chooseCategory('masculinising')}
      >
        Masculinising
      </button>
      <button
        class:active={selectedCategory === 'feminising'}
        onclick={() => chooseCategory('feminising')}
      >
        Feminising
      </button>
    </div>

    <div class="medications">
      {#each medications as medication, index}
        <button
          class="medication-card"
          class:active={index === selectedMedicationIndex}
          onclick={() => (selectedMedicationIndex = index)}
        >
          <strong>{medication.medicine.name}</strong>
          <span>{medication.medicine.route}</span>
        </button>
      {/each}
    </div>
  </section>

  <section class="panel preview-panel">
    {#if selectedMedication}
      <div class="preview-card">
      <div class="label-body">
        <p class="to-uppercase to-bold">
          <span style="font-weight: normal;">{selectedMedication.label.quantity}</span>
          {selectedMedication.medicine.trade_name || selectedMedication.medicine.name} 
          {selectedMedication.medicine.concentration} 
          {selectedMedication.medicine.concentration_unit}
          <!-- TODO: fix grammar if medication type needs an e before the s -->
          {selectedMedication.medicine.form}{#if selectedMedication.label.quantity > 1}s{/if}
        </p>
        <p class="to-bold">{formatDoseInstruction(selectedMedication.label)}</p>
        <p>If you feel dizzy DO NOT drive/operate machinery</p>
        <p>Read the printed advice.</p>
        <p>{selectedMedication.label.advice}</p>
        <p class="text-right">Container 1 of 6</p>
        <p>{selectedMedication.label.patient_name}({random5DigitNumber()}) {new Intl.DateTimeFormat('en-GB').format(new Date(new Date().setDate(new Date().getDate() - 36)))}</p>
      </div>
      <div class="label-footer">
        <div class="preview-icon" aria-label="Pharmacy label icon" role="img">
          <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
            <rect x="3" y="4" width="18" height="16" rx="2" ry="2" />
            <path d="M8 8h8" />
            <path d="M8 12h8" />
            <path d="M8 16h5" />
          </svg>
        </div>
        <div class="pharmacy-details">
          <h1>Warrington Pharmacy</h1>
          <p>18 Warrington Way</p>
          <p>Tel: 0161 322 5959</p>
        </div>
        <div class="dispensing-boxes" aria-label="Dispensing and checked boxes">
          <div class="dispensing-box">
            <span>DISP</span>
          </div>
          <div class="dispensing-box">
            <span>CHKD</span>
          </div>
        </div>
      </div>
      </div>
    {:else}
      <p>No medications available for this selection.</p>
    {/if}
  </section>
</div>

<style>
  :global(body) {
    margin: 0;
    background: #f4f6fb;
    font-family: system-ui, sans-serif;
  }

  .page {
    display: grid;
    grid-template-columns: 1.1fr 0.9fr;
    gap: 1.5rem;
    padding: 2rem;
    min-height: 100vh;
    box-sizing: border-box;
  }

  .panel {
    background: white;
    border-radius: 16px;
    padding: 1.5rem;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.06);
  }

  .choices {
    display: flex;
    gap: 0.75rem;
    margin: 1rem 0;
  }

  button {
    border: 1px solid #d2d8e3;
    background: #f8fafc;
    color: #162033;
    border-radius: 999px;
    padding: 0.7rem 1rem;
    cursor: pointer;
    font: inherit;
  }

  button.active {
    background: #162033;
    color: white;
    border-color: #162033;
  }

  .medications {
    display: grid;
    gap: 0.75rem;
    margin-top: 1rem;
  }

  .medication-card {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-radius: 12px;
    padding: 0.9rem 1rem;
    text-align: left;
    width: 100%;
  }

  .medication-card span {
    font-size: 0.9rem;
    color: #5b6474;
    text-transform: capitalize;
  }

  .preview-panel {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  @media (max-width: 900px) {
    .page {
      grid-template-columns: 1fr;
    }
  }
</style>