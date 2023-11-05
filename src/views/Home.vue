<script setup>
import { useRouter } from "vue-router";
const router = useRouter();
</script>

<template>
  <div class="flex flex-col items-center justify-center h-screen w-screen">
    <p class="text-5xl font-bold">Dungeonmania Map Generator</p>
    <div class="py-12">
      <button
        class="bg-transparent hover:bg-violet-500 text-violet-700 font-semibold hover:text-white py-2 px-4 border border-violet-500 hover:border-transparent rounded mr-10"
        @click="router.push('load')"
      >
        LOAD
      </button>
      <button
        class="bg-transparent hover:bg-violet-500 text-violet-700 font-semibold hover:text-white py-2 px-4 border border-violet-500 hover:border-transparent rounded"
        @click="router.push('new')"
      >
        CREATE
      </button>
    </div>
    <div class="flex flex-col justify-center items-center w-full">
      <span class="font-bold text-3xl"> Created and written by </span>
      <span class="font-bold text-3xl pt-4"> Tina Ji and Webster Zhang </span>
    </div>
  </div>
</template>
