<script>
  import { onMount } from "svelte";

  const url = window.location.href;
  const urlParams = url.substring(url.lastIndexOf("/") + 1);

  let data = "";

  onMount(async () => {
    const response = await fetch(`https://v5.vbb.transport.rest/stops/${urlParams}/departures?duration=10`);
    const data = await response.json();
    if (response.ok) {
      console.log(data);
      return data;
    } else {
      throw new Error(data);
    }
  });
</script>

{#await data}
  <p>Loading...</p>
{:then results}
  {console.log(results)}
  <p>asd</p>
{:catch}
  <p>Error: There is no stop with that identification</p>
{/await}
<p>{urlParams}</p>
