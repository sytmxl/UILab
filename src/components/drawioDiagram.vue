<template>
  <div>
    <el-dialog :modal="false" :visible.sync="dialogVisible"
        title="修改图表信息"
        v-if="dialogVisible"
        width="40%"
        :before-close="closeDialog">
      <el-row>
        <el-col :span="4">
          标题：
        </el-col>
        <el-col :span="20">
          <el-input
              placeholder="请输入标题"
              maxlength="20"
              show-word-limit
              v-model="newHeader">
          </el-input>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="4">
          图注：
        </el-col>
        <el-col :span="20">
          <el-input
              placeholder="请输入图注"
              maxlength="20"
              show-word-limit
              v-model="newBrief">
          </el-input>
        </el-col>
      </el-row>
      <span slot="footer" class="dialog-footer">
        <el-button @click="closeDialog">取消</el-button>
        <el-button type="primary" @click="updateData">确定</el-button>
      </span>
    </el-dialog>
    <el-card class="diagram effect-3" :body-style="{ padding: '0px' }" style="text-align: center;width: 360px;"
             shadow="always">
      <div class="preview">
        <img v-if="base64src" style="cursor: pointer;" title="编辑" @click="edit"
             :src="this.base64src" id="graph" ref="graph">
        <i v-else class="el-icon-loading" style="margin-top: 35%"/>
      </div>
      <div class="member-info">
        <h3>{{ title }}</h3>
        <h5>{{ description }}</h5>
        <h5>最后编辑-{{ lastEditTime }}</h5>
        <div class="social-touch" v-if="isdel != true">
          <el-button type="info" icon="el-icon-edit" circle title="编辑" @click="edit"/>
          <el-button type="danger" icon="el-icon-delete" circle title="移动到回收站" @click="del"/>
          <el-button icon="el-icon-magic-stick" circle title="修改信息" @click="openDialog"/>
          <el-button icon="el-icon-share" circle title="分享" @click="openShare($event)"/>
        </div>
        <div class="social-touch" v-else>
          <el-button type="info" icon="el-icon-magic-stick" circle title="还原" @click="recover"/>
          <el-button type="danger" icon="el-icon-close" circle title="彻底删除" @click="foreverDel"/>
          <el-dialog v-if="foreverDeldialogVisible" :modal="false" title="您正试图进行不可逆操作" :visible.sync="foreverDeldialogVisible" width="30%"
            :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true">
            <span><i class="el-icon-warning" style="font-size:20px;color:#909399"></i>永久删除"{{this.$data.title}}"?</span>
            <span slot="footer" class="dialog-footer">
              <el-button @click="foreverDeldialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="foreverDeldialogVisible = false, foreverDelconfirm()">确 定
              </el-button>
            </span>
          </el-dialog>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script>
import drawio from "@/scripts/drawio";
import qs from "qs";

export default {
  name: "drawioDigram",
  props: {
    file_id:{default:0},
    graph_id: {default: 0},
    isdel: {default: false}
  },
  beforeMount() {
    this.getData();
  },

  methods: {
    exitEdit(){
      if(this.$data.diagramEditor){
        this.$data.diagramEditor.stopEditing();
      }
    },
    closeDialog() {
      this.$data.dialogVisible = false
    },
    openDialog() {
      this.$data.dialogVisible = true
    },
    async edit() {
      window.stopLoading();
      this.$data.diagramEditor = await drawio.DiagramEditor.editElement(
          this.$refs.graph, this.$data.configs,
          "kennedy",
          null,
          ['pv=0'],
          this.$props.graph_id);
      await this.$emit('startEdit');
      if (!localStorage.getItem('noTipsOnedit')) {
        this.welcomeNotify = this.$notify({
          iconClass: 'el-icon-guide',
          title: '欢迎！这里一些使用提示...',
          message: '如果您想查看或不再显示提示，请点击这里',
          position: 'bottom-right',
          duration: 5000,
          onClick: this.ifNoMoreTips,
        });
      }
    },
    ifNoMoreTips() {
      this.welcomeNotify.close();
      this.$confirm('您是否要看看这些提示？', '', {
        confirmButtonText: '看一看',
        cancelButtonText: '不再显示',
        iconClass: 'el-icon-question'
      }).then(() => {
        this.getTips();
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '在您的localstorage被清除前提示会被隐藏'
        });
      });
    },
    async getTips() {
      if (this.tipNo >= this.tips.length) {
        await this.$confirm('您看完了。以后还看吗？', '', {
          confirmButtonText: '下次仍显示提示',
          cancelButtonText: '不再显示提示',
          iconClass: 'el-icon-question'
        }).then(() => {
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '在您的localstorage被清除前提示会被隐藏'
          });
        });
      } else {
        await this.$notify({
          iconClass: this.tips[this.tipNo].icon,
          title: this.tips[this.tipNo].title,
          message: this.tips[this.tipNo].content,
          dangerouslyUseHTMLString: true,
          position: 'bottom-right',
          duration: 0,
          onClose: this.callNextTip,
        });
      }
    },
    async callNextTip() {
      this.tipNo = this.tipNo + 1;
      setTimeout(() => console.log(''), 1000);
      await this.getTips()
    },
    del() {
      this.$confirm('您可以去回收站找回它们', '您正试图删除\"' + this.$data.title + '\"', {
        confirmButtonText: '是的',
        cancelButtonText: '取消',
        type: 'info'
      }).then(() => {
        this.$axios({
          method: "post",
          url: "app/del_graph",
          data: qs.stringify({
            project_id: this.$data.project_id,
            graph_id: this.$props.graph_id
          }),
        }).then(res => {
          this.$emit('deled');
        });
        this.$message({
          type: 'info',
          message: '已将\"' + this.$data.title + '\"扔到回收站'
        });

      });

    },
    recover() {
      this.$axios({
        method: "post",
        url: "app/recover_graph",
        data: qs.stringify({
          graph_id: this.$props.graph_id
        }),
      }).then(res => {
        this.$emit('deled');
      });
      this.$message({
        type: 'info',
        message: '已恢复\"' + this.$data.title + '\"'
      });
    },
    foreverDel() {
      this.$data.foreverDeldialogVisible=true;
    },
    foreverDelconfirm()
    {
        this.$axios({
          method: "post",
          url: "app/full_del_graph",
          data: qs.stringify({
            project_id: this.$data.project_id,
            graph_id: this.$props.graph_id
          }),
        }).then(res => {
          this.$emit('deled');
        });
        this.$message({
          type: 'info',
          message: '已删除\"' + this.$data.title + '\"'
        });
        setTimeout(() => { location.reload(); }, 500);
    },
    getData() {
      this.$axios({
        method: "post",
        url: "app/get_graph",
        data: qs.stringify({
          graph_id: this.$props.graph_id
        }),
      }).then(res => {
        this.$data.title = this.$data.newHeader = res.data.data.header;
        this.$data.description = this.$data.newBrief = res.data.data.brief;
        this.$data.lastEditTime = res.data.data.lastedit;
        this.$data.base64src = res.data.data.data;
      })
    },
    updateData() {
      this.$axios({
        method: "post",
        url: "app/modify_graph",
        data: qs.stringify({
          graph_id: this.$props.graph_id,
          graph_name: this.$data.newHeader,
          graph_info: this.$data.newBrief
        }),
      }).then(res => {
        this.$emit('deled');
      });
      this.$message({
        message: '\"' + this.$data.newHeader + '\"已更新了信息',
        type: 'success'
      });
      this.$data.newHeader = this.$data.newBrief = null;
      this.$data.dialogVisible = false;
    },
    async openShare(event) {
      await this.$axios({
        method: "post",
        url: "app/generate_graph_token",
        data: qs.stringify({
          graph_id: this.$props.graph_id
        }),
      }).then(res => {
        this.$message.success("已将分享链接复制到剪切板,八小时有效")
        // navigator.clipboard.writeText(res.data.data.graph_link);
        
        this.copyToClipboard(res.data.data.graph_link);
      })
    },
    copyToClipboard(textToCopy) {
            // navigator clipboard 需要https等安全上下文
            if (navigator.clipboard && window.isSecureContext) {
                // navigator clipboard 向剪贴板写文本
                return navigator.clipboard.writeText(textToCopy);
            } else {
                // 创建text area
                let textArea = document.createElement("textarea");
                textArea.value = textToCopy;
                // 使text area不在viewport，同时设置不可见
                textArea.style.position = "absolute";
                textArea.style.opacity = 0;
                textArea.style.left = "-999999px";
                textArea.style.top = "-999999px";
                document.body.appendChild(textArea);
                textArea.focus();
                textArea.select();
                return new Promise((res, rej) => {
                    // 执行复制命令并移除文本框
                    document.execCommand('copy') ? res() : rej();
                    textArea.remove();
                });
            }
        }
  },
  data() {
    return {
      SharePicToken:'',
      diagramEditor:null,
      foreverDeldialogVisible:false,
      welcomeNotify: null,
      dialogVisible: false,
      newHeader: null,
      newBrief: null,
      project_id: sessionStorage.getItem("project"),
      title: "项目",
      description: "无简介",
      lastEditTime: "2077-01-01",
      base64src: '',
      configs: {
        "defaultLibraries": "uml;er;ios;basic;",
      },
      tipNo: 0,
      tips: [
        {title: '基本导航', content: '按住鼠标滚轮来拖动画布，滚动滚轮来缩放画面', icon: 'el-icon-thumb'},
        {
          title: '撤销与重做',
          content: '在顶部工具栏中你可以进行撤销与重做，当然ctrl-z与ctrl-y也可以使用',
          icon: 'el-icon-refresh-left\n'
        },
        {
          title: '调整画布',
          content: '您可以在右侧栏\'绘图\'中选择画布大小。当溢出时，画布会自动在溢出方向自动扩展并显示出虚线边界',
          icon: 'el-icon-full-screen'
        },
        {title: '快速样式', content: '您可以在右侧栏\'样式\'中快速设置选中元素的样式', icon: 'el-icon-help'},
        {
          title: '元素层级',
          content: '在顶部工具栏中可以将元素置于顶层/底层<img style="vertical-align:middle;" src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEkAAAAhCAYAAACV1IbrAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAAFiUAABYlAUlSJPAAAAIcSURBVGhD7Zg9iwJBDIZzfjQK1mqhCBZiZWcvKNj4FwQbQQRbKwsrW0EEG8G/YCMoWFtaiWAhImivYOPH3WWICOrt5vZmR07mgbCbMMXLu5nMsB/H4/ETNIY46KkxQJvEQJvEQJvEQJvEQJvEQJvEQJvEgH2ZXK/XsFgsKHvE6/VCMpmkzH5U6mGbNB6PYTAYQCgUosqN/X4Pp9MJarUaVexHpZ5fmTSbzaBcLlPlBtZHoxFUKhWq2I+Rnul0Cv1+X5pJUmZSLBaDUqlE2fthuZN6vR5st1vxbkQgEIB8Pk+ZPMw6qdvtQjAYpMoNK3osdxIatFqtTINjpB18f3xpevQVgMGvTNrtdqLFL5cLVV4L6sGtdR/L5ZJWyIFtksfjEccqnmLn85mqr+OqB08xDJxBnU5HxHA4pFVysPxnstFoiD1uRjgchmq1Spl92KlHyUzCIdpsNqFer8NkMqHqa9hsNkLHNTh6lJjkdDohnU6Dy+WCw+FAVXVkMhkoFosiCoUC5HI5EVw9SkxyOBwQj8fB5/NRRS2RSAQSicRDcPUoMem/Y9kkv98vhqBZ4DoV/KTH7XbTCutYPt1+Aoc0ziDcYve0Wi2x7VKpFFXsB4czzh/cXvdw9Ujfbu12G+bzOWXvgfROwi+Hp8azoYj3mGw2q7yT/qpHukl47zA6VqPR6NMfZXYhQ490k94RfQVgoE1ioE1ioE0yBeALHLQ7vesjJP8AAAAASUVORK5CYII=">，在元素的右键菜单中调整'
          ,
          icon: 'el-icon-copy-document'
        },
        {
          title: '多选组件',
          content: '按住左键可以框选组件（就像在电脑桌面上框选图标一样），按住ctrl单击也可以选择复数组件',
          icon: 'el-icon-menu'
        },
        {
          title: '创建自定义组件',
          content: '您可以选择一部分画布上的组件，将它们拖到便笺本中以备复用',
          icon: 'el-icon-collection'
        },
        {
          title: '保存文件',
          content: '您可以按右上角的\'保存并退出\'或者用ctrl-s来离开编辑。如果点击\'退出\'将舍弃更改',
          icon: 'el-icon-finished'
        },
        {title: '导出文件', content: '在顶栏的\'文件>导出为\'可以导出图片到本地', icon: 'el-icon-download'},
      ]
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

.button {
  padding: 0;
  float: right;
}

.image {
  width: 100%;
  display: block;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}

.clearfix:after {
  clear: both
}

.diagram {
  border-radius: 20px;
  width: 100%;
  height: auto;
  float: left;
  margin: 30px 2.5%;
  background-color: #ffffff;
  text-align: center;
  position: relative;
}

.preview img {
  max-width: 100%;
  vertical-align: middle;
  height: 100%;
}

h3 {
  font-size: 24px;
  font-weight: normal;
  margin: 10px 0 0;
  text-transform: uppercase;
}

h5 {
  font-size: 16px;
  font-weight: 300;
  margin: 0 0 15px;
  line-height: 22px;
}

p {
  font-size: 14px;
  font-weight: 300;
  line-height: 22px;
  padding: 0 30px;
  margin-bottom: 10px;
}

.social-touch a {
  display: inline-block;
  width: 27px;
  height: 26px;
  vertical-align: middle;
  margin: 0 2px;
  background-repeat: no-repeat;
  opacity: 0.7;
  transition: 0.3s;
}

.social-touch a:hover {
  opacity: 1;
  transition: 0.3s;
}

.effect-3 {
  max-height: 302px;
  min-height: 302px;
  overflow: hidden;
}

.effect-3 h3 {
  padding-top: 7px;
  line-height: 33px;
}

.effect-3 .preview {
  transition: 0.4s;
  height: 212px;
  width: 100%;
  display: inline-block;
  float: none;
  vertical-align: middle;
}

.effect-3 .member-info {
  transition: 0.4s;
}

.effect-3 .preview img {
  height: 100%;
  width: available;
  vertical-align: bottom;
}

.effect-3 .social-touch {
  float: left;
  left: 0;
  bottom: 0;
  overflow: hidden;
  padding: 5px 0;
  width: 100%;
  transition: 0.4s;
}

.effect-3:hover .preview {
  border-bottom: 0;
  border-radius: 0 0 50px 50px;
  height: 81px;
  display: inline-block;
  overflow: hidden;
  width: 109px;
  transition: 0.4s;
}
</style>
