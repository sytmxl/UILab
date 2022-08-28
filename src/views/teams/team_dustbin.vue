<template>
  <el-container>
    <div class="main" v-if="teamname">

      <div class="all">
        <h1 class="label">回收站</h1>

        <div v-for="item in project_list" :key="item">
          <el-card class="box-card" shadow="hover">
            <div id="tools">
              <el-dialog v-if="deleteforeverdialogVisible"
              :modal="false" title="提示" :visible.sync="deleteforeverdialogVisible" width="30%"
                :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true">
                <span><i class="el-icon-warning" style="font-size:20px;color:#E6A23C"></i>此操作将永久删除该项目, 是否继续?</span>
                <span slot="footer" class="dialog-footer">
                  <el-button @click="deleteforeverdialogVisible = false">取 消</el-button>
                  <el-button type="primary" @click="deleteforeverdialogVisible = false, deleteforeverconfirm()">确 定
                  </el-button>
                </span>
              </el-dialog>
              <el-dialog v-if="restoredialogVisible" :modal="false" title="提示" :visible.sync="restoredialogVisible" width="30%"
                :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true">
                <span><i class="el-icon-warning" style="font-size:20px;color:#E6A23C"></i>此操作将恢复该项目, 是否继续?</span>
                <span slot="footer" class="dialog-footer">
                  <el-button @click="restoredialogVisible = false">取 消</el-button>
                  <el-button type="primary" @click="restoredialogVisible = false, restoreconfirm()">确 定</el-button>
                </span>
              </el-dialog>
              <i class="el-icon-document-delete"
                @click="deleteforever(item.project_id), deleteforeverdialogVisible = true"></i>
              <i class="el-icon-magic-stick" @click="restore(item.project_id), restoredialogVisible = true"></i>
            </div>
            <h5>{{ item.project_name }}</h5>
            <p>创建时间：<br />{{ item.create_time }}</p>
            <p>删除时间：<br />{{ item.update_time }}</p>


          </el-card>
        </div>
      </div>


    </div>
    <div class="chooseteam" v-else>
      <el-empty description="你还有没选择你的团队，快去选择一个吧" :image-size="200"></el-empty>
    </div>
  </el-container>
</template>

<style scoped>
.chooseteam {
  position: absolute;
  left: 0;
  right: 0;
}

.title {
  height: 150px;
  width: 100%;
  background: linear-gradient(270.6deg, #e3f6fd -8.4%, #f6faff 100%);
  font-size: 36px;
  color: black;
  text-align: left;
  line-height: 60px;
  border-radius: 10px;
}

.title h1 {
  margin-left: 50px;
  margin-top: 20px;
}

.main {
  width: 100%;
}

.all {
  width: 100%;
  text-align: left;
  font-size: 36px;
}

.all h1 {
  margin-top: 20px;
}

.content {
  margin-top: 20px;
}

.el-icon-edit {
  cursor: pointer;
  margin-left: 5px;
}

.box-card p {
  font-size: 18px;
  margin-top: 15px;
}

.box-card2 p {
  font-size: 18px;
  margin-top: 15px;
}

.bottom {
  margin-top: 100px;
}

.bottom .el-button {
  border-radius: 20px;
  margin-left: 85px;
}

.box-card {
  width: 280px;
  height: 220px;
  margin: 15px;
  border-radius: 15px;
  text-align: left;
  padding: 0px;
  float: left;
  border-color: rgb(206, 218, 226) 2px;
  transition: 0.4s;
}

/* .box-card:hover{
    width: 290px;
    height: 230px;
    margin: 10px;
  } */

.label {
  margin: 30px 0px 0px 30px !important;
  font-size: 50px;
  float: left;
  width: 100%;
  color: rgb(114, 132, 145);
}

#tools {
  background-color: rgb(157, 162, 176);
  color: rgba(0, 0, 0, 0);
  transition: 0.4s;
  height: 0px;
  padding: 0px;
  font-size: 0px;
  border-radius: 5px;
  text-align: center;
}

.box-card:hover #tools {
  height: 40px;
  color: rgb(0, 0, 0);
  font-size: 30px;
  margin-bottom: 20px;
}

.el-icon-delete,
.el-icon-edit-outline,
.el-icon-document-delete,
.el-icon-magic-stick {
  color: rgb(247, 239, 239);
  border-radius: 5px;
  padding: 0px;
  transition: 0.2s;
  width: 30%;
}

.el-icon-delete:hover,
.el-icon-edit-outline:hover,
.el-icon-document-delete:hover,
.el-icon-magic-stick:hover {
  color: rgb(247, 239, 239);
  border-radius: 10px;
  width: 40%;
}

.el-icon-delete:hover {
  background-color: rgb(199, 113, 113);
}

.el-icon-edit-outline:hover {
  background-color: rgb(113, 142, 199);
}

.el-icon-document-delete:hover {
  background-color: rgb(199, 113, 113);
}

.el-icon-magic-stick:hover {
  background-color: rgb(113, 199, 130);
}

.box-card:hover .el-icon-delete,
.el-icon-edit-outline,
.el-icon-document-delete,
.el-icon-magic-stick {
  /* background-color: rgb(199, 113, 113); */
  padding: 5px;
}

.box-card h5 {
  display: inline-block;
  white-space: nowrap;
  width: 100%;
  overflow: hidden;
  text-overflow: ellipsis;
  transition: 0.4s;
}

.box-card:hover h5 {
  white-space: unset;
  text-overflow: unset;
  font-size: 25px;
}
</style>

<script>
import qs from 'qs';
export default {
  data() {
    return {
      teamname: JSON.parse(sessionStorage.getItem('team')).team_name,
      project_list: [],
      deleteforever_project_id: "",
      restore_project_id: "",
      deleteforeverdialogVisible: false,
      restoredialogVisible: false,
    }
  },
  methods: {
    init() {
      this.$axios({
        method: "post",
        // headers: { "authorization": JSON.parse(sessionStorage.getItem('token')) },
        url: "app/get_project_list",
        data: qs.stringify({
          team_id: JSON.parse(sessionStorage.getItem('team')).team_id,
        }),
      })
        .then((res) => {
          this.project_list = res.data.data.project_recycle_list;
        })
        .catch((err) => {

        });
    },
    restore(id) {
      this.restore_project_id = id;
    },
    restoreconfirm() {
      this.$axios({
        method: "post",
        url: "app/recycle_project",
        data: qs.stringify({
          project_id: this.restore_project_id,
        }),
      })
        .then((res) => {
          this.$message.success("恢复成功");
          setTimeout(() => { location.reload(); }, 500)
        })
        .catch((err) => {

        });
    },
    deleteforever(id) {
      this.deleteforever_project_id = id;
    },
    deleteforeverconfirm() {
      this.$axios({
        method: "post",
        url: "app/permanent_del_project",
        data: qs.stringify({
          project_id: this.deleteforever_project_id,
        }),
      })
        .then((res) => {
          this.$message.success("已经彻底删除");
          setTimeout(() => { location.reload(); }, 500)
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