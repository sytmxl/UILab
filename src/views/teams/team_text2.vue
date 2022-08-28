<template>
  <el-container>
    <div class="main1">
      <div class="cyclecenter">
        <!-- <h1 class="label"> <i class="el-icon-top" @click="backfolder2()">{{pathname}}</i></h1> -->
        <h1 class="label" > 回收中心</h1>
        <div v-if="this.files!= '' " class="files">
          <div v-for="item in this.files" :key="item">
            <div class="folder item"  @contextmenu.prevent="show2($event,item)" v-if="(item.file_type==1)||(item.file_type==3)">
              <div v-if="item.file_type==3">
                <img class="folder-pic" src="../../assets/project_folder.svg">
                <h1 class="text">{{item.detail.project_name}}</h1>
              </div>
              <div v-else-if="item.file_type==1">
                <img class="folder-pic" src="../../assets/folder.svg">
                <h1 class="text">{{item.folder_name}}</h1>
              </div>
            </div>

            <div class="file item"  @contextmenu.prevent="show2($event,item)" v-else-if="item.file_type==2">
              <img class="file-pic" src="../../assets/file.svg">
              <h1 class="text">{{item.detail.doc_name}}</h1>
            </div>
          </div>
        </div>
        <div v-else>
          <el-empty :image-size="200"></el-empty>
        </div>
      </div>
    </div>
    
 <el-dialog :modal="false" v-if="restoreVisible"
  title="恢复文件"
  :visible.sync="restoreVisible"
  width="30%"
  :before-close="handleClose">
    <span>确定要恢复此文件</span>
  <span slot="footer" class="dialog-footer">
    <el-button @click="restoreVisible = false">取 消</el-button>
    <el-button type="primary" @click="restore()">确 定</el-button>
  </span>
</el-dialog>

 <el-dialog :modal="false" v-if="deleteVisible"
  title="彻底删除文件"
  :visible.sync="deleteVisible"
  width="20%"
  :before-close="handleClose">
    <span>确定要彻底删除此文件</span>
  <span slot="footer" class="dialog-footer">
    <el-button @click="deleteVisible = false">取 消</el-button>
    <el-button type="primary" @click="Delete()">确 定</el-button>
  </span>
</el-dialog>

  </el-container>
       

</template>

<style scoped>
.main1 {
  width: calc(100vw - 200px);
  min-height: calc(100vh);
  margin-left: 0px !important;
}
.label i {
  font-size: 50px;
}
.label {
  margin: 30px 0px 0px 30px !important;
  font-size: 50px;
  float: left;
  width: 100%;
  color: rgb(114, 132, 145);
  align-items:center;
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
.el-icon-document{
  font-size: 100px;
}
.el-icon-notebook-2{
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
  border: rgba(114, 132, 145, 0.64) 2px solid;
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
Vue.use(Contextmenu);
export default {
  inject:['reload'],
    data(){
      return{
        files:[],
        this_id:'',
        root_id:'',
        needrestore:'',
        needdelete:'',
        restoreVisible: false,
        deleteVisible:false,
        pathname:JSON.parse(sessionStorage.getItem('delfolderid')).path_name,
      }
    },
    methods:{
      async init(){
      if(JSON.parse(sessionStorage.getItem('delfolderid'))==null){
        this.getRootNode();
      }
      else{
        this.getAllFile2(JSON.parse(sessionStorage.getItem('delfolderid')).this_id);
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
          this.$store.dispatch('savedelfolderid',{root_id:this.root_id,last_id:res.data.data.parent_id,this_id:file_id,path_name:name});
           this.pathname = JSON.parse(sessionStorage.getItem('delfolderid')).path_name; 
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
          this.$store.dispatch('savedelfolderid',{root_id:res.data.data.file_id,last_id:res.data.data.file_id,this_id:res.data.data.file_id,path_name:'回收中心'});
          //  this.getAllFile(res.data.data.file_id);
          location.reload();
        })
        .catch((err) => {
          
        });

      },
      async getAllFile(file_id){
         this.pathname = JSON.parse(sessionStorage.getItem('delfolderid')).path_name; 
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
        })
        .catch((err) => {
          
        });
      },

      async getAllFile2(file_id){
         this.pathname = JSON.parse(sessionStorage.getItem('delfolderid')).path_name; 
      await this.$axios({
        method: "post",
        url: "/app/get_all_recycle_files",
        data: qs.stringify({
          file_id: file_id,
        }),
      })
        .then((res) => {
         this.files = res.data.data;
        })
        .catch((err) => {
          
        });
      },
      async intofolder2(item){
        if(item!=null){
          var last = item.parent_id;
         var now = item.file_id;
         console.log(now);
        }
      if(item.file_type==3){
        this.$store.dispatch('savedelfolderid',{root_id:this.root_id,last_id:last,this_id:now,path_name:this.pathname+'/'+item.detail.project_name});
      }
      else if(item.file_type==1){
        this.$store.dispatch('savedelfolderid',{root_id:this.root_id,last_id:last,this_id:now,path_name:this.pathname+'/'+item.folder_name});
      }

        await this.getAllFile2(JSON.parse(sessionStorage.getItem('delfolderid')).this_id);
      },

      async backfolder2(){
        var now = JSON.parse(sessionStorage.getItem('delfolderid')).last_id; 
        var n = this.pathname.lastIndexOf("/");
        var name = this.pathname.substr(0,n);
        if(this.pathname=='文档中心'){
          this.$message.warning("已经到了最顶部");
          console.log(this.pathname)
        }
        else{
            await this.getfatherid(now,name);
            await this.getAllFile2(now);
        }
      
      },
        restore(){
            this.restoreVisible = false;
            if(this.needrestore.file_type==1){
                

                this.$axios({
                  method: "post",
                  url: "/app/recycle_folder",
                  data: qs.stringify({
                    folder_id: this.needrestore.file_id,
                  }),
                })
                  .then((res) => {
                   this.$message.success("恢复成功");
                   this.getAllFile2(JSON.parse(sessionStorage.getItem('folderid')).this_id);
                  })
                  .catch((err) => {

                  });

                 
            }
            else if(this.needrestore.file_type==2){ //document
            
                    this.$axios({
                  method: "post",
                  url: "/app/recycle_doc",
                  data: qs.stringify({
                    file_id: this.needrestore.file_id,
                  }),
                })
                  .then((res) => {
                   this.$message.success("恢复成功");
                   this.getAllFile2(JSON.parse(sessionStorage.getItem('folderid')).this_id);
                  })
                  .catch((err) => {

                  });

                
            }
            else if(this.needrestore.file_type==3){
            
                    this.$axios({
                  method: "post",
                  url: "app/recycle_project",
                  data: qs.stringify({
                    project_id: this.needrestore.detail.project_id,
                  }),
                })
                  .then((res) => {
                   this.$message.success("恢复成功");
                   this.getAllFile2(JSON.parse(sessionStorage.getItem('folderid')).this_id);
                  })
                  .catch((err) => {

                  });

                
            }

        },
        Delete(){
            this.deleteVisible = false;
            console.log(this.needdelete)
             if(this.needdelete.file_type==1){
               

                    this.$axios({
                  method: "post",
                  url: "/app/permanent_del_folder",
                  data: qs.stringify({
                   folder_id: this.needdelete.file_id,
                  }),
                })
                  .then((res) => {
                   this.$message.success("删除成功");
                   this.getAllFile2(JSON.parse(sessionStorage.getItem('folderid')).this_id);
                  })
                  .catch((err) => {

                  });

                 
            }
            else if(this.needdelete.file_type==2){ //document
            

                    this.$axios({
                  method: "post",
                  url: "/app/permanent_del_doc",
                  data: qs.stringify({
                    file_id: this.needdelete.file_id,
                  }),
                })
                  .then((res) => {
                   this.$message.success("删除成功");
                   this.getAllFile2(JSON.parse(sessionStorage.getItem('folderid')).this_id);
                  })
                  .catch((err) => {

                  });

               
            }
            else if(this.needdelete.file_type==3){
            

                    this.$axios({
                  method: "post",
                  url: "app/permanent_del_project",
                  data: qs.stringify({
                    project_id: this.needdelete.detail.project_id,
                  }),
                })
                  .then((res) => {
                   this.$message.success("删除成功");
                   this.getAllFile2(JSON.parse(sessionStorage.getItem('folderid')).this_id);
                  })
                  .catch((err) => {

                  });

                
            }
        },
         show2(event,item) {
      this.$contextmenu({
        items: [
        
           {
            label: "恢复",
            onClick:() => {
            // setTimeout(() => {
            //     this.restoreVisible = true;
            // },500)
            this.needrestore = item;
                this.restore(item);
            }
          },
          {
            label: "删除",
            onClick:() => {
            setTimeout(() => {
                this.deleteVisible = true;
            },500)
            this.needdelete = item;
                // this.delete(item);
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


 
    },
   async mounted(){
    this.init();
    
   },
   destroyed(){
    // sessionStorage.removeItem('delfolderid');
   }
   
}
</script>