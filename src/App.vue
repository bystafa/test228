<template>
  <EmulatorConfigurator @add-floor="addFloor" @add-lift="addLift"/>
  <EmulatorMain :floors="floors" :lifts="lifts"/>
</template>

<script>
import { watch } from '@vue/runtime-core';
import EmulatorConfigurator from "./components/EmulatorConfigurator.vue";
import EmulatorMain from "./components/EmulatorMain.vue";
export default {
  components: {EmulatorMain, EmulatorConfigurator},
  data() {
    return {
      floors: [1,2,3,4,5],
      lifts: []
    }
  },
  methods: {
    addFloor() {
      this.floors.push(this.floors.length + 1)
    },
    addLift() {
      const newLift = {
        shaft: this.lifts.length + 1,
        floor: 1,
        status: 'free',
      };
      this.lifts.push(newLift)
    },
    parseLocalStorage(str) {
      return localStorage.getItem(str) ?
        JSON.parse(localStorage.getItem(str)) :
        [];
    }
  },
  mounted() {
    this.lifts = parseLocalStorage('liftList');
    this.floors = parseLocalStorage('floorsList');
  },
  watch: {
    floors() {
      localStorage.setItem('floorsList', JSON.stringify(this.floors))
    }
  }
}
</script>

<style scoped>
</style>
