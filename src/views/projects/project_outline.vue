<template>
  <div id="graphContainer">
    <el-container v-if="!inediting || inediting == false">
      <el-aside width="200px">
        <project-aside/>
      </el-aside>
      <el-main>
        <div>
          <el-row>
            <el-col :span="24">
              <h1 class="label">
                {{project_name}}
                <span>{{team_name}}</span>
              </h1>
              <p style="text-align: left; margin-left: 2.5%;">
                {{team_intro}}
              </p>
            </el-col>
          </el-row>
          <el-card shadow="never">
            <el-row>
              <el-col :span="24">
                <h1 style="text-align: left; margin-left: 2.5% ;margin-bottom: 1.5%">近期原型图</h1>
              </el-col>
            </el-row>
            <el-row v-if="PrototypeList.length != 0">
              <el-col :span="6" v-for="(id, index) in PrototypeList" :key="id" :offset="index > 0 ? 2 : 0">
                <drawio-digram v-on:startEdit="enterEdit(index)" v-on:deled = "get_prototype_list" :graph_id = "id" :isdel = "viewingDel" @deled = "updateOnDel"/>
              </el-col>
            </el-row>
            <el-row v-else>
              <el-empty :image-size="100"></el-empty>
            </el-row>
          </el-card>
          <el-card shadow="never">
            <el-row>
              <el-col :span="24">
                <h1 style="text-align: left; margin-left: 2.5% ;margin-bottom: 1.5%">近期UML</h1>
              </el-col>
            </el-row>
            <el-row v-if="UMLList.length != 0">
              <el-col :span="6" v-for="(id, index) in UMLList" :key="id" :offset="index > 0 ? 2 : 0">
                <drawio-digram v-on:startEdit="enterEdit(index)" v-on:deled = "get_uml_list" :graph_id = "id" :isdel = "viewingDel"/>
              </el-col>
            </el-row>
            <el-row v-else>
              <el-empty :image-size="100"></el-empty>
            </el-row>
          </el-card>
        </div>
      </el-main>
    </el-container>
  </div>

</template>

<script>
import qs from "qs";
import drawioDigram from "@/components/drawioDiagram";
import EtherpadFile from "@/components/etherpadFile";
import ProjectAside from "../../components/ProjectAside";
export default {
  components: {drawioDigram,EtherpadFile,ProjectAside},
  name: "project_outline",
  mounted() {
    window.exitEdit = this.exitEdit;
    window.stopLoading = function (){};
    window.startLoading = function (){};
    sessionStorage.setItem("project_id","1");
    this.refresh();
  },
  methods:{
    enterEdit(index) {
      this.inediting = true;
      this.nowediting = index;
    },exitEdit() {
      this.nowediting = null;
      this.inediting = false;
    },
    refresh(){
      this.get_uml_list();
      this.get_prototype_list();
      },
    get_prototype_list(){
      this.$data.PrototypeList = []
      this.$axios({
        method: "post" ,
        url: "app/get_graph_list" ,
        data: qs.stringify({
          project_id:this.$data.project_id,
          type:1,
          isdeleted:"0"
        }),
      }).then(res => {
        let graph_list = res.data.data.graph_list

        this.$data.PrototypeList = [];
        let i;
        for(i in graph_list){
          //
          this.$data.PrototypeList.push(graph_list[i].graph_id);
        }
        this.$data.PrototypeList = this.$data.PrototypeList.slice(0,3);
      })
    },
    get_uml_list(){
      this.$data.UMLList = [];
      this.$axios({
        method: "post" ,
        url: "app/get_graph_list" ,
        data: qs.stringify({
          project_id:this.$data.project_id,
          type:0,
          isdeleted:"0"
        }),
      }).then(res => {
        let graph_list = res.data.data.graph_list

        this.$data.UMLList = [];
        let i;
        for(i in graph_list){
          //
          this.$data.UMLList.push(graph_list[i].graph_id);
        }
        this.$data.UMLList =this.$data.UMLList.slice(0,3);
      })
    },
    get_doc_list(){
      this.$axios({
        method: "post" ,
        url: "app/get_doc_list" ,
        data: qs.stringify({
          project_id:this.$data.project_id,
        }),
      }).then(res=>{
        
        let resData = res.data.data;
        this.$data.doc_list = resData.doc_normal_list.slice(0,3);
        
      })
    },
  },
  data() {
    return {
      inediting:false,
      project_id : JSON.parse(sessionStorage.getItem("project")).project_id,
      team_name: JSON.parse(sessionStorage.getItem("team")).team_name,
      team_intro: sessionStorage.getItem(""),
      project_name: JSON.parse(sessionStorage.getItem("project")).project_name,
      UMLList:[],
      PrototypeList:[],
      doc_list:[],
    }
  }
}
</script>

<style scoped>

.el-card{
  margin-left: 2.5%;
  margin-right: 2.5%;
  margin-top: 1%;
}
.el-empty {
  padding: 0;
}
.label {
  margin: 30px 0px 0px 50px;
  font-size: 50px;
  /* float: left; */
  /* width: fit-content; */
  color: rgb(114, 132, 145); 
  text-align: left;
}
.label span {
  font-size: 20px;
  color: rgba(114, 132, 145, 0.631);
}
</style>