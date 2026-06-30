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
        <p class="to-uppercase to-bold">
          {selectedMedication.label.quantity}
          {selectedMedication.medicine.trade_name || selectedMedication.medicine.name} 
          {selectedMedication.medicine.concentration} 
          {selectedMedication.medicine.concentration_unit}
          <!-- TODO: fix grammar if medication type needs an e before the s -->
          {selectedMedication.medicine.form}{#if selectedMedication.label.quantity > 1}s{/if}
        </p>
          <div>
            <p class="to-bold">{formatDoseInstruction(selectedMedication.label)}</p>
          </div>
          <div>
            <p>If you feel dizzy DO NOT drive/operate machinery</p>
          </div>          
          <div>
            <p>Read the printed advice.</p>
          </div>
          <div>
            <p>{selectedMedication.label.advice}</p>
          </div>
          <div>
            <p class="text-right">Container 1 of 6</p>
          </div>
          <div>
            <p>{selectedMedication.label.patient_name}({random5DigitNumber()}) {new Intl.DateTimeFormat('en-GB').format(new Date(new Date().setDate(new Date().getDate() - 36)))}</p>
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

  .preview-card {
    width: 100%;
    max-width: 420px;
    border: 1px solid #e2e8f0;
    border-radius: 16px;
    padding: 1.25rem;
    background: linear-gradient(135deg, #ffffff, #f8fbff);
  }

  .eyebrow {
    text-transform: uppercase;
    letter-spacing: 0.2em;
    font-size: 0.75rem;
    color: #5b6474;
    margin: 0 0 0.35rem;
  }

  .subtitle {
    margin-top: 0;
    color: #5b6474;
  }

  .detail-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 0.75rem;
    margin: 1rem 0;
  }

  .detail-grid p {
    margin: 0.25rem 0 0;
  }

  .label-box {
    border-top: 1px solid #e2e8f0;
    padding-top: 1rem;
    margin-top: 1rem;
  }

  .label-box p {
    margin: 0.35rem 0;
  }

  @media (max-width: 900px) {
    .page {
      grid-template-columns: 1fr;
    }
  }
</style>