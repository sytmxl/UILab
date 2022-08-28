<template>
  <el-container>
    <div class="main" v-if="teamname">

      <div class="add" @click="addprojectSet()">
        <i class="el-icon-plus" style="font-size:20px" @click="addprojectSet()" title="创建新项目"></i>
        <el-dialog v-if="addprojectDialogVisible" :modal="false" title="创建新项目" :visible.sync="addprojectDialogVisible"
          width="30%" :close-on-press-escape="false" :append-to-body="true" center>
          <el-form ref="addproject" :model="addproject" class="addproject" :hide-required-asterisk="true" @submit.native.prevent>
            <el-form-item prop="projectname" :rules="[
              { required: true, message: '请输入新的项目名', trigger: 'blur' },
            ]" label="请输入新的项目名" label-position="top">
              <el-input id='addproject' v-model="addproject.projectname" placeholder="请输入新的项目名进行创建" type="text"
                autocomplete="off" clearable prefix-icon="el-icon-bank-card" maxlength="20" show-word-limit
                style="width:90%">
              </el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button class="addproject" @click="addprojectDialogVisible = false, resetForm('addproject')">取 消
            </el-button>
            <el-button class="addproject" type="primary" @click="addprojectFunc(addproject.projectname)">确 认
            </el-button>
          </span>
        </el-dialog>
      </div>

      <!-- <div class="folder" @click="isopen = !isopen">
      <i class="el-icon-folder" style="font-size:20px"  title="文档中心"></i>
    </div> -->


      <!-- <div class="filefolder" v-if="isopen">
      <el-input prefix-icon="el-icon-search"
          v-model="filterText">
      </el-input>
      <el-tree
      :data="data"
      node-key="id"
      default-expand-all
      :expand-on-click-node="false"
      :filter-node-method="filterNode"
        :props="defaultProps"
        class="filter-tree"
        ref="tree"
        @node-drag-start="handleDragStart"
        @node-drag-enter="handleDragEnter"
        @node-drag-leave="handleDragLeave"
        @node-drag-over="handleDragOver"
        @node-drag-end="handleDragEnd"
        @node-drop="handleDrop"
        :allow-drop="allowDrop"
        :allow-drag="allowDrag">
      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button v-if="data.depth>=3"
            type="text"
            @click="() => append(data)">
            <i class='el-icon-circle-plus-outline'></i>
          </el-button>
          <el-button v-if="data.depth>=3"
            type="text"
            @click="() => remove(node, data)">
            <i class='el-icon-remove-outline'></i>
          </el-button>
        </span>
      </span>
    </el-tree>
    </div> -->

      <!-- <div class="recent" v-if="recent_list.length!=0">
      <h1 class="label label_top">近期项目</h1>
      <div class="content">
        <div v-for="i in 4" :key="i">
          <el-card class="box-card" shadow="hover" v-if="recent_list[recent_list.length-i]">
            <div id="tools">
              <i class="el-icon-delete" @click="deleteproject(recent_list[recent_list.length-i].project_id)"></i>
              <i class="el-icon-edit-outline" @click="information(recent_list[recent_list.length-i])"></i>
            </div>
              <h5>{{recent_list[recent_list.length-i].project_name}}<i class="el-icon-edit" style="font-size:20px" @click="changename(recent_list[recent_list.length-i].project_id)" title="重命名" ></i></h5>
              <p>创建时间：<br/>{{recent_list[recent_list.length-i].create_time}}</p>
              <p>最近修改时间：<br/>{{recent_list[recent_list.length-i].update_time}}</p>
          </el-card>   
        </div>
      </div>
    </div>

    <div class="recent" v-else>
      <h1 class="label">近期项目</h1>
      <div class="chooseteam">
        <el-empty description="你尚无项目，快去新建一个吧" :image-size="200">
        </el-empty>
      </div>
    </div> -->

      <div class="all" v-if="project_list.length != 0">
        <div class="all_head">
          <h1 class="label">全部项目&nbsp;
            <el-dropdown>
              <span class="el-dropdown-link">
                {{ sorttype }}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item @click.native="sort(1)">按创建时间升序<i class="el-icon-check"
                    v-if="sorttype == '按创建时间升序'"></i></el-dropdown-item>
                <el-dropdown-item @click.native="sort(2)">按创建时间降序<i class="el-icon-check"
                    v-if="sorttype == '按创建时间降序'"></i></el-dropdown-item>
                <el-dropdown-item @click.native="sort(3)">按修改时间升序<i class="el-icon-check"
                    v-if="sorttype == '按修改时间升序'"></i></el-dropdown-item>
                <el-dropdown-item @click.native="sort(4)">按修改时间降序<i class="el-icon-check"
                    v-if="sorttype == '按修改时间降序'"></i></el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>

            <el-input prefix-icon="el-icon-search" v-model="search" @keyup.enter.native="projectsearch()">
            </el-input>
          </h1>
        </div>

        <div v-for="item in project_list" :key="item">
          <el-card class="box-card" shadow="hover">
            <div id="tools">
              <i class="el-icon-delete" @click="deleteproject(item.project_id),deletedialogVisible=true" title="删除项目"></i>
              <i class="el-icon-edit-outline" @click="information(item)" title="编辑项目"></i>
              <i class="el-icon-document-copy" @click="copy(item)" title="复制项目"></i>
            </div>
            <el-dialog v-if="deletedialogVisible" :modal="false" title="提示" :visible.sync="deletedialogVisible" width="30%"  :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true">
              <span><i class="el-icon-warning" style="font-size:20px;color:#E6A23C"></i>此操作将将项目移至回收站, 是否继续?</span>
              <span slot="footer" class="dialog-footer">
                <el-button @click="deletedialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="deletedialogVisible = false,confirm()">确 定</el-button>
              </span>
            </el-dialog>
            <el-dialog v-if="changenameDialogVisible" :modal="false" title="修改项目名称"
              :visible.sync="changenameDialogVisible" width="30%"  :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true"
              center>
              <el-form @submit.native.prevent ref="changename" :model="changename" class="changename"
                :hide-required-asterisk="true">
                <el-form-item prop="name" :rules="[
                  { required: true, message: '请输入新的项目名', trigger: 'blur' },
                ]" label="请输入新的项目名" label-position="top">
                  <el-input id='changename' v-model="changename.name" placeholder="请输入新的项目名进行更改" type="text"
                    autocomplete="off" clearable prefix-icon="el-icon-bank-card" maxlength="20" show-word-limit
                    style="width:90%">
                  </el-input>
                </el-form-item>
              </el-form>
              <span slot="footer" class="dialog-footer">
                <el-button class="changename" @click="changenameDialogVisible = false, resetForm('changename')">取 消
                </el-button>
                <el-button class="changename" type="primary" @click="changenameFunc(changename.name)">确 认
                </el-button>
              </span>
            </el-dialog>
            <h5>{{ item.project_name }}<i class="el-icon-edit" style="font-size:20px" @click="changenameInfo(item)"
                title="重命名"></i></h5>
            <p>创建时间：<br />{{ item.create_time }}</p>
            <p>最近修改时间：<br />{{ item.update_time }}</p>
          </el-card>
        </div>
      </div>
      <div class="all" v-else>
        <div class="all_head">
          <h1 class="label">全部项目&nbsp;
            <el-dropdown>
              <span class="el-dropdown-link">
                {{ sorttype }}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item @click.native="sort(1)">按创建时间升序<i class="el-icon-check"
                    v-if="sorttype == '按创建时间升序'"></i></el-dropdown-item>
                <el-dropdown-item @click.native="sort(2)">按创建时间降序<i class="el-icon-check"
                    v-if="sorttype == '按创建时间降序'"></i></el-dropdown-item>
                <el-dropdown-item @click.native="sort(3)">按修改时间升序<i class="el-icon-check"
                    v-if="sorttype == '按修改时间升序'"></i></el-dropdown-item>
                <el-dropdown-item @click.native="sort(4)">按修改时间降序<i class="el-icon-check"
                    v-if="sorttype == '按修改时间降序'"></i></el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>

            <el-input prefix-icon="el-icon-search" v-model="search" @keyup.enter.native="projectsearch()">
            </el-input>
          </h1>
        </div>
        <div class="empty">
          <el-empty description="" :image-size="200"></el-empty>
        </div>

      </div>
    </div>
    <div class="chooseteam" v-else>
      <el-empty description="你还有没选择你的团队，快去选择一个吧" :image-size="200"></el-empty>
    </div>
  </el-container>

</template>

<script>
import qs from 'qs';
export default {
  inject: ["reload"],
  data() {
    return {
      deletedialogVisible:false,
      changenameDialogVisible: false,
      addprojectDialogVisible: false,
      teamname: JSON.parse(sessionStorage.getItem('team')).team_name,
      project_list: [],
      recent_list: [],
      isopen: false,
      filterText: '',
      sorttype: '',
      search: JSON.parse(sessionStorage.getItem('projectsearch')),
      changename: {
        name: "",
        projectid: ""
      },
      addproject: {
        projectname: ""
      },
      deleteId:"",
      data: [{
        id: 1,
        label: '文档中心',
        depth: 1,
        children: [{
          id: 2,
          label: '项目文档区',
          depth: 2,
          children: [{
            id: 4,
            label: '三级 3-1-1',
            depth: 3,
          }, {
            id: 5,
            label: '三级 3-1-2',
            depth: 3,
          }]
        }, {
          id: 3,
          label: '团队文件区',
          depth: 2,
          children: [{
            id: 6,
            label: '三级 3-2-1',
            depth: 3,
          }, {
            id: 7,
            label: '三级 3-2-2',
            depth: 3,
          }]
        }]
      }],
    }

  },
  methods: {
    // changename(id) {
    //   this.$prompt('请输入新的项目名称', '修改项目名称', {
    //     confirmButtonText: '确定',
    //     cancelButtonText: '取消',
    //     inputPattern: /^.{1,20}$/,
    //     inputErrorMessage: '项目名称长度不合格',
    //     inputPlaceholder: '不超过20字',
    //     isMouseDown: '',

    //   }).then(({ value }) => {
    //     this.$message({
    //       type: 'success',
    //       message: '修改成功'
    //     });
    //     this.$axios({
    //       method: "post",
    //       url: "app/rename_project",
    //       data: qs.stringify({
    //         project_id: id,
    //         project_name: value
    //       }),
    //     })
    //       .then((res) => {

    //         this.reload();

    //       })
    //       .catch((err) => {

    //       });
    //   }).catch(() => {

    //   });
    // },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    changenameFunc(val) {

      this.$axios({
        method: "post",
        url: "app/rename_project",
        data: qs.stringify({
          project_id: this.changename.projectid,
          project_name: val
        }),
      })
        .then((res) => {
          this.$message({
            type: 'success',
            message: '修改成功'
          });
          this.changenameDialogVisible = false;
          // this.resetForm("changename");
          setTimeout(() => {
            this.reload();
          }, 1000);


        })
        .catch((err) => {

        });
    },
    changenameInfo(id) {
      this.changename.name = id.project_name;
      this.changename.projectid = id.project_id;
      this.changenameDialogVisible = true;
    },
    addprojectSet() {
      this.addprojectDialogVisible = true;
    },
    addprojectFunc(val) {
      this.$axios({
        method: "post",
        url: "app/create_project",
        data: qs.stringify({
          team_id: JSON.parse(sessionStorage.getItem('team')).team_id,
          project_name: val
        }),
      })
        .then((res) => {
          this.addprojectDialogVisible = false;
          var project = { project_id: res.data.data.project_id, project_name: val };
          this.$store.dispatch("saveproject", project);
          this.$message({
            type: 'success',
            message: '创建项目成功，即将跳转至项目页面'
          });
          setTimeout(() => {
            location.href = "/project_outline";

          }, 1000);
          // this.resetForm("addproject");
        })
        .catch((err) => {

        });
    },
    deleteproject(id) {
      this.deleteId = id;
    },
    copy(item){
       this.$axios({
        method: "post",
        url: "/app/copy_project",
        data: qs.stringify({
          project_id:item.project_id,
        }),
      })
        .then((res) => {
          if(res.data.errno==0){
            this.$message.success(res.data.msg);
            this.reload();
          }
          else{
            console.log(res.data.errno);
            this.$message.warning(res.data.msg);
          }
           
        })
        .catch((err) => {
          
        });
    },
    confirm()
    {
        this.$axios({
          method: "post",
          url: "app/del_project",
          data: qs.stringify({
            project_id: this.deleteId,
          }),
        })
          .then((res) => {
            if (res.data.errno == 0) {
              this.$message.success(res.data.msg);
            }
            else {
              this.$message.warning(res.data.msg);
            }
            this.reload();

          })
          .catch((err) => {

          });
    },
    information(item) {
      var project = { project_id: item.project_id, project_name: item.project_name };
      this.$store.dispatch("saveproject", project);
      location.href = "project_outline";
    },
    init() {
      this.$axios({
        method: "post",
        url: "app/get_project_list",
        data: qs.stringify({
          team_id: JSON.parse(sessionStorage.getItem('team')).team_id,
          project_name: JSON.parse(sessionStorage.getItem('projectsearch')),
        }),
      })
        .then((res) => {

          let type = JSON.parse(sessionStorage.getItem('sorttype'));
          this.project_list = res.data.data.project_normal_list;
          this.recent_list = res.data.data.project_normal_list;

          if (type == 1) {
            this.project_list.sort((a, b) => { return a.create_time > b.create_time ? 1 : -1 });
            this.sorttype = '按创建时间升序';
          }
          else if (type == 2) {
            this.project_list.sort((a, b) => { return b.create_time > a.create_time ? 1 : -1 });
            this.sorttype = '按创建时间降序';
          }
          else if (type == 3) {
            this.project_list.sort((a, b) => { return a.update_time > b.update_time ? 1 : -1 });
            this.sorttype = '按修改时间升序';
          }
          else if (type == 4) {
            this.project_list.sort((a, b) => { return b.update_time > a.update_time ? 1 : -1 });
            this.sorttype = '按修改时间降序';
          }

        })
        .catch((err) => {

        });


    },
    append(data) {
      const newChild = { id: data.id + 1, label: 'testtest', children: [], depth: data.depth + 1 };
      if (!data.children) {
        this.$set(data, 'children', []);
      }
      data.children.push(newChild);
    },

    remove(node, data) {
      const parent = node.parent;
      const children = parent.data.children || parent.data;
      const index = children.findIndex(d => d.id === data.id);
      children.splice(index, 1);
    },
    filterNode(value, data) {
      if (!value) return true;
      return data.label.indexOf(value) !== -1;
    },
    async sort(sorttype) {
      this.$store.dispatch("savesorttype", sorttype);
      await this.init();
    },
    async projectsearch() {
      this.$store.dispatch("saveprojectsearch", this.search);
      await this.init();
    }

  },
  mounted() {
    this.init();
  },
  watch: {
    filterText(val) {
      this.$refs.tree.filter(val);
    },
  },
  destroyed() {
    sessionStorage.removeItem('projectsearch');
  }

}
</script>

<style scoped>
.filefolder {
  width: 300px;
  height: 600px;
  position: fixed;
  backdrop-filter: blur(25px) brightness(110%);
  background-color: #53667713 !important;
  border-radius: 25px;
  right: 130px;
  top: 100px;
}

.el-input {
  width: 200px;
  margin-left: 20px;
  margin-top: 20px;
  margin-bottom: 20px;
}

.el-input>>>.el-input__inner {
  border-radius: 25px;
  font-size: 15px;
}

.el-tree {
  background-color: rgb(242, 244, 245);
}

.chooseteam {
  position: absolute;
  left: 0;
  right: 0;
}

.el-icon-plus:hover {
  cursor: pointer;
}

.main {
  width: 100%;
}

.recent {
  width: 100%;
  text-align: left;
  font-size: 36px;
  height: 300px;
}

.all {
  width: 100%;
  text-align: left;
  font-size: 36px;
}

.all h1 {
  margin-top: 20px;
}

.all_head {
  width: 100%;
  height: 80px;
}

.el-dropdown-menu {
  border-radius: 15px;
}

.el-dropdown-menu>>>.el-dropdown-menu__item {
  font-size: 16px;
  color: #2878ff;

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

.bottom {
  margin-top: 100px;
}

.bottom .el-button {
  border-radius: 20px;
  margin-left: 85px;
}

.add,
.folder {
  width: 62px;
  border-radius: 20px;

  /* background: linear-gradient(270.6deg, #cbcddb06 -8.4%, rgba(150, 169, 183, 0.422) 100%); */
  background-color: rgb(206, 218, 226);
  font-size: 36px;
  color: black;
  text-align: center;

  overflow: hidden;
  transition: 0.2s;
  padding-bottom: 10px;
  float: right;
  left: 93%;
  position: fixed;
  z-index: 1;
  top: 100px;
}

.add:hover,
.folder:hover {
  width: 62px;
  border-radius: 50%;

  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 10px rgba(0, 0, 0, 0.04);
}

.box-card {
  width: 280px;
  height: 220px;
  margin: 15px;
  border-radius: 15px;
  text-align: left;
  padding: 0px;
  float: left;
  /* border-color: rgb(206, 218, 226) 2px; */
  transition: 0.4s;
}

/* .box-card:hover{
    width: 290px;
    height: 230px;
    margin: 10px;
  } */
/* 这个悬浮效果会有一点显示bug 暂且放着吧 */
.label {
  margin: 30px 0px 0px 30px !important;
  font-size: 50px;
  float: left;
  width: 100%;
  color: rgb(114, 132, 145);
  align-items: center;
  display: flex;
}

.label_top {
  margin: 10px 0px 0px 30px !important;
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
.el-icon-magic-stick,
.el-icon-document-copy {
  color: rgb(247, 239, 239);
  border-radius: 5px;
  padding: 0px;
  transition: 0.2s;
  width: 25%;
}

.el-icon-delete:hover,
.el-icon-edit-outline:hover,
.el-icon-document-delete:hover,
.el-icon-magic-stick:hover,
.el-icon-document-copy:hover {
  color: rgb(247, 239, 239);
  border-radius: 10px;
  width: 30%;
}

.el-icon-delete:hover {
  background-color: rgb(199, 113, 113);
}

.el-icon-edit-outline:hover {
  background-color: rgb(113, 142, 199);
}

.el-icon-document-copy:hover {
  background-color: rgb(113, 199, 130);
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
.el-icon-magic-stick,
.el-icon-document-copy {
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

.el-dropdown-link {
  background: rgba(186, 194, 209, 0.42);
  padding: 5px;
  border-radius: 4px;
  transition: 0.4s;
}

.el-dropdown-link:hover {
  background: rgba(186, 194, 209, 0.777);
}

.folder {
  margin-top: 80px;
}

.el-empty {
  width: 100%;
}
</style>