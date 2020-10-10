<template>
  <div>
    <Machine v-for="(item, i) in machines" v-bind:key="i"
             v-bind:machine="item"/>
  </div>
</template>

<script>
import Machine from "@/components/Machine";

export default {
  name: "MachineList",
  components: {
    Machine
  },
  props: ['machines']
}
</script>

<style scoped>

</style>