<script setup>
import Map from '../components/Map.vue'
import { watch } from 'vue'
let file = $ref(null), goal = $ref(JSON.stringify({"goal": "exit"}))
let h = $ref(0), w = $ref(0)
let hShift = $ref(0), wShift = $ref(0)
let map = $ref([]), displayCoord = $ref(false)

watch(() => h, (h, prevH) => {
  if (h == prevH) return
  if (h > prevH) {
    console.log(1)
    for (let i = prevH; i < h; i++)
      map.push(new Array(w))
  } else if (h < prevH) {
    map.splice(h, prevH - h)
  }
})

watch(() => w, (w, prevW) => {
  if (w == prevW) return
  if (w > prevW) {
    for (let i = 0; i < h; i++)
      if (map[i].length < w) {
        for (let j = map[i].length; j < w; j++)
          map[i].push(null)
      }
  } else if (w < prevW) {
    for (let i = 0; i < h; i++)
      if (map[i].length > w) {
        map[i].splice(w, prevW - w)
      }
  }
})

// can only do positive positions
// TODO: add negative positions

function mapToDungeon (mapString, width, height) {
  mapStr = mapString
  mapString = JSON.parse(mapString)
  let maxHeight = 0;
  let maxWidth = 0;
  for (let j of mapString.entities) {
    if (j.y > maxHeight) maxHeight = j.y + 1;
    if (j.x > maxWidth) maxWidth = j.x + 1;
  }
  maxHeight += 1
  maxWidth += 1
  h = h || height || maxHeight || 15;
  w = w || width || maxWidth || 15;
  map = new Array(h)
  for (let i = 0; i < h; i++) {
    map[i] = new Array(w);
  }
  for (let j of mapString.entities) {
    map[j.y][j.x] = {
      ...j,
      'displayDetails': false
    }
  }
}

function createMap() {
  const entities = []
  map.forEach(row => {
    row.filter(i => i).forEach(i => entities.push(i))
  })
  const mapObj = {}
  mapObj.entities = entities
  mapObj['goal-condition'] = goal
  const mapStr = JSON.stringify(mapObj, undefined, 2)
  Swal.fire({
    title: 'Map info', 
    html: `<pre style="text-align: left">${mapStr}</pre>`
  })
}
</script>

<template>
  <div class="m-3">
    <label style="text-sm font-semibold">Height:</label>
    <input type="number" v-model="h" class="p-2 m-2 rounded text-sm border-2 border-solid border-violet-300">
    <label style="text-sm font-semibold">Width:</label>
    <input type="number" v-model="w" class="p-2 m-2 rounded text-sm border-2 border-solid border-violet-300">
    <button class="rounded-full px-4 py-2  m-2 font-semibold border-0 text-sm" :class="[map?.length === 0 ? 'bg-gray-300 text-white': 'bg-violet-50 hover:bg-violet-100 text-violet-700']" :disabled="map?.length === 0" @click="createMap">Create Map</button>
    <input type="checkbox" :value="displayCoord" @click="displayCoord = !displayCoord"> <label>Display Coordinates</label>
    <br />
    <textarea class="p-2 m-2 rounded text-sm border-2 border-solid border-violet-300" v-model="goal"></textarea>
    <Map :h="h" :w="w" :map="map" :displayCoord="displayCoord"></Map>
  </div>
</template>
