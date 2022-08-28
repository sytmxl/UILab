<template>
  <el-container>
    <div class="main" v-if="teamname">
      <h1 class="label">团队成员</h1>
      <div class="add" @click="add()">
        <i class="el-icon-plus" style="font-size:20px" @click="add()" title="添加新成员"></i>
        <el-dialog v-if="addmemberDialogVisible" :modal="false" title="邀请新的团队成员" :visible.sync="addmemberDialogVisible" width="30%"
          :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true" center>
          <el-form ref="addmember" :model="addmember" class="addmember" :hide-required-asterisk="true" @submit.native.prevent>
            <el-form-item prop="name" :rules="[
              { required: true, message: '请输入您要邀请的用户名', trigger: 'blur' },
            ]">
              <el-input id='addmember' v-model="addmember.name" placeholder="请输入您要邀请的用户名进行搜索" type="text"
                autocomplete="off" clearable prefix-icon="el-icon-user-solid" maxlength="20" show-word-limit></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button class="addmember" @click="addmemberDialogVisible = false, resetForm('addmember')">取 消</el-button>
            <el-button class="addmember" type="primary" @click="addmemberFunc(addmember.name)">搜 索
            </el-button>
          </span>
        </el-dialog>
      </div>
      <Teammate v-for="item in team_member_list" :key="item" :identity="item.identitys" :user_name="item.user_name"
        :user_id="item.user_id" :realname="item.real_name" :email="item.email" :imgurl="item.profile" />
    </div>
    <div class="chooseteam" v-else>
      <el-empty description="你还有没选择你的团队，快去选择一个吧" :image-size="200"></el-empty>
    </div>
    <div class="clear"></div>

  </el-container>


</template>

<script>

import qs from 'qs';
import Teammate from "@/components/teammate.vue";
export default {
  components: {
    Teammate
  },
  data() {
    return {
      teamname: JSON.parse(sessionStorage.getItem('team')).team_name,
      team_member_list: [],
      boss_list: [],
      manager_list: [],
      member_list: [],
      addmemberDialogVisible:false,
      addmember: {
        name: ""
      }
    }
  },
  methods: {
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    // addmember(){
    //      this.$prompt('请输入用户名', '邀请新成员', {
    //       confirmButtonText: '确定',
    //       cancelButtonText: '取消',
    //     }).then(({ value }) => {

    //      this.$store.dispatch("savesearched", {content:value});
    //      location.href="user_search";
    //     }).catch(() => {

    //     });
    //   },
    add()
    {
      this.addmemberDialogVisible=true;
    },
    addmemberFunc(val) {
      if(val == '')
      {
        this.$message.warning("搜索用户名不能为空！");
        return;
      }
      this.$store.dispatch("savesearched", { content:val});
      this.resetForm('addmember');
      location.href = "user_search";
    },
    init() {
      this.$axios({
        method: "post",
        url: "app/get_team_member_list",
        data: qs.stringify({
          team_id: JSON.parse(sessionStorage.getItem('team')).team_id,
        }),
      })
        .then((res) => {
          this.team_member_list = res.data.data.team_member_list;

          var count1 = 0;
          var count2 = 0;
          var count3 = 0;
          for (var i = 0; i < this.team_member_list.length; i++) {
            if (this.team_member_list[i].identitys == 3) {
              this.boss_list[count1++] = this.team_member_list[i];
            }
            else if (this.team_member_list[i].identitys == 2) {
              this.manager_list[count2++] = this.team_member_list[i];
            }
            else if (this.team_member_list[i].identitys == 1) {
              this.member_list[count3++] = this.team_member_list[i];
            }
          }

        })
        .catch((err) => {

        });

    }
  },
  mounted() {
    this.init()
  }
}
</script>

<style scoped>
.clear {
  clear: both;
}

.chooseteam {
  position: absolute;
  left: 0;
  right: 0;
}

.el-icon-plus:hover {
  cursor: pointer;
}

.add {
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

.add:hover {
  width: 62px;
  border-radius: 50%;

  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 10px rgba(0, 0, 0, 0.04);
}

/* .main .people{
    margin-bottom: 200px;
    margin-left: 20px;
  } */
.manager_info .norminfo-card {
  float: left;
}

.worker_info .lowerinfo-card {
  float: left;
}

.main {
  width: 100%;
  text-align: left;
}

.boss {
  height: auto;
  width: 100%;
}

.manager {
  height: auto;
  width: 100%;
}

.worker {
  height: auto;
  width: 100%;
}

.label {
  margin: 30px 0px 0px 30px;
  font-size: 50px;
  /* float: left; */
  width: 100%;
  color: rgb(114, 132, 145);
}
</style>