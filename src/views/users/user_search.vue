<template>
  <el-container>
    <!-- <el-header style="height: 80px; z-index: 2"> -->
      <topFrame/>
    <!-- </el-header> -->
    <el-main style="overflow: scroll">
      <el-row class="info">
        <el-col :span="14">
          <span class="slo">您搜索"{{ content }}"的结果如下：</span>
        </el-col>
      </el-row>
      <el-row class="nei">
        <el-col :span="4"><img/></el-col>
        <el-col :span="16">
          <el-tabs v-model="activeName">
            <el-tab-pane label="用户" name="first">
              <div v-if="userlist.length!=0">
              <!-- <el-card class="box-card" v-for="item in userlist" :key="item">
                <div slot="header" class="clearfix">
                  <span style="float: left; margin-top:-11px;font-weight:bold;font-size:20px;">{{ item.user_name
                  }}</span>
                  <el-button style="float: right; margin-top:-19px" type="text" @click="invite(item.user_name)">发送邀请</el-button>
                </div>
                <el-row>
                  <el-col :span="8">
                    <div class="photo">
                      <img src="../../assets/bk3.jpg"/>
                    </div>
                  </el-col>
                  <el-col :span="16">
                    <div class="text item name">
                      <span class="og">用户编号：</span>
                      {{ item.user_id }}
                    </div>
                    <div class="text item setter">
                      <span class="og">用户邮箱：</span>
                      {{ item.email }}
                    </div>
                    <div class="text item settime">
                      <span class="og">个性签名：</span>
                      {{ item.user_info }}
                    </div>
                  </el-col>
                </el-row>
              </el-card> -->
                <div class="bar bar1">
                  <div class="left">
                    <p class="name" style="margin-left: 60px">用户名</p>
                  </div>
                  <div class="right">
                    <!-- <p class="email" style="width: 300px">邮箱</p> -->
                    <p class="long" style="width: 350px">个性签名</p>
                  </div>
                </div>
                <div class="bar bar2" v-for="item in userlist">
                  <div class="left">
                    <img v-if="!item.profile" src="../../assets/bk3.jpg" alt=""/>
                    <img v-else :src="item.profile" alt=""/>
                    <p class="name">{{item.user_name}}</p>
                  </div>
                  <div class="right">
                    <div id="tools">
                      <i class="el-icon-s-promotion" @click="invite(item.user_name)">邀请</i>
                    </div>
                    <!-- <p class="email">{{item.email}}</p> -->
                    <p class="long">{{item.user_info}}</p>
                  </div>
                </div>
              </div>
              <div v-else style="margin-top:10%">
                <img src="../../assets/no.png" style="width:100px;height:100px;"/>
                <div class="txt">抱歉，没有搜到符合条件的用户，请尝试一下别的条件</div>
              </div>
            </el-tab-pane>
            <el-tab-pane label="团队" name="second">
              <div v-if="teamlist.length!=0">
              <!-- <el-card class="box-card1" v-for="item in teamlist" :key="item">
                  <div slot="header" class="clearfix">
                    <span style="float: left; margin-top:-11px;font-weight:bold;font-size:20px;">{{ item.team_name
                    }}</span>
                    <el-button style="float: right; margin-top:-19px" type="text"></el-button>
                  </div>
                  <div class="text item name">
                    <span class="og">团队编号：</span>
                    {{ item.team_id }}
                  </div>
                  <div class="text item setter">
                    <span class="og">团队类型：</span>
                    {{ item.team_type }}
                  </div>
                  <div class="text item setter">
                    <span class="og">团队人数：</span>
                    {{ item.team_member_num }}
                  </div>
                  <div class="text item setter">
                    <span class="og">队长姓名：</span>
                    {{ item.team_owner_user_name }}
                  </div>
                  <div class="text item setter">
                    <span class="og">队长邮箱：</span>
                    {{ item.team_owner_user_email }}
                  </div>
                  <div class="text item settime">
                    <span class="og">团队简介：</span>
                    {{ item.team_owner_user_info }}
                  </div>
              </el-card> -->
              <div class="bar bar1">
                <div class="left">
                  <p class="id">团队名称</p>
                </div>
                <div class="right">
                  <p class="short">类型</p>
                  <p class="short">人数</p>
                  <p class="id">队长姓名</p>
                  <p class="email">队长邮箱</p>
                  <p class="email">团队简介</p>
                </div>
              </div>
              <div class="bar bar2" v-for="item in teamlist">
                <div class="left">
                  <p class="id">{{item.team_name}}</p>
                </div>
                <div class="right">
                  <!-- <div id="tools">
                    <i class="el-icon-minus" @click="cancelmanager()"></i>
                  </div> -->
                  <p class="short">{{item.team_type}}</p>
                  <p class="short">{{item.team_member_num}}</p>
                  <p class="id">{{item.team_owner_user_name}}</p>
                  <p class="email">{{item.team_owner_user_email}}</p>
                  <p class="email">{{item.team_owner_user_info}}</p>
                </div>
              </div>

              </div>
              <div v-else style="margin-top:10%">
                <img src="../../assets/no.png" style="width:100px;height:100px;"/>
                <div class="txt">抱歉，没有搜到符合条件的团队，请尝试一下别的条件</div>
              </div>
            </el-tab-pane>
          </el-tabs>
        </el-col>
        <el-col :span="4"><img/></el-col>
      </el-row>
    </el-main>
  </el-container>
</template>

<script>
import qs from "qs";
import topFrame from "../../components/topFrame.vue";
export default {
  inject: ["reload"],
  components: {
    topFrame
  },
  data() {
    return {
      user_name: "",
      activeName: "first",
      content: JSON.parse(sessionStorage.getItem("searched")).content,
      userlist: [],
      teamlist: [],
    };
  },
  methods: {
    invite(user_name){
      this.$axios({
        method: "post",
        url: "app/generate_invite_link",
        data: qs.stringify({
          team_id: JSON.parse(sessionStorage.getItem('team')).team_id,
          invite_method: 'email',
          invite_user_name: user_name,
          targeted: 'true'
        }),
      })
        .then((res) => {
          if(res.data.errno==0){
            this.$message.success("邀请信息已发送");
          }
          else{
            this.$message.warning(res.data.msg);
          }

          
        })
        .catch((err) => {
          
        });
    },
    init() {
      this.$axios({
        method: "post" /* 指明请求方式，可以是 get 或 post */,
        url: "app/search_user_by_username" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
        // headers:{
        //   'authorization':JSON.parse(sessionStorage.getItem("token")).token_num
        // }
        data: qs.stringify({
          /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
          user_name: this.content,
          query_type: "fuzzy"
        }),
      })
        .then((res) => {
          
          if (res.data.errno == 0) {
            
            res.data.data.user_list.forEach((item) => {
              var tmp = {
                user_id: "",
                user_name: "",
                email: "",
                user_info: "",
                profile:"",
              };
              tmp.user_id = item.user_id;
              tmp.user_name = item.user_name;
              tmp.email = item.email;
              tmp.user_info = item.user_info;
              tmp.profile = item.profile;
              this.userlist.push(tmp);
              console.log(this.userlist)
            });
          } else {
            this.$message({
              message: res.data.msg,
              center: true,
              type: "error",
            });
          }
        })
        .catch((err) => {
          
        });

      this.$axios({
        method: "post" /* 指明请求方式，可以是 get 或 post */,
        url: "app/search_team_by_teamname" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
        // headers:{
        //   'authorization':JSON.parse(sessionStorage.getItem("token")).token_num
        // }
        data: qs.stringify({
          /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
          team_name: this.content,
          query_type: "fuzzy"
        }),
      })
        .then((res) => {
          
          if (res.data.errno == 0) {
            
            res.data.data.team_fuzzy_list.forEach((item) => {
              var tmp = {
                team_name: "",
                team_id: "",
                team_type: "",
                team_member_num: "",
                team_owner_user_name: "",
                team_owner_user_email: "",
                team_info:"",
              };
              tmp.team_id = item.team_id;
              tmp.team_name = item.team_name;
              tmp.team_type = item.team_type;
              tmp.team_member_num = item.team_member_num;
              tmp.team_owner_user_name = item.team_owner_user_name;
              tmp.team_owner_user_email = item.team_owner_user_email;
              tmp.team_info=item.team_info;
              this.teamlist.push(tmp);
              
            });
            console.log(this.teamlist)
          } else {
            this.$message({
              message: res.data.msg,
              center: true,
              type: "error",
            });
          }
        })
        .catch((err) => {
          
        });
    },
    lookinfo(val) {
      // this.$router.push({path:'/home',query: {user_id:val}})     
    }
  },
  mounted() {
    
    this.init();
  },
};
</script>

<style scoped>
.el-main {
  /* overflow:visible;  */
  /* padding-top: 0;  */
  position: absolute;
  left: 0;
  right: 0;
  top: 62px;
  bottom: 0;
  overflow-y: hidden;
}

.el-header {
  background-color: white;
  text-align: left;
  color: black;
  /* box-shadow: 1px 1px 10px rgb(240, 242, 245); */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04);
}

.slo {
  font-weight: bold;
  font-size: 25px;
  color: #444444;
  margin-left: 0px;
}

.nei {
  margin-top: 2%;
}

.text {
  font-size: 15px;
}

.item {
  padding: 10px 0;
}

.box-card {
  width: 45%;
  height: 220px;
  float: left;
  /* margin-left: 10px; */
  margin-right: 30px;
  margin-left: 5px;
  border-radius: 15px;
  text-align: left;
  margin-bottom: 20px;
}

.box-card1 {
  width: 45%;
  height: 350px;
  float: left;
  /* margin-left: 10px; */
  margin-right: 30px;
  margin-left: 5px;
  border-radius: 15px;
  text-align: left;
  margin-bottom: 20px;
}

.box-card .og {
  margin-top: 20px;
  font-weight: bold;
}

.el-card {
  border-radius: 20px;
  border-color: #c0c4cc;
}

.el-card:hover {
  border-color: #3f77e7;
}

.settime {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  overflow: hidden;
}

.photo {
  width: 100%;
  height: 20vh;
  margin-top: -4px;
}

.photo img {
  width: 100px;
  height: 100px;
  border-radius: 50%;
}

/* 这里的样式应该只能用/deep/改 */
/deep/ .el-tabs__item.is-active {
  color: rgb(2, 2, 2);
}

/deep/ .el-tabs__item:hover {
  color: #75889c;
}

/deep/ .el-tabs__item {
  color: #909399;
  font-size: 14px;
  font-weight: 500;
}

/deep/ .el-tabs__active-bar {
  height: 4px;
  border-radius: 2px;
  background: #75889c;
}

.bar {
  right: 0;
  border:  1px solid rgba(206, 218, 226, 0.536);
  border-radius: 45px;
  margin: 20px;
  /* padding: 10px; */
  /* 居中 */
  align-items: center;
  display: flex;
  transition: 0.4s;
  color: black;
}
.bar:hover {
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 10px rgba(0, 0, 0, 0.04);
}
.bar1 {
  background-color: rgba(95, 123, 143, 0.184);
  border:  1px solid rgba(85, 103, 116, 0.517);
}
.bar2 {
  background-color: rgba(206, 218, 226, 0.29);
}
.bar3 {
  background-color: rgba(95, 123, 143, 0.184);
  border:  1px solid rgb(85, 103, 116);
}
.left {
  /* height: max-content; */
  /* float: left; */
  align-items: center;
  display: flex;
}
.right {
  height: 40px;
  /* float: right; */
  align-items: center;
  /* display: flex; */
  position: absolute;
  right: 3%;
  overflow: hidden;
}
img { 
  width: 40px;
  height: 40px;
  margin: 8px;
  border-radius: 50%;
}
p {
  display: inline;
  margin: 10px;
  vertical-align: center;
}
.name, .id, .realname, .email, .long, .short{
  display: inline-block;
  white-space: nowrap; 
  /* width: 80px;  */
  overflow: hidden;
  text-overflow:ellipsis;
}
.name {
  width: 170px;
  text-align: left;
  padding-left: 5px;
}
.id {
  width: 120px;
}
.realname {
  width: 200px;
}
.email {
  width: 200px;
}
.short {
  width: 50px;
}
.long {
  width: 400px;
  text-align: left;
}

#tools {
  background-color: rgba(95, 123, 143, 0.304);
  /* color: rgb(95, 123, 143) !important; */
  transition: 0.3s;
  height: 0px;
  padding: 0px;
  /* font-size: 0px; */
  border-radius: 500px;
  text-align: center;
  color: rgb(255, 255, 255);
}
.bar3 #tools {
  background-color: rgba(253, 253, 253, 0.304);
}
.bar:hover #tools{
  height: 40px;
  /* color: rgb(0, 0, 0); */
  /* font-size: 20px; */
  margin-bottom: 10px;
}
.el-icon-s-promotion {
  color: rgb(70, 87, 122);
  border-radius: 5px;
  margin-top: 5px;
  transition: 0.2s;
  width: 100%;

  filter: blur(20px);
  opacity: 0%;
}
.el-icon-s-promotion:hover {
  /* color: rgb(0, 0, 0); */
  /* background-color: rgb(255, 255, 255); */
  /* margin: 5px; */
  height: 100%;
  cursor: pointer;
}
.el-icon-s-promotion:hover #tools{
  color: rgb(0, 0, 0);
  background-color: rgb(217, 221, 231) ;
  padding: 5px;
}

.bar:hover .el-icon-s-promotion{
  /* background-color: rgb(199, 113, 113); */
  padding: 5px;
  filter: blur(0px);
  opacity: 100%;
}
</style>