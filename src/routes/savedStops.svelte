<script>
  let storedData = localStorage.getItem("saved stops");
  let storedDataObject = JSON.parse(storedData);
  console.log(storedDataObject);
  const handleDelete = () => {
    for (let i = 0; i < storedDataObject.length; i++) {
      if (JSON.stringify(storedDataObject[i]) === JSON.stringify(array)) {
        localStorage.setItem("saved stops", JSON.stringify([storedDataObject.splice(i, 1)]));
        console.log(storedDataObject.splice(i, 1));
      }
    }
  };

  const handleClick = () => {
    localStorage.clear();
  };
</script>

<button on:click={handleClick}>Clear Storage</button>

<!-- {#each storedDataObject as array}
  {#each array as { destination, cancelled }, i}
    <a href={`/#/stops/${destination.id}`}>
      {#if cancelled}
        <p>THIS SERVICE WAS CANCELLED</p>
      {/if} -->
<!-- <p>{destination.name}</p> -->
<p>The following transports are available on this location</p>
<!-- The next line takes the transport object and filter them, leaving only the ones that are true -->
<!-- {#each Object.keys(destination.products).filter((k) => destination.products[k]) as transport}
        <p>{transport}</p>
      {/each}
      <button on:click={() => handleDelete(array)}>Delete</button>
      <p>______________________________</p>
    </a>
  {/each}
{/each} -->
<p>Saved stops</p>
