<template>
  <el-container>
    <topFrame :team="false" :search="false" />
    <el-main style="overflow: scroll">
      <el-row style="margin-top: 3.0%; margin-bottom: 5.5%">
        <el-col :span="10">
          <el-page-header @back="goBack" content="个人信息">
          </el-page-header>
          <img v-if="!profile" class="pic" src="../../assets/bk3.jpg" alt="" />
          <img v-else class="pic" :src="profile" alt="" />
          <el-upload ref="upload" class="avatar-uploader" accept="JPG, .PNG, .JPEG,.jpg, .png, .jpeg" :headers="headers"
            action="" :multiple="false" :show-file-list="false" :http-request="uploadImg"
            :before-upload="beforeAvatarUpload" :on-change="handleChange">
            <el-button class="left_b" size="mini" type="text" round>修改头像</el-button>
            <div slot="tip" class="el-upload__tip">
              只能上传jpg/png文件,且不超过1MB
            </div>
          </el-upload>
        </el-col>
        <el-col :span="11" style="margin-top: 1.5%">
          <el-descriptions :column="3" size="medium" border direction="vertical" title="个人信息">
            <template slot="extra">
              <el-button id="edit" type="primary" size="small" @click="edit()">修改密码</el-button>
            </template>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-user-solid"></i>
                用户名
              </template>
              <div class="user_name" v-if="noteditUserName"
                style="display: flex;align-items: center;justify-content: space-between;">
                {{ username }}
                <div class="CUN" style="font-size:15px"><i class="el-icon-edit" @click="changeUserName()"></i></div>
              </div>
              <div class="user_name" v-else>
                <el-input v-model="new_username" placeholder="请输入新用户名" id="new_user_name" size="mini">
                  <div class="SUN" slot="suffix" style="font-size:15px"><i class="el-icon-check  el-input__icon"
                      @click="saveUserName()"></i></div>
                </el-input>
              </div>
            </el-descriptions-item>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-s-custom"></i>
                真实姓名
              </template>
              <div class="real_name" v-if="noteditRealName"
                style="display: flex;align-items: center;justify-content: space-between;">
                {{ realname }}
                <div class="CRN" style="font-size:15px"><i class="el-icon-edit" @click="changeRealName()"></i></div>
              </div>
              <div class="new_real_name" v-else>
                <el-input v-model="new_real_name" placeholder="请输入新真实姓名" id="new_real_name" size="mini">
                  <div class="SRN" slot="suffix" style="font-size:15px"><i class="el-icon-check  el-input__icon"
                      @click="saveRealName()"></i></div>
                </el-input>
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
              <div class="word" v-if="noteditWord"
                style="display: flex;align-items: center;justify-content: space-between;">
                {{ word }}
                <div class="CW" style="font-size:15px;"><i class="el-icon-edit" @click="changeWord()"></i></div>
              </div>
              <div class="new_email" v-else>
                <el-input v-model="new_word" placeholder="请输入新个性签名" id="new_email" size="mini" maxlength="100">
                  <div slot="suffix" class="SW" style="font-size:15px"><i class="el-icon-check  el-input__icon"
                      @click="saveWord()"></i></div>
                </el-input>
              </div>
            </el-descriptions-item>
          </el-descriptions>
        </el-col>
        <el-dialog v-if="PasswordChangeDialogVi"
        :modal="false" title="更改您的密码" :visible.sync="PasswordChangeDialogVi" width="30%" :close-on-click-modal="false"
          :close-on-press-escape="false" center>
          <el-form ref="password" :model="password" class="password" :hide-required-asterisk="true" :rules="rules">
            <el-form-item prop="originPassWord" label="请输入旧密码：">
              <el-input prefix-icon="el-icon-lock" placeholder="在此输入旧密码" show-password type="password" clearable
                v-model="password.originPassWord" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item prop="passwordChange" label="请输入新密码：">
              <el-input prefix-icon="el-icon-lock" placeholder="在此输入新密码" show-password type="password" clearable
                v-model="password.passwordChange" autocomplete="off">
                <el-dropdown slot="suffix" size="mini" placement="top-start">
                  <i class="el-icon-warning-outline el-input__icon">
                  </i>
                  <el-dropdown-menu slot="dropdown">
                    <el-dropdown-item disabled>密码仅能由数字、26个英文字母或者下划线组成，长度为8-16位</el-dropdown-item>
                  </el-dropdown-menu>
                </el-dropdown>
              </el-input>
            </el-form-item>
            <el-form-item prop="checkpasswordChange" label="请再输入一遍新密码：">
              <el-input prefix-icon="el-icon-lock" placeholder="再此输入新密码" show-password type="password" clearable
                v-model="password.checkpasswordChange" autocomplete="off"></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="(PasswordChangeDialogVi = false), resetForm('password')">取消更改</el-button>
            <el-button type="primary" @click="changePass('password')">确认更改</el-button>
          </span>
        </el-dialog>

      </el-row>
      <el-divider></el-divider>
      <el-row style="margin: 1% 0.5% 0.5% 0.5%;">
        <el-tabs v-model="activeName" tab-position="left" @tab-click="handleClick">
          <el-tab-pane label="个人所在团队" name="first">
            <!-- <el-card class="box-card" v-for="item in teamlist">
              <div class="text item name">
                <a href="/team_outline" class="goteam"><span class="og">团队名称：</span>
                  {{ item.teamname }}</a>
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
            </el-card> -->
            <div class="bar header">
              <div class="left">
                <!-- <img src="../assets/bk3.jpg" alt=""/> -->
                <a class="goteam">
                  团队名称</a>
              </div>
              <div class="right">
                <p class="default">团队类型</p>
                <p class="default">团队创始人</p>
                <p class="default long">创建时间</p>
                <p class="default">现有人数</p>
              </div>
            </div>
            <div class="bar" v-for="item in teamlist">
              <div class="left">
                <!-- <img src="../assets/bk3.jpg" alt=""/> -->
                <a href="#" class="goteam" @click="gotoTeam(item)">
                  {{ item.teamname }}</a>
              </div>
              <div class="right">
                <p class="default">{{ item.teamtype }}</p>
                <p class="default">{{ item.teamsetter }}</p>
                <p class="default long">{{ item.teamsettime }}</p>
                <p class="default">{{ item.teamernum }}</p>
              </div>
            </div>

          </el-tab-pane>
          <el-tab-pane label="个人所在项目" name="second">
            <!-- <el-card class="box-card" v-for="item in projectlist">
              <div class="text item name">
                <a href="/project_outline" class="goproject">
                  <span class="og">项目名称：</span>
                  {{ item.project_name }}
                </a>
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
            </el-card> -->
            <div class="bar header">
              <div class="left">
                <!-- <img src="../assets/bk3.jpg" alt=""/> -->
                <a class="goteam">
                  项目名称</a>
              </div>
              <div class="right">
                <p class="default">项目编号</p>
                <p class="default long">创建时间</p>
                <p class="default long">更新时间</p>
                <p class="default long">所属团队名称</p>
              </div>
            </div>
            <div class="bar" v-for="item in projectlist">
              <div class="left">
                <!-- <img src="../assets/bk3.jpg" alt=""/> -->
                <a href="#" class="goteam" @click="goproject(item)">
                  {{ item.project_name }}</a>
              </div>
              <div class="right">
                <p class="default">{{ item.project_id }}</p>
                <p class="default long">{{ item.create_time }}</p>
                <p class="default long">{{ item.update_time }}</p>
                <p class="default long">{{ item.team_name }}</p>
              </div>
            </div>
          </el-tab-pane>
          <el-tab-pane label="个人回收项目" name="third">
            <!-- <el-card class="box-card" v-for="item in reprojectlist">
              <a href="/team_dustbin" class="goreproject">
                <div class="text item name">
                  <span class="og">项目名称：</span>
                  {{ item.project_name }}
                </div>
              </a>
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
            </el-card> -->

            <div class="bar header">
              <div class="left">
                <!-- <img src="../assets/bk3.jpg" alt=""/> -->
                <a class="goteam">
                  项目名称</a>
              </div>
              <div class="right">
                <p class="default">项目编号</p>
                <p class="default long">创建时间</p>
                <p class="default long">更新时间</p>
                <p class="default long">所属团队名称</p>
              </div>
            </div>
            <div class="bar" v-for="item in reprojectlist">
              <div class="left">
                <!-- <img src="../assets/bk3.jpg" alt=""/> -->
                <a href="#" class="goreproject" @click="goreproject(item)">
                  {{ item.project_name }}</a>
              </div>
              <div class="right">
                <p class="default">{{ item.project_id }}</p>
                <p class="default long">{{ item.create_time }}</p>
                <p class="default long">{{ item.update_time }}</p>
                <p class="default long">{{ item.team_name }}</p>
              </div>
            </div>

          </el-tab-pane>
          <!-- <el-tab-pane label="已经完成项目" name="third">角色管理</el-tab-pane> -->
        </el-tabs>
      </el-row>
    </el-main>
  </el-container>
</template>

<script>
import qs from "qs";
import axios from "axios";
import async from "async";
import topFrame from "../../components/topFrame.vue";
export default {
  inject: ['reload'],
  data() {
    var validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入新密码'));
      }
      else if (!/^\w+$/.exec(value) || value.length > 16 || value.length < 8) {
        callback(new Error('新密码格式错误'));
      }
      else {
        if (value === this.password.originPassWord) {
          callback(new Error('新旧密码不能相同'));
        }
        if (this.password.checkpasswordChange !== '') {
          this.$refs.password.validateField('checkpasswordChange');
        }
        callback();
      }
    };
    var validatePass2 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请再次输入新密码'));
      } else if (value !== this.password.passwordChange) {
        callback(new Error('两次输入密码不一致!'));
      } else {
        callback();
      }
    };
    return {
      imageurl: "",
      img_base64: "",
      activeName: "first",
      username: "",
      userId: "",
      realname: "",
      email: "",
      word: "",
      new_username: "",
      new_real_name: "",
      new_email: "",
      new_word: "",
      notedit: true,
      noteditRealName: true,
      noteditUserName: true,
      noteditWord: true,
      PasswordChangeDialogVi: false,
      teamlist: [],
      projectlist: [],
      reprojectlist: [],
      profile: "",
      password: {
        originPassWord: "",
        passwordChange: "",
        checkpasswordChange: "",
      },
      rules: {
        originPassWord:
          [
            { required: true, message: '请输入旧密码', trigger: 'blur' }
          ],
        passwordChange: [
          { validator: validatePass, trigger: 'blur' }
        ],
        checkpasswordChange: [
          { validator: validatePass2, trigger: 'blur' }
        ],
      },
      headers: {
        authorization: JSON.parse(sessionStorage.getItem('token')).token_num
      },
    };
  },
  components: {
    topFrame
  },
  methods: {
    goBack() {
      this.$router.go(-1);
    },
    changePass(formName) {
      // 检验数据的可行性
      this.$refs[formName].validate((valid) => {
        if (valid) {
          
          this.$axios({
            method: "post" /* 指明请求方式，可以是 get 或 post */,
            url: "app/update_password" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
            data: qs.stringify({
              /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
              pass_word: this.password.passwordChange,
              old_pass_word: this.password.originPassWord
            }),
          })
            .then((res) => {
              
              if (res.data.errno == 0) {
                this.$message({
                  // message: res.data.msg,
                  message: "成功修改密码",
                  center: true,
                  type: "success",
                });
              } else {
                this.$message({
                  message: res.data.msg.concat("，修改失败"),
                  center: true,
                  type: "warning",
                });
              }
              this.resetForm('password');
              this.PasswordChangeDialogVi = false;
            })
            .catch((err) => {
              console.log(err); /* 若出现异常则在终端输出相关信息 */
            })
        } else {
          this.$message.warning("请检查您的输入")
          
          return false;
        }
      });
      // resetForm('password')
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    gotoTeam(val) {
      var content = { team_id: val.teamId, team_name: val.teamname };
      this.$store.dispatch("saveteam", content);
      this.$message({
        message: "正在跳转至团队详细页面",
        center: true,
        type: "success",
        duration: 1000
      });
      setTimeout(() => {
        this.$router.push({ path: 'team_outline' });
      }, 1000);
    },
    goproject(val) {
      var content = { team_id: val.team_id, team_name: val.team_name };
      var content2 = { project_id: val.project_id, project_name: val.project_name };
      this.$store.dispatch("saveteam", content);
      this.$store.dispatch("saveproject", content2);
      this.$message({
        message: "正在跳转至项目详细页面",
        center: true,
        type: "success",
        duration: 1000
      });
      setTimeout(() => {
        this.$router.push({ path: 'project_outline' });
      }, 1000);
    },
    goreproject(val) {
      var content = { team_id: val.team_id, team_name: val.team_name };
      var content2 = { project_id: val.project_id, project_name: val.project_name };
      this.$store.dispatch("saveteam", content);
      this.$store.dispatch("saveproject", content2);
      this.$message({
        message: "正在跳转至回收站",
        center: true,
        type: "success",
        duration: 1000
      });
      setTimeout(() => {
        this.$router.push({ path: 'team_dustbin' });
      }, 1000);
    },
    edit() {
      this.PasswordChangeDialogVi = true;
    },
    changeUserName() {
      this.noteditUserName = false;
    },
    saveUserName() {
      this.noteditUserName = true;
      this.$axios({
        method: "post" /* 指明请求方式，可以是 get 或 post */,
        url: "app/update_username" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
        // headers:{
        //   'authorization':JSON.parse(sessionStorage.getItem("token")).token_num
        // },
        data: qs.stringify({
          /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
          user_name: this.new_username,
        }),
      })
        .then((res) => {
          
          if (res.data.errno == 0) {
            this.$message({
              // message: res.data.msg,
              message: "成功修改用户名",
              center: true,
              type: "success",
            });
            var user = { userId: this.userId, username: this.new_username }
            this.$store.dispatch("saveuser", user);
          } else {
            this.$message({
              message: res.data.msg,
              center: true,
              type: "warning",
            });
          }
        })
        .catch((err) => {
          console.log(err); /* 若出现异常则在终端输出相关信息 */
        })
      // this.reload();

      setTimeout(() => { location.reload() }, 1000);
    },
    changeRealName() {
      this.noteditRealName = false;
    },
    saveRealName() {
      this.noteditRealName = true;
      this.$axios({
        method: "post" /* 指明请求方式，可以是 get 或 post */,
        url: "app/update_user_realname" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
        // headers:{
        //   'authorization':JSON.parse(sessionStorage.getItem("token")).token_num
        // },
        data: qs.stringify({
          /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
          real_name: this.new_real_name,
        }),
      })
        .then((res) => {
          
          if (res.data.errno == 0) {
            this.$message({
              // message: res.data.msg,
              message: "成功修改真实姓名",
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
        .catch((err) => {
          console.log(err); /* 若出现异常则在终端输出相关信息 */
        });
      setTimeout(() => { location.reload() }, 1000);
    },
    changeWord() {
      this.noteditWord = false;
    },
    saveWord() {
      this.noteditWord = true;
      this.$axios({
        method: "post" /* 指明请求方式，可以是 get 或 post */,
        url: "app/update_user_info" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
        // headers:{
        //   'authorization':JSON.parse(sessionStorage.getItem("token")).token_num
        // },
        data: qs.stringify({
          /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
          user_info: this.new_word,
        }),
      })
        .then((res) => {
          
          if (res.data.errno == 0) {
            this.$message({
              // message: res.data.msg,
              message: "成功修改个性签名",
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
        .catch((err) => {
          console.log(err); /* 若出现异常则在终端输出相关信息 */
        });
      setTimeout(() => { location.reload() }, 1000);
    },
    // save() {
    //   this.notedit = true;
    //   this.$axios({
    //     method: "post" /* 指明请求方式，可以是 get 或 post */,
    //     url: "app/update_user_info" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
    //     // headers:{
    //     //   'authorization':JSON.parse(sessionStorage.getItem("token")).token_num
    //     // },
    //     data: qs.stringify({
    //       /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
    //       real_name: this.new_real_name,
    //       user_info: this.new_word,
    //     }),
    //   })
    //     .then((res) => {
    //       
    //       if (res.data.errno == 0) {
    //         this.$message({
    //           message: res.data.msg,
    //           center: true,
    //           type: "success",
    //         });
    //       } else {
    //         this.$message({
    //           message: res.data.msg,
    //           center: true,
    //           type: "warning",
    //         });
    //       }
    //     })
    //     .catch((err) => {
    //       
    //     });
    //   this.reload();
    // },
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
            
            this.userId = res.data.data.user_id;
            this.username = res.data.data.user_name;
            this.realname = res.data.data.real_name;
            this.email = res.data.data.email;
            this.word = res.data.data.user_info;
            this.new_real_name = this.realname;
            this.new_username = this.username;
            this.new_word = this.word;
            this.profile = res.data.data.profile
          } else {
            this.$message({
              message: res.data.msg,
              center: true,
              type: "error",
            });
          }
        })
        .catch((err) => {
          console.log(err); /* 若出现异常则在终端输出相关信息 */
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
                teamId: "",
                teamname: "",
                teamtype: "",
                teamsetter: "",
                teamsettime: "",
                teamernum: "",
              };
              tmp.teamId = item.team_id;
              tmp.teamname = item.team_name;
              tmp.teamtype = item.team_type;
              tmp.teamsetter = item.team_owner_user_name;
              tmp.teamsettime = item.team_time;
              tmp.teamernum = item.team_member_num;
              
              this.teamlist.push(tmp);
              
            });
            res.data.data.team_list_admin.forEach((item) => {
              var tmp = {
                teamId: "",
                teamname: "",
                teamtype: "",
                teamsetter: "",
                teamsettime: "",
                teamernum: "",
              };
              tmp.teamId = item.team_id;
              tmp.teamname = item.team_name;
              tmp.teamtype = item.team_type;
              tmp.teamsetter = item.team_owner_user_name;
              tmp.teamsettime = item.team_time;
              tmp.teamernum = item.team_member_num;
              
              this.teamlist.push(tmp);
              
            });
            res.data.data.team_list_member.forEach((item) => {
              var tmp = {
                teamId: "",
                teamname: "",
                teamtype: "",
                teamsetter: "",
                teamsettime: "",
                teamernum: "",
              };
              tmp.teamId = item.team_id;
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
                team_name: ""
              };
              tmp.project_id = item.project_id;
              tmp.project_name = item.project_name;
              tmp.create_time = item.create_time;
              tmp.update_time = item.update_time;
              tmp.team_id = item.team_id;
              tmp.team_name = item.team_name;
              console.log(tmp);
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
          console.log(err); /* 若出现异常则在终端输出相关信息 */
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
                team_name: ""
              };
              tmp.project_id = item.project_id;
              tmp.project_name = item.project_name;
              tmp.create_time = item.create_time;
              tmp.update_time = item.update_time;
              tmp.team_id = item.team_id;
              tmp.team_name = item.team_name;
              console.log(tmp);
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
          console.log(err); /* 若出现异常则在终端输出相关信息 */
        });
    },
    // 修改头像部分
    beforeAvatarUpload(file) {
      const isJPG = file.type == 'image/jpeg' || file.type == 'image/png'
      const isLt2M = file.size / 1024 / 1024 < 1;
      if (!isJPG) {
        this.$message.error("上传头像图片格式错误!");
      }
      if (!isLt2M) {
        this.$message.error("上传头像图片大小不能超过 1MB!");
      }
      return isJPG && isLt2M;
    },
    beforeAvatarUpload1(file) {
      const isJPG = file.type == 'image/jpeg' || file.type == 'image/png'
      const isLt2M = file.size / 1024 / 1024 < 1;
      return isJPG && isLt2M;
    },
    handleChange(file) {
      var a = this.beforeAvatarUpload1(file)
      if (!a) {
        return;
      }
    },
    // 转换base64方法时Promise对象，必须换成同步
    // 网上还有其它办法，但是尝试过后，这个方法出错的概率最低
    async uploadImg(file) {
      // 这里不一定是file.file，如果使用的方法不一样，有的是file.raw
      // 这里传入的应该是组件中携带的文件信息
      var base64Str = await this.getBase64(file.file);
      this.img_base64 = base64Str;
      
      this.imgString = base64Str.split(',');
      
      this.$axios({
        method: "post",
        url: "app/upload_profile",
        data: qs.stringify({
          profile: this.img_base64
        }),
      })
        .then((res) => {
          
          
          
          if (res.data.errno == 0) {
            this.$message.success("修改头像成功");
            var usericon = { userId: this.userId, picurl: this.img_base64 };
            this.$store.dispatch("saveusericon", usericon);
            window.location.reload();
          }
          else {
            this.$message({
              message: res.data.msg,
              center: true,
              type: "error",
            });
          }
        })
        .catch((err) => {
          console.log(err);
        });
      // setTimeout(() => {
      //       window.location.reload();
      //     }, 1000);
    },
    // 获取图片转base64，这里用的是Promise，所以调用方法时必须转换成同步（async，await）
    // 否则上传数据时好时坏，能不能上传成功全看运气 ^_^
    getBase64(file) {
      return new Promise(function (resolve, reject) {
        const reader = new FileReader();
        let imgResult = '';
        reader.readAsDataURL(file)
        reader.onload = function () {
          imgResult = reader.result;
        }
        reader.onerror = function (error) {
          reject(error);
        }
        reader.onloadend = function () {
          resolve(imgResult);
        }
      })
    },
    // 上传图片到服务器
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
.el-tabs--left,
.el-tabs--right {
  overflow: hidden;
  margin-left: 5%;
  margin-top: 7%;
}

.el-main {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  padding-top: 40px;
  overflow-y: hidden;
  background-color: rgba(234, 239, 242, 0.425);
}

.el-header {
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

.el-tabs--left>>>.el-tabs__item.is-left {
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

.tag1 {
  font-weight: bold;
  font-size: 30px;
  color: #BBBBBB
}

.re {
  padding: 0px 0;
  text-align: center;
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

.goteam {
  text-decoration: none;
  color: black;
}

.goproject {
  text-decoration: none;
  color: black;
}

.goreproject {
  text-decoration: none;
  color: black;
}

.CUN:hover {
  color: lightskyblue
}

.SUN:hover {
  color: lightskyblue
}

.SW:hover {
  color: lightskyblue
}

.CW:hover {
  color: lightskyblue
}

.SRN:hover {
  color: lightskyblue
}

.CRN:hover {
  color: lightskyblue
}

.bar {
  right: 0;
  border: 1px solid rgba(206, 218, 226, 0.536);
  border-radius: 15px;
  margin: 10px 20px 10px 20px;
  padding: 10px;
  /* 居中 */
  align-items: center;
  display: flex;
  transition: 0.4s;
  color: black;
  padding-left: 30px;
  background: rgba(206, 207, 217, 0.4);
  height: fit-content;
}

.bar:hover {
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 10px rgba(0, 0, 0, 0.04);
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
  right: 5%;
  overflow: hidden;
}

img {
  width: 50px;
  height: 50px;
  margin: 10px;
  border-radius: 50%;
}

p {
  display: inline;
  margin: 10px;
  vertical-align: center;
}

.default {
  display: inline-block;
  white-space: nowrap;
  width: 80px;
  overflow: hidden;
  text-overflow: ellipsis;
  /* background: rgb(240, 242, 243); */
}

.default {
  width: 100px;
  height: auto;
  /* word-wrap: break-word; */
}

.long {
  width: 200px;
}

.header {
  background: rgb(164, 170, 183);
  color: rgb(255, 255, 255);
  /* border-radius: 5px !important; */
}

.bar a {
  /* background: rgb(164, 170, 183); */
  /* color: rgb(255, 255, 255); */
  /* display: sticky; */
  width: 500px;
  text-align: left;
}

.header a {
  color: rgb(255, 255, 255);
}

.el-tabs--left {
  color: rgb(164, 170, 183) !important;
  /* background: #000; */
  border: rgba(164, 170, 183, 0.403) solid 1px;
  border-radius: 28px;
  background-color: #ffffff7a;
  padding: 10px 0px;
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
</style>