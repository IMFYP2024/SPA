<template>
  <div>
    <h1>實時地圖</h1>
    <div ref="map" style="height: 400px; width: 100%;"></div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';

let L; // 声明 L 变量，稍后赋值为 Leaflet 对象

const map = ref(null);
const isFirstLocation = ref(true); // 初始化 isFirstLocation
const userMarker = ref(null); // 初始化 userMarker

onMounted(async () => {
  if (process.client) {
    // 异步引入 Leaflet 库
    L = (await import('leaflet')).default;

    const mapInstance = L.map(map.value).setView([0, 0], 10);

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '© OpenStreetMap contributors'
    }).addTo(mapInstance);

    updateUserLocation(mapInstance);
    setInterval(() => updateUserLocation(mapInstance), 5000); // 每5秒更新一次位置

    addMarkers(mapInstance);

    const redIcon = new L.Icon({
      iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-red.png',
      iconSize: [25, 41],
      iconAnchor: [12, 41],
      popupAnchor: [1, -34],
      shadowSize: [41, 41]
    });

    function updateUserLocation(mapInstance) {
      navigator.geolocation.getCurrentPosition(position => {
        const lat = position.coords.latitude;
        const lng = position.coords.longitude;

        if (isFirstLocation.value) {
          mapInstance.setView([lat, lng], 15); // 第一次定位时缩放
          isFirstLocation.value = false; // 标记为非第一次定位
        } else {
          mapInstance.panTo([lat, lng]); // 更新定位时不缩放
        }
        if (!userMarker.value) {
          // 创建定位的标记并设置红色图标
          userMarker.value = L.marker([lat, lng], { icon: redIcon }).addTo(mapInstance);
        } else {
          userMarker.value.setLatLng([lat, lng]);
        }
      }, error => {
        console.error('获取用户位置失败:', error);
      });
    }

    function addMarkers(mapInstance) {
      const coordinates = [
        [24.149611, 120.683639],
        [24.150412633308154, 120.68306559887918],
        [24.149932396296602, 120.68247869372605],
        [24.1496005951253, 120.68297628722546],
        [24.150499948941835, 120.68385345525628],
        [24.15125377137894, 120.68286464766204],
        [24.151195561311393, 120.6839938021415],
        [24.151376012433865, 120.68231920860443],
        [24.152229312318074, 120.6834945118264]
      ];

      coordinates.forEach(coord => {
        const marker = L.marker(coord).addTo(mapInstance);
        marker.on('click', function(event) {
          const clickedLatlng = event.latlng;
          const popupContent = `
            <div style="text-align:center;">
              <p>Hello</p>
              <p>您点击了坐标：${clickedLatlng.lat.toFixed(6)}, ${clickedLatlng.lng.toFixed(6)}</p>
              <button id="myButton" style="background-color:blue;color:white;padding:5px 10px;border:none;">显示目标点</button>
            </div>
          `;
          const popup = L.popup()
            .setLatLng(clickedLatlng)
            .setContent(popupContent)
            .openOn(mapInstance);
        });
      });
    }
  }
});
</script>


<style>
/* Leaflet's default icon path fix */
.leaflet-marker-icon {
  background-image: url('leaflet/dist/images/marker-icon.png');
}
.leaflet-container {
  background-color: #f4f4f4;
}
</style>