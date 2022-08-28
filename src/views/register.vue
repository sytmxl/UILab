<template>
  <div class="register">
    <img src="../assets/bg.svg" alt="" />
    <div class="logo">
      INK BOOK
    </div>
    <div class="kuang">
      <p>Welcome</p>
      <el-form ref="form" :model="form" class="form">
        <el-form-item prop="email"
          :rules="[{ required: true, message: '邮箱不能为空', trigger: 'blur' }, { type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur', 'change'] }]">
          <el-input v-model="form.email" placeholder="请输入邮箱" type="email" autocomplete="off" clearable
            prefix-icon="el-icon-postcard">
          </el-input>
        </el-form-item>
        <el-form-item prop="username" :rules="[{ required: true, message: '用户名不能为空' }]">
          <el-input v-model="form.username" placeholder="请输入用户名" type="text" autocomplete="off" clearable
            prefix-icon="el-icon-user" maxlength="15">
            <el-dropdown slot="suffix" size="mini" placement="top-start">
              <i class="el-icon-warning-outline el-input__icon">
              </i>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item disabled>用户名仅能由中文字符、数字、下划线以及英文字母组成，长度不超过15个字符</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </el-input>
        </el-form-item>
        <el-form-item prop="realname" :rules="[{ required: true, message: '真实姓名不能为空' }]">
          <el-input v-model="form.realname" placeholder="请输入真实姓名" type="realname" autocomplete="off" clearable
            prefix-icon="el-icon-user" maxlength="15">
            <el-dropdown slot="suffix" size="mini" placement="top-start">
              <i class="el-icon-warning-outline el-input__icon">
              </i>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item disabled>真实姓名仅能由中文字符或英文组成，长度不超过15个字符</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </el-input>
        </el-form-item>
        <el-form-item id="password1" prop="password1" :rules="[{ required: true, message: '密码不能为空' }]">
          <el-input prefix-icon="el-icon-lock" placeholder="请输入密码" show-password type="password" clearable
            v-model="form.password1" autocomplete="off" @keyup.enter.native="register">
            <el-dropdown slot="suffix" size="mini" placement="top-start">
              <i class="el-icon-warning-outline el-input__icon">
              </i>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item disabled>密码仅能由数字、26个英文字母或者下划线组成，长度为8-16位</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </el-input>
        </el-form-item>
        <el-form-item id="password2" prop="password2" :rules="[{ required: true, message: '请再次输入密码' }]">
          <el-input prefix-icon="el-icon-lock" placeholder="请确认密码" show-password type="password" clearable
            v-model="form.password2" autocomplete="off" @keyup.enter.native="register"></el-input>
        </el-form-item>
        <el-form-item id="code" prop="code" :rules="[{ required: true, message: '请输入验证码' }]">
          <el-input placeholder="请输入验证码" type="text" clearable v-model="form.code" autocomplete="off"
            prefix-icon="el-icon-s-check" @keyup.enter.native="register" style="width: 60%; float: left"></el-input>
          <el-button class="send" type="primary" style="float: right" @click="submit">发送</el-button>
        </el-form-item>
        <el-form-item>
          <el-button class="btn_register" type="primary" @click="register" round>注&nbsp;册</el-button>
          <el-button class="btn_reset" @click="resetForm('form')" round>重&nbsp;置</el-button>
        </el-form-item>
        <div class="re" @click="toFirst">返回首页</div>
        <div class="regis" @click="toRegister">已有账号？前去登录</div>
      </el-form>
    </div>
  </div>
</template>

<script>
import qs from "qs";
import team_projectsVue from './teams/team_projects.vue';
export default {
  name: "Register",
  data() {
    return {
      form: {
        email: "",
        username: "",
        realname: "",
        password1: "",
        password2: "",
        code: "",
        validcode: "",
      },
    };
  },
  methods: {
    submit() {
      // 
      if (
        this.form.email === ""
      ) {
        this.$message.warning("注册邮箱不能为空！");
        return;
      }
      if (
        !/^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/.exec(
          this.form.email
        )
      ) {
        this.$message.warning("请检查您的邮箱格式！");
        return;
      }
      this.$axios({
        method: "post" /* 指明请求方式，可以是 get 或 post */,
        url: "app/send_code" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
        data: qs.stringify({
          /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
          email: this.form.email,
          send_type: 0,
        }),
      })
        .then((res) => {
          
          if (res.data.errno == 0) {
            this.$message.success("验证码发送成功，请查收");
          }
          else {
            this.$message.error("此邮箱已经被注册！");
          }
        })
        .catch((err) => {
          
        });
    },
    register() {
      if (
        this.form.username === "" ||
        this.form.password1 === "" ||
        this.form.email === "" ||
        this.form.password2 === "" ||
        this.form.realname === "" ||
        this.form.code === ""
      ) {
        this.$message.warning("注册相关信息不能为空！");
        return;
      }
      if (
        !/^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/.exec(
          this.form.email
        )
      ) {
        this.$message.warning("请检查您的邮箱格式！");
        return;
      }
      if (
        !/^[\u4E00-\u9FA5A-Za-z]+$/.exec(this.form.realname)
      ) {
        this.$message.warning("真实姓名仅能由中文字符或英文组成，请检查您的真实姓名输入！");
        return;
      }
      if (
        !/^[\u4E00-\u9FA5A-Za-z0-9_]+$/.exec(this.form.username)
      ) {
        this.$message.warning("用户名仅能由中文字符、数字、下划线以及英文字母组成，请检查您的用户名输入！");
        return;
      }
      if (
        !/^\w+$/.exec(this.form.password1) || this.form.password1.length > 16 || this.form.password1.length < 8
      ) {
        this.$message.warning("密码仅能由数字、26个英文字母或者下划线组成，长度为8-16位，请检查您的密码");
        return;
      }
      if (this.form.password1 != this.form.password2) {
        this.$message.warning("两次输入密码不一致，请检查");
      }
      if (this.code == '') {
        this.$message.warning("请输入验证码");
        return;
      }
      // if(this.form.code!=this.validcode){
      //   this.$message.warning("验证码错误！");
      //   return;
      // }
      // 
      // window.alert("用户名是："+this.username +" 密码是：" +this.password);
      this.$axios({
        method: "post" /* 指明请求方式，可以是 get 或 post */,
        url: "app/register" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
        data: qs.stringify({
          /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
          email: this.form.email,
          user_name: this.form.username,
          pass_word: this.form.password1,
          real_name: this.form.realname,
          code: this.form.code,
        }),
      })
        .then((res) => {
          /* res 是 response 的缩写 */
          
          if (res.data.errno == 0) {
            this.$message({
              message: "注册成功，将自动为您登陆",
              center: true,
              type: "success",
              duration: 900
            });
            var user = {
              userId: res.data.data.user_id,
              username: this.form.username,
            };
            this.$store.dispatch("saveuser", user);

            this.$axios({
              method: "post" /* 指明请求方式，可以是 get 或 post */,
              url: "app/login" /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */,
              data: qs.stringify({
                /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
                identity: this.form.username,
                loginmethod: "user_name",
                password: this.form.password1,
              }),
            })
              .then((res) => {
                /* res 是 response 的缩写 */
                // var usericon = {userId:  res.data.User_id,picurl:res.data.avatar_url};
                // this.$store.dispatch("saveusericon", usericon);
                
                if (res.data.errno == 0) {
                  this.$message.success("登录成功！");
                  var token = {
                    token_num: res.data.data.token
                  }
                  this.$store.dispatch("savetoken", token);
                  localStorage.setItem("saveuser", qs.stringify(user));
                  localStorage.setItem("savetoken", qs.stringify(token));
                  
                  
                  
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
                  this.$message({
                    message: res.data.msg,
                    center: true,
                    type: "error",
                  });
                }
              })
              .catch((err) => {
                
              });
            setTimeout(() => {
              this.$router.push({ path: 'team_outline' });
            }, 1000);
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
          
        });
    },
    toRegister() {
      this.$router.push({ path: "/login" });
    },
    toFirst() {
      this.$router.push({ path: "/" });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
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
  top: 10%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.kuang {
  width: 300px;
  height: auto;
  padding: 30px 25px 0px 25px;
  /* border: 1px solid grey; */
  margin: 21px auto;
  border-radius: 40px;
  line-height: 80px;
  /*可以让文字往下移一点 */

  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);

  transition: 0.4s;

  backdrop-filter: blur(2px);
  background-color: rgba(255, 255, 255, 0.5);

  box-shadow: 0 0px 0px rgb(0 0 0 / 10%), 0 12px 20px rgb(38 38 38 / 12%);
}

.kuang p {
  color: black;
  font-family: myfont;
  font-size: 30px;
}

.kuang:hover {
  width: 320px;
  height: auto;
}

.register {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  /* background-image: url(../assets/bg.png);
  background-repeat:no-repeat;
  background-size:100%; */
  height: 100vh;
  width: 100%;
  overflow: hidden;
}

.register img {
  height: 100%;
  width: 100%;
}

.register>>>.el-input__inner {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

/* .btn_register {
  width: 30%;
  margin-top: 5px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
}
.btn_reset {
  width: 30%;
  margin-top: 5px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
} */
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

.re {
  font-size: 12px;
  float: left;
  color: #999;
  cursor: pointer;
  transition: 0.5s;
}

.re:hover {
  color: rgb(145, 171, 203);
  font-size: 15px;
  padding: 0px 0px 10px 0px;
}

.el-button:not(.send) {
  border: none;
  border-radius: 20px !important;
  background-color: rgba(121, 167, 213, 0.73);
  font-size: 20px;
  width: 30%;
  transition: 0.4s !important;
}

.el-button:not(.send):hover {
  border-radius: 20px !important;
  background-color: rgba(121, 167, 213, 0.377);
  margin: 10px 0px 10px 0px;
  font-size: 20px;
  color: rgb(255, 255, 255);

  width: 60%;
}

.el-button:active {
  border-radius: 20px !important;
  background-color: rgb(82, 109, 137);
  color: rgb(255, 255, 255);

  font-size: 20px;
}

.send {
  height: 40px !important;
  border: 2px rgba(121, 167, 213, 0.377) solid;
  border-radius: 20px !important;
  background-color: rgba(121, 167, 213, 0);
  color: rgb(255, 255, 255);
  font-size: 20px;
  padding: 1px;
  width: 30%;
  transition: 0.4s !important;
}

.send:hover {
  border-radius: 20px !important;
  background-color: rgb(121, 167, 213);
  color: rgb(255, 255, 255);
  width: 33%;
}
</style>