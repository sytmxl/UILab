<template>
  <div class="register">
    <img src="../assets/bg.svg" alt="" />
    <div class="logo">
      INK BOOK
    </div>
    <div class="kuang">
      <p>重设您的密码</p>
      <el-form ref="form" :model="form" class="form" :rules="rules">
        <el-form-item id="password1" prop="password1">
          <el-input prefix-icon="el-icon-lock" placeholder="请输入新密码" show-password type="password" clearable
            v-model="form.password1" autocomplete="off">
            <el-dropdown slot="suffix" size="mini" placement="top-start">
              <i class="el-icon-warning-outline el-input__icon">
              </i>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item disabled>新密码仅能由数字、26个英文字母或者下划线组成，长度为8-16位</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </el-input>
        </el-form-item>
        <el-form-item id="password2" prop="password2">
          <el-input prefix-icon="el-icon-lock" placeholder="请确认新密码" show-password type="password" clearable
            v-model="form.password2" autocomplete="off"></el-input>
        </el-form-item>
        <div class="regis">
          <el-button class="btn_login" type="primary" @click="findPass('form')" round>保存</el-button>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script>
import qs from "qs";
import App from "@/App.vue";
export default {
  name: "forget",
  data() {
    var validatePass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入新的密码"));
      }
      else if (!/^\w+$/.exec(value) || value.length > 16 || value.length < 8) {
        callback(new Error('新密码格式错误'));
      }
      else {
        if (this.form.password2 !== "") {
          this.$refs.form.validateField("password2");
        }
        callback();
      }
    };
    var validatePass2 = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入新密码"));
      }
      else if (value !== this.form.password1) {
        callback(new Error("两次输入密码不一致!"));
      }
      else {
        callback();
      }
    };
    return {
      str: this.$route.params.id,
      form: {
        password1: "",
        password2: "",
      },
      rules: {
        password1: [
          { validator: validatePass, trigger: 'blur' }
        ],
        password2: [
          { validator: validatePass2, trigger: 'blur' }
        ],
      }
    };
  },
  methods: {
    findPass(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          
          this.$axios({
            method: "post",
            url: "app/forget_by_token",
            data: qs.stringify({
              // token: this.str.toString().split('localhost/')[1]
              token: this.str,
              pass_word:this.form.password1
            }),
          })
            .then((res) => {
              
              
              if (res.data.errno == 0) {
                this.$message.success("成功重设您的密码，将跳转至登录页面");
                setTimeout(() => {
                  this.$router.push({ name: 'login' });
              }, 2000);
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
        }
        else {
          
          this.$message.warning("请检查您的输入")
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
  },
  components: { App }
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
  top: 35%;
  left: 50%;
  transform: translate(-50%, -50%);

  transition: 0.5s;

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
  font-size: 20px;
  color: #999;
  cursor: pointer;
  transition: 0.5s;
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
  transition: 0.5s !important;
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
</style>