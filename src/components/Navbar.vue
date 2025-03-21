<script setup>
import { Avatar, Button } from 'primevue';
import Toolbar from 'primevue/toolbar';
</script>

<template>
  <Toolbar
    class="w-[60%] mx-auto my-8"
    style="border-radius: 3rem; padding: 1rem 1rem 1rem 1.5rem"
  >
    <template #center>
      <div class="flex items-center justify-items-center gap-2">
        <i class="pi pi-camera pointer-events-none" style="font-size: 2.5rem" />
        <span class="font-black text-xl">funsnap</span>
      </div>
    </template>
  </Toolbar>
</template>
