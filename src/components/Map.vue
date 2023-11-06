<script setup>
const { h, w, map, displayCoord } = defineProps([
  "h",
  "w",
  "map",
  "displayCoord",
]);

function showDetails(item) {
  if (!item) return;
  item.displayDetails = true;
}

function hideDetails(item) {
  if (!item) return;
  item.displayDetails = false;
}

const types = [
  "null",
  "player",
  "wall",
  "exit",
  "boulder",
  "switch",
  "door",
  "portal",
  "zombie_toast_spawner",
  "spider",
  "zombie_toast",
  "mercenary",
  "treasure",
  "key",
  "wood",
  "arrow",
  "bomb",
  "sword",
  "assassin",
  "hydra",
  "swamp_tile",
  "sun_stone",
  "time_turner",
  "time_travelling_portal",
  "light_bulb_off",
  "light_bulb_on",
  "wire",
  "switch_door",
  "invincibility_potion",
  "invisibility_potion",
  "midnight_armour",
  "snake_head",
];
async function updateItem(item, y, x) {
  const { value: index } = await Swal.fire({
    title: "Update current grid",
    input: "select",
    inputOptions: types,
    inputPlaceholder: "Select a new type",
    showCancelButton: true,
  });
  if (!index || types[index] === item?.type) {
    return;
  }
  const t = types[index];
  if (t === "null") {
    map[x][y] = null;
    return;
  }
  if (
    item &&
    (item.type === "door" ||
      (item.type === "key" && t !== "door" && t !== "key"))
  )
    delete item.key;
  if (item && item.type === "portal") delete item.colour;
  if (t === "door" || t === "key") {
    const { value: key } = await Swal.fire({
      title: "Please enter the key",
      input: "number",
      inputPlaceholder: "enter the key",
      showCancelButton: true,
    });
    if (!key) return;
    if (!item) map[x][y] = { key };
    else map[x][y].key = key;
  }
  if (t === "swamp_tile") {
    const { value: key } = await Swal.fire({
      title: "Please enter the movement factor",
      input: "number",
      inputPlaceholder: "enter the movement factor",
      showCancelButton: true,
    });
    if (!key) return;
    if (!item) map[x][y] = { movement_factor: key };
    else map[x][y]["movement_factor"] = key;
  }
  if (t === "portal") {
    const { value: colour } = await Swal.fire({
      title: "Please choose portal colours",
      input: "select",
      inputOptions: { BLUE: "BLUE", RED: "RED", GREEN: "GREEN" },
      inputPlaceholder: "choose a colour",
      showCancelButton: true,
    });
    if (!colour) return;
    if (!item) map[x][y] = { colour };
    else map[x][y].colour = colour;
  }
  map[x][y] = {
    ...map[x][y],
    x: y,
    y: x,
    type: t,
  };
}
</script>

<template v-if="w > 0 && h > 0">
  <div
    v-for="(row, j) in map"
    :key="row"
    class="flex flex-row ml-3 h-20 w-full"
  >
    <div v-for="(item, i) in row" :key="item">
      <div
        @mouseover="showDetails(item)"
        @mouseleave="hideDetails(item)"
        @click="updateItem(item, i, j)"
        class="bg-gray-200 w-16 h-20 flex z-0 justify-center items-center m-0 flex-wrap"
      >
        <img
          v-if="item"
          class="w-12 h-12 relative z-1"
          :src="`/img/${item.type}.png`"
        />
        <!-- <img v-if="item" class="w-12 h-12 relative z-1" :src="`/img/${item.type}.png`"> -->
        <div v-else class="w-12 h-12 z-1 bg-blue-300 opacity-20" />
        <p v-if="displayCoord" class="text-xs whitespace-nowrap">
          ({{ i }}, {{ j }})
        </p>
        <div
          v-if="item?.displayDetails"
          class="fixed z-2 bg-white p-2 rounded opacity-70"
        >
          <div v-for="(v, k) in item" :key="k" class="relative z-2">
            <p v-if="k !== 'displayDetails'">{{ k }}: {{ v }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
