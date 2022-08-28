<template>
  <div>
    <drawio-digram :del = "true"/>
  </div>
</template>

<script>
import drawioDigram from "@/components/drawioDiagram";
export default {
  name: "testpage",
  components: {drawioDigram}
}
</script>

<style scoped>

</style>