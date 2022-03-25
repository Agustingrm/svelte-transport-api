<script>
  import { afterUpdate } from "svelte";
  import { savedStops } from "../stores";
  export let params;
  console.log(params.stops);

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
      promise = "";
      promise = getDepartureData();
    }
  });

  const handleClick = () => {
    //This codes checks if the station was already bookmarked. If it is, it wont add it again
    savedStops.update(() => {
      let savedInLocalStorage = JSON.parse(localStorage.getItem("saved stops"));
      if (savedInLocalStorage === null) {
        localStorage.setItem("saved stops", JSON.stringify([[data, params.stops]]));
        console.log([[data, params.stops]]);
      } else {
        let thereAreDuplicates = false;
        for (let i = 0; i < savedInLocalStorage.length; i++) {
          if (JSON.stringify(savedInLocalStorage[i]) === JSON.stringify([data, params.stops])) {
            thereAreDuplicates = true;
          }
        }
        if (!thereAreDuplicates) {
          localStorage.setItem("saved stops", JSON.stringify([[data, params.stops], ...savedInLocalStorage]));
          console.log([[data, params.stops], ...savedInLocalStorage]);
        }
      }
    });
  };
</script>

<div class="container">
  {#await promise}
    <p>Loading...</p>
  {:then results}
    <h2>{params.stops}</h2>
    <p>From {params.stops} you can go to the following stops in the next minutes</p>
    {#if results.length == 0}
      <p>There are no services going out from this location in the next minutes</p>
    {/if}
    {#each results as { destination, cancelled, when }}
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
          <p>The following transports are available on this location</p>
          <!-- The next line takes the transport object and filter them, leaving only the ones that are true -->
          <div class="transports">
            {#each Object.keys(destination.products).filter((k) => destination.products[k]) as transport}
              <p>{transport}</p>
            {/each}
          </div>
        </a>
      </div>
    {/each}
    {#if results.length !== 0}
      <button on:click={handleClick}>Bookmark this Stop!</button>
    {/if}
  {:catch}
    <p>Error: There is no stop with that identification</p>
    <a href="/#/">Go to Home</a>
  {/await}
</div>

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
  button {
    position: absolute;
    top: 90px;
    right: 30px;
    color: black;
    background-color: rgb(0, 179, 0);
  }
  button:hover {
    cursor: pointer;
  }
  @media all and (max-width: 400px) {
    h2 {
      margin-top: 80px;
    }
  }
</style>
