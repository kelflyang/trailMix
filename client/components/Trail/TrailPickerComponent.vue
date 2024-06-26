<script setup lang="ts">
// @ts-nocheck
import MapVisualizerComponent from "@/components/Map/MapVisualizerComponent.vue";
import { computed, ref } from "vue";
const props = defineProps(["trails", "trailValue"]);
const emit = defineEmits("update:trailValue");

let distance = ref(0);
let duration = ref(0);

const eventTrails = computed(() => {
  const filteredTrails = props.trails.filter((t) => t.event);
  return filteredTrails;
});

const selectedTrail = computed({
  get() {
    return props.trailValue;
  },
  set(value) {
    emit("update:trailValue", value);
  },
});

const updateDistanceTime = (info) => {
  distance.value = info.distance;
  duration.value = info.duration;
};

const itemProps = (item) => {
  return {
    title: item.name,
    subtitle: item.author,
  };
};

const trailSelectRules = [
  (v) => {
    if (v) return true;
    return "You must select a trail or create a new one!";
  },
];
</script>

<template>
  <v-row>
    <v-col md="6">
      <v-select
        color="#95b08d"
        variant="outlined"
        clearable
        v-model="selectedTrail"
        label="Select from previously constructed trails"
        :items="eventTrails"
        :itemProps="itemProps"
        :rules="trailSelectRules"
      >
      </v-select>
      <div v-if="selectedTrail">
        <h3>{{ selectedTrail.name }}</h3>
        <p>{{ selectedTrail.description }}</p>
        <p>Author: {{ selectedTrail.author }}</p>
        <p>Estimated distance: {{ distance }} miles</p>
        <p>Estimated time: {{ duration }} hours</p>
      </div>
    </v-col>
    <v-col md="6">
      <div class="section map-container">
        <MapVisualizerComponent mapRef="trail-picker-map" :trails="[selectedTrail]" @updateDistanceTime="updateDistanceTime" :draggable="false" />
      </div>
    </v-col>
  </v-row>
</template>

<style scoped>
.map-container {
  height: 50vh;
  position: relative;
}
#picker-map-container {
  position: absolute;
  height: 100%;
}
</style>
