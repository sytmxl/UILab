<template>
  <div>
    <el-dialog :modal="false" title="重命名文档" :visible.sync="dialogVisible" width="50%" :before-close="closeDialog">
      <span>
        <el-row>
          <el-col :span="4">
            文档标题：
          </el-col>
          <el-col :span="20">
            <el-input placeholder="请输入标题" maxlength="20" show-word-limit v-model="newTitle">
            </el-input>
          </el-col>
        </el-row>
      </span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="closeDialog">取消</el-button>
        <el-button type="primary" @click="modify">重命名</el-button>
      </span>
    </el-dialog>
    <div class="toolbar" >
      <!-- <el-card :body-style="{ padding: '0px' }" style="width: 85%;margin: 5% auto;height: 200px;cursor: pointer;"
        shadow="hover"> -->
        <!-- <div id="tools" v-if="inRecycle == false">
          <i class="el-icon-delete" @click="del" />
          <el-dialog v-if="deldialogVisible" :modal="false" title="您可以去回收站找回它们" :visible.sync="deldialogVisible" width="30%"
            :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true">
            <span><i class="el-icon-warning" style="font-size:20px;color:#909399"></i>您正试图删除"{{this.$data.title}}"</span>
            <span slot="footer" class="dialog-footer">
              <el-button @click="deldialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="deldialogVisible = false, delconfirm()">确 定
              </el-button>
            </span>
          </el-dialog>
          <i><img style="width: 25%" /></i>
          <i class="el-icon-edit-outline" @click="openDialog" />
        </div>
        <div id="tools" v-if="inRecycle == true">
          <i class="el-icon-document-delete" @click="foreverDel" />
          <el-dialog v-if="foreverDeldialogVisible" :modal="false" title="您正试图进行不可逆操作" :visible.sync="foreverDeldialogVisible" width="30%"
            :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true">
            <span><i class="el-icon-warning" style="font-size:20px;color:#909399"></i>永久删除"{{this.$data.title}}"?</span>
            <span slot="footer" class="dialog-footer">
              <el-button @click="foreverDeldialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="foreverDeldialogVisible = false, foreverDel()">确 定
              </el-button>
            </span>
          </el-dialog>
          <i><img style="width: 25%" /></i>
          <i class="el-icon-magic-stick" @click="recover" />
        </div> -->
        
      <!-- </el-card> -->
      <div id="incard" @click="edit">
        <p v-if="preview != ''">{{ preview }}</p>
        <p v-else>该文档为空<br />您可以点击这里来编辑文档</p>
      </div>
      <div style="padding: 14px;">
        <slot></slot>
        <div class="bottom clearfix">
          <time class="time">{{ this.$props.last_edit_time }}</time>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import qs from "qs";
import { apikey } from "@/scripts/apikey";

export default {
  name: "etherpadFile",
  props: {
    id: { default: 0 },
    token: { default: "", type: String },
    inRecycle: { default: false },
    title: { default: "示例项目", type: String },
    description: { default: "无简介", type: String },
    last_edit_time: { default: "2022-08-01", type: String },
  },
  mounted() {
    this.getProps();
  },
  methods: {
    getProps() {
      this.axios({
        method: "post",
        url: "api/1/getText",
        params: {
          apikey: apikey,
          padID: this.$props.token,
        }
      }).then(res => {
        this.$data.preview = res.data.data.text;
        if (this.$data.preview.length > 50) { this.$data.preview = this.$data.preview.substring(0, 50) + "..." }
      })
    },
    closeDialog() {
      this.$data.dialogVisible = false;
    },
    openDialog() {
      this.$data.dialogVisible = true;
    },
    edit() {
      this.$emit('start_edit', this.$props.token);
    },
    // del() {
    //   this.$confirm('您可以去回收站找回它们', '您正试图删除\"' + this.$data.title + '\"', {
    //     confirmButtonText: '是的',
    //     cancelButtonText: '取消',
    //     type: 'info'
    //   }).then(() => {
    //     this.$axios({
    //       method: "post",
    //       url: "app/del_doc",
    //       data: qs.stringify({
    //         doc_id: this.$props.id
    //       }),
    //     }).then(res => { this.$emit('deled'); }); //?
    //     this.$message({
    //       type: 'info',
    //       message: '已将\"' + this.$data.title + '\"扔到回收站'
    //     });
    //   })
    // },
    del() {
      this.$data.deldialogVisible=true;
    },
    delconfirm()
    {
        this.$axios({
          method: "post",
          url: "app/del_doc",
          data: qs.stringify({
            doc_id: this.$props.id
          }),
        }).then(res => { this.$emit('deled'); }); //?
        this.$message({
          type: 'info',
          message: '已将\"' + this.$data.title + '\"扔到回收站'
        });
        setTimeout(() => { location.reload(); }, 500);
    },
    modify() {
      this.$axios({
        method: "post",
        url: "app/rename_doc",
        data: qs.stringify({
          doc_id: this.$props.id,
          doc_name: this.$data.newTitle
        }),
      }).then(res => { this.$emit('deled'); });
      this.$message({
        message: '已经重命名文档为\"' + this.$data.newTitle + '\"',
        type: 'success'
      });
      this.$data.title = this.$data.newTitle;
      this.$data.newTitle = null;
      this.$data.dialogVisible = false;
      setTimeout(() => { location.reload(); }, 500);
    },
    foreverDel() {
      this.$data.foreverDeldialogVisible=true;
    },
    foreverDelconfirm()
    {
      this.$axios({
          method: "post",
          url: "app/permanent_del_doc",
          data: qs.stringify({
            doc_id: this.$props.id
          }),
        });
        this.$emit('deled');
        this.$message({
          type: 'info',
          message: '已删除\"' + this.$data.title + '\"'
        });
        setTimeout(() => { location.reload(); }, 500);
    },
    recover() {
      this.$axios({
        method: "post",
        url: "app/recycle_doc",
        data: qs.stringify({
          doc_id: this.$props.id
        }),
      });
      this.$emit('deled');
      this.$message({
        type: 'info',
        message: '已恢复\"' + this.$data.title + '\"'
      });
    },
    getShareLink() {
      //TODO 获取只读的分享链接
    },
    catUrl(token) {
      //TODO 在这里根据EtherPad服务的baseUrl拼出应该访问的文档
    },
  },
  data() {
    return {
      id: this.$props.id,
      title: this.$props.title,
      dialogVisible: false,
      deldialogVisible:false,
      foreverDeldialogVisible:false,
      newTitle: '',
      preview: '',
    }
  }
}
</script>

<style scoped>
.time {
  font-size: 13px;
  color: #999;
}

.bottom {
  margin-top: 13px;
  line-height: 12px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}

.clearfix:after {
  clear: both
}
#toolsRecycle {
  background-color: rgb(157, 162, 176);
  color: rgba(0, 0, 0, 0);
  transition: 0.4s;
  height: 0px;
  padding: 0px;
  font-size: 0px;
}

#tools {
  background-color: rgb(157, 162, 176);
  color: rgba(0, 0, 0, 0);
  transition: 0.4s;
  height: 0px;
  padding: 0px;
  font-size: 0px;
}
/* 过期动画 */
/* .toolbar:hover #tools {
  height: 40px;
  color: rgb(0, 0, 0);
  font-size: 30px;
} */

.el-icon-delete,
.el-icon-edit-outline,
.el-icon-document-delete,
.el-icon-magic-stick {
  color: rgb(247, 239, 239);
  border-radius: 5px;
  padding: 0px;
  transition: 0.2s;
  width: 10%;
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

.toolbar:hover .el-icon-delete,
.el-icon-edit-outline,
.el-icon-document-delete,
.el-icon-magic-stick {
  /* background-color: rgb(199, 113, 113); */
  padding: 5px;
}
#incard {
  /* padding-top: 15%; */
  background-color: rgb(238, 238, 238);
  height: 200px;
  /* width: 360px !important; */
  padding: 20px;
  text-align: left;
  font-size: 20px;
  color: rgb(114, 132, 145); 
  transition: 0.3s;
}
#incard:hover{
  /* height: 500px; */
  background-color: rgba(220, 221, 225, 0.82);
  cursor: pointer;
}
#incard:hover .toolbar{
  border: rgb(114, 132, 145) 1px solid;
}
.toolbar {
  overflow: hidden;
  border-radius: 20px;
  text-align: center;
  padding: 0px !important;
  margin-bottom: 10px;
  width: 350px;
  transition: 0.3s;
  border: rgb(238, 238, 238) 1px solid;
}
</style>
