<script>
  export let store;
  export let set;
  export let suggest;
  import { currentMarker } from "../stores";

  let mapWidth = 0;
  let scaledPos = [];
  let scaledSuggestions = [];

  function handleClick(evt) {
    if (!$currentMarker) return;
    if (store.password) {
      set(unscale(evt.layerX), unscale(evt.layerY), $currentMarker, store.password);
    } else {
      suggest(unscale(evt.layerX), unscale(evt.layerY), $currentMarker);
    }
  }

  $: if (store?.fairies) {
    scaledPos = Object.values(store?.fairies).map(({ x, y, ...rest }) => {
      return { x: scale(Number(x)), y: scale(Number(y)), ...rest };
    });
  }
  $: if (store?.suggestions) {
    scaledSuggestions = Object.values(store?.suggestions)
      .map(({ x, y, ...rest }) => {
        return { x: scale(Number(x)), y: scale(Number(y)), ...rest };
      });
  }

  function scale(point) {
    return (point / 42) * mapWidth;
  }

  function unscale(point) {
    return Math.floor((point * 42) / mapWidth * 10) / 10;
  }
</script>

<div bind:clientWidth={mapWidth} class="mx-20">
  <img on:click={handleClick}
       src={`/maps/${store.zone}.jpeg`}
       alt={`Map of Eureka ${store.zone}`} />
  {#each scaledPos as pos, i}
    <div style={`position: absolute; left: ${pos.x - 15}px; top: ${pos.y - 10}px;`}
         data-toggle="tooltip"
         data-title={`x${Object.values(store.fairies)[i].x}, y${Object.values(store.fairies)[i].y}`}
    >
      <img width={30}
           height={30}
           src={`/waymarks/map/${pos.marker}.png`}
           alt={`${pos.marker} marker`} />
    </div>
  {/each}
  {#each scaledSuggestions as pos, i}
    <div style={`position: absolute; left: ${pos.x - 15}px; top: ${pos.y - 10}px;`}
         data-toggle="tooltip"
         data-title={`Suggestion: x${Object.values(store.suggestions)[i].x}, y${Object.values(store.suggestions)[i].y}`}
    >
      <img width={30}
           height={30}
           src={`/waymarks/map/${pos.marker}.png`}
           alt={`${pos.marker} marker`}
           style="filter: grayscale(100%);"
      />
    </div>
  {/each}
</div>

<style>
  div:hover {
    cursor: crosshair;
  }
</style>
