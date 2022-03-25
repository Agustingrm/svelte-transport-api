<script>
  import Router from "svelte-spa-router";
  import Home from "./routes/home.svelte";
  import NotFound from "./routes/notFound.svelte";
  import Nav from "./components/Nav.svelte";
  import SavedStops from "./routes/savedStops.svelte";
  import { wrap } from "svelte-spa-router/wrap";
  import Departures from "./routes/[departures].svelte";

  let routes = {
    "/": Home,
    "/stops/:departures": wrap({
      asyncComponent: () => Departures,
    }),
    "/saved-stops": SavedStops,
    // If there is no match then
    "*": NotFound,
  };
</script>

<Nav />
<main>
  <Router {routes} />
</main>

<style>
</style>
