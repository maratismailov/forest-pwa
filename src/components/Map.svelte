<script>
  import L from "leaflet";
  import axios from "axios";
  import "../../public/leaflet/Bing.js";
  import "../../public/leaflet/Leaflet.Editable.js";
  import { onMount } from "svelte";
  // import "leaflet/dist/leaflet.css";
  let height = (window.innerHeight * 0.35).toString() + "px";
  let map;

  let center = [42.87, 74.594];
  let zoom = 17;
  let lat;
  let lon;
  let standsdata = [];
  let currentstand;
  let selected = "";
  let area = ''
  let area_id = 0
  let multi_json;
  let id;
  const api_key =
    "AijiWK2E56tAWqQiXj0TpzHR4V0xb0wDyCUzeUIqjbuPuwoFPP2kiWNi6TUVMpBn";

  onMount(() => {
    const getLocation = () => {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(showPosition);
      } else {
        location = "Geolocation is not supported by this browser.";
      }
    };
    createMap();
    L.NewPolygonControl = L.Control.extend({
      options: {
        position: "topleft"
      },

      onAdd: function(map) {
        var container = L.DomUtil.create("div", "leaflet-control leaflet-bar"),
          link = L.DomUtil.create("a", "", container);

        link.href = "#";
        link.title = "Create a new polygon";
        link.innerHTML = "▱";
        L.DomEvent.on(link, "click", L.DomEvent.stop).on(
          link,
          "click",
          function() {
            map.editTools.startPolygon();
          }
        );
        container.style.display = "block";
        map.editTools.on("editable:enabled", function(e) {
          container.style.display = "none";
        });
        map.editTools.on("editable:disable", function(e) {
          container.style.display = "block";
        });
        return container;
      }
    });
    L.AddPolygonShapeControl = L.Control.extend({
      options: {
        position: "topleft"
      },

      onAdd: function(map) {
        var container = L.DomUtil.create("div", "leaflet-control leaflet-bar"),
          link = L.DomUtil.create("a", "", container);

        link.href = "#";
        link.title = "Create a new polygon";
        link.innerHTML = "▱▱";
        L.DomEvent.on(link, "click", L.DomEvent.stop).on(
          link,
          "click",
          function() {
            if (!map.editTools.currentPolygon) return;
            map.editTools.currentPolygon.editor.newShape();
          }
        );
        container.style.display = "none";
        map.editTools.on("editable:enabled", function(e) {
          container.style.display = "block";
        });
        map.editTools.on("editable:disable", function(e) {
          container.style.display = "none";
        });

        return container;
      }
    });

    map.addControl(new L.NewPolygonControl());
    map.addControl(new L.AddPolygonShapeControl());
    map.on("layeradd", function(e) {
      if (e.layer instanceof L.Polygon)
        e.layer
          .on("dblclick", L.DomEvent.stop)
          .on("dblclick", e.layer.toggleEdit);
    });
    map.on("layerremove", function(e) {
      if (e.layer instanceof L.Polygon)
        e.layer
          .off("dblclick", L.DomEvent.stop)
          .off("dblclick", e.layer.toggleEdit);
    });
    map.editTools.on("editable:enable", function(e) {
      if (this.currentPolygon) this.currentPolygon.disableEdit();
      this.currentPolygon = e.layer;
      this.fire("editable:enabled");
    });
    map.editTools.on("editable:disable", function(e) {
      delete this.currentPolygon;
    });

    axios.get("https://gd.caiag.kg/forestpwagetstands").then(response => {
      const pre_data = response.data;
      refineData(pre_data);
    });
  });

  const showPosition = position => {
    lat = position.coords.latitude;
    lon = position.coords.longitude;
    center = [lat, lon];
  };

  const createMap = () => {
    map = L.map("map", {
      editable: true
    }).setView(center, zoom);
    const bing = new L.BingLayer(api_key);
    map.addLayer(bing);
  };

  const resize = () => {
    height = (window.innerHeight * 0.35).toString() + "px";
  };

  const refineData = data => {

    standsdata = data.map(elem => {
      console.log(elem)
      return {
        id: elem[0],
        area: JSON.parse(elem[1]),
        line: elem[2],
        point1: elem[3],
        point2: elem[4]
      };
    });
  };

  const handle_select = () => {
    id = selected.id
    // var multi = L.polygon([
    //   [
    //     [
    //       [43.1239, 1.244],
    //       [43.123, 1.253],
    //       [43.1252, 1.255],
    //       [43.125, 1.251],
    //       [43.1239, 1.244]
    //     ],
    //     [[43.124, 1.246], [43.1236, 1.248], [43.12475, 1.25]],
    //     [[43.124, 1.251], [43.1236, 1.253], [43.12475, 1.254]]
    //   ],
    //   [[[43.1269, 1.246], [43.126, 1.252], [43.1282, 1.255], [43.128, 1.245]]]
    // ]).addTo(map);
    // map.fitBounds(multi.getBounds());
    // multi.enableEdit();

    console.log(selected);
    area = L.geoJSON(selected.area).addTo(map);
    area_id = area._leaflet_id
    console.log(area_id)
    // let multi = area.addTo(map);
    // map.setView([42.87, 74.594], 5)
    map.fitBounds(area.getBounds());
    area.getLayers().forEach(l => {
      l.enableEdit();
    });

    // const geo = L.geoJSON(geoJsonObj).addTo(this.map);
    // geo.enableEdit();
    // geo.getLayers().forEach(l => l.enableEdit())
  };
  const refresh_area = () => {
    area = area
    multi_json = JSON.stringify(area.toGeoJSON().features[0].geometry)
    // multi_json = JSON.stringify(area.toGeoJSON())
    // multi_json = `"{"type":"MultiPolygon","coordinates":[[[[73.6080423093854,40.9031889211661],[73.6080491542851,40.9031985718277],[73.60810966006,40.9032319486259],[73.6083723570599,40.9032472401287],[73.6088357437687,40.9032804444176],[73.6091020723693,40.9032994361655],[73.6093924580782,40.9033386984018],[73.6095292258252,40.9033268512584],[73.6095997170215,40.9032914068196],[73.6096919289181,40.9031583008782],[73.6100210439812,40.9029090388043],[73.6104359438997,40.9026729165784],[73.6107533848054,40.9025900965791],[73.6111391560585,40.9025742319935],[73.6115528798881,40.9025874073581],[73.6118850208436,40.9026167577287],[73.6120420056247,40.9024022669173],[73.6119171131475,40.9023259029403],[73.6116356647473,40.9022147182191],[73.6114877366345,40.9021929463547],[73.6112901468683,40.9021688841074],[73.6105832529729,40.9023193016028],[73.6096549913538,40.9024999511465],[73.6091470444447,40.902644894773],[73.6090406747354,40.9026754013972],[73.6082547589697,40.9029661049602],[73.6080703497972,40.9030907735845],[73.6080282496724,40.9031696157854],[73.6080423093854,40.9031889211661]]]]}"`
    console.log(multi_json)
    axios.post("https://gd.caiag.kg/forestpwawritemultipoly?geojson=" + `'` + multi_json + `'` + '&id=' + id).then(response => {
      const pre_data = response.data;
    });

  }
</script>

<style>
  .map {
    width: 98vw;
    margin: auto;
  }
</style>

<svelte:window on:resize={resize} on:orientationchange={resize} />

<div style="height: {height}" class="map" id="map">
  <slot />
</div>

<br />

<p>Выберите выдел</p>

<select bind:value={selected} on:change={handle_select}>
  {#each standsdata as stand}
    <option value={stand}>{stand.id}</option>
  {/each}
</select>

<button on:click={refresh_area}>Refresh Selected</button>

{#if area != ''}
  {#each area._layers[area_id - 1]._latlngs[0][0] as coordinate, index}
    <p>{index}</p>
    <p>{coordinate}</p>
  {/each}
{/if}
