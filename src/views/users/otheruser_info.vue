<template>
  <el-container>
    <!-- <el-header style="height: 80px; z-index: 1"> -->
      <topFrame/>
    <!-- </el-header> -->
    <el-main style="overflow: scroll">
      <el-row style="margin-top: 2.5%; margin-bottom: 5.5%">
        <el-col span="10">
          <img
            v-if="!imageUrl"
            class="pic"
            src="../../assets/member_270x210.jpg"
            alt=""
          />
          <img v-else class="pic" :src="imageUrl" alt="" />
        </el-col>

        <el-col span="11" style="margin-top: -0.5%">
          <el-descriptions
            :column="3"
            size="medium"
            border
            direction="vertical"
            title="个人信息"
          >
            <template slot="extra">
              <el-button
                id="edit"
                type="primary"
                size="small"
                @click="edit()"
                v-if="notedit"
                >编辑</el-button
              >
              <el-button
                v-else
                id="save"
                type="primary"
                size="small"
                @click="save()"
                >保存</el-button
              >
            </template>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-user-solid"></i>
                用户名
              </template>
              <div class="user_name">{{ username }}</div>
              <!-- <div class="new_user_name"  v-else>
                  <el-input
                      v-model="new_user_name"
                      placeholder="请输入新用户名"
                      id="new_user_name"
                      size="small"
                  ></el-input>
                </div> -->
            </el-descriptions-item>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-s-custom"></i>
                真实姓名
              </template>
              <div class="real_name" v-if="notedit">{{ realname }}</div>
              <div class="new_real_name" v-else>
                <el-input
                  v-model="new_real_name"
                  placeholder="请输入新真实姓名"
                  id="new_real_name"
                  size="small"
                ></el-input>
              </div>
            </el-descriptions-item>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-message"></i>
                联系邮箱
              </template>
              <div class="email">{{ email }}</div>
              <!-- <div class="new_email" v-else>
                  <el-input
                      v-model="new_email"
                      placeholder="请输入新邮箱"
                      id="new_email"
                      size="small"
                  ></el-input>
                </div> -->
            </el-descriptions-item>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-star-on"></i>
                个性签名
              </template>
              <div class="word" v-if="notedit">{{ word }}</div>
              <div class="new_email" v-else>
                <el-input
                  v-model="new_word"
                  placeholder="请输入新个性签名"
                  id="new_email"
                  size="small"
                ></el-input>
              </div>
            </el-descriptions-item>
          </el-descriptions>
        </el-col>
      </el-row>
      <el-divider></el-divider>
      <el-row style="margin: 0.5%">
        <el-tabs
          v-model="activeName"
          tab-position="left"
          type="card"
          @tab-click="handleClick"
        >
          <el-tab-pane label="个人所在团队" name="first">
            <el-card class="box-card" v-for="item in teamlist">
              <div class="text item name">
                <span class="og">团队名称：</span>
                {{ item.teamname }}
              </div>
              <div class="text item type">
                <span class="og">团队类型：</span>
                {{ item.teamtype }}
              </div>
              <div class="text item setter">
                <span class="og">团队创始人：</span>
                {{ item.teamsetter }}
              </div>
              <div class="text item settime">
                <span class="og">创建时间：</span>
                {{ item.teamsettime }}
              </div>
              <div class="text item peoplenum">
                <span class="og">现有人数：</span>
                {{ item.teamernum }}
              </div>
            </el-card>
          </el-tab-pane>
          <el-tab-pane label="个人所在项目" name="second">
            <el-card class="box-card" v-for="item in projectlist">
              <div class="text item name">
                <span class="og">项目名称：</span>
                {{ item.project_name }}
              </div>
              <div class="text item type">
                <span class="og">项目编号：</span>
                {{ item.project_id }}
              </div>
              <div class="text item setter">
                <span class="og">创建时间：</span>
                {{ item.create_time }}
              </div>
              <div class="text item settime">
                <span class="og">更新时间：</span>
                {{ item.update_time }}
              </div>
              <div class="text item peoplenum">
                <span class="og">所属团队编号：</span>
                {{ item.team_id }}
              </div>
            </el-card>
          </el-tab-pane>
          <el-tab-pane label="个人回收项目" name="third">
            <el-card class="box-card" v-for="item in reprojectlist">
              <!-- <div class="text tag re">
                <span class="tag1">已回收项目！！！</span>
              </div> -->
              <div class="text item name">
                <span class="og">项目名称：</span>
                {{ item.project_name }}
              </div>
              <div class="text item type">
                <span class="og">项目编号：</span>
                {{ item.project_id }}
              </div>
              <div class="text item setter">
                <span class="og">创建时间：</span>
                {{ item.create_time }}
              </div>
              <div class="text item settime">
                <span class="og">更新时间：</span>
                {{ item.update_time }}
              </div>
              <div class="text item peoplenum">
                <span class="og">所属团队编号：</span>
                {{ item.team_id }}
              </div>
            </el-card>
          </el-tab-pane>
          <!-- <el-tab-pane label="已经完成项目" name="third">角色管理</el-tab-pane> -->
        </el-tabs>
      </el-row>
    </el-main>
  </el-container>
</template>

<script>
import qs from "qs";
import topFrame from "../../components/topFrame.vue";
export default {
  inject: ["reload"],
  data() {
    return {
      imageurl: "app/",
      activeName: "first",
      username: "",
      realname: "",
      email: "",
      word: "",
      new_username: "",
      new_real_name: "",
      new_email: "",
      new_word: "",
      notedit: true,
      teamlist: [],
      projectlist: [],
      reprojectlist: [
        {
          project_id: "1",
          project_name: "1",
          create_time: "1",
          update_time: "1",
          team_id: "1",
        },
      ],
    };
  },
  components: {
    topFrame,
  },
  methods: {
    // gototeam(){
    //   this.$message({
    //             message: "正在跳转团队详细页面",
    //             center: true,
    //             type: "success",
    //             duration:1500
    //           });
    //   setTimeout(() => {
    //               this.$router.push({ path:'team_outline' });
    //           }, 1000);
    // },
    edit() {
      this.notedit = false;
    },
    save() {
      this.notedit = true;
      this.$axios({
        method: "post" /* 指明请求方式，可以是 get 或 post */,
        url: "app/update_user_info" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
        // headers:{
        //   'authorization':JSON.parse(sessionStorage.getItem("token")).token_num
        // },
        data: qs.stringify({
          /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
          real_name: this.new_real_name,
          user_info: this.new_word,
        }),
      })
        .then((res) => {
          
          // if(res.data.errno==0)
          // {
          //     
          //     this.username = res.data.data.user_name;
          //     this.realname = res.data.data.real_name;
          //     this.email = res.data.data.email;
          //     this.word = res.data.data.user_info;
          // }
          // else{
          if (res.data.errno == 0) {
            this.$message({
              message: res.data.msg,
              center: true,
              type: "success",
            });
          } else {
            this.$message({
              message: res.data.msg,
              center: true,
              type: "warning",
            });
          }
        })
        // })
        .catch((err) => {
          
        });
      this.reload();
    },
    init() {
      
      this.$axios({
        method: "get" /* 指明请求方式，可以是 get 或 post */,
        url: "app/get_logined_userinfo" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
        // headers:{
        //   'authorization':JSON.parse(sessionStorage.getItem("token")).token_num
        // }
      })
        .then((res) => {
          
          if (res.data.errno == 0) {
            
            this.username = res.data.data.user_name;
            this.realname = res.data.data.real_name;
            this.email = res.data.data.email;
            this.word = res.data.data.user_info;
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
    getTeam() {
      this.$axios({
        method: "get" /* 指明请求方式，可以是 get 或 post */,
        url: "app/get_team_list" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
      })
        .then((res) => {
          
          if (res.data.errno == 0) {
            
            
            
            res.data.data.team_list_owner.forEach((item) => {
              var tmp = {
                teamname: "",
                teamtype: "",
                teamsetter: "",
                teamsettime: "",
                teamernum: "",
              };
              tmp.teamname = item.team_name;
              tmp.teamtype = item.team_type;
              tmp.teamsetter = item.team_owner_user_name;
              tmp.teamsettime = item.team_time;
              tmp.teamernum = item.team_member_num;
              
              this.teamlist.push(tmp);
              
            });
            res.data.data.team_list_admin.forEach((item) => {
              var tmp = {
                teamname: "",
                teamtype: "",
                teamsetter: "",
                teamsettime: "",
                teamernum: "",
              };
              tmp.teamname = item.team_name;
              tmp.teamtype = item.team_type;
              tmp.teamsetter = item.team_owner_user_name;
              tmp.teamsettime = item.team_time;
              tmp.teamernum = item.team_member_num;
              
              this.teamlist.push(tmp);
              
            });
            res.data.data.team_list_member.forEach((item) => {
              var tmp = {
                teamname: "",
                teamtype: "",
                teamsetter: "",
                teamsettime: "",
                teamernum: "",
              };
              tmp.teamname = item.team_name;
              tmp.teamtype = item.team_type;
              tmp.teamsetter = item.team_owner_user_name;
              tmp.teamsettime = item.team_time;
              tmp.teamernum = item.team_member_num;
              
              this.teamlist.push(tmp);
              
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
    },

    getProject() {
      
      this.$axios({
        method: "get" /* 指明请求方式，可以是 get 或 post */,
        url: "app/get_user_projects" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
      })
        .then((res) => {
          
          if (res.data.errno == 0) {
            
            
            res.data.data.project_normal_list.forEach((item) => {
              
              var tmp = {
                project_id: "",
                project_name: "",
                create_time: "",
                update_time: "",
                team_id: "",
              };
              tmp.project_id = item.project_id;
              tmp.project_name = item.project_name;
              tmp.create_time = item.create_time;
              tmp.update_time = item.update_time;
              tmp.team_id = item.team_id;
              
              this.projectlist.push(tmp);
              
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
    },
    getreProject() {
      
      this.$axios({
        method: "get" /* 指明请求方式，可以是 get 或 post */,
        url: "app/get_user_projects" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
      })
        .then((res) => {
          
          if (res.data.errno == 0) {
            
            
            res.data.data.project_recycle_list.forEach((item) => {
              
              var tmp = {
                project_id: "",
                project_name: "",
                create_time: "",
                update_time: "",
                team_id: "",
              };
              tmp.project_id = item.project_id;
              tmp.project_name = item.project_name;
              tmp.create_time = item.create_time;
              tmp.update_time = item.update_time;
              tmp.team_id = item.team_id;
              
              this.reprojectlist.push(tmp);
              
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
    },
  },
  mounted() {
    this.init();
    this.getTeam();
    this.getProject();
    this.getreProject();
  },
};
</script>

<style scoped>
/* .el-tabs--left,
.el-tabs--right {
  overflow: hidden;
  margin-left: 20%;
} */
.el-tabs--left,
.el-tabs--right {
      overflow: hidden;
    margin-left: 5%;
    margin-top: 7%;
}
.el-main {
  /* overflow:visible;  */
  /* padding-top: 0;  */
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  padding-top: 62px;
  overflow-y: hidden;
  background-color: rgba(234, 239, 242, 0.425);
}

.el-header {
  /* background-color: white; */
  backdrop-filter: blur(10px) brightness(110%);

  text-align: left;
  color: black;
  /* box-shadow: 1px 1px 10px rgb(240, 242, 245); */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04);
}

.pic {
  height: 200px;
  width: 200px;
  border-radius: 50%;
}
.el-divider--horizontal {
  display: block;
  height: 1px;
  width: 70%;
  margin: -58px 11px;
  padding-left: 27%;
  overflow: hidden;
}
.el-tabs--left >>> .el-tabs__item.is-left {
  text-align: right;
  height: 80px;
  line-height: 80px;
}
.el-tabs--left[data-v-50bb520e],
.el-tabs--right[data-v-50bb520e] {
  overflow: hidden;
  margin-left: 5%;
  margin-top: 7%;
}
.el-tabs__item {
  padding: 0 20px;
  height: 75px;
  box-sizing: border-box;
  line-height: 68px;
  display: inline-block;
  list-style: none;
  font-size: 14px;
  font-weight: 500;
  color: #303133;
  position: relative;
}
.text {
  font-size: 20px;
}

.item {
  padding: 16px 0;
}

.box-card {
  width: 490px;
  height: 350px;
  float: left;
  margin-left: 50px;
  border-radius: 15px;
  text-align: left;
  margin-bottom: 20px;
  border-color: rgb(206, 218, 226) 2px !important;
  padding: 0px 30px 0px 30px;
  box-shadow: none;
}
.box-card .og {
  margin-top: 20px;
  font-weight: bold;
}

.el-card:hover {
  box-shadow: 0 5px 5px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04);
}
.tag1{
  font-weight: bold;
  font-size:30px;
  color:#BBBBBB
}
.re{
  padding: 0px 0;
  text-align:center;
}
.el-button {
  background-color: rgb(206, 218, 226);
  border-color: rgb(206, 218, 226);
  color: black;
  transition: 0.4s;
}
.el-button:hover {
  background-color: rgb(150, 169, 183) !important;
  border-color: rgb(150, 169, 183);
  color: rgb(255, 255, 255);
}
.goteam{
  text-decoration: none;
  color:black;
}
.goproject{
  text-decoration: none;
  color:black;
}
.goreproject{
  text-decoration: none;
  color:black;
}
</style>