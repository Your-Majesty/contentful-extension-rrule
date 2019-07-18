<script>
  import rrule from 'rrule'

  let textValue = ''
  let error = false
  let rruleResultText = ''
  let input

  let initiatedExtension = null

  function tryDates (text) {
    let dates
    try {
      dates = rrule.fromText(text)
    } catch (err) {
      error = err
      return []
    }
    error = false

    rruleResultText = dates.toText()

    if (initiatedExtension) initiatedExtension.field.setValue(textValue)

    return dates.all((d, i) => i < 10 ? d : false)
      .map(d => `${d.toLocaleString('en-GB', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' })}`)
  }

  function clickExample (event) {
    textValue = event.target.innerText
  }

  $: previewDates = tryDates(textValue)

  window.contentfulExtension.init((extension) => {
    initiatedExtension = extension
    initiatedExtension.window.startAutoResizer()
    textValue = initiatedExtension.field.getValue()
  })
</script>

<style>
  .examples {
    max-width: 600px;
  }
  
  .button {
    display: inline-block;
    color: #666;
    font-size: 14px;
    cursor: pointer;

    padding: 4px;
    border: 1px solid #eee;
    margin: 4px 0;
  }
  .button:hover {
    background: #eee;
    color: #000;
  }
  
  h2 {
    font-size: 20px;
  }

  .explain {
    font-size: 12px;
    color: #888;
  }

  .input-container {
    display: flex;
    align-items: center;
  }

  input {
    width: 100%;
    border: 2px solid #eee;
    font-size: inherit;
    padding: 8px;
    line-height: 22px;
    padding-left: 4px;
    border-left: none;
  }
  .input-container span {
    padding-right: 10px;
    border: 2px solid #eee;
    padding: 8px;
    line-height: 22px;
    padding-right: 0;
    border-right: none;
  }

  .result {
    font-style: italic;
    color: green;
    background: rgba(0, 255, 0, .2);
    padding: 16px;
  }
  .result.error {
    color: red;
    background: rgba(255, 0, 0, .2);
  }
</style>

<div>
  <div class="input-container">
    <span on:click={() =>  { input.focus() }}>Repeat</span>
    <input bind:this={input} type="text" bind:value={textValue} placeholder="every year">
  </div>

  <div class="examples">
    <h2>Example uses</h2>
    <div class="button" on:click={clickExample}>every month on first friday</div>
    <div class="button" on:click={clickExample}>every tuesday</div>
    <div class="button" on:click={clickExample}>every monday, tuesday and saturday</div>
    <div class="button" on:click={clickExample}>every september the 24th</div>
    <div class="button" on:click={clickExample}>every day</div>
    <div class="button" on:click={clickExample}>every week</div>
    <div class="button" on:click={clickExample}>every 10 days</div>
    <div class="button" on:click={clickExample}>every friday the 13th</div>
  </div>

  <div class="interpreted">
    <h2>Interpreted result</h2>
    <div class="result" class:error={error}>
      {#if error}
        {error}
      {:else}
        {rruleResultText}
      {/if}  
    </div>
  </div>

  {#if !error}
    <h2>Upcoming dates</h2>
    {#each previewDates as date}
      <div>
        {date}
      </div>
    {/each}
  {/if}
</div>
