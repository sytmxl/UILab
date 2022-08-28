<template>
<el-header style="height:60px;">
  <div class="top">
    <div class="left">
        <div id="nav-header">
          <span class="brandtext" ><a href="/team_outline">INK BOOK</a></span>
        </div>
    </div>

    <div class="team" v-if="team==true">
      <el-dropdown trigger="click">
        <span class="el-dropdown-link">
          {{checkedteam}}<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
      
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item  v-for = "item in allteams" :key="item"   @click.native="checkit(item)">{{item.team_name}} <i class="el-icon-check" v-if="item.team_id==checkedteamid"></i></el-dropdown-item>
          <div class="splitline"></div>
        <el-dropdown-item icon="el-icon-plus" @click.native="dialogFormVisible = true">点击创建团队</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>

      <el-dialog v-if="dialogFormVisible" :modal="false" title="创建团队" :visible.sync="dialogFormVisible" :append-to-body="true">
      <el-form :model="form">
          <el-form-item label="团队类型" :label-width="formLabelWidth">
          <el-select v-model="form.type" placeholder="请选择团队类型" >
                  <el-option label="IT" value="IT" ></el-option>
                  <el-option label="教育" value="教育"></el-option>
                  <el-option label="金融" value="金融"></el-option>
                </el-select>
        </el-form-item>
        <el-form-item label="团队名称" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off" maxlength="20" show-word-limit></el-input>
        </el-form-item>
    
        <el-form-item label="团队介绍" :label-width="formLabelWidth">
          <el-input v-model="form.intro" autocomplete="off" maxlength="50" show-word-limit></el-input>
        </el-form-item>
      </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="newteam()">确 定</el-button>
          </div>
        </el-dialog>
    </div>

    <div class="search" v-if="search==true">
      <el-input
        placeholder="按下回车键即可搜索"
        prefix-icon="el-icon-search"
        v-model="input" clearable="true" 
        @keyup.enter.native="doSearch()">
      </el-input>
    </div>

    <div class="right">
      <div class="user">
        <img v-if="!profile" src="../assets/bk3.jpg" alt=""/>
        <img v-else :src="profile" alt=""/>
        <div class="username">
          <a href="user_information" title="个人中心">{{username}}</a>
          <a href="/" title="登出" @click="logout()">登出</a>
        </div>
      </div> 
    </div>
  </div>
</el-header>
</template>

<script>
import qs from "qs";
export default {
    data(){
      return{
        checkedteam:'',
        checkedteamid:'',
        dialogFormVisible:false,
        form: {
          name: '',
          type: '',
          intro:''
        },
        formLabelWidth: '120px',
        allteams:[],
        username:JSON.parse(sessionStorage.getItem('user')).username,
        profile:"",
        input:"",
      }
    },
    props: {
      search:{default:true},//根据传参判断是否显示组件
      team:{default:true}
    },
    methods:{
      logout(){
        sessionStorage.removeItem('team');
        sessionStorage.removeItem('user');
        sessionStorage.removeItem('token');
        sessionStorage.removeItem('usericon');
        sessionStorage.removeItem('folderid');
        sessionStorage.removeItem('delfolderid');
        sessionStorage.removeItem('doc_tok');
        localStorage.removeItem('saveuser');
        localStorage.removeItem('savetoken');

      },
      doSearch()
      {
        var info = {content: this.input};
        this.$router.push({path:'/user_search'});
        this.$store.dispatch("savesearched", info);
        location.href="user_search"
      },
      checkit(content){
        this.checkedteam = content.team_name;
        this.checkedteamid = content.team_id;
        this.$store.dispatch("saveteam", content);
         sessionStorage.removeItem('folderid');
        sessionStorage.removeItem('copy');
        sessionStorage.removeItem('delfolderid');
        location.reload();
      },
      newteam(){
        this.dialogFormVisible = false;
         this.$axios({
        method: "post",
        // headers: { "authorization": JSON.parse(sessionStorage.getItem('token')) },
        url: "app/create_team",
        data: qs.stringify({
          team_name: this.form.name,
          team_type:this.form.type,
          team_info: this.form.intro
        }),
      })
        .then((res) => {
          var content = {team_id: res.data.data.team_id, team_name: this.form.name}; 
           this.$store.dispatch("saveteam", content);
           this.$message.success(res.data.msg);
           location.reload();
          // this.start = res.data.OrderDate;
          //待定
            
        })
        .catch((err) => {
          
        });

      },
      init(){
         this.$axios({
        method: "get",
        url: "app/get_team_list",
        data: qs.stringify({
        }),
      })
        .then((res) => {
          for( var i in res.data.data.team_list_owner){
            this.allteams.push(res.data.data.team_list_owner[i]);
          }
           for( var i in res.data.data.team_list_admin){
            this.allteams.push(res.data.data.team_list_admin[i]);
          }
           for( var i in res.data.data.team_list_member){
            this.allteams.push(res.data.data.team_list_member[i]);
          }
          // this.allteams.push(res.data.data.team_list_owner);
          // this.allteams.push(res.data.data.team_list_admin);
          // this.allteams.push(res.data.data.team_list_member);
          
        })
        .catch((err) => {
          
        });

        this.profile=JSON.parse(sessionStorage.getItem('usericon')).picurl
        if(JSON.parse(sessionStorage.getItem('team'))==null){
          this.checkedteam = '请选择你的团队';
        }
        else{
          this.checkedteam = JSON.parse(sessionStorage.getItem('team')).team_name;
          this.checkedteamid = JSON.parse(sessionStorage.getItem('team')).team_id;
        }


      }
    },
    mounted(){
      this.init()
    }
}
</script>

<style scoped>
.el-header {
  backdrop-filter: blur(25px) brightness(110%);
  background-color: #53667713 !important;
  z-index: 100;
  width: 100%;
}
.top {
  /* margin-top: 10px; */
  display: flex;
}
@font-face {
  font-family: myFont;
  src: url("../assets/Futura.ttc");
}
.brandtext {
  font-family: myFont;
  user-select: none;
  color:rgb(73, 88, 100);
}
.brandtext a{
    text-decoration: none;
}
.brandtext a:visited{
  color: black;
}
#nav-header {
  display: flex;
  height: 100%;
  margin-left: 20px;
  align-items: center;
  font-size: 35px;
}
.left{
  /* float: left; */
  /* position: absolute; */
  left: 20px;
  padding-right: 20px;
}
.right{
  /* float: right; */
  position: absolute;
  right:0;
  /* top: 7px; */
}
.splitline{
  height: 1px;
  width: 90%;
  margin-left: 5%;
  background-color: #e0e0e0;
}

.user{
  margin-right:0px;
  float: right;
}
.user img{
  width: 40px;
  height: 40px;
  margin: 10px;
  border-radius: 50%;
  right: 20px;
  position: absolute;
  transition: 0.5s;
}
.username{
  font-size: 20px;
  transition: 0.5s;
  float: right;
  margin: 4px 10px 10px 10px;
  padding: 10px 0px 10px 0px;
  filter: blur(20px);
  opacity: 0%;
}
.username a{
  text-decoration:none;
  margin-right: 20px;
  color: black;

}
.username a:visited{
  color: black;
}
.username a:hover{
  color: rgb(150, 169, 183);
}
.user:hover .username {
  font-size: 20px;
  margin: 4px 10px 10px 10px;
  padding: 10px 0px 10px 0px;
  float: right;
  opacity: 100%;
  filter: blur(0px);
  /* padding: 10px 10px 10px 80px; */
}
.user:hover img {
  filter: blur(20px);
  opacity: 0%;
}
.el-input >>> .el-input__inner{
    border-radius:25px;
    font-size:20px;
}
 .el-dropdown-link {
    cursor: pointer;
    color: black;
    font-size: 20px;
  }
  .el-icon-arrow-down {
    font-size: 15px;
     
  }

  .el-dropdown-menu {
      border-radius: 15px;
  }
  .el-dropdown-menu>>>.el-dropdown-menu__item{
      font-size: 16px;
      /* color: #2878ff; */
   
  }

  .demonstration {
    display: block;
    /* color: #409EFF; */
    font-size: 15px;
    margin-bottom: 20px;
    /* border-radius: 15px; */
  }
  .left {
    margin-top: 7px;
    float: left;
  }
  .left:hover {
    background: rgba(137, 149, 158, 0.164);
    border-radius: 10px;
    margin-top: 7px;
  }
  .team {
    /* margin-left: 250px; */
    /* float: left; */
    padding: 10px;
    margin-top: 7px;
    margin-left: 5px;
    border-radius: 20px;
    transition: 0.2s;
  }
  .team:hover {
    background: rgba(142, 155, 164, 0.23);
    border-radius: 10px;
    padding: 10px;
    margin-top: 7px;
  }
  .team:active {
    background: rgba(150, 169, 183, 0.721);
    border-radius: 10px;
    /* padding: 12px; */
    /* margin-top: 7px; */
    font-size: 19px;
    transition: 0.05s;
    transform: scaleY(10px);
  }
  .el-dropdown-item {
    transition: 0.4s;
  }
  /* .el-dropdown-menu__item{
    width: 150px;
    text-overflow: ellipsis;
    overflow: hidden; 
  } */
  .el-dropdown-item:hover{
    background: rgba(150, 169, 183, 0.422) !important;
    border-radius: 10px !important;
    margin: 10px !important;
  }
  .search {
    margin-top: 10px;
    margin-left: 50px;
    width: 5px;
    transition: 0.4s;
    /* float: right; */
  }
  .search:hover {
    width: 300px;
    border-color: rgb(150, 169, 183) !important;
    box-shadow: 5px;
  }
  .search:active {
    width: 300px;
    border-color: rgb(95, 106, 113) !important;
    box-shadow: 5px;
  }
  .search:focus {
    width: 300px;
    border-color: rgb(95, 106, 113) !important;
    box-shadow: 5px;
  }
</style>
<style>
  .el-message-box{
    border-radius: 15px;
  }
  .el-button--small{
    border-radius: 15px;
  }
</style>