<template>
  <div class="login">
    <img src="../assets/bg.svg" alt="" />
    <div class="logo">
      INK BOOK
    </div>
    <div class="kuang">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="邮箱登录" name="first">
          <el-form ref="form" :model="form" class="form">
            <el-form-item prop="email" :rules="[{ required: true, message: '邮箱不能为空' }]">
              <el-input v-model="form.email" placeholder="请输入您的邮箱" type="email" autocomplete="off" clearable
                prefix-icon="el-icon-postcard"></el-input>
            </el-form-item>
            <el-form-item id="password" prop="password" :rules="[{ required: true, message: '密码不能为空' }]">
              <el-input prefix-icon="el-icon-lock" placeholder="请输入您的密码" show-password type="password" clearable
                v-model="form.password" autocomplete="off" @keyup.enter.native="login"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button class="btn_login" type="primary" @click="login" round>登&nbsp;&nbsp;录</el-button>
            </el-form-item>
          </el-form>
        </el-tab-pane>
        <el-tab-pane label="用户名登录" name="second">
          <el-form ref="form" :model="form" class="form">
            <el-form-item prop="username" :rules="[{ required: true, message: '用户名不能为空' }]">
              <el-input v-model="form.username" placeholder="请输入您的用户名" type="username" autocomplete="off" clearable
                prefix-icon="el-icon-postcard"></el-input>
            </el-form-item>
            <el-form-item id="password" prop="password" :rules="[{ required: true, message: '密码不能为空' }]">
              <el-input prefix-icon="el-icon-lock" placeholder="请输入您的密码" show-password type="password" clearable
                v-model="form.password" autocomplete="off" @keyup.enter.native="login"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button class="btn_login" type="primary" @click="login" round>登&nbsp;&nbsp;录</el-button>
            </el-form-item>
          </el-form>
        </el-tab-pane>
        <el-dialog v-if="forgetDialogVisible" :modal="false" title="请输入您的邮箱" :visible.sync="forgetDialogVisible" width="30%" :close-on-click-modal="false"
          :close-on-press-escape="false" :append-to-body="true" center>
          <el-form ref="forget" :model="forget" class="forget" :hide-required-asterisk="true">
            <el-form-item prop="forget_email" :rules="[
              { required: true, message: '请输入邮箱地址', trigger: 'blur' },
              { type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur', 'change'] }
            ]">
              <el-input id='forget' v-model="forget.forget_email" placeholder="请输入注册时所用邮箱，用于找回密码" type="email" autocomplete="off"
                clearable prefix-icon="el-icon-postcard"></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button class="forget" @click="forgetDialogVisible = false, resetForm('forget')">取 消</el-button>
            <el-button class="forget" type="primary"
              @click="toReset(forget.forget_email)">确 定
            </el-button>
          </span>
        </el-dialog>
        <div class="back" @click="forgetPass()">忘记密码</div>
        <div class="regis" @click="toRegister">没有账号？前去注册</div>

      </el-tabs>

    </div>
  </div>
</template>

<script>
import qs from "qs";
import axios from "axios";
import async from "async";
export default {
  name: "Login",
  data() {
    return {
      form: {
        email: "",
        username: "",
        password: "",
      },
      forget: {
        forget_email: "",
      },
      loginmethod: "",
      activeName: "first",
      forgetDialogVisible: false,
    };
  },
  methods: {
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    async login() {
      if (this.activeName == "first") {
        if (this.form.email === "" && this.form.password === "") {
          this.$message.warning("请输入邮箱和密码！");
          return;
        } else if (this.form.email === "") {
          this.$message.warning("邮箱不能为空!");
          return;
        } else if (this.form.password === "") {
          this.$message.warning("密码不能为空！");
          return;
        }
        await this.$axios({
          method: "post" /* 指明请求方式，可以是 get 或 post */,
          url: "app/login" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
          data: qs.stringify({
            /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
            identity: this.form.email,
            loginmethod: "email",
            password: this.form.password,
          }),
        })
          .then(async (res) => {
            
            /* res 是 response 的缩写 */
            // var usericon = {userId:  res.data.User_id,picurl:res.data.avatar_url};
            // this.$store.dispatch("saveusericon", usericon);
            if (res.data.errno == 0) {
              this.$message.success("登录成功！");
              var user = {
                userId: res.data.data.user_id,
                username: res.data.data.user_name,
              };
              var token = {
                token_num: res.data.data.token
              };
              var usericon = {userId: res.data.data.user_id,picurl: res.data.data.profile};
              await this.$store.dispatch("saveusericon", usericon);
              await this.$store.dispatch("saveuser", user);
              await this.$store.dispatch("savetoken", token);
              localStorage.setItem("saveuser", qs.stringify(user));
              localStorage.setItem("savetoken", qs.stringify(token));
              
              
              
              window.location.href = "team_outline";
              /* 从 localStorage 中读取 preRoute 键对应的值 */
              // const history_pth = localStorage.getItem("FirstPage");
              /* 若保存的路由为空或为注册路由，则跳转首页；否则跳转前路由（setTimeout表示1000ms后执行） */
              // setTimeout(() => {
              //   if (history_pth == null || history_pth === "/register") {
              //     this.$router.push("/");
              //   } else {
              //     this.$router.push({path: history_pth});
              //   }
              // }, 1000);
              axios.interceptors.request.use(
                config => {
                  config.headers['Authorization'] = token
                  return config;
                },
                error => {
                  return Promise.reject(error);
                }
              );
            } else {
              await this.$message({
                message: res.data.msg,
                center: true,
                type: "error",
              });
            }
          })
          .catch((err) => {
            
          });
      } else {
        if (this.form.username === "" && this.form.password === "") {
          this.$message.warning("请输入用户名和密码！");
          return;
        } else if (this.form.username === "") {
          this.$message.warning("用户名不能为空!");
          return;
        } else if (this.form.password === "") {
          this.$message.warning("密码不能为空！");
          return;
        }
        await this.$axios({
          method: "post" /* 指明请求方式，可以是 get 或 post */,
          url: "app/login" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
          data: qs.stringify({
            /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
            identity: this.form.username,
            loginmethod: "user_name",
            password: this.form.password,
          }),
        })
          .then(async (res) => {
            /* res 是 response 的缩写 */
            // var usericon = {userId:  res.data.User_id,picurl:res.data.avatar_url};
            // this.$store.dispatch("saveusericon", usericon);
            
            if (res.data.errno == 0) {
              this.$message.success("登录成功！");
              var user = {
                userId: res.data.data.user_id,
                username: res.data.data.user_name,
              };
              var token = {
                token_num: res.data.data.token
              };
              var usericon = {userId: res.data.data.user_id,picurl: res.data.data.profile};
              await this.$store.dispatch("saveusericon", usericon);
              await this.$store.dispatch("saveuser", user);
              await this.$store.dispatch("savetoken", token);
              localStorage.setItem("saveuser", qs.stringify(user));
              localStorage.setItem("savetoken", qs.stringify(token));
              
              
              
              window.location.href = "team_outline";
              /* 从 localStorage 中读取 preRoute 键对应的值 */
              // const history_pth = localStorage.getItem("FirstPage");
              /* 若保存的路由为空或为注册路由，则跳转首页；否则跳转前路由（setTimeout表示1000ms后执行） */
              // setTimeout(() => {
              //   if (history_pth == null || history_pth === "/register") {
              //     this.$router.push("/");
              //   } else {
              //     this.$router.push({ path: history_pth });
              //   }
              // }, 1000);
              axios.interceptors.request.use(
                config => {
                  config.headers['Authorization'] = token
                  return config;
                },
                error => {
                  return Promise.reject(error);
                }
              );
            }
            else {
              await this.$message({
                message: res.data.msg,
                center: true,
                type: "error",
              });
            }
          })
          .catch((err) => {
            
          });
      }
    },
    toRegister() {
      this.$router.push("/register");
    },
    forgetPass() {
      this.forgetDialogVisible = true;
    },
    toReset(i) {
      if (
        this.forget.forget_email === ""
      ) {
        this.$message.warning("注册邮箱不能为空！");
        return;
      }
      if (
        !/^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/.exec(
          this.forget.forget_email
        )
      ) {
        this.$message.warning("请检查您的邮箱格式！");
        return;
      }
      this.$axios({
        method: "post",
        url: "app/generate_forget_link",
        data: qs.stringify({
          email: i
        }),
      })
        .then((res) => {
          
          
          if (res.data.errno == 0) {
            this.$message.success("邮件已发送,请前往您的邮箱查看信息")
          }
          else {
            this.$message({
              message: res.data.msg,
              center: true,
              type: "error",
            });
          }
        },
        this.forgetDialogVisible=false,
        this.resetForm('forget')
        )
        .catch((err) => {
          
        });
    }
  },
};
</script>

<style scoped>
@font-face {
  font-family: myFont;
  src: url("../assets/Futura.ttc");
}

.logo {
  float: left;
  font-size: 40px;
  font-weight: 400;
  font-family: myfont;
  color: black;
  position: absolute;
  top: 15%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.kuang {
  width: 300px;
  height: auto;
  padding: 0 25px;
  /* border: 1px solid grey; */
  margin: 21px auto;
  border-radius: 40px;
  line-height: 80px;
  /*可以让文字往下移一点 */

  position: absolute;
  top: 45%;
  left: 50%;
  transform: translate(-50%, -50%);

  transition: 0.4s;

  backdrop-filter: blur(2px);
  background-color: rgba(255, 255, 255, 0.5);

  box-shadow: 0 0px 0px rgb(0 0 0 / 10%), 0 12px 20px rgb(38 38 38 / 12%);
}

.kuang:hover {
  width: 320px;
  height: auto;
}

.login {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  /* background-repeat:no-repeat;
   background-size:100%;
   background-image: url("../../assets/backgroundimg/bg.png"); */
  /* height: 100%; */
  height: 100vh;
  width: 100%;
  overflow: hidden;
  overflow-y: hidden;
}

.login img {
  height: 100%;
  width: 100%;
}

.login>>>.el-input__inner {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

.el-tabs {
  margin-top: 30px;
}

.regis {
  font-size: 12px;
  float: right;
  color: #999;
  cursor: pointer;
  transition: 0.4s;
}

.regis:hover {
  color: rgb(145, 171, 203);
  font-size: 15px;
  padding: 0px 0px 10px 0px;
}

.back {
  font-size: 12px;
  float: left;
  color: #999;
  cursor: pointer;
  transition: 0.4s;
}

.back:hover {
  color: rgb(145, 171, 203);
  font-size: 15px;
  padding: 0px 0px 10px 0px;
}

.el-form {
  border-radius: 25px !important;
}

.el-button:not(.forget) {
  border: none;
  border-radius: 20px !important;
  background-color: rgba(121, 167, 213, 0.73);
  font-size: 20px;
  width: 100%;
  transition: 0.4s !important;
}

.el-button:hover:not(.forget:hover) {
  border-radius: 20px !important;
  background-color: rgba(121, 167, 213, 0.377);
  margin: 10px 0px 10px 0px;
  font-size: 20px;
}

.el-button:active:not(.forget:active) {
  border-radius: 20px !important;
  background-color: rgb(82, 109, 137);

  font-size: 20px;
}
.forget {
  width: 40%;
}
/deep/ #forget {
  width: 250% !important;
}
</style>