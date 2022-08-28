<template>
  <div>
    <template>
      <el-backtop :right="40" :bottom="60" :visibility-height='400'>
        <div style="{
        height: 100%;
        width: 100%;
        background-color: #f2f5f6;
        box-shadow: 0 0 6px rgba(0,0,0, .12);
        text-align: center;
        line-height: 40px;
        color: #1989fa;
        border-radius:50%;
      }">
          UP
        </div>
        <!-- <div>
          <i class="el-icon-top"></i>
        </div> -->
      </el-backtop>
    </template>
    <div style="overflow: hidden">
      <div id="top_bg">
        <div id="nav-header">
          <a class="brand" href="/"></a>
          <a class="brandtext1" href="/">INK BOOK&nbsp;&nbsp;</a>
          <!-- <div
            id="Layer1"
            style="
              width: 2px;
              height: 50px;
              z-index: 1;
              background-color: #000000;
              layer-background-color: #000000;
              border: 2px none #000000;
            "
          ></div>
          <a class="brandtext2" href="/">&nbsp;墨书</a> -->
        </div>
        <ul id="nav-section">
          <li class="func">
            <a class="part" href="#part1">助力工作</a>
          </li>
          <li class="team">
            <a class="part" href="#part2">团队协作</a>
          </li>
          <li class="draw">
            <a class="part" href="#part3">灵活绘图</a>
          </li>
          <li class="draw">
            <a class="part" href="#part4">多人编辑</a>
          </li>
        </ul>
        <ul id="nav-footer">

          <el-button type="default" @click="register()">注&nbsp;册</el-button>
          <el-button type="primary" @click="login">登&nbsp;录</el-button>
          <!-- <div class="login_button" @click="login()">登录</div> -->
          <!-- <div class="login_button" @click="register()">注册</div> -->
        </ul>
      </div>
    </div>
    <div class="dis1" id="part1">
      <div class="space"></div>
      <div class="space1">
        <div class="txt1">
          <div class="txt1-1 wow fadeInLeft" data-wow-delay="0.35s">
            助力每个团队的工作
          </div>
          <div class="txt1-2 wow fadeInLeft " data-wow-delay="0.35s">
            墨书在线协作平台，旨在帮助每个人更好的完成自己的工作，为中小团队的项目协作开发提供管理服务。
          </div>
        </div>
        <div class="video">
          <video autoplay playsinline muted class="video1">
            <source src="//cloud.video.taobao.com/play/u/1458252/p/1/e/6/t/1/291939960010.mp4" type="video/mp4">您的浏览器不支持
            video 标签。
          </video>
        </div>
      </div>
    </div>
    <div class="dis2" id="part2">
      <div class="space2"></div>
      <div class="space2-1">
        <div class="pic1 wow animate__fadeInUp" data-wow-delay="0.35s">
          <img src="../assets/student2.png" class="pic1-1">
        </div>
        <div class="txt2">
          <div class="txt2-1 wow animate__fadeInUp" data-wow-delay="0.35s">
            使团队高效率协作成为可能
          </div>
          <div class="txt2-2 wow animate__fadeInUp" data-wow-delay="0.35s">
            墨书在线协作平台，为您提供丰富的功能，从产品的诞生到完善，让团队高效率完成协作，为企业创造更大的价值
          </div>
        </div>
      </div>

    </div>
    <div class="dis1" id="part3">
      <div class="space"></div>
      <div class="space1">
        <div class="txt3">
          <div class="txt3-1 wow animate__slideInUp" data-wow-delay="0.35s">
            内置多款画图工具，随时灵活绘图
          </div>
          <div class="txt3-2 wow animate__slideInUp" data-wow-delay="0.35s">
            墨书在线协作平台，提供了原型设计，UML 图绘制等多样的绘图功能，助力您实现各种绘图需求
          </div>
        </div>
        <div class="pic2 wow animate__slideInUp" data-wow-delay="0.35s">
          <img src="../assets/uml.png" class="pic2-1">
        </div>
      </div>
    </div>
    <div class="dis2" id="part4">
      <div class="space2"></div>
      <div class="space2-1">
        <div class="picx wow animate__fadeInUp" data-wow-delay="0.35s">
          <img src="../assets/word.png" class="picx-1">
        </div>
        <div class="txt2">
          <div class="txt2-1 wow animate__fadeInUp" data-wow-delay="0.35s">
            多人在线编辑，让灵感共同迸发
          </div>
          <div class="txt2-2 wow animate__fadeInUp" data-wow-delay="0.35s">
            墨书在线协作平台，让您无论身处何地，一键发起协作，即可和团队成员共同编辑文稿，添加批注讨论，让所有操作有迹可循，你的办公模式从此刻升级
          </div>
        </div>
      </div>

    </div>
    <div class="dis3" id="part3">
      <div class="txt4 wow fadeInLeft" data-wow-delay="0.35s">
        墨书
      </div>
      <div class="txt7 wow fadeInLeft" data-wow-delay="0.35s">
        ·
      </div>
      <div class="txt6 wow fadeInLeft" data-wow-delay="0.35s">
        让产品设计更简单
      </div>
      <div class="txt5 wow fadeInLeft" data-wow-delay="0.35s">
        一款软工团队协作与管理平台，为中小团队的项目协作开发提供管理服务，站在用户的角度，注重用户的隐私性与操作方便性，方便您随时随地管理团队、执行项目
      </div>

      <el-button type="primary" @click="register()" class="wow fadeInLeft" data-wow-delay="0.35s">立即注册</el-button>
    </div>
  </div>
</template>


<script>
// import $ from "jquery";
export default {
  mounted() {
    // 初始化wow
    new this.$wow.WOW().init();
  },
  methods: {
    login() {
      this.$router.push({
        path: "login",
      });
    },
    register() {
      this.$router.push({
        path: "register",
      });
    },
  },
};
// $(function () {
//   $("#top_bg2").hide();
//   $(window).scroll(function () {
//     if ($(document).scrollTop() >= 30) {
//       $("#top_bg2").addClass("fixnav").slideDown();
//     } else {
//       $("#top_bg2").hide();
//     }
//   });
// });
</script>

<style scoped>
.el-button {
  margin: 13px 8px 13px 8px;
}

.el-button--small.is-round {
  padding: 9px 33px;
}

/*设置字体*/
@font-face {
  font-family: myFont;
  src: url("../assets/Futura.ttc");
}

#nav-header {
  display: flex;
  height: 100%;
  margin-left: 90px;
  align-items: center;
  font-size: 35px;
  width: 250px;
}

#nav-section {
  display: flex;
  font-size: 20px;
  width: 650px;
  /* padding-left: 170px; */
}

#nav-section li {
  margin-left: 60px;
}

#nav-section li a {
  user-select: none;
  text-decoration: none;
  color: black;
  ;
  /* color:rgb(166,166,166); */
}

#nav-section li a:hover {
  user-select: none;
  text-decoration: none;
  color: rgb(150, 169, 183);
}

.func {
  display: flex;
  align-items: center;
}

.team {
  display: flex;
  align-items: center;
}

.draw {
  display: flex;
  align-items: center;
}

#nav-footer {
  display: flex;
  margin-left: 350px;
  font-size: 30px;
}

.brandtext1 {
  font-family: myFont;
  user-select: none;
  text-decoration: none;
  color: black;
}

.brandtext2 {
  font-family: "楷体";
  user-select: none;
  text-decoration: none;
  color: black;
}

/* .fixnav {
  position: fixed;
  top: 0px;
  left: 0px;
} */
#top_bg {
  width: 100%;
  height: 72px;
  background: #ffffff;
  display: flex;
  position: fixed;
  backdrop-filter: blur(20px);
  background-color: rgba(255, 255, 255, 0.5);
  z-index: 1;
  box-shadow: 10rpx 10rpx 10rpx rgb(0 0 0 / 18%);
}

/* #init {
  margin: 20px auto;
  color: blueviolet;
  font-size: 200px;
  font-weight: bold;
} */
.dis1 {
  background-color: rgb(244, 244, 249);
}

.space {
  height: 90px;
}

.space1 {
  display: flex;
  flex-direction: row;
}

.txt1 {
  margin-left: 14%;
  height: 659px;
  width: 358px;
}

.txt1-1 {
  height: 106px;
  font-size: 64px;
  font-family: GlowSansSC-ExtendedHeavy;
  font-weight: 900;
  line-height: 72px;
  color: #2e3a59;
  letter-spacing: 2.1px;
  margin-top: 175px;
  text-align: left;
}

.txt1-2 {
  font-size: 14px;
  color: #323c55;
  text-align: justify;
  line-height: 24px;
  margin-top: 60px;
  font-family: PingFang SC;
}

.video {
  height: 600px;
}

.video1 {
  margin-top: 130px;
  margin-left: 337px;
  position: relative;
  width: 450px;

}

.space2 {
  height: 119px;
}

.space2-1 {
  display: flex;
  flex-direction: row;
}

.txt2 {
  margin-left: 13%;
  height: 659px;
  width: 463px;
}

.txt2-1 {
  height: 106px;
  font-size: 64px;
  font-family: GlowSansSC-ExtendedHeavy;
  font-weight: 900;
  line-height: 72px;
  color: #2e3a59;
  letter-spacing: 2.1px;
  margin-top: 175px;
  text-align: left;
}

.txt2-2 {
  font-size: 14px;
  color: #323c55;
  text-align: justify;
  line-height: 24px;
  margin-top: 60px;
  font-family: PingFang SC;
}

.pic1 {
  height: 600px;
}

.pic1-1 {
  margin-top: 90px;
  margin-left: 113px;
  position: relative;
  width: 632px;

}

.pic2 {
  height: 600px;
}

.pic2-1 {
  margin-top: 90px;
  margin-left: 145px;
  position: relative;
  width: 678px;
  height: 358px;

}

.picx {
  height: 600px;
}

.picx-1 {
  margin-top: 120px;
  margin-left: 113px;
  position: relative;
  width: 650px;
  height: 350px;
}

.txt3 {
  margin-left: 14%;
  height: 659px;
  width: 450px;
}

.txt3-1 {
  height: 106px;
  font-size: 64px;
  font-family: GlowSansSC-ExtendedHeavy;
  font-weight: 900;
  line-height: 72px;
  color: #2e3a59;
  letter-spacing: 2.1px;
  margin-top: 131px;
  text-align: left;
}

.txt3-2 {
  font-size: 14px;
  color: #323c55;
  text-align: justify;
  line-height: 24px;
  margin-top: 121px;
  font-family: PingFang SC;
}

.dis2 {
  background-image: url(../assets/bg2.png);
}

.dis1 {
  background-image: url(../assets/bg2.png);
}

.dis3 {
  background-image: url(../assets/bg2.png);
  height: 460px;
  background-color: rgb(244, 244, 249);
  color: black
}

.txt4 {
  font-family: "楷体";
  font-size: 60px;
  font-weight: bold;
  padding-top: 50px;
}

.txt5 {
  font-size: 16px;
  /* font-weight: bold; */
  /* font-weight:; */
  color: #2e3a59;
  padding-top: 25px;
  padding-bottom: 20px;
  width: 400px;
  margin: 0 auto;
  line-height: 25px;
}

.txt6 {
  font-size: 40px;
  font-family: "楷体";
  /* font-weight:; */
  /* padding-top: 30px;
  padding-bottom: 40px; */
  font-weight: bold;
  width: 400px;
  margin: 0 auto;
}

.txt7 {
  font-size: 40px;
  font-weight: bold;
  font-family: "楷体";
  width: 400px;
  margin: 0 auto;
}

.login_button {
  float: right;
  font-size: 20px;
  border: 1px solid rgba(150, 169, 183, 0.413);
  border-radius: 30px;
  margin: 10px;
  padding: 10px;
  width: 80px;
}

.login_button:hover {
  background-color: rgb(150, 169, 183);
  color: white;
}

.el-backtop {
  color: #000000;
}
</style>