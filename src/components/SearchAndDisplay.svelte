<script>
  let stopName = "";
  let data = "";

  const getData = async () => {
    const response = await fetch(`https://v5.vbb.transport.rest/locations?query=${stopName}&results=10`);
    const data = await response.json();
    if (response.ok) {
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
    <label for="location">Enter here the stop to search</label>
    <input type="text" name="location" bind:value={stopName} />
    <button on:click={handleSubmit}>Search</button>
  </form>
  {#await promise}
    <p>Loading...</p>
  {:then results}
    {#if promise}
      <p>The top 10 matches of your search are:</p>
      {#each results as { id, name, products }}
        <a href={`/#/stops/${id}`}>
          <p>{name}</p>
          <p>The following transports are available on this location</p>
          <!-- The next line takes the transport object and filter them, leaving only the ones that are true -->
          <!-- {#each Object.keys(products).filter((k) => products[k]) as transport}
            <p>{transport}</p>
          {/each} -->
          <p>______________________________</p>
        </a>
      {/each}
    {/if}
  {/await}
</div>
