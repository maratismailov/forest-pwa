<script>
  import { onMount } from "svelte";

  let location;

  onMount(() => {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(showPosition);
    } else {
      location = "Geolocation is not supported by this browser.";
    }
    console.log(location);
  });

  const showPosition = () => {
    location =
      "Latitude: " +
      position.coords.latitude +
      "<br>Longitude: " +
      position.coords.longitude;
  };

  const loadFile = e => {
    var output = document.getElementById('output');
    output.src = URL.createObjectURL(e.target.files[0]);
    output.onload = function() {
      URL.revokeObjectURL(output.src) // free memory
    }
  };
</script>

<style>

</style>

<main>
  <input type="file" accept="image/*" capture on:change={event => loadFile(event)} />
  <img id="output" alt="image"/>
  <div>{location}</div>
</main>
