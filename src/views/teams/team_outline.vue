<template>
    <el-container>
     
     <div class="main" v-if="teamname">
        <h1 class="label">团队信息</h1>
        <div class="content">

              <el-card class="box-card">
                <div  class="text item name">
                  <span class="og">团队名称：</span>
                  {{teamname}}
                   <i class="el-icon-edit" style="font-size:20px" @click="dialogVisible0 = true" title="重命名团队名称"></i>
                </div>
                 <el-dialog :modal="false"
                  title="修改团队名"
                  :visible.sync="dialogVisible0"
                  width="30%"
                  :before-close="handleClose"
                  v-if="dialogVisible0">
                  <el-input
                    maxlength="20"
                    show-word-limit
                    v-model="teamname">
                  </el-input>
                  <span slot="footer" class="dialog-footer">
                    <el-button @click="dialogVisible0 = false">取 消</el-button>
                    <el-button type="primary" @click="changename()">确 定</el-button>
                  </span>
                </el-dialog>

                <div  class="text item type">
                  <span class="og">团队类型：</span>
                  {{teamtype}}
                </div>
                 <div  class="text item setter">
                  <span class="og">团队创始人：</span>
                  {{teamsetter}}
                </div>
                 <div  class="text item settime">
                  <span class="og">创建时间：</span>
                  {{teamsettime}}
                </div>
                 <div  class="text item peoplenum">
                  <span class="og">现有人数：</span>
                  {{teamernum}}
                </div>
                
              </el-card>

              <el-card class="box-card2">
                <div  class="text item introduce">
                  <span class="og">团队简介：<i class="el-icon-edit" style="font-size:20px" @click="dialogVisible = true" title="修改简介"></i></span>
                  <p style="margin-top: 10px;word-wrap: break-word;font-weight: 400;">{{teamintro}}</p>
                </div>
                <el-dialog :modal="false"
                  title="修改简介"
                  :visible.sync="dialogVisible"
                  width="30%"
                  :before-close="handleClose" v-if="dialogVisible">
                  <el-input
                    type="textarea"
                    :rows="6"
                    maxlength="100"
                    show-word-limit
                    v-model="teamintro">
                  </el-input>
                  <span slot="footer" class="dialog-footer">
                    <el-button @click="dialogVisible = false">取 消</el-button>
                    <el-button type="primary" @click="changeintro()">确 定</el-button>
                  </span>
                </el-dialog>
              </el-card>
        </div>
     </div>
        <div class="chooseteam" v-else>
            <el-empty description="你还有没选择你的团队，快去选择一个吧" :image-size="200"></el-empty>
        </div>


</el-container>
</template>
<script>
import qs from 'qs';
export default {
    data(){
      return{
        teamname:JSON.parse(sessionStorage.getItem('team')).team_name,
        teamid:JSON.parse(sessionStorage.getItem('team')).team_id,
        dialogVisible0:false,
        dialogVisible:false,
        teamtype:'',
        teamsetter:'',
        teamsettime:'',
        teamernum:'',
        teamintro:'',
        allteams:[]
      }
    },
    methods:{
      changeintro(){
       this.dialogVisible = false;

       this.$axios({
        method: "post",
        url: "app/update_team_info",
        data: qs.stringify({
          team_id: this.teamid,
          team_info: this.teamintro,
        }),
      })
        .then((res) => {
        
        if(res.data.errno==0){
         this.$message({
            type: 'success',
            message: '修改成功'
          });
        }
       else{
        if(res.data.errno==2004)
        this.$message.warning(res.data.msg+'，不具备修改权限');
        else
        this.$message.warning(res.data.msg);
       }
          location.reload();
          })
        .catch((err) => {
          
        });

      },
      changename(){
        this.dialogVisible0 = false;
          this.$axios({
        method: "post",
        url: "app/rename_team",
        data: qs.stringify({
          team_id: this.teamid,
          team_name: this.teamname,
        }),
      })
        .then((res) => {
        
        if(res.data.errno==0){
           var content = {team_id: this.teamid,team_name:this.teamname}
        this.$store.dispatch("saveteam", content);
         this.$message({
            type: 'success',
            message: '修改成功'
          });
        }
       else{
        this.$message.warning(res.data.msg+'，不具备修改权限');
       }
          location.reload();
          })
        .catch((err) => {
          
        });
      },
      init(){
        this.$axios({
        method: "get",
        url: "app/get_team_list",
        data: qs.stringify({
        }),
      })
        .then((res) => {
          
          if(res.data.data.team_list_owner.length>0)
          for( var i in res.data.data.team_list_owner){
            this.allteams.push(res.data.data.team_list_owner[i]);
          }
          if(res.data.data.team_list_admin.length>0)
          for( var i in res.data.data.team_list_admin){
            this.allteams.push(res.data.data.team_list_admin[i]);
          }
          if(res.data.data.team_list_member.length>0)
          for( var i in res.data.data.team_list_member){
            this.allteams.push(res.data.data.team_list_member[i]);
          }
          
          for (var i = 0; i < this.allteams.length; i++) { 
                 if(this.allteams[i].team_id == this.teamid){
                    this.teamtype = this.allteams[i].team_type;
                    this.teamsetter = this.allteams[i].team_owner_user_name;
                    this.teamsettime = this.allteams[i].team_time;
                    this.teamernum = this.allteams[i].team_member_num;
                    this.teamintro = this.allteams[i].team_info;
                    break;
                 }
              }
          })
        .catch((err) => {
          
        });
      }
    },
    mounted(){
      this.init();
    }
}
</script>
<style scoped>
body {
  padding-right:0 !important ;
}
.main{
  width: 100%;
  text-align: left;
}
.content .el-icon-edit{
  float: right;
}
.teamname{
  height: 150px;
  width: 100%;
  background: linear-gradient(270.6deg, #e3f6fd -8.4%, #f6faff 100%);
  font-size: 36px;
  color: black;
  text-align: left;
  line-height: 60px;
  border-radius: 10px;
}
.teamname h1{
  margin-left: 50px;
  margin-top: 20px;
}
.chooseteam{
  position: absolute;
  left: 0;
  right: 0;
}
.el-icon-edit:hover{
  cursor: pointer;
}

.content{
  margin-top: 100px;
}
  .text {
  font-size: 20px;
}

.item {
  padding: 18px 0;
}

.box-card {
  width: 550px;
  height: 350px;
  float: left;
  margin-left: 50px;
  border-radius: 15px;
  text-align: left;
  
}
.box-card .og{
  font-weight: bold;
}
  .box-card2 {
  width: 550px;
  height: 350px;
  float: right;
  margin-right: 50px;
  border-radius: 15px;
  text-align: left;
  font-weight: bold;
}
.box-card2 .og{
  font-weight: bold;
}
.el-icon-edit{
  height: 30px;
  width:38px;
  border-radius: 10px;
  
  /* background: linear-gradient(270.6deg, #cbcddb06 -8.4%, rgba(150, 169, 183, 0.422) 100%); */
  background-color: rgb(206, 218, 226);
  
  color: black;
  text-align: center;
  
  overflow: hidden;
  transition: 0.2s;
  padding-top: 8px;
  /* position: absolute; */
}
.el-icon-edit:hover {
  border-radius: 50%;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 10px rgba(0, 0, 0, 0.04);
}
.label {
  margin: 30px 0px 50px 30px;
  font-size: 50px;
  float: left;
  width: 100%;
  color: rgb(114, 132, 145); 
}
</style>