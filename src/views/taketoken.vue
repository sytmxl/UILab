<template>
  <h1>HELLO!</h1>
</template>

<style scoped>
.h1 {
  font-size: 18px;
}
</style>

<script>
import qs from 'qs';
export default {
  data() {
    return {
      str: this.$route.params.id
    }

  },
  methods: {
    init() {
      this.$axios({
        method: "post",
        url: "app/join_team_by_token",
        data: qs.stringify({
          // token: this.str.toString().split('localhost/')[1]
          token: this.str
        }),
      })
        .then((res) => {

          if (res.data.errno == 0) {
            this.$message.success("加入成功");
            window.location.href = "http://summer.mosymosy.cn/";
          }
          else if (res.data.errno == 1001) {
            this.$message.warning("请登录后重试");
            window.location.href = "http://summer.mosymosy.cn/";
          }
          else if (res.data.errno == 2006) {
            this.$message.success("你已经加入团队了，无须重复加入");
            window.location.href = "http://summer.mosymosy.cn/";
          }

        })
        .catch((err) => {
          
        });
    }
  },
  mounted() {
    this.init();
  }
}
</script>