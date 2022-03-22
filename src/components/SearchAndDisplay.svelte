<script>
  let stopName = "";
  let data = "";

  const getData = async () => {
    const response = await fetch(`https://v5.vbb.transport.rest/locations?query=${stopName}&results=10`);
    const data = await response.json();
    if (response.ok) {
      console.log(data);
      return data;
    } else {
      throw new Error(data);
    }
  };

  let promise = "";

  const handleSubmit = (e) => {
    e.preventDefault();
    promise = getData();
  };
</script>

<div>
  <form>
    <label for="location">Enter here the stop to searcsh</label>
    <input type="text" name="location" bind:value={stopName} />
    <button on:click={handleSubmit}>Search</button>
  </form>
  {#await promise}
    <p>Loading...</p>
  {:then results}
    {#if promise}
      {#each results as { name }}
        <p>{name}</p>
      {/each}
    {/if}
  {/await}
</div>
