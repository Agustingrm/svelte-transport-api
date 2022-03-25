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

<div class="container">
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
        <div class="results">
          <a href={`/#/${name}/${id}`}>
            <h3>{name}</h3>
            <p>The following transports are available on this location</p>
            <!-- The next line takes the transport object and filter them, leaving only the ones that are true -->
            <div class="transports">
              {#each Object.keys(products).filter((k) => products[k]) as transport}
                <p>{transport}</p>
              {/each}
            </div>
          </a>
        </div>
      {/each}
    {/if}
  {/await}
</div>

<style>
  .container {
    padding: 10px;
  }
  label {
    margin: 10px 0;
  }
  .results {
    background-color: #cecece;
    border: 1px solid black;
    margin: 10px 20px;
    padding: 10px;
  }
  .transports {
    display: flex;
    flex-wrap: wrap;
  }
  .transports p {
    margin: 0 10px 0 0;
  }
  h3 {
    margin: 5px 0;
  }
  a {
    text-decoration: none;
    color: black;
  }
</style>
