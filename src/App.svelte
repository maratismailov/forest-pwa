<script>
  import { onMount } from "svelte";

  let location;
  let images = [];

  const showPosition = position => {
    location =
      "Latitude: " +
      position.coords.latitude +
      "\nLongitude: " +
      position.coords.longitude;
  };

  const loadFile = e => {
    var output = document.getElementById("output");
    images.push(URL.createObjectURL(e.target.files[0]));
    images = images;
  };

  const getLocation = () => {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(showPosition);
    } else {
      location = "Geolocation is not supported by this browser.";
    }
  };
</script>

<style>

</style>

<main>

  <div class="image-upload">
    <label for="file-input">
      <img src="assets/photo.svg" style="max-height: 6vh"/>
    </label>

    <input
      id="file-input"
      type="file"
      accept="image/*"
      capture
      on:change={event => loadFile(event)} />
  </div>

  <hr />
  {#each images as image}
    <img src={image} alt="" />
  {/each}
  <hr />
  <input
    type="image"
    src="assets/pin.svg"
    style="max-height: 6vh"
    on:click={() => getLocation()} />
  <!-- <button style="background: url(assets/pin.svg)" on:click={() => getLocation()}>Get location</button> -->
  <hr />
  <div style="white-space: pre-line">{location}</div>
</main>
