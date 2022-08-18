<script setup>
import Map from '../components/Map.vue'
let file = $ref(null), mapStr = $ref('')
let h = $ref(0), w = $ref(0)
let map = $ref([]), displayCoord = $ref(false)

function display(e) {
  h = 0, w = 0, map = []
  file = e.target.files[0]
  const reader = new FileReader()
  reader.onload = (res) => {
    mapToDungeon(res.target.result)
  }
  reader.readAsText(file)
}

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

function updateMap() {
  const entities = []
  map.forEach(row => {
    row.filter(i => i).forEach(i => entities.push(i))
  })
  const mapObj = JSON.parse(mapStr)
  mapObj.entities = entities
  mapStr = JSON.stringify(mapObj, undefined, 2)
  Swal.fire({
    title: 'Map info', 
    html: `<pre style="text-align: left">${mapStr}</pre>`
  })
}
</script>

<template>
  <div class="m-3">
    <input type="file" accept=".json" @change="display" class="text-sm text-slate-500
      file:mr-4 file:py-2 file:px-4
      file:rounded-full file:border-0
      file:text-sm file:font-semibold
      file:bg-violet-50 file:text-violet-700
      hover:file:bg-violet-100
      hover:file:cursor-pointer">
    <button class="rounded-full px-4 py-2  m-2 font-semibold border-0 text-sm" :class="[map?.length === 0 ? 'bg-gray-300 text-white': 'bg-violet-50 hover:bg-violet-100 text-violet-700']" :disabled="map?.length === 0" @click="updateMap">Update Map</button>
    <input type="checkbox" :value="displayCoord" @click="displayCoord = !displayCoord"> <label>Display Coordinates</label>
    <Map :h="h" :w="w" :map="map" :displayCoord="displayCoord"></Map>
  </div>
</template>
