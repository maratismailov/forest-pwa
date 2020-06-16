<script>
  import { Route, active } from "tinro";
  import Location from "./components/Location.svelte";
  import Photo from "./components/Photo.svelte";
  import Map from "./components/Map.svelte";
</script>

<style>

</style>

<main>
  <div class="scrollmenu">
    <a href="/" use:active exact>Photo</a>
    <a href="/location" use:active exact>Location</a>
    <a href="/map" use:active exact>Map</a>
  </div>

</main>

<Route path="/">
  <Photo />
</Route>
<Route path="/location">
  <Location />
</Route>
<Route path="/map">
  <Map />
</Route>
<!-- <Route path="/currentdustazure">
  <CurrentDustAzure />
</Route>
<Route path="/heatmap">
  <Heatmap />
</Route> -->
