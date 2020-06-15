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

  const showPosition = position => {
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

  const getLocation = () => {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(showPosition);
  } else { 
    location = "Geolocation is not supported by this browser.";
  }
}

</script>

<style>

</style>

<main>
  <input type="file" accept="image/*" capture on:change={event => loadFile(event)} />
  <hr>
  <img id="output" alt="imageu"/>
  <hr>
  <button on:click={() => getLocation()}>Get location</button>
  <div>{location}</div>
</main>
