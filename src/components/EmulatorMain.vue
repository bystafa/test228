<template>
  <div class="floors">
    <EmulatorLift v-for="lift in lifts" :lift="lift"
                    :style="{position:'absolute', left: `${lift.shaft * 90}px`}"/>
    <div class="floor" v-for="(floor,index) in floors" :key="index">
      <EmulatorBtn @click="liftCall(floor)" :isCalled="!!this.calls.find(c=>c.floor === floor)"/>
    </div>
  </div>
</template>

<script>
import EmulatorLift from "./EmulatorLift.vue";
import EmulatorBtn from "./EmulatorBtn.vue";

export default {
  name: "EmulatorMain",
  components: {EmulatorBtn, EmulatorLift},
  props: {
    floors: {
      type: Array,
      required: true,
      default: [1, 2, 3, 4, 5]
    },
    lifts: {
      type: Array,
      required: true,
      default: []
    },
  },
  data() {
    return {
      calls: []
    }
  },
  methods: {
    liftCall(floor) {
      if (this.lifts.find(l => (l.floor === floor || l.floor_to_move === floor))) {
        return
      }
      if (this.calls.find(c => c.floor === floor)) {
        return
      }
      this.calls = [...this.calls, {floor:floor, isCalled: false}]
    },
    liftMove(lift,floor) {
      lift.moving_distance = lift.floor - floor
      lift.status = 'moving';
      lift.floor_to_move = floor;
      list.time = Math.abs(lift.moving_distance) * 1000
      setTimeout(() => {
        lift.status = 'waiting';
        lift.floor = lift.floor_to_move
        setTimeout(() => {
          lift.status = 'free';
          this.calls = this.calls.filter(fl => fl.floor !== floor);
        }, 3000)
      }, list.time)
    },
    liftFindClosest(floor) {
      if (this.freeLifts.length)
        return this.freeLifts
            .reduce((p, n) => 
              Math.abs(p.floor - floor) < Math.abs(n.floor - floor) ? p : n
            )
    }
  },
  computed: {
    freeLifts() {
      return this.lifts.filter(l => l.status === 'free')
    },
  },
  watch: {
    calls(newVal) {
      const nextFloorCall = newVal.find(c => c.isCalled === false)
      if (!nextFloorCall || !this.freeLifts.length) {
        return;
      }
      const liftToMove = this.liftFindClosest(nextFloorCall.floor)
      this.liftMove(liftToMove, nextFloorCall.floor);
      nextFloorCall.isCalled = true;
    },
    lifts: {
      handler() {
        localStorage.setItem('liftList',JSON.stringify(this.lifts))
      },
      deep: true
    }
  }
}
</script>

<style scoped>
.floors {
  display: flex;
  flex-direction: column-reverse;
  position: relative;
}

.floor {
  width: 100vw;
  height: 100px;
  border-bottom: 4px solid grey;
  display: flex;
  flex-direction: row;
  justify-content: start;
  align-items: center;
}
</style>