<script setup>
import Navbar from './components/Navbar.vue';
</script>

<template>
  <!-- container -->
  <div class="w-full lg:w-[80%] mx-auto my-6 space-y-4">
    <Navbar />
    <div
      class="lg:grid lg:grid-cols-4 lg:grid-rows-6 lg:gap-4 w-full lg:min-h-250"
    >
      <div
        class="lg:col-span-3 lg:row-span-5 lg:order-1 rounded-2xl bg-blue-300 relative"
      >
        <video id="camera" class="h-full w-full"></video>
        <div
          class="rounded-full p-4 bg-purple-400 cursor-pointer grid place-items-center absolute left-1/2 -translate-x-1/2 bottom-2 lg:bottom-6"
        >
          <i class="pi pi-camera" style="font-size: 1.75rem" />
        </div>
      </div>
      <div
        class="lg:col-span-3 lg:row-span-1 lg:order-3 rounded-2xl min-w-80 max-sm bg-blue-400"
      >
        filter
      </div>
      <div
        class="lg:col-span-1 lg:row-span-6 lg:order-2 rounded-2xl bg-blue-200"
      >
        hasil foto
      </div>
    </div>
    <footer class="text-center p-4">
      Copyright &copy; Johan Adi Wimbanu - 2025
    </footer>
  </div>
</template>

<style scoped></style>
