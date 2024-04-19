<template>
  <div>
    <a-scene arjs="sourceType: webcam; videoTexture: true; debugUIEnabled: false" renderer="antialias: true; alpha: true">
      <a-camera gps-new-camera="gpsMinDistance: 5"></a-camera>
      <a-entity v-for="(location, index) in locations" :key="index" :material="`color: ${location.color}`" geometry="primitive: box" :gps-new-entity-place="location"></a-entity>
    </a-scene>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const locations = ref([
  { latitude: 24.150979213419177, longitude: 120.68160754026252, color: 'red' },
  { latitude: 24.151025939583285, longitude: 120.68141029301923, color: 'blue' },
  { latitude: 24.149815016080225, longitude: 120.68377522548887, color: 'green' },
  { latitude: 24.14995472170715, longitude: 120.6831723718261, color: 'black' },
  { latitude: 24.15050772164821, longitude: 120.68400807372899, color: 'gray' },
  { latitude: 24.1504262270704, longitude: 120.6831691821242, color: 'pink' }
]);

import 'AR.js';
import 'Aframe';
src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js";
</script>
