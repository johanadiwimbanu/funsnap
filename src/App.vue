<script setup>
import { ref, onMounted } from 'vue';
import Navbar from './components/Navbar.vue';

const videoEl = ref(null);
const isFrontCamera = ref(true);
const currentStream = ref(null);

const startCamera = async () => {
  if (currentStream) {
    currentStream.value.getTracks().forEach((track) => track.stop());
  }
  try {
    const stream = await navigator.mediaDevices.getUserMedia({
      video: {
        facingMode: isFrontCamera.value ? 'user' : 'environment',
      },
    });
    if (videoEl.value) {
      videoEl.value.srcObject = stream;
      currentStream = stream;
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

onMounted(startCamera());
</script>

<template>
  <!-- container -->
  <div
    class="w-full lg:w-[80%] mx-auto my-4 px-4 flex flex-col justify-between gap-6"
  >
    <Navbar />
    <div
      class="grid grid-cols-1 lg:grid-cols-4 lg:grid-rows-6 gap-2 w-full min-h-96 h-"
    >
      <div class="lg:col-span-3 lg:row-span-5 lg:order-1 relative">
        <button
          @click="toggleCamera"
          class="z-10 absolute group top-2 right-2 text-white font-black bg-black/50 p-2 rounded-full cursor-pointer"
        >
          <i
            class="pi pi-sync !text-xl transition-transform duration-300 group-hover:rotate-135"
            :class="isFrontCamera ? 'rotate-90' : 'rotate-0'"
          ></i>
        </button>
        <video
          autoplay
          playsinline
          ref="videoEl"
          id="camera"
          class="h-[32rem] rounded-2xl lg:h-auto w-full object-cover overflow-hidden"
        ></video>
        <div
          class="rounded-full px-4 py-3 bg-black/50 group text-white cursor-pointer grid place-items-center absolute left-1/2 -translate-x-1/2 bottom-4"
        >
          <i class="pi pi-camera !text-xl" />
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
    <footer class="text-center p-2">
      Copyright &copy; Johan Adi Wimbanu - 2025
    </footer>
  </div>
</template>

<style scoped></style>
