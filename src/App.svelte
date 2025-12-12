<script>
  import { onMount } from "svelte";

  let text = "";
  let activity = "";
  let dateStr = "";
  let entries = [];
  let darkMode = true; // default to dark mode for 508 compliance, keep standard wheel
  let rightMessage = "Select an entry to review.";

  const quickButtons = [
    { label: "20251102", getMsg: () => "Robotics was fun and I feel great when playing with the components." },
    { label: "20251103", getMsg: () => "Math completed and I feel more confident" },
    { label: "20251201", getMsg: () => "I read an extra 10-minutes today!" }
  ];

  function showRightMessage(msg) {
    rightMessage = msg;
  }


  onMount(() => {
    const saved = localStorage.getItem("journalEntries");
    if (saved) entries = JSON.parse(saved);

    const savedTheme = localStorage.getItem("darkMode");
    if (savedTheme !== null) darkMode = savedTheme === "true";
  });

  function saveEntry() {
    if (!dateStr) {
      alert("Please enter a date first. Date required for journal entry.");
      return;
    }
    const ok = confirm(
      `Save entry for ${dateStr} with activity "${activity || "unspecified"}"?`
    );
    if (!ok) return;

    const entry = { id: Date.now(), date: dateStr, activity, text };
    entries = [...entries, entry];
    localStorage.setItem("journalEntries", JSON.stringify(entries));
    alert("Entry saved!");
    rightMessage = `Saved entry for ${dateStr}.`;
  }

  function toggleDarkMode() {
    darkMode = !darkMode;
    localStorage.setItem("darkMode", String(darkMode)); // store as "true"/"false" suggestion from ChatGPT
  }
</script>

<main class:dark={darkMode}>
  <h1>Journal Entry</h1>

  <!-- Dark mode toggle -->
  <button class="theme-toggle" on:click={toggleDarkMode}>
    {darkMode ? "\u2600\uFE0F Light Mode" : "\u{1F319} Dark Mode"} <!-- moon emoji from ChatGPT -->
  </button>

  <!-- Rounded rectangular buttons -->
  <div class="quick-actions" aria-label="Quick actions">
    {#each quickButtons as b}
      <button
        class="rounded-action"
        type="button"
        on:click={() => showRightMessage(b.getMsg())}
      >
        {b.label}
      </button>
    {/each}
  </div>

  <!-- Right-side message panel -->
  <aside class="right-panel" aria-live="polite">
    <div class="right-panel-title">Message</div>
    <pre class="right-panel-body">{rightMessage}</pre>
  </aside>


  <!-- Controls above the text box -->
  <div class="controls">
    <fieldset class="activity">
      <legend>Activity</legend>
      <label>
        <input type="radio" name="activity" value="Robotics" bind:group={activity}>
        Robotics
      </label>
      <label>
        <input type="radio" name="activity" value="Mathmatics" bind:group={activity}>
        Mathmatics
      </label>
      <label>
        <input type="radio" name="activity" value="Reading" bind:group={activity}>
        Reading
      </label>
    </fieldset>

    <div class="row">
      <label class="date-label">
        Date (required)
        <input type="date" bind:value={dateStr} />
      </label>

      <button class="save" on:click={saveEntry}>Save</button>
    </div>
  </div>

  <textarea
    bind:value={text}
    placeholder="How did you feel when you completed your activity?">
  </textarea>
</main>

<style>
  main {
    padding: 2rem;
    font-family: "Courier New", monospace;
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    gap: 1rem;
    background: #f9f9f9;
    color: #111;
    transition: background 0.3s, color 0.3s; /* w3schools suggestion for consistent transition*/
  }

  main.dark {
    background: #222;
    color: #eee;
  }

  .theme-toggle {
    padding: 0.4rem 0.8rem;
    font-size: 0.9rem;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    background: #444;
    color: #fff;
    margin-bottom: 1rem;
  }

  main.dark .theme-toggle {
    background: #ddd;
    color: #222;
  }

  .controls {
    width: clamp(300px, 80vw, 1200px);
    box-sizing: border-box;
  }

  fieldset.activity {
    border: 1px solid #ccc;
    border-radius: 8px;
    padding: 0.75rem 1rem;
    display: flex;
    gap: 1rem;
    flex-wrap: wrap;
    margin: 0 0 0.75rem 0;
  }

  fieldset.activity legend {
    padding: 0 0.5rem;
    font-weight: bold;
  }

  fieldset.activity label {
    display: flex;
    align-items: center;
    gap: 0.4rem;
    cursor: pointer;
  }

  .row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 1rem;
  }

  .date-label {
    display: flex;
    align-items: center;
    gap: 0.6rem;
    font-weight: bold;
  }

  .date-label input[type="date"] {
    font-size: 1rem;
    padding: 0.4rem 0.5rem;
  }

  .save {
    padding: 0.6rem 1.2rem;
    font-size: 1rem;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    background: #888;
    color: #fff;
  }

  main.dark .save {
    background: #eee;
    color: #111;
  }

  textarea {
    height: 30vh;
    width: clamp(300px, 80vw, 1200px);
    font-size: 1.2rem;
    padding: 2rem;
    resize: none;
    display: block;
    margin: 0 auto;
    box-sizing: border-box;
    border: 1px solid #888;
    background: #fff;
    color: #111;
    transition: background 0.3s, color 0.3s;
  }

  main.dark textarea {
    background: #333;
    color: #eee;
    border: 1px solid #555;
  }

  /* ===== Added styles for rounded buttons + right message panel IAW feedback received ===== */

  .quick-actions {
    width: clamp(300px, 80vw, 1200px);
    display: flex;
    gap: 12px;
    flex-wrap: wrap;
    justify-content: center;
  }

  .rounded-action {
    width: 220px;
    height: 54px;
    border-radius: 14px;          /* rounded rectangle */
    border: 1px solid #888;
    cursor: pointer;

    display: flex;                /* centered text */
    align-items: center;
    justify-content: center;

    font-size: 1rem;
    font-weight: 600;

    background: #fff;
    color: #111;
    transition: transform 0.05s, background 0.2s, color 0.2s, border-color 0.2s;
  }

  .rounded-action:hover {
    background: #f2f2f2;
  }

  .rounded-action:active {
    transform: translateY(1px);
  }

  main.dark .rounded-action {
    background: #333;
    color: #eee;
    border-color: #aaa;
  }

  main.dark .rounded-action:hover {
    background: #3a3a3a;
  }

  .right-panel {
    position: fixed;
    top: 24px;
    right: 24px;
    width: min(420px, 34vw);
    max-height: calc(100vh - 48px);
    overflow: auto;

    border: 1px solid #bbb;
    border-radius: 14px;
    padding: 14px;
    background: #fff;
    color: #111;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.12);
  }

  main.dark .right-panel {
    background: #2b2b2b;
    color: #eee;
    border-color: #666;
  }

  .right-panel-title {
    font-weight: 800;
    margin-bottom: 10px;
  }

  .right-panel-body {
    margin: 0;
    white-space: pre-wrap;
    font-family: inherit;
    font-size: 0.95rem;
    line-height: 1.35;
  }

  /* Allows the screen to scale */
  @media (max-width: 900px) {
    .right-panel {
      position: static;
      width: clamp(300px, 80vw, 1200px);
      box-shadow: none;
    }
  }
</style>

<!-- No saving of entries needed per professor, no backend needed -->
