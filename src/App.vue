<script setup>
import { ref, onMounted, shallowRef } from 'vue';
import sampleImage from './../public/sample.jpg';
import Button from 'primevue/button';

const videoEl = shallowRef(null);
const currentStream = shallowRef(null);
const isFrontCamera = ref(true);
const photos = ref([]);
const isMultiVideoInputDevice = ref(null);
const currentFilter = ref('none');
const filters = [
  { name: 'Normal', value: 'none' },
  { name: 'Grayscale', value: 'grayscale(100%)' },
  { name: 'Sepia', value: 'sepia(80%)' },
  { name: 'Blur', value: 'blur(4px)' },
  { name: 'Brightness', value: 'brightness(1.5)' },
  { name: 'Contrast', value: 'contrast(200%)' },
];

const checkIsMultiVideoInputDevice = async () => {
  console.log('cek device');
  const inputDevices = await navigator.mediaDevices.enumerateDevices();
  const videoInputDevices = inputDevices.filter(
    (device) => device.kind === 'videoinput'
  );
  isMultiVideoInputDevice.value = videoInputDevices.length > 1;
};

const startCamera = async () => {
  console.log('render camera');
  if (currentStream) {
    currentStream.value?.getTracks().forEach((track) => track.stop());
  }
  try {
    const stream = await navigator.mediaDevices.getUserMedia({
      video: {
        facingMode: isFrontCamera.value ? 'user' : 'environment',
      },
    });
    if (videoEl.value) {
      videoEl.value.srcObject = stream;
      currentStream.value = stream;
    }
  } catch (error) {
    alert(error);
    console.error('Gagal mengakses kamera:', error);
  }
};

const toggleCamera = () => {
  isFrontCamera.value = !isFrontCamera.value;
  startCamera();
};

const takePhoto = () => {
  if (!videoEl.value || !currentStream.value) return;

  const canvas = document.createElement('canvas');
  canvas.width = videoEl.value.videoWidth;
  canvas.height = videoEl.value.videoHeight;

  const ctx = canvas.getContext('2d');

  ctx.filter = currentFilter.value;
  ctx.drawImage(videoEl.value, 0, 0, canvas.width, canvas.height);

  // Mengambil URL data dari canvas
  const photoUrl = canvas.toDataURL('image/jpeg');

  const newPhotos = [...photos.value];
  newPhotos.unshift({
    id: Date.now(),
    url: photoUrl,
    timestamp: new Date().toISOString(),
  });
  photos.value = newPhotos;

  console.log('Photo taken!');
};

const handleSetCurrentFilter = (filter) => {
  currentFilter.value = filter;
};

const handleMaintenance = () => {
  alert('Tak totokno lek ndak wegah');
};

onMounted(() => {
  checkIsMultiVideoInputDevice();
  startCamera();
});
</script>

<template>
  <div class="flex flex-col min-h-screen lg:h-screen bg-gray-900 text-white">
    <!-- Header -->
    <header
      class="p-4 flex justify-between items-center bg-black sticky top-0 z-50"
    >
      <h1 class="text-2xl font-bold flex items-center">
        <i class="pi pi-camera mr-2"></i>
        <span class="text-white"
          >fun<span class="text-blue-400">snap</span></span
        >
      </h1>
      <div class="flex space-x-4">
        <Button
          icon="pi pi-share-alt"
          @click="handleMaintenance"
          class="p-button-rounded p-button-text"
        />
        <Button
          icon="pi pi-download"
          @click="handleMaintenance"
          class="p-button-rounded p-button-text"
        />
      </div>
    </header>

    <!-- Main Content -->
    <div class="flex flex-1 flex-col lg:flex-row overflow-hidden">
      <!-- Sidebar -->
      <div
        class="w-full lg:w-16 order-3 lg:order-0 bg-gray-900 border-r border-gray-800 flex lg:flex-col items-center py-4 gap-4 lg:space-y-6"
      >
        <div class="hide lg:flex-1"></div>
        <a
          target="_blank"
          href="https://github.com/johanadiwimbanu/funsnap"
          class="p-link p-button-rounded p-button-text"
        >
          <i class="pi pi-github"></i>
        </a>
        <a
          target="_blank"
          href="https://www.linkedin.com/in/johan-adi-wimbanu-1b0a2b27a/"
          class="p-link p-button-rounded p-button-text"
        >
          <i class="pi pi-linkedin text-blue-500"></i>
        </a>
        <a
          target="_blank"
          href="https://www.instagram.com/_awjohn/"
          class="p-link p-button-rounded p-button-text"
        >
          <i class="pi pi-instagram text-pink-500"></i>
        </a>
      </div>

      <!-- Main Image Area -->
      <div class="flex-1 flex flex-col">
        <div class="flex-1 bg-black flex items-center justify-center p-4">
          <div
            class="relative w-full max-w-4xl aspect-video bg-gray-800 rounded-lg overflow-hidden"
          >
            <div class="absolute top-6 right-4 z-10">
              <Button
                v-if="isMultiVideoInputDevice"
                @click="toggleCamera"
                :class="isFrontCamera ? 'rotate-90' : 'rotate-0'"
                icon="pi pi-sync"
                class="p-button-rounded p-button-secondary"
              />
            </div>
            <video
              autoplay
              playsinline
              ref="videoEl"
              id="camera"
              :style="{ filter: currentFilter }"
              class="rounded-2xl w-full h-full object-fit overflow-hidden"
            ></video>
            <div class="absolute bottom-4 right-4">
              <Button
                icon="pi pi-camera"
                class="p-button-rounded p-button-info"
                @click="takePhoto"
              />
            </div>
          </div>
        </div>

        <!-- Filter Options -->
        <div class="bg-black p-4">
          <h3 class="mb-3 text-gray-400 font-medium">Filters</h3>
          <div class="flex space-x-4 overflow-x-auto p-2">
            <div
              v-for="filter in filters"
              :key="filter.name"
              @click="handleSetCurrentFilter(filter.value)"
              class="flex flex-col items-center cursor-pointer transition-all group"
              :class="{
                'scale-95': currentFilter === filter.value,
                'scale-100 hover:scale-95': currentFilter !== filter.value,
              }"
            >
              <div
                class="w-16 h-16 rounded-lg flex items-center justify-center overflow-hidden ring-1 ring-gray-700"
                :class="{
                  'border-blue-500 border': currentFilter === filter.value,
                  'ring-1 ring-gray-700': currentFilter !== filter.value,
                }"
              >
                <img
                  :src="sampleImage"
                  :style="{ filter: filter.value }"
                  alt="Sample photo"
                  class="rounded w-full object-cover"
                />
              </div>
              <span
                class="text-xs"
                :class="{
                  'text-blue-400': currentFilter === filter.value,
                  'text-gray-400 group-hover:text-gray-300':
                    currentFilter !== filter.value,
                }"
              >
                {{ filter.name }}
              </span>
            </div>
          </div>
        </div>
      </div>

      <!-- Right Sidebar -->
      <div class="w-full lg:w-64 bg-gray-900 border-l border-gray-800 p-4">
        <h3 class="text-gray-400 font-medium">Recent Captures</h3>
        <div
          class="grid grid-cols-2 lg:flex lg:flex-col gap-2 lg:overflow-y-auto w-full h-full custom-scrollbar"
        >
          <div
            v-for="photo in photos"
            :key="photo.id"
            class="p-card mb-2 p-2 cursor-pointer hover:border-blue-500 border-2 border-transparent transition-all"
          >
            <img :src="photo.url" class="w-full h-32 object-contain rounded" />
          </div>
        </div>
      </div>
    </div>

    <!-- Footer -->
    <footer class="p-2 text-center bg-black text-gray-600 text-xs">
      Copyright © FunSnap - 2025
    </footer>
  </div>
</template>

<style scoped>
.custom-scrollbar::-webkit-scrollbar {
  width: 6px; /* untuk scroll vertikal */
  height: 6px; /* untuk scroll horizontal */
}

.custom-scrollbar::-webkit-scrollbar-thumb {
  background-color: rgba(0, 0, 0, 0.2); /* warna thumb */
  border-radius: 4px;
}
</style>
