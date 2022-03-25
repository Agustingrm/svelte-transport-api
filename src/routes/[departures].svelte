<script>
  import { afterUpdate } from "svelte";
  import { savedStops } from "../stores";
  export let params;
  console.log(params);

  let data = "";
  let promise = "";
  let stopId = "";

  let getDepartureData = async () => {
    stopId = params.departures;
    const response = await fetch(
      `https://v5.vbb.transport.rest/stops/${params.departures}/departures?duration=10`
    );
    data = await response.json();
    console.log(data);
    if (response.ok) {
      return data;
    } else {
      throw new Error(data);
    }
  };
  promise = getDepartureData();

  afterUpdate(() => {
    if (stopId !== params.departures) {
      console.log("asd");
      promise = "";
      promise = getDepartureData();
    }
  });

  const handleClick = () => {
    //This codes checks if the station was already bookmarked. If it is, it wont add it again
    savedStops.update(() => {
      let savedInLocalStorage = JSON.parse(localStorage.getItem("saved stops"));
      if (savedInLocalStorage === null) {
        localStorage.setItem("saved stops", JSON.stringify([data]));
      } else {
        let thereAreDuplicates = false;
        for (let i = 0; i < savedInLocalStorage.length; i++) {
          if (JSON.stringify(savedInLocalStorage[i]) === JSON.stringify(data)) {
            thereAreDuplicates = true;
          }
        }
        if (!thereAreDuplicates) {
          localStorage.setItem("saved stops", JSON.stringify([data, ...savedInLocalStorage]));
        }
      }
    });
  };
</script>

{#await promise}
  <p>Loading...</p>
{:then results}
  {#each results as { destination, cancelled }}
    <a href={`/#/stops/${destination.id}`}>
      {#if cancelled}
        <p>THIS SERVICE WAS CANCELLED</p>
      {/if}
      <p>{destination.name}</p>
      <p>The following transports are available on this location</p>
      <!-- The next line takes the transport object and filter them, leaving only the ones that are true -->
      {#each Object.keys(destination.products).filter((k) => destination.products[k]) as transport}
        <p>{transport}</p>
      {/each}
      <p>______________________________</p>
    </a>
    {#if data == []}
      <p>There are no services going out from this location in the next minutes</p>
    {/if}
  {/each}
  <button on:click={handleClick}>Bookmark this Stop!</button>
{:catch}
  <p>Error: There is no stop with that identification</p>
  <a href="/#/">Go to Home</a>
{/await}
<p>{params.departures}</p>
<p>{params.departures}</p>
<p>{params.departures}</p>
<p>{params.departures}</p>
<p>{params.departures}</p>
