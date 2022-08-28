<template>
  <el-container>
    <el-aside><TeamAside/></el-aside>
    <!-- <el-container> -->
    <topFrame/>
    <el-main> 
      <router-view></router-view>
    </el-main>
    <!-- </el-container> -->
  </el-container>
</template>

<script>
import topFrame from "../../components/topFrame.vue";
import TeamAside from "../../components/TeamAside.vue";
export default {
 components: {
    topFrame,
    TeamAside
},
}
</script>

<style scoped>

.el-main {
  background-color: rgba(244, 247, 249, 0.371);
  position: absolute;
  left: 200px;
  right: 0;
  top: 0px;
  bottom: 0;
  padding-top: 60px;
  overflow-y: scroll;
}
.el-aside{
  display: block;
  position: absolute;
  left: 0;
  top: 60px;
  bottom: 0;
}
.el-header {
  /* background-color: white; */
  text-align: left;
  color:black;
  /* box-shadow: 1px 1px 10px rgb(240, 242, 245); */
  box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);
}
</style>