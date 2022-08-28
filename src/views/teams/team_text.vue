<template>
<el-container>
  <div class="main1">
    <div class="filecenter">
      <h1 class="label" > <span class="span2" @click="backfolder()"><i class="el-icon-arrow-left"></i>文档中心&nbsp;</span><span class="span1">&nbsp;&nbsp;/{{pathname}}</span></h1>
      <div v-if="this.files.length != 0" class="files">
        <div v-for="item in this.files" :key="item">
          <div class="folder item" @contextmenu.prevent="show1($event,item)" v-if="(item.file_type==1&&item.folder_status==0)||(item.file_type==3&&item.detail.project_status==0)" @click="intofolder(item)">
            <div v-if="item.file_type==3">
              <img class="folder-pic" src="../../assets/project_folder.svg">
              <h1 class="text">{{item.detail.project_name}}</h1>
            </div>
            <div v-else-if="item.file_type==1">
              <img class="folder-pic" src="../../assets/folder.svg">
              <h1 class="text">{{item.folder_name}}</h1>
            </div>
          </div>

          <div class="file item"  @contextmenu.prevent="show1($event,item)" v-else-if="item.file_type==2&&item.detail.doc_status==0" @click="intofolder(item)">
            <img class="file-pic" src="../../assets/file.svg">
            <h1 class="text">{{item.detail.doc_name}}</h1>
          </div>
        </div>
      </div>
      <div v-else>
        <el-empty :image-size="200"></el-empty>
      </div>
    </div>
    <div class="blank" @contextmenu.prevent="show($event)">
    </div>
  </div>

<el-dialog :modal="false" v-if="newfolderVisible"
  title="新建文件夹"
  :visible.sync="newfolderVisible"
  width="30%"
  :before-close="handleClose">
    <el-input
        placeholder="请输入新文件夹名称"
        v-model="newfoldername"
        maxlength="20"
        show-word-limit
        clearable>
    </el-input>
  <span slot="footer" class="dialog-footer">
    <el-button @click="newfolderVisible = false">取 消</el-button>
    <el-button type="primary" @click="newfolder()">确 定</el-button>
  </span>
</el-dialog>

<el-dialog :modal="false" v-if="newfileVisible"
  title="新建文件"
  :visible.sync="newfileVisible"
  width="30%"
  :before-close="handleClose">
    <el-input
        placeholder="请输入新文件名称"
        v-model="newfilename"
        maxlength="20"
        show-word-limit
        clearable>
    </el-input>
  <span slot="footer" class="dialog-footer">
    <el-button @click="newfileVisible = false">取 消</el-button>
    <el-button type="primary" @click="newfile()">确 定</el-button>
  </span>
</el-dialog>

<el-dialog :modal="false" v-if="renameVisible"
  title="重命名文件"
  :visible.sync="renameVisible"
  width="30%"
  :before-close="handleClose">
    <el-input
        placeholder="请输入新文件名称"
        v-model="renewname"
        maxlength="20"
        show-word-limit
        clearable>
    </el-input>
  <span slot="footer" class="dialog-footer">
    <el-button @click="renameVisible = false">取 消</el-button>
    <el-button type="primary" @click="rename(needrenameitem,renewname)">确 定</el-button>
  </span>
</el-dialog>
</el-container>
       

</template>

<style scoped>
.blank{
  width: calc(100vw - 200px);
  min-height: calc(100vh);
}
.main1 {
  width: calc(100vw - 200px);
  min-height: calc(100vh);
  margin-left: 0px !important;
}
.label i {
  font-size: 50px;
}
.label {
  margin: 30px 0px 0px 0px !important;
  font-size: 50px;
  float: left;
  width: 100%;
  color: rgb(114, 132, 145);
  align-items:baseline;
  display: flex;
}
.span2 {
  border-radius: 20px;
  transition: 0.3s;
}
.span2:hover{
  background: rgba(114, 132, 145, 0.165);
}
.label .span1 {
  font-size: 20px;
  color: rgba(114, 132, 145, 0.64);
  /* bottom: 0; */
}
.el-icon-top{
  font-size: 20px;
}
.el-icon-folder {
  font-size: 100px;
}
.el-icon-notebook-2{
  font-size: 100px;
}
.el-icon-document{
  font-size: 100px;
}
.folder{
  float: left;
  margin-top: 30px;
  margin-left: 50px;
}
.file{
  float: left;
  margin-top: 30px;
  margin-left: 50px;
}
.span2:hover,.folder:hover,.file:hover{
  cursor: pointer;
}

.folder-pic {
  width: 100px;
}
.file-pic {
  width: 100px;
}
.item {
  /* float: left; */
  width: 150px;
  height: 150px;
  background-color: rgba(255, 255, 255, 0.708);
  border-radius: 20px;
  transition: 0.4s;
  border: rgba(114, 132, 145, 0) 2px solid;
  overflow: hidden;
}
.item:hover {
  border: rgba(114, 132, 145, 0.304) 2px solid;
}
.el-empty {
  width: 100%;
}
.text {
  display: inline-block;
  white-space: nowrap; 
  width: 100%; 
  overflow: hidden;
  text-overflow:ellipsis;
  transition: 0.5s;
  border-radius: 20px;
}
.item:hover .text {
  transform: translateY(-80px);
  white-space: unset;
  text-overflow: unset;
  font-size: 23px;
  /* text-overflow:ellipsis; */
  /* color: rgb(230, 234, 239); */
  /* background: rgba(60, 65, 73, 0.719); */
  /* border-radius: 20px; */
}
img {
  transition: 0.4s;
  filter: blur(0px);
  opacity: 100%;
}
.item:hover img {
  filter: blur(25px);
  opacity: 60%;
  transition: 0.4s;
  /* width: 120px; */
  /* height: 120px; */
}
</style>

<script>
import Vue from 'vue';
import Contextmenu from "vue-contextmenujs";
import qs from "qs";
import {apikey} from "@/scripts/apikey";
Vue.use(Contextmenu);
export default {
  inject:['reload'],
    data(){
      return{
        files:[],
        this_id:JSON.parse(sessionStorage.getItem('folderid')).this_id,
        root_id:JSON.parse(sessionStorage.getItem('folderid')).root_id,
        newfolderVisible: false,
        newfileVisible: false,
        renameVisible:false,
        newfoldername:'',
        newfilename:'',
        renewname:'',
        needrenameitem:'',
        pathname:JSON.parse(sessionStorage.getItem('folderid')).path_name,
      }
    },
    methods:{
      async init(){
      if(JSON.parse(sessionStorage.getItem('folderid'))==null){
         this.getRootNode();
      }
      else{
        this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
      }
      },
      getfatherid(file_id,name){
         this.$axios({
        method: "post",
        url: "/app/get_file_parentid",
        data: qs.stringify({
          file_id: file_id,
        }),
      })
        .then((res) => {
          this.$store.dispatch('savefolderid',{root_id:this.root_id,last_id:res.data.data.parent_id,this_id:file_id,path_name:name});
           this.pathname = JSON.parse(sessionStorage.getItem('folderid')).path_name; 
        })
        .catch((err) => {
          
        });
      },
      async getRootNode(){
      this.$axios({
        method: "post",
        url: "/app/get_team_root_fileid",
        data: qs.stringify({
          team_id: JSON.parse(sessionStorage.getItem('team')).team_id,
        }),
      })
        .then((res) => {
          this.$store.dispatch('savefolderid',{root_id:res.data.data.file_id,last_id:res.data.data.file_id,this_id:res.data.data.file_id,path_name:'文档中心'});
          // getAllFile(res.data.data.file_id);
          location.reload();
        })
        .catch((err) => {
          
        });

      },
      async getAllFile(file_id){
         this.pathname = JSON.parse(sessionStorage.getItem('folderid')).path_name; 
      await this.$axios({
        method: "post",
        url: "/app/get_file_content",
        data: qs.stringify({
          file_id: file_id,
          allow_recycle: true
        }),
      })
        .then((res) => {
         this.files = res.data.data;
         this.root_id = JSON.parse(sessionStorage.getItem('folderid')).root_id;
        })
        .catch((err) => {
          
        });
      },
      async intofolder(item){
        if(item!=null){
          var last = item.parent_id;
         var now = item.file_id;
         console.log(now);
        }
        if(item.file_type==2){
          this.$store.dispatch('savedoc_tok',item.detail.doc_token);
          location.href="team_docedit";
        }
      if(item.file_type==3){
        this.$store.dispatch('savefolderid',{root_id:this.root_id,last_id:last,this_id:now,path_name:this.pathname+'/'+item.detail.project_name});
      }
      else if(item.file_type==1){
        this.$store.dispatch('savefolderid',{root_id:this.root_id,last_id:last,this_id:now,path_name:this.pathname+'/'+item.folder_name});
      }

        await this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
      },

      async backfolder(){
        var now = JSON.parse(sessionStorage.getItem('folderid')).last_id; 
        var n = this.pathname.lastIndexOf("/");
        var name = this.pathname.substr(0,n);
        if(this.pathname=='文档中心'){
          this.$message.warning("已经到了最顶部");
          console.log(this.pathname)
        }
        else{
            await this.getfatherid(now,name);
            await this.getAllFile(now);
        }
      
      },
       show(event) {
      this.$contextmenu({
        items: [
          {
            label: "新建",
            divided: true,
            minWidth: 0,
            children: [{label: "新建子文件夹"
            ,onClick:()=>{
              this.newfolderVisible=true;
            }}, {
              label: "新建子文件",
              onClick:()=>{
              this.newfileVisible=true
            }
            }]
          },{
            label: "粘贴",
            onClick:() =>{
              this.paste();
            }
          }
        ],
        event, // 鼠标事件信息
        customClass: "custom-class", // 自定义菜单 class
        zIndex: 3, // 菜单样式 z-index
        minWidth: 230 // 主菜单最小宽度
      });
      return false;
    },
        show1(event,item) {
      this.$contextmenu({
        items: [
          {label: "打开",
          onClick:() => this.intofolder(item)},
          {label: "剪切",
          onClick:() =>{
            this.cut(item);
          }},
          {label: "复制",
          onClick:() =>{
            this.copy(item);
          }},
          {label: "重命名",
          onClick:() => {
            setTimeout(() => {
                this.renameVisible = true;
            },500)
            this.needrenameitem = item;
            // this.renameVisible = true;
            console.log(this.needrenameitem)
          }},
          {
            label: "删除",
            onClick:() =>{
            this.delete(item);
          }
          },
        ],
        event, // 鼠标事件信息
        customClass: "custom-class", // 自定义菜单 class
        zIndex: 3, // 菜单样式 z-index
        minWidth: 230 // 主菜单最小宽度
      });
      return false;
    },

    newfile(){
      this.newfileVisible = false;
       this.$axios({
        method: "post",
        url: "/app/create_doc",
        data: qs.stringify({
          folder_id:JSON.parse(sessionStorage.getItem('folderid')).this_id,
          create_method: "folder_id",
          doc_name: this.newfilename
        }),
      })
        .then((res) => {
          if(res.data.errno==0){
            this.$message.success(res.data.msg);
            this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
          }
          else{
            this.$message.warning(res.data.msg);
          }
        })
        .catch((err) => {
          
        });
    },
    newfolder(){
      this.newfolderVisible = false;
      this.$axios({
        method: "post",
        url: "/app/create_folder",
        data: qs.stringify({
          folder_id: JSON.parse(sessionStorage.getItem('folderid')).this_id,
          new_folder_name:this.newfoldername
        }),
      })
        .then((res) => {
           if(res.data.errno==0){
            this.$message.success(res.data.msg);
            this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
          }
          else{
            this.$message.warning(res.data.msg);
          }
        })
        .catch((err) => {
          
        });
    },
    rename(item,newname){
      this.renameVisible = false;
      if(item.file_type==2){
        this.$axios({
        method: "post",
        url: "/app/rename_doc",
        data: qs.stringify({
          file_id: item.file_id,
          doc_name: newname
        }),
      })
        .then((res) => {
        if(res.data.errno==0){
            this.$message.success(res.data.msg);
            this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
          }
          else{
            this.$message.warning(res.data.msg);
          }
        })
        .catch((err) => {
          
        });
      }
      else if(item.file_type==1){
        this.$axios({
        method: "post",
        url: "/app/rename_folder",
        data: qs.stringify({
          file_id: item.file_id,
          folder_name:newname,
        }),
      })
        .then((res) => {
         this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
        })
        .catch((err) => {
          
        });
      }
      else if(item.file_type==3){
         this.$axios({
        method: "post",
        url: "/app/rename_project",
        data: qs.stringify({
          project_id: item.detail.project_id,
          project_name:newname,
        }),
      })
        .then((res) => {
         this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
        })
        .catch((err) => {
          
        });
      }
    },
    async copy(item){
      if(item.file_type==2){
        this.$store.dispatch('savecopy',{file_id:item.detail.doc_id,op:'copy'});
        // this.$message.success("复制成功");
      }
      else if(item.file_type==1){
        this.$store.dispatch('savecopy',{file_id:item.file_id,op:'copydir'}); //待定
        // this.$message.success("复制成功");
      }
      else if(item.file_type==3){
        await this.$store.dispatch('savecopy',{file_id:item.detail.project_id,op:'copypro'});
        // await this.$message.success("复制成功");
        await this.paste();
      }
    },
    cut(item){
      this.$store.dispatch('savecopy',{file_id:item.file_id,op:'cut'});
      this.$message.success("剪切成功");
    },
      paste() {
        var op = JSON.parse(sessionStorage.getItem('copy')).op;
        var dest = JSON.parse(sessionStorage.getItem('folderid')).this_id;
        if (op == 'copy') {
          this.$axios({
            method: "post",
            url: "/app/copy_doc",
            data: qs.stringify({
              folder_id: dest,
              doc_id: JSON.parse(sessionStorage.getItem('copy')).file_id
            }),
          })
              .then((res) => {
                if (res.data.errno == 0) {
                  this.$message.success(res.data.msg);
                  this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
                    let resData = res.data.data;
                    let i;
                  this.axios({
                    method: "post",
                    url: "api/1.2.15/copyPadWithoutHistory",
                    params: {
                      apikey: apikey,
                      sourceID: resData.doc_token ,
                      destinationID :resData.new_doc_token,
                      text: 'test'
                    }
                  })
                } else {
                  console.log(res.data.errno);
                  this.$message.warning(res.data.msg);
                }

              })
              .catch((err) => {

              });
        } else if (op == 'copydir') {
          this.$axios({
            method: "post",
            url: "/app/copy_folderfile",
            data: qs.stringify({
              folder_id: JSON.parse(sessionStorage.getItem('copy')).file_id,
              target_dirid: dest
            }),
          })
              .then((res) => {
                if (res.data.errno == 0) {
                  this.$message.success(res.data.msg);
                  this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
                    let resData = res.data.data;
                    let i;
                    for(i in resData){
                      this.axios({
                        method: "post",
                        url: "api/1.2.15/copyPadWithoutHistory",
                        params: {
                          apikey: apikey,
                          sourceID: resData[i].doc_token ,
                          destinationID :resData[i].new_doc_token,
                          text: 'test'
                        }
                      })
                    }

                } else {
                  console.log(res.data.errno);
                  this.$message.warning(res.data.msg);
                }

              })
              .catch((err) => {

              });
        } else if (op == 'copypro') {
          this.$axios({
            method: "post",
            url: "/app/copy_project",
            data: qs.stringify({
              project_id: JSON.parse(sessionStorage.getItem('copy')).file_id,
            }),
          })
              .then((res) => {
                if (res.data.errno == 0) {
                  this.$message.success(res.data.msg);
                  this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
                    let resData = res.data.data;
                    let i;
                    for(i in resData){
                      this.axios({
                        method: "post",
                        url: "api/1.2.15/copyPadWithoutHistory",
                        params: {
                          apikey: apikey,
                          sourceID: resData[i].doc_token ,
                          destinationID :resData[i].new_doc_token,
                          text: 'test'
                        }
                      })
                    }
                } else {
                  console.log(res.data.errno);
                  this.$message.warning(res.data.msg);
                }

              })
              .catch((err) => {

              });
        } else if (op == 'cut') {
          this.$axios({
            method: "post",
            url: "/app/move_file",
            data: qs.stringify({
              file_id: JSON.parse(sessionStorage.getItem('copy')).file_id,
              target_dirid: dest
            }),
          })
              .then((res) => {
                if (res.data.errno == 0) {
                  this.$message.success(res.data.msg);
                  this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
                } else {
                  this.$message.warning(res.data.msg);
                }
              })
              .catch((err) => {

              });
        }
      },
    delete(item){
      if(item.file_type==1){
        this.$axios({
        method: "post",
        url: "/app/del_folder",
        data: qs.stringify({
          folder_id:item.file_id,
        }),
      })
        .then((res) => {
          if(res.data.errno==0){
            this.$message.success(res.data.msg);
            this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
          }
          else{
            console.log(res.data.errno);
            this.$message.warning(res.data.msg);
          }
           
        })
        .catch((err) => {
          
        });
      }
      else if(item.file_type==2){
        this.$axios({
        method: "post",
        url: "/app/del_doc",
        data: qs.stringify({
          file_id:item.file_id,
        }),
      })
        .then((res) => {
          if(res.data.errno==0){
            this.$message.success(res.data.msg);
            this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
          }
          else{
            console.log(res.data.errno);
            this.$message.warning(res.data.msg);
          }
           
        })
        .catch((err) => {
        });
      }
      else if(item.file_type==3){
        this.$axios({
        method: "post",
        url: "/app/del_project",
        data: qs.stringify({
          project_id:item.detail.project_id,
        }),
      })
        .then((res) => {
          if(res.data.errno==0){
            this.$message.success(res.data.msg);
            this.getAllFile(JSON.parse(sessionStorage.getItem('folderid')).this_id);
          }
          else{
            console.log(res.data.errno);
            this.$message.warning(res.data.msg);
          }
           
        })
        .catch((err) => {
        });
      }
    }
    },
   async mounted(){
    this.init();
   },
   destroyed(){
    // sessionStorage.removeItem('folderid');
    sessionStorage.removeItem('copy');
   }
   
}
</script>