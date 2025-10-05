<script setup>
import MapView from './components/MapView.vue'
import BottomSheet from './components/BottomSheet.vue'
import QuizModal from './components/QuizModal.vue'
import { ref } from 'vue'

const quizOpen = ref(false)
const mapPoints = ref([])
const sheetRef = ref(null)
const mapRef = ref(null)
function onPickArea() {
  quizOpen.value = true
}

function onQuizComplete(payload) {
  quizOpen.value = false
  // свернуть нижнюю панель полностью
  try { sheetRef.value && sheetRef.value.collapseFully && sheetRef.value.collapseFully() } catch {}
  // Запускаем режим районов с теплокартой по данным из JSON
  try { mapRef.value && mapRef.value.setFilters && mapRef.value.setFilters(payload && payload.filters ? payload.filters : {}) } catch {}
  try { mapRef.value && mapRef.value.showDistricts && mapRef.value.showDistricts() } catch {}
}

const selectedDistrict = ref(null)
const selectedCluster = ref(null)

function onDistrictSelected(payload) {
  selectedDistrict.value = payload
  selectedCluster.value = null
}

function onClusterSelected(payload) {
  selectedCluster.value = payload
}

function onClusterBack() {
  selectedCluster.value = null
  try { mapRef.value && mapRef.value.backToDistrictClusters && mapRef.value.backToDistrictClusters() } catch {}
}

function onDistrictBack() {
  // Сбросить выбранный район, вернуться к общему виду (районы + теплокарта по всем данным)
  selectedDistrict.value = null
  selectedCluster.value = null
  try { mapRef.value && mapRef.value.showDistricts && mapRef.value.showDistricts() } catch {}
}
</script>

<template>
  <div class="app-layout">
    <MapView ref="mapRef" :points="mapPoints" @district-selected="onDistrictSelected" @cluster-selected="onClusterSelected" />
    <BottomSheet ref="sheetRef" @pick-area="onPickArea" :district="selectedDistrict" :cluster="selectedCluster" @cluster-back="onClusterBack" @district-back="onDistrictBack" />
    <QuizModal v-model="quizOpen" @complete="onQuizComplete" />
  </div>
  
</template>

<style scoped>
.app-layout {
  position: fixed;
  inset: 0;
}
</style>
