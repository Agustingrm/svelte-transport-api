<script>
  let storedData = localStorage.getItem("saved stops");
  let storedDataObject = JSON.parse(storedData);

  const handleDelete = (array) => {
    for (let i = 0; i <= storedDataObject.length; i++) {
      let savedData = storedDataObject;
      if (JSON.stringify(storedDataObject[i]) === JSON.stringify(array)) {
        savedData.splice(i, 1);
        localStorage.setItem("saved stops", JSON.stringify(savedData));
        // This will cause a rerender
        storedDataObject = savedData;
      }
    }
  };
  const handleClear = () => {
    localStorage.clear();
  };
</script>

{#if storedDataObject.length == 0}
  <p>There are no stops Bookmarked</p>
{:else}
  <div class="container">
    <button on:click={handleClear}>Delete all Bookmarks</button>
    {#each storedDataObject as array}
      <h2>{array[1]}</h2>
      <button on:click={() => handleDelete(array)}>Delete</button>
      {#each array[0] as { destination, cancelled, when }}
        <div class="results">
          <a href={`/#/${destination.name}/${destination.id}`}>
            {#if cancelled}
              <p>THIS SERVICE WAS CANCELLED</p>
            {/if}
            <h3>{destination.name}</h3>
            <h4>
              {new Date(when).toLocaleString("en-GB", {
                hour: "2-digit",
                minute: "2-digit",
                year: "2-digit",
                month: "2-digit",
                day: "2-digit",
              })}
            </h4>
            <p>The following transports are available on this location:</p>
            <!-- The next line takes the transport object and filter them, leaving only the ones that are true -->
            <div class="transports">
              {#each Object.keys(destination.products).filter((k) => destination.products[k]) as transport}
                <p>{transport}</p>
              {/each}
            </div>
          </a>
        </div>
      {/each}
    {/each}
  </div>
{/if}

<style>
  .container {
    padding: 10px;
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
