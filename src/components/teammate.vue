<template>
<div>
  <div class="bar3 bar" v-if="identity == 3">
    <div class="left">
      <img v-if="!imgurl" src="../assets/bk3.jpg" alt="" />
      <img v-else :src="imgurl" alt="" />
      <p class="name">{{ user_name }}</p>
    </div>
    <div class="right">
      <!-- <div  id="tools">
        <i class="el-icon-user"></i>
      </div>     -->
      <p class="id">团队创始人</p>
      <p class="realname">真实姓名：{{ realname }}</p>
      <p class="email">邮箱：{{ email }}</p>
    </div>
  </div>

  <div class="bar2 bar" v-else-if="identity == 2">
    <div class="left">
      <img v-if="!imgurl" src="../assets/bk3.jpg" alt="" />
      <img v-else :src="imgurl" alt="" />
      <p class="name">{{ user_name }}</p>
    </div>
    <div class="right">
      <div id="tools">
        <!-- <i class="el-icon-user" ></i> -->
        <i class="el-icon-minus" @click="cancelmanager()"></i>
        <i class="el-icon-delete" @click="deletemember()"></i>
      </div>
      <p class="id">管理员</p>
      <p class="realname">真实姓名：{{ realname }}</p>
      <p class="email">邮箱：{{ email }}</p>
    </div>
  </div>

  <div class="bar1 bar" v-else>
    <div class="left">
      <img v-if="!imgurl" src="../assets/bk3.jpg" alt="" />
      <img v-else :src="imgurl" alt="" />
      <p class="name">{{ user_name }}</p>
    </div>

    <div class="right">
      <div id="tools">
        <!-- <i class="el-icon-user" ></i> -->
        <i class="el-icon-plus" @click="tobemanager()"></i>
        <i class="el-icon-delete" @click="deletemember()"></i>
      </div>
      <p class="id">成员</p>
      <p class="realname">真实姓名：{{ realname }}</p>
      <p class="email">邮箱：{{ email }}</p>
    </div>
  </div>
  <el-dialog v-if="cancelmanagerdialogVisible" :modal="false" title="提示" :visible.sync="cancelmanagerdialogVisible" width="30%"
      :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true">
      <span><i class="el-icon-warning" style="font-size:20px;color:#E6A23C"></i>要取消该用户管理员身份, 是否继续?</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="cancelmanagerdialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="cancelmanagerdialogVisible = false, cancelmanagerconfirm()">确 定
        </el-button>
      </span>
    </el-dialog>
    <el-dialog v-if="deletememberdialogVisible" :modal="false" title="提示" :visible.sync="deletememberdialogVisible" width="30%"
      :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true">
      <span><i class="el-icon-warning" style="font-size:20px;color:#E6A23C"></i>从团队删除此用户, 是否继续?</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="deletememberdialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="deletememberdialogVisible = false, deletememberconfirm()">确 定
        </el-button>
      </span>
    </el-dialog>
    <el-dialog v-if="tobemanagerdialogVisible" :modal="false" title="提示" :visible.sync="tobemanagerdialogVisible" width="30%"
      :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true">
      <span><i class="el-icon-warning" style="font-size:20px;color:#E6A23C"></i>将其设为管理员, 是否继续?</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="tobemanagerdialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="tobemanagerdialogVisible = false, tobemanagerconfirm()">确 定
        </el-button>
      </span>
    </el-dialog>
</div>
</template>

<script>
import qs from 'qs';
export default {
  name: "teammate",
  props: {
    user_name: { default: "", type: String },
    identity: { default: 0 },
    email: { default: "", type: String },
    realname: { default: "", type: String },
    user_id: '',
    imgurl: { default: "", type: String },
  },
  data() {
    return {
      cancelmanagerdialogVisible: false,
      tobemanagerdialogVisible: false,
      deletememberdialogVisible: false,
    }
  },
  methods: {
    cancelmanager() {
      this.cancelmanagerdialogVisible = true;
    },
    cancelmanagerconfirm() {
      this.$axios({
        method: "post",
        url: "app/change_team_member_identitys",
        data: qs.stringify({
          team_id: JSON.parse(sessionStorage.getItem('team')).team_id,
          user_id_to_change: this.user_id,
          identitys: "member",
        }),
      })
        .then((res) => {
          if (res.data.errno == 0) {
            this.$message.success(res.data.msg);
            setTimeout(() => { location.reload(); }, 500)
          }
          else {
            this.$message.warning(res.data.msg);
          }
        })
        .catch((err) => {

        });
    },
    tobemanager() {
      this.tobemanagerdialogVisible = true;
    },
    tobemanagerconfirm() {
      this.$axios({
        method: "post",
        url: "app/change_team_member_identitys",
        data: qs.stringify({
          team_id: JSON.parse(sessionStorage.getItem('team')).team_id,
          user_id_to_change: this.user_id,
          identitys: "admin",
        }),
      })
        .then((res) => {
          if (res.data.errno == 0) {
            this.$message.success(res.data.msg);
            setTimeout(() => { location.reload(); }, 500)
          }
          else {
            this.$message.warning(res.data.msg);
          }
        })
        .catch((err) => {

        });
    },
    deletemember() {
      this.deletememberdialogVisible = true;
    },
    deletememberconfirm() {
      this.$axios({
        method: "post",
        url: "app/del_team_member",
        data: qs.stringify({
          team_id: JSON.parse(sessionStorage.getItem('team')).team_id,
          user_id_to_del: this.user_id,
        }),
      })
        .then((res) => {
          if (res.data.errno == 0) {
            this.$message.success(res.data.msg);
            setTimeout(() => { location.reload(); }, 500)
          }
          else {
            this.$message.warning(res.data.msg);
          }
        })
        .catch((err) => {

        });
    }
  }
}
</script>

<style scoped>
.bar {
  right: 0;
  border: 1px solid rgba(206, 218, 226, 0.536);
  border-radius: 15px;
  margin: 20px;
  padding: 10px;
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
  background-color: white;
}

.bar2 {
  background-color: rgba(206, 218, 226, 0.29);
}

.bar3 {
  background-color: rgba(95, 123, 143, 0.184);
  border: 1px solid rgb(85, 103, 116);
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

.name,
.id,
.realname,
.email {
  display: inline-block;
  white-space: nowrap;
  /* width: 80px;  */
  overflow: hidden;
  text-overflow: ellipsis;
}

.name {
  width: 80px;
}

.id {
  width: 120px;
}

.realname {
  width: 200px;
}

.email {
  width: 300px;
}

#tools {
  background-color: rgba(95, 123, 143, 0.304);
  /* color: rgb(95, 123, 143) !important; */
  transition: 0.3s;
  height: 0px;
  padding: 0px;
  font-size: 0px;
  border-radius: 5px;
  text-align: center;
}

.bar3 #tools {
  background-color: rgba(253, 253, 253, 0.304);
}

.bar:hover #tools {
  height: 40px;
  /* color: rgb(0, 0, 0); */
  font-size: 30px;
  margin-bottom: 20px;
}

.el-icon-delete,
.el-icon-user,
.el-icon-minus,
.el-icon-plus {
  color: rgb(95, 123, 143);
  border-radius: 5px;
  padding: 0px;
  transition: 0.2s;
  width: 30%;
}

.el-icon-delete:hover,
.el-icon-user:hover,
.el-icon-minus:hover,
.el-icon-plus:hover {
  color: rgb(247, 239, 239);
  border-radius: 10px;
  width: 33%;
}

.el-icon-delete:hover {
  background-color: rgb(36, 32, 32);
}

.el-icon-user:hover {
  background-color: rgb(113, 142, 199);
}

.el-icon-minus:hover {
  background-color: rgb(199, 113, 113);
}

.el-icon-plus:hover {
  background-color: rgb(113, 199, 130);
}

.bar:hover .el-icon-delete,
.el-icon-user,
.el-icon-minus,
.el-icon-plus {
  /* background-color: rgb(199, 113, 113); */
  padding: 5px;
}
</style>

