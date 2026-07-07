<script>
  import dataset from '../dataset.json';
  import mdIcon from '../assets/md-icon.svg';
  import './+page.css';

  let selectedCategory = $state('masculinising');
  let selectedMedicationIndex = $state(0);
  let showVibes = $state(false);
  let customMedication = $state({
    medicine: {
      name: '',
      trade_name: '',
      form: '',
      route: '',
      concentration: '',
      concentration_unit: ''
    },
    label: {
      patient_name: '',
      quantity: 1,
      dose: 1,
      dose_type: '',
      frequency_amount: 1,
      frequency_period: 'day',
      advice: ''
    }
  });

  let medications = $derived(
    ({ masculinising: dataset.templates.masculinising, feminising: dataset.templates.feminising })[
      selectedCategory
    ] ?? []
  );
  let selectedMedication = $derived.by(() => {
    if (selectedCategory === 'custom') {
      return customMedication;
    }

    return medications[selectedMedicationIndex] ?? medications[0] ?? null;
  });

  /**
   * @param {'masculinising' | 'feminising' | 'custom'} category
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
    <h1>UK Pharmacy Label Generator</h1>
    <h2 class="subheading">For trans individuals</h2>
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
      <button class:active={selectedCategory === 'custom'} onclick={() => chooseCategory('custom')}>
        Custom
      </button>
    </div>

    {#if selectedCategory === 'custom'}
      <form class="custom-form">
        <div class="field-grid">
          <label class="field">
            <span>Medicine name</span>
            <input bind:value={customMedication.medicine.name} placeholder="e.g., estradiol" />
          </label>
          <label class="field">
            <span>Trade name</span>
            <input bind:value={customMedication.medicine.trade_name} placeholder="e.g., Elleste Solo" />
          </label>
          <label class="field">
            <span>Form</span>
            <select bind:value={customMedication.medicine.form}>
              <option value="">Select a form</option>
              <option value="tablet">tablet</option>
              <option value="capsule">Capsule</option>
              <option value="oral solution">Oral solution</option>
              <option value="transdermal gel">Transdermal gel</option>
              <option value="transdermal patch">Transdermal patch</option>
              <option value="solution for injection">Solution for injection</option>
              <option value="injection">Injection</option>
              <option value="implant">Implant</option>
              <option value="cream">Cream</option>
              <option value="spray">Spray</option>
              <option value="lozenge">Lozenge</option>
            </select>
          </label>
          <label class="field">
            <span>Route</span>
            <input bind:value={customMedication.medicine.route}  />
          </label>
          <label class="field">
            <span class="field-label-with-help">
              <span>Concentration</span>
              <span class="help-icon" aria-label="What concentration means">
                ?
                <span class="help-tooltip">
                  Concentration is the strength of the medicine, such as 2 mg or 100 micrograms per 24 hours.
                </span>
              </span>
            </span>
            <input bind:value={customMedication.medicine.concentration} placeholder="e.g., 100" />
          </label>
          <label class="field">
            <span>Concentration unit</span>
            <select bind:value={customMedication.medicine.concentration_unit}>
              <option value="">Select a unit</option>
              <option value="mg">mg</option>
              <option value="mcg">mcg</option>
              <option value="ml">ml</option>
              <option value="IU">IU</option>
            </select>
          </label>
          <label class="field">
            <span>Patient name</span>
            <input bind:value={customMedication.label.patient_name} placeholder="e.g., Bob Cobb, Sally Snail." />
          </label>
          <label class="field">
            <span class="field-label-with-help">
              <span>Quantity</span>
              <span class="help-icon" aria-label="What quantity means">
                ?
                <span class="help-tooltip">
                  Quantity means the number of individual items in the pack or supply being dispensed, such as 28 tablets or 1 pump pack.
                </span>
              </span>
            </span>
            <input type="number" bind:value={customMedication.label.quantity} />
          </label>
          <label class="field">
            <span class="field-label-with-help">
              <span>Dose</span>
              <span class="help-icon" aria-label="What dose means">
                ?
                <span class="help-tooltip">
                  Dose is the amount of medicine taken each time, such as 1 tablet or 1 pump.
                </span>
              </span>
            </span>
            <input type="number" bind:value={customMedication.label.dose} />
          </label>
          <label class="field">
            <span class="field-label-with-help">
              <span>Dose type</span>
              <span class="help-icon" aria-label="What dose type means">
                ?
                <span class="help-tooltip">
                  Dose type describes how the medicine is taken, such as tablet, patch, pump, or application.
                </span>
              </span>
            </span>
            <input bind:value={customMedication.label.dose_type} />
          </label>
          <label class="field">
            <span class="field-label-with-help">
              <span>Frequency amount</span>
              <span class="help-icon" aria-label="What frequency amount means">
                ?
                <span class="help-tooltip">
                  Frequency amount is how many times the medicine is taken per period, such as 2.
                </span>
              </span>
            </span>
            <input type="number" bind:value={customMedication.label.frequency_amount} />
          </label>
          <label class="field">
            <span class="field-label-with-help">
              <span>Frequency period</span>
              <span class="help-icon" aria-label="What frequency period means">
                ?
                <span class="help-tooltip">
                  Frequency period is the time frame for the dosing schedule, such as day, week, or month.
                </span>
              </span>
            </span>
            <input bind:value={customMedication.label.frequency_period} />
          </label>
          <label class="field field-wide">
            <span class="field-label-with-help">
              <span>Advice</span>
              <span class="help-icon" aria-label="What advice means">
                ?
                <span class="help-tooltip">
                  Advice is the patient-facing instruction shown on the label, such as how and when to take the medicine.
                </span>
              </span>
            </span>
            <textarea bind:value={customMedication.label.advice}></textarea>
          </label>
        </div>
      </form>
    {:else}
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
    {/if}
  </section>

  <section class="panel preview-panel">
    {#if selectedMedication}
      <div class="preview-viewport">
        <div class="preview-card">
          <div class="label-body">
            <div class="label-details">
              <p class="to-uppercase to-bold">
                <span class="quantity-value">{selectedMedication.label.quantity}</span>
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
              <br />
              <br />
              <p class="text-right">Container 1 of 3</p>
              <br />
              <p>{selectedMedication.label.patient_name}({random5DigitNumber()}) {new Intl.DateTimeFormat('en-GB').format(new Date(new Date().setDate(new Date().getDate() - 36)))}</p>
            </div>
            <div class="child-warning">
              <p>Keep out of reach and sight of children</p>
            </div>
          </div>
          <div class="label-footer">
            <div class="preview-icon" aria-label="Pharmacy label icon" role="img">
              <img src={mdIcon} alt="" aria-hidden="true" />
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
      </div>
    {:else}
      <p>No medications available for this selection.</p>
    {/if}
  </section>
</div>

<footer class="page-footer" class:open={showVibes}>
  <div class="vibes-panel" class:open={showVibes}>
    <p>The website was created with vibe coding. Do I love AI? Fuck no. Am I a shit developer? Absolutely. You could say that the AI had help from me for the little bits and not the other way around. 🤷‍♀️</p>
  </div>
  <div class="footer-row">
    <p>Made with ❤️ for my 🏳️‍⚧️ siblings by <a href="https://github.com/gumbogal" target="_blank" rel="noopener noreferrer">gumbogal</a></p>
    <button class="vibes-toggle" onclick={() => (showVibes = !showVibes)}>
      Vibes?
    </button>
  </div>
</footer>

